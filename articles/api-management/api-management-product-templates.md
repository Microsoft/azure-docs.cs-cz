---
title: Šablony produktů v Azure API Management | Microsoft Docs
description: Naučte se přizpůsobit obsah stránek produktu na portálu pro vývojáře Azure API Management.
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
ms.openlocfilehash: 4c8cd4aa3e91c5d69c40e47683818ed8bc9be338
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86249899"
---
# <a name="product-templates-in-azure-api-management"></a>Šablony produktů v Azure API Management

Azure API Management poskytuje možnost přizpůsobení obsahu stránek portálu pro vývojáře pomocí sady šablon, které konfigurují svůj obsah. Pomocí syntaxe [DotLiquid](http://dotliquidmarkup.org/) a editoru podle vlastního výběru, jako je například [DotLiquid pro návrháře](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)a poskytnutá sada lokalizovaných [řetězcových prostředků](api-management-template-resources.md#strings), [prostředků glyfů](api-management-template-resources.md#glyphs)a [ovládacích prvků stránky](api-management-page-controls.md), máte skvělou flexibilitu pro konfiguraci obsahu stránek, jak vidíte, aby byly použity pomocí těchto šablon.  
  
 Šablony v této části umožňují přizpůsobit obsah stránek produktu na portálu pro vývojáře.  
  
-   [Seznam produktů](#ProductList)  
  
-   [Product](#Product) (Produkt)  
  
> [!NOTE]
>  Ukázkové výchozí šablony jsou uvedené v následující dokumentaci, ale můžou se změnit z důvodu průběžných vylepšení. Živé výchozí šablony můžete zobrazit na portálu pro vývojáře tak, že přejdete na požadované jednotlivé šablony. Další informace o práci se šablonami najdete v tématu [Postup přizpůsobení API Management portálu pro vývojáře pomocí šablon](./api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="product-list"></a><a name="ProductList"></a> Seznam produktů  
 Šablona **seznam produktů** vám umožní přizpůsobit tělo stránky seznam produktů na portálu pro vývojáře.  
  
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
 `Product list`Šablona může používat následující [ovládací prvky stránky](api-management-page-controls.md).  
  
-   [ovládací prvek stránkování](api-management-page-controls.md#paging-control)  
  
-   [hledání – ovládací prvek](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Datový model  
  
|Vlastnost|Typ|Description|  
|--------------|----------|-----------------|  
|Stránkování|Entita [stránkování](api-management-template-data-model-reference.md#Paging)|Informace o stránkování kolekce Products|  
|Filtrování|Entita [filtrování](api-management-template-data-model-reference.md#Filtering)|Informace o filtrování pro stránku seznam produktů.|  
|Produkty|Kolekce entit [produktu](api-management-template-data-model-reference.md#Product)|Produkty viditelné pro aktuálního uživatele.|  
  
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
  
##  <a name="product"></a><a name="Product"></a> Produktu  
 Šablona **produktu** vám umožní přizpůsobit text stránky produktu na portálu pro vývojáře.  
  
 ![Stránka produktu portál pro vývojáře](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
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
 `Product list`Šablona může používat následující [ovládací prvky stránky](api-management-page-controls.md).  
  
-   [přihlášení k odběru – tlačítko](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Datový model  
  
|Vlastnost|Typ|Description|  
|--------------|----------|-----------------|  
|Produkt|[Product](api-management-template-data-model-reference.md#Product) (Produkt)|Zadaný produkt.|  
|IsDeveloperSubscribed|boolean|Určuje, zda je aktuální uživatel přihlášen k odběru tohoto produktu.|  
|SubscriptionState|číslo|Stav předplatného. Možné stavy:<br /><br /> -   `0 - suspended` – předplatné je blokované a předplatitel nemůže volat žádná rozhraní API produktu.<br />-   `1 - active` – předplatné je aktivní.<br />-   `2 - expired` – předplatné dosáhlo data vypršení platnosti a bylo deaktivováno.<br />-   `3 - submitted` – žádost o odběr byla vytvořena vývojářem, ale ještě nebyla schválena nebo odmítnuta.<br />-   `4 - rejected` – žádost o předplatné byla zamítnuta správcem.<br />-   `5 - cancelled` – předplatné zrušila vývojář nebo správce.|  
|Omezení|array|Tato vlastnost je zastaralá a neměla by se používat.|  
|DelegatedSubscriptionEnabled|boolean|Zda je pro toto předplatné povoleno [delegování](./api-management-howto-setup-delegation.md) .|  
|DelegatedSubscriptionUrl|řetězec|Pokud je povoleno delegování, adresa URL delegovaného předplatného.|  
|Souhlasí|boolean|Pokud má produkt nějaké výrazy, bez ohledu na to, jestli aktuální uživatel souhlasil s podmínkami.|  
|Předplatná|Kolekce [souhrnných entit předplatného](api-management-template-data-model-reference.md#SubscriptionSummary)|Odběry produktu.|  
|Třídy|Kolekce entit [rozhraní API](api-management-template-data-model-reference.md#API) .|Rozhraní API v tomto produktu.|  
|CannotAddBecauseSubscriptionNumberLimitReached|boolean|Zda má aktuální uživatel nárok na přihlášení k odběru tohoto produktu s ohledem na limit předplatného.|  
|CannotAddBecauseMultipleSubscriptionsNotAllowed|boolean|Zda má aktuální uživatel nárok na přihlášení k odběru tohoto produktu s ohledem na více než více předplatných povolených.|  
  
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
Další informace o práci se šablonami najdete v tématu [Postup přizpůsobení API Management portálu pro vývojáře pomocí šablon](api-management-developer-portal-templates.md).
