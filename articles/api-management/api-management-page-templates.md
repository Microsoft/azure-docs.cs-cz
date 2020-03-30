---
title: Šablony stránek ve správě rozhraní Azure API | Dokumenty společnosti Microsoft
description: Zjistěte, jak přizpůsobit obsah stránek portálu pro vývojáře pomocí sady šablon ve správě rozhraní Azure API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: e57df269-1019-4b74-b74d-53155b809d59
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: ce56c406c884471c445b25343d5c42f9edcbe4c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249539"
---
# <a name="page-templates-in-azure-api-management"></a>Šablony stránek ve správě rozhraní Azure API
Azure API Management poskytuje možnost přizpůsobit obsah stránek portálu pro vývojáře pomocí sady šablon, které konfigurují jejich obsah. Pomocí [DotLiquid](http://dotliquidmarkup.org/) syntaxe a editor podle vašeho výběru, jako je [například DotLiquid pro návrháře](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)a za předpokladu, sadu lokalizovaných [prostředků řetězce](api-management-template-resources.md#strings), glyph [prostředky](api-management-template-resources.md#glyphs)a page [ovládací prvky](api-management-page-controls.md), máte velkou flexibilitu ke konfiguraci obsahu stránek, jak uznáte za vhodné pomocí těchto šablon.  
  
 Šablony v této části umožňují přizpůsobit obsah přihlašovacích stránek, registrací a stránek, které nebyly nalezeny na portálu pro vývojáře.  
  
-   [Přihlásit](#SignIn)  
  
-   [Registrace](#SignUp)  
  
-   [Stránka nebyla nalezena.](#PageNotFound)  
  
> [!NOTE]
>  Ukázkové výchozí šablony jsou zahrnuty v následující dokumentaci, ale mohou se měnit z důvodu neustálého zlepšování. Výchozí živé šablony můžete zobrazit na portálu pro vývojáře tak, že přejdete na požadované jednotlivé šablony. Další informace o práci se šablonami najdete [v tématu Jak přizpůsobit portál pro vývojáře pro správu rozhraní API pomocí šablon](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="sign-in"></a><a name="SignIn"></a>Přihlásit se  
 Přihlašovací **sign in** šablona umožňuje přizpůsobit přihlašovací stránku na portálu pro vývojáře.  
  
 ![Přihlašovací stránka](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "Šablony portálu pro přihlášení apimů na portálu pro vývojáře stránek")  
  
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
 Tato šablona může používat následující [ovládací prvky stránky](api-management-page-controls.md).  
  
-   [základní signin](api-management-page-controls.md#basic-signin)  
  
-   [Poskytovatelů](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Datový model  
 [Entita přihlášení uživatele.](api-management-template-data-model-reference.md#UseSignIn)  
  
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
  
##  <a name="sign-up"></a><a name="SignUp"></a>Registrace  
 Šablona **registrace** umožňuje přizpůsobit stránku registrace na portálu pro vývojáře.  
  
 ![Zaregistrovat stránku](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "Šablony portálu pro registraci stránek APIM")  
  
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
 Tato šablona může používat následující [ovládací prvky stránky](api-management-page-controls.md).  
  
-   [Sign-up](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Datový model  
 [Entita registrace uživatele.](api-management-template-data-model-reference.md#UserSignUp)  
  
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
  
##  <a name="page-not-found"></a><a name="PageNotFound"></a>Stránka nebyla nalezena.  
 Šablona **stránka nebyla nalezena** umožňuje přizpůsobit stránku, která nebyla nalezena na portálu pro vývojáře.  
  
 ![Stránka Nebyla nalezena](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "Šablony portálu pro vývojáře stránek APIM nebyly nalezeny")  
  
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
 Tato šablona nesmí používat žádné [ovládací prvky stránky](api-management-page-controls.md).  
  
### <a name="data-model"></a>Datový model  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|referenceKód|řetězec|Kód generovaný, pokud byla tato stránka zobrazena jako výsledek vnitřní chyby.|  
|Errorcode|řetězec|Kód generovaný, pokud byla tato stránka zobrazena jako výsledek vnitřní chyby.|  
|emailBody|řetězec|Tělo e-mailu generované, pokud tato stránka byla zobrazena jako výsledek vnitřní chyby.|  
|požadovaná adresa Url|řetězec|Adresa URL požadovaná v případě, že stránka nebyla nalezena.|  
|odkazová adresa Url|řetězec|Adresa URL odkazované ho odkazuje na požadovanou adresu URL.|  
  
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

## <a name="next-steps"></a>Další kroky
Další informace o práci se šablonami najdete [v tématu Jak přizpůsobit portál pro vývojáře pro správu rozhraní API pomocí šablon](api-management-developer-portal-templates.md).
