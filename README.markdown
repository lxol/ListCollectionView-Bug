##Description

`ListCollectionView` should dispatch a `COLLECTION_CHANGE` event when an array element changes.  This is not the case when we set `filterFunction` property and don't follow it with `refresh()` 

##  Details
[Example](http://alder101.github.com/ListCollectionView-Bug/)


We bind 3 instances of ArrayCollections (myAC1, myAC2, myAC3) to `dataProvider` properties of DataGrids. 

Click the first button and all instances will be created with the same array elements. Each element is an instance of `SimpleClass` with bindable properties.

The only difference between myAC2 and my AC3 is a non `nill` `filterFunction` property of AC3.

We expect that all dataProviders get updated in responce to the change in the array element's property. (Click button 2). However third DataGrid is not updated. 

Everything works as expected if we refresh myAC3 right after setting `filterFunction` property. (click buttons 1, 3, 2).

## What's going on

An array element change triggers `handlePropertyChange` function of `ListCollectionView`.

````actionscript
private function handlePropertyChangeEvents(events:Array):void
{
    var eventItems:Array = events;
    if (sort || filterFunction != null)
    {
      ....
       moveItemInView(updateEntry.item, updateEntry.item, eventItems);
      ....
    }
    if (eventItems.length > 0)
    {
       var updateEvent:CollectionEvent =
              new CollectionEvent(CollectionEvent.COLLECTION_CHANGE);
        ...
       dispatchEvent(updateEvent);
    }
}
````

`COLLECTION_CHANGE` event is dispatched if `filterFunction` is not set or run `moveItemInview` otherwise. 

`moveItemInView` dispatch `COLLECTION_CHANGE` event only if `localIndex` is not `null`.

```actionscript
private function moveItemInView(item:Object,
                               dispatch:Boolean = true, updateEventItems:Array = null):void
{
    if (localIndex)
    {
       ....
       var event:CollectionEvent =
                    new CollectionEvent(CollectionEvent.COLLECTION_CHANGE);
       ....
       dispatchEvent(event);
     ....
    }
}
````
The problem is that `localIndex` is null by default and takes non `null` value after `refresh()`. So in case when we set `filterFunction` with no `refresh()` `COLLECTION_CHANGE` event will never be dispatched.

