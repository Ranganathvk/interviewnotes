# AGENTS.md — Interview Notes

Guidance for AI agents working in this repository.

## Purpose

Personal interview prep notes for a **senior Java backend** engineer (Java, Spring, Kubernetes, system design). Prefer depth suited to senior interviews over beginner tutorials.

## Repo map

| Area | Path | Role |
|------|------|------|
| Topic index | [INIT.md](INIT.md) | Browse by area; update when adding topics |
| Full TOC | [README.md](README.md) | Anchored table of contents |
| Java backlog | [update-java](update-java) | Java module roadmap and gaps |
| Planned topics | [need-to-add](need-to-add) | Non-Java topics still missing |
| Java notes | [Java/](Java/) | Core Java (basics, collections, concurrency, etc.) |
| OOP | [oop/](oop/) | Already covered separately from `Java/` |
| Design patterns | [design-pattern/](design-pattern/) | Creational / structural / behavioral |

## Writing conventions

Match existing note style (see `Java/basic/java-basics.md`, `oop/oops.md`):

- Markdown with section headers (`====` / `----` undertitles or `##` / `###` — stay consistent with the file you edit).
- Interview-oriented: definitions, why it matters, comparisons, traps.
- Prefer bullets over long prose; include short Java snippets when they clarify behavior.
- Bold key terms on first use in a section.
- Do not invent a second parallel hierarchy (e.g. do not create `java/` lowercase next to `Java/`).

## Expanding Java notes

1. Use [update-java](update-java) as the source of truth for module order and topic checklist.
2. Extend existing files when the topic fits (`java-basics.md`, `java8.md`, `collection-framework.md`, `multi-threading.md`, `oop/oops.md`).
3. Add a new file under `Java/<area>/` only when the module is large enough to warrant it (JVM, GC, IO/NIO, generics, exceptions, strings, interview traps).
4. After adding a new note file, link it from [INIT.md](INIT.md) (and README where other Java entries appear).
5. Mark progress in [update-java](update-java) (status per module / topic).

## Do / don't

- **Do** keep notes concise and senior-interview focused.
- **Do** reuse and deepen existing docs before creating near-duplicates.
- **Don't** commit secrets, or rewrite large note files unless asked.
- **Don't** create commits unless the user explicitly requests them.
