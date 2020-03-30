---
title: Šablony produktů ve správě rozhraní Azure API | Dokumenty společnosti Microsoft
description: Zjistěte, jak přizpůsobit obsah stránek produktů na portálu pro vývojáře Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 393563427e936e07315cd44b78cb793d4292b352
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243923"
---
# <a name="product-templates-in-azure-api-management"></a>Šablony produktů ve správě rozhraní Azure API

Azure API Management poskytuje možnost přizpůsobit obsah stránek portálu pro vývojáře pomocí sady šablon, které konfigurují jejich obsah. Pomocí [DotLiquid](http://dotliquidmarkup.org/) syntaxe a editor podle vašeho výběru, jako je [například DotLiquid pro návrháře](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)a za předpokladu, sadu lokalizovaných [prostředků řetězce](api-management-template-resources.md#strings), glyph [prostředky](api-management-template-resources.md#glyphs)a page [ovládací prvky](api-management-page-controls.md), máte velkou flexibilitu ke konfiguraci obsahu stránek, jak uznáte za vhodné pomocí těchto šablon.  
  
 Šablony v této části umožňují přizpůsobit obsah stránek produktů na portálu pro vývojáře.  
  
-   [Seznam produktů](#ProductList)  
  
-   [Produktu](#Product)  
  
> [!NOTE]
>  Ukázkové výchozí šablony jsou zahrnuty v následující dokumentaci, ale mohou se měnit z důvodu neustálého zlepšování. Výchozí živé šablony můžete zobrazit na portálu pro vývojáře tak, že přejdete na požadované jednotlivé šablony. Další informace o práci se šablonami najdete [v tématu Jak přizpůsobit portál pro vývojáře pro správu rozhraní API pomocí šablon](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="product-list"></a><a name="ProductList"></a>Seznam produktů  
 Šablona **Seznam produktů** umožňuje přizpůsobit text stránky seznamu produktů na portálu pro vývojáře.  
  
 ![Seznam produktů](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
### <a name="default-template"></a>Výchozí šablona  
  
```xml  
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
  
### <a name="controls"></a>Ovládací prvky  
 Šablona `Product list` může používat následující [ovládací prvky stránky](api-management-page-controls.md).  
  
-   [stránkovací řízení](api-management-page-controls.md#paging-control)  
  
-   [řízení vyhledávání](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Datový model  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|Stránkování|[Stránkovací entita.](api-management-template-data-model-reference.md#Paging)|Stránkovací informace pro kolekci produktů.|  
|Filtrování|[Entita filtrování.](api-management-template-data-model-reference.md#Filtering)|Informace o filtrování pro stránku seznamu produktů.|  
|Produkty|Kolekce entit [produktu.](api-management-template-data-model-reference.md#Product)|Produkty viditelné pro aktuálního uživatele.|  
  
### <a name="sample-template-data"></a>Ukázková data šablony  
  
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
            "Id": "56f9445ffaf7560049060001",  
            "Title": "Starter",  
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "Terms": "",  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        },  
        {  
            "Id": "56f9445ffaf7560049060002",  
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
  
##  <a name="product"></a><a name="Product"></a>Produktu  
 Šablona **Produkt** umožňuje přizpůsobit text stránky produktu na portálu pro vývojáře.  
  
 ![Stránka produktu portálu pro vývojáře](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
### <a name="default-template"></a>Výchozí šablona  
  
```xml  
<h2>{{Product.Title}}</h2>  
<p>{{Product.Description}}</p>  
  
{% assign replaceString0 = '{0}' %}  
  
{% if Limits and Limits.size > 0 %}  
<h3>{% localized "ProductDetailsStrings|WebProductsUsageLimitsHeader"%}</h3>  
<ul>  
  {% for limit in Limits %}  
  <li>{{limit.DisplayName}}</li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if apis.size > 0 %}  
<p>  
  <b>  
    {% if apis.size == 1 %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockSingleApisCount" %}{% endcapture %}  
    {% else %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockMultipleApisCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture apisCount %}{{apis.size}}{% endcapture %}  
    {{ apisCountText | replace : replaceString0, apisCount }}  
  </b>  
</p>  
  
<ul>  
  {% for api in Apis %}  
  <li>  
    <a href="/docs/services/{{api.Id}}">{{api.Name}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if subscriptions.size > 0 %}  
<p>  
  <b>  
    {% if subscriptions.size == 1 %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockSingleSubscriptionsCount" %}{% endcapture %}  
    {% else %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockMultipleSubscriptionsCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture subscriptionsCount %}{{subscriptions.size}}{% endcapture %}  
    {{ subscriptionsCountText | replace : replaceString0, subscriptionsCount }}  
  </b>  
</p>  
  
<ul>  
  {% for subscription in subscriptions %}  
  <li>  
    <a href="/developer#{{subscription.Id}}">{{subscription.DisplayName}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
{% if CannotAddBecauseSubscriptionNumberLimitReached %}  
<b>{% localized "ProductDetailsStrings|TextblockSubscriptionLimitReached" %}</b>  
{% elsif CannotAddBecauseMultipleSubscriptionsNotAllowed == false %}  
<subscribe-button></subscribe-button>  
{% endif %}  
```  
  
### <a name="controls"></a>Ovládací prvky  
 Šablona `Product list` může používat následující [ovládací prvky stránky](api-management-page-controls.md).  
  
-   [tlačítko pro přihlášení k odběru](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Datový model  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|Produkt|[Produktu](api-management-template-data-model-reference.md#Product)|Zadaný produkt.|  
|IsDeveloperSubscribed|Boolean|Určuje, zda je aktuální uživatel přihlášen k odběru tohoto produktu.|  
|SubscriptionState|číslo|Stav předplatného. Možné stavy jsou:<br /><br /> -   `0 - suspended`– předplatné je blokováno a odběratel nemůže volat žádná api produktu.<br />-   `1 - active`– předplatné je aktivní.<br />-   `2 - expired`– předplatné dosáhlo data vypršení platnosti a bylo deaktivováno.<br />-   `3 - submitted`– žádost o předplatné byla podána vývojářem, ale dosud nebyla schválena nebo zamítnuta.<br />-   `4 - rejected`– požadavek na předplatné byl zamítnut správcem.<br />-   `5 - cancelled`– vývojář nebo správce zrušil předplatné.|  
|Omezení|pole|Tato vlastnost je zastaralé a by neměly být používány.|  
|DelegatedSubscriptionEnabled|Boolean|Určuje, zda je pro toto předplatné povoleno [delegování.](https://azure.microsoft.com/documentation/articles/api-management-howto-setup-delegation/)|  
|Adresa DelegatedSubscriptionUrl|řetězec|Pokud je delegování povoleno, adresa URL delegovaného předplatného.|  
|Jedohodnuto|Boolean|Pokud má produkt podmínky, zda aktuální uživatel souhlasil s podmínkami.|  
|Předplatná|Kolekce [souhrnných](api-management-template-data-model-reference.md#SubscriptionSummary) entit předplatného.|Předplatná produktu.|  
|Rozhraní api|Kolekce entit [rozhraní API.](api-management-template-data-model-reference.md#API)|API v tomto produktu.|  
|Hodnota CannotAddBecauseSubscriptionNumberLimitReached|Boolean|Určuje, zda se aktuální uživatel může přihlásit k odběru tohoto produktu s ohledem na limit předplatného.|  
|NelzepřidatVzhledk: MultipleSubscriptionsNotAllowed|Boolean|Určuje, zda se současný uživatel může přihlásit k odběru tohoto produktu s ohledem na povolení více předplatných či nikoli.|  
  
### <a name="sample-template-data"></a>Ukázková data šablony  
  
```json  
{  
    "Product": {  
        "Id": "56f9445ffaf7560049060001",  
        "Title": "Starter",  
        "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
        "Terms": "",  
        "ProductState": 1,  
        "AllowMultipleSubscriptions": false,  
        "MultipleSubscriptionsCount": 1  
    },  
    "IsDeveloperSubscribed": true,  
    "SubscriptionState": 1,  
    "Limits": [],  
    "DelegatedSubscriptionEnabled": false,  
    "DelegatedSubscriptionUrl": null,  
    "IsAgreed": false,  
    "Subscriptions": [  
        {  
            "Id": "56f9445ffaf7560049070001",  
            "DisplayName": "Starter  (default)"  
        }  
    ],  
    "Apis": [  
        {  
            "id": "56f9445ffaf7560049040001",  
            "name": "Echo API",  
            "description": null,  
            "serviceUrl": "http://echoapi.cloudapp.net/api",  
            "path": "echo",  
            "protocols": [  
                2  
            ],  
            "authenticationSettings": null,  
            "subscriptionKeyParameterNames": null  
        }  
    ],  
    "CannotAddBecauseSubscriptionNumberLimitReached": false,  
    "CannotAddBecauseMultipleSubscriptionsNotAllowed": true  
}  
```

## <a name="next-steps"></a>Další kroky
Další informace o práci se šablonami najdete [v tématu Jak přizpůsobit portál pro vývojáře pro správu rozhraní API pomocí šablon](api-management-developer-portal-templates.md).
