---
title: Přizpůsobení API Management portálu pro vývojáře pomocí šablon
titleSuffix: Azure API Management
description: Naučte se, jak přizpůsobit portál Azure API Management pro vývojáře pomocí šablon.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 16c0b3e6dc4bd9097312e3a8c43618e66781ef85
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92092881"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Postup přizpůsobení portálu Azure API Management pro vývojáře pomocí šablon

Existují tři základní způsoby, kterými jde přizpůsobit portál pro vývojáře ve službě Azure API Management:

* [Úprava obsahu statických stránek a elementů rozložení stránek][modify-content-layout]
* [Aktualizace stylů použitých pro elementy stránek napříč portálem pro vývojáře][customize-styles]
* [Úprava šablon použitých pro stránky generované portálem][portal-templates] (vysvětlení v této příručce)

Šablony slouží k přizpůsobení obsahu stránek portálu pro vývojáře vygenerovaných systémem (například dokumentace k rozhraní API, produkty, ověřování uživatelů atd.). Pomocí syntaxe [DotLiquid](http://dotliquidmarkup.org/) a poskytnuté sady lokalizovaných řetězcových prostředků, ikon a ovládacích prvků stránky máte skvělou flexibilitu při konfiguraci obsahu stránek podle potřeby.

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>Přehled šablon portálu pro vývojáře

Úpravy šablon se provádí na **portálu pro vývojáře** , když se přihlašujete jako správce. Pokud se chcete dostat nejdřív, otevřete Azure Portal a klikněte na **portál pro vývojáře** na panelu nástrojů služby vaší instance API Management.

Chcete-li získat přístup k šablonám portálu pro vývojáře, klikněte na ikonu Přizpůsobit na levé straně a zobrazte nabídku vlastní nastavení a klikněte na **šablony**.

![Snímek obrazovky, který zvýrazní ikonu přizpůsobit, aby se zobrazila nabídka vlastní nastavení][api-management-customize-menu]

Seznam šablony obsahuje několik kategorií šablon pokrývajících různé stránky na portálu pro vývojáře. Každá šablona je odlišná, ale kroky pro jejich úpravu a publikování změn jsou stejné. Chcete-li upravit šablonu, klikněte na název šablony.

![Šablony portálu pro vývojáře][api-management-templates-menu]

Kliknutím na šablonu přejdete na stránku portálu pro vývojáře, která je přizpůsobitelná touto šablonou. V tomto příkladu se zobrazí šablona **seznam produktů** . Šablona **seznamu produktů** určuje oblast obrazovky určenou červeným obdélníkem.

![Šablona seznamu produktů][api-management-developer-portal-templates-overview]

Některé šablony, například šablony **profilů uživatelů** , přizpůsobují různé části stejné stránky.

![Šablony profilů uživatelů][api-management-user-profile-templates]

Editor pro jednotlivé šablony portálu pro vývojáře obsahuje dva oddíly, které se zobrazují v dolní části stránky. Na levé straně se zobrazí podokno úprav pro šablonu a na pravé straně se zobrazí datový model pro šablonu.

Podokno pro úpravu šablony obsahuje značky, které řídí vzhled a chování odpovídající stránky na portálu pro vývojáře. Označení v šabloně používá syntaxi [DotLiquid](http://dotliquidmarkup.org/) . Jedním z oblíbených editorů pro DotLiquid je [DotLiquid pro návrháře](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Všechny změny provedené v šabloně během úprav se v prohlížeči zobrazují v reálném čase, ale nevidíte pro vaše zákazníky, dokud šablonu [neuložíte](#to-save-a-template) a [nepublikujete](#to-publish-a-template) .

![Kód šablony][api-management-template]

Podokno **data šablony** poskytuje Průvodce datovým modelem pro entity, které jsou k dispozici pro použití v konkrétní šabloně. Poskytuje tento průvodce zobrazením živých dat, která jsou aktuálně zobrazená na portálu pro vývojáře. Podokna šablon můžete rozbalit kliknutím na obdélník v pravém horním rohu podokna **data šablony** .

![Datový model šablony][api-management-template-data]

V předchozím příkladu jsou na portálu pro vývojáře zobrazeny dva produkty, které byly načteny z dat zobrazených v podokně **data šablony** , jak je znázorněno v následujícím příkladu:

```json
{
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 2,
        "ShowAll": false,
        "PageCount": 1
    },
    "Filtering": {
        "Pattern": null,
        "Placeholder": "Search products"
    },
    "Products": [
        {
            "Id": "56ec64c380ed850042060001",
            "Title": "Starter",
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
            "Terms": "",
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        },
        {
            "Id": "56ec64c380ed850042060002",
            "Title": "Unlimited",
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
            "Terms": null,
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        }
    ]
}
```

Označení v šabloně **seznamu produktů** zpracovává data, aby poskytovala požadovaný výstup tím, že provede iteraci kolekcí produktů, aby zobrazila informace a odkaz na každý jednotlivý produkt. Poznamenejte `<search-control>` si `<page-control>` prvky a v označení. Tento ovládací prvek zobrazuje na stránce ovládací prvky hledání a stránkování. `ProductsStrings|PageTitleProducts` je lokalizovaný odkaz na řetězec, který obsahuje `h2` text záhlaví stránky. Seznam prostředků řetězců, ovládacích prvků stránky a ikon dostupných pro použití v šablonách portálu pro vývojáře naleznete v tématu [API Management Reference k šablonám portálu pro vývojáře](api-management-developer-portal-templates-reference.md).

```html
<search-control></search-control>
<div class="row">
    <div class="col-md-9">
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
    </div>
</div>
<div class="row">
    <div class="col-md-12">
    {% if products.size > 0 %}
    <ul class="list-unstyled">
    {% for product in products %}
        <li>
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
            {{product.description}}
        </li>
    {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    </div>
</div>
```

## <a name="to-save-a-template"></a>Uložení šablony
Chcete-li uložit šablonu, klikněte na Uložit v editoru šablon.

![Uložení šablony][api-management-save-template]

Uložené změny nejsou na portálu pro vývojáře aktivní, dokud nebudou publikované.

## <a name="to-publish-a-template"></a>Publikování šablony
Uložené šablony lze publikovat jednotlivě nebo společně. Chcete-li publikovat jednotlivé šablony, klikněte v editoru šablon na publikovat.

![Publikování šablony][api-management-publish-template]

Kliknutím na **Ano** potvrďte a zpřístupněte šablonu na portálu pro vývojáře.

![Snímek obrazovky, který ukazuje, kde vyberte Ano, aby se šablona mohla aktivovat.][api-management-publish-template-confirm]

Pokud chcete publikovat všechny aktuálně nepublikované verze šablon, v seznamu šablon klikněte na **publikovat** . Nepublikované šablony jsou označeny hvězdičkou, která následuje za názvem šablony. V tomto příkladu se zveřejňují **seznam produktů** a šablony **produktu** .

![Publikování šablon][api-management-publish-templates]

Kliknutím na **publikovat vlastní nastavení** potvrďte.

![Potvrdit publikování][api-management-publish-customizations]

Nově publikované šablony se okamžitě projeví na portálu pro vývojáře.

## <a name="to-revert-a-template-to-the-previous-version"></a>Vrácení šablony zpět na předchozí verzi
Chcete-li obnovit šablonu na předchozí publikovanou verzi, klikněte na tlačítko vrátit v editoru šablon.

![Snímek obrazovky, který zvýrazní ikonu použitou k vrácení šablony.][api-management-revert-template]

Pro potvrzení klikněte na tlačítko **Ano**.

![Snímek obrazovky, který ukazuje, kde chcete potvrdit změny, pokud vyberete Ano.][api-management-revert-template-confirm]

Dříve publikovaná verze šablony je po dokončení operace vrácení zpět na portálu pro vývojáře živá.

## <a name="to-restore-a-template-to-the-default-version"></a>Obnovení šablony do výchozí verze
Obnovení šablon do jejich výchozí verze je proces se dvěma kroky. Nejdřív je potřeba obnovit šablony a pak obnovit obnovené verze.

Chcete-li obnovit jednu šablonu do výchozí verze, klikněte na tlačítko obnovit v editoru šablon.

![Vrátit šablonu][api-management-reset-template]

Pro potvrzení klikněte na tlačítko **Ano**.

![Confirm][api-management-reset-template-confirm]

Chcete-li obnovit výchozí verze všech šablon, klikněte na tlačítko **Obnovit výchozí šablony** v seznamu šablon.

![Obnovit šablony][api-management-restore-templates]

Obnovené šablony musí být následně publikovány jednotlivě nebo současně pomocí postupu v části [pro publikování šablony](#to-publish-a-template).

## <a name="next-steps"></a>Další kroky
Referenční informace pro šablony portálu pro vývojáře, prostředky řetězců, ikony a ovládací prvky stránky naleznete v tématu [API Management Reference k šablonám portálu pro vývojáře](api-management-developer-portal-templates-reference.md).

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-management-console]: ./media/api-management-developer-portal-templates/api-management-management-console.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png
