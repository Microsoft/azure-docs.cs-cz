---
title: Historie verzí služby Azure AD Connect Health
description: Tento dokument popisuje vydaná vydání pro Azure AD Connect Health a informace o tom, co bylo součástí těchto vydání.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 08/10/2020
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7653f850edc910fc78b14a628b87dcb22aeb903
ms.sourcegitcommit: c94a177b11a850ab30f406edb233de6923ca742a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89279410"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: Historie vydaných verzí
Tým Azure Active Directory pravidelně aktualizuje Azure AD Connect Health novými funkcemi a funkcemi. V tomto článku jsou uvedené verze a funkce, které byly vydány.  

> [!NOTE]
> Při vydání nové verze se automaticky aktualizují agenti stavu připojení. Ujistěte se prosím, že nastavení automatického upgradu je povolené z Azure Portal.
>

Azure AD Connect Health pro synchronizaci je integrována s instalací Azure AD Connect. Přečtěte si další informace o [Azure AD Connect historii verzí](./reference-connect-version-history.md) pro zpětnou vazbu k funkcím, Hlasujte na [telefonním kanálu uživatele Connect Health](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)

## <a name="april-2020"></a>Duben 2020
**Aktualizace agenta**

- Agent Azure AD Connect Health pro AD FS (verze 3.1.77.0)

   1.   Oprava chyby pro výstrahu "neplatný hlavní název služby (SPN) pro službu AD FS Service"), pro kterou byla výstraha nesprávně hlášena.


## <a name="july-2019"></a>Červenec 2019
**Aktualizace agenta**
* Agent Azure AD Connect Health pro AD FS (verze 3.1.59.0) 
   1. Změna textu v TestWindowsTransport
   2. Změny pro nahrání AD FS RP
   
* Agent Azure AD Connect Health pro AD FS (verze 3.1.56.0) 
   1. Přidat TestWindowsTransport test a odebrat kontroly koncových bodů WsTrust v testu CheckOffice365Endpoints
   2. Protokolování informací o operačním systému a .NET
   3. Zvyšte velikost nahrávání zprávy konfigurace RP na 1 MB.
   4. Opravy chyb
   
* Agent Azure AD Connect Health pro služba AD DS (verze 3.1.56.0) 
   1. Protokolování informací o operačním systému a .NET 
   2. Opravy chyb

## <a name="may-2019"></a>Květen 2019
**Aktualizace agenta:** 
* Agent Azure AD Connect Health pro AD FS (verze 3.1.51.0) 
   1. Oprava chyb pro rozlišení mezi několika znaménky, které sdílejí stejné ID klienta a požadavku.
   2. Oprava chyb pro analýzu chybných uživatelských jmen a hesel na lokalizovaných serverech jazyka   

## <a name="april-2019"></a>Duben 2019
**Aktualizace agenta:** 
* Agent Azure AD Connect Health pro AD FS (verze 3.1.46.0) 
   1. Opravit kontrolu duplicitního procesu výstrahy hlavního názvu služby pro ADFS

## <a name="march-2019"></a>Březen 2019
**Aktualizace agenta:** 
* Agent Azure AD Connect Health pro služba AD DS (verze 3.1.41.0)  
   1. Kolekce verzí rozhraní .NET
   2. Zlepšení shromažďování čítačů výkonu, když chybějí určité kategorie
   3. Oprava chyby při zablokování více instancí agenta monitorování

* Agent Azure AD Connect Health pro AD FS (verze 3.1.41.0) 
   1. Integrace a upgrade skriptů AD FS testů pomocí ADFSToolBox
   2. Implementovat kolekci verzí rozhraní .NET
   3. Zlepšení shromažďování čítačů výkonu, když chybějí určité kategorie
   4. Oprava chyby při zablokování více instancí agenta monitorování


## <a name="november-2018"></a>Listopad 2018
**Nové funkce GA:** 
* Azure AD Connect Health pro synchronizaci – Diagnostikujte a opravte chyby synchronizace duplicitních atributů z portálu.

**Aktualizace agenta:** 
* Agent Azure AD Connect Health pro služba AD DS (verze 3.1.24.0) 
   1. Dodržování předpisů a vynucování protokolu TLS (Transport Layer Security) verze 1,2
   2. Snížení šumu na výstrahách globálního katalogu
   3. Opravy chyb registrace agenta stavu

* Agent Azure AD Connect Health pro AD FS (verze 3.1.24.0)  
   1. Dodržování předpisů a vynucování protokolu TLS (Transport Layer Security) verze 1,2
   2. Podpora testu – ADFSRequestToken pro lokalizovaný operační systém
   3. Problém s vyřešeným uzamknutím agenta diagnostiky
   4. Opravy chyb registrace agenta stavu

## <a name="august-2018"></a>Srpen 2018 
*  Agent Azure AD Connect Health pro synchronizaci (verze 3.1.7.0) byl vydaný s Azure AD Connect verze 1.1.880.0.    
   1. Oprava hotfix pro [vysoký problém s procesorem agenta monitorování s .NET Framework vydáními KB](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>Červen 2018 
**Nové funkce ve verzi Preview:** 
* Azure AD Connect Health pro synchronizaci – Diagnostikujte a opravte chyby synchronizace duplicitních atributů z portálu. 

**Aktualizace agenta:** 
* Agent Azure AD Connect Health pro služba AD DS (verze 3.1.7.0)    
  1. Oprava hotfix pro [vysoký problém s procesorem agenta monitorování s .NET Framework vydáními KB](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
* Agent Azure AD Connect Health pro AD FS (verze 3.1.7.0)  
  1. Oprava hotfix pro [vysoký problém s procesorem agenta monitorování s .NET Framework vydáními KB](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
  2. Opravy výsledků testů na sekundárním serveru ADFS Server 2016
   
* Agent Azure AD Connect Health pro AD FS (verze 3.1.2.0)  
  1. Oprava hotfix pro správu paměti agenta a související výstrahy specificky pro 3.0.244.0 verze


## <a name="may-2018"></a>Květen 2018
**Aktualizace agenta:**
* Agent Azure AD Connect Health pro služba AD DS (verze 3.0.244.0)
  1. Vylepšení ochrany osobních údajů agenta  
  2. Opravy chyb a Obecná vylepšení

* Agent Azure AD Connect Health pro AD FS (verze 3.0.244.0)
  1. Vylepšení služby Agent Diagnostics a souvisejících modulů PowerShellu
  2. Vylepšení ochrany osobních údajů agenta  
  3. Opravy chyb a Obecná vylepšení

* Agent Azure AD Connect Health pro synchronizaci (verze 3.0.164.0) byl vydaný s Azure AD Connect verze 1.1.819.0. 
  1. Vylepšení ochrany osobních údajů agenta  
  2. Opravy chyb a Obecná vylepšení


## <a name="march-2018"></a>Březen 2018
**Nové funkce ve verzi Preview:**
* Azure AD Connect Health pro AD FS rizikové zprávy a výstrahy protokolu IP.

**Aktualizace agenta:**

* Agent Azure AD Connect Health pro služba AD DS (verze 3.0.176.0)
  1. Vylepšení dostupnosti agenta 
  2. Opravy chyb a Obecná vylepšení
* Agent Azure AD Connect Health pro AD FS (verze 3.0.176.0)
  1. Vylepšení dostupnosti agenta 
  2. Opravy chyb a Obecná vylepšení
* Agent Azure AD Connect Health pro synchronizaci (verze 3.0.129.0) byl vydaný s Azure AD Connect verze 1.1.750.0.  
  1. Vylepšení dostupnosti agenta 
  2. Opravy chyb a Obecná vylepšení

## <a name="december-2017"></a>Prosinec 2017
**Aktualizace agenta:**

* Agent Azure AD Connect Health pro služba AD DS (verze 3.0.145.0)
  1. Vylepšení dostupnosti agenta 
  2. Přidání nového příkazu pro řešení potíží s agenty
  3. Opravy chyb a Obecná vylepšení
* Agent Azure AD Connect Health pro AD FS (verze 3.0.145.0)
  1. Přidání nového příkazu pro řešení potíží s agenty
  2. Vylepšení dostupnosti agenta 
  3. Opravy chyb a Obecná vylepšení
  
## <a name="october-2017"></a>Říjen 2017
**Aktualizace agenta:**

 * Agent Azure AD Connect Health pro synchronizaci (verze 3.0.129.0) byl vydaný s Azure AD Connect verze 1.1.649.0.
<br></br> Opravili jsme problém s kompatibilitou verzí mezi Azure AD Connect a agentem Azure AD Connect Health pro synchronizaci. Tento problém má vliv na zákazníky, kteří provádějí Azure AD Connect místního upgradu na verzi 1.1.647.0, ale v současné době má agenta Health verze 3.0.127.0. Po upgradu již Agent stavu nebude moci odesílat data o stavu o službě Azure AD Connect Synchronization Service do služby Azure AD Health Service. S touto opravou se Agent stavu 3.0.129.0 nainstaluje během Azure AD Connect místního upgradu. Agent stavu verze 3.0.129.0 nemá problémy s kompatibilitou s Azure AD Connect verzí 1.1.649.0.

## <a name="july-2017"></a>Červenec 2017
**Aktualizace agenta:**

* Agent Azure AD Connect Health pro služba AD DS (verze 3.0.68.0)
  1. Opravy chyb a Obecná vylepšení
  2. Svrchovaná podpora pro Cloud
* Agent Azure AD Connect Health pro AD FS (verze 3.0.68.0)
  1. Opravy chyb a Obecná vylepšení
  2. Svrchovaná podpora pro Cloud
* Agent Azure AD Connect Health pro synchronizaci (verze 3.0.68.0) byl vydaný s Azure AD Connect verze 1.1.614.0.
  1. Podpora pro Microsoft Azure Government Cloud a Microsoft Cloud Německo

## <a name="april-2017"></a>Duben 2017      
**Aktualizace agenta:**

* Agent Azure AD Connect Health pro AD FS (verze 3.0.12.0)
  1. Opravy chyb a Obecná vylepšení
* Agent Azure AD Connect Health pro služba AD DS (verze 3.0.12.0)
  1. Vylepšení nahrávání čítačů výkonu
  2. Opravy chyb a Obecná vylepšení

## <a name="october-2016"></a>Říjen 2016
**Aktualizace agenta:**

* Agent Azure AD Connect Health pro AD FS (verze 2.6.408.0)
* Vylepšení zjišťování IP adres klientů v žádostech o ověření
* Opravy chyb související s výstrahami
* Agent Azure AD Connect Health pro služba AD DS (verze 2.6.408.0)
* Opravy chyb související s výstrahami.
* Agent Azure AD Connect Health pro synchronizaci (verze 2.6.353.0) byl vydaný s Azure AD Connect verze 1.1.281.0.
* Zadejte požadovaná data pro zprávy o chybách synchronizace.
* Opravy chyb související s výstrahami

**Nové funkce ve verzi Preview:**

* Zprávy o chybách synchronizace pro Azure AD Connect

**Nové funkce:**

* Pole Azure AD Connect Health pro AD FS-IP adresa je k dispozici v sestavě o prvních 50 uživatelích s chybným uživatelským jménem nebo heslem.

## <a name="july-2016"></a>Červenec 2016
**Nové funkce ve verzi Preview:**

* [Azure AD Connect Health pro služba AD DS](how-to-connect-health-adds.md).

## <a name="january-2016"></a>Leden 2016
**Aktualizace agenta:**

* Agent Azure AD Connect Health pro AD FS (verze 2.6.91.1512)

**Nové funkce:**

* [Nástroj pro testování připojení pro agenty Azure AD Connect Health](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>Listopad 2015
**Nové funkce:**

* Podpora [řízení přístupu na základě role v Azure (Azure RBAC)](how-to-connect-health-operations.md#manage-access-with-azure-rbac)

**Nové funkce ve verzi Preview:**

* [Azure AD Connect Health pro synchronizaci](how-to-connect-health-sync.md).

**Opravené problémy:**

* Opravy chyb pro chyby zjištěné při registraci agenta.

## <a name="september-2015"></a>Září 2015
**Nové funkce:**

* Nesprávná sestava hesla uživatelského jména pro AD FS
* Podpora konfigurace neověřeného proxy serveru HTTP
* Podpora konfigurace agenta v jádru serveru
* Vylepšení výstrah pro AD FS
* Vylepšení v agentovi Azure AD Connect Health pro AD FS pro připojení a nahrávání dat.

**Opravené problémy:**

* Opravy chyb v přehledech využití AD FS typy chyb.

## <a name="june-2015"></a>Červen 2015
**Počáteční verze Azure AD Connect Health pro AD FS a AD FS proxy**

**Nové funkce:**

* Výstrahy týkající se monitorování AD FS a AD FS proxy serverů pomocí e-mailových oznámení.
* Snadný přístup k AD FS topologie a vzory v AD FS čítače výkonu.
* Trend úspěšných požadavků na tokeny na serverech AD FS seskupených podle aplikací, metod ověřování, požadavku na umístění v síti atd.
* Trendy v neúspěšné žádosti na serverech AD FS seskupené podle aplikací, typů chyb atd.
* Jednodušší nasazení agenta pomocí přihlašovacích údajů globálního správce Azure AD.  

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [monitorování místní infrastruktury identit a synchronizačních služeb v cloudu](./whatis-azure-ad-connect.md).