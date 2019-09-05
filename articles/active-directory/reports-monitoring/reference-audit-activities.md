---
title: Referenční informace k aktivitám auditování Azure Active Directory (Azure AD) | Microsoft Docs
description: Získejte přehled o aktivitách auditování, které můžete protokolovat v protokolech auditu v Azure Active Directory (Azure AD).
services: active-directory
documentationcenter: ''
author: cawrites
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
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: aaa60a7737b7781a21e23516d139332f10bdf448
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306011"
---
# <a name="azure-ad-audit-activity-reference"></a>Referenční informace k aktivitám auditování Azure AD

Pomocí sestav Azure Active Directory (Azure AD) můžete získat informace, které potřebujete k určení toho, jak vaše prostředí dělá.

Architektura generování sestav v Azure AD se skládá z následujících komponent:

- **Sestavy aktivit** 
    - [Přihlášení](concept-sign-ins.md) – poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů.
    - [Protokoly auditu](concept-audit-logs.md) – Zajišťuje sledovatelnost prostřednictvím protokolů všech změn provedených různými funkcemi v rámci Azure AD. 
    
- **Sestavy zabezpečení** 
    - [Riziková přihlášení](concept-risky-sign-ins.md) –Rizikové přihlášení je indikátorem pokusu o přihlášení, který mohl provést někdo, kdo není legitimním vlastníkem uživatelského účtu. 
    - [Uživatelé označení příznakem rizika](concept-user-at-risk.md) – Rizikový uživatel je indikátorem uživatelského účtu, který mohl být ohrožený. 

Tento článek obsahuje seznam aktivit auditování, které můžete protokolovat v protokolech auditu.

## <a name="access-reviews"></a>Kontroly přístupu

|Kategorie auditu|Aktivita|
|---|---|
|Kontroly přístupu|Ukončení kontroly přístupu|
|Kontroly přístupu|Přidání schvalovatele k žádosti o schválení|
|Kontroly přístupu|Přidání revidujícího ke kontrole přístupu|
|Kontroly přístupu|Použít kontrolu přístupu|
|Kontroly přístupu|Vytvořit kontrolu přístupu|
|Kontroly přístupu|Vytvoření programu|
|Kontroly přístupu|Vytvoření žádosti o schválení|
|Kontroly přístupu|Odstranit kontrolu přístupu|
|Kontroly přístupu|Odstranění programu|
|Kontroly přístupu|Připojení k řízení aplikací|
|Kontroly přístupu|Registrace ke kontrolám přístupu Azure AD|
|Kontroly přístupu|Odebrání revidujícího z kontroly přístupu|
|Kontroly přístupu|Žádost a zastavení kontroly|
|Kontroly přístupu|Požádat o použití výsledků kontroly|
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
|Kontroly přístupu|Aktualizovat nastavení připomenutí přístupu k revizi|
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
|Správa aplikací|Přidat aplikaci|
|Správa aplikací|Přidání vlastníka do aplikace|
|Správa aplikací|Přidání vlastníka k instančnímu objektu|
|Správa aplikací|Přidání zásad k instančnímu objektu|
|Správa adresářů|Přidání instančního objektu|
|Správa adresářů|Přidání přihlašovacích údajů instančního objektu|
|Správa adresářů|Souhlas s aplikací|
|Správa adresářů|Odstranit aplikaci|
|Správa adresářů|Trvalé odstranění aplikace|
|Správa adresářů|Odebrání OAuth2PermissionGrant|
|Správa adresářů|Odebrání přiřazení role aplikace z instančního objektu|
|Správa adresářů|Odebrání vlastníka z aplikace|
|Resource|Odebrání vlastníka z instančního objektu|
|Resource|Odebrání zásad z instančního objektu|
|Resource|Odebrání instančního objektu|


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
|Ověřování|AdminUserJourneys-SetResources|
|Ověřování|Create IdentityProvider|
|Ověřování|Vytvoření aplikace V1|
|Ověřování|Vytvoření aplikace V2|
|Ověřování|Vytvoření vlastních domén v tenantovi|
|Authorization|Vytvoření nového objektu AdminUserJourney|
|Authorization|Vytvoření kódu JSON lokalizovaného prostředku|
|Authorization|Vytvoření nového vlastního zprostředkovatele identity|
|Authorization|Vytvoření nového zprostředkovatele identity|
|Authorization|Vytvoření nebo aktualizace prostředku adresáře B2C|
|Authorization|Vytvořit zásadu|
|Authorization|Vytvoření zásad trustFramework|
|Authorization|Vytvoření zásad trustFramework s konfigurovatelnou předponou|
|Authorization|Vytvoření atributu uživatele|
|Authorization|CreateTrustFrameworkPolicy|
|Authorization|Vytvoří nebo aktualizuje nový objekt AdminUserJourney|
|Authorization|Odstranění zprostředkovatele identity|
|Authorization|Vytvoření objektu IdentityProvider|
|Authorization|Odstranění aplikace V1|
|Authorization|Odstranění aplikace V2|
|Authorization|Odstranění udělení oprávnění aplikací V2|
|Authorization|Odstranění prostředku adresáře B2C|
|Authorization|Odstranění kontejneru klíčů CPIM|
|Authorization|Odstranění zásad trustFramework|
|Authorization|Odstranění atributu uživatele|
|Authorization|Povolení funkce B2C|
|Authorization|Získání prostředků adresáře B2C v předplatném|
|Authorization|Získání vlastního zprostředkovatele identity|
|Authorization|Získání zprostředkovatele identity|
|Authorization|Získání aplikací V1 a V2|
|Authorization|Získání aplikace V1|
|Authorization|Získání aplikací V1|
|Authorization|Získání aplikace V2|
|Authorization|Získání aplikací V2|
|Authorization|Získat prostředek adresáře B2C|
|Authorization|Získání seznamu vlastních domén v tenantovi|
|Authorization|Získání cesty uživatele|
|Authorization|Získání povolených deklarací identity aplikace pro cestu uživatele|
|Authorization|Získání povolených deklarací identity s vlastním potvrzením pro cestu uživatele|
|Authorization|Získání povolených deklarací identity s vlastním potvrzením pro zásady|
|Authorization|Získání seznamu dostupných výstupních deklarací identity|
|Authorization|Získání definic obsahu pro cestu uživatele|
|Authorization|Získání zprostředkovatelů identity pro konkrétní tok správy|
|Authorization|Získání metadat aktivního klíče kontejneru klíčů ve formátu JWK|
|Authorization|Získání seznamu všech toků správy|
|Authorization|Získání seznamu značek pro všechny toky správy všech uživatelů|
|Authorization|Získání seznamu tenantů pro uživatele|
|Authorization|Získání deklarací identity s vlastním potvrzením pro místní účty|
|Authorization|Získání kódu JSON lokalizovaného prostředku|
|Authorization|Získání operací poskytovatele prostředků Microsoft.AzureActiveDirectory|
|Authorization|Získání zásad|
|Authorization|Získání zásady|
|Authorization|Získání vlastností prostředku tenanta|
|Authorization|Získání seznamu podporovaných zprostředkovatelů identity|
|Authorization|Získání seznamu podporovaných zprostředkovatelů identity cesty uživatele|
|Authorization|Získání informací o tenantovi|
|Authorization|Získání povolených funkcí tenanta|
|Authorization|Získání seznamu vlastních zprostředkovatelů identity definovaných v tenantovi|
|Authorization|Získání seznamu zprostředkovatelů identity definovaných v tenantovi|
|Authorization|Získání seznamu místních zprostředkovatelů identity definovaných v tenantovi|
|Authorization|Získání podrobností o tenantovi pro uživatele pro vytvoření prostředku|
|Authorization|Získání seznamu tenantů|
|Authorization|Získání objektu tenantDomains|
|Authorization|Získání výchozí podporované kultury pro CPIM|
|Authorization|Získání podrobností o toku správy|
|Authorization|Získání seznamu objektů UserJourney pro tohoto tenanta|
|Authorization|Získání sady dostupných podporovaných kultur pro CPIM|
|Authorization|Získání zásad trustFramework|
|Authorization|Získání zásad trustFramework ve formátu XML|
|Authorization|Získání atributu uživatele|
|Authorization|Získání atributů uživatele|
|Authorization|Získání seznamu cest uživatele|
|Authorization|GetIEFPolicies|
|Authorization|GetIdentityProviders|
|Authorization|GetTrustFrameworkPolicy|
|Authorization|Získá kontejner klíčů CPIM ve formátu JWK|
|Authorization|Získá seznam kontejnerů klíčů v tenantovi|
|Authorization|Získá typ tenanta|
|Authorization|MigrateTenantMetadata|
|Authorization|Oprava objektu IdentityProvider|
|Authorization|PutTrustFrameworkPolicy|
|Authorization|PutTrustFrameworkpolicy|
|Authorization|Odebrání cesty uživatele|
|Authorization|Obnovení zálohy kontejneru klíčů CPIM|
|Authorization|Načtení udělení oprávnění aplikace V2|
|Authorization|Načtení instančních objektů aplikace V2 v aktuálním tenantovi|
|Authorization|Aktualizace vlastního zprostředkovatele identity|
|Authorization|Aktualizace zprostředkovatele identity|
|Authorization|Aktualizovat místní IDP|
|Authorization|Aktualizace aplikace V1|
|Authorization|Aktualizace aplikace V2|
|Authorization|Aktualizace udělení oprávnění aplikace V2|
|Authorization|Aktualizace zásady|
|Authorization|Aktualizace atributu uživatele|
|Authorization|Nahrání šifrovaného klíče CPIM|
|Authorization|Autorizace uživatele: Rozhraní API je pro tenanta tenanta zakázané.|
|Authorization|Autorizace uživatele: Uživateli byl udělen přístup jako oprávnění tenant admin.|
|Authorization|Autorizace uživatele: Uživateli byla udělena přístupová práva ověřeného uživatele.|
|Authorization|Ověření povolení funkce B2C|
|Authorization|Ověřte, jestli je funkce povolená.|
|Authorization|Vytvoření programu|
|Authorization|Odstranění programu|
|Authorization|Připojení k řízení aplikací|
|Authorization|Registrace ke kontrolám přístupu Azure AD|
|Authorization|Zrušení připojení k řízení aplikací|
|Authorization|Aktualizace programu|
|Authorization|Zákaz jednotného přihlašování na počítači|
|Authorization|Zákaz jednotného přihlašování na počítači pro konkrétní doménu|
|Authorization|Zakázat Proxy aplikací|
|Authorization|Zákaz předávacího ověřování|
|Authorization|Povolení jednotného přihlašování na počítači|
|Správa adresářů|Povolení jednotného přihlašování na počítači pro konkrétní doménu|
|Správa adresářů|Povolit Proxy aplikací|
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
|Ostatní|Adresář je trvale odstraněný|
|Ostatní|Adresář s naplánovaným odstraněním|
|Resource|Propagace společnosti u partnera|
|Resource|Vyprázdnění vlastností Rights Management|
|Resource|Odebrání partnera ze společnosti|
|Resource|Odebrání neověřené domény|
|Resource|Odebrání ověřené domény|
|Resource|Nastavení informací o společnosti|
|Resource|Nastavení funkce DirSync|
|Resource|Nastavení příznaku DirSyncEnabled|
|Resource|Nastavení partnerství|
|Resource|Nastavení prahové hodnoty náhodného odstranění|
|Resource|Nastavení povoleného umístění dat společnosti|
|Resource|Povolení vícejazykové funkce společnosti|
|Resource|Nastavení funkce adresáře v tenantovi|
|Resource|Nastavení doménového ověřování|
|Resource|Nastavení federování v doméně|
|Resource|Nastavení zásad hesel|
|Resource|Nastavení vlastností Rights Management|
|Resource|Aktualizace společnosti|
|Resource|Aktualizace nastavení společnosti|
|Resource|Aktualizovat doménu|
|Resource|Ověřit doménu|
|Resource|Ověření domény s e-mailovým ověřením|
|Resource|Připojování|
|Resource|Aktualizovat nastavení výstrah|
|Resource|Aktualizace nastavení týdenního přehledu|
|Resource|Zákaz zpětného zápisu hesel pro adresář|
|Resource|Povolení zpětného zápisu hesel pro adresář|
|Resource|Přidání přiřazení role aplikace ke skupině|
|Resource|Přidat skupinu|
|Resource|Přidá člena do skupiny|
|Resource|Přidání vlastníka do skupiny|
|Resource|Vytvoření nastavení skupin|
|Resource|Odstranit skupinu|
|Resource|Odstranění nastavení skupin|
|Resource|Ukončení použití skupinové licence pro uživatele|
|Resource|Trvalé odstranění skupiny|
|Resource|Odebrání přiřazení role aplikace ze skupiny|
|Resource|Odebrat člena ze skupiny|
|Resource|Odebrání vlastníka ze skupiny|
|Resource|Obnovení skupiny|
|Resource|Nastavení skupinové licence|
|Resource|Nastavení správy skupiny uživatelem|
|Resource|Zahájení použití skupinové licence pro uživatele|
|Resource|Aktivace přepočtu skupinové licence|
|Resource|Aktualizace skupiny|
|Resource|Aktualizace nastavení skupin|
|Resource|Přidat člena|
|Resource|Vytvořit skupinu|
|Resource|Odstranit skupinu|
|Resource|Odebrat člena|
|Resource|Aktualizovat skupinu|
|Resource|Schválení čekající žádosti o připojení ke skupině|
|Resource|Zrušení čekající žádosti o připojení ke skupině|
|Resource|Vytvoření zásad správy životního cyklu|
|Resource|Odstranění čekající žádosti o připojení ke skupině|
|Resource|Odmítnutí čekající žádosti o připojení ke skupině|
|Resource|Prodloužit platnost skupiny|
|Resource|Žádost o připojení ke skupině|
|Resource|Nastavení vlastností dynamických skupin|
|Resource|Aktualizace zásad správy životního cyklu|
|Resource|Přidání klíče založeného na tajném klíči ASCII do kontejneru klíčů CPIM|
|Resource|Přidání klíče do kontejneru klíčů CPIM|
|Resource|Odstranění kontejneru klíčů CPIM|
|Resource|Odstranění kontejneru klíčů|
|Resource|Získání metadat aktivního klíče kontejneru klíčů ve formátu JWK|
|Resource|Získání metadat kontejneru klíčů|
|Resource|Získá kontejner klíčů CPIM ve formátu JWK|
|Resource|Získá seznam kontejnerů klíčů v tenantovi|
|Resource|Obnovení zálohy kontejneru klíčů CPIM|
|Resource|Uložení kontejneru klíčů|
|Resource|Nahrání šifrovaného klíče CPIM|
|Resource|Vydání ověřovacího kódu pro aplikaci|
|Resource|Vydání tokentu id_token pro aplikaci|


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
|Správa aplikací|Přidat zásady|
|Správa aplikací|Odstranit zásadu|
|Správa aplikací|Odebrání přihlašovacích údajů zásad|
|Správa aplikací|Aktualizace zásady|
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
|Správa aplikací|Přidání certifikátu SSL aplikace|
|Správa aplikací|Odstranění vazby SSL|
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
|Správa adresářů|Vytvořit zásadu|
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
|Správa skupin|Získání podrobností o toku správy|
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
|Resource|MigrateTenantMetadata|
|Resource|Přesunout prostředky|
|Resource|Oprava objektu IdentityProvider|
|Resource|PutTrustFrameworkPolicy|
|Resource|PutTrustFrameworkpolicy|
|Resource|Odebrání cesty uživatele|
|Resource|Aktualizace vlastního zprostředkovatele identity|
|Resource|Aktualizace zprostředkovatele identity|
|Resource|Aktualizovat místní IDP|
|Resource|Aktualizace prostředku adresáře B2C|
|Resource|Aktualizace zásady|
|Resource|Aktualizace stavu předplatného|
|Správa rolí|Aktualizace atributu uživatele|
|Správa rolí|Ověření přesunu prostředků|
|Správa rolí|Přidání zařízení|
|Správa rolí|Přidání konfigurace zařízení|
|Správa rolí|Přidání registrovaného vlastníka zařízení|
|Správa rolí|Přidání registrovaných uživatelů zařízení|
|Správa rolí|Odstranit zařízení|
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
|Správa uživatelů|Odebrat člena z role|
|Správa uživatelů|Odebrání přiřazení role z definice role|
|Správa uživatelů|Odebrání vymezeného člena z role|
|Správa uživatelů|Aktualizovat zařízení|
|Správa uživatelů|Aktualizace konfigurace zařízení|
|Správa uživatelů|Aktualizace role|






## <a name="identity-protection"></a>Ochrana identit

|Kategorie auditu|Aktivita|
|---|---|
|Správa adresářů|Zvýšit|
|Správa adresářů|Odebráno|
|Správa adresářů|Změny nastavení role|
|Ostatní|ScanAlertsNow|
|Ostatní|Registrace|
|Ostatní|Pozastavení|
|Ostatní|UpdateAlertSettings|
|Ostatní|UpdateCurrentState|
|Správa zásad|Ukončení kontroly přístupu|
|Správa zásad|Přidání schvalovatele k žádosti o schválení|
|Správa zásad|Přidání revidujícího ke kontrole přístupu|
|Správa uživatelů|Použít kontrolu přístupu|
|Správa uživatelů|Vytvořit kontrolu přístupu|


## <a name="invited-users"></a>Pozvaní uživatelé

|Kategorie auditu|Aktivita|
|---|---|
|Ostatní|Vytvoření žádosti o schválení|
|Ostatní|Odstranit kontrolu přístupu|
|Správa uživatelů|Odebrání revidujícího z kontroly přístupu|
|Správa uživatelů|Požádat o použití výsledků kontroly|
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
|PIM|Zvýšit|
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
|Správa skupin|Obnovit uživatele|
|Správa skupin|Nastavení vynucené změny hesla uživatele|
|Správa skupin|Nastavení správce uživatelů|
|Správa skupin|Nastavení povolení metadat tokenu OAuth uživatele|
|Správa skupin|Aktualizace časového razítka StsRefreshTokenValidFrom|
|Správa skupin|Aktualizace externích tajných klíčů|
|Správa skupin|Aktualizuje uživatele|
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
