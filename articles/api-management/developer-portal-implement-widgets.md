---
title: Implementace widgetů na portálu pro vývojáře
titleSuffix: Azure API Management
description: Naučte se implementovat widgety, které využívají data z externích rozhraní API, a zobrazit je na portálu pro vývojáře API Management.
author: dlepow
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 4bda136a1abe8f9ffb443973731ebbe13b56ac3b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741618"
---
# <a name="implement-widgets-in-the-developer-portal"></a>Implementace widgetů na portálu pro vývojáře

V tomto kurzu implementujete pomůcku, která využívá data z externího rozhraní API a zobrazuje ji na portálu pro vývojáře API Management.

Widget načte popisy relací z ukázkové [konferenčního rozhraní API](https://conferenceapi.azurewebsites.net/?format=json). Identifikátor relace se nastaví pomocí určeného editoru widgetů.

V rámci procesu vývoje vám pomůžou informace o dokončeném widgetu ve `examples` složce API Management v [úložišti GitHub](https://github.com/Azure/api-management-developer-portal/)portálu pro vývojáře: `/examples/widgets/conference-session` .

:::image type="content" source="media/developer-portal-implement-widgets/widget-published.png" alt-text="Snímek obrazovky publikované pomůcky":::

## <a name="prerequisites"></a>Požadavky

* Nastavte [místní prostředí](developer-portal-self-host.md#step-1-set-up-local-environment) pro nejnovější vydání portálu pro vývojáře.

* Měli byste pochopit [anatomii widgetu Paperbits](https://paperbits.io/wiki/widget-anatomy).


## <a name="copy-the-scaffold"></a>Kopírovat generování uživatelského rozhraní

`widget` `/scaffolds` Pro sestavování nového widgetu použijte jako výchozí bod generování uživatelského rozhraní ze složky.

1. Zkopírujte složku `/scaffolds/widget` do složky `/community/widgets` .
1. Přejmenujte složku na `conference-session` .

## <a name="rename-exported-module-classes"></a>Přejmenovat třídy exportovaných modulů

Přejmenujte exportované třídy modulu nahrazením `Widget` předpony `ConferenceSession` v těchto souborech:

- `widget.design.module.ts`

- `widget.publish.module.ts`

- `widget.runtime.module.ts`
    
Například v `widget.design.module.ts` souboru změňte `WidgetDesignModule` na `ConferenceSessionDesignModule` :
    
```typescript
export class WidgetDesignModule implements IInjectorModule {
```
na 
    
```typescript
export class ConferenceSessionDesignModule implements IInjectorModule {
```
    
   
## <a name="register-the-widget"></a>Zaregistrovat pomůcku

Zaregistrujte moduly widgetu v kořenových modulech portálu přidáním následujících řádků do příslušných souborů:

1. `src/apim.design.module.ts` – modul, který registruje závislosti v době návrhu.

   ```typescript
   import { ConferenceSessionDesignModule } from "../community/widgets/conference-session/widget.design.module";
   
   ...
   injector.bindModule(new ConferenceSessionDesignModule());
   ```
1. `src/apim.publish.module.ts` – modul, který zaregistruje závislosti při publikování.

   ```typescript
   import { ConferenceSessionPublishModule } from "../community/widgets/conference-session/widget.publish.module";

    ...

    injector.bindModule(new ConferenceSessionPublishModule());
    ```

1. `src/apim.runtime.module.ts` – závislosti modulu runtime.

    ```typescript
    import { ConferenceSessionRuntimeModule } from "../community/widgets/conference-session/widget.runtime.module";

    ...

    injector.bindModule(new ConferenceSessionRuntimeModule());
    ```

## <a name="place-the-widget-in-the-portal"></a>Umístit widget na portál

Nyní jste připraveni připojit duplikované uživatelské rozhraní a použít ho na portálu pro vývojáře.

1. Spusťte příkaz `npm start`.

1. Po načtení aplikace umístit novou pomůcku na stránku. Můžete ji najít pod názvem `Your widget` v `Community` kategorii v selektoru pomůcky.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-selector.png" alt-text="Snímek obrazovky s selektorem widgetů":::

1. Uložte stránku stisknutím **kombinace kláves CTRL** + **S** (nebo **⌘** + **s** v MacOS).

    > [!NOTE]
    > V době návrhu můžete i nadále pracovat s webem tím, že podržíte klávesu **CTRL** (nebo **⌘**).

## <a name="add-custom-properties"></a>Přidat vlastní vlastnosti

Aby pomůcka načetla popisy relací, musí znát identifikátor relace. Přidejte `Session ID` vlastnost do příslušných rozhraní a tříd:

Aby widget mohl načíst popis relace, musí znát identifikátor relace. Přidejte vlastnost ID relace do příslušných rozhraní a tříd:

1. `widgetContract.ts` -kontrakt dat (Datová vrstva) definující způsob zachování konfigurace pomůcky.

    ```typescript
    export interface WidgetContract extends Contract {
        sessionNumber: string;
    }
    ```

1. `widgetModel.ts` -Model (obchodní vrstva) – primární reprezentace widgetu v systému. Je aktualizována editory a vykreslena prezentační vrstvou.

    ```typescript
    export class WidgetModel {
        public sessionNumber: string;
    }
    ```

1. `ko/widgetViewModel.ts` -ViewModel (prezentační vrstva) – objekt specifický pro rozhraní .NET Framework, který portál pro vývojáře vykresluje pomocí šablony HTML.

    > [!NOTE]
    > V tomto souboru nemusíte nic měnit.

## <a name="configure-binders"></a>Konfigurace pojiv

Povolte tok `sessionNumber` ze zdroje dat do prezentace widgetu. Upravte `ModelBinder` entity a `ViewModelBinder` :

1. `widgetModelBinder.ts` pomáhá připravit model pomocí dat popsaných v kontraktu.

    ```typescript
    export class WidgetModelBinder implements IModelBinder<WidgetModel> {
        public async contractToModel(contract: WidgetContract): Promise<WidgetModel> {
            model.sessionNumber = contract.sessionNumber || "107"; // 107 is the default session id
            ...
        }
    
        public modelToContract(model: WidgetModel): Contract {
            const contract: WidgetContract = {
                sessionNumber: model.sessionNumber
                ...
            };
            ...
        }
    }
    ```

1. `ko/widgetViewModelBinder.ts` ví, jak portál pro vývojáře potřebuje k dispozici model (jako ViewModel) v konkrétní architektuře uživatelského rozhraní.

    ```typescript
    ...
    public async updateViewModel(model: WidgetModel, viewModel: WidgetViewModel): Promise<void> {
            viewModel.runtimeConfig(JSON.stringify({
                sessionNumber: model.sessionNumber
            }));
        }
    }
    ...
    ```

## <a name="adjust-design-time-widget-template"></a>Úprava šablony pomůcky pro dobu návrhu

Komponenty každého oboru jsou spouštěny nezávisle. Mají samostatné kontejnery pro vkládání závislostí, jejich vlastní konfiguraci, životní cyklus atd. Mohou dokonce být napájeny pomocí různých architektur uživatelského rozhraní (v tomto příkladu je vyseknutí JS).

V perspektivě návrhu je jakákoli běhová komponenta pouze značkou HTML s určitými atributy nebo obsahem. V případě potřeby je konfigurace předána s prostým označením. V jednoduchých případech, jako v tomto příkladu, je parametr předán v atributu. Pokud je konfigurace složitější, můžete použít identifikátor požadovaných nastavení načtených určeným poskytovatelem konfigurace (například `ISettingsProvider` ).

1. Aktualizujte `ko/widgetView.html` soubor:

    ```html
    <widget-runtime data-bind="attr: { params: runtimeConfig }"></widget-runtime>
    ```

    Když portál pro vývojáře spouští `attr` vazbu v *době návrhu* nebo *publikování*, výsledný kód HTML:

    ```html
    <widget-runtime params="{ sessionNumber: 107 }"></widget-runtime>
    ```

    Pak se v modulu runtime `widget-runtime` přečte komponenta `sessionNumber` a použije se v inicializačním kódu (viz níže).

1. Aktualizujte `widgetHandlers.ts` soubor, aby při vytváření PŘIŘADIL ID relace:

    ```typescript
    ...
    createModel: async () => {
        var model = new ConferenceSessionModel();
        model.sessionNumber = "107";
            return model;
        }
    ...
    ```

## <a name="revise-runtime-view-model"></a>Revidovat běhový model zobrazení

Běhové komponenty jsou kód spuštěný na samotném webu. Například na portálu pro vývojáře API Management jsou všechny skripty za dynamickými součástmi (například *Podrobnosti rozhraní API*, *Konzola rozhraní API*), zpracování operací, jako je generování ukázek kódu, odesílání požadavků atd.

Model zobrazení komponenty modulu runtime musí mít následující metody a vlastnosti:

- `sessionNumber`Vlastnost (označená pomocí `Param` dekoratér) použitá jako vstupní parametr komponenty předaný mimo (značky vygenerované v době návrhu; viz předchozí krok).
- `sessionDescription`Vlastnost vázaná na šablonu pomůcky (viz `widget-runtime.html` dále v tomto článku).
- `initialize`Metoda (with `OnMounted` dekoratér) se vyvolala po vytvoření widgetu a k přiřazení všech jeho parametrů. Je dobrým místem pro čtení `sessionNumber` a vyvolání rozhraní API pomocí `HttpClient` . `HttpClient`Je závislost vložená pomocí kontejneru IOC (inverze Control).

- Portál pro vývojáře nejprve vytvoří widget a přiřadí všechny jeho parametry. Poté vyvolá `initialize` metodu.

    ```typescript
    ...
    import * as ko from "knockout";
    import { Component, RuntimeComponent, OnMounted, OnDestroyed, Param } from "@paperbits/common/ko/decorators";
    import { HttpClient, HttpRequest } from "@paperbits/common/http";
    ...

    export class WidgetRuntime {
        public readonly sessionDescription: ko.Observable<string>;

        constructor(private readonly httpClient: HttpClient) {
            ...
            this.sessionNumber = ko.observable();
            this.sessionDescription = ko.observable();
            ...
        }

        @Param()
        public readonly sessionNumber: ko.Observable<string>;

        @OnMounted()
        public async initialize(): Promise<void> {
            ...
            const sessionNumber = this.sessionNumber();

            const request: HttpRequest = {
                url: `https://conferenceapi.azurewebsites.net/session/${sessionNumber}`,
                method: "GET"
            };

            const response = await this.httpClient.send<string>(request);
            const sessionDescription = response.toText();
    
            this.sessionDescription(sessionDescription);
            ...
        }
        ...
    }
    ```

## <a name="tweak-the-widget-template"></a>Selepšit šablonu widgetu

Aktualizujte widget, aby se zobrazil popis relace.

Pomocí značky odstavce a `markdown` (nebo `text` ) vazby v `ko/runtime/widget-runtime.html` souboru vykreslete Popis:

```html
<p data-bind="markdown: sessionDescription"></p>
```

## <a name="add-the-widget-editor"></a>Přidat Editor widgetů

Pomůcka je nyní nakonfigurována tak, aby načetla popis relace `107` . Zadali jste `107` v kódu jako výchozí relaci. Pokud chcete zkontrolovat, jestli jste všechno napravo, spusťte `npm start` a potvrďte, že na stránce portál pro vývojáře je zobrazený popis.

Nyní proveďte tyto kroky, aby uživatel mohl nastavit ID relace pomocí editoru widgetů:

1. Aktualizujte `ko/widgetEditorViewModel.ts` soubor:

    ```typescript
    export class WidgetEditor implements WidgetEditor<WidgetModel> {
        public readonly sessionNumber: ko.Observable<string>;

        constructor() {
            this.sessionNumber = ko.observable();
        }

        @Param()
        public model: WidgetModel;

        @Event()
        public onChange: (model: WidgetModel) => void;

        @OnMounted()
        public async initialize(): Promise<void> {
            this.sessionNumber(this.model.sessionNumber);
            this.sessionNumber.subscribe(this.applyChanges);
        }

        private applyChanges(): void {
            this.model.sessionNumber = this.sessionNumber();
            this.onChange(this.model);
        }
    }
    ```

    Model zobrazení editoru používá stejný přístup, který jste viděli dříve, ale existuje nová vlastnost `onChange` dekorovaná pomocí `@Event()` . IT vodiče volá zpětné volání pro upozornění posluchačů (v tomto případě Editor obsahu) změn modelu.

1. Aktualizujte `ko/widgetEditorView.html` soubor:

    ```html
    <input type="text" class="form-control" data-bind="textInput: sessionNumber" />
    ```

1. Spusťte `npm start` znovu. V editoru widgetů byste měli být schopní měnit `sessionNumber` . Změňte ID na `108` , uložte změny a aktualizujte kartu prohlížeče. Pokud máte problémy, možná budete muset přidat widget na stránku znovu.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-editor.png" alt-text="Snímek obrazovky s editorem widgetů":::

## <a name="rename-the-widget"></a>Přejmenování widgetu

Změňte název widgetu v `constants.ts` souboru:

```typescript
...
export const widgetName = "conference-session";
export const widgetDisplayName = "Conference session";
...
```

> [!NOTE]
> Pokud přispějete k widgetu do úložiště, `widgetName` musí být stejný jako název složky a musí být odvozen od zobrazovaného názvu (malými písmeny a mezerami nahrazenými pomlčkami). Kategorie by měla zůstat zachována `Community` .

## <a name="next-steps"></a>Další kroky


Další informace o portálu pro vývojáře:

- [Přehled vývojářského portálu pro službu Azure API Management](api-management-howto-developer-portal.md)

- [Widgety pro Contribute](developer-portal-widget-contribution-guidelines.md) – Vítejte a podporujeme komunitní příspěvky.

- V tématu [Použití widgetů komunity](developer-portal-use-community-widgets.md) zjistíte, jak používat widgety, které přispívají komunitou.
