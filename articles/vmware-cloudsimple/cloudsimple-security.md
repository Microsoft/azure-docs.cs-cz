---
title: Zabezpečení služeb CloudSimple
description: Popisuje modely sdílená odpovědnost u zabezpečení CloudSimple služeb
author: sharaths-cs
ms.author: b-shsury
ms.date: 4/27/19
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: db093f6aef4f1bdb28a96bf89b3013d359a7b796
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154112"
---
# <a name="cloudsimple-security-overview"></a>Přehled zabezpečení CloudSimple

Tento článek obsahuje základní informace o způsobu implementace zabezpečení na řešení VMware Azure CloudSimple služeb, infrastruktury a datacenter. Dozvíte se o ochranu dat a zabezpečení, zabezpečení sítě a jak se spravují ohrožení zabezpečení a opravy.

## <a name="shared-responsibility"></a>Sdílená odpovědnost

Řešení Azure VMware podle CloudSimple používá sdílenou odpovědnost model pro zabezpečení. Důvěryhodné zabezpečení v cloudu se dosahuje prostřednictvím sdíleného zodpovědnosti zákazníků a Microsoft jako poskytovatel služeb. Tato matice zodpovědnosti poskytuje vyšší úroveň zabezpečení a eliminuje způsobujícím selhání.

## <a name="azure-infrastructure"></a>Infrastruktura Azure 

Aspekty zabezpečení infrastruktury Azure zahrnují umístění datových center a zařízení.

### <a name="datacenter-security"></a>Zabezpečení datového centra 

Microsoft má celou divizi věnovaný navrhování, sestavování a provoz fyzické zařízení, která podporují Azure. Tento tým se investovalo stavu nejmodernější fyzické zabezpečení. Další informace týkající se fyzického zabezpečení najdete v tématu [zařízení Azure, místním prostředí a fyzické zabezpečení](https://docs.microsoft.com/azure/security/azure-physical-security).

### <a name="equipment-location"></a>Umístění zařízení

Holého hardwaru zařízení, na kterém běží privátních cloudů je hostován v umístění datového centra Azure. Na základě biometrické dvojúrovňového ověřování je potřeba získat přístup k klece, ve kterém se zařízení nachází.

## <a name="dedicated-hardware"></a>Vyhrazený hardware

Jako součást služby CloudSimple všem zákazníkům CloudSimple získání vyhrazené úplné obnovení hostitelů s místní připojenými disky, které jsou fyzicky oddělená od hardwaru jiné tenanty. Hypervisoru ESXi s sítě vSAN běží na všech uzlech. Uzly se spravují přes vyhrazené zákazníka VMware vCenter a NSX. Nesdílí hardwaru mezi tenanty poskytuje další úroveň izolace a zabezpečení ochrany.

## <a name="data-security"></a>Zabezpečení dat

Zákazníci zachovat ovládacího prvku a vlastnictví svá data. Správa dat zákaznických dat je odpovědnosti zákazníka.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Ochrana dat pro neaktivní uložená data a data přenášená do interní sítě

Pro data uložená v prostředí privátního cloudu můžete použít šifrování síti vSAN. síť vSAN šifrování funguje s servery VMware certified externí správy klíčů (KMS) ve virtuální síti nebo místní. Šifrovací klíče dat určujete sami. Pro data přenášená data v rámci privátního cloudu vSphere podporuje šifrování dat při přenosu pro veškerý provoz VMkernel, která zahrnuje provoz řešení vMotion.

### <a name="data-protection-for-data-thats-required-to-move-through-public-networks"></a>Ochrana dat pro data, která je nutné pro přechod na veřejných sítích

Pokud chcete chránit data, která prochází přes veřejné sítě, můžete vytvořit protokolu IPsec a SSL VPN tunely pro privátní cloudy. Podporují se běžné metody šifrování, včetně AES 128 bajtů a 256 bajtů. Data při přenosu, která obsahuje zákaznická data, ověřování a přístup pro správu, se šifrují pomocí standardní šifrovací mechanismy, jako jsou SSH, TLS 1.2 a zabezpečený protokol RDP. Komunikaci, která přenáší citlivé informace pomocí standardní šifrovací mechanismy.

### <a name="secure-disposal"></a>Ochraně 

Pokud vaše služba CloudSimple vyprší platnost nebo se ukončí, zodpovídáte za odebrání nebo odstranění vaše data. CloudSimple spolupracuje s můžete odstranit nebo vrátit všechna data zákazníků, jak je uvedený v této smlouvě zákazníkovi, s výjimkou rozsahu CloudSimple nevyžaduje platný zákon zachovat některé nebo všechny osobní údaje. V případě potřeby zachovat všechny osobní údaje, CloudSimple archivaci dat a implementuje přiměřená opatření, aby se zabránilo zákaznická data z dalšího zpracování.

### <a name="data-location"></a>Umístění dat

Když nastavíte privátních cloudů, zvolte oblast Azure, ve kterých jsou nasazeny. Data virtuálního počítače VMware není z tohoto fyzického datacentra přesunout, pokud provádíte migraci dat nebo zálohování dat mimo pracoviště. Také můžete hostovat úlohy a ukládání dat v několika oblastech Azure, pokud je to vhodné pro vaše potřeby.

Zákaznická data, která je v uzlech hyperkonvergovaného privátního cloudu nebude procházet umístění bez explicitní akce správce tenanta. Je vaší odpovědností, abyste ve vysoce dostupným způsobem implementovat vaše úlohy.

### <a name="data-backups"></a>Zálohování dat
CloudSimple nepodporuje zálohování nebo archivaci dat zákazníků. CloudSimple provádět pravidelné zálohování serveru vCenter a NSX dat pro zajištění vysoké dostupnosti serveru pro správu. Před zálohováním všechna data se šifrují ve zdrojovém serveru vCenter pomocí rozhraní API pro VMware. Šifrovaná data se přenášejí a uložená v objektu blob Azure. Šifrovací klíče pro zálohy jsou uložené ve vysoce zabezpečených CloudSimple spravované trezor, na kterém běží ve virtuální síti CloudSimple v Azure.

## <a name="network-security"></a>Zabezpečení sítě

Řešení CloudSimple spoléhá na vrstvy zabezpečení sítě.

### <a name="azure-edge-security"></a>Zabezpečení Azure edge

CloudSimple služby jsou postavené na zabezpečení základní sítě poskytovaný platformou Azure. Azure se vztahuje defense-in-depth techniky pro zjišťování a včas odpověď síťové útoky související s neobvyklou příchozí nebo odchozí provoz vzory a distribuovanými útoky s cílem odepření služeb (DDoS). Tento ovládací prvek zabezpečení platí pro prostředí privátních cloudů a software pro rovinu řízení vypracovanou organizací cccppf CloudSimple.

### <a name="segmentation"></a>Segmentace

Služba CloudSimple má logicky oddělené sítě vrstvy 2, které omezují přístup k privátním sítím ve vašem prostředí privátního cloudu. Vaše sítě privátního cloudu jde dál chránit pomocí brány firewall. Na portálu CloudSimple můžete definovat pravidla pro řízení provoz typu east-west a sever jih sítě pro veškerý přenos v síti, která zahrnuje přenosy dat uvnitř privátního cloudu, přenosy dat mezi privátního cloudu, obecné provoz do Internetu a síťový provoz do místní přes připojení s IPsec VPN nebo Azure ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Správa ohrožení zabezpečení a opravy 

CloudSimple zodpovídá za spravovaného softwaru VMware, jako je například ESXi a vCenter, NSX opravy chyb zabezpečení.

## <a name="identity-and-access-management"></a>Správa identit a přístupu

Zákazníky můžete ověřit ke svému účtu Azure (ve službě Azure Active Directory) pomocí vícefaktorového ověřování nebo jednotného přihlašování jako upřednostňovaný. Na webu Azure Portal můžete spustit portál CloudSimple bez nutnosti opětovného zadávání přihlašovacích údajů.

CloudSimple podporuje volitelná konfigurace zdroj identity pro vCenter privátního cloudu. Můžete použít [zdroj identity v místním](https://docs.azure.cloudsimple.com/set-vcenter-identity), nový zdroj identity pro daný privátní cloud, nebo [Azure Active Directory](https://docs.azure.cloudsimple.com/azure-ad).

Ve výchozím nastavení disponují zákazníci oprávnění, které jsou nezbytné pro každodenní operace vCenter v rámci privátního cloudu. Tuto úroveň oprávnění neobsahuje přístup správce k serveru vCenter. Pokud se dočasně vyžaduje přístup správce, můžete si [eskalovat oprávnění](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges) po omezenou dobu při dokončování úlohy správy.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [v Azure vytvořit službu CloudSimple](quickstart-create-cloudsimple-service.md).
* Zjistěte, jak [vytvoření privátního cloudu](https://docs.azure.cloudsimple.com/create-private-cloud/).
* Zjistěte, jak [konfigurace prostředí privátního cloudu](quickstart-create-private-cloud.md).
