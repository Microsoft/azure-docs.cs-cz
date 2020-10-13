---
title: Konfigurace front-endové rozhraní se službou Azure static Web Apps Preview
description: Nastavení oblíbených rozhraní front-end potřebných pro statickou Web Apps Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 07/18/2020
ms.author: cshoe
ms.openlocfilehash: 4b1bc58b6b4a87cd6e5e09e83020a38261b8746f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905369"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps-preview"></a>Konfigurace front-endové architektur a knihoven pomocí Azure static Web Apps Preview

Statická Web Apps Azure vyžaduje, abyste v [konfiguračním souboru sestavení](github-actions-workflow.md) pro front-end architekturu nebo knihovnu nastavili příslušné hodnoty konfigurace.

## <a name="configuration"></a>Konfigurace

V následující tabulce jsou uvedena nastavení pro řadu architektur a knihoven<sup>1</sup>.

Záměr sloupců tabulky je vysvětlen následujícími položkami:

- **Umístění artefaktu aplikace**: zobrazuje hodnotu pro `app_artifact_location` , což je [Složka pro sestavené verze souborů aplikace](github-actions-workflow.md#build-and-deploy).

- **Vlastní sestavení příkazu**: když rozhraní vyžaduje příkaz jiný než `npm run build` nebo `npm run azure:build` , můžete definovat [vlastní příkaz buildu](github-actions-workflow.md#custom-build-commands).

| Rozhraní .NET Framework | Umístění artefaktů aplikace | Vlastní sestavení – příkaz |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | není k dispozici <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [Úhlová světový](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Aurelia](https://aurelia.io/) | `dist` | neuvedeno |
| [Backbone.js](https://backbonejs.org/) | `/` | neuvedeno |
| [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) | `wwwroot` | neuvedeno |
| [Ember](https://emberjs.com/) | `dist` | neuvedeno |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Glimmer](https://glimmerjs.com/) | `dist` | neuvedeno |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | neuvedeno |
| [Hyperapp](https://hyperapp.dev/) | `/` | neuvedeno |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | neuvedeno |
| [jQuery](https://jquery.com/) | `/` | neuvedeno |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | neuvedeno |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | neuvedeno |
| [Marko](https://markojs.com/) | `public` | neuvedeno |
| [Meteor](https://www.meteor.com/) | `bundle` | neuvedeno |
| [Mithril](https://mithril.js.org/) | `dist` | neuvedeno |
| [Polymer](https://www.polymer-project.org/) | `build/default` | neuvedeno |
| [Preact](https://preactjs.com/) | `build` | neuvedeno |
| [React](https://reactjs.org/) | `build` | neuvedeno |
| [Kóty](https://stenciljs.com/) | `www` | neuvedeno |
| [Svelte](https://svelte.dev/) | `public` | neuvedeno |
| [Three.js](https://threejs.org/) | `/` | neuvedeno |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | neuvedeno |
| [Vue.js](https://vuejs.org/) | `dist` | neuvedeno |

<sup>1</sup> výše uvedená tabulka není vyčerpávajícím seznamem architektur a knihoven, které pracují se službou Azure static Web Apps.

<sup>2</sup> nelze použít

## <a name="next-steps"></a>Další kroky

- [Sestavení a konfigurace pracovního postupu](github-actions-workflow.md)
