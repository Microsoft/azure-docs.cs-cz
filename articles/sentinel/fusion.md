---
title: Rozšířená detekce útoků s více fázemi v Azure Sentinel
description: Pomocí technologie Fusion v Azure Sentinel můžete snížit únavu výstrah a vytvořit incidenty, které jsou založené na pokročilé detekci útoku na více fází.
services: sentinel
documentationcenter: na
author: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/12/2020
ms.author: rkarlin
ms.openlocfilehash: ada2ad67bc3634d8e6a31d3c8a69fc0c8b08a93a
ms.sourcegitcommit: f255f869c1dc451fd71e0cab340af629a1b5fb6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2020
ms.locfileid: "77369686"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Rozšířená detekce útoků s více fázemi v Azure Sentinel

Pomocí technologie Fusion, která je založená na strojovém učení, může Azure Sentinel automaticky detekovat útoky s více fázemi kombinací chování neobvyklé a podezřelých aktivit, které jsou pozorovány v různých fázích dezaktivačního řetězu. Azure Sentinel pak generuje incidenty, které by jinak bylo obtížné zachytit. Tyto incidenty uzavřou dvě nebo více výstrah nebo aktivit. V takovém případě mají tyto incidenty nízký objem, vysokou přesnost a vysokou závažnost.

Tato detekce přizpůsobená vašemu prostředí neomezuje jenom falešně pozitivní míru, ale může také detekovat útoky s omezenými nebo chybějícími informacemi.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Konfigurace pro pokročilou detekci útoku na více fází

Tato detekce je ve výchozím nastavení povolená v Azure Sentinel. Pokud chcete stav ověřit nebo ho zakázat, protože používáte alternativní řešení k vytváření incidentů na základě více výstrah, postupujte podle následujících pokynů:

1. Pokud jste to ještě neudělali, přihlaste se k [Portálu Azure](https://portal.azure.com).

2. Přejděte na **Azure Sentinel** > **Configuration** > **Analytics** .

3. Vyberte **aktivní pravidla** a ve sloupci **název** vyhledejte **Pokročilé zjišťování útoků s více fázemi** . Zkontrolujte sloupec **stav** a potvrďte, jestli je toto zjišťování povolené nebo zakázané.

4. Chcete-li změnit stav, vyberte tuto položku a v okně **Pokročilé zjišťování útoků s více fázemi** vyberte možnost **Upravit**.

5. V okně **Průvodce vytvořením pravidla** se automaticky vybere Změna stavu, takže vyberte **Další: zkontrolovat**a pak **Uložit**. 

Šablony pravidel nelze použít pro pokročilou detekci útoku na více fází.

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-atp"></a>Fúze pomocí Palo Alto Networks a ATP v programu Microsoft Defender

- Požadavek na síť pro službu anonymity na základě provozu následovaný provozem neobvyklé označeným příznakem Palo Alto Networks firewall

- Prostředí PowerShell provedlo podezřelé síťové připojení, po kterém následují přenosy neobvyklé s příznakem Palo Alto Networks firewall

- Odchozí připojení k IP adrese s historií neautorizovaných pokusů o přístup následovaných neobvyklé provozem označenými příznakem Palo Alto Networks firewall



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>Fúze pomocí Identity Protection a Microsoft Cloud App Security

Pomocí pokročilé detekce útoků na více fází podporuje Azure Sentinel následující scénáře, které kombinují události anomálií z Azure Active Directory Identity Protection a Microsoft Cloud App Security:

- [Nemožná cesta do neobvyklých umístění následovaných aktivitou neobvyklé Office 365](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Přihlašovací aktivita pro neznámé místo, po kterém následují neobvyklé aktivita Office 365](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Přihlašovací aktivita z infikovaného zařízení následovaný neobvyklé aktivitou Office 365](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Přihlašovací aktivita z anonymní IP adresy, za kterou následuje aktivita neobvyklé Office 365](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Přihlašovací aktivita od uživatele s nevrácenými přihlašovacími údaji následovanými aktivitou neobvyklé Office 365](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

Je nutné mít nakonfigurované [konektory Azure AD Identity Protection data](connect-azure-ad-identity-protection.md) a [Cloud App Security](connect-cloud-app-security.md) .

V popisech, které následují, zobrazí Azure Sentinel skutečnou hodnotu z dat, která se na této stránce reprezentují jako proměnné v závorkách. Například skutečný zobrazovaný název účtu místo \<*název účtu*> a skutečný počet, nikoli \<*číslo*>.

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Nemožná cesta do neobvyklých umístění následovaných aktivitou neobvyklé Office 365

K dispozici je sedm možných incidentů Sentinel Azure, které spojují nepravděpodobné cesty k neobvyklým výstrahám umístění z Azure AD Identity Protection a neobvyklé výstrahy sady Office 365 vygenerované Microsoft Cloud App Security:

- **Nemožná cesta do netypických míst, která vedou k exfiltrace poštovní schránky Office 365**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z nemožného umístění do \<ho *umístění*>, což je neobvyklým umístěním, po kterém následuje pravidlo pro přesměrování doručené pošty, které bylo nastaveno v doručené poště uživatele.
    
    To může znamenat, že došlo k ohrožení zabezpečení účtu a že se poštovní schránka používá k exfiltrovatí informací z vaší organizace. *Název účtu*uživatele \<> vytvořil nebo aktualizoval pravidlo přeposílání doručené pošty, které předává všechny příchozí e-maily na externí adresu \<> *e-mailová adresa*.

- **Nemožná cesta do netypických míst, která by vedla k podezřelé aktivitě správy Cloud**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z nemožného cestování do \<ho *umístění*>, což je neobvyklé umístění.
    
    V dalším kroku se *název*účtu \<účtu > provedl v rámci \<*číslo*> aktivity správy v jedné relaci.

- **Nemožná cesta do netypických umístění, což vede k hromadnému odstranění souborů**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> do \<ho *umístění*>, což je neobvyklé umístění. 
    
    V dalším kroku se *název*účtu \<účtu > odstranil v jedné relaci \<*počet*> jedinečných souborů.

- **Nemožná cesta do netypických míst, která vedou ke stažení hromadného souboru**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z nemožného cestování do \<ho *umístění*>, což je neobvyklé umístění. 
    
    V dalším kroku se *název*účtu \<účtu > stáhl přes \<*počet*> jedinečných souborů v jedné relaci.

- **Nemožná cesta k netypickým místům, které vede k zosobnění Office 365**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z nemožného cestování do \<ho *umístění*>, což je neobvyklé umístění. 
    
    V dalším kroku *název*účtu \<účtu > v jedné relaci provedl neobvyklou částku (\<*počet aktivit*>) aktivit zosobnění.

- **Nemožná cesta do netypických míst, která vedou ke sdílení souborů**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z nemožného cestování do \<ho *umístění*>, což je neobvyklé umístění. 
    
    V dalším kroku \<*název účtu*účtu > sdílet přes \<*počet*> jedinečných souborů v jedné relaci.

- **Nepovedlo se cestovat do neobvyklých míst, která ransomwarem v cloudové aplikaci.**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z nemožného cestování do \<ho *umístění*>, což je neobvyklé umístění. 
    
    V dalším kroku se název účtu \<*účtu*> nahrál \<*počet*souborů > a odstranil se celkový *počet \<souborů >.* 
    
    Tento vzor aktivity je indikativní pro potenciální útok ransomwarem.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Přihlašovací aktivita pro neznámé místo, po kterém následují neobvyklé aktivita Office 365

K dispozici je sedm možných incidentů Sentinel Azure, které spojují přihlašovací aktivitu pro neznámé výstrahy umístění od Azure AD Identity Protection a neobvyklé výstrahy sady Office 365 vygenerované Microsoft Cloud App Security.

- **Přihlašovací událost z neznámého umístění, které vede k Exchange Online Mailbox exfiltrace**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z \<ho *umístění*>, neznámého umístění, po kterém následuje pravidlo pro předávání podezřelé doručené pošty, bylo nastaveno na doručenou poštu uživatele.
    
    To může znamenat, že došlo k ohrožení zabezpečení účtu a že se poštovní schránka používá k exfiltrovatí informací z vaší organizace. *Název účtu*uživatele \<> vytvořil nebo aktualizoval pravidlo přeposílání doručené pošty, které předává všechny příchozí e-maily na externí adresu \<> *e-mailová adresa*. 

- **Přihlašovací událost z neznámého umístění vedoucí k podezřelé aktivitě správy cloudové aplikace**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z \<ho *umístění*>, neznámého umístění. 
    
    V dalším kroku se *název*účtu \<účtu > provedl v rámci \<*počtu*> aktivit správy v jedné relaci.

- **Událost přihlášení z neznámého umístění, které vede k odstranění souboru**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z \<ho *umístění*>, neznámého umístění. 
    
    V dalším kroku se *název*účtu \<účtu > odstranil v jedné relaci \<*počet*> jedinečných souborů.

- **Událost přihlášení z neznámého umístění, které vede k hromadnému stažení souboru**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z \<ho *umístění*>, neznámého umístění. 
    
    V dalším kroku se *název*účtu \<účtu > stáhl přes \<*počet*> jedinečných souborů v jedné relaci.

- **Přihlašovací událost z neznámého umístění, které vede k zosobnění Office 365**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z \<ho *umístění*>, neznámého umístění.
    
    V dalším kroku se *název*účtu \<účtu > zosobnit přes \<*počet*> různých účtů v jedné relaci.

- **Událost přihlášení z neznámého umístění, které vede k hromadnému sdílení souborů**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z \<ho *umístění*>, neznámého umístění. 
    
    V dalším kroku \<*název účtu*účtu > sdílet přes \<*počet*> jedinečných souborů v jedné relaci.

- **Přihlašovací událost z neznámého umístění, které vede k ransomwarem v cloudové aplikaci**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z \<ho *umístění*>, neznámého umístění. 
    
    V dalším kroku se název účtu \<*účtu*> nahrál \<*počet*souborů > a odstranil se celkový *počet \<souborů >.* 
    
    Tento vzor aktivity je indikativní pro potenciální útok ransomwarem.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Přihlašovací aktivita z infikovaného zařízení následovaný neobvyklé aktivitou Office 365

Existuje sedm možných incidentů Sentinel Azure, které spojují přihlašovací aktivitu z nakažených výstrah zařízení od Azure AD Identity Protection a neobvyklé výstrah Office 365 generovaných Microsoft Cloud App Security:

- **Přihlašovací událost z nakaženého zařízení, které vede k exfiltrace poštovní schránky Office 365**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> ze zařízení, které je potenciálně nakažené malwarem, po kterém následuje podezřelé pravidlo pro přesměrování doručené pošty nastavené na doručenou poštu uživatele.
    
    To může znamenat, že došlo k ohrožení zabezpečení účtu a že se poštovní schránka používá k exfiltrovatí informací z vaší organizace. *Název účtu*uživatele \<> vytvořil nebo aktualizoval pravidlo přeposílání doručené pošty, které předává všechny příchozí e-maily na externí adresu \<> *e-mailová adresa*. 

- **Přihlašovací událost z nakaženého zařízení vedoucí k podezřelé aktivitě správy cloudové aplikace**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> ze zařízení, které je potenciálně napadené malwarem.
    
    V dalším kroku se *název*účtu \<účtu > provedl v rámci \<*počtu*> aktivit správy v jedné relaci.

- **Událost přihlášení z nakaženého zařízení, které by vedlo k odstranění souboru**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> ze zařízení, které je potenciálně napadené malwarem. 
    
    V dalším kroku se *název*účtu \<účtu > odstranil v jedné relaci \<*počet*> jedinečných souborů.

- **Událost přihlášení z nakaženého zařízení, které vede k hromadnému stažení souboru**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> ze zařízení, které je potenciálně napadené malwarem. 
    
    V dalším kroku se *název*účtu \<účtu > stáhl přes \<*počet*> jedinečných souborů v jedné relaci.

- **Přihlašovací událost z nakaženého zařízení, které vede k zosobnění Office 365**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> ze zařízení, které je potenciálně napadené malwarem. 
    
    V dalším kroku se *název*účtu \<účtu > zosobnit přes \<*počet*> různých účtů v jedné relaci.

- **Událost přihlášení z nakaženého zařízení, které vede k hromadnému sdílení souborů**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> ze zařízení, které je potenciálně napadené malwarem. 
    
    V dalším kroku \<*název účtu*účtu > sdílet přes \<*počet*> jedinečných souborů v jedné relaci.

- **Přihlašovací událost z nakaženého zařízení, které se ransomwarem v cloudové aplikaci**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> ze zařízení, které je potenciálně napadené malwarem. 
    
    V dalším kroku se název účtu \<*účtu*> nahrál \<*počet*souborů > a odstranil se celkový *počet \<souborů >.* 
    
    Tento vzor aktivity je indikativní pro potenciální útok ransomwarem.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Přihlašovací aktivita z anonymní IP adresy, za kterou následuje aktivita neobvyklé Office 365

K dispozici je sedm možných incidentů ověřování Azure, které spojují přihlašovací aktivitu z výstrah anonymních IP adres z Azure AD Identity Protection a neobvyklé výstrahy sady Office 365 vygenerované Microsoft Cloud App Security:

- **Událost přihlášení z anonymní IP adresy, která vede k exfiltrace poštovní schránky Office 365**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z IP adresy anonymního proxy serveru \<> *IP adresa*, po které následuje pravidlo pro přeposílání podezřelé doručené pošty v doručené poště uživatele.
    
    To může znamenat, že došlo k ohrožení zabezpečení účtu a že se poštovní schránka používá k exfiltrovatí informací z vaší organizace. *Název účtu*uživatele \<> vytvořil nebo aktualizoval pravidlo přeposílání doručené pošty, které předává všechny příchozí e-maily na externí adresu \<> *e-mailová adresa*. 

- **Událost přihlášení z anonymní IP adresy, která vede k podezřelé aktivitě správy cloudové aplikace**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z IP adresy anonymního proxy serveru \<> *IP adresa*. 
    
    V dalším kroku se *název*účtu \<účtu > provedl v rámci \<*počtu*> aktivit správy v jedné relaci.

- **Událost přihlášení z anonymní IP adresy, která vede k odstranění souboru**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z IP adresy anonymního proxy serveru \<> *IP adresa*. 
    
    V dalším kroku se *název*účtu \<účtu > odstranil v jedné relaci \<*počet*> jedinečných souborů.

- **Událost přihlášení z anonymní IP adresy, která vede ke stažení hromadného souboru**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z IP adresy anonymního proxy serveru \<> *IP adresa*. 
    
    V dalším kroku se *název*účtu \<účtu > stáhl přes \<*počet*> jedinečných souborů v jedné relaci.

- **Událost přihlášení z anonymní IP adresy, která vede k zosobnění systému Office 365**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z IP adresy anonymního proxy serveru \<> *IP adresa*. 
    
    V dalším kroku se *název*účtu \<účtu > zosobnit přes \<*počet*> různých účtů v jedné relaci.

- **Událost přihlášení z anonymní IP adresy, která vede ke sdílení souborů**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z IP adresy anonymního proxy serveru \<> *IP adresa*. 
    
    V dalším kroku \<*název účtu*účtu > sdílet přes \<*počet*> jedinečných souborů v jedné relaci.

- **Událost přihlášení z anonymní IP adresy do ransomwarem v cloudové aplikaci**
    
    Tato výstraha je označením události přihlášení \<*název účtu*> z IP adresy anonymního proxy serveru \<> *IP adresa*. 
    
    V dalším kroku se název účtu \<*účtu*> nahrál \<*počet*souborů > a odstranil se celkový *počet \<souborů >.* 
    
    Tento vzor aktivity je indikativní pro potenciální útok ransomwarem.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Přihlašovací aktivita od uživatele s nevrácenými přihlašovacími údaji následovanými aktivitou neobvyklé Office 365

Existuje sedm možných incidentů Sentinel Azure, které spojují přihlašovací aktivitu od uživatele s nevrácenými výstrahami přihlašovacích údajů z Azure AD Identity Protection a neobvyklé výstrah Office 365 generovaných Microsoft Cloud App Security:

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k poštovní schránce Office 365 exfiltrace**
    
    Tato výstraha znamená, že přihlašovací událost podle \<*název účtu*> použila nepoužívané přihlašovací údaje, na které se v doručené poště uživatele nastavilo pravidlo pro přeposílání podezřelé doručené pošty. 
    
    To může znamenat, že došlo k ohrožení zabezpečení účtu a že se poštovní schránka používá k exfiltrovatí informací z vaší organizace. *Název účtu*uživatele \<> vytvořil nebo aktualizoval pravidlo přeposílání doručené pošty, které předává všechny příchozí e-maily na externí adresu \<> *e-mailová adresa*. 

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k podezřelé aktivitě správy cloudové aplikace**
    
    Tato výstraha znamená, že přihlašovací událost podle \<*název účtu*> použitá nevrácená pověření.
    
    V dalším kroku se *název*účtu \<účtu > provedl v rámci \<*počtu*> aktivit správy v jedné relaci.

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k hromadnému odstranění souboru**
    
    Tato výstraha znamená, že přihlašovací událost podle \<*název účtu*> použitá nevrácená pověření.
    
    V dalším kroku se *název*účtu \<účtu > odstranil v jedné relaci \<*počet*> jedinečných souborů.

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucím ke stažení souboru**
    
    Tato výstraha znamená, že přihlašovací událost podle \<*název účtu*> použitá nevrácená pověření.
    
    V dalším kroku se *název*účtu \<účtu > stáhl přes \<*počet*> jedinečných souborů v jedné relaci.

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k zosobnění systému Office 365**
    
    Tato výstraha znamená, že přihlašovací událost podle \<*název účtu*> použitá nevrácená pověření. 
    
    V dalším kroku se *název*účtu \<účtu > zosobnit přes \<*počet*> různých účtů v jedné relaci.

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji vedoucími k hromadnému sdílení souborů**
    
    Tato výstraha znamená, že přihlašovací událost podle \<*název účtu*> použitá nevrácená pověření.
    
    V dalším kroku \<*název účtu*účtu > sdílet přes \<*počet*> jedinečných souborů v jedné relaci.

- **Přihlašovací událost od uživatele s nevrácenými přihlašovacími údaji do ransomwarem v cloudové aplikaci**
    
    Tato výstraha znamená, že přihlašovací událost podle \<*název účtu*> použitá nevrácená pověření. 
    
    V dalším kroku se název účtu \<*účtu*> nahrál \<*počet*souborů > a odstranil se celkový *počet \<souborů >.* 
    
    Tento vzor aktivity je indikativní pro potenciální útok ransomwarem.

## <a name="next-steps"></a>Další kroky

Seznámili jste se s tím, že jste se dozvěděli o pokročilé detekci útoků ve více fázích, a můžete se zajímat, jak získat přehled o vašich datech a potenciálních hrozbách: [Začínáme s Sentinel Azure](quickstart-get-visibility.md).

Pokud jste připraveni prozkoumat incidenty, které jste si vytvořili, přečtěte si následující kurz: [Prozkoumejte incidenty pomocí Azure Sentinel](tutorial-investigate-cases.md).

