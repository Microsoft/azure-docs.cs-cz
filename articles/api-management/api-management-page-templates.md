---
title: Stránka šablon ve službě Azure API Management | Dokumentace Microsoftu
description: Zjistěte, jak přizpůsobit obsah stránek portálu pro vývojáře pomocí sady šablon ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2018
ms.author: apimpm
ms.openlocfilehash: 1fbafcdab938a0f8653df48631d7733cc58a3668
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441906"
---
# <a name="page-templates-in-azure-api-management"></a>Stránka šablon ve službě Azure API Management
Azure API Management poskytuje schopnost přizpůsobit obsah stránek portálu pro vývojáře pomocí sady šablon, které konfigurace jejich obsahu. Pomocí [DotLiquid](http://dotliquidmarkup.org/) syntaxe a do editoru podle vašeho výběru, jako například [DotLiquid pro profesionální návrháře využívající](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), nebo umístění zadaná sada [prostředky řetězce, které](api-management-template-resources.md#strings), [glyfů prostředky](api-management-template-resources.md#glyphs), a [stránce ovládací prvky](api-management-page-controls.md), máte velkou flexibilitu konfigurovat obsahu stránek podle svých potřeb, pomocí těchto šablon.  
  
 Šablony v této části můžete použít k přizpůsobení obsahu přihlášení, přihlaste se a stránka nebyla nalezena stránek portálu pro vývojáře.  
  
-   [Přihlásit se](#SignIn)  
  
-   [Zaregistrovat se](#SignUp)  
  
-   [Stránka nebyla nalezena.](#PageNotFound)  
  
> [!NOTE]
>  Ukázka výchozí šablony jsou zahrnuty v následující dokumentaci, ale můžou se změnit kvůli průběžně vylepšujeme. Živé výchozí šablony můžete zobrazit na portálu pro vývojáře tak, že přejdete na jednotlivé požadované šablony. Další informace o práci se šablonami najdete v tématu [přizpůsobení portálu pro správu rozhraní API pro vývojáře pomocí šablon](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="SignIn"></a> Přihlásit se  
 **Přihlášení** šablony vám umožní přizpůsobit přihlašovací stránku na portálu pro vývojáře.  
  
 ![Přihlašovací stránka](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "APIM přihlášení šablon portálu pro vývojáře stránky")  
  
### <a name="default-template"></a>Výchozí šablona  
  
```xml  
<h2 class="text-center">{% localized "SigninStrings|WebAuthenticationSigninTitle" %}</h2>  
{% if registrationEnabled == true %}  
<p class="text-center">{% localized "SigninStrings|WebAuthenticationNotAMember" %}</p>  
{% endif %}  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    {% if registrationEnabled == true %}  
        <p>{% localized "SigninStrings|WebAuthenticationSigininWithPassword" %}</p>  
    <basic-SignIn></basic-SignIn>  
    {% endif %}  
  </div>  
  
    {% if registrationEnabled != true and providers.size == 0 %}  
        {% localized "ProviderInfoStrings|TextboxExternalIdentitiesDisabled" %}  
  {% else %}  
        {% if providers.size > 0 %}  
      <div class="col-md-6">  
            <div class="providers-list">  
                <p class="text-left">  
                {% if registrationEnabled == true %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitation" %}  
                {% else %}  
                    {% localized "ProviderInfoStrings|TextboxExternalIdentitiesSigninInvitationPrimary" %}  
                {% endif %}  
                </p>  
        <providers></providers>  
            </div>  
    </div>  
        {% endif %}  
    {% endif %}  
  
  {% if userRegistrationTermsEnabled == true %}  
    <div class="col-md-6">  
        <div id="terms" class="modal" role="dialog" tabindex="-1">  
            <div class="modal-dialog">  
                <div class="modal-content">  
                    <div class="modal-header">  
                        <h4 class="modal-title">{% localized "SigninResources|DialogHeadingTermsOfUse" %}</h4>  
                    </div>  
                    <div class="modal-body break-all">{{userRegistrationTerms}}</div>  
                    <div class="modal-footer">  
                        <button type="button" class="btn btn-default" data-dismiss="modal">{% localized "CommonStrings|ButtonLabelClose" %}</button>  
                    </div>  
                </div>  
            </div>  
        </div>  
        <p>{% localized "SigninResources|TextblockUserRegistrationTermsProvided" %}</p>  
    </div>  
    {% endif %}  
</div>  
```  
  
### <a name="controls"></a>Ovládací prvky  
 Tato šablona může používat následující [stránce ovládací prvky](api-management-page-controls.md).  
  
-   [Basic – přihlášení](api-management-page-controls.md#basic-signin)  
  
-   [Zprostředkovatelé](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Datový model  
 [Přihlášení uživatele](api-management-template-data-model-reference.md#UseSignIn) entity.  
  
### <a name="sample-template-data"></a>Ukázková data šablony  
  
```json  
{
    "Email": null,
    "Password": null,
    "ReturnUrl": null,
    "RememberMe": false,
    "RegistrationEnabled": true,
    "DelegationEnabled": false,
    "DelegationUrl": null,
    "SsoSignUpUrl": null,
    "AuxServiceUrl": "https://portal.azure.com/#resource/subscriptions/{subscription ID}/resourceGroups/Api-Default-West-US/providers/Microsoft.ApiManagement/service/contoso5",
    "Providers": [  
        {  
            "Properties": {  
                "AuthenticationType": "Aad",  
                "Caption": "Azure Active Directory"  
            },  
            "AuthenticationType": "Aad",  
            "Caption": "Azure Active Directory"  
        }  
        ],
    "UserRegistrationTerms": null,
    "UserRegistrationTermsEnabled": false
}
```  
  
##  <a name="SignUp"></a> Registrace  
 **Zaregistrovat** šablony vám umožní přizpůsobit přihlašovací stránku v portálu pro vývojáře.  
  
 ![Zaregistrujte stránku](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "APIM registrace šablon portálu pro vývojáře stránky")  
  
### <a name="default-template"></a>Výchozí šablona  
  
```xml  
<h2 class="text-center">{% localized "SignupStrings|PageTitleSignup" %}</h2>  
<p class="text-center">  
  {% localized "SignupStrings|WebAuthenticationAlreadyAMember" %} <a href="/signin">{% localized "SignupStrings|WebAuthenticationSigninNow" %}</a>  
</p>  
  
<div class="row center-block ap-idp-container">  
  <div class="col-md-6">  
    <p>{% localized "SignupStrings|WebAuthenticationCreateNewAccount" %}</p>  
    <sign-up></sign-up>  
  </div>  
</div>  
```  
  
### <a name="controls"></a>Ovládací prvky  
 Tato šablona může používat následující [stránce ovládací prvky](api-management-page-controls.md).  
  
-   [Zaregistrujte se](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Datový model  
 [Registrace uživatelů do](api-management-template-data-model-reference.md#UserSignUp) entity.  
  
### <a name="sample-template-data"></a>Ukázková data šablony  
  
```json  
{  
    "PasswordConfirm": null,  
    "Password": null,  
    "PasswordVerdictLevel": 0,  
    "UserRegistrationTerms": null,  
    "UserRegistrationTermsOptions": 0,  
    "ConsentAccepted": false,  
    "Email": null,  
    "FirstName": null,  
    "LastName": null,  
    "UserData": null,  
    "NameIdentifier": null,  
    "ProviderName": null  
}  
```  
  
##  <a name="PageNotFound"></a> Stránka nebyla nalezena.  
 **Stránka nebyla nalezena** šablony vám umožní přizpůsobit stránce nebyla nalezena stránka na portálu pro vývojáře.  
  
 ![Nebyla nalezena stránka](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "APIM nebyla nalezena stránka Developer šablon portálu")  
  
### <a name="default-template"></a>Výchozí šablona  
  
```xml  
<h2>{% localized "NotFoundStrings|PageTitleNotFound" %}</h2>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialCause" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseOldLink" %}</li>  
  <li>{% localized "NotFoundStrings|TextblockPotentialCauseMisspelledUrl" %}</li>  
</ul>  
  
<h3>{% localized "NotFoundStrings|TitlePotentialSolution" %}</h3>  
<ul>  
  <li>{% localized "NotFoundStrings|TextblockPotentialSolutionRetype" %}</li>  
  <li>  
    {% capture textPotentialSolutionStartOver %}{% localized "NotFoundStrings|TextblockPotentialSolutionStartOver" %}{% endcapture %}  
    {% capture homeLink %}<a href="/">{% localized "NotFoundStrings|LinkLabelHomePage" %}</a>{% endcapture %}  
    {% assign replaceString = '{0}' %}  
  
    {{ textPotentialSolutionStartOver | replace : replaceString, homeLink }}  
  </li>  
</ul>  
  
<p>  
  {% capture textReportProblem %}{% localized "NotFoundStrings|TextReportProblem" %}{% endcapture %}  
  {% capture emailLink %}<a href="mailto:apimgmt@microsoft.com" target="_self" title="API Management Support">{% localized "NotFoundStrings|LinkLabelSendUsEmail" %}</a>{% endcapture %}  
  {% assign replaceString = '{0}' %}  
  
  {{ textReportProblem | replace : replaceString, emailLink }}  
</p>  
```  
  
### <a name="controls"></a>Ovládací prvky  
 Tato šablona nemusí používat všechny [stránce ovládací prvky](api-management-page-controls.md).  
  
### <a name="data-model"></a>Datový model  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|referenceCode|řetězec|Kód generovaný, pokud se tato stránka zobrazila v důsledku vnitřní chyby.|  
|Kód chyby|řetězec|Kód generovaný, pokud se tato stránka zobrazila v důsledku vnitřní chyby.|  
|emailBody|řetězec|Tělo, které jsou generovány, pokud se tato stránka zobrazila v důsledku vnitřní chyby e-mailu.|  
|requestedUrl|řetězec|Adresa URL požadovat stránka nebyla nalezena.|  
|referrerUrl|řetězec|Adresa URL odkazující na požadovanou URL adresu.|  
  
### <a name="sample-template-data"></a>Ukázková data šablony  
  
```json  
{  
    "referenceCode": null,  
    "errorCode": null,  
    "emailBody": null,  
    "requestedUrl": "https://contoso5.portal.azure-api.net:443/NotFoundPage?startEditTemplate=NotFoundPage",  
    "referrerUrl": "https://contoso5.portal.azure-api.net/signup?startEditTemplate=SignUpTemplate"  
}  
```

## <a name="next-steps"></a>Další postup
Další informace o práci se šablonami najdete v tématu [přizpůsobení portálu pro správu rozhraní API pro vývojáře pomocí šablon](api-management-developer-portal-templates.md).
