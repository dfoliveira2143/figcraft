# FigCraft — funcionalidades e módulos

Última atualização: 2026-10-28.

## Fluxo principal

| Etapa | Entradas | Responsabilidade e dependências | Saídas |
|---|---|---|---|
| Abrir e editar | Árvore de nós, imagens, texto e comandos | [doc](../../crates/doc/), [layout](../../crates/layout/), [text](../../crates/text/), [engine](../../crates/engine/) | Documento com páginas, componentes e protótipo |
| Avaliar e produzir saída | Estado editado e parâmetros de saída | [render](../../crates/render/), [format](../../crates/format/), [svg](../../crates/svg/), [pdf](../../crates/pdf/), [collab](../../crates/collab/), [collab-net](../../crates/collab-net/) | Imagens, arquivos de design e alterações sincronizadas |
| Acesso pelo usuário ou agente | Gestos, argumentos CLI ou chamadas estruturadas | [apps](../../apps/), [engine](../../crates/engine/) e interfaces descritas abaixo | Estado atualizado, resposta ou erro; comportamento detalhado nos contratos |

A tabela mostra o caminho conceitual, não uma garantia de suporte a todos os formatos. APIs, estruturas, unidades, efeitos colaterais e limites ficam na [arquitetura](../architecture.md), nos documentos de assunto e nos módulos indicados.

## Contratos e exemplos de uso

- [auto-layout-parity.md](../auto-layout-parity.md)
- [components-parity.md](../components-parity.md)
- [prototyping-parity.md](../prototyping-parity.md)
- [server.md](../server.md)
- [collab-protocol.md](../collab-protocol.md)
- [control-protocol.md](../control-protocol.md)
- [mcp.md](../mcp.md)

## Inventário de bibliotecas

Responsabilidades resumidas a partir dos manifestos e comentários de módulo do checkout. As dependências listadas são as internas diretas declaradas em `dependencies` (incluindo condicionais); não incluem dependências de teste/build nem expansão de features. O manifesto de cada linha é a fonte exata.

| Módulo / manifesto | Responsabilidade declarada | Dependências internas diretas |
|---|---|---|
| [collab](../../crates/collab/Cargo.toml) | FigCraft real-time collaboration core: the synced object/property model, change sets, fractional positions, server ordering and validation, client reconciliation, per-user undo, offline queueing and reconnect, and the wire messages (no networking) | `figcraft-doc` |
| [collab-net](../../crates/collab-net/Cargo.toml) | FigCraft live sessions over the network: the WebSocket transport, the session host the app embeds, invite links and presence relay (carries figcraft-collab's messages) | `figcraft-collab`, `figcraft-doc`, `figcraft-engine` |
| [color](../../crates/color/Cargo.toml) | FigCraft colour and paint: RGBA colours, HSB/HSL/LCH/OKLCH, hex/CSS parsing, solid/gradient/image paints, blend modes | Nenhuma declarada nesta categoria |
| [doc](../../crates/doc/Cargo.toml) | FigCraft document model: pages, the node tree, properties, components, styles, variables, prototype data | `figcraft-color`, `figcraft-geom` |
| [engine](../../crates/engine/Cargo.toml) | FigCraft engine: sessions, undo history, the command registry every surface (UI, CLI, control channel, MCP) dispatches through, pointer tools, components and the Figma menu catalog | `figcraft-collab`, `figcraft-color`, `figcraft-doc`, `figcraft-format`, `figcraft-geom`, `figcraft-layout`, `figcraft-pathops`, `figcraft-pdf`, `figcraft-plugin`, `figcraft-render`, `figcraft-svg`, `figcraft-text`, `figcraft-vectordrawable` |
| [format](../../crates/format/Cargo.toml) | FigCraft native file format (.figcraft: zip of document.json + images), image import helpers, REST API file JSON import and .fig import | `figcraft-doc`, `figcraft-pathops` |
| [geom](../../crates/geom/Cargo.toml) | FigCraft geometry: affine transforms, shape path builders (rounded/smoothed corners, polygons, stars, arcs) | Nenhuma declarada nesta categoria |
| [layout](../../crates/layout/Cargo.toml) | FigCraft layout: auto layout (horizontal, vertical, wrap, grid; hug/fill/fixed), constraints, layout grid geometry | `figcraft-doc`, `figcraft-geom` |
| [mcp](../../crates/mcp/Cargo.toml) | FigCraft MCP server: JSON-RPC 2.0 over stdio, driving a headless engine session or a running FigCraft app | `figcraft-collab-net`, `figcraft-doc`, `figcraft-engine`, `figcraft-geom`, `figcraft-render` |
| [pathops](../../crates/pathops/Cargo.toml) | FigCraft path operations: booleans (union, subtract, intersect, exclude), outline stroke with inside/center/outside alignment, arrowheads | `figcraft-doc`, `figcraft-geom` |
| [pdf](../../crates/pdf/Cargo.toml) | FigCraft PDF export (krilla): vector shapes, gradients, embedded images, aligned strokes as outlines, clips, masks, blend modes, real selectable text, effects as rasters, print options (bleed, marks, page boxes, CMYK) | `figcraft-color`, `figcraft-doc`, `figcraft-geom`, `figcraft-pathops`, `figcraft-render`, `figcraft-text` |
| [plugin](../../crates/plugin/Cargo.toml) | FigCraft plugins: the plugin manifest and the WebAssembly sandbox plugins run in (a metered interpreter with no file system, network or clock) | `figcraft-color` |
| [render](../../crates/render/Cargo.toml) | FigCraft renderer: the scene graph to pixels on tiny-skia (fills, gradients, images, aligned strokes, shadows, blurs, masks, blend modes, text) | `figcraft-color`, `figcraft-doc`, `figcraft-geom`, `figcraft-pathops`, `figcraft-text` |
| [svg](../../crates/svg/Cargo.toml) | FigCraft SVG export (shapes, gradients, images, strokes, effects as filters, text as outlines) and import (via usvg) | `figcraft-color`, `figcraft-doc`, `figcraft-geom`, `figcraft-pathops`, `figcraft-render`, `figcraft-text` |
| [text](../../crates/text/Cargo.toml) | FigCraft text: font database (embedded craft-fonts + system fonts), shaping, line layout, glyph outlines | `figcraft-doc`, `figcraft-geom` |
| [ui-egui](../../crates/ui-egui/Cargo.toml) | FigCraft egui frontend (the Figma-style UI: canvas, toolbar, layers, design and prototype panels, menus); swappable: nothing below depends on it | `figcraft-color`, `figcraft-doc`, `figcraft-engine`, `figcraft-format`, `figcraft-geom`, `figcraft-layout`, `figcraft-pathops`, `figcraft-render`, `figcraft-text` |
| [vectordrawable](../../crates/vectordrawable/Cargo.toml) | FigCraft Android Vector Drawable export (shapes, strokes, gradients, clips and vector masks as a <vector> XML drawable) | `figcraft-color`, `figcraft-doc`, `figcraft-geom`, `figcraft-pathops`, `figcraft-render`, `figcraft-text` |

Ao mudar uma entrada, saída ou dependência, atualize primeiro o contrato do módulo e depois esta navegação. [Voltar ao índice](../INDEX.md).
