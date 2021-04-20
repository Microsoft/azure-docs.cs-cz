---
title: Použití widgetů komunity na portálu pro vývojáře
titleSuffix: Azure API Management
description: Přečtěte si o widgetech komunity pro API Management portálu pro vývojáře a o tom, jak je vložit a používat ve svém kódu.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: de6160aa2e556297287ae9e9ecd58a93e54d863f
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741603"
---
# <a name="use-community-widgets-in-the-developer-portal"></a>Použití widgetů komunity na portálu pro vývojáře

Všichni vývojáři umístí své widgety komunity do `/community/widgets/` složky API Management [úložiště GitHub](https://github.com/Azure/api-management-developer-portal)na portálu pro vývojáře. Každý z nich přijal tým portálu pro vývojáře. Widgety můžete používat tak, že je vložíte do své [samoobslužné verze](developer-portal-self-host.md) portálu. Spravovaná verze portálu pro vývojáře v současné době nepodporuje widgety komunity.

> [!NOTE]
> Tým portálu pro vývojáře důkladně kontroluje příspěvky a jejich závislosti. Tým ale nemůže zaručit, že se tyto widgety dají bezpečně načíst. Využijte svůj vlastní rozsudk při rozhodování o používání widgetu, který přispěla komunita. Další informace o našich preventivních opatřeních najdete v našich [pokynech k příspěvku widgetů](developer-portal-widget-contribution-guidelines.md#contribution-guidelines) .

## <a name="inject-and-use-external-widgets"></a>Vkládání a používání externích widgetů

1. Nastavte [místní prostředí](developer-portal-self-host.md#step-1-set-up-local-environment) pro nejnovější vydání portálu pro vývojáře.

1. Přejít do složky widgetu v `/community/widgets` adresáři. Přečtěte si popis widgetu v `readme.md` souboru.

1. Zaregistrujte widget v modulech portálu:

    1. `src/apim.design.module.ts` – modul, který registruje závislosti v době návrhu.
    
        ```typescript
        import { WidgetNameDesignModule } from "../community/widgets/<widget-name>/widget.design.module";
    
        ...
    
            injector.bindModule(new WidgetNameDesignModule());
        ```
    
    1. `src/apim.publish.module.ts` – modul, který zaregistruje závislosti při publikování.
    
        ```typescript
        import { WidgetNamePublishModule } from "../community/widgets/<widget-name>/widget.publish.module";
    
        ...
    
            injector.bindModule(new WidgetNamePublishModule());
        ```
    
    1. `src/apim.runtime.module.ts` – modul, který registruje závislosti za běhu.
    
        ```typescript
        import { WidgetNameRuntimeModule } from "../community/widgets/<widget-name>/widget.runtime.module";
    
        ...
    
            injector.bindModule(new WidgetNameRuntimeModule());
        ```

1. Ověřte, zda widget obsahuje `npm_dependencies` soubor.

1. Pokud ano, zkopírujte příkazy ze souboru a spusťte je v horním adresáři úložiště.

    Tím se nainstalují závislosti widgetu.

1. Spusťte `npm start`.

Pomůcku můžete zobrazit v kategorii **komunita** v selektoru widgetů.

:::image type="content" source="media/developer-portal-use-community-widgets/widget-selector.png" alt-text="Snímek obrazovky s selektorem widgetů":::


## <a name="next-steps"></a>Další kroky


Další informace o portálu pro vývojáře:

- [Přehled vývojářského portálu pro službu Azure API Management](api-management-howto-developer-portal.md)

- [Widgety pro Contribute](developer-portal-widget-contribution-guidelines.md)
