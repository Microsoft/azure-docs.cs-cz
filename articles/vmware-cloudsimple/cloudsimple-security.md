---
title: Zabezpečení pro služby CloudSimple Services
description: Popisuje sdílené zodpovědnosti modelů pro zabezpečení služeb CloudSimple Services.
author: sharaths-cs
ms.author: b-shsury
ms.date: 04/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ff1bd3c6e1f3cf98e92e10eecf972681ed6c7819
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68816177"
---
# <a name="cloudsimple-security-overview"></a>Přehled zabezpečení CloudSimple

Tento článek poskytuje přehled o tom, jak se zabezpečení implementuje v řešení Azure VMware pomocí služby CloudSimple, infrastruktury a datacentra. Dozvíte se o ochraně dat a zabezpečení, zabezpečení sítě a způsobu, jakým se spravují ohrožení zabezpečení a opravy.

## <a name="shared-responsibility"></a>Sdílená odpovědnost

Řešení Azure VMware podle CloudSimple používá ke zabezpečení sdílený model zodpovědnosti. Důvěryhodné zabezpečení v cloudu se dosahuje prostřednictvím sdílených odpovědností zákazníků a Microsoftu jako poskytovatele služeb. Tato matice zodpovědnosti poskytuje vyšší zabezpečení a eliminuje jednotlivé body selhání.

## <a name="azure-infrastructure"></a>Infrastruktura Azure 

Mezi požadavky na zabezpečení infrastruktury Azure patří datacentra a umístění zařízení.

### <a name="datacenter-security"></a>Zabezpečení datového centra 

Microsoft má celou divizi, která je věnována navrhování, sestavování a provozování fyzických zařízení podporujících Azure. Tento tým je investoval do zachování nejmodernějšího fyzického zabezpečení. Další informace o fyzickém zabezpečení najdete v tématech [zařízení Azure, místní a fyzické zabezpečení] (https://docs.microsoft.com/azure/security/fundamentals/physical-security.

### <a name="equipment-location"></a>Umístění zařízení

Holé hardwarové zařízení, které spouští vaše privátní cloudy, je hostované v umístění datacentra Azure. Pro získání přístupu k pracovním postupům, kde se toto zařízení nachází, je nutné použít dvojúrovňové ověřování pomocí vícefaktorového měření.

## <a name="dedicated-hardware"></a>Vyhrazený hardware

V rámci služby CloudSimple získají všichni zákazníci CloudSimple vyhrazeného holého hostitele s místními připojenými disky, které jsou fyzicky izolované od jiného klientského hardwaru. Hypervisor pro ESXi s síti vSAN se spouští na všech uzlech. Uzly se spravují prostřednictvím VMware, vCenter a NSX vyhrazených zákazníkem. Nesdílení hardwaru mezi klienty poskytuje další úroveň izolace a ochrany zabezpečení.

## <a name="data-security"></a>Zabezpečení dat

Zákazníci si udrží kontrolu a vlastnictví svých dat. Data Stewardship zákaznických dat jsou zodpovědností zákazníka.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Ochrana dat v klidovém provozu a přenášených dat v rámci interních sítí

Pro neaktivní data v prostředí privátního cloudu můžete použít šifrování síti vSAN. šifrování síti vSAN funguje ve vaší vlastní virtuální síti nebo v místním prostředí pomocí serverů pro správu externích klíčů (KMS) s certifikací VMware. Šifrovací klíče dat můžete řídit sami. Pro přenos dat v rámci privátního cloudu vSphere podporuje šifrování dat pro všechny přenosy VMkernel, což zahrnuje přenos vMotion.

### <a name="data-protection-for-data-thats-required-to-move-through-public-networks"></a>Ochrana dat pro data, která jsou nutná k přesunu přes veřejné sítě

K ochraně dat, která se pohybují přes veřejné sítě, můžete pro privátní cloudy vytvořit tunelová propojení VPN IPsec a SSL. Podporují se běžné metody šifrování, včetně 128-Byte a 256-Byte AES. Přenášená data, která zahrnují ověřování, přístup pro správu a zákaznická data, se šifrují pomocí standardních šifrovacích mechanismů, jako jsou SSH, TLS 1,2 a Secure RDP. Komunikace, která předává citlivé informace, používá standardní šifrovací mechanismy.

### <a name="secure-disposal"></a>Zabezpečené vyřazení 

Pokud vaše služba CloudSimple vyprší nebo se ukončí, zodpovídáte za odebrání nebo odstranění vašich dat. CloudSimple spolupracuje s tím, že vám umožňuje odstranit nebo vrátit veškerá zákaznická data, jak jsou uvedena v rámci smlouvy o zákaznících, s výjimkou případů, kdy rozhodné právo vyžaduje, aby se některá nebo všechna osobní data zachovala v rozsahu CloudSimple. Pokud je to nutné pro uchovávání osobních údajů, CloudSimple archivuje data a implementuje přiměřená opatření k tomu, aby se zabránilo dalším zpracování zákaznických dat.

### <a name="data-location"></a>Umístění dat

Při nastavování privátních cloudů zvolíte oblast Azure, ve které se nasazují. Data virtuálních počítačů VMware se z tohoto fyzického datacentra nepřesunou, Pokud neprovedete migraci dat nebo zálohu dat mimo pracoviště. Můžete také hostovat úlohy a ukládat data v několika oblastech Azure, pokud to vyhovuje vašim potřebám.

Zákaznická data, která jsou rezidentovaná v privátním cloudu, probíhají uzly bez nutnosti explicitního zásahu správce tenanta. Vaše zodpovědnost za vaše úlohy můžete implementovat vysoce dostupným způsobem.

### <a name="data-backups"></a>Zálohování dat
CloudSimple nemůže zálohovat nebo archivovat zákaznická data. CloudSimple provádí pravidelná zálohování dat vCenter a NSX, aby poskytovala vysokou dostupnost serverů pro správu. Před zálohováním se všechna data šifrují ve zdroji vCenter pomocí rozhraní API VMware. Šifrovaná data se přepravují a ukládají do objektu blob Azure. Šifrovací klíče pro zálohy se ukládají ve vysoce zabezpečeném úložišti CloudSimple spravovaném trezorem, který běží ve virtuální síti CloudSimple v Azure.

## <a name="network-security"></a>Zabezpečení sítě

Řešení CloudSimple spoléhá na vrstvy zabezpečení sítě.

### <a name="azure-edge-security"></a>Zabezpečení Azure Edge

Služby CloudSimple jsou postavené na základním zabezpečení sítě, které poskytuje Azure. Azure používá k detekci a včasné reakci na síťové útoky spojené s neobvyklémi vstupními nebo výstupními vzory přenosů dat a distribuovaných útoků DDoS (Denial of Service). Toto řízení zabezpečení platí pro prostředí privátních cloudů a řídicí software roviny vyvinutý pomocí CloudSimple.

### <a name="segmentation"></a>Segmentace

Služba CloudSimple logicky odděluje sítě vrstvy 2, které omezují přístup k vašim soukromým sítím v prostředí privátního cloudu. Privátní cloudové sítě můžete dále chránit pomocí brány firewall. Na portálu CloudSimple definujete pravidla řízení přenosů dat v oblasti USA – západ a sever-jih pro veškerý síťový provoz, který zahrnuje privátní cloudový provoz, meziprivátní cloudový provoz, obecný provoz na internetu a síťový provoz do místního prostředí. přes IPsec VPN nebo připojení Azure ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Zabezpečení a správa oprav 

CloudSimple zodpovídá za pravidelné opravy zabezpečení spravovaného softwaru VMware, jako je třeba ESXi, vCenter a NSX.

## <a name="identity-and-access-management"></a>Správa identit a přístupu

Zákazníci se můžou k účtu Azure (v Azure Active Directory) ověřit pomocí vícefaktorového ověřování nebo jednotného přihlašování jako preferované. Z Azure Portal můžete spustit portál CloudSimple bez opětovného zadání přihlašovacích údajů.

CloudSimple podporuje volitelnou konfiguraci zdroje identity pro Server vCenter privátního cloudu. Můžete použít [místní zdroj identity](https://docs.azure.cloudsimple.com/set-vcenter-identity), nový zdroj identity pro daný privátní cloud nebo [Azure Active Directory](https://docs.azure.cloudsimple.com/azure-ad).

Ve výchozím nastavení jsou zákazníkům udělena oprávnění, která jsou nezbytná pro každodenní operace vCenter v rámci privátního cloudu. Tato úroveň oprávnění neobsahuje přístup pro správu k vCenter. Pokud je přístup pro správu dočasně vyžadován, můžete během provádění úloh správy zvýšit [vaše oprávnění](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges) po dobu omezeného období.

## <a name="next-steps"></a>Další postup

* Naučte se [vytvořit službu CloudSimple v Azure](quickstart-create-cloudsimple-service.md).
* Přečtěte si, jak [vytvořit privátní cloud](https://docs.azure.cloudsimple.com/create-private-cloud/).
* Naučte se [Konfigurovat prostředí privátního cloudu](quickstart-create-private-cloud.md).
