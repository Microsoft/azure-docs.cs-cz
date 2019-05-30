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
ms.openlocfilehash: f5f3fe32e03a9a2bb0186854a83917f8918c6647
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2019
ms.locfileid: "66358120"
---
# <a name="cloudsimple-security-overview"></a>Přehled zabezpečení CloudSimple

Tento článek obsahuje přehled, jak je implementovaná zabezpečení CloudSimple služeb, infrastruktury a datacenter.  Dozvíte se o ochranu dat a zabezpečení, zabezpečení sítě a jak se spravují ohrožení zabezpečení a opravy.

## <a name="shared-responsibility"></a>Sdílená odpovědnost

Řešení Azure VMware podle CloudSimple používá sdílenou odpovědnost model pro zabezpečení. Důvěryhodné zabezpečení v cloudu se dosahuje prostřednictvím sdíleného zodpovědnosti zákazníků a Microsoft jako poskytovatel služeb. Tato matice zodpovědnosti poskytuje vyšší úroveň zabezpečení a eliminuje způsobujícím selhání. 

## <a name="azure-infrastructure"></a>Infrastruktura Azure 

Aspekty zabezpečení infrastruktury Azure zahrnují umístění datových center a zařízení. 

### <a name="datacenter-security"></a>Zabezpečení datového centra 

Microsoft má celou divizi věnovaný navrhování, sestavování a provoz fyzické zařízení podporující Azure. Tento tým se investovalo stavu nejmodernější fyzické zabezpečení. Podrobnosti týkající se fyzického zabezpečení najdete v tématu [zařízení Azure, místním prostředí a fyzické zabezpečení](https://docs.microsoft.com/azure/security/azure-physical-security).

### <a name="equipment-location"></a>Umístění zařízení

Holé počítače hardwarové zařízení, na kterém běží privátních Cloudů je hostován v umístění datového centra Azure.  Klece, kde jsou zařízení, vyžaduje biometrické na základě dvoufaktorového ověřování pro přístup.

## <a name="dedicated-hardware"></a>Vyhrazený hardware

Jako součást služby CloudSimple získat všichni zákazníci CloudSimple vyhrazené holé počítače hostitele s místní připojenými disky, které jsou fyzicky oddělená od hardwaru jiné tenanty. Hypervisoru ESXi s sítě vSAN běží na všech uzlech. Uzly se spravují přes vyhrazené zákazníka VMware vCenter a NSX. Nesdílí hardwaru mezi tenanty poskytuje další úroveň izolace a zabezpečení ochrany.

## <a name="data-security"></a>Zabezpečení dat

Zákazníci zachovat ovládacího prvku a vlastnictví svá data. Správa dat zákaznických dat je odpovědnosti zákazníka.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Ochrana dat pro neaktivní uložená data a data přenášená do interní sítě

Pro data uložená v prostředí privátního cloudu můžete použít šifrování síti vSAN. síť vSAN šifrování funguje s VMware certified servery externí správy klíčů (KMS) ve virtuální síti nebo místní.  Šifrovací klíče dat určujete sami. Pro data přenášená data v rámci privátního cloudu vSphere podporuje šifrování dat při přenosu pro veškerý provoz vmkernel (včetně přenosů řešení vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Ochrana dat pro data, která se vyžaduje pro přechod na veřejných sítích

Pokud chcete chránit data, která prochází přes veřejné sítě, můžete vytvořit protokolu IPsec a SSL VPN tunely pro privátních Cloudů. Podporují se běžné metody šifrování, včetně AES 128 bajtů a 256 bajtů. Data přenášená (včetně ověřování, přístup pro správu a zákaznická data) jsou šifrována pomocí standardní šifrovací mechanismy (SSH, TLS 1.2 a zabezpečený protokol RDP). Komunikaci, která přenáší citlivé informace pomocí standardní šifrovací mechanismy.

### <a name="secure-disposal"></a>Ochraně 

Pokud vaše služba CloudSimple vyprší platnost nebo se ukončí, budete muset pro odebrání nebo odstranění vašich dat. CloudSimple spolupracovat s vámi odstranění nebo vrátit všechna data zákazníků, jak je uvedený v této smlouvě zákazníkovi, s výjimkou rozsahu CloudSimple nevyžaduje platný zákon zachovat některé nebo všechny osobní údaje. V případě potřeby zachovat všechny osobní údaje, se CloudSimple archivaci dat a implementovat přiměřená opatření, aby se zabránilo zákaznická data z dalšího zpracování.

### <a name="data-location"></a>Umístění dat

Při nastavování privátních Cloudů, zvolte oblast Azure, kde bude nasazen. Data virtuálního počítače VMware není z tohoto fyzického datacentra přesunout, pokud provádíte migraci dat nebo zálohování dat mimo pracoviště. Můžete hostovat úlohy a ukládání dat v několika oblastech Azure, pokud je to vhodné pro vaše potřeby.

Zákaznická data, která je v hyperkonvergované uzly privátního cloudu nebude procházet umístění bez explicitní akce správce tenanta. Je vaší odpovědností, abyste ve vysoce dostupným způsobem implementovat vaše úlohy.

### <a name="data-backups"></a>Zálohování dat
CloudSimple nepodporuje zálohování nebo archivaci dat zákazníků. CloudSimple provádět pravidelné zálohování serveru vCenter a NSX dat pro zajištění vysoké dostupnosti serveru pro správu. Před zálohováním všechna data zašifrovaná pomocí rozhraní API VMware vCenter zdroj. Šifrovaná data se přenášejí a uložená v Azure blob. Šifrovací klíče pro zálohy jsou uložené v trezoru vysoce zabezpečených spravovaných CloudSimple používané CloudSimple virtuální sítě v Azure.

## <a name="network-security"></a>Zabezpečení sítě

Řešení CloudSimple spoléhá na vrstvy zabezpečení sítě.

### <a name="azure-edge-security"></a>Zabezpečení Azure edge

CloudSimple služby jsou postavené na zabezpečení základní sítě poskytovaný platformou Azure. Azure se vztahuje defense-in-depth techniky pro zjišťování a včas odpověď síťové útoky související s neobvyklou příchozí nebo odchozí provoz vzory a distribuovanými útoky s cílem odepření služeb (DDoS). Tento ovládací prvek zabezpečení se vztahuje na prostředí privátního cloudu a software pro rovinu řízení vypracovanou organizací cccppf CloudSimple.

### <a name="segmentation"></a>Segmentace

Služba CloudSimple má logicky oddělené sítě vrstvy 2, které omezují přístup k privátním sítím ve vašem prostředí privátního cloudu. Jde dál chránit svoje privátního cloudu sítě pomocí brány firewall. CloudSimple Portál umožňuje definovat nové a NS pravidla síťových přenosů ovládací prvky pro veškerý přenos v síti, včetně uvnitř provoz privátního cloudu, přenosy dat mezi privátním cloudu, obecné provoz do Internetu a síťový provoz do místní přes síť VPN typu IPsec nebo Připojení ExpressRoute.

## <a name="vulnerability-and-patch-management"></a>Ohrožení zabezpečení a správu oprav 

CloudSimple zodpovídá za použití dílčích oprav zabezpečení spravovaného softwaru VMware (ESXi, vCenter a NSX).

## <a name="identity-and-access-management"></a>Správa identit a přístupu

Zákazníci ověřit jejich účtu Azure (Azure AD) pomocí služby Multi-Factor authentication nebo jednotného přihlašování jako upřednostňovaný. Na webu Azure Portal můžete spustit CloudSimple portál bez nutnosti opětovného zadávání přihlašovacích údajů.

CloudSimple podporuje volitelná konfigurace zdroj identity pro vCenter privátního cloudu. Můžete použít [zdroj identity v místním](https://docs.azure.cloudsimple.com/set-vcenter-identity), nový zdroj identity pro privátní Cloud, nebo [Azure AD](https://docs.azure.cloudsimple.com/azure-ad).

Ve výchozím nastavení disponují zákazníci oprávnění, které jsou nezbytné pro každodenní operace vCenter v rámci privátního cloudu. Tuto úroveň oprávnění neobsahuje přístup správce k serveru vCenter. Pokud se dočasně vyžaduje přístup správce, můžete si [eskalovat oprávnění](https://docs.azure.cloudsimple.com/escalate-private-cloud-privileges) po omezenou dobu při dokončování úlohy správy.

## <a name="next-steps"></a>Další postup

* Zjistěte, jak [vytvoření CloudSimple služby v Azure](quickstart-create-cloudsimple-service.md)
* Zjistěte, jak [vytvoření privátního cloudu](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Zjistěte, jak [konfigurace prostředí privátního cloudu](quickstart-create-private-cloud.md)
