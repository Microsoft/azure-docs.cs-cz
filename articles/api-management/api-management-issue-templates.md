---
title: Vystavení šablon ve službě Azure API Management | Microsoft Docs
description: Zjistěte, jak přizpůsobit obsah stránky problém v portálu pro vývojáře ve službě Azure API Management.
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
ms.openlocfilehash: 9d13a146e94328b8ac57dc1036676328a4bea9d9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23835129"
---
# <a name="issue-templates-in-azure-api-management"></a>Vystavení šablon ve službě Azure API Management
Azure API Management poskytuje schopnost přizpůsobit obsah stránky na portálu vývojáře pomocí sady šablony, které konfigurace jejich obsahu. Pomocí [DotLiquid](http://dotliquidmarkup.org/) syntaxe a editoru podle své volby, například [DotLiquid pro návrháře](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), a lokalizované zadaný sadu [řetězce prostředků](api-management-template-resources.md#strings), [glyfy prostředky](api-management-template-resources.md#glyphs), a [stránka ovládací prvky](api-management-page-controls.md), máte flexibilitu při konfiguraci obsahu stránek, podle potřeby pomocí těchto šablon.  
  
 Šablony v této části umožňují přizpůsobit obsah stránky problém v portálu pro vývojáře.  
  
-   [Seznam problémů](#IssueList)  
  
> [!NOTE]
>  Ukázka výchozí šablony jsou zahrnuty v následující dokumentaci, ale mohou být změněna z důvodu průběžné vylepšení. Za provozu výchozí šablony můžete zobrazit v portálu pro vývojáře přechodem na jednotlivé požadované šablony. Další informace o práci se šablonami najdete v tématu [postup přizpůsobení portálu pro vývojáře API Management pomocí šablon](api-management-developer-portal-templates.md).  
  
##  <a name="IssueList"></a>Seznam problémů  
 **Seznam problémů** šablona umožňuje přizpůsobení textu stránky seznamu problém v portálu pro vývojáře.  
  
 ![Vystavení portál pro vývojáře seznamu](./media/api-management-issue-templates/APIM-Issue-List-Developer-Portal.png "portál pro vývojáře APIM problém seznamu")  
  
### <a name="default-template"></a>Výchozí šablony  
  
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
 `Issue list` Šablona může používat následující [stránka ovládací prvky](api-management-page-controls.md).  
  
-   [ovládací prvek stránkování](api-management-page-controls.md#paging-control)  
  
### <a name="data-model"></a>Datový model  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|Problémy|Kolekce [problém](api-management-template-data-model-reference.md#Issue) entity.|Problémy, které jsou viditelné pro aktuálního uživatele.|  
|Stránkování|[Stránkování](api-management-template-data-model-reference.md#Paging) entity.|Informace o stránkování pro kolekce aplikací.|  
|IsAuthenticated|Logická hodnota|Jestli má aktuální uživatel je přihlášený k portálu pro vývojáře.|  
|CanReportIssues|Logická hodnota|Jestli má aktuální uživatel oprávnění k souboru problém.|  
|Search|Řetězec|Tato vlastnost je zastaralá a by se neměla používat.|  
  
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
Další informace o práci se šablonami najdete v tématu [postup přizpůsobení portálu pro vývojáře API Management pomocí šablon](api-management-developer-portal-templates.md).