---
title: Stránka šablon ve službě Azure API Management | Microsoft Docs
description: Zjistěte, jak přizpůsobit obsah stránky na portálu vývojáře pomocí sadu šablon ve službě Azure API Management.
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
ms.openlocfilehash: 882bb27c7692f4eaf628d26f6081b1e1015be81b
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2018
ms.locfileid: "27745003"
---
# <a name="page-templates-in-azure-api-management"></a>Stránka šablon ve službě Azure API Management
Azure API Management poskytuje schopnost přizpůsobit obsah stránky na portálu vývojáře pomocí sady šablony, které konfigurace jejich obsahu. Pomocí [DotLiquid](http://dotliquidmarkup.org/) syntaxe a editoru podle své volby, například [DotLiquid pro návrháře](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), a lokalizované zadaný sadu [řetězce prostředků](api-management-template-resources.md#strings), [glyfy prostředky](api-management-template-resources.md#glyphs), a [stránka ovládací prvky](api-management-page-controls.md), máte flexibilitu při konfiguraci obsahu stránek, podle potřeby pomocí těchto šablon.  
  
 Šablony v této části umožňují přizpůsobit obsah přihlášení, přihlaste se a stránka nebyla nalezena stránky v portálu pro vývojáře.  
  
-   [Přihlásit se](#SignIn)  
  
-   [Registrace](#SignUp)  
  
-   [Stránka nebyla nalezena.](#PageNotFound)  
  
> [!NOTE]
>  Ukázka výchozí šablony jsou zahrnuty v následující dokumentaci, ale mohou být změněna z důvodu průběžné vylepšení. Za provozu výchozí šablony můžete zobrazit v portálu pro vývojáře přechodem na jednotlivé požadované šablony. Další informace o práci se šablonami najdete v tématu [postup přizpůsobení portálu pro vývojáře API Management pomocí šablon](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
##  <a name="SignIn"></a>Přihlásit se  
 **Přihlášení** šablona umožňuje přizpůsobit přihlašovací stránku v portálu pro vývojáře.  
  
 ![Přihlašovací stránka](./media/api-management-page-templates/APIM-Sign-In-Page-Developer-Portal-Templates.png "APIM přihlášení vývojář stránky portálu šablony")  
  
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
 Tato šablona může používat následující [stránka ovládací prvky](api-management-page-controls.md).  
  
-   [Basic přihlášení](api-management-page-controls.md#basic-signin)  
  
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
  
##  <a name="SignUp"></a>Registrace  
 **Zaregistrovat** šablona umožňuje přizpůsobit přihlašovací stránku v portálu pro vývojáře.  
  
 ![Přihlašovací stránku služby](./media/api-management-page-templates/APIM-Sign-Up-Page-Developer-Portal-Templates.png "APIM registrace vývojář stránky portálu šablony")  
  
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
 Tato šablona může používat následující [stránka ovládací prvky](api-management-page-controls.md).  
  
-   [registrace](api-management-page-controls.md#sign-up)  
  
### <a name="data-model"></a>Datový model  
 [Registrace uživatele](api-management-template-data-model-reference.md#UserSignUp) entity.  
  
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
  
##  <a name="PageNotFound"></a>Stránka nebyla nalezena.  
 **Stránka nebyla nalezena** šablona umožňuje přizpůsobit stránka nebyla nalezena stránka v portálu pro vývojáře.  
  
 ![Nebyla nalezena stránka](./media/api-management-page-templates/APIM-Not-Found-Page-Developer-Portal-Templates.png "APIM nebyl nalezen vývojář stránky portálu šablony")  
  
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
 Tato šablona nesmíte používat žádné [stránka ovládací prvky](api-management-page-controls.md).  
  
### <a name="data-model"></a>Datový model  
  
|Vlastnost|Typ|Popis|  
|--------------|----------|-----------------|  
|referenceCode|řetězec|Kód vygenerováno, pokud tato stránka se nezobrazí v důsledku vnitřní chyby.|  
|Kód chyby|řetězec|Kód vygenerováno, pokud tato stránka se nezobrazí v důsledku vnitřní chyby.|  
|emailBody|řetězec|E-mailu textu vygenerováno, pokud tato stránka se nezobrazí v důsledku vnitřní chyby.|  
|requestedUrl|řetězec|Adresa URL vyžádá, když stránka nebyla nalezena.|  
|referrerUrl|řetězec|Odkazující server Adresa URL pro požadovanou adresu URL.|  
  
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
Další informace o práci se šablonami najdete v tématu [postup přizpůsobení portálu pro vývojáře API Management pomocí šablon](api-management-developer-portal-templates.md).
