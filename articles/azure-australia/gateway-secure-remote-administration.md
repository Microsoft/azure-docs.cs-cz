---
title: Zabezpečení vzdálené správy vaší brány v Azure Austrálie
description: Pokyny ke konfiguraci zabezpečené vzdálené správy v rámci australských oblastí pro splnění konkrétních požadavků na zásady australské vlády, předpisy a právní předpisy.
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 1e4c4712312faf2274a4a0737c4fc1f7ce39f98e
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68824188"
---
# <a name="secure-remote-administration-of-your-gateway-in-azure-australia"></a>Zabezpečení vzdálené správy vaší brány v Azure Austrálie

Je zásadní pro dostupnost a integritu všech systémů, které provádí zabezpečené aktivity správy a jsou řízeny. Aktivity správy by se měly provádět ze zabezpečeného zařízení, přes zabezpečené připojení a musí být zajištěny pomocí silných procesů ověřování a autorizace. Zabezpečená Vzdálená správa zajišťuje provádění jenom schválených akcí a jenom autorizovaných správců.

Tento článek poskytuje informace o implementaci zabezpečené funkce vzdálené správy pro internetový dostupný systém hostovaný v Azure, který je v souladu se zákaznickými příručkami k ACSC (australských Internet Security Center) a záměrem informací o ACSC. Bezpečnostní příručka (ISM).

## <a name="australian-cyber-security-centre-acsc-requirements"></a>Požadavky na středisko ACSC (australských Internet Security Center)

Celkové požadavky na zabezpečení pro systémy Společenství jsou definované v rámci ISM. Pro pomoc subjektům Společenství při poskytování bezpečné správy zveřejnila [acsc acsc ochranu: Zabezpečená správa](https://www.acsc.gov.au/publications/protect/secure-administration.htm)

Tento dokument popisuje důležitost zabezpečené správy a navrhuje jednu z metod implementace zabezpečeného prostředí pro správu. Dokument popisuje prvky řešení zabezpečené správy následujícím způsobem:

|Prvek   |Popis   |
|---|---|
|Řízení privilegovaného přístupu   |Řízení přístupu k privilegovaným účtům je základní ovládací prvek zabezpečení, který bude chránit privilegované účty před zneužitím. Metodologie řízení přístupu bude zahrnovat koncepty "minimální oprávnění" a "potřebu" a také postupy a postupy pro správu účtů služeb a pohybů zaměstnanců.   |
|Vícefaktorové ověřování   |Implementace dalších faktorů ověřování nad rámec uživatelských jmen a hesel, jako jsou fyzické tokeny nebo čipové karty, může přispět k ochraně důležitých prostředků. Pokud nežádoucí osoba ohrozí přihlašovací údaje pro privilegované účty, protože všechny akce správy by nejdřív musely projít určitou formou služby Multi-Factor Authentication, můžou být důsledky výrazně omezené.|
|Privilegované pracovní stanice|Použití známého zabezpečeného prostředí pro úlohy správy může mít za následek menší riziko napadení sítě z důvodu implementace dalších bezpečnostních mechanismů.|
|Protokolování a auditování   |Automatizované generování, shromažďování a analýza událostí souvisejících se zabezpečením a správou z pracovních stanic, serverů, síťových zařízení a seznamů odkazů umožní detekci ohrožení a pokus o ohrožení zabezpečení. Automatizace umožňuje organizacím reagovat rychleji a snížit dopad ohrožení.|
|Segmentace a dělení sítě|Segmentace sítě do logických zón, jako jsou například rozdílné domény zabezpečení, a další oddělení těchto logických sítí omezením typů dat, která se pohybují z jedné zóny do druhé, omezuje boční pohyb. Segmentace brání nežádoucí osoba získat přístup k dalším prostředkům.|
|Pole s odkazy|Pole s odkazem je server s posíleným vzdáleným přístupem, který běžně využívá software služby Vzdálená plocha nebo Secure Shell (SSH) společnosti Microsoft. Pole s odkazy slouží jako bod krokování pro správce, kteří přistupují k důležitým systémům se všemi akcemi správy provedenými z vyhrazeného hostitele.|

Tento článek poskytuje referenční architekturu, jak se výše uvedené prvky dají použít k zabezpečení správy systémů nasazených v Azure.

## <a name="architecture"></a>Architektura

Poskytování zabezpečených možností správy vyžaduje více komponent, které dohromady spolupracují za účelem vytvoření soudržného řešení. V rámci referenční architektury jsou komponenty mapovány na prvky popsané v [acsc chránit: Zabezpečená správa](https://www.acsc.gov.au/publications/protect/secure-administration.htm)

![Architektura zabezpečené vzdálené správy Azure](media/remote-admin.png)

## <a name="components"></a>Komponenty

Architektura je navržená tak, aby zajistila, že privilegovaný účet má udělena jenom potřebná oprávnění, je zabezpečený a pak poskytuje přístup k rozhraním pro správu jenom z autorizovaného zařízení a prostřednictvím zabezpečené komunikace. mechanismy, které se kontrolují a auditují.

|Řešení| Komponenty|Elementy|
|---|---|---|
|Zabezpečená zařízení |<ul><li>Privilegovaná pracovní stanice</li><li>Mobilní zařízení</li><li>Microsoft Intune</li><li>Zásada skupiny</li><li>Server skoku/hostitel bastionu</li><li>Správa JIT (just in time)</li></ul> |<ul><li>Privilegované pracovní stanice</li><li>Pole s odkazy</li></ul>|
|Zabezpečená komunikace |<ul><li>portál Azure</li><li>Azure VPN Gateway</li><li>Brána vzdálené plochy (RD)</li><li>Skupiny zabezpečení sítě (skupin zabezpečení sítě)</li></ul> |<ul><li>Segmentace a dělení sítě</li></ul>|
|Silné ověřování |<ul><li>Řadič domény (DC)</li><li>Azure Active Directory (Azure AD)</li><li>Server NPS (Network Policy Server)</li><li>Azure MFA</li></ul> |<ul><li>Vícefaktorové ověřování</li></ul> |
|Silné oprávnění |<ul><li>Správa identit a přístupu (IAM)</li><li>Privileged Identity Management (PIM)</li><li>Podmíněný přístup</li></ul>|<ul><li>Řízení privilegovaného přístupu</li></ul>|
|||

>[!NOTE]
>Další informace o elementu Logging a audit najdete v článku [protokolování, auditování a viditelnost brány](gateway-log-audit-visibility.md) .

## <a name="administration-workflow"></a>Pracovní postup správy

Správa systémů nasazených v Azure je rozdělená do dvou různých kategorií, což je Správa konfigurace Azure a správa úloh nasazených v Azure. Konfigurace Azure se provádí prostřednictvím Azure Portal a správa úloh se dokončuje prostřednictvím mechanismů správy, jako je protokol RDP (Remote Desktop Protocol) (RDP), Secure Shell (SSH) nebo PaaS, pomocí nástrojů, jako je SQL Management Studio.

Získání přístupu pro správu je proces s více kroky zahrnující komponenty uvedené v architektuře a vyžaduje přístup k Azure Portal a konfiguraci Azure před tím, než bude možné provést přístup k úlohám Azure.

>[!NOTE]
> Postup popsaný tady je obecný proces využívající komponenty grafického uživatelského rozhraní (GUI) Azure. Tyto kroky se dají provést taky pomocí jiných rozhraní, jako je PowerShell.

### <a name="azure-configuration-and-azure-portal-access"></a>Konfigurace Azure a přístup k Azure Portal

|Krok |Popis |
|---|---|
|Přihlášení privilegované pracovní stanice |Správce přihlásí privilegovanou pracovní stanici pomocí pověření správce. Zásady skupiny ovládací prvky brání účtům bez oprávnění správce v ověřování v privilegované pracovní stanici a brání účtům pro správu v ověřování pro neprivilegované pracovní stanice. Microsoft Intune spravuje dodržování předpisů privilegované pracovní stanice, aby se zajistilo aktuálnost softwarových oprav, antimalwaru a dalších požadavků na dodržování předpisů. |
|Přihlášení Azure Portal |Správce otevře webový prohlížeč na Azure Portal, který je zašifrovaný pomocí protokolu TLS (Transport Layer Security), a přihlásí se pomocí přihlašovacích údajů správce. Požadavek na ověření se zpracovává prostřednictvím Azure Active Directory přímo nebo prostřednictvím ověřovacích mechanismů, jako je Active Directory Federation Services (AD FS) (AD FS) nebo předávací ověřování. |
|Azure MFA |Azure MFA pošle požadavek na ověření do zaregistrovaného mobilního zařízení privilegovaného účtu. Mobilní zařízení spravuje Intune, aby se zajistilo dodržování požadavků na zabezpečení. Aby se mohl pokus o ověření povolit pro Azure MFA, musí se nejdřív ověřit u mobilního zařízení a potom do aplikace Microsoft Authenticator pomocí PIN nebo biometrického systému. |
|Podmíněný přístup |Zásady podmíněného přístupu kontrolují pokus o ověření, aby splňovaly nezbytné požadavky, jako je třeba IP adresa, ze které připojení pochází, členství ve skupině pro privilegovaný účet a stav správy a dodržování předpisů. Privilegovaná pracovní stanice uvedená v Intune |
|Privileged Identity Management (PIM) |Prostřednictvím Azure Portal může správce nyní aktivovat nebo požádat o aktivaci privilegovaných rolí, pro které mají oprávnění prostřednictvím PIM. PIM zajišťuje, že privilegované účty nemají žádná stálá oprávnění správce a že všechny požadavky na privilegovaný přístup jsou jenom za dobu potřebnou k provádění úloh správy. PIM poskytuje také protokolování všech požadavků a aktivací pro účely auditování. |
|Správa identit a přístupu|Po bezpečné identifikaci privilegovaného účtu a aktivaci rolí správce poskytne přístup k předplatným Azure a prostředkům, ke kterým jim byla přiřazena oprávnění prostřednictvím správy identit a přístupu.|

Jakmile privilegovaný účet dokončí kroky pro získání přístupu pro správu k Azure Portal, je možné nakonfigurovat přístup k pracovním postupům a vytvořit připojení pro správu.

### <a name="azure-workload-administration"></a>Správa úloh Azure

|Krok |Popis|
|---|---|
|Přístup k JIT (just in time)|Chcete-li získat přístup k virtuálním počítačům, správce použije JIT k vyžádání přístupu k protokolu RDP na server skoku z Brána VP IP adresy a protokolu RDP nebo SSH ze serveru skoku na příslušné virtuální počítače s úlohami.|
|Azure VPN Gateway|Správce nyní zřídí připojení VPN typu Point-to-Site z své privilegované pracovní stanice do Azure VPN Gateway, které provádí ověřování pomocí certifikátů k navázání připojení.|
|Brána VP|Správce teď pokusí připojení RDP k serveru skoku s Brána VP zadanou v konfiguraci Připojení ke vzdálené ploše. Brána VP má privátní IP adresu, která je dosažitelná prostřednictvím připojení Azure VPN Gateway. Zásady Brána VP určují, jestli je privilegovaný účet autorizovaný pro přístup k požadovanému serveru skoku. Brána VP vyzve správce k zadání přihlašovacích údajů a předá požadavek na ověření serveru NPS (Network Policy Server).|
|Server NPS (Network Policy Server)|Server NPS obdrží požadavek na ověření od Brána VP a před odesláním žádosti o Azure Active Directory k aktivaci žádosti o ověření Azure MFA ověří uživatelské jméno a heslo proti službě Active Directory.|
|Azure MFA|Azure MFA pošle požadavek na ověření do zaregistrovaného mobilního zařízení privilegovaného účtu. Mobilní zařízení spravuje Intune, aby se zajistilo dodržování požadavků na zabezpečení. Aby se mohl pokus o ověření povolit pro Azure MFA, musí se nejdřív ověřit u mobilního zařízení a potom do aplikace Microsoft Authenticator pomocí PIN nebo biometrického systému.|
|Server pro skok|Po úspěšném ověření je připojení RDP šifrované pomocí protokolu TLS (Transport Layer Security) a pak se prostřednictvím Brána VP a na serveru skoku pošle prostřednictvím šifrovaného tunelu IPSec do Azure VPN Gateway. Na serveru skoku může správce nyní pomocí protokolu RDP nebo SSH vymezit virtuálním počítačům úlohy, jak je uvedeno v požadavku JIT.|

## <a name="general-guidance"></a>Obecné pokyny

Při implementaci součástí uvedených v tomto článku platí následující obecné pokyny:

* Ověřte dostupnost oblasti služeb a zajistěte, aby všechna data zůstala v povolených umístěních, a jako první předvolby pro chráněné úlohy nasaďte na AU Central nebo AU Central 2.

* Seznamte se s *certifikací certifikace Azure-acsc – chráněná 2018* pro stav certifikace jednotlivých služeb a provádět vlastní posouzení všech relevantních komponent, které nejsou součástí sestavy, podle *příručky pro příjemce acsc – Microsoft Azure v CHRÁNĚNých*

* Ujistěte se, že připojení k síti a veškerá potřebná konfigurace proxy serveru pro přístup k potřebným ověřovacím součástem, jako je například Azure AD, ADFS a PTA

* Použití Azure Policy k monitorování a prosazování dodržování předpisů pomocí požadavků

* Ujistěte se, že virtuální počítače, zejména řadiče Doména služby Active Directory, jsou uložené v šifrovaných účtech úložiště a využívají Azure Disk Encryption

* Vytváření a údržba robustních procesů správy identit a oprávnění správce a zásad správného řízení pro podporu technických kontrol uvedených v tomto článku

|Resource|URL|
|---|---|
|Australské dokumenty a předpisy o dodržování předpisů v australském pořádku|[Australské dokumenty a předpisy o dodržování předpisů v australském pořádku](https://aka.ms/au-irap)|
|Produkty Azure – australské oblasti a jiné než regionální|[Produkty Azure – australské oblasti a jiné než regionální](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Strategie pro zmírnění rizikových incidentů zabezpečení pro Internet|[Strategie pro zmírnění rizikových incidentů zabezpečení pro Internet](https://acsc.gov.au/infosec/mitigationstrategies.htm)|
|ACSC chránit: Zabezpečená správa|[ACSC chránit: Zabezpečená správa](https://acsc.gov.au/publications/protect/secure-administration.htm)|
|Jak: Integrace služby Brána vzdálené plochy infrastrukturu s použitím rozšíření serveru NPS (Network Policy Server) a Azure AD|[Integrace Brána VP se serverem NPS a službou Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-nps-extension-rdg)|

## <a name="component-guidance"></a>Doprovodné materiály k komponentám

V této části najdete informace o účelu každé součásti a její roli v celkové architektuře zabezpečené vzdálené správy. K dispozici jsou další odkazy pro přístup k užitečným prostředkům, jako jsou referenční dokumentace, příručky a kurzy.

## <a name="secure-devices"></a>Zabezpečená zařízení

Fyzická zařízení, která používají privilegované uživatele k provádění funkcí správy, jsou pro škodlivé objekty actor cennou cílem. Klíčovou součástí poskytování zabezpečených funkcí vzdálené správy je zachování zabezpečení a integrity fyzických zařízení a zajištění, že jsou od škodlivého softwaru volná a chrání je před ohrožením. To zahrnuje konfiguraci zabezpečení s vysokou prioritou, jak je uvedeno v základních osmi strategiích pro ACSC, aby se zmírnily počítačové bezpečnostní incidenty, jako je seznam povolených aplikací, opravy aplikací, posílení zabezpečení aplikací a opravy operačních systémů. Tyto funkce musí být nainstalované, nakonfigurované, auditovány, ověřené a nahlášené, aby se zajistilo, že stav zařízení splňuje požadavky organizace.

### <a name="privileged-workstation"></a>Privilegovaná pracovní stanice

Privilegovaný pracovní stanice je posílený počítač, který se dá použít k provádění úloh správy a je dostupný jenom pro účty správců. V případě, že má privilegované pracovní stanice omezit software, který se dá spustit, měl by být přístup k síťovým prostředkům a Internetu a přihlašovací údaje chráněny v případě, že je zařízení odcizené nebo ohrožené. |

|Zdroje a prostředky|Odkaz|
|---|---|
|Přehled architektury privilegovaného přístupu pracovních stanic|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)|
|Zabezpečení privilegovaného přístupu – referenční materiál|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)|

### <a name="mobile-device"></a>Mobilní zařízení

Mobilní zařízení má větší riziko náhodné ztráty nebo krádeže z důvodu jeho přenositelnosti a velikosti a je potřeba ho správně zabezpečit. Mobilní zařízení poskytuje silný dodatečný faktor pro ověřování, který má za způsobilou možnost vyhovět ověřování pro přístup k zařízení, sledovatelnost prostřednictvím služeb zjišťování polohy, šifrovací funkce a možnost vzdáleného vymazání. Pokud používáte mobilní zařízení jako dodatečný faktor ověřování pro Azure, zařízení by mělo být nakonfigurované tak, aby používalo Microsoft Authenticator aplikaci s PIN kódem nebo biometrické ověřování, a ne prostřednictvím telefonních hovorů nebo textových zpráv.

|Zdroje a prostředky|Odkaz|
|---|---|
|Metody ověřování Azure AD|[https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)|
|Jak používat aplikaci Microsoft Authenticator|[https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app](https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app)|

### <a name="microsoft-intune"></a>Microsoft Intune

Intune je součást Enterprise Mobility + Security, která spravuje mobilní zařízení a aplikace. Úzce se integruje s dalšími součástmi, jako jsou Azure Active Directory pro řízení identit a přístupu a Azure Information Protection pro ochranu dat. Intune poskytuje zásady pro pracovní stanice a mobilní zařízení k nastavování požadavků na dodržování předpisů pro přístup k prostředkům a poskytuje možnosti vytváření sestav a auditování pro získání přehledu o stavu zařízení pro správu.

|Zdroje a prostředky|Odkaz|
|---|---|
|Dokumentace k Microsoft Intune|[https://docs.microsoft.com/intune/](https://docs.microsoft.com/intune/)|
|Začínáme s dodržováním předpisů zařízením v Intune|[https://docs.microsoft.com/intune/device-compliance-get-started](https://docs.microsoft.com/intune/device-compliance-get-started)|

### <a name="group-policy"></a>Zásada skupiny

Zásady skupiny slouží k řízení konfigurace operačních systémů a aplikací. Zásady zabezpečení řídí nastavení ověřování, autorizace a auditování systému. Zásady skupiny slouží k posílení privilegované pracovní stanice, ochraně přihlašovacích údajů pro správu a omezení účtů, které Neprivilegovaný přístup využívají k přístupu k privilegovaným zařízením.

|Zdroje a prostředky|Odkaz|
|---|---|
|Povolení nastavení přihlášení Zásady skupiny lokálně|[https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally](https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally)|

### <a name="jump-server--bastion-host"></a>Server skoku/hostitel bastionu

Server skoku/hostitel bastionu je centralizovaným bodem pro správu. Má nástroje potřebné k provedení administrativních úloh, ale má také přístup k síti, který je nezbytný pro připojení k prostředkům na portech pro správu. Server odkazů je centrálním bodem pro správu úloh virtuálních počítačů v tomto článku, ale je možné ho nakonfigurovat taky jako autorizovaný bod pro správu funkcí platformy jako služby (PaaS), jako je SQL. Přístup k funkcím PaaS lze omezit na základě jednotlivých služeb pomocí ovládacích prvků identity a sítě.

|Zdroje a prostředky|Odkaz|
|---|---|
|Implementace zabezpečených hostitelů pro správu|[https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts)|

### <a name="just-in-time-jit-access"></a>Přístup k JIT (just in time)

JIT je Azure Security Center schopnost, která využívá skupiny zabezpečení sítě (skupin zabezpečení sítě) k blokování přístupu k protokolům pro správu, jako jsou RDP a SSH, na Virtual Machines. Aplikace hostované v Virtual Machines nadále fungují jako normální, ale aby bylo možné získat přístup pro správu, musí se požádat o jeho udělení pouze na nastavené časové období. Všechny požadavky jsou protokolovány pro účely auditování.

|Zdroje a prostředky |Odkaz |
|---|---|
|Správa přístupu JIT (just in time)|[https://docs.microsoft.com/azure/security-center/security-center-just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)|
|Automatizace přístupu k virtuálnímu počítači podle potřeby Azure|[https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access](https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access)|

## <a name="secure-communication"></a>Zabezpečená komunikace

Komunikační přenos pro aktivity správy může obsahovat vysoce citlivé informace, jako jsou přihlašovací údaje správce, a musí se proto spravovat a chránit. Poskytování zabezpečené komunikace zahrnuje spolehlivé možnosti šifrování, aby se zabránilo odposlouchávání a segmentaci sítě a omezení, které omezují přenos pro správu do autorizovaných koncových bodů, a řídí pohyb v případě ohrožení systému.

### <a name="azure-portal"></a>portál Azure

Komunikace s Azure Portal je šifrovaná pomocí protokolu TLS (Transport Layer Security) a použití Azure Portal bylo certifikováno ACSC. Entity Společenství by měly dodržovat doporučení v *příručce pro příjemce acsc* a nakonfigurovat jejich webové prohlížeče tak, aby používaly nejnovější verzi TLS a podporované kryptografické algoritmy.

|Zdroje a prostředky |Odkaz |
|---|---|
|Přehled šifrování Azure – šifrování při přenosu|[https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit](https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit)|

### <a name="azure-vpn-gateway"></a>Azure VPN Gateway

Azure VPN Gateway poskytuje zabezpečené šifrované připojení z privilegované pracovní stanice do Azure. Služba Azure VPN Gateway byla certifikována ACSCem pro zajištění zabezpečené komunikace protokolu IPSec. Entity Společenství by měly nakonfigurovat VPN Gateway Azure v souladu se zákaznickou příručkou ACSC, zprávou o certifikaci ACSC a dalšími konkrétními pokyny.

|Zdroje a prostředky |Odkaz |
|---|---|
|Připojení Point-to-site|[https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about](https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about)|
|Podrobnosti o šifrování Azure VPN Gateway|[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto)|
|Konfigurace Azure VPN Gateway|[Konfigurace Azure VPN Gateway](vpn-gateway.md)|

### <a name="remote-desktop-rd-gateway"></a>Brána vzdálené plochy (RD)

Brána VP je zabezpečený mechanismus pro řízení a povolování připojení RDP k systémům. Funguje tak, že zapouzdření provozu RDP v protokolu HTTPS (HyperText Transfer Protocol Secure) a šifruje pomocí protokolu TLS. Protokol TLS poskytuje další úroveň zabezpečení pro správu provozu.

|Zdroje a prostředky |Odkaz |
|---|---|
|Architektura služby Vzdálená plocha|[https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)|

### <a name="network-security-groups-nsgs"></a>Skupiny zabezpečení sítě (skupin zabezpečení sítě)

Skupin zabezpečení sítě jako seznamy pro Access Control (seznamy ACL) pro síťový provoz, který vstupuje do podsítě nebo virtuální počítače. Skupin zabezpečení sítě poskytuje segmentaci sítě a poskytuje mechanismus pro řízení a omezování komunikačních toků povolených mezi systémy. Skupin zabezpečení sítě jsou základní součástí správy právě v čase (JIT) pro povolení nebo odepření přístupu k protokolům pro správu.

|Zdroje a prostředky |Odkaz |
|---|---|
|Přehled skupin zabezpečení Azure|[https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)|
|Postup: Plánování virtuálních sítí|[https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|

## <a name="strong-authentication"></a>Silné ověřování

Základní součástí zabezpečené správy je bezpečné identifikaci privilegovaných uživatelů před udělením přístupu k systémům. K ochraně přihlašovacích údajů přidružených k privilegovanému účtu a k tomu, aby se zabránilo škodlivým aktérům v získání přístupu k systémům prostřednictvím zosobnění nebo krádeže přihlašovacích údajů, musí být mechanismy na místě.

### <a name="domain-controller-dc"></a>Řadič domény (DC)

Na vysoké úrovni řadič domény hostuje kopii databáze služby Active Directory, která obsahuje všechny uživatele, počítače a skupiny v rámci domény. Řadiče domény provádějí ověřování pro uživatele a počítače. Řadiče domény v této architektuře se hostují jako virtuální počítače v Azure a poskytují ověřovací služby pro privilegované účty, které se připojují k serverům odkazů a virtuálním počítačům s úlohami.

|Zdroje a prostředky |Odkaz |
|---|---|
|Přehled Active Directory Domain Services|[https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)|

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Azure AD je ověřovací služba pro Azure. Obsahuje Cloud.

identity a poskytuje ověřování a oprávnění pro prostředí Azure. Služba Azure AD se dá v rámci služby Active Directory s využitím Azure AD Connect a může poskytovat federované ověřování prostřednictvím Active Directory Federation Services (AD FS) (AD FS) a Azure AD Connect. Azure AD je základní součástí zabezpečené správy.

|Zdroje a prostředky |Odkaz |
|---|---|
|Dokumentace k Azure Active Directory|[https://docs.microsoft.com/azure/active-directory](https://docs.microsoft.com/azure/active-directory)|
|Dokumentace k hybridní identitě|[https://docs.microsoft.com/azure/active-directory/hybrid](https://docs.microsoft.com/azure/active-directory/hybrid)|

### <a name="network-policy-server-nps"></a>Server NPS (Network Policy Server)

NPS je server pro ověřování a zásady, který poskytuje pokročilé procesy ověřování a autorizace. Server NPS v této architektuře se poskytuje pro integraci ověřování Azure MFA s požadavky na Brána VP ověřování. Server NPS má konkrétní modul plug-in, který podporuje integraci s Azure MFA ve službě Azure AD.

|Zdroje a prostředky |Odkaz |
|---|---|
|Dokumentace k serveru NPS (Network Policy Server)|[https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top)|

### <a name="azure-mfa"></a>Azure MFA

Azure MFA je ověřovací služba poskytovaná v rámci Azure Active Directory, která povoluje žádosti o ověření nad rámec uživatelského jména a hesla pro přístup k prostředkům cloudu, jako je Azure Portal. Azure MFA podporuje řadu metod ověřování a tato architektura využívá aplikaci Microsoft Authenticator pro rozšířené zabezpečení a integraci se serverem NPS.

|Zdroje a prostředky |Odkaz |
|---|---|
|Jak to funguje: Služba Azure Multi-Factor Authentication|[https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)|
|Postup: Nasadit cloudovou službu Azure Multi-Factor Authentication|[https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)|

## <a name="strong-authorisation"></a>Silné oprávnění

Jakmile je privilegovaný účet zabezpečený, může mu být udělen přístup k prostředkům. Řízení a Správa oprávnění, která jsou přiřazena konkrétnímu účtu. Silné autorizační procesy se zarovnají s ACSCou základní osmi strategii pro zmírnění rizikových incidentů zabezpečení, které omezují oprávnění správce.

### <a name="identity-and-access-management"></a>Správa identit a přístupu

Přístup k provádění privilegovaných akcí v rámci Azure je založený na rolích, které jsou přiřazené k tomuto účtu. Azure zahrnuje rozsáhlou a podrobnější škálu rolí s konkrétními oprávněními k provádění konkrétních úkolů. Tyto role je možné udělit na více úrovních, jako je například předplatné nebo skupina prostředků. Přiřazení rolí a Správa oprávnění jsou založené na účtech a skupinách v Azure Active Directory a spravují se prostřednictvím Access Control (IAM) v Azure.

|Zdroje a prostředky |Odkaz |
|---|---|
|Access Control na základě rolí Azure|[https://docs.microsoft.com/azure/role-based-access-control](https://docs.microsoft.com/azure/role-based-access-control)|
|Pochopení definic rolí|[https://docs.microsoft.com/azure/role-based-access-control/role-definitions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions)|

### <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

PIM je Azure Active Directory komponenta, která řídí přístup k privilegovaným rolím. Privilegované účty nevyžadují trvalý nebo stálý privilegovaný přístup, ale můžou se místo toho udělit možnost požádat o privilegovaný přístup po určitou dobu, aby bylo možné aktivity s privilegovaným přístupem dokončit. PIM poskytuje další kontroly týkající se údržby a omezení privilegovaného přístupu a protokolování a auditování, které sledují instance používání oprávnění.

|Zdroje a prostředky |Odkaz |
|---|---|
|Privileged Identity Management (PIM) – dokumentace|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)|
|Zahájení práce s využitím PIM|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started)|

### <a name="conditional-access"></a>podmíněný přístup

Podmíněný přístup je součást Azure Active Directory, která umožňuje nebo odmítá přístup k prostředkům na základě podmínek. Tyto podmínky můžou být síťové umístění podle umístění, typ zařízení, stav dodržování předpisů, členství ve skupině a další. Podmíněný přístup se používá k prosazování MFA, správy zařízení a dodržování předpisů prostřednictvím služby Intune a členství ve skupinách účtů pro správu.

|Zdroje a prostředky |Odkaz |
|---|---|
|Dokumentace k podmíněnému přístupu|[https://docs.microsoft.com/azure/active-directory/conditional-access](https://docs.microsoft.com/azure/active-directory/conditional-access)|
|Postup: Vyžadovat pro cloudovou aplikaci přístup spravovaných zařízení s podmíněným přístupem|[https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)|

## <a name="next-steps"></a>Další postup

Podrobnosti o řízení toku přenosů přes komponenty brány v Azure najdete v článku [Správa a řízení provozu brány](gateway-ingress-traffic.md) .
