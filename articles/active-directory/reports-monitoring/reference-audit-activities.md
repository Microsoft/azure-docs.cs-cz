---
title: Referenční informace k aktivitám auditování Azure Active Directory (Azure AD) | Microsoft Docs
description: Získejte přehled o aktivitách auditování, které můžete protokolovat v protokolech auditu v Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/24/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56f9976faf2a803e747bce8b851a3555e6d746b0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89231142"
---
# <a name="azure-ad-audit-activity-reference"></a>Referenční informace k aktivitám auditování Azure AD

Pomocí sestav Azure Active Directory (Azure AD) můžete získat informace, které potřebujete k určení toho, jak vaše prostředí dělá.

Architektura generování sestav v Azure AD se skládá z následujících komponent:

- **Sestavy aktivit** 
    - [Přihlášení](concept-sign-ins.md) – poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
    - [Protokoly auditu](concept-audit-logs.md) – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. 
    
- **Sestavy zabezpečení** 
    - [Riziková přihlášení](../identity-protection/overview-identity-protection.md) – Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. 
    - [Uživatelé označení příznakem rizika](../identity-protection/overview-identity-protection.md) – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený. 

Tento článek obsahuje seznam aktivit auditování, které můžete protokolovat v protokolech auditu.

## <a name="access-reviews"></a>Kontroly přístupu

|Kategorie auditu|Aktivita|
|---|---|
|Kontroly přístupu|Ukončení kontroly přístupu|
|Kontroly přístupu|Přidání schvalovatele k žádosti o schválení|
|Kontroly přístupu|Přidání revidujícího ke kontrole přístupu|
|Kontroly přístupu|Použití kontroly přístupu|
|Kontroly přístupu|Vytvoření kontroly přístupu|
|Kontroly přístupu|Vytvoření programu|
|Kontroly přístupu|Vytvoření žádosti o schválení|
|Kontroly přístupu|Odstranění kontroly přístupu|
|Kontroly přístupu|Odstranění programu|
|Kontroly přístupu|Připojení k řízení aplikací|
|Kontroly přístupu|Registrace ke kontrolám přístupu Azure AD|
|Kontroly přístupu|Odebrání revidujícího z kontroly přístupu|
|Kontroly přístupu|Žádost a zastavení kontroly|
|Kontroly přístupu|Žádost o použití výsledku kontroly|
|Kontroly přístupu|Kontrola členství v rolích RBAC|
|Kontroly přístupu|Kontrola přiřazení aplikace|
|Kontroly přístupu|Kontrola členství ve skupinách|
|Kontroly přístupu|Kontrola žádosti o schválení žádosti|
|Kontroly přístupu|Zrušení připojení k řízení aplikací|
|Kontroly přístupu|Aktualizace kontroly přístupu|
|Kontroly přístupu|Aktualizovat stav zprovoznění kontrol přístupu Azure AD|
|Kontroly přístupu|Aktualizace nastavení e-mailového oznámení o kontrole přístupu|
|Kontroly přístupu|Nastavení aktualizace počtu opakování kontroly přístupu|
|Kontroly přístupu|Nastavení aktualizace doby trvání opakování kontrol přístupu ve dnech|
|Kontroly přístupu|Nastavení typu konce opakování kontroly přístupu aktualizace|
|Kontroly přístupu|Nastavení aktualizace typu opakování kontroly přístupu|
|Kontroly přístupu|Aktualizace nastavení připomenutí kontroly přístupu|
|Kontroly přístupu|Aktualizace programu|
|Kontroly přístupu|Aktualizace žádosti o schválení|
|Kontroly přístupu|Uživatel zakázán|

## <a name="account-provisioning"></a>Zřizování účtů

|Kategorie auditu|Aktivita|
|---|---|
|Správa aplikací|Načtení udělení oprávnění aplikace V2|
|Správa aplikací|Načtení instančních objektů aplikace V2 v aktuálním tenantovi|
|Správa aplikací|Aktualizace aplikace V1|
|Správa aplikací|Aktualizace aplikace V2|
|Správa aplikací|Aktualizace udělení oprávnění aplikace V2|
|Správa aplikací|Přidání OAuth2PermissionGrant|
|Správa aplikací|Přidání přiřazení role aplikace k instančnímu objektu|

## <a name="application-proxy"></a>Proxy aplikací

|Kategorie auditu|Aktivita|
|---|---|
|Správa aplikací|Přidání aplikace|
|Správa aplikací|Přidání vlastníka do aplikace|
|Správa aplikací|Přidání vlastníka k instančnímu objektu|
|Správa aplikací|Přidání zásad k instančnímu objektu|
|Správa adresářů|Přidání instančního objektu|
|Správa adresářů|Přidání přihlašovacích údajů instančního objektu|
|Správa adresářů|Souhlas s aplikací|
|Správa adresářů|Odstranění aplikace|
|Správa adresářů|Trvalé odstranění aplikace|
|Správa adresářů|Odebrání OAuth2PermissionGrant|
|Správa adresářů|Odebrání přiřazení role aplikace z instančního objektu|
|Správa adresářů|Odebrání vlastníka z aplikace|
|Prostředek|Odebrání vlastníka z instančního objektu|
|Prostředek|Odebrání zásad z instančního objektu|
|Prostředek|Odebrání instančního objektu|


## <a name="automated-password-rollover"></a>Automatická změna hesel

|Kategorie auditu|Aktivita|
|---|---|
|Správa aplikací|Odebrání přihlašovacích údajů instančního objektu|


## <a name="b2c"></a>B2C

|Kategorie auditu|Aktivita|
|---|---|
|Správa aplikací|Obnovení aplikace|
|Správa aplikací|Odvolání souhlasu|
|Správa aplikací|Aktualizace aplikace|
|Správa aplikací|Aktualizace externích tajných klíčů|
|Správa aplikací|Aktualizace instančního objektu|
|Správa aplikací|Vydání přístupového tokenu pro aplikaci|
|Správa aplikací|Vydání ověřovacího kódu pro aplikaci|
|Správa aplikací|Vydání tokentu id_token pro aplikaci|
|Správa aplikací|Ověření přihlašovacích údajů místního účtu|
|Správa aplikací|Kontrola ověření uživatele|
|Správa aplikací|Přidání oprávnění aplikací V2|
|Správa aplikací|Přidání klíče založeného na tajném klíči ASCII do kontejneru klíčů CPIM|
|Správa aplikací|Přidání klíče do kontejneru klíčů CPIM|
|Správa aplikací|AdminPolicyDatas-SetResources|
|Správa aplikací|AdminUserJourneys-GetResources|
|Správa aplikací|AdminUserJourneys-RemoveResources|
|Authentication|AdminUserJourneys-SetResources|
|Authentication|Create IdentityProvider|
|Authentication|Vytvoření aplikace V1|
|Authentication|Vytvoření aplikace V2|
|Authentication|Vytvoření vlastních domén v tenantovi|
|Autorizace|Vytvoření nového objektu AdminUserJourney|
|Autorizace|Vytvoření kódu JSON lokalizovaného prostředku|
|Autorizace|Vytvoření nového vlastního zprostředkovatele identity|
|Autorizace|Vytvoření nového zprostředkovatele identity|
|Autorizace|Vytvoření nebo aktualizace prostředku adresáře B2C|
|Autorizace|Vytvoření zásad|
|Autorizace|Vytvoření zásad trustFramework|
|Autorizace|Vytvoření zásad trustFramework s konfigurovatelnou předponou|
|Autorizace|Vytvoření atributu uživatele|
|Autorizace|CreateTrustFrameworkPolicy|
|Autorizace|Vytvoří nebo aktualizuje nový objekt AdminUserJourney|
|Autorizace|Odstranění zprostředkovatele identity|
|Autorizace|Vytvoření objektu IdentityProvider|
|Autorizace|Odstranění aplikace V1|
|Autorizace|Odstranění aplikace V2|
|Autorizace|Odstranění udělení oprávnění aplikací V2|
|Autorizace|Odstranění prostředku adresáře B2C|
|Autorizace|Odstranění kontejneru klíčů CPIM|
|Autorizace|Odstranění zásad trustFramework|
|Autorizace|Odstranění atributu uživatele|
|Autorizace|Povolení funkce B2C|
|Autorizace|Získání prostředků adresáře B2C v předplatném|
|Autorizace|Získání vlastního zprostředkovatele identity|
|Autorizace|Získání zprostředkovatele identity|
|Autorizace|Získání aplikací V1 a V2|
|Autorizace|Získání aplikace V1|
|Autorizace|Získání aplikací V1|
|Autorizace|Získání aplikace V2|
|Autorizace|Získání aplikací V2|
|Autorizace|Získat prostředek adresáře B2C|
|Autorizace|Získání seznamu vlastních domén v tenantovi|
|Autorizace|Získání cesty uživatele|
|Autorizace|Získání povolených deklarací identity aplikace pro cestu uživatele|
|Autorizace|Získání povolených deklarací identity s vlastním potvrzením pro cestu uživatele|
|Autorizace|Získání povolených deklarací identity s vlastním potvrzením pro zásady|
|Autorizace|Získání seznamu dostupných výstupních deklarací identity|
|Autorizace|Získání definic obsahu pro cestu uživatele|
|Autorizace|Získání zprostředkovatelů identity pro konkrétní tok správy|
|Autorizace|Získání metadat aktivního klíče kontejneru klíčů ve formátu JWK|
|Autorizace|Získání seznamu všech toků správy|
|Autorizace|Získání seznamu značek pro všechny toky správy všech uživatelů|
|Autorizace|Získání seznamu tenantů pro uživatele|
|Autorizace|Získání deklarací identity s vlastním potvrzením pro místní účty|
|Autorizace|Získání kódu JSON lokalizovaného prostředku|
|Autorizace|Získání operací poskytovatele prostředků Microsoft.AzureActiveDirectory|
|Autorizace|Získání zásad|
|Autorizace|Získání zásady|
|Autorizace|Získání vlastností prostředku tenanta|
|Autorizace|Získání seznamu podporovaných zprostředkovatelů identity|
|Autorizace|Získání seznamu podporovaných zprostředkovatelů identity cesty uživatele|
|Autorizace|Získání informací o tenantovi|
|Autorizace|Získání povolených funkcí tenanta|
|Autorizace|Získání seznamu vlastních zprostředkovatelů identity definovaných v tenantovi|
|Autorizace|Získání seznamu zprostředkovatelů identity definovaných v tenantovi|
|Autorizace|Získání seznamu místních zprostředkovatelů identity definovaných v tenantovi|
|Autorizace|Získání podrobností o tenantovi pro uživatele pro vytvoření prostředku|
|Autorizace|Získání seznamu tenantů|
|Autorizace|Získání objektu tenantDomains|
|Autorizace|Získání výchozí podporované kultury pro CPIM|
|Autorizace|Získání podrobností o taku správy|
|Autorizace|Získání seznamu objektů UserJourney pro tohoto tenanta|
|Autorizace|Získání sady dostupných podporovaných kultur pro CPIM|
|Autorizace|Získání zásad trustFramework|
|Autorizace|Získání zásad trustFramework ve formátu XML|
|Autorizace|Získání atributu uživatele|
|Autorizace|Získání atributů uživatele|
|Autorizace|Získání seznamu cest uživatele|
|Autorizace|GetIEFPolicies|
|Autorizace|GetIdentityProviders|
|Autorizace|GetTrustFrameworkPolicy|
|Autorizace|Získá kontejner klíčů CPIM ve formátu JWK|
|Autorizace|Získá seznam kontejnerů klíčů v tenantovi|
|Autorizace|Získá typ tenanta|
|Autorizace|MigrateTenantMetadata|
|Autorizace|Oprava objektu IdentityProvider|
|Autorizace|PutTrustFrameworkPolicy|
|Autorizace|PutTrustFrameworkpolicy|
|Autorizace|Odebrání cesty uživatele|
|Autorizace|Obnovení zálohy kontejneru klíčů CPIM|
|Autorizace|Načtení udělení oprávnění aplikace V2|
|Autorizace|Načtení instančních objektů aplikace V2 v aktuálním tenantovi|
|Autorizace|Aktualizace vlastního zprostředkovatele identity|
|Autorizace|Aktualizace zprostředkovatele identity|
|Autorizace|Aktualizace místního zprostředkovatele identity|
|Autorizace|Aktualizace aplikace V1|
|Autorizace|Aktualizace aplikace V2|
|Autorizace|Aktualizace udělení oprávnění aplikace V2|
|Autorizace|Aktualizace zásad|
|Autorizace|Aktualizace atributu uživatele|
|Autorizace|Nahrání šifrovaného klíče CPIM|
|Autorizace|Autorizace uživatelů: Rozhraní API je zakázané pro sadu funkcí tenanta|
|Autorizace|Autorizace uživatelů: Uživatel má udělený přístup jako správce tenanta|
|Autorizace|Autorizace uživatelů: Uživatel má udělená přístupová práva ověřeného uživatele|
|Autorizace|Ověření povolení funkce B2C|
|Autorizace|Ověřte, jestli je funkce povolená.|
|Autorizace|Vytvoření programu|
|Autorizace|Odstranění programu|
|Autorizace|Připojení k řízení aplikací|
|Autorizace|Registrace ke kontrolám přístupu Azure AD|
|Autorizace|Zrušení připojení k řízení aplikací|
|Autorizace|Aktualizace programu|
|Autorizace|Zákaz jednotného přihlašování na počítači|
|Autorizace|Zákaz jednotného přihlašování na počítači pro konkrétní doménu|
|Autorizace|Zákaz proxy aplikací|
|Autorizace|Zákaz předávacího ověřování|
|Autorizace|Povolení jednotného přihlašování na počítači|
|Správa adresářů|Povolení jednotného přihlašování na počítači pro konkrétní doménu|
|Správa adresářů|Povolení proxy aplikací|
|Správa adresářů|Povolení předávacího ověřování|
|Správa adresářů|Vytvoření vlastních domén v tenantovi|
|Správa adresářů|Povolení funkce B2C|
|Správa adresářů|Získání seznamu vlastních domén v tenantovi|
|Správa adresářů|Získání vlastností prostředku tenanta|
|Správa adresářů|Získání informací o tenantovi|
|Správa adresářů|Získání povolených funkcí tenanta|
|Správa adresářů|Získání objektu tenantDomains|
|Klíč|Získá typ tenanta|
|Klíč|Ověření povolení funkce B2C|
|Klíč|Ověřte, jestli je funkce povolená.|
|Klíč|Přidání partnera ke společnosti|
|Klíč|Přidání neověřené domény|
|Klíč|Přidání ověřené domény|
|Klíč|Vytvoření společnosti|
|Klíč|Vytvoření nastavení společnosti|
|Klíč|Odstranění nastavení společnosti|
|Klíč|Snížení úrovně partnera|
|Klíč|Adresář je odstraněný|
|Jiné|Adresář je trvale odstraněný|
|Jiné|Adresář s naplánovaným odstraněním|
|Prostředek|Propagace společnosti u partnera|
|Prostředek|Vyprázdnění vlastností Rights Management|
|Prostředek|Odebrání partnera ze společnosti|
|Prostředek|Odebrání neověřené domény|
|Prostředek|Odebrání ověřené domény|
|Prostředek|Nastavení informací o společnosti|
|Prostředek|Nastavení funkce DirSync|
|Prostředek|Nastavení příznaku DirSyncEnabled|
|Prostředek|Nastavení partnerství|
|Prostředek|Nastavení prahové hodnoty náhodného odstranění|
|Prostředek|Nastavení povoleného umístění dat společnosti|
|Prostředek|Povolení vícejazykové funkce společnosti|
|Prostředek|Nastavení funkce adresáře v tenantovi|
|Prostředek|Nastavení doménového ověřování|
|Prostředek|Nastavení federování v doméně|
|Prostředek|Nastavení zásad hesel|
|Prostředek|Nastavení vlastností Rights Management|
|Prostředek|Aktualizace společnosti|
|Prostředek|Aktualizace nastavení společnosti|
|Prostředek|Aktualizace domény|
|Prostředek|Ověření domény|
|Prostředek|Ověření domény s e-mailovým ověřením|
|Prostředek|Onboarding|
|Prostředek|Aktualizace nastavení výstrah|
|Prostředek|Aktualizace nastavení týdenního přehledu|
|Prostředek|Zákaz zpětného zápisu hesel pro adresář|
|Prostředek|Povolení zpětného zápisu hesel pro adresář|
|Prostředek|Přidání přiřazení role aplikace ke skupině|
|Prostředek|Přidání skupiny|
|Prostředek|Přidání člena do skupiny|
|Prostředek|Přidání vlastníka do skupiny|
|Prostředek|Vytvoření nastavení skupin|
|Prostředek|Odstranění skupiny|
|Prostředek|Odstranění nastavení skupin|
|Prostředek|Ukončení použití skupinové licence pro uživatele|
|Prostředek|Trvalé odstranění skupiny|
|Prostředek|Odebrání přiřazení role aplikace ze skupiny|
|Prostředek|Odebrání člena ze skupiny|
|Prostředek|Odebrání vlastníka ze skupiny|
|Prostředek|Obnovení skupiny|
|Prostředek|Nastavení skupinové licence|
|Prostředek|Nastavení správy skupiny uživatelem|
|Prostředek|Zahájení použití skupinové licence pro uživatele|
|Prostředek|Aktivace přepočtu skupinové licence|
|Prostředek|Aktualizace skupiny|
|Prostředek|Aktualizace nastavení skupin|
|Prostředek|Přidání člena|
|Prostředek|Vytvoření skupiny|
|Prostředek|Odstranění skupiny|
|Prostředek|Odebrání člena|
|Prostředek|Aktualizace skupiny|
|Prostředek|Schválení čekající žádosti o připojení ke skupině|
|Prostředek|Zrušení čekající žádosti o připojení ke skupině|
|Prostředek|Vytvoření zásad správy životního cyklu|
|Prostředek|Odstranění čekající žádosti o připojení ke skupině|
|Prostředek|Odmítnutí čekající žádosti o připojení ke skupině|
|Prostředek|Obnovení skupiny|
|Prostředek|Žádost o připojení ke skupině|
|Prostředek|Nastavení vlastností dynamických skupin|
|Prostředek|Aktualizace zásad správy životního cyklu|
|Prostředek|Přidání klíče založeného na tajném klíči ASCII do kontejneru klíčů CPIM|
|Prostředek|Přidání klíče do kontejneru klíčů CPIM|
|Prostředek|Odstranění kontejneru klíčů CPIM|
|Prostředek|Odstranění kontejneru klíčů|
|Prostředek|Získání metadat aktivního klíče kontejneru klíčů ve formátu JWK|
|Prostředek|Získání metadat kontejneru klíčů|
|Prostředek|Získá kontejner klíčů CPIM ve formátu JWK|
|Prostředek|Získá seznam kontejnerů klíčů v tenantovi|
|Prostředek|Obnovení zálohy kontejneru klíčů CPIM|
|Prostředek|Uložení kontejneru klíčů|
|Prostředek|Nahrání šifrovaného klíče CPIM|
|Prostředek|Vydání ověřovacího kódu pro aplikaci|
|Prostředek|Vydání tokentu id_token pro aplikaci|


## <a name="core-directory"></a>Základní adresář

|Kategorie auditu|Aktivita|
|---|---|
|Správa jednotek pro správu|Stažení jediného typu detekce rizika|
|Správa jednotek pro správu|Stažení správců a stavu týdenního přehledu vyjádření souhlasu|
|Správa jednotek pro správu|Stáhnout všechny typy detekce rizik|
|Správa jednotek pro správu|Stáhnout zjišťování rizik uživatelů zdarma|
|Správa jednotek pro správu|Stažení uživatelů označených příznakem rizika|
|Správa aplikací|Zpracované hromadné pozvánky|
|Správa aplikací|Odeslané hromadné pozvánky|
|Správa aplikací|Přidání vlastníka k zásadám|
|Správa aplikací|Přidání zásad|
|Správa aplikací|Odstranění zásad|
|Správa aplikací|Odebrání přihlašovacích údajů zásad|
|Správa aplikací|Aktualizace zásad|
|Správa aplikací|Nastavení zásad registrace MFA|
|Správa aplikací|Nastavení zásad rizika přihlašování|
|Správa aplikací|Nastavení zásad rizika uživatele|
|Správa aplikací|Přijetí podmínek použití|
|Správa aplikací|Vytvoření podmínek použití|
|Správa aplikací|Odmítnutí podmínek použití|
|Správa aplikací|Odstranění podmínek použití|
|Správa aplikací|Úprava podmínky použití|
|Správa aplikací|Publikování podmínek použití|
|Správa aplikací|Zrušení publikování podmínek použití|
|Správa aplikací|Přidat certifikát TLS/SSL aplikace|
|Správa aplikací|Odstranit vazbu TLS|
|Správa aplikací|Registrace konektoru|
|Správa aplikací|AdminPolicyDatas-RemoveResources|
|Správa aplikací|AdminPolicyDatas-SetResources|
|Správa aplikací|AdminUserJourneys-GetResources|
|Správa adresářů|AdminUserJourneys-RemoveResources|
|Správa adresářů|AdminUserJourneys-SetResources|
|Správa adresářů|Create IdentityProvider|
|Správa adresářů|Vytvoření nového objektu AdminUserJourney|
|Správa adresářů|Vytvoření kódu JSON lokalizovaného prostředku|
|Správa adresářů|Vytvoření nového vlastního zprostředkovatele identity|
|Správa adresářů|Vytvoření nového zprostředkovatele identity|
|Správa adresářů|Vytvoření nebo aktualizace prostředku adresáře B2C|
|Správa adresářů|Vytvoření zásad|
|Správa adresářů|Vytvoření zásad trustFramework|
|Správa adresářů|Vytvoření zásad trustFramework s konfigurovatelnou předponou|
|Správa adresářů|Vytvoření atributu uživatele|
|Správa adresářů|CreateTrustFrameworkPolicy|
|Správa adresářů|Odstranění zprostředkovatele identity|
|Správa adresářů|Vytvoření objektu IdentityProvider|
|Správa adresářů|Odstranění prostředku adresáře B2C|
|Správa adresářů|Odstranění zásad trustFramework|
|Správa adresářů|Odstranění atributu uživatele|
|Správa adresářů|Získání prostředků adresáře B2C ve skupině prostředků|
|Správa adresářů|Získání prostředků adresáře B2C v předplatném|
|Správa adresářů|Získání vlastního zprostředkovatele identity|
|Správa adresářů|Získání zprostředkovatele identity|
|Správa adresářů|Získat prostředek adresáře B2C|
|Správa adresářů|Získání cesty uživatele|
|Správa adresářů|Získání povolených deklarací identity aplikace pro cestu uživatele|
|Správa adresářů|Získání povolených deklarací identity s vlastním potvrzením pro cestu uživatele|
|Správa adresářů|Získání povolených deklarací identity s vlastním potvrzením pro zásady|
|Správa adresářů|Získání seznamu dostupných výstupních deklarací identity|
|Správa adresářů|Získání definic obsahu pro cestu uživatele|
|Správa adresářů|Získání zprostředkovatelů identity pro konkrétní tok správy|
|Správa adresářů|Získání seznamu všech toků správy|
|Správa adresářů|Získání seznamu značek pro všechny toky správy všech uživatelů|
|Správa skupin|Členové skupiny hromadného stahování – zahájeno|
|Správa skupin|Členové skupiny hromadného stahování – dokončeno|
|Správa skupin|Členové skupiny hromadného importu – spuštěno|
|Správa skupin|Členové skupiny hromadného importu – dokončeno|
|Správa skupin|Hromadné odebrání členů skupiny – zahájeno|
|Správa skupin|Hromadné odebrání členů skupiny – dokončeno|
|Správa skupin|Hromadné stažení skupin – zahájeno|
|Správa skupin|Hromadné stažení skupin – dokončeno|
|Správa skupin|Získání seznamu tenantů pro uživatele|
|Správa skupin|Získání deklarací identity s vlastním potvrzením pro místní účty|
|Správa skupin|Získání kódu JSON lokalizovaného prostředku|
|Správa skupin|Získání operací poskytovatele prostředků Microsoft.AzureActiveDirectory|
|Správa skupin|Získání zásad|
|Správa skupin|Získání zásady|
|Správa skupin|Získání seznamu podporovaných zprostředkovatelů identity|
|Správa skupin|Získání seznamu podporovaných zprostředkovatelů identity cesty uživatele|
|Správa skupin|Získání seznamu vlastních zprostředkovatelů identity definovaných v tenantovi|
|Správa skupin|Získání seznamu zprostředkovatelů identity definovaných v tenantovi|
|Správa skupin|Získání seznamu místních zprostředkovatelů identity definovaných v tenantovi|
|Správa skupin|Získání podrobností o tenantovi pro uživatele pro vytvoření prostředku|
|Správa skupin|Získání výchozí podporované kultury pro CPIM|
|Správa skupin|Získání podrobností o taku správy|
|Správa skupin|Získání seznamu objektů UserJourney pro tohoto tenanta|
|Správa skupin|Získání sady dostupných podporovaných kultur pro CPIM|
|Správa skupin|Získání zásad trustFramework|
|Správa skupin|Získání zásad trustFramework ve formátu XML|
|Správa skupin|Získání atributu uživatele|
|Správa zásad|Získání atributů uživatele|
|Správa zásad|Získání seznamu cest uživatele|
|Správa zásad|GetIEFPolicies|
|Správa zásad|GetIdentityProviders|
|Správa zásad|GetTrustFrameworkPolicy|
|Prostředek|MigrateTenantMetadata|
|Prostředek|Přesunutí prostředků|
|Prostředek|Oprava objektu IdentityProvider|
|Prostředek|PutTrustFrameworkPolicy|
|Prostředek|PutTrustFrameworkpolicy|
|Prostředek|Odebrání cesty uživatele|
|Prostředek|Aktualizace vlastního zprostředkovatele identity|
|Prostředek|Aktualizace zprostředkovatele identity|
|Prostředek|Aktualizace místního zprostředkovatele identity|
|Prostředek|Aktualizace prostředku adresáře B2C|
|Prostředek|Aktualizace zásad|
|Prostředek|Aktualizace stavu předplatného|
|Správa rolí|Aktualizace atributu uživatele|
|Správa rolí|Ověření přesunu prostředků|
|Správa rolí|Přidání zařízení|
|Správa rolí|Přidání konfigurace zařízení|
|Správa rolí|Přidání registrovaného vlastníka zařízení|
|Správa rolí|Přidání registrovaných uživatelů zařízení|
|Správa rolí|Odstranění zařízení|
|Správa rolí|Odstranění konfigurace zařízení|
|Správa rolí|Zařízení už nevyhovuje předpisům|
|Správa rolí|Zařízení už není spravované|
|Správa uživatelů|AccessReview_Review|
|Správa uživatelů|AccessReview_Update|
|Správa uživatelů|ActivationAborted|
|Správa uživatelů|ActivationApproved|
|Správa uživatelů|ActivationCanceled|
|Správa uživatelů|ActivationRequested|
|Správa uživatelů|Přidání oprávněného člena do role|
|Správa uživatelů|Přidání člena do role|
|Správa uživatelů|Přidání přiřazení role do definice role|
|Správa uživatelů|Přidání role ze šablony|
|Správa uživatelů|Přidání vymezeného člena do role|
|Správa uživatelů|Přidáno|
|Správa uživatelů|Přiřadit|
|Správa uživatelů|Hromadné vytvoření uživatelů – spuštěné|
|Správa uživatelů|Hromadné vytvoření uživatelů – dokončeno|
|Správa uživatelů|Hromadné odstranění uživatelů – spuštěné|
|Správa uživatelů|Hromadné odstranění uživatelů – dokončeno|
|Správa uživatelů|Uživatelé hromadného stahování – spuštěno|
|Správa uživatelů|Uživatelé hromadného stahování – dokončeno|
|Správa uživatelů|Hromadné obnovení odstraněných uživatelů-zahájeno|
|Správa uživatelů|Hromadné obnovení odstraněných uživatelů – dokončeno|
|Správa uživatelů|Uživatelé hromadného pozvání – spuštěno|
|Správa uživatelů|Hromadná Pozvánka uživatelů – dokončeno|
|Správa uživatelů|Odebrání registrovaného vlastníka ze zařízení|
|Správa uživatelů|Odebrání registrovaných uživatelů ze zařízení|
|Správa uživatelů|Odebrání oprávněného člena z role|
|Správa uživatelů|Odebrání člena z role|
|Správa uživatelů|Odebrání přiřazení role z definice role|
|Správa uživatelů|Odebrání vymezeného člena z role|
|Správa uživatelů|Aktualizace zařízení|
|Správa uživatelů|Aktualizace konfigurace zařízení|
|Správa uživatelů|Aktualizace role|






## <a name="identity-protection"></a>Ochrana identit

|Kategorie auditu|Aktivita|
|---|---|
|Správa adresářů|Zvýšení oprávnění|
|Správa adresářů|Odebráno|
|Správa adresářů|Změny nastavení role|
|Jiné|ScanAlertsNow|
|Jiné|Registrace|
|Jiné|Pozastavení|
|Jiné|UpdateAlertSettings|
|Jiné|UpdateCurrentState|
|Správa zásad|Ukončení kontroly přístupu|
|Správa zásad|Přidání schvalovatele k žádosti o schválení|
|Správa zásad|Přidání revidujícího ke kontrole přístupu|
|Správa uživatelů|Použití kontroly přístupu|
|Správa uživatelů|Vytvoření kontroly přístupu|


## <a name="invited-users"></a>Pozvaní uživatelé

|Kategorie auditu|Aktivita|
|---|---|
|Jiné|Vytvoření žádosti o schválení|
|Jiné|Odstranění kontroly přístupu|
|Správa uživatelů|Odebrání revidujícího z kontroly přístupu|
|Správa uživatelů|Žádost o použití výsledku kontroly|
|Správa uživatelů|Žádost a zastavení kontroly|
|Správa uživatelů|Kontrola přiřazení aplikace|
|Správa uživatelů|Kontrola členství ve skupinách|
|Správa uživatelů|Kontrola členství v rolích RBAC|


## <a name="microsoft-identity-manager-mim"></a>Microsoft Identity Manager (MIM)

|Kategorie auditu|Aktivita|
|---|---|
|Správa skupin|Kontrola žádosti o schválení žádosti|
|Správa skupin|Aktualizace kontroly přístupu|
|Správa skupin|Aktualizace nastavení e-mailového oznámení o kontrole přístupu|
|Správa skupin|Nastavení aktualizace počtu opakování kontroly přístupu|
|Správa skupin|Nastavení aktualizace doby trvání opakování kontrol přístupu ve dnech|
|Správa uživatelů|Nastavení typu konce opakování kontroly přístupu aktualizace|
|Správa uživatelů|Nastavení aktualizace typu opakování kontroly přístupu|



## <a name="privileged-identity-management"></a>Privileged Identity Management

|Kategorie auditu|Aktivita|
|---|---|
|PIM|ActivationAborted|
|PIM|ActivationApproved|
|PIM|ActivationCanceled|
|PIM|ActivationDenied|
|PIM|ActivationRequested|
|PIM|Přidáno|
|PIM|AddedOutsidePIM|
|PIM|Přiřadit|
|PIM|DismissAlert|
|PIM|Zvýšení oprávnění|
|PIM|ReactivateAlert|
|PIM|Odebráno|
|PIM|RemovedOutsidePIM|
|PIM|Žádost a zastavení kontroly|
|PIM|Změny nastavení role|
|PIM|ScanAlertsNow|
|PIM|Registrace|
|PIM|Zrušit přiřazení|
|PIM|Pozastavení|
|PIM|UpdateAlertSettings|
|PIM|UpdateCurrentState|


## <a name="self-service-group-management"></a>Samoobslužná správa skupin

|Kategorie auditu|Aktivita|
|---|---|
|Správa skupin|Resetování hesla uživatele|
|Správa skupin|Obnovení uživatele|
|Správa skupin|Nastavení vynucené změny hesla uživatele|
|Správa skupin|Nastavení správce uživatelů|
|Správa skupin|Nastavení povolení metadat tokenu OAuth uživatele|
|Správa skupin|Aktualizace časového razítka StsRefreshTokenValidFrom|
|Správa skupin|Aktualizace externích tajných klíčů|
|Správa skupin|Aktualizace uživatele|
|Správa skupin|Správce generuje dočasné heslo|


## <a name="self-service-password-management"></a>Samoobslužná správa hesel

|Kategorie auditu|Aktivita|
|---|---|
|Správa adresářů|Správce vyžaduje resetování hesla uživatelem|
|Správa adresářů|Přiřazení externího uživatele k aplikaci|
|Správa uživatelů|E-mail se neodeslal, uživatel zrušil odběr|
|Správa uživatelů|Pozvání externího uživatele|
|Správa uživatelů|Uplatnění pozvání externího uživatele|
|Správa uživatelů|Vytvoření virálního tenanta|
|Správa uživatelů|Vytvoření virálního uživatele|
|Správa uživatelů|Registrace hesla uživatele|
|Správa uživatelů|Resetování hesla uživatele|
|Správa uživatelů|Blokování samoobslužného resetování hesla|


## <a name="terms-of-use"></a>Podmínky použití

|Kategorie auditu|Aktivita|
|---|---|
|Podmínky použití|Přijetí podmínek použití|
|Podmínky použití|Vytvoření podmínek použití|
|Podmínky použití|Odmítnutí podmínek použití|
|Podmínky použití|Odstranit souhlas|
|Podmínky použití|Odstranění podmínek použití|
|Podmínky použití|Úprava podmínky použití|
|Podmínky použití|Vypršení podmínek použití|
|Podmínky použití|Pevné odstranění podmínek použití|
|Podmínky použití|Publikování podmínek použití|
|Podmínky použití|Zrušení publikování podmínek použití|


## <a name="next-steps"></a>Další kroky

- [Přehled sestav Azure AD](overview-reports.md).
- [Sestava protokolů auditu](concept-audit-logs.md). 
- [Programový přístup k sestavám Azure AD](concept-reporting-api.md)