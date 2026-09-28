# Foundational datamodels

The standard kinds of data in [Cloudmorrow](https://github.com/Cloudmorrow/cloudmorrow):
the shapes every [Quill](https://github.com/Cloudmorrow/cloudmorrow/blob/main/docs/QUILLS.md)
agrees on. A contact is a contact whichever Quill made it; a Quill that needs
more adds fields of its own to one of these, rather than inventing a second
kind of contact.

They come in **domains**, chosen together when you install a Cloudmorrow
([`domains.toml`](domains.toml)):

| domain | datamodels |
| --- | --- |
| Tasks | `board`, `task` |
| Customers (CRM) | `book`, `organisation`, `contact`, `stage`, `deal`, `activity` |
| Fleet | `vehicle` |
| Calendars | `calendar`, `event` |
| Messaging | `channel`, `message` |
| Notes | `note` |
| Secrets | `secret` |

A **space** (`space = true`: a calendar, a channel) is personal, shared with
members, or public, and what is in it (`in_space = "calendar"`) is for
whoever may see the space; left out of any — its space link empty — it is
its writer's own. A CRM's `book` is a space: the organisations, people,
deals and activities a team keeps together, and the `stage`s of its
pipeline, in order. A few foundational datamodels are kept by a
**backend** instead of the record store (`backend = "notes"`): notes stay
the Markdown files they always were, and secrets stay in the store `cm secret`
has always used (`backend = "vaults"`).

A server copies in the ones its Quills use, from the release the
[Quill Catalog](https://github.com/Cloudmorrow/quill-catalog) pins, and keeps
them: it never needs this repository to run.

## The format

```toml
[datamodel]
id = "task"
version = 1
label = "Task"
description = "One thing to do, on a board, in a lane."
domain = "tasks"
scopes = ["personal"]
title = "title"
ordered_within = ["board", "lane"]

[fields]
board = { kind = "link", to = "board", required = true, indexed = true, on_delete = "cascade" }
title = { kind = "string", required = true }
lane  = { kind = "enum", values = ["todo", "doing", "done"], labels = ["To Do", "Doing", "Done"], default = "todo", indexed = true }
```

Field kinds: string, text, markdown, bool, int, decimal, date, datetime, enum,
email, phone, url, link, json. Indexed fields are plain on disk so a server can
filter by them; everything else is encrypted at rest. `secret = true` on a string
or text field keeps it out of every listing and has every surface draw it
hidden until it is asked for.

## Changing one

Versions are integers and a released version never changes. A new standard
field arrives as a new version; the bar for a new datamodel here is that two
unrelated Quills wanted it. Open a pull request with the reason.
