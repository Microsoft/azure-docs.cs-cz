---
title: Konfigurace front-endové rozhraní se službou Azure static Web Apps Preview
description: Nastavení oblíbených rozhraní front-end potřebných pro statickou Web Apps Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: cshoe
ms.openlocfilehash: 41f0c44ad65af5fdf2560da3f977a28f135af878
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84976706"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps-preview"></a>Konfigurace front-endové architektur a knihoven pomocí Azure static Web Apps Preview

Statická Web Apps Azure vyžaduje, abyste v [konfiguračním souboru sestavení](github-actions-workflow.md) pro front-end architekturu nebo knihovnu nastavili příslušné hodnoty konfigurace.

## <a name="configuration"></a>Konfigurace

V následující tabulce jsou uvedena nastavení pro řadu architektur a knihoven<sup>1</sup>.

Záměr sloupců tabulky je vysvětlen následujícími položkami:

- **Umístění artefaktu aplikace**: zobrazuje hodnotu pro `app_artifact_location` , což je [Složka pro sestavené verze souborů aplikace](github-actions-workflow.md#build-and-deploy).

- **Vlastní sestavení příkazu**: když rozhraní vyžaduje příkaz jiný než `npm run build` nebo `npm run azure:build` , můžete definovat [vlastní příkaz buildu](github-actions-workflow.md#custom-build-commands).

| Rozhraní .NET Framework | Umístění artefaktu aplikace | Vlastní sestavení – příkaz |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | není k dispozici <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [Úhlová světový](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Aurelia](https://aurelia.io/) | `dist` | Není k dispozici |
| [Backbone.js](https://backbonejs.org/) | `/` | Není k dispozici |
| [Ember](https://emberjs.com/) | `dist` | Není k dispozici |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Glimmer](https://glimmerjs.com/) | `dist` | Není k dispozici |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | Není k dispozici |
| [Hyperapp](https://hyperapp.dev/) | `/` | Není k dispozici |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | Není k dispozici |
| [jQuery](https://jquery.com/) | `/` | Není k dispozici |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | Není k dispozici |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | Není k dispozici |
| [Marko](https://markojs.com/) | `public` | Není k dispozici |
| [Meteor](https://www.meteor.com/) | `bundle` | Není k dispozici |
| [Mithril](https://mithril.js.org/) | `dist` | Není k dispozici |
| [Polymer](https://www.polymer-project.org/) | `build/default` | Není k dispozici |
| [Preact](https://preactjs.com/) | `build` | Není k dispozici |
| [React](https://reactjs.org/) | `build` | Není k dispozici |
| [Kóty](https://stenciljs.com/) | `www` | Není k dispozici |
| [Svelte](https://svelte.dev/) | `public` | Není k dispozici |
| [Three.js](https://threejs.org/) | `/` | Není k dispozici |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | Není k dispozici |
| [Vue](http://vuejs.com/) | `dist` | Není k dispozici |

<sup>1</sup> výše uvedená tabulka není vyčerpávajícím seznamem architektur a knihoven, které pracují se službou Azure static Web Apps.

<sup>2</sup> nelze použít

## <a name="next-steps"></a>Další kroky

- [Sestavení a konfigurace pracovního postupu](github-actions-workflow.md)
