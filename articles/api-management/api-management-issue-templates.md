---
title: Vydání šablony ve službě Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak přizpůsobit obsah stránek problém na portálu pro vývojáře ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 47da4bb2-426e-4e53-8fa7-214ee2e3ab37
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: f099c27c55b817d6d9217a614ee66bf1d414a4dd
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52446363"
---
# <a name="issue-templates-in-azure-api-management"></a>Problém šablon ve službě Azure API Management
Azure API Management poskytuje schopnost přizpůsobit obsah stránek portálu pro vývojáře pomocí sady šablon, které konfigurace jejich obsahu. Pomocí [DotLiquid](http://dotliquidmarkup.org/) syntaxe a do editoru podle vašeho výběru, jako například [DotLiquid pro profesionální návrháře využívající](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), nebo umístění zadaná sada [prostředky řetězce, které](api-management-template-resources.md#strings), [glyfů prostředky](api-management-template-resources.md#glyphs), a [stránce ovládací prvky](api-management-page-controls.md), máte velkou flexibilitu konfigurovat obsahu stránek podle svých potřeb, pomocí těchto šablon.  
  
 Šablony v této části umožňují přizpůsobit obsah stránek problém na portálu pro vývojáře.  
  
-   [Seznam problémů](#IssueList)  
  
> [!NOTE]
>  Ukázka výchozí šablony jsou zahrnuty v následující dokumentaci, ale můžou se změnit kvůli průběžně vylepšujeme. Živé výchozí šablony můžete zobrazit na portálu pro vývojáře tak, že přejdete na jednotlivé požadované šablony. Další informace o práci se šablonami najdete v tématu [přizpůsobení portálu pro správu rozhraní API pro vývojáře pomocí šablon](api-management-developer-portal-templates.md).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="IssueList"></a> Seznam problémů  
 **Seznam problémů** šablony vám umožní přizpůsobit text na stránce seznamu problém na portálu pro vývojáře.  
  
 ![Vydat portál pro vývojáře seznamu](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "portál pro vývojáře seznamu problém APIM")  
  
### <a name="default-template"></a>Výchozí šablona  
  
```xml  
<div class="row">  
  <div class="col-md-9">  
    <h2>{% localized "IssuesStrings|WebIssuesIndexTitle" %}</h2>  
  </div>  
</div>  
<div class="row">  
  <div class="col-md-12">  
    {% if issues.size > 0 %}  
    <ul class="list-unstyled">  
      {% capture reportedBy %}{% localized "IssuesStrings|WebIssuesStatusReportedBy" %}{% endcapture %}  
      {% assign replaceString0 = '{0}' %}  
      {% assign replaceString1 = '{1}' %}  
      {% for issue in issues %}  
      <li>  
        <h3>  
          <a href="/issues/{{issue.id}}">{{issue.title}}</a>  
        </h3>  
        <p>{{issue.description}}</p>  
        <em>  
          {% capture state %}{{issue.issueState}}{% endcapture %}  
          {% capture devName %}{{issue.subscriptionDeveloperName}}{% endcapture %}  
          {% capture str1 %}{{ reportedBy | replace : replaceString0, state }}{% endcapture %}  
          {{ str1 | replace : replaceString1, devName }}  
          <span class="UtcDateElement">{{ issue.reportedOn | date: "r" }}</span>  
        </em>  
      </li>  
      {% endfor %}  
    </ul>  
    <paging-control></paging-control>  
    {% else %}  
    {% localized "CommonResources|NoItemsToDisplay" %}  
    {% endif %}  
    {% if canReportIssue %}  
    <a class="btn btn-primary" id="createIssue" href="/Issues/Create">{% localized "IssuesStrings|WebIssuesReportIssueButton" %}</a>  
    {% elsif isAuthenticated %}  
    <hr />  
    <p>{% localized "IssuesStrings|WebIssuesNoActiveSubscriptions" %}</p>  
    {% else %}  
    <hr />  
    <p>  
      {% capture signIntext %}{% localized "IssuesStrings|WebIssuesNotSignin" %}{% endcapture %}  
      {% capture link %}<a href="/signin">{% localized "IssuesStrings|WebIssuesSignIn" %}</a>{% endcapture %}  
      {{ signIntext | replace : replaceString0, link }}  
    </p>  
    {% endif %}  
  </div>  
</div>  
```  
  
### <a name="controls"></a>Ovládací prvky  
 `Issue list` Šablona může používat následující [stránce ovládací prvky](api-management-page-controls.md).  
  
-   [ovládací prvek stránkování](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Datový model  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|Problémy|Kolekce [problém](api-management-template-data-model-reference.md#Issue) entity.|Problémy, které jsou viditelné pro aktuálního uživatele.|  
|Stránkování|[Stránkování](api-management-template-data-model-reference.md#Paging) entity.|Informace o stránkování pro kolekci aplikací.|  
|Ověření identity|Boolean|Určuje, zda má aktuální uživatel je přihlášený ke portálu pro vývojáře.|  
|CanReportIssues|Boolean|Určuje, zda má aktuální uživatel oprávnění k souboru k problému.|  
|Search|řetězec|Tato vlastnost je zastaralý a neměl by se používat.|  
  
### <a name="sample-template-data"></a>Ukázková data šablony  
  
```json  
{  
    "Issues": [  
        {  
            "Id": "5702b68bb16653124c8f9ba7",  
            "ApiId": "570275f1b16653124c8f9ba3",  
            "Title": "I couldn't figure out how to connect my application to the API",  
            "Description": "I'm having trouble connecting my application to the backend API.",  
            "SubscriptionDeveloperName": "Clayton",  
            "IssueState": "Proposed",  
            "ReportedOn": "2016-04-04T18:46:35.64",  
            "Comments": null,  
            "Attachments": null,  
            "Services": null  
        }  
    ],  
    "Paging": {  
        "Page": 1,  
        "PageSize": 10,  
        "TotalItemCount": 1,  
        "ShowAll": false,  
        "PageCount": 1  
    },  
    "IsAuthenticated": true,  
    "CanReportIssue": true,  
    "Search": null  
}  
```

## <a name="next-steps"></a>Další postup
Další informace o práci se šablonami najdete v tématu [přizpůsobení portálu pro správu rozhraní API pro vývojáře pomocí šablon](api-management-developer-portal-templates.md).