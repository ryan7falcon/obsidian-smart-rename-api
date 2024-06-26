# Smart Rename API

This is a fork of a [Smart Rename plugin](https://github.com/mnaoumov/obsidian-smart-rename) for [Obsidian](https://obsidian.md/) that adds the command `Smart Rename` which performs the following steps after renaming the note:

1. Adds the previous title as an alias to the renamed note
2. Preserves the backlinks to the renamed note that were using previous title as a display text.

In addition to the orginal functionality it exposes API that allows to call a function to perform Smart Renaming programmatically. See example below.

The motivation for creating this fork for me was the use case of having existing links to non-existing files. When a critical mass of those links tells me I should create the file, I want to do it with my template and rename the file to include a timestamp (I use Zettelkasten), but preserve the old name as an alias, and in all the existing links I want to use it as a display text to not pollute links' texts with timestamps.

## Detailed explanation

1. You have

`OldName.md`:

```markdown
This is a note `OldName.md` that is going to be renamed to `NewName.md`.
```

`OtherNote.md`:

```markdown
This note references

1. Wikilink [[OldName]]
2. Wikilink with the same display text [[OldName|OldName]]
3. Wikilink with a custom display text [[OldName|Custom display text]]
4. Markdown link [OldName](OldName.md)
5. Markdown link with a custom display text [Custom display text](OldName.md)
```

2. You invoke current plugin providing `NewName` as a new title

3. Now you have

`NewName.md`:

```markdown
---
aliases:
  - OldName
---

This is a note `OldName.md` that is going to be renamed to `NewName.md`.
```

`OtherNote.md`:

```markdown
This note references

1. Wikilink [[NewName|OldName]]
2. Wikilink with the same display text [[NewName|OldName]]
3. Wikilink with a custom display text [[NewName|Custom display text]]
4. Markdown link [OldName](NewName.md)
5. Markdown link with a custom display text [Custom display text](NewName.md)
```

Current plugin's aim is to preserve `OldName` display text in links 1, 2, 4

## Installation

- `Smart Rename API` needs to be installed from source.
- Prerequesite: [Node.js](https://nodejs.org/en/)
- Download the code
- Navigate to the plugin directory.

```
cd obsidian-smart-rename-api
```

- Install dependencies.

```
npm install
```

- Compile the source code. 

```
npm run build
```

- Copy the contents of `dist/build` folder to `{YOUR_VAULT_PATH}/.obsidian/plugins/obsidian-smart-rename-api`
- In Obsidian go to `Settings -> Community Plugins` and click `Reload Plugins` button next to `Installed Plugins`. Then enable it.

## Example Usage
- Create a new file to use a template with `templater` plugin (which should be installed and enabled):

```
<%*
const title = tp.file.title;
const date = tp.date.now("YYYYMMDDHHmmss");
const newTitle = `${date} ${title}`

const tFile = tp.file.find_tfile(tp.file.path(true))
app.fileManager.processFrontMatter(tFile, (frontmatter) => { frontmatter["tags"] = "📝"; 
	frontmatter["dg-publish"] = false; 
	frontmatter["related"] = null; 
	frontmatter["reference"] = null; 
	frontmatter["status"] = "🌱 Seedling"; 
	});


%>
# <%tp.file.title%>
<%tp.file.cursor(0)%>

<%* 
tp.hooks.on_all_templates_executed(async() => {
	await app.plugins.plugins["smart-rename-api"].api.smartRename(tFile)(newTitle)
})
_%>

```

- In any note create a link to a non-existing note:

```
[[New Note]]
```

- Click on the link to create that note
- **PRESS ENTER** (this is very important, so that the file is not totally empty when templater runs, otherwise it doesnt run correctly for some reason)
- In that new note run templater command `Templater: Open Insert Template modal` and select your template
- The `New Note` note should now be renamed with a timestamp and the link to it updated so that display text didnt change.

## License

 © [Michael Naumov](https://github.com/mnaoumov/)
 © [Ryan Falcon](https://github.com/ryan7falcon/)
