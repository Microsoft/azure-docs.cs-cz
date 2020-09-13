---
title: Rozšířená detekce útoků s více fázemi v Azure Sentinel
description: Pomocí technologie Fusion v Azure Sentinel můžete snížit únavu výstrah a vytvořit incidenty, které jsou založené na pokročilé detekci útoku na více fází.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/18/2020
ms.author: yelevin
ms.openlocfilehash: 1f415294c77b743996993f1f00be45e36f9d6002
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89660669"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Rozšířená detekce útoků s více fázemi v Azure Sentinel


> [!IMPORTANT]
> Některé funkce Fusion v Azure Sentinel jsou momentálně ve verzi Public Preview.
> Tyto funkce se poskytují bez smlouvy o úrovni služeb a nedoporučují se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Pomocí technologie Fusion, která je založená na strojovém učení, může Azure Sentinel automaticky detekovat útoky s více fázemi kombinací chování neobvyklé a podezřelých aktivit, které jsou pozorovány v různých fázích dezaktivačního řetězu. Azure Sentinel pak generuje incidenty, které by jinak bylo obtížné zachytit. Tyto incidenty uzavřou dvě nebo více výstrah nebo aktivit. V takovém případě mají tyto incidenty nízký objem, vysokou přesnost a vysokou závažnost.

Tato detekce přizpůsobená vašemu prostředí neomezuje jenom falešně pozitivní míru, ale může také detekovat útoky s omezenými nebo chybějícími informacemi.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Konfigurace pro pokročilou detekci útoků s více fázemi

Tato detekce je ve výchozím nastavení povolená v Azure Sentinel. Pokud chcete stav ověřit nebo ho zakázat, protože používáte alternativní řešení k vytváření incidentů na základě více výstrah, postupujte podle následujících pokynů:

1. Pokud jste to ještě neudělali, přihlaste se k [Portálu Azure](https://portal.azure.com).

2. Přejít na **Azure Sentinel**  >  **Configuration**  >  **Analytics**

3. Vyberte **aktivní pravidla** a ve sloupci **název** vyhledejte **Pokročilé zjišťování útoků s více fázemi** . Zkontrolujte sloupec **stav** a potvrďte, jestli je toto zjišťování povolené nebo zakázané.

4. Chcete-li změnit stav, vyberte tuto položku a v okně **Pokročilé zjišťování útoků s více fázemi** vyberte možnost **Upravit**.

5. V okně **Průvodce vytvořením pravidla** se automaticky vybere Změna stavu, takže vyberte **Další: zkontrolovat**a pak **Uložit**. 

Šablony pravidel nelze použít pro pokročilou detekci útoku na více fází.

> [!NOTE]
> Služba Azure Sentinel aktuálně používá ke studiu systémů strojového učení 30 dní historických dat. Tato data se vždycky šifrují pomocí klíčů Microsoftu při jejich předávání prostřednictvím kanálu strojového učení. Školicí data se ale nešifrují pomocí [zákaznických klíčů (CMK)](customer-managed-keys.md) , pokud jste v pracovním prostoru Sentinel Azure povolili CMK. Pokud se chcete odhlásit z fúze, přejděte na **Azure Sentinel**   \>  **Configuration**   \>  **Analytics \> aktivní pravidla \> Upřesnit detekci útoků ve více fázích** a ve sloupci **stav** vyberte **zakázat.**

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>Fúze pomocí Palo Alto Networks a programu Microsoft Defender pro koncový bod (dříve Microsoft Defender ATP)

Tyto scénáře kombinují dva základní protokoly používané analytiky zabezpečení: protokoly brány firewall z Palo Alto Networks a protokoly detekce koncových bodů z Microsoft Defenderu pro koncový bod. Ve všech scénářích uvedených níže se v koncovém bodě, který zahrnuje externí IP adresu, detekuje podezřelá aktivita, a pak se za ní potom neobvyklé provoz z externí IP adresy zpátky do brány firewall. V protokolech Palo Alto se Azure Sentinel zaměřuje na [protokoly hrozeb](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)a provoz se považuje za podezřelý, pokud jsou povolené hrozby (podezřelá data, soubory, zaplavení, pakety, kontroly, spyware, adresy URL, viry, chyby zabezpečení, Wildfire-viry, wildfires).

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Požadavek na síť na službu pro samoobslužné zpracování dat následovaný provozem neobvyklé označeným příznakem Palo Alto Networks firewall.

V tomto scénáři Azure Sentinel nejdřív detekuje výstrahu, že Microsoft Defender pro koncové body (dříve Microsoft Defender pro ATP) zjistil požadavek na síť pro službu pro automatizaci systému, která vede na neobvyklé aktivitu. To bylo iniciováno v účtu {account Name} s ID SID {SID} v {time}. Odchozí IP adresa pro připojení byla {IndividualIp}.
Pak se zjistila neobvyklá aktivita v bráně firewall sítě Palo Alto na adrese {TimeGenerated}. To znamená, že se škodlivý provoz zadaný v síti cílová IP adresa pro síťový provoz je {DestinationIP}.

Tento scénář je aktuálně ve verzi Public Preview.


### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Prostředí PowerShell provedlo podezřelé síťové připojení, po kterém následují přenosy neobvyklé s příznakem Palo Alto Networks firewall.

V tomto scénáři Azure Sentinel nejprve detekuje výstrahu, že program Microsoft Defender pro koncové body (dříve Microsoft Defender pro ATP) zjistil, že prostředí PowerShell provedlo podezřelé síťové připojení, které vedlo k neobvyklé aktivitě, kterou zjistila Palo Alto Network firewall. Toto spustil účet {Account Name} s ID SID {SID} v {time}. Odchozí IP adresa pro připojení byla {IndividualIp}. Pak se zjistila neobvyklá aktivita v bráně firewall sítě Palo Alto na adrese {TimeGenerated}. To znamená, že škodlivý provoz vstoupil do vaší sítě. Cílová IP adresa pro síťový provoz je {DestinationIP}.

Tento scénář je aktuálně ve verzi Public Preview.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Odchozí připojení k IP adrese s historií neautorizovaných pokusů o přístup následovaných neobvyklé provozem označenými příznakem Palo Alto Networks firewall

V tomto scénáři služba Azure Sentinel detekuje výstrahu, že program Microsoft Defender pro koncové body (dříve Microsoft Defender pro ATP) zjistil odchozí připojení k IP adrese s historií neautorizovaných pokusů o přístup, které v bráně firewall Palo Alto Networks zavedly detekci neobvyklé aktivit. Toto spustil účet {Account Name} s ID SID {SID} v {time}. Odchozí IP adresa pro připojení byla {IndividualIp}. Po této instalaci zjistila brána firewall sítě Palo Alto v lokalitě {TimeGenerated} neobvyklé aktivity. To znamená, že škodlivý provoz vstoupil do vaší sítě. Cílová IP adresa pro síťový provoz je {DestinationIP}.

Tento scénář je aktuálně ve verzi Public Preview.



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>Fúze pomocí Identity Protection a Microsoft Cloud App Security

Pomocí pokročilé detekce útoků na více fází podporuje Azure Sentinel následující scénáře, které kombinují události anomálií z Azure Active Directory Identity Protection a Microsoft Cloud App Security:

- [Nemožná cesta do neobvyklých umístění následovaných aktivitou neobvyklé Office 365](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Přihlašovací aktivita pro neznámé místo, po kterém následují neobvyklé aktivita Office 365](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Přihlašovací aktivita z infikovaného zařízení následovaný neobvyklé aktivitou Office 365](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Přihlašovací aktivita z anonymní IP adresy, za kterou následuje aktivita neobvyklé Office 365](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Přihlašovací aktivita od uživatele s nevrácenými přihlašovacími údaji následovanými aktivitou neobvyklé Office 365](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

Je nutné mít nakonfigurované [konektory Azure AD Identity Protection data](connect-azure-ad-identity-protection.md) a [Cloud App Security](connect-cloud-app-security.md) .

V popisech, které následují, zobrazí Azure Sentinel skutečnou hodnotu z dat, která se na této stránce reprezentují jako proměnné v závorkách. Například skutečný zobrazovaný název účtu \<*account name*> , nikoli a skutečný počet, nikoli \<*number*> .

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Nemožná cesta do neobvyklých umístění následovaných aktivitou neobvyklé Office 365

K dispozici je sedm možných incidentů Sentinel Azure, které spojují nepravděpodobné cesty k neobvyklým výstrahám umístění z Azure AD Identity Protection a neobvyklé výstrahy sady Office 365 vygenerované Microsoft Cloud App Security:

- **Nemožná cesta do netypických míst, která vedou k exfiltrace poštovní schránky Office 365**
    
    Tato výstraha je označením události přihlášení \<*account name*>  z nemožného cestování do \<*location*> neobvyklých umístění, po kterém následuje pravidlo pro přeposílání podezřelé doručené pošty, které je nastaveno v doručené poště uživatele.
    
    To může znamenat, že došlo k ohrožení zabezpečení účtu a že se poštovní schránka používá k exfiltrovatí informací z vaší organizace. Uživatel \<*account name*> vytvořil nebo aktualizoval pravidlo přeposílání doručené pošty, které předává všechny příchozí e-maily na externí adresu \<*email address*> .

- **Nemožná cesta do netypických míst, která by vedla k podezřelé aktivitě správy Cloud**
    
    Tato výstraha je označením události přihlášení \<*account name*> z nemožného cestování do \<*location*> neobvyklých míst.
    
    Dále účet, který se \<*account name*> provádí \<*number*> v rámci aktivit správy v jedné relaci.

- **Nemožná cesta do netypických umístění, což vede k hromadnému odstranění souborů**
    
    Tato výstraha je označením události přihlášení \<*account name*> do \<*location*> neobvyklých umístění. 
    
    Dále účet \<*account name*> odstranil \<*number of*> v jedné relaci jedinečné soubory.

- **Nemožná cesta do netypických míst, která vedou ke stažení hromadného souboru**
    
    Tato výstraha je označením události přihlášení \<*account name*> z nemožného cestování do \<*location*> neobvyklých míst. 
    
    V dalším kroku se účet \<*account name*> stáhl \<*number of*> v jedné relaci do jedinečných souborů.

- **Nemožná cesta k netypickým místům, které vede k zosobnění Office 365**
    
    Tato výstraha je označením události přihlášení \<*account name*> z nemožného cestování do \<*location*> neobvyklých míst. 
    
    Dále účet \<*account name*> provedl neobvyklé množství ( \<*number of activities*> ) zosobněných aktivit v jedné relaci.

- **Nemožná cesta do netypických míst, která vedou ke sdílení souborů**
    
    Tato výstraha je označením události přihlášení \<*account name*> z nemožného cestování do \<*location*> neobvyklých míst. 
    
    Dále účet \<*account name*> sdílený přes \<*number of*> jedinečné soubory v jedné relaci.

- **Nepovedlo se cestovat do neobvyklých míst, která ransomwarem v cloudové aplikaci.**
    
    Tato výstraha je označením události přihlášení \<*account name*> z nemožného cestování do \<*location*> neobvyklých míst. 
    
    Dále účet \<*account name*> nahrál \<*number of*> soubory a odstranil celkem \<*number of*> souborů. 
    
    Tento vzor aktivity je indikativní pro potenciální útok ransomwarem.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Přihlašovací aktivita pro neznámé místo, po kterém následují neobvyklé aktivita Office 365

K dispozici je sedm možných incidentů Sentinel Azure, které spojují přihlašovací aktivitu pro neznámé výstrahy umístění od Azure AD Identity Protection a neobvyklé výstrahy sady Office 365 vygenerované Microsoft Cloud App Security.

- **Přihlašovací událost z neznámého umístění, které vede k Exchange Online Mailbox exfiltrace**
    
    Tato výstraha je označením události přihlášení \<*account name*> z \<*location*> neznámého umístění, po kterém následuje pravidlo pro přeposílání podezřelé doručené pošty, které bylo nastaveno v doručené poště uživatele.
    
    To může znamenat, že došlo k ohrožení zabezpečení účtu a že se poštovní schránka používá k exfiltrovatí informací z vaší organizace. Uživatel \<*account name*> vytvořil nebo aktualizoval pravidlo přeposílání doručené pošty, které předává všechny příchozí e-maily na externí adresu \<*email address*> . 

- **Přihlašovací událost z neznámého umístění vedoucí k podezřelé aktivitě správy cloudové aplikace**
    
    Tato výstraha je označením události přihlášení \<*account name*> z \<*location*> neznámého umístění. 
    
    Dále účet, který se \<*account name*> provádí \<*number of*> v rámci aktivit správy v jedné relaci.

- **Událost přihlášení z neznámého umístění, které vede k odstranění souboru**
    
    Tato výstraha je označením události přihlášení \<*account name*> z \<*location*> neznámého umístění. 
    
    Dále účet \<*account name*> odstranil \<*number of*> v jedné relaci jedinečné soubory.

- **Událost přihlášení z neznámého umístění, které vede k hromadnému stažení souboru**
    
    Tato výstraha je označením události přihlášení \<*account name*> z \<*location*> neznámého umístění. 
    
    V dalším kroku se účet \<*account name*> stáhl \<*number of*> v jedné relaci do jedinečných souborů.

- **Přihlašovací událost z neznámého umístění, které vede k zosobnění Office 365**
    
    Tato výstraha je označením události přihlášení \<*account name*> z \<*location*> neznámého umístění.
    
    V dalším kroku se účet \<*account name*> zosobňuje \<*number of*> v jedné relaci v rámci různých účtů.

- **Událost přihlášení z neznámého umístění, které vede k hromadnému sdílení souborů**
    
    Tato výstraha je označením události přihlášení \<*account name*> z \<*location*> neznámého umístění. 
    
    Dále účet \<*account name*> sdílený přes \<*number of*> jedinečné soubory v jedné relaci.

- **Přihlašovací událost z neznámého umístění, které vede k ransomwarem v cloudové aplikaci**
    
    Tato výstraha je označením události přihlášení \<*account name*> z \<*location*> neznámého umístění. 
    
    Dále účet \<*account name*> nahrál \<*number of*> soubory a odstranil celkem \<*number of*> souborů. 
    
    Tento vzor aktivity je indikativní pro potenciální útok ransomwarem.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Přihlašovací aktivita z infikovaného zařízení následovaný neobvyklé aktivitou Office 365

Existuje sedm možných incidentů Sentinel Azure, které spojují přihlašovací aktivitu z nakažených výstrah zařízení od Azure AD Identity Protection a neobvyklé výstrah Office 365 generovaných Microsoft Cloud App Security:

- **Přihlašovací událost z nakaženého zařízení, které vede k exfiltrace poštovní schránky Office 365**
    
    Tato výstraha je označením události přihlášení \<*account name*> ze zařízení potenciálně nakaženého malwarem, po kterém následuje pravidlo pro přeposílání podezřelé doručené pošty, které je nastavené v doručené poště uživatele.
    
    To může znamenat, že došlo k ohrožení zabezpečení účtu a že se poštovní schránka používá k exfiltrovatí informací z vaší organizace. Uživatel \<*account name*> vytvořil nebo aktualizoval pravidlo přeposílání doručené pošty, které předává všechny příchozí e-maily na externí adresu \<*email address*> . 

- **Přihlašovací událost z nakaženého zařízení vedoucí k podezřelé aktivitě správy cloudové aplikace**
    
    Tato výstraha je označením události přihlášení ze zařízení, které \<*account name*> je potenciálně napadené malwarem.
    
    Dále účet, který se \<*account name*> provádí \<*number of*> v rámci aktivit správy v jedné relaci.

- **Událost přihlášení z nakaženého zařízení, které by vedlo k odstranění souboru**
    
    Tato výstraha je označením události přihlášení ze zařízení, které \<*account name*> je potenciálně napadené malwarem. 
    
    Dále účet \<*account name*> odstranil \<*number of*> v jedné relaci jedinečné soubory.

- **Událost přihlášení z nakaženého zařízení, které vede k hromadnému stažení souboru**
    
    Tato výstraha je označením události přihlášení ze zařízení, které \<*account name*> je potenciálně napadené malwarem. 
    
    V dalším kroku se účet \<*account name*> stáhl \<*number of*> v jedné relaci do jedinečných souborů.

- **Přihlašovací událost z nakaženého zařízení, které vede k zosobnění Office 365**
    
    Tato výstraha je označením události přihlášení ze zařízení, které \<*account name*> je potenciálně napadené malwarem. 
    
    V dalším kroku se účet \<*account name*> zosobňuje \<*number of*> v jedné relaci v rámci různých účtů.

- **Událost přihlášení z nakaženého zařízení, které vede k hromadnému sdílení souborů**
    
    Tato výstraha je označením události přihlášení ze zařízení, které \<*account name*> je potenciálně napadené malwarem. 
    
    Dále účet \<*account name*> sdílený přes \<*number of*> jedinečné soubory v jedné relaci.

- **Přihlašovací událost z nakaženého zařízení, které se ransomwarem v cloudové aplikaci**
    
    Tato výstraha je označením události přihlášení ze zařízení, které \<*account name*> je potenciálně napadené malwarem. 
    
    Dále účet \<*account name*> nahrál \<*number of*> soubory a odstranil celkem \<*number of*> souborů. 
    
    Tento vzor aktivity je indikativní pro potenciální útok ransomwarem.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Přihlašovací aktivita z anonymní IP adresy, za kterou následuje aktivita neobvyklé Office 365

K dispozici je sedm možných incidentů ověřování Azure, které spojují přihlašovací aktivitu z výstrah anonymních IP adres z Azure AD Identity Protection a neobvyklé výstrahy sady Office 365 vygenerované Microsoft Cloud App Security:

- **Událost přihlášení z anonymní IP adresy, která vede k exfiltrace poštovní schránky Office 365**
    
    Tato výstraha je označením události přihlášení \<*account name*> z IP adresy anonymního proxy serveru \<*IP address*> , po které následuje pravidlo pro přeposílání podezřelé doručené pošty, které je nastaveno v doručené poště uživatele.
    
    To může znamenat, že došlo k ohrožení zabezpečení účtu a že se poštovní schránka používá k exfiltrovatí informací z vaší organizace. Uživatel \<*account name*> vytvořil nebo aktualizoval pravidlo přeposílání doručené pošty, které předává všechny příchozí e-maily na externí adresu \<*email address*> . 

- **Událost přihlášení z anonymní IP adresy, která vede k podezřelé aktivitě správy cloudové aplikace**
    
    Tato výstraha je označením události přihlášení \<*account name*> z IP adresy anonymního proxy serveru \<*IP address*> . 
    
    Dále účet, který se \<*account name*> provádí \<*number of*> v rámci aktivit správy v jedné relaci.

- **Událost přihlášení z anonymní IP adresy, která vede k odstranění souboru**
    
    Tato výstraha je označením události přihlášení \<*account name*> z IP adresy anonymního proxy serveru \<*IP address*> . 
    
    Dále účet \<*account name*> odstranil \<*number of*> v jedné relaci jedinečné soubory.

- **Událost přihlášení z anonymní IP adresy, která vede ke stažení hromadného souboru**
    
    Tato výstraha je označením události přihlášení \<*account name*> z IP adresy anonymního proxy serveru \<*IP address*> . 
    
    V dalším kroku se účet \<*account name*> stáhl \<*number of*> v jedné relaci do jedinečných souborů.

- **Událost přihlášení z anonymní IP adresy, která vede k zosobnění systému Office 365**
    
    Tato výstraha je označením události přihlášení \<*account name*> z IP adresy anonymního proxy serveru \<*IP address*> . 
    
    V dalším kroku se účet \<*account name*> zosobňuje \<*number of*> v jedné relaci v rámci různých účtů.

- **Událost přihlášení z anonymní IP adresy, která vede ke sdílení souborů**
    
    Tato výstraha je označením události přihlášení \<*account name*> z IP adresy anonymního proxy serveru \<*IP address*> . 
    
    Dále účet \<*account name*> sdílený přes \<*number of*> jedinečné soubory v jedné relaci.

- **Událost přihlášení z anonymní IP adresy do ransomwarem v cloudové aplikaci**
    
    Tato výstraha je označením události přihlášení \<*account name*> z IP adresy anonymního proxy serveru \<*IP address*> . 
    
    Dále účet \<*account name*> nahrál \<*number of*> soubory a odstranil celkem \<*number of*> souborů. 
    
    Tento vzor aktivity je indikativní pro potenciální útok ransomwarem.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Přihlašovací aktivita od uživatele s nevrácenými přihlašovacími údaji následovanými aktivitou neobvyklé Office 365

Existuje sedm možných incidentů Sentinel Azure, které spojují přihlašovací aktivitu od uživatele s nevrácenými výstrahami přihlašovacích údajů z Azure AD Identity Protection a neobvyklé výstrah Office 365 generovaných Microsoft Cloud App Security:

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k poštovní schránce Office 365 exfiltrace**
    
    Tato výstraha je označením, že přihlašovací událost, která po \<*account name*> odstraněných přihlašovacích údajích, následovaná pravidlem pro přesměrování doručené pošty, byla nastavena v doručené poště uživatele. 
    
    To může znamenat, že došlo k ohrožení zabezpečení účtu a že se poštovní schránka používá k exfiltrovatí informací z vaší organizace. Uživatel \<*account name*> vytvořil nebo aktualizoval pravidlo přeposílání doručené pošty, které předává všechny příchozí e-maily na externí adresu \<*email address*> . 

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k podezřelé aktivitě správy cloudové aplikace**
    
    Tato výstraha znamená, že přihlašovací událost \<*account name*> využívala nevyužité přihlašovací údaje.
    
    Dále účet, který se \<*account name*> provádí \<*number of*> v rámci aktivit správy v jedné relaci.

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k hromadnému odstranění souboru**
    
    Tato výstraha znamená, že přihlašovací událost \<*account name*> využívala nevyužité přihlašovací údaje.
    
    Dále účet \<*account name*> odstranil \<*number of*> v jedné relaci jedinečné soubory.

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucím ke stažení souboru**
    
    Tato výstraha znamená, že přihlašovací událost \<*account name*> využívala nevyužité přihlašovací údaje.
    
    V dalším kroku se účet \<*account name*> stáhl \<*number of*> v jedné relaci do jedinečných souborů.

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k zosobnění systému Office 365**
    
    Tato výstraha znamená, že přihlašovací událost \<*account name*> využívala nevyužité přihlašovací údaje. 
    
    V dalším kroku se účet \<*account name*> zosobňuje \<*number of*> v jedné relaci v rámci různých účtů.

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k hromadnému sdílení souborů**
    
    Tato výstraha znamená, že přihlašovací událost \<*account name*> využívala nevyužité přihlašovací údaje.
    
    Dále účet \<*account name*> sdílený přes \<*number of*> jedinečné soubory v jedné relaci.

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji do ransomwarem v cloudové aplikaci**
    
    Tato výstraha znamená, že přihlašovací událost \<*account name*> využívala nevyužité přihlašovací údaje. 
    
    Dále účet \<*account name*> nahrál \<*number of*> soubory a odstranil celkem \<*number of*> souborů. 
    
    Tento vzor aktivity je indikativní pro potenciální útok ransomwarem.

## <a name="next-steps"></a>Další kroky

Seznámili jste se s tím, že jste se dozvěděli o pokročilé detekci útoků ve více fázích, a můžete se zajímat, jak získat přehled o vašich datech a potenciálních hrozbách: [Začínáme s Sentinel Azure](quickstart-get-visibility.md).

Pokud jste připraveni prozkoumat incidenty, které jste si vytvořili, přečtěte si následující kurz: [Prozkoumejte incidenty pomocí Azure Sentinel](tutorial-investigate-cases.md).

