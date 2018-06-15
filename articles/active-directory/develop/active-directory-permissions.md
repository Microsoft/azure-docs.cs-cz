---
title: Oprávnění ve službě Azure AD | Microsoft Docs
description: Seznamte se s obory a oprávněními v Azure Active Directory a jejich použitím.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 6c0dc122-2cd8-4d70-be5a-3943459d308e
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 04/20/2017
ms.author: celested
ms.reviewer: justhu
ms.custom: aaddev
ms.openlocfilehash: 749253d6a082bcdc2b80c5984f20c4b8c4039ad0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156887"
---
# <a name="permissions-in-azure-ad"></a>Oprávnění ve službě Azure AD
Azure Active Directory (Azure AD) ve velké míře využívá oprávnění v tocích OAuth i OpenID Connect (OIDC). Když vaše aplikace obdrží přístupový token ze služby Azure AD, bude tento token obsahovat deklarace identity popisující oprávnění (označovaná také jako obory) vaší aplikace ve vztahu ke konkrétnímu prostředku. Tím se usnadňuje autorizace prostředku, protože stačí pouze zkontrolovat, jestli váš token obsahuje odpovídající oprávnění pro volané rozhraní API. 

## <a name="types-of-permissions"></a>Typy oprávnění
Azure AD definuje dva typy oprávnění: 
* **Delegovaná oprávnění** – Používají je aplikace, ve kterých je přihlášený uživatel. V případě těchto aplikací uživatel nebo správce udělí souhlas s oprávněními, která aplikace požaduje, a aplikaci se deleguje oprávnění k tomu, aby při volání rozhraní API fungovala jako přihlášený uživatel. V závislosti na rozhraní API uživatel možná nebude moct udělit souhlas s rozhraním API přímo a místo toho bude muset [požádat správce o poskytnutí souhlasu správce](/azure/active-directory/develop/active-directory-devhowto-multi-tenant-overview#understanding-user-and-admin-consent).
* **Oprávnění aplikací** – Používají je aplikace spuštěné bez přihlášeného uživatele, například aplikace spuštěné jako služby na pozadí nebo procesy démon. Oprávnění aplikací může [odsouhlasit pouze správce](/azure/active-directory/develop/active-directory-v2-scopes#requesting-consent-for-an-entire-tenant), protože jsou obvykle velmi mocná a umožňují přístup k datům různých uživatelů nebo datům, ke kterým by jinak měl přístup pouze správce. 

Efektivní oprávnění jsou oprávnění, která bude mít vaše aplikace při posílání požadavků do rozhraní API. 

* V případě delegovaných oprávnění budou efektivní oprávnění vaší aplikace představovat průnik nejnižších oprávnění udělených aplikaci (prostřednictvím souhlasu) a oprávnění aktuálně přihlášeného uživatele. Aplikace nemůže mít nikdy více oprávnění než přihlášený uživatel. V rámci organizací je možné oprávnění přihlášeného uživatele určit pomocí zásady nebo členství v jedné nebo několika rolích správce. Další informace o rolích správce najdete v tématu [Přiřazování rolí správce ve službě Azure AD](/azure/active-directory/active-directory-assign-admin-roles-azure-portal.md).
    Předpokládejme například, že má vaše aplikace udělené delegované oprávnění `User.ReadWrite.All` v Microsoft Graphu. Toto oprávnění vaší aplikaci výslovně uděluje oprávnění ke čtení a aktualizaci profilu každého uživatele v organizaci. Pokud je přihlášený uživatel globální správce, vaše aplikace bude moct aktualizovat profil každého uživatele v organizaci. Pokud však přihlášený uživatel není v roli správce, vaše aplikace bude moct aktualizovat pouze profil přihlášeného uživatele. Nebude moct aktualizovat profily ostatních uživatelů v organizaci, protože uživatel, jehož jménem má aplikace oprávnění jednat, tato oprávnění nemá.
* V případě oprávnění aplikací představují efektivní oprávnění vaší aplikace plný rozsah implikovaných oprávnění. Například aplikace s oprávněním aplikace `User.ReadWrite.All` může aktualizovat profil každého uživatele v organizaci. 

## <a name="permission-attributes"></a>Atributy oprávnění
Oprávnění ve službě Azure AD mají řadu vlastností, které uživatelům, správcům nebo vývojářům aplikací pomáhají kvalifikovaně rozhodovat o tom, k čemu oprávnění uděluje přístup. 

> [!NOTE]
> Oprávnění zveřejněná aplikací nebo instančním objektem Azure AD můžete zobrazit pomocí webu Azure Portal nebo PowerShellu. Vyzkoušejte tento skript k zobrazení oprávnění zveřejněných Microsoft Graphem.
> ```powershell
> Connect-AzureAD
> 
> # Get OAuth2 Permissions/delegated permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").OAuth2Permissions
> 
> # Get App roles/application permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").AppRoles
> ```

| Název vlastnosti | Popis | Příklad | 
| --- | --- | --- |
| ID | Toto je hodnota GUID, která jednoznačně identifikuje toto oprávnění. | 570282fd-fa5c-430d-a7fd-fc8dc98a9dca | 
| IsEnabled | Určuje, jestli je tento obor k dispozici pro použití. | true (pravda) | 
| Typ | Určuje, jestli toto oprávnění vyžaduje souhlas uživatele nebo souhlas správce. | Uživatel | 
| AdminConsentDescription | Toto je popis, který se zobrazí správcům při udělování souhlasu správce. | Umožňuje aplikaci číst e-maily v poštovních schránkách uživatelů. | 
| AdminConsentDisplayName | Toto je popisný název, který se zobrazí správcům při udělování souhlasu správce. | Čtení pošty uživatelů | 
| UserConsentDescription | Toto je popis, který se zobrazí uživatelům při udělování souhlasu uživatele. |  Umožňuje aplikaci číst e-maily ve vaší poštovní schránce. | 
| UserConsentDisplayName | Toto je popisný název, který se zobrazí uživatelům při udělování souhlasu uživatele. | Čtení vaší pošty | 
| Hodnota | Toto je řetězec, který slouží k identifikaci oprávnění v autorizačních tocích OAuth 2.0. V kombinaci s řetězcem identifikátoru URI ID aplikace také může vytvořit plně kvalifikovaný název oprávnění. | `Mail.Read` | 

## <a name="types-of-consent"></a>Typy souhlasu
Aplikace ve službě Azure AD získávají přístup k potřebným prostředkům nebo rozhraním API na základě souhlasu. Existuje několik druhů souhlasu, o kterých byste měli vědět, aby vaše aplikace byla úspěšná. Pokud definujete oprávnění, budete také muset porozumět způsobu, jakým vaši uživatelé budou získávat přístup k vaší aplikaci nebo rozhraní API.

* **Statický souhlas uživatele** – Uděluje se automaticky v rámci [autorizačního toku OAuth 2.0](/azure/active-directory/develop/active-directory-protocols-oauth-code.md#request-an-authorization-code) při zadávání prostředků, se kterými chce vaše aplikace pracovat. Ve scénáři využívajícím statický souhlas uživatele musí vaše aplikace již mít zadaná všechna potřebná oprávnění v konfiguraci aplikace na webu Azure Portal. Pokud uživatel (případně správce) neudělil souhlas pro tuto aplikaci, Azure AD v tuto chvíli zobrazí uživateli výzvu k poskytnutí souhlasu. 

    Další informace o registraci aplikace Azure AD, která vyžaduje přístup k statické sadě rozhraní API.
* **Dynamický souhlas uživatele** – Jedná se o funkci modelu aplikace Azure AD v2. V tomto scénáři vaše aplikace vyžaduje sadu potřebných oborů v [autorizačním toku OAuth 2.0 pro aplikace v2](/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent). Pokud uživatel ještě neudělil souhlas, v tuto chvíli se zobrazí výzva k vyjádření souhlasu. [Další informace o dynamickém souhlasu](/azure/active-directory/develop/active-directory-v2-compare#incremental-and-dynamic-consent).

    > [!NOTE]
    > Dynamický souhlas může být praktický, ale představuje velkou výzvu pro oprávnění vyžadující souhlas správce, protože prostředí pro udělení souhlasu správce v době vyjádření souhlasu nemá informace o těchto oprávněních. Pokud potřebujete obory s oprávněním správce, vaše aplikace je musí zaregistrovat na webu Azure Portal.
  
* **Souhlas správce** – Vyžaduje se v případě, že vaše aplikace potřebuje přístup k určitým vysoce privilegovaným oprávněním. Tím se zajistí, aby správci měli k dispozici několik dalších ovládacích prvků, než autorizují přístup aplikací nebo uživatelů k vysoce privilegovaným datům organizace. [Další informace o udělování souhlasu správce](/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="best-practices"></a>Osvědčené postupy

### <a name="resource-best-practices"></a>Osvědčené postupy pro prostředky
Prostředky, které zveřejňují rozhraní API, by měly definovat velmi konkrétní oprávnění k datům nebo akcím, které chrání. Tím se pomůže zajistit, že klienti neskončí s oprávněním k přístupu k datům, která nepotřebují, a že uživatelé budou dobře informovaní o tom, ke kterým datům udělují souhlas.

Oprávnění `Read` a `ReadWrite` by prostředky měly definovat explicitně a zvlášť. 

Prostředky by měly všechna oprávnění povolující přístup k datům různých uživatelů označovat jako oprávnění `Admin`. 

Prostředky by měly dodržovat formát pojmenování `Subject.Permission[.Modifier]`, kde `Subject` odpovídá typu dostupných dat, `Permission` odpovídá akci, kterou může uživatel s daty provést, a `Modifier` volitelně slouží k popisu specializací jiného oprávnění. Příklad: 
* Mail.Read – Umožňuje uživatelům číst poštu. 
* Mail.ReadWrite – Umožňuje uživatelům číst nebo psát poštu.
* Mail.ReadWrite.All – Umožňuje správci nebo uživateli přístup k poště v organizaci.

### <a name="client-best-practices"></a>Osvědčené postupy pro klienta
Požadujte oprávnění pouze pro obory, které vaše aplikace potřebuje. Aplikace s příliš mnoha oprávněními se vystavují riziku zveřejnění dat uživatelů v případě napadení.

Klienti by ze stejné aplikace neměli požadovat oprávnění aplikace i delegovaná oprávnění. To může mít za následek zvýšení oprávnění a umožnit uživateli získat přístup k datům, ke kterým jeho vlastní oprávnění přístup nepovoluje. 




