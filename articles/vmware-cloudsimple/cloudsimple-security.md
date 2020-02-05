---
title: Řešení Azure VMware (AVS) – zabezpečení pro služby AVS
description: Popisuje sdílené zodpovědnosti modelů pro zabezpečení služby AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d2d45f827d4165175a4a5429f0b9393a55e2ff1e
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024989"
---
# <a name="avs-security-overview"></a>Přehled zabezpečení pro funkci AVS

Tento článek poskytuje přehled o tom, jak se zabezpečení implementuje v řešení Azure VMware pomocí služby AVS, infrastruktury a datacentra. Dozvíte se o ochraně dat a zabezpečení, zabezpečení sítě a způsobu, jakým se spravují ohrožení zabezpečení a opravy.

## <a name="shared-responsibility"></a>Sdílená odpovědnost

Řešení Azure VMware pomocí služby AVS používá ke zabezpečení sdílený model zodpovědnosti. Důvěryhodné zabezpečení v cloudu se dosahuje prostřednictvím sdílených odpovědností zákazníků a Microsoftu jako poskytovatele služeb. Tato matice zodpovědnosti poskytuje vyšší zabezpečení a eliminuje jednotlivé body selhání.

## <a name="azure-infrastructure"></a>Infrastruktura Azure

Mezi požadavky na zabezpečení infrastruktury Azure patří datacentra a umístění zařízení.

### <a name="datacenter-security"></a>Zabezpečení datového centra

Microsoft má celou divizi, která je věnována navrhování, sestavování a provozování fyzických zařízení podporujících Azure. Tento tým je investoval do zachování nejmodernějšího fyzického zabezpečení. Podrobnosti o fyzickém zabezpečení najdete v tématech [zařízení, místní a fyzické zabezpečení, které](../security/azure-physical-security.md)jsou v Azure.

### <a name="equipment-location"></a>Umístění zařízení

Úplné hardwarové zařízení, které spouští privátní cloudy služby AVS, je hostované v umístění datacentra Azure. V klecích, kde se toto zařízení nachází, se k získání přístupu vyžaduje biometrická ověřování pomocí dvou faktorů.

## <a name="dedicated-hardware"></a>Vyhrazený hardware

V rámci služby AVS mají všichni zákazníci využívající službu AVS vyhrazenou hostitele holých počítačů s místními připojenými disky, které jsou fyzicky izolované od jiného klientského hardwaru. Hypervisor pro ESXi s síti vSAN se spouští na všech uzlech. Uzly se spravují prostřednictvím vyhrazeného zákaznického serveru VMware vCenter a NSX. Nesdílení hardwaru mezi klienty poskytuje další úroveň izolace a ochrany zabezpečení.

## <a name="data-security"></a>Zabezpečení dat

Zákazníci si udrží kontrolu a vlastnictví svých dat. Data Stewardship zákaznických dat jsou zodpovědností zákazníka.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Ochrana dat v klidovém provozu a přenášených dat v rámci interních sítí

V případě neaktivních dat v prostředí privátního cloudu služby AVS můžete použít šifrování síti vSAN. šifrování síti vSAN funguje ve vaší vlastní virtuální síti nebo v místním prostředí pomocí serverů pro správu externích klíčů (KMS) s certifikací VMware. Šifrovací klíče dat můžete řídit sami. Pro přenos dat v rámci privátního cloudu služby AVS podporuje vSphere šifrování dat pro všechny přenosy VMKernel (včetně přenosů vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Ochrana dat pro data, která jsou nutná k přesunu přes veřejné sítě

Pokud chcete chránit data, která se pohybují přes veřejné sítě, můžete pro své privátní cloudy služby AVS vytvořit tunelové propojení VPN IPsec a SSL. Podporují se běžné metody šifrování, včetně 128-Byte a 256-Byte AES. Přenášená data (včetně ověřování, přístupu pro správu a zákaznických dat) jsou šifrovaná pomocí standardních šifrovacích mechanismů (SSH, TLS 1,2 a Secure RDP). Komunikace, která předává citlivé informace, používá standardní šifrovací mechanismy.

### <a name="secure-disposal"></a>Zabezpečené vyřazení

Pokud vaše služba AVS vyprší nebo se ukončí, zodpovídáte za odebrání nebo odstranění vašich dat. Služba AVS bude spolupracovat s tím, že bude odstraňovat nebo vracet veškerá zákaznická data, jak je uvedeno v rámci smlouvy o zákaznících, s výjimkou případů, kdy rozhodné právo vyžaduje, aby bylo možné zachovat některá nebo všechna osobní data. Pokud je třeba zachovat veškerá osobní data, funkce AVS data archivuje a implementuje přiměřená opatření, aby se zabránilo dalším zpracování zákaznických dat.

### <a name="data-location"></a>Umístění dat

Při nastavování privátních cloudů služby AVS si zvolíte oblast Azure, do které se budou nasazovat. Data virtuálního počítače VMware se z tohoto fyzického datacentra nepřesunou, pokud neprovádíte migraci dat nebo zálohu dat mimo pracoviště. Můžete také hostovat úlohy a ukládat data v několika oblastech Azure, pokud to vyhovuje vašim potřebám.

Zákaznická data, která jsou rezidentovaná v privátních uzlech Hyper-v, které jsou v cloudu založené na službě AVS, nepřecházejí umístění bez explicitní akce správce klienta. Vaše zodpovědnost za vaše úlohy můžete implementovat vysoce dostupným způsobem.

### <a name="data-backups"></a>Zálohování dat

Funkce AVS nezálohuje ani nearchivuje zákaznická data. V případě, že k zajištění vysoké dostupnosti serverů pro správu provádí aplikace AVS pravidelné zálohování dat vCenter a NSX. Před zálohováním se všechna data šifrují ve zdroji vCenter pomocí rozhraní API VMware. Šifrovaná data se přepravují a ukládají v objektu blob Azure. Šifrovací klíče pro zálohy se ukládají v vysoce zabezpečeném trezoru služby AVS spravovaném ve virtuální síti služby AVS v Azure.

## <a name="network-security"></a>Zabezpečení sítě

Řešení AVS spoléhá na vrstvy zabezpečení sítě.

### <a name="azure-edge-security"></a>Zabezpečení Azure Edge

Služby AVS jsou postavené na základním zabezpečení sítě, které poskytuje Azure. Azure používá k detekci a včasné reakci na síťové útoky spojené s neobvyklémi vstupními nebo výstupními vzory přenosů dat a distribuovaných útoků DDoS (Denial of Service). Toto řízení zabezpečení platí pro prostředí privátních cloudů pro funkci AVS a řídicí software roviny vyvinutý službou AVS.

### <a name="segmentation"></a>Segmentace

Služba AVS má logicky oddělené sítě vrstvy 2, které omezují přístup k vašim soukromým sítím v prostředí privátního cloudu služby AVS. Privátní cloudové sítě služby AVS můžete dál chránit pomocí brány firewall. Portál služby AVS umožňuje definovat síťová a NS pravidla řízení síťového provozu pro veškerý síťový provoz, včetně provozu privátního cloudu v rámci služby AVS, mezisíťovému přenosu dat privátního cloudu, obecného provozu na internetu a síťového provozu do místního počítače přes IPsec VPN nebo ExpressRoute připojení.

## <a name="vulnerability-and-patch-management"></a>Zabezpečení a správa oprav

Služby AVS zodpovídá za pravidelné opravy zabezpečení spravovaného softwaru VMware (ESXi, vCenter a NSX).

## <a name="identity-and-access-management"></a>Správa identit a přístupu

Zákazníci se můžou na svůj účet Azure (ve službě Azure AD) ověřit pomocí služby Multi-Factor Authentication nebo jednotného přihlašování jako upřednostňované. Z Azure Portal můžete spustit portál služby AVS bez opětovného zadání přihlašovacích údajů.

AVS podporuje volitelnou konfiguraci zdroje identity pro server služby AVS Private Cloud vCenter. Můžete použít [místní zdroj identity](set-vcenter-identity.md), nový zdroj identity pro privátní cloud služby AVS nebo [Azure AD](azure-ad.md).

Ve výchozím nastavení jsou zákazníkům udělena oprávnění, která jsou nezbytná pro každodenní operace vCenter v rámci privátního cloudu služby AVS. Tato úroveň oprávnění neobsahuje přístup pro správu k vCenter. Pokud je přístup pro správu dočasně vyžadován, můžete během provádění úloh správy zvýšit [vaše oprávnění](escalate-private-cloud-privileges.md) po dobu omezeného období.
