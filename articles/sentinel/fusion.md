---
title: Pokročilá vícestupňová detekce útoků v Azure Sentinelu
description: Pomocí technologie Fusion v Azure Sentinelu můžete snížit únavu výstrah a vytvořit události, které lze použít a které jsou založeny na pokročilé detekci vícestupňového útoku.
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
ms.openlocfilehash: 87ca322cbdfdd8a53a3ecefcb120a961ea1bb936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77587919"
---
# <a name="advanced-multistage-attack-detection-in-azure-sentinel"></a>Pokročilá vícestupňová detekce útoků v Azure Sentinelu


> [!IMPORTANT]
> Některé funkce Fusion v Azure Sentinelu jsou aktuálně ve verzi Public Preview.
> Tyto funkce jsou k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



Pomocí technologie Fusion, která je založena na strojovém učení, Azure Sentinel můžete automaticky detekovat vícestupňové útoky kombinací neobvyklé chování a podezřelé aktivity, které jsou pozorovány v různých fázích kill-chain. Azure Sentinel pak generuje incidenty, které by jinak bylo velmi obtížné zachytit. Tyto incidenty zapouzdřené dva nebo více výstrah nebo aktivit. Podle návrhu jsou tyto incidenty nízké objem, vysoká věrnost a vysoká závažnost.

Přizpůsobené pro vaše prostředí, tato detekce nejen snižuje falešně pozitivní sazby, ale může také detekovat útoky s omezenými nebo chybějícími informacemi.

## <a name="configuration-for-advanced-multistage-attack-detection"></a>Konfigurace pro pokročilou detekci útoků s více fázemi

Toto zjišťování je ve výchozím nastavení povoleno v Azure Sentinelu. Chcete-li zkontrolovat stav nebo jej zakázat, možná proto, že používáte alternativní řešení pro vytváření incidentů na základě více výstrah, použijte následující pokyny:

1. Pokud jste to ještě neudělali, přihlaste se k [Portálu Azure](https://portal.azure.com).

2. Přechod na Azure **Sentinel** > **Configuration** > **Analytics**

3. Vyberte **Aktivní pravidla** a vyhledejte **rozšířené vícestupňové detekce útoků** ve sloupci **NÁZEV.** Zkontrolujte sloupec **STAV a** ověřte, zda je tato detekce povolena nebo zakázána.

4. Chcete-li změnit stav, vyberte tuto položku a v okně **Rozšířená vícestupňová detekce útoků** vyberte **upravit**.

5. V okně **Průvodce vytvořením pravidla** je automaticky vybrána změna stavu, takže vyberte **Další: Revize**a Potom **Uložit**. 

Šablony pravidel se nevztahují na pokročilou detekci vícestupňového útoku.

> [!NOTE]
> Azure Sentinel v současné době používá 30 dní historických dat k trénování systémů strojového učení. Tato data jsou vždy šifrována pomocí klíčů společnosti Microsoft při průchodu kanálem strojového učení. Trénovací data však není šifrována pomocí [klíčů spravovaných zákazníky (CMK),](customer-managed-keys.md) pokud jste povolili CMK v pracovním prostoru Azure Sentinel. Pokud se chcete z **Azure Sentinel** \>fúze odhlásit, přejděte na aktivní pravidla **Configuration** \> **Status**  ** \> \> ** Azure Sentinel Configuration Analytics Rozšířené vícestupňové zjišťování útoků a ve sloupci Stav vyberte **Zakázat.**

## <a name="fusion-using-palo-alto-networks-and-microsoft-defender-atp"></a>Fúze pomocí palo alto sítí a microsoft defender atp

Tyto scénáře kombinují dva základní protokoly používané analytiky zabezpečení: protokoly brány firewall z palo alto sítí a protokoly zjišťování koncových bodů z programu Microsoft Defender ATP. Ve všech scénářích uvedených níže je v koncovém bodě, který zahrnuje externí IP adresu, zjištěna podezřelá aktivita, po níž následuje neobvyklý provoz z externí IP adresy zpět do brány firewall. V protokolech Palo Alto se Azure Sentinel zaměřuje na [protokoly hrozeb](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/view-and-manage-logs/log-types-and-severity-levels/threat-logs)a provoz je považován za podezřelý, když jsou povoleny hrozby (podezřelá data, soubory, záplavy, pakety, skeny, spyware, adresy URL, viry, slabá místa, viry požáru, požáry).

### <a name="network-request-to-tor-anonymization-service-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Požadavek sítě na anonymizační službu TOR následovaný anomálním provozem označeným bránou firewall Palo Alto Networks.

V tomto scénáři Azure Sentinel nejprve zjistí výstrahu, že Microsoft Defender Advanced Threat Protection zjistil požadavek sítě na anonymizační službu TOR, která vede k anomální aktivitě. Toto bylo zahájeno pod účtem {název účtu} s ID SID {sid} v {time}. Odchozí ADRESA IP připojení byla {IndividualIp}.
Poté byla brána firewall palo alto sítí zjištěna neobvyklou aktivitou v {TimeGenerated}. To znamená, že škodlivý provoz vstoupil do vaší sítě Cílová ADRESA IP pro síťový provoz je {DestinationIP}.

Tento scénář je aktuálně ve verzi Public Preview.


### <a name="powershell-made-a-suspicious-network-connection-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Prostředí PowerShell vytvořilo podezřelé síťové připojení následované neobvyklým provozem označeným bránou firewall Palo Alto Networks.

V tomto scénáři Azure Sentinel nejprve zjistí výstrahu, že Microsoft Defender Advanced Threat Protection zjistil, že PowerShell vytvořil podezřelé síťové připojení vedoucí k anomální aktivity, která byla zjištěna palo Alto síťové brány firewall. To to bylo iniciováno účtem {název účtu} s ID SID {sid} v {time}. Odchozí ADRESA IP připojení byla {IndividualIp}. Poté byla brána firewall palo alto sítí zjištěna neobvyklou aktivitou v {TimeGenerated}. To znamená, že do sítě vstoupil škodlivý provoz. Cílová adresa IP síťového provozu je {DestinationIP}.

Tento scénář je aktuálně ve verzi Public Preview.

### <a name="outbound-connection-to-ip-with-a-history-of-unauthorized-access-attempts-followed-by-anomalous-traffic-flagged-by-palo-alto-networks-firewall"></a>Odchozí připojení k PROTOKOLU IP s historií pokusů o neoprávněný přístup následovaných anomálním provozem označeným bránou firewall palo alto networks

V tomto scénáři Azure Sentinel zjistí výstrahu, že Microsoft Defender Advanced Threat Protection zjistil odchozí připojení k IP adrese s historií pokusů o neoprávněný přístup, které vedou k neobvyklé aktivity zjištěné Palo Alto Brána firewall sítě. To to bylo iniciováno účtem {název účtu} s ID SID {sid} v {time}. Odchozí ADRESA IP připojení byla {IndividualIp}. Poté byla brána firewall palo alto sítí zjištěna neobvyklou aktivitou v {TimeGenerated}. To znamená, že do sítě vstoupil škodlivý provoz. Cílová adresa IP síťového provozu je {DestinationIP}.

Tento scénář je aktuálně ve verzi Public Preview.



## <a name="fusion-using-identity-protection-and-microsoft-cloud-app-security"></a>Fúze pomocí ochrany identity a zabezpečení aplikací Microsoft Cloud App Security

Pomocí pokročilé vícestupňové detekce útoků podporuje Azure Sentinel následující scénáře, které kombinují události anomálií z Azure Active Directory Identity Protection a Microsoft Cloud App Security:

- [Nemožné cestování do atypické polohy následované anomální aktivitou Office 365](#impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity)
- [Aktivita přihlášení pro neznámé umístění následovaná neobvyklou aktivitou Office 365](#sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity)
- [Aktivita přihlášení z infikovaného zařízení následovaná anomální aktivitou Office 365](#sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity)
- [Aktivita přihlášení z anonymní IP adresy následovaná neobvyklou aktivitou Office 365](#sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity)
- [Aktivita přihlášení od uživatele s uniklými přihlašovacími údaji následovanými neobvyklou aktivitou Office 365](#sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity)

Musíte mít nakonfigurovaný [datový konektor Azure AD Identity Protection](connect-azure-ad-identity-protection.md) a konektory Cloud App [Security.](connect-cloud-app-security.md)

V následujících popisech Azure Sentinel zobrazí skutečnou hodnotu z vašich dat, která je na této stránce reprezentována jako proměnné v závorkách. Například skutečný zobrazovaný název účtu, \<nikoli *název účtu*> a \<skutečné číslo *>.*

### <a name="impossible-travel-to-atypical-location-followed-by-anomalous-office-365-activity"></a>Nemožné cestování do atypické polohy následované anomální aktivitou Office 365

Existuje sedm možných incidentů Azure Sentinelu, které kombinují nemožné cestování s atypickými výstrahami polohy z Azure AD Identity Protection a neobvyklými výstrahami Office 365 generovanými službou Microsoft Cloud App Security:

- **Nemožné cestování do atypických míst vedoucích k exfiltraci poštovní schránky Office 365**
    
    Tato výstraha je označením události \<přihlášení *podle názvu účtu* \<> z nemožnécesty do *místa*>, atypické umístění, následované podezřelým pravidlem pro přesměrování doručené pošty, které bylo nastaveno v doručené poště uživatele.
    
    To může znamenat, že účet je ohrožena a že poštovní schránka se používá k exfiltraci informací z vaší organizace. Název \< *uživatelského účtu*> vytvořit nebo aktualizovat pravidlo předávání doručené pošty, \<které předává všechny příchozí e-maily na *e-mailovou adresu* externí adresy>.

- **Nemožné cestování do atypických míst vedoucích k podezřelé administrativní činnosti cloudových aplikací**
    
    Tato výstraha je označením události \<přihlášení podle *názvu účtu* \<> z nemožné cesty do *místa*>, což je atypické umístění.
    
    Dále> \< *název účtu* proveden \<přes *číslo*> aktivity správy v jedné relaci.

- **Nemožné cestování do atypických míst vedoucích k hromadnému mazání souborů**
    
    Tato výstraha je označením události \<přihlášení podle *názvu účtu*> \< *umístění*>, což je atypické umístění. 
    
    Dále název \< *účtu*> \< *odstraněný počet*> jedinečných souborů v jedné relaci.

- **Nemožné cestování do atypických míst vedoucích k hromadnému stahování souborů**
    
    Tato výstraha je označením události \<přihlášení podle *názvu účtu* \<> z nemožné cesty do *místa*>, což je atypické umístění. 
    
    Dále> \< *název účtu* stažen \<přes počet> *jedinečných* souborů v jedné relaci.

- **Nemožné cestování do atypických míst vedoucích k zosobnění Office 365**
    
    Tato výstraha je označením události \<přihlášení podle *názvu účtu* \<> z nemožné cesty do *místa*>, což je atypické umístění. 
    
    Dále název \< *účtu*> provedl neobvyklou\<*částku (počet aktivit*>) aktivit zosobnění v jedné relaci.

- **Nemožné cestování do atypických míst vedoucích k hromadnému sdílení souborů**
    
    Tato výstraha je označením události \<přihlášení podle *názvu účtu* \<> z nemožné cesty do *místa*>, což je atypické umístění. 
    
    Dále> \< *název účtu* sdílen \<přes počet> *jedinečných* souborů v jedné relaci.

- **Nemožné cestování do atypických míst vedoucích k ransomwaru v cloudové aplikaci**
    
    Tato výstraha je označením události \<přihlášení podle *názvu účtu* \<> z nemožné cesty do *místa*>, což je atypické umístění. 
    
    Dále> \< *název účtu* nahrál \< *počet*> souborů a \<odstranil celkem *počet* souborů>. 
    
    Tento vzorec aktivity svědčí o potenciálním útoku ransomware.


### <a name="sign-in-activity-for-unfamiliar-location-followed-by-anomalous-office-365-activity"></a>Aktivita přihlášení pro neznámé umístění následovaná neobvyklou aktivitou Office 365

Existuje sedm možných incidentů Azure Sentinelu, které kombinují aktivitu přihlášení pro neznámé výstrahy polohy z Azure AD Identity Protection a neobvyklé výstrahy Office 365 generované Microsoft Cloud App Security.

- **Událost přihlášení z neznámého místa vedoucího k exfiltraci poštovní schránky Exchange Online**
    
    Tato výstraha je označením události \<přihlášení podle \<názvu *účtu*> z *umístění*>, neznámého umístění, následované podezřelým pravidlem pro předávání doručené pošty, které bylo nastaveno v doručené poště uživatele.
    
    To může znamenat, že účet je ohrožena a že poštovní schránka se používá k exfiltraci informací z vaší organizace. Název \< *uživatelského účtu*> vytvořit nebo aktualizovat pravidlo předávání doručené pošty, \<které předává všechny příchozí e-maily na *e-mailovou adresu* externí adresy>. 

- **Událost přihlášení z neznámého umístění vedoucí k podezřelé aktivitě správy cloudových aplikací**
    
    Tato výstraha je označením události \<přihlášení podle \<názvu *účtu*> z *umístění*> neznámého umístění. 
    
    Dále> \< *název účtu* proveden \<přes *počet*> aktivit správy v jedné relaci.

- **Událost přihlášení z neznámého umístění vedoucík hromadnému odstranění souboru**
    
    Tato výstraha je označením události \<přihlášení podle \<názvu *účtu*> z *umístění*> neznámého umístění. 
    
    Dále název \< *účtu*> \< *odstraněný počet*> jedinečných souborů v jedné relaci.

- **Událost přihlášení z neznámého umístění vedoucí hoštinového souboru**
    
    Tato výstraha je označením události \<přihlášení podle \<názvu *účtu*> z *umístění*> neznámého umístění. 
    
    Dále> \< *název účtu* stažen \<přes počet> *jedinečných* souborů v jedné relaci.

- **Událost přihlášení z neznámého místa vedoucího k zosobnění Office 365**
    
    Tato výstraha je označením události \<přihlášení podle \<názvu *účtu*> z *umístění*> neznámého umístění.
    
    Dále název \< *účtu*> zosobněn přes \< *počet*> různých účtů v jedné relaci.

- **Událost přihlášení z neznámého umístění vedoucího k hromadnému sdílení souborů**
    
    Tato výstraha je označením události \<přihlášení podle \<názvu *účtu*> z *umístění*> neznámého umístění. 
    
    Dále> \< *název účtu* sdílen \<přes počet> *jedinečných* souborů v jedné relaci.

- **Událost přihlášení z neznámého místa vedoucího k ransomwaru v cloudové aplikaci**
    
    Tato výstraha je označením události \<přihlášení podle \<názvu *účtu*> z *umístění*> neznámého umístění. 
    
    Dále> \< *název účtu* nahrál \< *počet*> souborů a \<odstranil celkem *počet* souborů>. 
    
    Tento vzorec aktivity svědčí o potenciálním útoku ransomware.

### <a name="sign-in-activity-from-infected-device-followed-by-anomalous-office-365-activity"></a>Aktivita přihlášení z infikovaného zařízení následovaná anomální aktivitou Office 365

Existuje sedm možných incidentů Azure Sentinelu, které kombinují aktivitu přihlášení z infikovaných zařízení výstrah z Azure AD Identity Protection a neobvyklé výstrahy Office 365 generované Microsoft Cloud App Security:

- **Událost přihlášení z infikovaného zařízení vedoucího k exfiltraci poštovní schránky Office 365**
    
    Tato výstraha je označením události \<přihlášení *podle názvu účtu*> ze zařízení potenciálně infikovaného malwarem, následované podezřelým pravidlem pro přesměrování doručené pošty nastaveným v doručené poště uživatele.
    
    To může znamenat, že účet je ohrožena a že poštovní schránka se používá k exfiltraci informací z vaší organizace. Název \< *uživatelského účtu*> vytvořit nebo aktualizovat pravidlo předávání doručené pošty, \<které předává všechny příchozí e-maily na *e-mailovou adresu* externí adresy>. 

- **Událost přihlášení z infikovaného zařízení vedoucí k podezřelé aktivitě správy cloudových aplikací**
    
    Tato výstraha je označením události \<přihlášení podle *názvu účtu*> ze zařízení potenciálně infikovaného malwarem.
    
    Dále> \< *název účtu* proveden \<přes *počet*> aktivit správy v jedné relaci.

- **Událost přihlášení z infikovaného zařízení vedoucí k hromadnému odstranění souborů**
    
    Tato výstraha je označením události \<přihlášení podle *názvu účtu*> ze zařízení potenciálně infikovaného malwarem. 
    
    Dále název \< *účtu*> \< *odstraněný počet*> jedinečných souborů v jedné relaci.

- **Událost přihlášení z infikovaného zařízení vedoucí k hromadnému stahování souborů**
    
    Tato výstraha je označením události \<přihlášení podle *názvu účtu*> ze zařízení potenciálně infikovaného malwarem. 
    
    Dále> \< *název účtu* stažen \<přes počet> *jedinečných* souborů v jedné relaci.

- **Událost přihlášení z infikovaného zařízení vedoucího k zosobnění Office 365**
    
    Tato výstraha je označením události \<přihlášení podle *názvu účtu*> ze zařízení potenciálně infikovaného malwarem. 
    
    Dále název \< *účtu*> zosobněn přes \< *počet*> různých účtů v jedné relaci.

- **Událost přihlášení z infikovaného zařízení vedoucí k hromadnému sdílení souborů**
    
    Tato výstraha je označením události \<přihlášení podle *názvu účtu*> ze zařízení potenciálně infikovaného malwarem. 
    
    Dále> \< *název účtu* sdílen \<přes počet> *jedinečných* souborů v jedné relaci.

- **Událost přihlášení z infikovaného zařízení vedoucího k ransomwaru v cloudové aplikaci**
    
    Tato výstraha je označením události \<přihlášení podle *názvu účtu*> ze zařízení potenciálně infikovaného malwarem. 
    
    Dále> \< *název účtu* nahrál \< *počet*> souborů a \<odstranil celkem *počet* souborů>. 
    
    Tento vzorec aktivity svědčí o potenciálním útoku ransomware.

### <a name="sign-in-activity-from-anonymous-ip-address-followed-by-anomalous-office-365-activity"></a>Aktivita přihlášení z anonymní IP adresy následovaná neobvyklou aktivitou Office 365

Existuje sedm možných incidentů Azure Sentinelu, které kombinují aktivitu přihlášení z anonymních výstrah IP adres z Azure AD Identity Protection a neobvyklých výstrah Office 365 generovaných microsoftcloudovým zabezpečením aplikací:

- **Událost přihlášení z anonymní IP adresy vedoucí k exfiltraci poštovní schránky Office 365**
    
    Tato výstraha je označením události \<přihlášení *podle názvu účtu*> \<z adresy *IP* anonymní proxy adresy>, následovanou podezřelým pravidlem pro předávání doručené pošty, které bylo nastaveno v doručené poště uživatele.
    
    To může znamenat, že účet je ohrožena a že poštovní schránka se používá k exfiltraci informací z vaší organizace. Název \< *uživatelského účtu*> vytvořit nebo aktualizovat pravidlo předávání doručené pošty, \<které předává všechny příchozí e-maily na *e-mailovou adresu* externí adresy>. 

- **Událost přihlášení z anonymní IP adresy vedoucí k podezřelé aktivitě správy cloudových aplikací**
    
    Tato výstraha je označením události \<přihlášení podle *názvu účtu*> \<z ip *adresy* IP anonymní proxy adresy>. 
    
    Dále> \< *název účtu* proveden \<přes *počet*> aktivit správy v jedné relaci.

- **Událost přihlášení z anonymní IP adresy vedoucí k hromadnému odstranění souborů**
    
    Tato výstraha je označením události \<přihlášení podle *názvu účtu*> \<z ip *adresy* IP anonymní proxy adresy>. 
    
    Dále název \< *účtu*> \< *odstraněný počet*> jedinečných souborů v jedné relaci.

- **Událost přihlášení z anonymní IP adresy vedoucí k hromadnému stahování souborů**
    
    Tato výstraha je označením události \<přihlášení podle *názvu účtu*> \<z ip *adresy* IP anonymní proxy adresy>. 
    
    Dále> \< *název účtu* stažen \<přes počet> *jedinečných* souborů v jedné relaci.

- **Událost přihlášení z anonymní IP adresy vedoucí k zosobnění Office 365**
    
    Tato výstraha je označením události \<přihlášení podle *názvu účtu*> \<z ip *adresy* IP anonymní proxy adresy>. 
    
    Dále název \< *účtu*> zosobněn přes \< *počet*> různých účtů v jedné relaci.

- **Událost přihlášení z anonymní IP adresy vedoucí k hromadnému sdílení souborů**
    
    Tato výstraha je označením události \<přihlášení podle *názvu účtu*> \<z ip *adresy* IP anonymní proxy adresy>. 
    
    Dále> \< *název účtu* sdílen \<přes počet> *jedinečných* souborů v jedné relaci.

- **Událost přihlášení z anonymní IP adresy na ransomware v cloudové aplikaci**
    
    Tato výstraha je označením události \<přihlášení podle *názvu účtu*> \<z ip *adresy* IP anonymní proxy adresy>. 
    
    Dále> \< *název účtu* nahrál \< *počet*> souborů a \<odstranil celkem *počet* souborů>. 
    
    Tento vzorec aktivity svědčí o potenciálním útoku ransomware.

### <a name="sign-in-activity-from-user-with-leaked-credentials-followed-by-anomalous-office-365-activity"></a>Aktivita přihlášení od uživatele s uniklými přihlašovacími údaji následovanými neobvyklou aktivitou Office 365

Existuje sedm možných incidentů Azure Sentinelu, které kombinují přihlašovací aktivitu od uživatele s uniklými výstrahami přihlašovacích údajů z Azure AD Identity Protection a neobvyklými výstrahami Office 365 generovanými službou Microsoft Cloud App Security:

- **Událost přihlášení od uživatele s uniklými přihlašovacími údaji vedoucími k exfiltraci poštovní schránky Office 365**
    
    Tato výstraha označuje, že událost \<přihlášení podle *názvu účtu*> použita neuniklá pověření následovaná podezřelým pravidlem pro předávání doručené pošty, které bylo nastaveno v doručené poště uživatele. 
    
    To může znamenat, že účet je ohrožena a že poštovní schránka se používá k exfiltraci informací z vaší organizace. Název \< *uživatelského účtu*> vytvořit nebo aktualizovat pravidlo předávání doručené pošty, \<které předává všechny příchozí e-maily na *e-mailovou adresu* externí adresy>. 

- **Událost přihlášení od uživatele s uniklými přihlašovacími údaji vedoucími k podezřelé aktivitě správy cloudových aplikací**
    
    Tato výstraha označuje, že událost \<přihlášení podle *názvu účtu*> použita neuniklá pověření.
    
    Dále> \< *název účtu* proveden \<přes *počet*> aktivit správy v jedné relaci.

- **Událost přihlášení od uživatele s uniklými přihlašovacími údaji vedoucími k hromadnému odstranění souborů**
    
    Tato výstraha označuje, že událost \<přihlášení podle *názvu účtu*> použita neuniklá pověření.
    
    Dále název \< *účtu*> \< *odstraněný počet*> jedinečných souborů v jedné relaci.

- **Událost přihlášení od uživatele s uniklými přihlašovacími údaji vedoucími k hromadnému stahování souborů**
    
    Tato výstraha označuje, že událost \<přihlášení podle *názvu účtu*> použita neuniklá pověření.
    
    Dále> \< *název účtu* stažen \<přes počet> *jedinečných* souborů v jedné relaci.

- **Událost přihlášení od uživatele s uniklými přihlašovacími údaji vedoucími k zosobnění Office 365**
    
    Tato výstraha označuje, že událost \<přihlášení podle *názvu účtu*> použita neuniklá pověření. 
    
    Dále název \< *účtu*> zosobněn přes \< *počet*> různých účtů v jedné relaci.

- **Událost přihlášení od uživatele s uniklými přihlašovacími údaji vedoucími k hromadnému sdílení souborů**
    
    Tato výstraha označuje, že událost \<přihlášení podle *názvu účtu*> použita neuniklá pověření.
    
    Dále> \< *název účtu* sdílen \<přes počet> *jedinečných* souborů v jedné relaci.

- **Událost přihlášení od uživatele s uniklými přihlašovacími údaji k ransomwaru v cloudové aplikaci**
    
    Tato výstraha označuje, že událost \<přihlášení podle *názvu účtu*> použita neuniklá pověření. 
    
    Dále> \< *název účtu* nahrál \< *počet*> souborů a \<odstranil celkem *počet* souborů>. 
    
    Tento vzorec aktivity svědčí o potenciálním útoku ransomware.

## <a name="next-steps"></a>Další kroky

Nyní jste se dozvěděli více o pokročilé vícestupňové detekci útoků, které by vás mohly zajímat následující rychlý start, abyste zjistili, jak získat přehled o vašich datech a potenciálních hrozbách: [Začněte s Azure Sentinelem](quickstart-get-visibility.md).

Pokud jste připraveni prozkoumat incidenty, které jsou vytvořeny pro vás, podívejte se na následující kurz: [Prozkoumat incidenty s Azure Sentinel](tutorial-investigate-cases.md).

