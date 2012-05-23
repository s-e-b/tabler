Tabler.js
========

A lightweight library for building dynamic tables.

## Dependencies

Tabler depends on underscore.js (http://documentcloud.github.com/underscore/)

## Usage

Tabler works as an AMD module or standalone

### AMD

    define(['PATH/tabler/tabler'], function(tabler){
        // Your module code
        var table = tabler.create();

        table.load([..data..]);
        table.render();

        $(..el..).append(table.$el);
        // More module code
    });

### Standalone

Reference the scripts directly - tabler.js first and plugins after

## Configuration

Configuration for the table iself is done through the specs passed to the `tabler.create()` method:

    var table = tabler.create([
        {field: 'name', name: 'Name'},
        {field: 'apples', name: '# Apples'},
        {field: 'bananas', name: '# Bananas'}
    ]);

    table.load([
        {name: 'Steve', apples: 2, bananas: 4},
        {name: 'Graham', apples: 1, bananas: 6},
        {name: 'Dan', apples: 9, bananas: 2},
        {name: 'Jon', apples: 5, bananas: 6}
    ]);
    table.render();

The Default set of configuration parameters are:

* field: The field from each row to render
* name: The title for the column that will appear in the header. If you do not specify a name attribute for any column then the table will not have any columns
* formatter: A function(row, colSpec) that returns formatted data for the row (eg wrap each number in a link)
* headerFormatter: A function(colSpec) that returns formatted data for the column header (eg wrapped in a link)
* disabled: true/false whether the table should render the column at all

## Using Plugins

Simply pass the plugins as the second parameter of the `create` method:

    var table = tabler.create([..spec..], {plugins: [sortable]});

    // table.pager and table.sortable are now available and automatically applied on the next call to `render`

Or add plugins later on:

    table.addPlugin(pager, {pageSize: 10, currentPage: 0})

The included plugins are also AMD modules and should be required in the same manner as the main module.

As you can see, plugin options cannot currently be specific when calling the create method.  You can normally set these after the fact (eg table.pager.options.pageSize or table.sortable.sorter)

## Included Plugins

Tabler has a number of plugins that come out-of-the-box:

### Aggregator

Allows you to add an aggregator function on each column which can perform calculations, the function runs for each column of the data and runs a reduce-like function on it, displaying the final result in the footer of the table

    function totaliser(memo, value){
        return (memo || 0) + value;
    }

    var table = tabler.create([
        {name: 'Apples', field: 'apples', aggregator: totaliser}
    ]);
    table.render();

    // The Apples tfoot td now has the total of all apples cells

### Column Grouper

Allows you to group columns

    var table = tabler.create([
        {field: 'name', name: 'Name'},
        {field: 'apples', name: '# Apples', columnGroup: 'Fruit'},
        {field: 'bananas', name: '# Bananas', columnGroup: 'Fruit'}
    ]);
    table.render();

    The table will now have a th spanning the second and third column with the content "Fruit"

### Pager

Adds a pager to the footer of the table, with next/prev/first/last links & client-side paging capabilities (by default)

    table.addPlugin(pager, {
        pageSize: 20,
        currentPage: 1
    });
    table.render();

Options:

* pageSize: The size of each page
* currentPage: The current page index, 0-based
* totalResults: The total number of results (useful for server-side paging)
* pager: [optional] function(data, pageOptions, callback) that can be passed in to perform server-side paging (run the callback when done)

### PageSize

Adds a simple page size dropdown to the pager footer row (requires pager plugin)

    table.addPlugin(pageSize, {sizes: [10, 25, 50]});

If the `sizes` option is not passed in, then a default set of sizes of [20, 50, 100] is used

### Sortable

Adds anchors to designated column headers, making them sortable on click

    var table = tabler.create([
        {field: 'name', name: 'Name'},
        {field: 'apples', name: '# Apples', columnGroup: 'Fruit', sortable: true},
        {field: 'bananas', name: '# Bananas', columnGroup: 'Fruit', sortable: true}
    ]);

Options:

* sorter: function(data, field, dir, callback) that can perform server-side sorting (run the callback when done)