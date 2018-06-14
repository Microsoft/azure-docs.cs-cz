---
title: Přizpůsobení portálu pro vývojáře API Management pomocí šablon-Azure | Microsoft Docs
description: Naučte se přizpůsobovat portál pro vývojáře Azure API Management pomocí šablon.
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
ms.openlocfilehash: 2bdb8c30ffa630f85d666f3a16ce2b9dcdab6492
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29118220"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Postup přizpůsobení portálu pro vývojáře Azure API Management pomocí šablon

Existují tři základní způsoby, kterými jde přizpůsobit portál pro vývojáře ve službě Azure API Management:

* [Úprava obsahu statických stránek a elementů rozložení stránek][modify-content-layout]
* [Aktualizace stylů použitých pro elementy stránek napříč portálem pro vývojáře][customize-styles]
* [Úprava šablony použité pro stránky vytvořený portál][portal-templates] (popsáno v této příručce)

Šablony slouží k přizpůsobení obsah stránky portálu vývojáře generované systémem (například dokumentace rozhraní API, produktů, ověřování uživatelů, atd.). Pomocí [DotLiquid](http://dotliquidmarkup.org/) syntaxe a zadané sadu prostředků lokalizovaný řetězec, ikony a ovládací prvky stránky, máte flexibilitu při konfiguraci obsahu stránek, podle potřeby.

## <a name="developer-portal-templates-overview"></a>Přehled šablon portálu vývojáře
Úprava šablony se provádí z **portál pro vývojáře** při právě přihlášeni jako správce. Tam dostat nejdřív otevřete portál Azure a klikněte na tlačítko **portál pro vývojáře** na panelu nástrojů služby instanci služby API Management.

Pro přístup k portálu šablony vývojáře, klikněte na ikonu přizpůsobit na levé straně zobrazení nabídky přizpůsobení, a klikněte na **šablony**.

![Šablony na portálu vývojáře][api-management-customize-menu]

Seznam šablon se zobrazí několik kategorií šablon pokrývajících na různých stránkách v portálu pro vývojáře. Každá šablona se liší, ale postup upravit je a publikovat změny jsou stejné. Chcete-li upravit šablonu, klikněte na název šablony.

![Šablony na portálu vývojáře][api-management-templates-menu]

Klepnutím na šablonu přejdete na stránku portálu vývojáře, která lze přizpůsobit pomocí této šablony. V tomto příkladu **seznam produktů** se šablony zobrazí. **Seznam produktů** šablony řídí oblasti obrazovky označená červeným rámečkem. 

![Šablona seznam produktů][api-management-developer-portal-templates-overview]

Některé šablony, jako **profil uživatele** šablony, přizpůsobit různých součástí stejné stránky. 

![Šablony profilu uživatele][api-management-user-profile-templates]

Editor pro každé šablony portálu pro vývojáře má dvě části, které se zobrazí v dolní části stránky. Na levé straně zobrazí podokna úprav pro šablonu, a na pravé straně datový model pro šablonu. 

Šablona úpravy podokně obsahuje kód, který řídí vzhled a chování odpovídající stránky v portálu pro vývojáře. Značky v šabloně používají [DotLiquid](http://dotliquidmarkup.org/) syntaxe. Je jeden oblíbených editor pro DotLiquid [DotLiquid pro návrháře](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Všechny změny provedené v šabloně úpravy se zobrazují v reálném čase v prohlížeči, ale nejsou, uvidí vaši zákazníci dokud [Uložit](#to-save-a-template) a [publikování](#to-publish-a-template) šablony.

![Kód šablony][api-management-template]

**Data šablony** podokně poskytuje návod, jak do datového modelu entity, které jsou k dispozici pro použití v konkrétní šablonu. Tato příručka poskytuje zobrazením za provozu data, která jsou aktuálně zobrazeny v portálu pro vývojáře. Podokna šablony můžete rozbalit kliknutím obdélníku v pravém horním rohu **data šablony** podokně.

![Šablona datového modelu][api-management-template-data]

V předchozím příkladu jsou dva produkty zobrazí v portálu pro vývojáře, které byly získány ze údaje zobrazené na **data šablony** podokně, jak je znázorněno v následujícím příkladu:

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

Kód v **seznam produktů** šablony zpracovává data, která mají poskytnout požadovaný výstupní pomocí iterace kolekce produktů můžete zobrazit informace a odkaz na jednotlivé jednotlivé produkty. Poznámka: `<search-control>` a `<page-control>` elementy ve značkách. Tyto řízení zobrazení hledání a stránkování ovládací prvky na stránce. `ProductsStrings|PageTitleProducts`odkaz na lokalizovaný řetězec, který obsahuje `h2` text záhlaví pro stránku. Seznam řetězcové prostředky, ovládací prvky stránky a ikony, které jsou k dispozici pro použití v šablonách portálu vývojáře najdete v tématu [API managementu vývojáře portálu šablony](api-management-developer-portal-templates-reference.md).

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

## <a name="to-save-a-template"></a>Pokud chcete uložit šablonu
Chcete-li uložit šablonu, klikněte na tlačítko Uložit v editoru šablony.

![Uložení šablony][api-management-save-template]

Změny uloženy nejsou za provozu v portálu pro vývojáře, dokud nejsou publikovány.

## <a name="to-publish-a-template"></a>K publikování šablony
Uložené šablony mohou být publikovány buď jednotlivě, nebo všechny společně. K publikování jednotlivých šablony, klikněte na tlačítko Publikovat v editoru šablony.

![Publikování šablony][api-management-publish-template]

Klikněte na tlačítko **Ano** potvrďte a zajistit, že šablona za provozu na portál pro vývojáře.

![Potvrďte publikování][api-management-publish-template-confirm]

Chcete-li publikovat všechny aktuálně publikování šablony verze, klikněte na tlačítko **publikovat** v seznamu šablon. Publikování šablon jsou označeny hvězdičkou následující název šablony. V tomto příkladu **seznam produktů** a **produktu** publikování šablony.

![Publikování šablon][api-management-publish-templates]

Klikněte na tlačítko **publikovat vlastní nastavení** k potvrzení.

![Potvrďte publikování][api-management-publish-customizations]

Přehled nově publikovaných šablony jsou efektivní hned v portálu pro vývojáře.

## <a name="to-revert-a-template-to-the-previous-version"></a>Chcete-li obnovit šablonu pro předchozí verze
Můžete obnovit šablonu na předchozí publikovanou verzi, klikněte na tlačítko Obnovit v editoru šablony.

![Obnovit šablonu][api-management-revert-template]

Pro potvrzení klikněte na tlačítko **Ano**.

![Potvrdit][api-management-revert-template-confirm]

Po dokončení této operace je za provozu v portálu pro vývojáře dřív publikovaná verze šablony.

## <a name="to-restore-a-template-to-the-default-version"></a>Chcete-li obnovit šablonu výchozí verze
Obnovení šablony na jejich výchozí verze je dvoustupňový proces. Nejprve je nutné obnovit šablony, a pak musí být publikován obnovené verze.

Chcete-li obnovit jednu šablonu, která má výchozí verze kliknutím na tlačítko Obnovit v editoru šablony.

![Obnovit šablonu][api-management-reset-template]

Pro potvrzení klikněte na tlačítko **Ano**.

![Potvrdit][api-management-reset-template-confirm]

Chcete-li obnovit všechny šablony na jejich výchozí verze, klikněte na tlačítko **obnovit výchozí šablony** na seznam šablon.

![Obnovit šablony][api-management-restore-templates]

Obnovená šablony musí pak publikovat samostatně nebo všechny najednou podle kroků v [k publikování šablony](#to-publish-a-template).

## <a name="next-steps"></a>Další postup
Referenční informace pro vývojáře portálu šablony, řetězcové prostředky, ikony a ovládací prvky stránky najdete v tématu [API managementu vývojáře portálu šablony](api-management-developer-portal-templates-reference.md).

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







