---
title: Šablony problémů ve správě rozhraní Azure API | Dokumenty společnosti Microsoft
description: Zjistěte, jak přizpůsobit obsah stránek problém na portálu pro vývojáře ve správě rozhraní Azure API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 47da4bb2-426e-4e53-8fa7-214ee2e3ab37
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 1dac90053797caf66af79e458b9dbb95b682cd17
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249578"
---
# <a name="issue-templates-in-azure-api-management"></a>Šablony problémů ve správě rozhraní Azure API
Azure API Management poskytuje možnost přizpůsobit obsah stránek portálu pro vývojáře pomocí sady šablon, které konfigurují jejich obsah. Pomocí [DotLiquid](http://dotliquidmarkup.org/) syntaxe a editor podle vašeho výběru, jako je [například DotLiquid pro návrháře](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)a za předpokladu, sadu lokalizovaných [prostředků řetězce](api-management-template-resources.md#strings), glyph [prostředky](api-management-template-resources.md#glyphs)a page [ovládací prvky](api-management-page-controls.md), máte velkou flexibilitu ke konfiguraci obsahu stránek, jak uznáte za vhodné pomocí těchto šablon.  
  
 Šablony v této části umožňují přizpůsobit obsah stránek Problém na portálu pro vývojáře.  
  
-   [Seznam problémů](#IssueList)  
  
> [!NOTE]
>  Ukázkové výchozí šablony jsou zahrnuty v následující dokumentaci, ale mohou se měnit z důvodu neustálého zlepšování. Výchozí živé šablony můžete zobrazit na portálu pro vývojáře tak, že přejdete na požadované jednotlivé šablony. Další informace o práci se šablonami najdete [v tématu Jak přizpůsobit portál pro vývojáře pro správu rozhraní API pomocí šablon](api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="issue-list"></a><a name="IssueList"></a>Seznam problémů  
 Šablona **seznamu problémů** umožňuje přizpůsobit text stránky seznamu problémů na portálu pro vývojáře.  
  
 ![Portál pro vývojáře seznamu problémů](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "Portál pro vývojáře seznamu problémů APIM")  
  
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
 Šablona `Issue list` může používat následující [ovládací prvky stránky](api-management-page-controls.md).  
  
-   [stránkovací řízení](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Datový model  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Issues`|Kolekce entit [vydání.](api-management-template-data-model-reference.md#Issue)|Problémy viditelné pro aktuálního uživatele.|  
|`Paging`|[Stránkovací entita.](api-management-template-data-model-reference.md#Paging)|Stránkování informace pro kolekci aplikací.|  
|`IsAuthenticated`|Boolean|Určuje, zda je aktuální uživatel přihlášen k portálu pro vývojáře.|  
|`CanReportIssues`|Boolean|Určuje, zda má aktuální uživatel oprávnění k podání problému.|  
|`Search`|řetězec|Tato vlastnost je zastaralé a by neměly být používány.|  
  
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

## <a name="next-steps"></a>Další kroky
Další informace o práci se šablonami najdete [v tématu Jak přizpůsobit portál pro vývojáře pro správu rozhraní API pomocí šablon](api-management-developer-portal-templates.md).
