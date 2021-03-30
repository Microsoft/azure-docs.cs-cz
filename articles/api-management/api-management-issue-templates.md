---
title: Vystavení šablon v Azure API Management | Microsoft Docs
description: Naučte se přizpůsobit obsah stránek problému na portálu pro vývojáře v Azure API Management.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85846855"
---
# <a name="issue-templates-in-azure-api-management"></a>Vystavení šablon v Azure API Management
Azure API Management poskytuje možnost přizpůsobení obsahu stránek portálu pro vývojáře pomocí sady šablon, které konfigurují svůj obsah. Pomocí syntaxe [DotLiquid](http://dotliquidmarkup.org/) a editoru podle vlastního výběru, jako je například [DotLiquid pro návrháře](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)a poskytnutá sada lokalizovaných [řetězcových prostředků](api-management-template-resources.md#strings), [prostředků glyfů](api-management-template-resources.md#glyphs)a [ovládacích prvků stránky](api-management-page-controls.md), máte skvělou flexibilitu pro konfiguraci obsahu stránek, jak vidíte, aby byly použity pomocí těchto šablon.  
  
 Šablony v této části umožňují přizpůsobit obsah stránek problému na portálu pro vývojáře.  
  
-   [Seznam problémů](#IssueList)  
  
> [!NOTE]
>  Ukázkové výchozí šablony jsou uvedené v následující dokumentaci, ale můžou se změnit z důvodu průběžných vylepšení. Živé výchozí šablony můžete zobrazit na portálu pro vývojáře tak, že přejdete na požadované jednotlivé šablony. Další informace o práci se šablonami najdete v tématu [Postup přizpůsobení API Management portálu pro vývojáře pomocí šablon](api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="issue-list"></a><a name="IssueList"></a> Seznam problémů  
 Šablona **seznam problémů** vám umožní přizpůsobit tělo stránky se seznamem problémů na portálu pro vývojáře.  
  
 ![Portál pro vývojáře seznamu problémů](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "Portál pro vývojáře seznam problémů APIM")  
  
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
 `Issue list`Šablona může používat následující [ovládací prvky stránky](api-management-page-controls.md).  
  
-   [ovládací prvek stránkování](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Datový model  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|`Issues`|Kolekce entit [problémů](api-management-template-data-model-reference.md#Issue) .|Problémy viditelné pro aktuálního uživatele.|  
|`Paging`|Entita [stránkování](api-management-template-data-model-reference.md#Paging)|Informace o stránkování kolekce aplikací.|  
|`IsAuthenticated`|boolean|Určuje, jestli je aktuální uživatel přihlášený k portálu pro vývojáře.|  
|`CanReportIssues`|boolean|Zda má aktuální uživatel oprávnění k zastavení problému.|  
|`Search`|řetězec|Tato vlastnost je zastaralá a neměla by se používat.|  
  
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
Další informace o práci se šablonami najdete v tématu [Postup přizpůsobení API Management portálu pro vývojáře pomocí šablon](api-management-developer-portal-templates.md).
