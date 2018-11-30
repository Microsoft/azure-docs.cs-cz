---
title: Šablony aplikací ve službě Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak přizpůsobit obsah stránek aplikací na portálu pro vývojáře ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: f3122c4d-e10e-4cdf-977b-36e8f4133fc8
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 0eead6201f737448b4de76476802518a506375da
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446230"
---
# <a name="application-templates-in-azure-api-management"></a>Šablony aplikací ve službě Azure API Management
Azure API Management poskytuje schopnost přizpůsobit obsah stránek portálu pro vývojáře pomocí sady šablon, které konfigurace jejich obsahu. Pomocí [DotLiquid](http://dotliquidmarkup.org/) syntaxe a do editoru podle vašeho výběru, jako například [DotLiquid pro profesionální návrháře využívající](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), nebo umístění zadaná sada [prostředky řetězce, které](api-management-template-resources.md#strings), [glyfů prostředky](api-management-template-resources.md#glyphs), a [stránce ovládací prvky](api-management-page-controls.md), máte velkou flexibilitu konfigurovat obsahu stránek podle svých potřeb, pomocí těchto šablon.  
  
 Šablony v této části umožňují přizpůsobit obsah stránek aplikací na portálu pro vývojáře.  
  
-   [Seznam aplikací](#ProductList)  
  
-   [Aplikace](#Application)  
  
> [!NOTE]
>  Ukázka výchozí šablony jsou zahrnuty v následující dokumentaci, ale můžou se změnit kvůli průběžně vylepšujeme. Živé výchozí šablony můžete zobrazit na portálu pro vývojáře tak, že přejdete na jednotlivé požadované šablony. Další informace o práci se šablonami najdete v tématu [přizpůsobení portálu pro správu rozhraní API pro vývojáře pomocí šablon](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="ProductList"></a> Seznam aplikací  
 **Seznam aplikací** šablony vám umožní přizpůsobit těla stránky seznamu aplikací na portálu pro vývojáře.  
  
 ![Šablon portálu pro vývojáře stránku v seznamu aplikací](./media/api-management-application-templates/APIM-Application-List-Page-Developer-Portal-Templates.png "šablony portálu seznamu vývojář aplikací pro služby APIM")  
  
### <a name="default-template"></a>Výchozí šablona  
  
```xml  
<div class="row">  
    <div class="col-md-9">  
        <h2>{% localized "AppStrings|WebApplicationsHeader" %}</h2>  
    </div>  
</div>  
<div class="row">  
    <div class="col-md-12">  
    {% if applications.size > 0 %}  
        <ul class="list-unstyled">  
        {% for app in applications %}  
            <li>  
            {% if app.application.icon.url != "" %}  
                <aside>  
                    <a href="/applications/details/{{app.application.id}}"><img src="{{app.application.icon.url}}" alt="App Icon" /></a>  
                </aside>  
            {% endif %}  
                <h3><a href="/applications/details/{{app.application.id}}">{{app.application.title}}</a></h3>  
                {{app.application.description}}  
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
  
### <a name="data-model"></a>Datový model  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|Stránkování|[Stránkování](api-management-template-data-model-reference.md#Paging) entity.|Informace o stránkování pro kolekci aplikací.|  
|Aplikace|Kolekce [aplikace](api-management-template-data-model-reference.md#Application) entity.|Aplikace, které jsou viditelné pro aktuálního uživatele.|  
|CategoryName|řetězec|Kategorie aplikace.|  
  
### <a name="sample-template-data"></a>Ukázková data šablony  
  
```json  
{  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 1,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "Applications": [  
        {  
            "Application": {  
                "Id": "5702b96fb16653124c8f9ba8",  
                "Title": "Contoso Calculator",  
                "Description": "A simple online calculator.",  
                "Url": null,  
                "Version": null,  
                "Requirements": "Free application with no requirements.",  
                "State": 2,  
                "RegistrationDate": "2016-04-04T18:59:00",  
                "CategoryId": 5,  
                "DeveloperId": "5702b5b0b16653124c8f9ba4",  
                "Attachments": [  
                    {  
                        "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
                        "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
                        "Type": "Icon",  
                        "ContentType": "image/png"  
                    },  
                    {  
                        "UniqueId": "2b4fa5dd-00ff-4a8f-b1b7-51e715849ede",  
                        "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/2b4fa5dd-00ff-4a8f-b1b7-51e715849ede.png",  
                        "Type": "Screenshot",  
                        "ContentType": "image/png"  
                    }  
                ],  
                "Icon": {  
                    "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
                    "Url": "https://apimgmtst65gdjvjrgdbfhr4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
                    "Type": "Icon",  
                    "ContentType": "image/png"  
                }  
            },  
            "CategoryName": "Finance"  
        }  
    ]  
}  
```  
  
##  <a name="Application"></a> Aplikace  
 **Aplikace** šablony vám umožní přizpůsobit text na stránce aplikace na portálu pro vývojáře.  
  
 ![Šablon portálu pro vývojáře stránky aplikace](./media/api-management-application-templates/APIM-Application-Page-Developer-Portal-Templates.png "šablon portálu pro vývojáře stránky v aplikaci služby APIM")  
  
### <a name="default-template"></a>Výchozí šablona  
  
```xml  
<h2>{{title}}</h2>  
{% if icon.url != "" %}  
<aside class="applications_aside">  
  <div class="image-placeholder">  
    <img src="{{icon.url}}" alt="Application Icon" />  
  </div>  
</aside>  
{% endif %}  
  
<article>  
  {% if url != "" %}  
    <a target="_blank" href="{{url}}">{{url}}</a>  
  {% endif %}  
  
  <p>{{description}}</p>  
  
  {% if requirements != null %}  
  <h3>{% localized "AppDetailsStrings|WebApplicationsRequirementsHeader" %}</h3>  
  <p>{{requirements}}</p>  
  {% endif %}  
  
  {% if attachments.size > 0 %}  
  <h3>{% localized "AppDetailsStrings|WebApplicationsScreenshotsHeader" %}</h3>  
    {% for screenshot in attachments %}  
      {% if screenshot.type != "Icon" %}  
      <a href="{{screenshot.url}}" data-lightbox="example-set">  
          <img src="/Developer/Applications/Thumbnail?url={{screenshot.url}}" alt='{% localized "AppDetailsStrings|WebApplicationsScreenshotAlt" %}' />  
      </a>  
      {% endif %}  
    {% endfor %}  
  {% endif %}  
</article>  
  
```  
  
### <a name="controls"></a>Ovládací prvky  
 `Application` Šablona neumožňuje použití žádné [stránce ovládací prvky](api-management-page-controls.md).  
  
### <a name="data-model"></a>Datový model  
 [Aplikace](api-management-template-data-model-reference.md#Application) entity.  
  
### <a name="sample-template-data"></a>Ukázková data šablony  
  
```json  
{  
    "Id": "5702b96fb16653124c8f9ba8",  
    "Title": "Contoso Calculator",  
    "Description": "A simple online calculator.",  
    "Url": null,  
    "Version": null,  
    "Requirements": "Free application with no requirements.",  
    "State": 2,  
    "RegistrationDate": "2016-04-04T18:59:00",  
    "CategoryId": 5,  
    "DeveloperId": "5702b5b0b16653124c8f9ba4",  
    "Attachments": [  
        {  
            "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
            "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
            "Type": "Icon",  
            "ContentType": "image/png"  
        },  
        {  
            "UniqueId": "2b4fa5dd-00ff-4a8f-b1b7-51e715849ede",  
            "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/2b4fa5dd-00ff-4a8f-b1b7-51e715849ede.png",  
            "Type": "Screenshot",  
            "ContentType": "image/png"  
        }  
    ],  
    "Icon": {  
        "UniqueId": "a58af001-e6c3-45fd-8bc9-c60a1875c3f6",  
        "Url": "https://apimgmtst3aybshdqqcqrle4.blob.core.windows.net/content/applications/a58af001-e6c3-45fd-8bc9-c60a1875c3f6.png",  
        "Type": "Icon",  
        "ContentType": "image/png"  
    }  
}  
```

## <a name="next-steps"></a>Další postup
Další informace o práci se šablonami najdete v tématu [přizpůsobení portálu pro správu rozhraní API pro vývojáře pomocí šablon](api-management-developer-portal-templates.md).