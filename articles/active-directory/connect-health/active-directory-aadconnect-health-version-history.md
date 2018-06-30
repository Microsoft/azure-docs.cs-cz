---
title: Historie verzí služby Azure AD Connect Health
description: Tento dokument popisuje verze pro Azure AD Connect Health a co byl zahrnut v těchto verzích.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: mtillman
editor: curtand
ms.assetid: 8dd4e998-747b-4c52-b8d3-3900fe77d88f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 8907619b802d93cee4463a80155f38ce9030948f
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129667"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: Historie vydaných verzí
Tým služby Azure Active Directory pravidelně aktualizuje Azure AD Connect Health se nové funkce a funkce. V tomto článku jsou uvedené verze a funkce, které byly vydány.

## <a name="june-2018"></a>2018 června 
**Nové funkce verze preview:** 
* Azure AD Connect Health pro synchronizaci - diagnostikovat a opravit chyby synchronizace duplicitní atribut z portálu 

**Aktualizace agenta:** 
*  Agent Azure AD Connect Health pro službu AD DS (verze 3.1.7.0)    
   1. Oprava hotfix pro problém vysoké využití procesoru monitorování agent s verzemi KB rozhraní .NET Framework

*   Agent Azure AD Connect Health pro službu AD FS (verze 3.1.7.0)  
   1. Oprava hotfix pro problém vysoké využití procesoru monitorování agent s verzemi KB rozhraní .NET Framework  
   2. Výsledky testu opravy na sekundárním serveru služby AD FS Server 2016
   
*   Agent Azure AD Connect Health pro službu AD FS (verze 3.1.2.0)  
   1. Oprava hotfix pro agenta správy paměti a související výstrahy speciálně pro verzi 3.0.244.0


## <a name="may-2018"></a>Květen 2018
**Aktualizace agenta:**
*   Agent Azure AD Connect Health pro službu AD DS (verze 3.0.244.0)
 1. Agent ochrany osobních údajů zlepšování  
 2. Obecné vylepšení a opravy chyb

*   Agent Azure AD Connect Health pro službu AD FS (verze 3.0.244.0)
 1. Služba agenta diagnostiky a související vylepšení modulu prostředí PowerShell
 2. Agent ochrany osobních údajů zlepšování  
 3. Obecné vylepšení a opravy chyb

* Agent Azure AD Connect Health pro synchronizaci (verze 3.0.164.0) vydané službou Azure AD Connect verze 1.1.819.0
 1. Agent ochrany osobních údajů zlepšování  
 2. Obecné vylepšení a opravy chyb


## <a name="march-2018"></a>2018 března
**Nové funkce verze preview:**
* Azure AD Connect Health pro AD FS – riziková IP adresa sestavy a výstrahy.

**Aktualizace agenta:**

*   Agent Azure AD Connect Health pro službu AD DS (verze 3.0.176.0)
  1. Vylepšení dostupnost agenta 
  2. Obecné vylepšení a opravy chyb
*   Agent Azure AD Connect Health pro službu AD FS (verze 3.0.176.0)
  1. Vylepšení dostupnost agenta 
  2. Obecné vylepšení a opravy chyb
* Agent Azure AD Connect Health pro synchronizaci (verze 3.0.176.0)
  1. Vylepšení dostupnost agenta 
  2. Obecné vylepšení a opravy chyb

## <a name="december-2017"></a>2017 prosinec
**Aktualizace agenta:**

*   Agent Azure AD Connect Health pro službu AD DS (verze 3.0.145.0)
  1. Vylepšení dostupnost agenta 
  2. Přidání nové řešení problémů s příkazy agenta
  3. Obecné vylepšení a opravy chyb
*   Agent Azure AD Connect Health pro službu AD FS (verze 3.0.145.0)
  1. Přidání nové řešení problémů s příkazy agenta
  2. Vylepšení dostupnost agenta 
  3. Obecné vylepšení a opravy chyb
  
## <a name="october-2017"></a>Říjen 2017
**Aktualizace agenta:**

 * Agent Azure AD Connect Health pro synchronizaci (verze 3.0.129.0) vydané službou Azure AD Connect verze 1.1.649.0
<br></br> Opravit potíže s kompatibilitou verze mezi Azure AD Connect a Agent Azure AD Connect Health pro synchronizaci. Tento problém ovlivňuje zákazníky, kteří jsou provádění Azure AD Connect místní upgrade na verzi 1.1.647.0, ale verze 3.0.127.0 stavu agenta v současnosti má. Po upgradu agenta stavu už odesílat data o stavu týkající se služby Azure AD Connect synchronizace služby Azure AD Health. Pomocí této opravy je nainstalován Agent stavu verze 3.0.129.0 během Azure AD Connect místní upgrade. Agent stavu verze 3.0.129.0 nemá potíže s kompatibilitou s Azure AD Connect verze 1.1.649.0.

## <a name="july-2017"></a>2017 července
**Aktualizace agenta:**

*   Agent Azure AD Connect Health pro službu AD DS (verze 3.0.68.0)
  1. Obecné vylepšení a opravy chyb
  2. Podpora svrchovaných cloudu
*   Agent Azure AD Connect Health pro službu AD FS (verze 3.0.68.0)
  1. Obecné vylepšení a opravy chyb
  2. Podpora svrchovaných cloudu
* Agent Azure AD Connect Health pro synchronizaci (verze 3.0.68.0) vydané službou Azure AD Connect verze 1.1.614.0
  1. Podpora pro Microsoft Azure Cloud vlády a Německo Microsoft Cloud

## <a name="april-2017"></a>Dubna 2017      
**Aktualizace agenta:**

*   Agent Azure AD Connect Health pro službu AD FS (verze 3.0.12.0)
  1. Obecné vylepšení a opravy chyb
*   Agent Azure AD Connect Health pro službu AD DS (verze 3.0.12.0)
  1. Čítače výkonu nahrát vylepšení
  2. Obecné vylepšení a opravy chyb

## <a name="october-2016"></a>Října 2016
**Aktualizace agenta:**

* Agent Azure AD Connect Health pro službu AD FS (verze 2.6.408.0)
1. Vylepšení detekce klientských IP adres v žádosti o ověření
2. Opravy chyb, které souvisí s výstrahami
* Agent Azure AD Connect Health pro službu AD DS (verze 2.6.408.0)
1. Opravy chyb, které souvisí s výstrahami.
* Agent Azure AD Connect Health pro synchronizaci (verze 2.6.353.0) vydané službou Azure AD Connect verze 1.1.281.0
1. Zadejte požadovaná data pro sestavy chyb synchronizace
2. Opravy chyb, které souvisí s výstrahami

**Nové funkce verze preview:**

* Zprávy o chybách synchronizace pro Azure AD Connect

**Nové funkce:**

* Azure AD Connect Health pro AD FS – pole IP adresy je k dispozici v sestavě o nejčastějších 50 uživatelů s chybné uživatelské jméno a heslo.

## <a name="july-2016"></a>Července 2016
**Nové funkce verze preview:**

* [Azure AD Connect Health pro službu AD DS](active-directory-aadconnect-health-adds.md).

## <a name="january-2016"></a>leden 2016
**Aktualizace agenta:**

* Agent Azure AD Connect Health pro službu AD FS (verze 2.6.91.1512)

**Nové funkce:**

* [Nástroj pro testování připojení pro Azure AD Connect stavu agentů](active-directory-aadconnect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>listopad 2015
**Nové funkce:**

* Podpora pro [řízení přístupu podle rolí](active-directory-aadconnect-health-operations.md#manage-access-with-role-based-access-control)

**Nové funkce verze preview:**

* [Azure AD Connect Health pro synchronizaci](active-directory-aadconnect-health-sync.md).

**Opravené problémy:**

* Opravy chyb pro chyby můžou vyskytnout během registrace agenta.

## <a name="september-2015"></a>Září 2015
**Nové funkce:**

* Nesprávný uživatelské jméno hesla sestav pro službu AD FS
* Podpora konfigurace neověřené Proxy protokolu HTTP
* Podpora konfigurace agenta na jádru serveru
* Vylepšení výstrah pro službu AD FS
* Vylepšení v agenta Azure AD Connect Health pro službu AD FS pro připojení a data nahrát.

**Opravené problémy:**

* Opravy chyb v přehledy využití pro typy chyb AD FS.

## <a name="june-2015"></a>Červen 2015
**Počáteční verze služby Azure AD Connect Health pro AD FS a Proxy služby AD FS.**

**Nové funkce:**

* Výstrahy sledování serverů služby AD FS a Proxy služby AD FS s e-mailová oznámení.
* Snadný přístup k topologie služby AD FS a vzory v čítače výkonu služby AD FS.
* Trend úspěšné žádosti o tokeny na serverech služby AD FS, které jsou seskupené podle aplikací, metody ověřování, žádosti o síťové umístění atd.
* Trendy v chybných požadavků na serverech služby AD FS, které jsou seskupené podle aplikací, chyba typy atd.
* Jednodušší nasazení agenta pomocí přihlašovacích údajů Azure AD globálního správce.  

## <a name="next-steps"></a>Další postup
Další informace o [monitorování vaší místní infrastruktury identit a synchronizaci služeb v cloudu](active-directory-aadconnect-health.md).

