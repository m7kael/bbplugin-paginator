<script>
  import { getContext } from "svelte"

  const { styleable, Provider } = getContext("sdk")
  const component = getContext("component")

  export let page
  export let pageSize
  export let pages 
  export let pageButtons
  export let records 

  let startIndex = 1
  $: pages = Math.ceil( records / pageSize )
  $: lastIndex = pageButtons >= pages ? pages : pageButtons + startIndex - 3

  $: dataContext = { offset: Number(pageSize * (page - 1)), limit: Number(pageSize), currentPage: Number(page) }
  $: buttons = generateState( page, pageSize, pages, pageButtons)

  function generateState ( ) {
    let buttons = [] 
    
    if ( pageButtons > pages - startIndex ) {
      for (var i =  startIndex; i <= pages; i++) {
        buttons.push(i);
      }
    } else {
      for (var i =  startIndex; i <= startIndex + ( pageButtons - 3 ); i++) {
        buttons.push(i);
      }
        buttons.push("...");
        buttons.push(pages);
    }

    return buttons;
  }

  function handlePrevious () {
    page = page > 1 ? page - 1 : 1
    if (page < startIndex) startIndex = page;  
  }

  function handleClick ( item ) {

    if (item == lastIndex) {
      startIndex = item
    } else if ( item == startIndex && startIndex > 1) {
      startIndex = item - 1
    } else if ( item == pages ) { 
      startIndex = pages - pageButtons + 1
    } else if ( item == ",,,") {
      return
    }

    page = item

  }

  function handleNext () {
    page = page < pages ? page + 1 : pages
    if (page > lastIndex) startIndex = page
  }
</script>

<div use:styleable={$component.styles}>
  <Provider data={dataContext}>
    <slot />
  </Provider>
 
  <div class="paginator">
      <nav style:margin-top={"1rem"} class="spectrum-Pagination spectrum-Pagination--listing">
        <div>Total: {records}</div>
        <button 
          disabled={page == 1} 
          on:click={handlePrevious} 
          class="spectrum-ActionButton spectrum-ActionButton--sizeM spectrum-ActionButton--quiet">
          <span class="spectrum-Button-label">    
            <svg class="spectrum-Icon spectrum-UIIcon-ChevronLeft100" focusable="false" aria-hidden="true" aria-label="ChevronLeft">
            <use xlink:href="#spectrum-css-icon-Chevron100"></use>
          </svg></span>
        </button>

        {#each buttons as button}
          <button 
            on:click={ () => handleClick(button) }  
            class:is-selected={button == page } 
            class="spectrum-ActionButton spectrum-ActionButton--sizeM spectrum-ActionButton--quiet">
            <span class="spectrum-ActionButton-label">{button}</span>
          </button>
        {/each}

        <button disabled={page == pages} on:click={handleNext} class="spectrum-ActionButton spectrum-ActionButton--sizeM spectrum-ActionButton--quiet">
          <svg class="spectrum-Icon spectrum-UIIcon-ChevronRight100" focusable="false" aria-hidden="true" aria-label="ChevronLeft">
            <use xlink:href="#spectrum-css-icon-Chevron100"></use>
          </svg>
        </button>

      </nav>
  </div>
</div>

<style>
  .paginator {
    width: 100%;
    display: flex;
    justify-content: flex-end;
    padding-right: 0.85rem;
  }
</style>