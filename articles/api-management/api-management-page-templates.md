---
title: Šablony stránek v Azure API Management | Microsoft Docs
description: Naučte se přizpůsobit obsah šablon stránek portálu pro vývojáře v Azure API Management.
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
ms.openlocfilehash: 24d026785025dba4ae45de404edec67c2cf3871a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91335581"
---
# <a name="page-templates-in-azure-api-management"></a>Šablony stránek v Azure API Management
Azure API Management poskytuje možnost přizpůsobení obsahu stránek portálu pro vývojáře pomocí sady šablon, které konfigurují svůj obsah. Pomocí syntaxe [DotLiquid](http://dotliquidmarkup.org/) a editoru podle vlastního výběru, jako je například [DotLiquid pro návrháře](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers)a poskytnutá sada lokalizovaných [řetězcových prostředků](api-management-template-resources.md#strings), [prostředků glyfů](api-management-template-resources.md#glyphs)a [ovládacích prvků stránky](api-management-page-controls.md), máte skvělou flexibilitu pro konfiguraci obsahu stránek, jak vidíte, aby byly použity pomocí těchto šablon.  
  
 Šablony v této části vám umožní přizpůsobit obsah stránek přihlášení, registrace a nenalezené stránky na portálu pro vývojáře.  
  
-   [Přihlásit](#SignIn)  
  
-   [Registrace](#SignUp)  
  
-   [Stránka nenalezena](#PageNotFound)  
  
> [!NOTE]
>  Ukázkové výchozí šablony jsou uvedené v následující dokumentaci, ale můžou se změnit z důvodu průběžných vylepšení. Živé výchozí šablony můžete zobrazit na portálu pro vývojáře tak, že přejdete na požadované jednotlivé šablony. Další informace o práci se šablonami najdete v tématu [Postup přizpůsobení API Management portálu pro vývojáře pomocí šablon](./api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="sign-in"></a><a name="SignIn"></a> Přihlásit se  
 Šablona **přihlášení** vám umožní přizpůsobit přihlašovací stránku na portálu pro vývojáře.  
  
 ![Přihlašovací stránka](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "Šablony portálu pro vývojáře pro APIM přihlašovací stránky")  
  
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
  
-   [základní – přihlášení](api-management-page-controls.md#basic-signin)  
  
-   [dodavateli](api-management-page-controls.md#providers)  
  
### <a name="data-model"></a>Datový model  
 [Přihlašovací entita uživatele](api-management-template-data-model-reference.md#UseSignIn)  
  
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
  
##  <a name="sign-up"></a><a name="SignUp"></a> Registrace  
 Šablona pro **registraci** vám umožní přizpůsobit si přihlašovací stránku na portálu pro vývojáře.  
  
 ![Přihlašovací stránka](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "Šablony portálu pro APIM pro registraci stránek pro vývojáře")  
  
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
  
-   [registrace](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Datový model  
 Entita [registrace uživatele](api-management-template-data-model-reference.md#UserSignUp)  
  
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
  
##  <a name="page-not-found"></a><a name="PageNotFound"></a> Stránka nenalezena  
 Šablona **nenalezené stránky** vám umožní přizpůsobit stránku nenalezené stránky na portálu pro vývojáře.  
  
 ![Stránka nenalezena](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "Šablony portálu pro vývojáře stránky APIM se nenašly.")  
  
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
 Tato šablona nemůže používat žádné [ovládací prvky stránky](api-management-page-controls.md).  
  
### <a name="data-model"></a>Datový model  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|referenceCode|řetězec|Generuje se kód, pokud se tato stránka zobrazila jako výsledek vnitřní chyby.|  
|errorCode|řetězec|Generuje se kód, pokud se tato stránka zobrazila jako výsledek vnitřní chyby.|  
|emailBody|řetězec|Tělo e-mailu vygenerovalo, pokud se tato stránka zobrazila jako výsledek vnitřní chyby.|  
|requestedUrl|řetězec|Adresa URL požadovaná při nalezení stránky|  
|referrerUrl|řetězec|Adresa URL odkazující na požadovanou adresu URL|  
  
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
Další informace o práci se šablonami najdete v tématu [Postup přizpůsobení API Management portálu pro vývojáře pomocí šablon](api-management-developer-portal-templates.md).
