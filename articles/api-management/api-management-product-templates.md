---
title: Šablony produktu ve službě Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak přizpůsobit obsah stránky produktu na portálu pro vývojáře Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 14090e21fb7c6ca07fe63220ffd1d44d483ac869
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52443623"
---
# <a name="product-templates-in-azure-api-management"></a>Šablony produktu ve službě Azure API Management

Azure API Management poskytuje schopnost přizpůsobit obsah stránek portálu pro vývojáře pomocí sady šablon, které konfigurace jejich obsahu. Pomocí [DotLiquid](http://dotliquidmarkup.org/) syntaxe a do editoru podle vašeho výběru, jako například [DotLiquid pro profesionální návrháře využívající](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), nebo umístění zadaná sada [prostředky řetězce, které](api-management-template-resources.md#strings), [glyfů prostředky](api-management-template-resources.md#glyphs), a [stránce ovládací prvky](api-management-page-controls.md), máte velkou flexibilitu konfigurovat obsahu stránek podle svých potřeb, pomocí těchto šablon.  
  
 Šablony v této části umožňují přizpůsobit obsah stránky produktu na portálu pro vývojáře.  
  
-   [Seznam produktů](#ProductList)  
  
-   [Produkt](#Product)  
  
> [!NOTE]
>  Ukázka výchozí šablony jsou zahrnuty v následující dokumentaci, ale můžou se změnit kvůli průběžně vylepšujeme. Živé výchozí šablony můžete zobrazit na portálu pro vývojáře tak, že přejdete na jednotlivé požadované šablony. Další informace o práci se šablonami najdete v tématu [přizpůsobení portálu pro správu rozhraní API pro vývojáře pomocí šablon](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="ProductList"></a> Seznam produktů  
 **Seznam produktů** šablony vám umožní přizpůsobit text na stránce seznamu produktu na portálu pro vývojáře.  
  
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
 `Product list` Šablona může používat následující [stránce ovládací prvky](api-management-page-controls.md).  
  
-   [ovládací prvek stránkování](api-management-page-controls.md#paging-control)  
  
-   [ovládací prvek hledání](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Datový model  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|Stránkování|[Stránkování](api-management-template-data-model-reference.md#Paging) entity.|Informace o stránkování pro kolekci produktů.|  
|Filtrování|[Filtrování](api-management-template-data-model-reference.md#Filtering) entity.|Informace o filtrování pro stránku se seznamem produktů.|  
|Produkty|Kolekce [produktu](api-management-template-data-model-reference.md#Product) entity.|Produkty viditelné pro aktuálního uživatele.|  
  
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
  
##  <a name="Product"></a> Produkt  
 **Produktu** šablony vám umožní přizpůsobit text na stránce produktu na portálu pro vývojáře.  
  
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
 `Product list` Šablona může používat následující [stránce ovládací prvky](api-management-page-controls.md).  
  
-   [tlačítko přihlášení odběru](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Datový model  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|Produkt|[Produkt](api-management-template-data-model-reference.md#Product)|Zadaný produkt.|  
|IsDeveloperSubscribed|Boolean|Určuje, zda má aktuální uživatel je přihlášen k tomuto produktu.|  
|SubscriptionState|číslo|Stav odběru. Možné stavy jsou:<br /><br /> -   `0 - suspended` – předplatného je zablokovaná a odběrateli nelze volat jakékoli rozhraní API produktu.<br />-   `1 - active` – je předplatné aktivní.<br />-   `2 - expired` – předplatné dosáhlo datum vypršení platnosti a bylo deaktivováno.<br />-   `3 - submitted` – žádost o odběr provedl pro vývojáře, ale má ještě se schválením nebo zamítnutím.<br />-   `4 - rejected` – předplatné žádost byla zamítnuta správcem.<br />-   `5 - cancelled` – předplatné zrušil vývojáři nebo správci.|  
|Omezení|pole|Tato vlastnost je zastaralý a neměl by se používat.|  
|DelegatedSubscriptionEnabled|Boolean|Zda [delegování](https://azure.microsoft.com/documentation/articles/api-management-howto-setup-delegation/) je pro toto předplatné povolená.|  
|DelegatedSubscriptionUrl|řetězec|Pokud je povoleno delegování, adresa URL delegované předplatné.|  
|IsAgreed|Boolean|Pokud produkt má podmínky, zda má aktuální uživatel souhlasit s podmínkami.|  
|Předplatná|Kolekce [předplatné Souhrn](api-management-template-data-model-reference.md#SubscriptionSummary) entity.|Předplatná produktu.|  
|rozhraní API|Kolekce [API](api-management-template-data-model-reference.md#API) entity.|Rozhraní API v tomto produktu.|  
|CannotAddBecauseSubscriptionNumberLimitReached|Boolean|Určuje, zda má aktuální uživatel vás opravňuje k odběru tohoto produktu s ohledem na omezení předplatného.|  
|CannotAddBecauseMultipleSubscriptionsNotAllowed|Boolean|Určuje, zda má aktuální uživatel vás opravňuje k odběru tohoto produktu s ohledem na několik předplatných, nebo není povolené.|  
  
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

## <a name="next-steps"></a>Další postup
Další informace o práci se šablonami najdete v tématu [přizpůsobení portálu pro správu rozhraní API pro vývojáře pomocí šablon](api-management-developer-portal-templates.md).