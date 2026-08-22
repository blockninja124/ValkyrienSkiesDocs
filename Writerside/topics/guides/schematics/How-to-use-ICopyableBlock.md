# How to use ICopyableBlock

Learn how to use the ICopyableBlock interface to improve compatibility with assembly, schematics, and splitting.

## Introduction

ICopyableBlock is an interface which can be implemented by any custom class that extends Block.
The methods it provides will be used by Valkyrien Skies itself, as well as addons, to improve your blocks compatibility
with transitions such as schematic copy/pasting (like VDex or VMod) or ship splitting.

It can be used for a wide variety of blocks to improve compat. Examples include:
- Bearing block being pasted in schematics without losing its joint
- Cross-ship fluid pipe being split off into a second ship without losing its connection
- Control seat persisting its linked blocks after being pasted as a new ship

If your blocks are having issues with VMod schematics, you're in the right place.

## Steps

### Implementing the interface

The first step is to add the interface to your custom block class. 
You can also add it to another mods block using a mixin, but that won't be covered in this guide.

> Don't accidentally implement ICopyableBlock on your **block entity**, 
> it will not be called. It must be implemented on your Block class. 
>
{style="warning"}

<tabs group="ktj">
<tab title="Kotlin" group-key="kotlin">
<code-block lang="Kotlin">
class MyCustomBlock: Block, ICopyableBlock {
    // ...
}
</code-block>
</tab>
<tab title="Java" group-key="java">
<code-block lang="Java">
public class MyCustomBlock extends Block implements ICopyableBlock {
    // ...
}
</code-block>
</tab>
</tabs>

Then, you must implement two functions, `onCopy` and `onPaste`. (Your IDE will often provide the method signature for you)

### Understanding the interface

The Javadoc comments on these methods are rather technical, so here is a higher-level overview of what they do:

When your block is copied, onCopy is called. The CompoundTag that onCopy returns will be saved. If onCopy returns null, then
the CompoundTag from the block entity write() is saved. Most blocks choose to return null from `onCopy` for simplicity, saving the BE data.

Then, when the block is pasted, `onPaste` is called. The tag you returned from `onCopy` or `write` will be given to `onPaste`, as 
well as some additional information like a mapping of old ship ids (the ids from when your block was copied) to new ship ids 
(the ids of the ships your block is being pasted onto). 

Whatever you return from `onPaste` is saved in the blocks data, and is usually parsed by the block entities `read` function shortly after being pasted.

This means you can use `onPaste` as a form of "pre-processor" for the block entities data. You take in the data that was saved 
(for example the ship your bearing is jointed to), and map it to new data (like the new ship ids you have been pasted on).

This may sound complicated, but in practice it can be simple. To help with understanding, included below is an example of a bearing block 
that supports being duplicated to a new ship when pasted in a schematic.

### Example

<tabs group="ktj">
<tab title="Kotlin" group-key="kotlin">
<code-block lang="Kotlin">
class MyCustomBlock: Block, ICopyableBlock {
    // ...
}

class MyCustomBlockEntity: BlockEntity {
    // ...
}
</code-block>
</tab>
<tab title="Java" group-key="java">
<code-block lang="Java">
public class MyCustomBlock extends Block implements ICopyableBlock {
    // ...
}

public class MyCustomBlockEntity extends BlockEntity {
    // ...
}
</code-block>
</tab>
</tabs>