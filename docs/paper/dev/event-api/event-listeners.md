---
slug: /dev/event-listeners
---

# Event Listeners

Events are an efficient way to listen for specific actions that happen in the game. They can be called by the server, or by plugins. 
These are called by the server or plugins when something happens, such as a player joining the server, or a block being broken.
Plugins are able to call custom events, such as a player completing a quest, for other plugins to listen for.

## Your listener class

To listen for events, you need to create a class that implements `Listener`. 
This class can be called anything you want, but it is recommended to name it something related to the events you are listening for.

```java title="ExampleListener.java"
public class ExampleListener implements Listener {
    // ...
}
```

## `@EventHandler`

To listen for an event, you need to create a method that is annotated with `@EventHandler`. 
This method can be named anything you want, but it is recommended to name it something meaningful related to the event it is listening for.

## The listener method

The method body does not need to return any data, for this reason use `void` as the return type. 
Listeners take in a single parameter, which is the event that is being listened for.
    
```java title="ExampleListener.java"
public class ExampleListener implements Listener {

    @EventHandler
    public void onPlayerJoin(PlayerJoinEvent event) {
        // ...
    }
}
```

:::note Events

There is no list of events that can be listened for, however take a look 
[here](https://jd.papermc.io/paper/1.19/org/bukkit/event/Event.html) to see all events that extend `Event`.

:::

## Registering the listener

To register the listener, you need to call `Bukkit.getPluginManager().registerEvents()` 
and pass in your listener class instance and an instance of your plugin.

This will register your listener class and allow it to listen for events. 
This is commonly done in the `onEnable()` method of your plugin so that it is registered when the server starts ticking.

```java title="ExamplePlugin.java"
public class ExamplePlugin extends JavaPlugin {

    @Override
    public void onEnable() {
        getServer().getPluginManager().registerEvents(new ExampleListener(), this);
    }
}
```

## Event priority

You can also specify the priority of the event.
    
```java title="ExampleListener.java"
public class ExampleListener implements Listener {

    @EventHandler(priority = EventPriority.HIGH)
    public void onPlayerJoin(PlayerJoinEvent event) {
        // ...
    }
}
```
There are six different priorities that you can use:
- `EventPriority.LOWEST`
- `EventPriority.LOW`
- `EventPriority.NORMAL`
- `EventPriority.HIGH`
- `EventPriority.HIGHEST`
- `EventPriority.MONITOR`

The order of the priorities is somewhat counter-intuitive. The **higher** the priority, the **later** the event is called. 
For example, If it is important that your plugin has the last say in a certain event - to avoid it being changed - you 
should use `EventPriority.HIGHEST`.

:::note

The `MONITOR` priority is used to monitor the event, but not change it. It is called after all other priorities have been called.
This means you can get the result of any plugin interaction such as cancellation or modification.

:::

## Event cancellation

Some events can be cancelled, preventing the given action from being completed. These events implement `Cancellable`.
    
```java title="ExampleListener.java"
public class ExampleListener implements Listener {

    @EventHandler
    public void onPlayerJoin(PlayerJoinEvent event) {
        event.setCancelled(true);
    }
}
```

:::warning

It is important to consider that another plugin could have cancelled or changed the event before your plugin is called.
Always check the event before doing anything with it.

:::

The above example will cancel the event, meaning that the player will not be able to join the server.
Once an event is cancelled, it will continue to call any other listeners for that event unless they add 
`ignoreCancelled = true` to the `@EventHandler` annotation to ignore cancelled events.

```java title="ExampleListener.java"
public class ExampleListener implements Listener {

    @EventHandler(ignoreCancelled = true)
    public void onPlayerJoin(PlayerJoinEvent event) {
        // ...
    }
}
```


