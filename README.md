## CS-Cart: Trailblazing Through Tygh to Create New WYSIWYG for Addon.xml, Controller, & Smarty Fields
 
This is an example repo. There are not currently proper hooks to release this. Full thought: https://dhaupin.com/learn/cs-cart/cs-cart-creating-a-common-wysiwyg-tpl-for-addon-xml-setting-field


## The 10k Foot View

There are a couple of hard coded edits we need to make,  although a couple files included in this repo take care of some. For completeness, lets start from nothing. To start, create a file `/design/backend/templates/common/wysiwyg.tpl` with the following content (from repo):

```
<textarea id="{$id}" name="{$name}" cols="55" rows="8" class="cm-wysiwyg input-large {$class}" {if $disable_input}disabled="disabled"{/if}>{$data}</textarea>
```

Copy this file to `/var/themes_repository/basic/templates/common` in order to make it available on catalog side (from repo). The syntax for the include is:

```
{include file="common/wysiwyg.tpl" id="a_unique_id" name="data[a_unique_id]" data="html_content nofilter" class="optional_classes"}
```

Now to add the field type so we can set in addon.xml. Edit `/app/Tygh/Addons/AXmlScheme.php` and find the `_getTypes()` function. Add an array item (hardcoded):

```
'wysiwyg' => 'J'
```

To apply that, we can a conditional into `design/backend/templates/common/settings_fields.tpl`. This file is included in a loop, so settings for each are $item. Before `{elseif $item.type == "K"}` add the following (hardcoded):

```
{elseif $item.type == "J"}
    <div class="control-group">
      {include file="common/wysiwyg.tpl" id="addon_option_{$item.section_name}_{$item.name}" name="addon_data[options][{$item.object_id}]" data="{$item.value nofilter}" class="{$item.section_name}_{$item.name}"}
    </div>
```

This change has a possibility to be cached. So in order to poke CS enough to display the new field type, we must clear every cache with `/admin.php?cc&ctpl` followed by "Administration > Storage > Clear Cache". Additionally, an existing addon that was stored in DB before the WYSIWYG field came alive may need to be re-installed.
