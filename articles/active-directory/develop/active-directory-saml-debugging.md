---
title: Postup ladění na základě SAML jednotného přihlašování k aplikacím v Azure Active Directory | Microsoft Docs
description: 'Zjistěte, jak ladit na základě SAML jednotného přihlašování k aplikacím v Azure Active Directory '
services: active-directory
author: asmalser-msft
documentationcenter: na
manager: mtillman
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: asmalser
ms.custom: aaddev
ms.reviewer: dastrock
ms.openlocfilehash: 55ff6b7a70bcdcceacb1484f9969337f9853ce50
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Postup ladění na základě SAML jednotného přihlašování k aplikacím v Azure Active Directory

Při ladění aplikace založené na SAML integrace, je často vhodné použít nástroj, jako je [Fiddler](http://www.telerik.com/fiddler) SAML požadavek a odpověď SAML, které obsahují token SAML, který byl vydán pro aplikaci. 

![Fiddler][1]

Tyto problémy jsou často související s nesprávnou konfiguraci v Azure Active Directory nebo na straně aplikace. V závislosti na tom, kde se zobrazí chyba je nutné zkontrolovat SAML požadavku nebo odpovědi:

- Zobrazuje chybu ve společnosti přihlašovací stránka Moje [odkazy na části]
- Zobrazuje chybu na stránce aplikace po přihlášení [odkazy na části]

## <a name="i-see-an-error-in-my-company-sign-in-page"></a>V mé přihlašovací stránku společnosti se zobrazují k chybě.

Můžete najít mapování 1: 1 mezi kód chyby a postup řešení v [potíže při přihlašování do Galerie aplikace nakonfigurovaná pro federované jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML).

Pokud uvidíte chybu v přihlašovací stránku vaší společnosti, budete potřebovat chybová zpráva a žádost SAML k ladění problém.

### <a name="how-can-i-get-the-error--message"></a>Načtení chybová zpráva

K získání chybové zprávy, podívejte se na pravém dolním rohu stránky. Zobrazí chybu, která zahrnuje:

- CorrelationID
- Časovým razítkem
- Zpráva

![Chyba][2] 

 
Id korelace a časové razítko tvoří jedinečný identifikátor, který můžete použít k vyhledání protokolů back-end přidružené k chybě přihlášení. Tyto hodnoty jsou důležité, když vytvoříte případu podpory se společností Microsoft, protože pomáhají technici zajistit rychlejší řešení svých problémů.

Zpráva je hlavní příčinou problému přihlášení. 


### <a name="how-can-i-review-the-saml-request"></a>Jak můžete zkontrolovat žádost SAML?

SAML požadavek odeslaný aplikací do Azure Active Directory je obvykle poslední odpověď HTTP 200 [ https://login.microsoftonline.com ](https://login.microsoftonline.com).
 
Pomocí Fiddleru, můžete zobrazit žádost SAML výběrem tohoto řádku a potom výběrem **inspektoři > WebForms** kartě v pravém panelu. Klikněte pravým tlačítkem myši **SAMLRequest** hodnotu a pak vyberte **poslat TextWizard**. Potom vyberte **z formátu Base64** z **transformace** nabídky k dekódování tokenu a zobrazit jeho obsah. 

Navíc můžete kopírovat hodnota SAMLrequest a použít jiný decoder Base64.

    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    Destination=https://login.microsoftonline.com/<Tenant-ID>/saml2
    AssertionConsumerServiceURL="https://contoso.applicationname.com/acs"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>

S žádostí SAML, dekódovat zkontrolujte následující položky:

1. **Cílový** v žádosti SAML odpovídají SAML jeden přihlašování adresa URL služby získané z Azure Active Directory.
 
2. **Vystavitel** v SAML požadavku je stejný **identifikátor** jste nakonfigurovali pro aplikaci v Azure Active Directory. Azure AD používá vystavitele najít aplikaci ve vašem adresáři.

3. **AssertionConsumerServiceURL** je, kde se předpokládá, že aplikace přijímat tokenu SAML z Azure Active Directory. Tuto hodnotu můžete nakonfigurovat v Azure Active Directory, ale není povinný, pokud je součástí žádost SAML.


## <a name="i-see-an-error-on-the-applications-page-after-signing-in"></a>I po přihlášení na stránce aplikace zobrazí chyba

V tomto scénáři aplikace nepřijímá odpovědi vydané službou Azure AD. Je důležité, abyste ověřili odpověď z Azure AD, který obsahuje token SAML se na dodavatele aplikace a zjistit, co je chybí nebo je nesprávný. 

### <a name="how-can-i-get-and-review-the-saml-response"></a>Jak můžete získat a zkontrolujte odpověď SAML?

Odpověď z Azure AD, který obsahuje SAML token je obvykle ten, který se nachází za přesměrování HTTP 302 z https://login.microsoftonline.com a je odeslána do konfigurovaného **adresa URL odpovědi** aplikace. 

Můžete zobrazit výběrem tohoto řádku a potom výběrem tokenu SAML **inspektoři > WebForms** kartě v pravém panelu. Zde, klikněte pravým tlačítkem myši **SAMLResponse** hodnotu a vyberte **poslat TextWizard**. Potom vyberte **z formátu Base64** z **transformace** nabídky k dekódování tokenu a zobrazte její obsah, použijte jiný decoder Base64. 

Můžete také zkopírovat hodnotu v **SAMLrequest** a použít jiný decoder Base64.

Navštivte [chyby na stránce aplikace po přihlášení](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML) řešení potíží s pokyny pro další informace o co může být chybějící nebo nesprávný v odpovědi SAML.

Informace o tom, jak zkontrolovat SAML odpovědi najdete v článku [protokolu SAML přihlašování](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML#response).


## <a name="related-articles"></a>Související články
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](../active-directory-apps-index.md)
* [Konfigurace jednotného přihlašování k aplikacím, které nejsou v galerii aplikací Azure Active Directory](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Postup přizpůsobení deklarace identity vystavené v tokenu SAML pro předběžně integrované aplikace](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png
[2]: ../media/active-directory-saml-debugging/error.png
