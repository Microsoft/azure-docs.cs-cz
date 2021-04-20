---
title: Jak přispívat widgetů pro portál pro vývojáře
titleSuffix: Azure API Management
description: Přečtěte si o doporučených pokynech, které vám pomohou při přispívání widgetu do úložiště portálu pro vývojáře API Management.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: c4d3ed2aeaac57f721d23d7c7aa1c70ef14e4294
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741626"
---
# <a name="how-to-contribute-widgets-to-the-api-management-developer-portal"></a>Jak přispět widgety na portál pro vývojáře API Management

Pokud chcete přispět pomůcku do [úložiště GitHub](https://github.com/Azure/api-management-developer-portal)portálu pro vývojáře API Management, postupujte podle tohoto procesu se třemi kroky:

1. Rozvětvení úložiště

1. Implementujte widget.

1. Otevřete žádost o přijetí změn pro zahrnutí widgetu do oficiálního úložiště.

Widget bude dědit licenci úložiště. Bude k dispozici pro [instalaci výslovných souhlasu](developer-portal-use-community-widgets.md) v samoobslužné verzi portálu. Tým portálu pro vývojáře se může rozhodnout také zahrnout do spravované verze portálu.

Příklad vývoje vlastního widgetu najdete v kurzu [implementace pomůcky](developer-portal-implement-widgets.md) .

## <a name="contribution-guidelines"></a>Pokyny k příspěvku

Tato příručka je určená k zajištění bezpečnosti a ochrany soukromí našich zákazníků a návštěvníků jejich portálů. Pokud chcete zajistit, aby váš příspěvek byl přijatý, postupujte podle těchto pokynů:

1. Dejte widget do `community/widgets/<your-widget-name>` složky.

1. Název widgetu musí být malými písmeny a alfanumerickými pomlčkami, které odděluje slova. Například, `my-new-widget`.

1. Složka musí obsahovat snímek obrazovky widgetu na publikovaném portálu.

1. Složka musí obsahovat `readme.md` soubor, který následuje za šablonou ze `/scaffolds/widget/readme.md` souboru.

1. Složka může obsahovat `npm_dependencies` soubor s npm příkazy pro instalaci nebo správu závislostí widgetu.

    Explicitně zadejte verzi každé závislosti. Například:  

    ```console
    npm install azure-storage@2.10.3 axios@0.19.1
    ```

    Widget by měl vyžadovat minimální závislosti. Všechny závislosti se pečlivě kontrolují kontrolory. Konkrétně by měla být základní logika widgetu ve složce widgetu otevřená. Nezalomí ho do balíčku npm.

1. Změny všech souborů mimo složku widgetu nejsou povolené jako součást příspěvku widgetu. Který zahrnuje, ale není omezen na `/package.json` soubor.

1. Vkládání skriptů pro sledování nebo posílání zákaznických dat do vlastních služeb není povoleno.

    > [!NOTE]
    > Data vytvořená zákazníkem můžete shromažďovat jenom prostřednictvím `Logger` rozhraní.

## <a name="next-steps"></a>Další kroky

- Další informace o příspěvcích najdete v tématu [úložiště GitHub](https://github.com/Azure/api-management-developer-portal/)na portálu pro vývojáře API Management.

- V tématu [implementace widgetů](developer-portal-implement-widgets.md) se naučíte vyvíjet vlastní widget, krok za krokem.

- V tématu [Použití widgetů komunity](developer-portal-use-community-widgets.md) zjistíte, jak používat widgety, které přispívají komunitou.