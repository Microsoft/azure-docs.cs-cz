---
title: Historie verzí služby Azure AD Connect Health
description: Tento dokument popisuje verze pro Azure AD Connect Health a co bylo zahrnuto v těchto verzích.
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
ms.date: 03/20/2019
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ace8d170a5ba48c00775c3b376df8bb70a337d5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76897035"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: Historie vydaných verzí
Tým Služby Azure Active Directory pravidelně aktualizuje azure ad connect health s novými funkcemi a funkcemi. Tento článek uvádí verze a funkce, které byly vydány.  

> [!NOTE]
> Agenti stavu Connect se aktualizují automaticky při vydání nové verze. Zkontrolujte, zda je z webu Azure Portal povoleno nastavení automatického upgradu.
>

Azure AD Connect Health for Sync je integrovaná s instalací Azure AD Connect. Další informace o [historii verzí služby Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history) Pro zpětnou vazbu o funkcích, hlasování na kanálu Connect Health User [Voice](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)

## <a name="july-2019"></a>Červenec 2019
**Aktualizace agenta**
* Agent azure ad připojení pro službu AD FS (verze 3.1.59.0) 
   1. Změna textu v TestWindowsTransport
   2. Změny pro nahrávání ad FS RP
   
* Agent azure ad připojení pro službu AD FS (verze 3.1.56.0) 
   1. Přidání testu TestWindowsTransport test a odebrat WsTrust koncové body se změnami v testu CheckOffice365Koncové body
   2. Protokolovat informace o o osaaa a .NET
   3. Zvětšete velikost nahrávání konfigurační zprávy RP na 1 MB.
   4. Opravy chyb
   
* Agent azure ad připojení pro službu AD DS (verze 3.1.56.0) 
   1. Protokolovat informace o o osaaa a .NET 
   2. Opravy chyb

## <a name="may-2019"></a>Květen 2019
**Aktualizace agenta:** 
* Agent azure ad připojení pro službu AD FS (verze 3.1.51.0) 
   1. Oprava chyb k rozlišení mezi více přihlášení, které sdílejí stejný id požadavku klienta.
   2. Oprava chyb y analýzy chyby uživatelského jména a hesla na lokalizovaných serverech jazyka.   

## <a name="april-2019"></a>Duben 2019
**Aktualizace agenta:** 
* Agent azure ad připojení pro službu AD FS (verze 3.1.46.0) 
   1. Opravit kontrolu duplicitního procesu výstrahy spn pro službu ADFS

## <a name="march-2019"></a>Březen 2019
**Aktualizace agenta:** 
* Agent azure ad připojení pro službu AD DS (verze 3.1.41.0)  
   1. Kolekce verzí rozhraní .NET
   2. Zlepšení sběru čítačů výkonu při vynechání určitých kategorií
   3. Oprava chyby v zabránění tření více instancí agenta monitorování

* Agent azure ad připojení pro službu AD FS (verze 3.1.41.0) 
   1. Integrace a upgrade testovacích skriptů služby AD FS pomocí adfstoolboxu
   2. Implementovat kolekci verzí rozhraní .NET
   3. Zlepšení sběru čítačů výkonu při vynechání určitých kategorií
   4. Oprava chyby v zabránění tření více instancí agenta monitorování


## <a name="november-2018"></a>Listopad 2018
**Nové funkce GA:** 
* Azure AD Connect Health for Sync – diagnostika a náprava duplicitních chyb synchronizace atributů z portálu

**Aktualizace agenta:** 
* Agent azure ad připojení pro službu AD DS (verze 3.1.24.0) 
   1. Dodržování a vynucení protokolu TLS (Transport Layer Security) verze 1.2
   2. Snižte výstražný šum globálního katalogu
   3. Opravy chyb registrace agenta stavu

* Agent azure ad připojení pro službu AD FS (verze 3.1.24.0)  
   1. Dodržování a vynucení protokolu TLS (Transport Layer Security) verze 1.2
   2. Podpora testu ADFSRequestToken pro lokalizovaný operační systém
   3. Vyřešený problém uzamčení diagnostiky agenta EventHandler
   4. Opravy chyb registrace agenta stavu

## <a name="august-2018"></a>Srpen 2018 
*  Agent azure ad připojení pro synchronizaci (verze 3.1.7.0) vydaný s Azure AD Connect verze 1.1.880.0    
   1. Oprava hotfix pro [vysoký problém procesoru monitorovacího agenta s verzemi .NET Framework KB](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>Červen 2018 
**Nové funkce náhledu:** 
* Azure AD Connect Health for Sync – diagnostika a náprava duplicitních chyb synchronizace atributů z portálu 

**Aktualizace agenta:** 
* Agent azure ad připojení pro službu AD DS (verze 3.1.7.0)    
  1. Oprava hotfix pro [vysoký problém procesoru monitorovacího agenta s verzemi .NET Framework KB](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
* Agent azure ad připojení pro službu AD FS (verze 3.1.7.0)  
  1. Oprava hotfix pro [vysoký problém procesoru monitorovacího agenta s verzemi .NET Framework KB](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
  2. Opravy výsledků testů na sekundárním serveru ADFS Server 2016
   
* Agent azure ad připojení pro službu AD FS (verze 3.1.2.0)  
  1. Oprava hotfix pro správu paměti agenta a související výstrahy speciálně pro verzi 3.0.244.0


## <a name="may-2018"></a>Květen 2018
**Aktualizace agenta:**
* Agent azure ad připojení pro službu AD DS (verze 3.0.244.0)
  1. Zlepšení ochrany osobních údajů agenta  
  2. Opravy chyb a obecná vylepšení

* Agent azure ad připojení pro službu AD FS (verze 3.0.244.0)
  1. Služba Diagnostika agentů a související vylepšení modulů Prostředí PowerShell
  2. Zlepšení ochrany osobních údajů agenta  
  3. Opravy chyb a obecná vylepšení

* Agent azure ad připojení pro synchronizaci (verze 3.0.164.0) vydaný s Azure AD Connect verze 1.1.819.0 
  1. Zlepšení ochrany osobních údajů agenta  
  2. Opravy chyb a obecná vylepšení


## <a name="march-2018"></a>Březen 2018
**Nové funkce náhledu:**
* Azure AD Connect Health pro službu AD FS – sestava a výstraha rizikové IP adresy.

**Aktualizace agenta:**

* Agent azure ad připojení pro službu AD DS (verze 3.0.176.0)
  1. Vylepšení dostupnosti agentů 
  2. Opravy chyb a obecná vylepšení
* Agent azure ad připojení pro službu AD FS (verze 3.0.176.0)
  1. Vylepšení dostupnosti agentů 
  2. Opravy chyb a obecná vylepšení
* Agent azure ad připojení pro synchronizaci (verze 3.0.129.0) vydaný s Azure AD Connect verze 1.1.750.0  
  1. Vylepšení dostupnosti agentů 
  2. Opravy chyb a obecná vylepšení

## <a name="december-2017"></a>Prosinec 2017
**Aktualizace agenta:**

* Agent azure ad připojení pro službu AD DS (verze 3.0.145.0)
  1. Vylepšení dostupnosti agentů 
  2. Přidány nové příkazy pro řešení potíží s agentem
  3. Opravy chyb a obecná vylepšení
* Agent azure ad připojení pro službu AD FS (verze 3.0.145.0)
  1. Přidány nové příkazy pro řešení potíží s agentem
  2. Vylepšení dostupnosti agentů 
  3. Opravy chyb a obecná vylepšení
  
## <a name="october-2017"></a>Říjen 2017
**Aktualizace agenta:**

 * Agent azure ad připojení pro synchronizaci (verze 3.0.129.0) vydaný s Azure AD Connect verze 1.1.649.0
<br></br> Opraven problém s kompatibilitou verzí mezi Azure AD Connect a Agentem stavu Azure AD Connect pro synchronizaci. Tento problém se týká zákazníků, kteří provádějí upgrade služby Azure AD Connect na místo na verzi 1.1.647.0, ale aktuálně má agenta stavu verze 3.0.127.0. Po upgradu agenta stavu už nemůže odesílat data o synchronizační službě Azure AD Connect do služby Azure AD Health. S touto opravou se agent a agent a stav verze 3.0.129.0 nainstaluje během upgradu Azure AD Connect na místě. Agent stavu verze 3.0.129.0 nemá problém s kompatibilitou s Azure AD Connect verze 1.1.649.0.

## <a name="july-2017"></a>Červenec 2017
**Aktualizace agenta:**

* Agent azure ad připojení pro službu AD DS (verze 3.0.68.0)
  1. Opravy chyb a obecná vylepšení
  2. Podpora suverénního cloudu
* Agent azure ad připojení pro službu AD FS (verze 3.0.68.0)
  1. Opravy chyb a obecná vylepšení
  2. Podpora suverénního cloudu
* Agent azure ad připojení pro synchronizaci (verze 3.0.68.0) vydaný s Azure AD Connect verze 1.1.614.0
  1. Podpora pro Microsoft Azure Government Cloud a Microsoft Cloud Germany

## <a name="april-2017"></a>Duben 2017      
**Aktualizace agenta:**

* Agent azure ad připojení pro službu AD FS (verze 3.0.12.0)
  1. Opravy chyb a obecná vylepšení
* Agent azure ad připojení pro službu AD DS (verze 3.0.12.0)
  1. Vylepšení nahrávání čítačů výkonu
  2. Opravy chyb a obecná vylepšení

## <a name="october-2016"></a>Říjen 2016
**Aktualizace agenta:**

* Agent azure ad připojení pro službu AD FS (verze 2.6.408.0)
* Vylepšení zjišťování IP adres klientů v požadavcích na ověření
* Opravy chyb související s výstrahami
* Agent azure ad připojení pro službu AD DS (verze 2.6.408.0)
* Opravy chyb související s výstrahami.
* Agent azure ad připojení pro synchronizaci (verze 2.6.353.0) vydaný s Azure AD Connect verze 1.1.281.0
* Poskytnutí požadovaných dat pro zprávy o chybách synchronizace
* Opravy chyb související s výstrahami

**Nové funkce náhledu:**

* Zprávy o chybách synchronizace pro Azure AD Connect

**Nové funkce:**

* Azure AD Connect Health pro službu AD FS – pole IP adresy je k dispozici v sestavě o 50 nejlepších uživatelích se špatným uživatelským jménem/heslem.

## <a name="july-2016"></a>Červenec 2016
**Nové funkce náhledu:**

* [Azure AD Connect Stav pro službu AD DS](how-to-connect-health-adds.md).

## <a name="january-2016"></a>Leden 2016
**Aktualizace agenta:**

* Agent azure ad připojení pro službu AD FS (verze 2.6.91.1512)

**Nové funkce:**

* [Nástroj test připojení pro agenty stavu Azure AD Connect](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>Listopad 2015
**Nové funkce:**

* Podpora [řízení přístupu na základě rolí](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)

**Nové funkce náhledu:**

* [Azure AD Connect Health pro synchronizaci](how-to-connect-health-sync.md).

**Opraveny problémy:**

* Opravy chyb chyb při registraci agenta.

## <a name="september-2015"></a>Září 2015
**Nové funkce:**

* Nesprávná sestava hesla uživatelského jména pro ad FS
* Podpora konfigurace neověřeného serveru proxy HTTP
* Podpora konfigurace agenta v jádru serveru
* Vylepšení výstrah pro ad FS
* Vylepšení agenta stavu Azure AD Connect pro službu AD FS pro připojení a nahrávání dat.

**Opraveny problémy:**

* Opravy chyb v přehledech využití pro typy chyb služby AD FS.

## <a name="june-2015"></a>Červen 2015
**Počáteční verze Azure AD Connect Health pro službu AD FS a proxy služby AD FS.**

**Nové funkce:**

* Upozornění pro sledování serverů Proxy služby AD FS a Služby AD FS pomocí e-mailových oznámení.
* Snadný přístup k topologii a vzorům služby AD FS v čítačích výkonu služby AD FS.
* Trend v úspěšných požadavcích na tokeny na serverech služby AD FS seskupených podle aplikací, metod ověřování, požadavku na umístění v síti atd.
* Trendy v neúspěšném požadavku na serverech služby AD FS seskupených podle aplikací, typů chyb atd.
* Jednodušší nasazení agenta pomocí přihlašovacích údajů globálního správce Azure AD.  

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [monitorování místní infrastruktury identit a synchronizačních službách v cloudu](whatis-hybrid-identity-health.md).

