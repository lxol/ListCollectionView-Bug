##Description

`ListCollectionView` should dispatch a `COLLECTION_CHANGE` event when an array element changes.  This is not the case in situation when we set `filterFunction` property and don't follow it with `refresh()` 

##  Details
[Example](http://alder101.github.com/ListCollectionView-Bug/)
We bound `dataProvider` of three DataGrids to 3 bindable instances of `ArrayCollection`: myAC1, myAC2, myAC3. 

Click the first button and all instances will be created with the same array elements. Each element is and an instance of `SimpleClass` with bindable properties.

The only difference between myAC2 and my AC3 is `filterFunction` property that is set for myAC3.

We expect that all dataProvides should trigger the change of the property of the first element. (Click button 2). However third data is not updated. 

Everything works as expected if we refresh myAC3 right after setting `filterFunction` property. (click first button), then refresh myAC3 (cliick 3-rd button) a
nd change a property of the first array element (click 2-nd button).


## What's going on

Array element change triggers `handlePropertyChange` function of `ListCollectionView`. On line `1337` the function checks if `filterFunction` is  `null`. It runs `moveItemInView`. `COLLECTION_CHAGE` event should be dispatched from `moveItemInView`.

`moveItemInView` executes it's code only if `localIndex` property is not `null`(line `1574`). `localIndex` on the other hand will be set only by `refresh()`. 

So in case when we set `filterFunction` with no `refresh()` `COLLECTION_CHANGE` event will never be dispatched.

 