---
title: Řešení Azure VMware podle CloudSimple – zabezpečení pro služby CloudSimple
description: Popisuje sdílené zodpovědnosti modelů pro zabezpečení služeb CloudSimple Services.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d62eec72a884470ade03897a0533e62c6f1b0f71
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898789"
---
# <a name="cloudsimple-security-overview"></a>Přehled zabezpečení CloudSimple

Tento článek poskytuje přehled o tom, jak se zabezpečení implementuje v řešení Azure VMware pomocí služby CloudSimple, infrastruktury a datacentra. Dozvíte se o ochraně dat a zabezpečení, zabezpečení sítě a způsobu, jakým se spravují ohrožení zabezpečení a opravy.

## <a name="shared-responsibility"></a>Společná odpovědnost

Řešení Azure VMware podle CloudSimple používá ke zabezpečení sdílený model zodpovědnosti. Důvěryhodné zabezpečení v cloudu se dosahuje prostřednictvím sdílených odpovědností zákazníků a Microsoftu jako poskytovatele služeb. Tato matice zodpovědnosti poskytuje vyšší zabezpečení a eliminuje jednotlivé body selhání.

## <a name="azure-infrastructure"></a>Infrastruktura Azure

Mezi požadavky na zabezpečení infrastruktury Azure patří datacentra a umístění zařízení.

### <a name="datacenter-security"></a>Zabezpečení datového centra

Microsoft má celou divizi, která je věnována navrhování, sestavování a provozování fyzických zařízení podporujících Azure. Tento tým je investoval do zachování nejmodernějšího fyzického zabezpečení. Podrobnosti o fyzickém zabezpečení najdete v tématech [zařízení, místní a fyzické zabezpečení, které](../security/fundamentals/physical-security.md)jsou v Azure.

### <a name="equipment-location"></a>Umístění zařízení

Úplné hardwarové zařízení, které spouští vaše privátní cloudy, je hostované v umístění datacentra Azure.  V klecích, kde se toto zařízení nachází, se k získání přístupu vyžaduje biometrická ověřování pomocí dvou faktorů.

## <a name="dedicated-hardware"></a>Vyhrazený hardware

V rámci služby CloudSimple získají všichni zákazníci CloudSimple vyhrazené holé hostitele s místními připojenými disky, které jsou fyzicky izolované od jiného klientského hardwaru. Hypervisor pro ESXi s síti vSAN se spouští na všech uzlech. Uzly se spravují prostřednictvím vyhrazeného zákaznického serveru VMware vCenter a NSX. Nesdílení hardwaru mezi klienty poskytuje další úroveň izolace a ochrany zabezpečení.

## <a name="data-security"></a>Zabezpečení dat

Zákazníci si udrží kontrolu a vlastnictví svých dat. Data Stewardship zákaznických dat jsou zodpovědností zákazníka.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Ochrana dat v klidovém provozu a přenášených dat v rámci interních sítí

Pro neaktivní data v prostředí privátního cloudu můžete použít šifrování síti vSAN. šifrování síti vSAN funguje ve vaší vlastní virtuální síti nebo v místním prostředí pomocí serverů pro správu externích klíčů (KMS) s certifikací VMware.  Šifrovací klíče dat můžete řídit sami. Pro data přenášená v rámci privátního cloudu podporuje vSphere šifrování dat pro všechny přenosy VMKernel (včetně přenosů vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Ochrana dat pro data, která jsou nutná k přesunu přes veřejné sítě

Pokud chcete chránit data, která se pohybují přes veřejné sítě, můžete pro privátní cloudy vytvořit tunelová propojení sítě VPN IPsec a TLS. Podporují se běžné metody šifrování, včetně 128-Byte a 256-Byte AES. Přenášená data (včetně ověřování, přístupu pro správu a zákaznických dat) jsou šifrovaná pomocí standardních šifrovacích mechanismů (SSH, TLS 1,2 a Secure RDP). Komunikace, která předává citlivé informace, používá standardní šifrovací mechanismy.

### <a name="secure-disposal"></a>Zabezpečené vyřazení

Pokud vaše služba CloudSimple vyprší nebo se ukončí, zodpovídáte za odebrání nebo odstranění vašich dat. CloudSimple bude spolupracovat s tím, že bude odstraňovat nebo vracet veškerá zákaznická data, která jsou poskytována smlouvou o zákaznících, s výjimkou případů, kdy rozhodné právo vyžaduje, aby se některá nebo všechna osobní data zachovala v rozsahu CloudSimple. Pokud je to nutné pro uchovávání osobních údajů, CloudSimple archivuje data a implementuje přiměřená opatření, aby se zabránilo dalším zpracování zákaznických dat.

### <a name="data-location"></a>Umístění dat

Při nastavování privátních cloudů zvolíte oblast Azure, do které se budou nasazovat. Data virtuálního počítače VMware se z tohoto fyzického datacentra nepřesunou, pokud neprovádíte migraci dat nebo zálohu dat mimo pracoviště. Můžete také hostovat úlohy a ukládat data v několika oblastech Azure, pokud to vyhovuje vašim potřebám.

Zákaznická data, která jsou rezidentovaná v uzlech Hyper-v privátním cloudu, se procházejí umístěními bez explicitní akce správce klienta. Vaše zodpovědnost za vaše úlohy můžete implementovat vysoce dostupným způsobem.

### <a name="data-backups"></a>Zálohy dat

CloudSimple nemůže zálohovat nebo archivovat zákaznická data. CloudSimple provádí pravidelná zálohování dat vCenter a NSX, aby poskytovala vysokou dostupnost serverů pro správu. Před zálohováním se všechna data šifrují ve zdroji vCenter pomocí rozhraní API VMware. Šifrovaná data se přepravují a ukládají v objektu blob Azure. Šifrovací klíče pro zálohy se ukládají ve vysoce zabezpečeném trezoru CloudSimple, který běží ve virtuální síti CloudSimple v Azure.

## <a name="network-security"></a>Zabezpečení sítě

Řešení CloudSimple spoléhá na vrstvy zabezpečení sítě.

### <a name="azure-edge-security"></a>Zabezpečení Azure Edge

Služby CloudSimple jsou postavené na základním zabezpečení sítě, které poskytuje Azure. Azure používá k detekci a včasné reakci na síťové útoky spojené s neobvyklémi vstupními nebo výstupními vzory přenosů dat a distribuovaných útoků DDoS (Denial of Service). Toto řízení zabezpečení platí pro prostředí privátních cloudů a řídicí software roviny vyvinutý pomocí CloudSimple.

### <a name="segmentation"></a>Segmentation

Služba CloudSimple logicky odděluje sítě vrstvy 2, které omezují přístup k vašim soukromým sítím v prostředí privátního cloudu. Privátní cloudové sítě můžete dále chránit pomocí brány firewall. Portál CloudSimple umožňuje definovat síťová a NS pravidla řízení síťového provozu pro veškerý síťový provoz, včetně provozu uvnitř privátního cloudu, meziprivátního cloudového provozu, obecného provozu na internetu a síťového provozu do místního počítače přes připojení IPsec VPN nebo ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Zabezpečení a správa oprav

CloudSimple zodpovídá za pravidelné opravy zabezpečení spravovaného softwaru VMware (ESXi, vCenter a NSX).

## <a name="identity-and-access-management"></a>Správa identit a přístupu

Zákazníci se můžou na svůj účet Azure (ve službě Azure AD) ověřit pomocí služby Multi-Factor Authentication nebo jednotného přihlašování jako upřednostňované. Z Azure Portal můžete spustit portál CloudSimple bez opětovného zadání přihlašovacích údajů.

CloudSimple podporuje volitelnou konfiguraci zdroje identity pro Server vCenter privátního cloudu. Můžete použít [místní zdroj identity](set-vcenter-identity.md), nový zdroj identity pro privátní cloud nebo [Azure AD](azure-ad.md).

Ve výchozím nastavení jsou zákazníkům udělena oprávnění, která jsou nezbytná pro každodenní operace vCenter v rámci privátního cloudu. Tato úroveň oprávnění neobsahuje přístup pro správu k vCenter. Pokud je přístup pro správu dočasně vyžadován, můžete během provádění úloh správy zvýšit [vaše oprávnění](escalate-private-cloud-privileges.md) po dobu omezeného období.
