---
title: Historie verzí služby Azure AD Connect Health
description: Tento dokument popisuje se vydání verzí pro Azure AD Connect Health a co je součástí v těchto verzích.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
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
ms.openlocfilehash: 08d1589b8b215471983dc76a836dd89f83fd8204
ms.sourcegitcommit: d61faf71620a6a55dda014a665155f2a5dcd3fa2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54053394"
---
# <a name="azure-ad-connect-health-version-release-history"></a>Azure AD Connect Health: Historie vydaných verzí
Tým služby Azure Active Directory pravidelně aktualizuje s novými funkcemi a funkce Azure AD Connect Health. Tento článek uvádí verzích a funkcích, které byly vydány.
Azure AD Connect Health pro synchronizaci je součástí instalace služby Azure AD Connect. Další informace o [historie verzí služby Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history) zpětnou vazbu funkce, Hlasujte na [Connect Health User Voice kanálu](https://feedback.azure.com/forums/169401-azure-active-directory/filters/new?category_id=165591)

## <a name="november-2018"></a>. Listopadu 2018
**Nové funkce všeobecné dostupnosti:** 
* Azure AD Connect Health pro synchronizaci - diagnostikovat a opravit chyby synchronizace duplicitním atributem z portálu

**Aktualizace agenta:** 
*  Agent Azure AD Connect Health pro službu AD DS (verze 3.1.24.0) 
1. Přenos dodržování předpisů verze 1.2 protokol zabezpečení vrstvy (TLS) a vynucení
2. Omezily rušivé výstrahy globálního katalogu
3. Opravy chyb registrace agenta stavu

*   Agent Azure AD Connect Health pro službu AD FS (verze 3.1.24.0)
1. Přenos dodržování předpisů verze 1.2 protokol zabezpečení vrstvy (TLS) a vynucení
2. Podpora ADFSRequestToken testovacího lokalizovaný operačního systému
3. Vyřešit problém zamykání EventHandler disgnostic agenta
4. Opravy chyb registrace agenta stavu

## <a name="august-2018"></a>Srpen 2018 
*  Agent Azure AD Connect Health pro synchronizaci (verze 3.1.7.0) vydané službou Azure AD Connect verze 1.1.880.0    
   1. Oprava hotfix pro [monitoring agent pomocí rozhraní .NET Framework KB vysoké využití procesoru vydání verze](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)

## <a name="june-2018"></a>Červen 2018 
**Nové funkce ve verzi preview:** 
* Azure AD Connect Health pro synchronizaci - diagnostikovat a opravit chyby synchronizace duplicitním atributem z portálu 

**Aktualizace agenta:** 
*  Agent Azure AD Connect Health pro službu AD DS (verze 3.1.7.0)    
   1. Oprava hotfix pro [monitoring agent pomocí rozhraní .NET Framework KB vysoké využití procesoru vydání verze](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   
*   Agent Azure AD Connect Health pro službu AD FS (verze 3.1.7.0)  
   1. Oprava hotfix pro [monitoring agent pomocí rozhraní .NET Framework KB vysoké využití procesoru vydání verze](https://support.microsoft.com/help/4346822/high-cpu-issue-in-azure-active-directory-connect-health-for-sync)
   2. Výsledky testu opravy na sekundárním serveru služby AD FS Server 2016
   
*   Agent Azure AD Connect Health pro službu AD FS (verze 3.1.2.0)  
   1. Oprava hotfix pro agenta správy paměti a souvisejících výstrah speciálně pro verzi 3.0.244.0


## <a name="may-2018"></a>Květen 2018
**Aktualizace agenta:**
*   Agent Azure AD Connect Health pro službu AD DS (verze 3.0.244.0)
 1. Zlepšení agenta ochrany osobních údajů  
 2. Obecná vylepšení a opravy chyb

*   Agent Azure AD Connect Health pro službu AD FS (verze 3.0.244.0)
 1. Služba agenta diagnostiky a související vylepšení modulu prostředí PowerShell
 2. Zlepšení agenta ochrany osobních údajů  
 3. Obecná vylepšení a opravy chyb

* Agent Azure AD Connect Health pro synchronizaci (verze 3.0.164.0) vydané službou Azure AD Connect verze 1.1.819.0 
 1. Zlepšení agenta ochrany osobních údajů  
 2. Obecná vylepšení a opravy chyb


## <a name="march-2018"></a>Březen 2018
**Nové funkce ve verzi preview:**
* Azure AD Connect Health pro AD FS – sestavě rizikových IP adres a upozornění.

**Aktualizace agenta:**

*   Agent Azure AD Connect Health pro službu AD DS (verze 3.0.176.0)
  1. Vylepšení dostupnosti agenta 
  2. Obecná vylepšení a opravy chyb
*   Agent Azure AD Connect Health pro službu AD FS (verze 3.0.176.0)
  1. Vylepšení dostupnosti agenta 
  2. Obecná vylepšení a opravy chyb
* Agent Azure AD Connect Health pro synchronizaci (verze 3.0.129.0) vydané službou Azure AD Connect verze 1.1.750.0  
  1. Vylepšení dostupnosti agenta 
  2. Obecná vylepšení a opravy chyb

## <a name="december-2017"></a>Prosinec 2017
**Aktualizace agenta:**

*   Agent Azure AD Connect Health pro službu AD DS (verze 3.0.145.0)
  1. Vylepšení dostupnosti agenta 
  2. Přidání nových řešení problémů příkazů agenta
  3. Obecná vylepšení a opravy chyb
*   Agent Azure AD Connect Health pro službu AD FS (verze 3.0.145.0)
  1. Přidání nových řešení problémů příkazů agenta
  2. Vylepšení dostupnosti agenta 
  3. Obecná vylepšení a opravy chyb
  
## <a name="october-2017"></a>Říjen 2017
**Aktualizace agenta:**

 * Agent Azure AD Connect Health pro synchronizaci (verze 3.0.129.0) vydané službou Azure AD Connect verze 1.1.649.0
<br></br> Opravili jsme potíže s kompatibilitou verze mezi Azure AD Connect a agenta Azure AD Connect Health pro synchronizaci. Tento problém má vliv na zákazníky, kteří působí Azure AD Connect místní upgrade na verzi 1.1.647.0, ale má v současné době verze 3.0.127.0 stavu agenta. Po upgradu agenta stavu už odesílání dat o službu Azure AD Connect synchronizaci stavu do služby Azure AD Health. S touto opravou během místní upgrade služby Azure AD Connect nainstaluje agenta služby Health verze 3.0.129.0. Agent stavu verze 3.0.129.0 nemá potíže s kompatibilitou s Azure AD Connect verze 1.1.649.0.

## <a name="july-2017"></a>Červenec 2017
**Aktualizace agenta:**

*   Agent Azure AD Connect Health pro službu AD DS (verze 3.0.68.0)
  1. Obecná vylepšení a opravy chyb
  2. Podpora suverénních cloudů
*   Agent Azure AD Connect Health pro službu AD FS (verze 3.0.68.0)
  1. Obecná vylepšení a opravy chyb
  2. Podpora suverénních cloudů
* Agent Azure AD Connect Health pro synchronizaci (verze 3.0.68.0) vydané službou Azure AD Connect verze 1.1.614.0
  1. Podpora pro Microsoft Azure Government Cloud a Microsoft Cloud Germany

## <a name="april-2017"></a>Duben 2017      
**Aktualizace agenta:**

*   Agent Azure AD Connect Health pro službu AD FS (verze 3.0.12.0)
  1. Obecná vylepšení a opravy chyb
*   Agent Azure AD Connect Health pro službu AD DS (verze 3.0.12.0)
  1. Čítače výkonu nahrát vylepšení
  2. Obecná vylepšení a opravy chyb

## <a name="october-2016"></a>Říjen 2016
**Aktualizace agenta:**

* Agent Azure AD Connect Health pro službu AD FS (verze 2.6.408.0)
1. Vylepšení detekce klientských IP adres v žádosti o ověření
2. Opravy chyb související s výstrahami
* Agent Azure AD Connect Health pro službu AD DS (verze 2.6.408.0)
1. Opravy chyb související s výstrahami.
* Agent Azure AD Connect Health pro synchronizaci (verze 2.6.353.0) vydané službou Azure AD Connect verze 1.1.281.0
1. Zadejte požadovaná data pro zprávy o chybách synchronizace
2. Opravy chyb související s výstrahami

**Nové funkce ve verzi preview:**

* Zprávy o chybách synchronizace Azure AD Connect

**Nové funkce:**

* Azure AD Connect Health pro AD FS – IP adresa pole je k dispozici v sestavě o nejčastějších 50 uživatelů s chybné uživatelské jméno a heslo.

## <a name="july-2016"></a>Červenec 2016
**Nové funkce ve verzi preview:**

* [Azure AD Connect Health pro službu AD DS](how-to-connect-health-adds.md).

## <a name="january-2016"></a>leden 2016
**Aktualizace agenta:**

* Agent Azure AD Connect Health pro službu AD FS (verze 2.6.91.1512)

**Nové funkce:**

* [Nástroj pro testování připojení pro Azure AD Connect stavu agentů](how-to-connect-health-agent-install.md#test-connectivity-to-azure-ad-connect-health-service)

## <a name="november-2015"></a>listopad 2015
**Nové funkce:**

* Podpora pro [řízení přístupu podle rolí](how-to-connect-health-operations.md#manage-access-with-role-based-access-control)

**Nové funkce ve verzi preview:**

* [Azure AD Connect Health pro synchronizaci](how-to-connect-health-sync.md).

**Opravené problémy:**

* Opravy chyb během registrace agenta.

## <a name="september-2015"></a>Září 2015
**Nové funkce:**

* Nesprávné uživatelské jméno heslo sestavy pro službu AD FS
* Podpora konfigurace neověřené Proxy protokolu HTTP
* Podpora konfigurace agenta na jádru serveru
* Vylepšení výstrah pro službu AD FS
* Vylepšení agenta Azure AD Connect Health pro službu AD FS pro připojení a data nahrát.

**Opravené problémy:**

* Opravy chyb v přehledů o využití pro typy chyb AD FS.

## <a name="june-2015"></a>Červen 2015
**Počáteční verze služby Azure AD Connect Health pro AD FS a Proxy služby AD FS.**

**Nové funkce:**

* Výstrahy pro monitorování serverů služby AD FS a Proxy služby AD FS s e-mailová oznámení.
* Snadný přístup k topologie služby AD FS a vzory v čítače výkonu služby AD FS.
* Trend v úspěšných žádostí o token na serverech služby AD FS seskupené podle aplikací, metody ověřování, žádost o síťové umístění atd.
* Vývoj požadavků na servery služby AD FS seskupené podle aplikací, chyba typy atd.
* Jednodušší nasazení agenta pomocí přihlašovacích údajů globálního správce Azure AD.  

## <a name="next-steps"></a>Další postup
Další informace o [monitorování vaší místní infrastruktury identit a synchronizace služeb v cloudu](whatis-hybrid-identity-health.md).

