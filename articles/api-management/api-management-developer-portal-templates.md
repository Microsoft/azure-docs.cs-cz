---
title: Přizpůsobení portálu pro správu rozhraní API pro vývojáře pomocí šablon – Azure | Dokumentace Microsoftu
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
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 00d5e3df78e85d19a519786dad1a1b176ad7fa08
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55733055"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Přizpůsobení portálu pro vývojáře Azure API Management pomocí šablon

Existují tři základní způsoby, kterými jde přizpůsobit portál pro vývojáře ve službě Azure API Management:

* [Úprava obsahu statických stránek a elementů rozložení stránek][modify-content-layout]
* [Aktualizace stylů použitých pro elementy stránek napříč portálem pro vývojáře][customize-styles]
* [Úprava šablony použité pro stránky vytvořený portál][portal-templates] (popsáno v této příručce)

Šablony umožňují přizpůsobit obsah stránek portálu pro vývojáře systémem generovaných (například dokumentace rozhraní API, produkty, ověřování uživatelů, atd.). Pomocí [DotLiquid](http://dotliquidmarkup.org/) syntaxi a zadané sadě prostředků lokalizované řetězce, ikon a ovládací prvky stránky, máte velkou flexibilitu konfigurovat obsahu stránek podle svých potřeb.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>Přehled šablon portálu pro vývojáře

Úpravy šablon se provádí z **portál pro vývojáře** když jste přihlášení jako správce. Tam dostat nejprve otevřít na webu Azure Portal a klikněte na tlačítko **portál pro vývojáře** z panelu nástrojů služby vaší instance služby API Management.

Pro přístup k šablon portálu pro vývojáře, klikněte na ikonu přizpůsobení na levé straně zobrazí nabídka vlastní nastavení a klikněte na tlačítko **šablony**.

![Šablon portálu pro vývojáře][api-management-customize-menu]

Seznam šablon se zobrazí několik kategorií šablon vztahující se na různých stránkách portálu pro vývojáře. Každá šablona je odlišná, ale postup je upravit a publikovat změny jsou stejné. Chcete-li upravit šablonu, klikněte na název šablony.

![Šablon portálu pro vývojáře][api-management-templates-menu]

Klepnutím na šablonu přejdete na stránku portálu pro vývojáře, který lze přizpůsobit pomocí této šablony. V tomto příkladu **seznam produktů** se šablony zobrazí. **Seznam produktů** šablony určuje oblasti obrazovky označená červeným rámečkem.

![Šablona seznamu produktů][api-management-developer-portal-templates-overview]

Některé šablony, jako jsou **profilu uživatele** šablony, přizpůsobení různých součástí stejné stránky.

![Šablony profilu uživatele][api-management-user-profile-templates]

Editor pro každé šablony portálu pro vývojáře má dvě části zobrazí v dolní části stránky. Zobrazí na levé straně podokna úprav šablony a na pravé straně datový model pro šablonu.

Úpravy podokně šablony obsahuje kód, který řídí vzhled a chování odpovídající stránku na portálu pro vývojáře. Značky v šabloně používají [DotLiquid](http://dotliquidmarkup.org/) syntaxe. Je jeden oblíbený editor pro DotLiquid [DotLiquid pro profesionální návrháře využívající](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Všechny změny provedené při úpravách šablony se zobrazují v reálném čase v prohlížeči, ale nejsou viditelné pro vaše zákazníky dokud [Uložit](#to-save-a-template) a [publikovat](#to-publish-a-template) šablony.

![Kód šablony][api-management-template]

**Data šablony** podokně poskytuje Průvodce datovým modelem entity, které jsou k dispozici pro použití v konkrétní šablonu. Tato příručka poskytuje tím, že zobrazuje dynamická data, která se aktuálně zobrazují na portálu pro vývojáře. Podokna šablony můžete rozbalit kliknutím na tlačítko v pravém horním rohu obdélníku **data šablony** podokně.

![Šablona datového modelu][api-management-template-data]

V předchozím příkladu jsou dva produkty, které se zobrazí na portálu pro vývojáře, které byly načteny z data zobrazená v **data šablony** podokně, jak je znázorněno v následujícím příkladu:

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

Značky **seznam produktů** šablony zpracovává data k poskytnutí požadovaného výstupu tak provede iterace přes kolekce produktů můžete zobrazit informace a odkaz na jednotlivé produkty. Poznámka: `<search-control>` a `<page-control>` elementy v kódu. Tyto ovládací prvek zobrazení hledání a stránkování ovládacích prvků na stránce. `ProductsStrings|PageTitleProducts` obsahuje odkaz na lokalizované řetězce `h2` text záhlaví stránky. Seznam prostředků řetězců, ovládací prvky stránky a ikony, které jsou k dispozici pro použití v šablon portálu pro vývojáře najdete v tématu [šablon portálu pro vývojáře rozhraní API managementu](api-management-developer-portal-templates-reference.md).

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

## <a name="to-save-a-template"></a>Chcete-li uložit šablonu
Uložte šablonu, klikněte na Uložit v editoru šablon.

![Uložení šablony][api-management-save-template]

Uložené změny nejsou v portálu pro vývojáře, dokud se zveřejní.

## <a name="to-publish-a-template"></a>K publikování šablony
Uložené šablony mohou být publikovány buď jednotlivě, nebo všechno dohromady. Chcete-li publikovat jednotlivé šablony, publikování kliknutím v editoru šablon.

![Publikování šablony][api-management-publish-template]

Klikněte na tlačítko **Ano** potvrďte a ujistěte se, šablony na portálu pro vývojáře.

![Potvrzení publikování][api-management-publish-template-confirm]

Chcete-li publikovat všechny aktuálně nepublikovaných šablony verze, klikněte na tlačítko **publikovat** v seznamu šablon. Publikování šablon jsou označeny hvězdičkou po názvu šablony. V tomto příkladu **seznam produktů** a **produktu** publikují se šablony.

![Publikování šablony][api-management-publish-templates]

Klikněte na tlačítko **publikovat vlastní nastavení** potvrďte.

![Potvrzení publikování][api-management-publish-customizations]

Přehled nově publikovaných šablon jsou efektivní okamžitě na portálu pro vývojáře.

## <a name="to-revert-a-template-to-the-previous-version"></a>Obnovit šablonu a předchozí verze
Obnovit šablonu na předchozím publikovanou verzi, klikněte na tlačítko Obnovit v editoru šablon.

![Obnovit šablony][api-management-revert-template]

Pro potvrzení klikněte na tlačítko **Ano**.

![Potvrdit][api-management-revert-template-confirm]

Po dokončení operace vrácení dřív publikovaná verze šablony je v provozu na portálu pro vývojáře.

## <a name="to-restore-a-template-to-the-default-version"></a>Chcete-li obnovit výchozí verze šablony
Obnovení na jejich výchozí verze šablony je dvoustupňový proces. Nejdřív je potřeba obnovit šablony a pak musí být obnovená verze publikovány.

Chcete-li obnovit jednu šablonu, která výchozí verze, kliknutím na tlačítko Obnovit v editoru šablon.

![Obnovit šablony][api-management-reset-template]

Pro potvrzení klikněte na tlačítko **Ano**.

![Potvrdit][api-management-reset-template-confirm]

Pokud chcete obnovit všechny šablony na jejich výchozí verze, klikněte na tlačítko **obnovit výchozí šablony** v seznamu šablon.

![Obnovit šablony][api-management-restore-templates]

Obnovené šablony musí pak lze publikovat samostatně nebo celou najednou pomocí následujících kroků v [publikovat šablony](#to-publish-a-template).

## <a name="next-steps"></a>Další postup
Referenční informace pro šablon portálu pro vývojáře, řetězcové prostředky, ikony a ovládací prvky stránky najdete v tématu [šablon portálu pro vývojáře rozhraní API managementu](api-management-developer-portal-templates-reference.md).

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
