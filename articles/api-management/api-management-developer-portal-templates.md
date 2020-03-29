---
title: Přizpůsobení portálu pro vývojáře správy rozhraní API pomocí šablon
titleSuffix: Azure API Management
description: Zjistěte, jak přizpůsobit portál pro vývojáře Azure API Management pomocí šablon.
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
ms.openlocfilehash: 7a8c348340be143f7059ce7e64a1c66b66074a45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430791"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Jak přizpůsobit portál pro vývojáře Azure API Management pomocí šablon

Existují tři základní způsoby, kterými jde přizpůsobit portál pro vývojáře ve službě Azure API Management:

* [Úprava obsahu statických stránek a elementů rozložení stránek][modify-content-layout]
* [Aktualizace stylů použitých pro elementy stránek napříč portálem pro vývojáře][customize-styles]
* [Úprava šablon používaných pro stránky generované portálem][portal-templates] (vysvětleno v této příručce)

Šablony se používají k přizpůsobení obsahu stránek portálu pro vývojáře generovaných systémem (například dokumenty rozhraní API, produkty, ověřování uživatelů atd.). Pomocí syntaxe [DotLiquid](http://dotliquidmarkup.org/) a za předpokladu, sadu lokalizovaných řetězcových prostředků, ikon a ovládacích prvků stránky, máte velkou flexibilitu ke konfiguraci obsahu stránek, jak uznáte za vhodné.

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>Přehled šablon portálů pro vývojáře

Úpravy šablon se provádí z **portálu pro vývojáře,** zatímco jsou přihlášeni jako správce. Pokud se tam dostanete, nejdřív otevřete portál Azure a z panelu nástrojů služby instance Api Management klikněte na **portál pro vývojáře.**

Chcete-li získat přístup k šablonám portálu pro vývojáře, klepněte na ikonu přizpůsobení vlevo, chcete-li zobrazit nabídku vlastního nastavení, a klepněte na **položku Šablony**.

![Šablony portálu pro vývojáře][api-management-customize-menu]

Seznam šablon zobrazuje několik kategorií šablon pokrývajících různé stránky na portálu pro vývojáře. Každá šablona je jiná, ale kroky k jejich úpravě a publikování změn jsou stejné. Pokud chcete šablonu upravit, klikněte na její název.

![Šablony portálu pro vývojáře][api-management-templates-menu]

Kliknutím na šablonu přejdete na stránku portálu pro vývojáře, kterou lze přizpůsobit podle této šablony. V tomto příkladu se zobrazí šablona **seznamu produktů.** Šablona **seznamu produktů** určuje oblast obrazovky označenou červeným obdélníkem.

![Šablona seznamu produktů][api-management-developer-portal-templates-overview]

Některé šablony, například šablony **profilu uživatele,** přizpůsobují různé části stejné stránky.

![Šablony profilů uživatelů][api-management-user-profile-templates]

Editor pro každou šablonu portálu pro vývojáře má dvě části zobrazené v dolní části stránky. Na levé straně se zobrazí podokno úprav šablony a na pravé straně se zobrazí datový model šablony.

Podokno úprav šablony obsahuje značky, které řídí vzhled a chování odpovídající stránky na portálu pro vývojáře. Značky v šabloně používá syntaxi [DotLiquid.](http://dotliquidmarkup.org/) Jeden populární editor pro DotLiquid je [DotLiquid pro designéry](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Všechny změny provedené v šabloně během úprav jsou v prohlížeči zobrazeny v reálném čase, ale nejsou viditelné pro zákazníky, dokud šablonu [neuložíte](#to-save-a-template) a [nepublikujete.](#to-publish-a-template)

![Značky šablony][api-management-template]

Podokno **dat šablony** poskytuje průvodce datovým modelem pro entity, které jsou k dispozici pro použití v určité šabloně. Poskytuje tuto příručku zobrazením živých dat, která jsou aktuálně zobrazena na portálu pro vývojáře. Podokna předloh můžete rozbalit klepnutím na obdélník v pravém horním rohu **podokna dat šablony.**

![Datový model šablony][api-management-template-data]

V předchozím příkladu jsou na portálu pro vývojáře zobrazeny dva produkty, které byly načteny z dat zobrazených v podokně **dat šablony,** jak je znázorněno v následujícím příkladu:

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

Značka v šabloně **seznamu produktů** zpracovává data tak, aby poskytovala požadovaný výstup iteracem prostřednictvím kolekce produktů pro zobrazení informací a odkaz na každý jednotlivý produkt. Všimněte `<search-control>` `<page-control>` si prvků a ve značkách. Tyto ovládací prvky řídí zobrazení ovládacích prvků hledání a stránkování na stránce. `ProductsStrings|PageTitleProducts`je lokalizovaný odkaz na `h2` řetězec, který obsahuje text záhlaví stránky. Seznam prostředků řetězce, ovládacíprvky stránky a ikony, které jsou k dispozici pro použití v šablonách portálu pro vývojáře, najdete v [tématu Odkaz na šablony portálu pro vývojáře pro správu rozhraní API](api-management-developer-portal-templates-reference.md).

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
Pokud chcete šablonu uložit, klikněte v editoru šablon na Uložit.

![Uložení šablony][api-management-save-template]

Uložené změny nejsou na portálu pro vývojáře aktivní, dokud nebudou publikovány.

## <a name="to-publish-a-template"></a>Publikování šablony
Uložené šablony lze publikovat jednotlivě nebo společně. Chcete-li publikovat jednotlivé šablony, klikněte na publikovat v editoru šablon.

![Publikování šablony][api-management-publish-template]

Klepnutím na tlačítko **Ano** potvrďte a vytvořte šablonu aktivní na portálu pro vývojáře.

![Potvrdit publikování][api-management-publish-template-confirm]

Pokud chcete publikovat všechny aktuálně nepublikované verze šablon, klikněte v seznamu šablon na **Publikovat.** Nepublikované šablony jsou označeny hvězdičkou za názvem šablony. V tomto příkladu jsou publikovány **seznam produktů** a šablony **produktu.**

![Publikování šablon][api-management-publish-templates]

Potvrďte, že chcete **publikovat vlastní nastavení.**

![Potvrdit publikování][api-management-publish-customizations]

Nově publikované šablony jsou účinné okamžitě na portálu pro vývojáře.

## <a name="to-revert-a-template-to-the-previous-version"></a>Vrácení šablony k předchozí verzi
Chcete-li vrátit šablonu k předchozí publikované verzi, klepněte na tlačítko Vrátit v editoru šablon.

![Vrátit šablonu][api-management-revert-template]

Pro potvrzení klikněte na tlačítko **Ano**.

![Confirm][api-management-revert-template-confirm]

Dříve publikovaná verze šablony je aktivní na portálu pro vývojáře po dokončení operace vrácení.

## <a name="to-restore-a-template-to-the-default-version"></a>Obnovení výchozí verze šablony
Obnovení šablon na výchozí verzi je dvoustupňový proces. Nejprve musí být obnoveny šablony a pak musí být publikovány obnovené verze.

Chcete-li obnovit jednu šablonu do výchozí verze, klepněte na tlačítko Obnovit v editoru šablon.

![Vrátit šablonu][api-management-reset-template]

Pro potvrzení klikněte na tlačítko **Ano**.

![Confirm][api-management-reset-template-confirm]

Pokud chcete obnovit všechny šablony do výchozích verzí, klikněte v seznamu šablon na **Obnovit výchozí šablony.**

![Obnovení šablon][api-management-restore-templates]

Obnovené šablony pak musí být publikovány jednotlivě nebo všechny najednou podle kroků v [části Publikování šablony](#to-publish-a-template).

## <a name="next-steps"></a>Další kroky
Referenční informace pro šablony portálu pro vývojáře, prostředky řetězce, ikony a ovládací prvky stránky naleznete [v tématu odkaz na šablony portálu pro vývojáře pro správu rozhraní API](api-management-developer-portal-templates-reference.md).

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
