---
title: Řešení Azure VMware od CloudSimple – zabezpečení pro cloudové jednoduché služby
description: Popisuje modely sdílené odpovědnosti za zabezpečení služeb CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1a33e20ec540a05885eb13a3828d28ffc9923fff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024989"
---
# <a name="cloudsimple-security-overview"></a>Přehled zabezpečení CloudSimple

Tento článek poskytuje přehled o tom, jak je zabezpečení implementováno v řešení Azure VMware službou CloudSimple službou, infrastrukturou a datovým centrem. Dozvíte se o ochraně a zabezpečení dat, zabezpečení sítě a o tom, jak jsou spravovány chyby zabezpečení a opravy.

## <a name="shared-responsibility"></a>Společná odpovědnost

Azure VMware Solution by CloudSimple používá model sdílené odpovědnosti za zabezpečení. Důvěryhodného zabezpečení v cloudu je dosaženo prostřednictvím sdílených odpovědností zákazníků a společnosti Microsoft jako poskytovatele služeb. Tato matice odpovědnosti poskytuje vyšší zabezpečení a eliminuje jednotlivé body selhání.

## <a name="azure-infrastructure"></a>Infrastruktura Azure

Mezi důležité informace o zabezpečení infrastruktury Azure patří datová centra a umístění zařízení.

### <a name="datacenter-security"></a>Zabezpečení datového centra

Microsoft má celou divizi věnovanou navrhování, vytváření a provozování fyzických zařízení, která podporují Azure. Tento tým je investován do udržování nejmodernější fyzické bezpečnosti. Podrobnosti o fyzickém zabezpečení najdete v [tématu zařízení Azure, prostory a fyzické zabezpečení](../security/azure-physical-security.md).

### <a name="equipment-location"></a>Umístění zařízení

Holé kovové hardwarové vybavení, které spouští vaše privátní cloudy, je hostované v umístěních datových center Azure.  Klece, kde je toto zařízení, vyžadují biometrickou dvoufaktorovou autentizaci, aby získaly přístup.

## <a name="dedicated-hardware"></a>Vyhrazený hardware

Jako součást služby CloudSimple všichni zákazníci CloudSimple získat vyhrazené holé kovové hostitele s místní připojené disky, které jsou fyzicky izolované od jiného hardwaru klienta. Hypervisor ESXi s vSAN běží na každém uzlu. Uzly jsou spravovány prostřednictvím zákazníků vyhrazených vMware vCenter a NSX. Nesdílení hardwaru mezi klienty poskytuje další vrstvu izolace a ochrany zabezpečení.

## <a name="data-security"></a>Zabezpečení dat

Zákazníci si udržují kontrolu a vlastnictví svých dat. Správcovství dat o zákaznících je odpovědností zákazníka.

### <a name="data-protection-for-data-at-rest-and-data-in-motion-within-internal-networks"></a>Ochrana údajů pro data v klidovém stavu a data v pohybu v rámci interních sítí

Pro data v klidovém stavu v prostředí Privátního cloudu můžete použít šifrování vSAN. Šifrování vSAN funguje s externími servery pro správu klíčů (KMS) certifikovanými společností VMware ve vaší virtuální síti nebo v místním prostředí.  Šifrovací klíče dat můžete ovládat sami. Pro data v pohybu v rámci privátního cloudu podporuje vSphere šifrování dat přes drát pro všechny přenosy vmkernel (včetně provozu vMotion).

### <a name="data-protection-for-data-that-is-required-to-move-through-public-networks"></a>Ochrana údajů pro údaje, které jsou nutné pro pohyb prostřednictvím veřejných sítí

Chcete-li chránit data, která se pohybují prostřednictvím veřejných sítí, můžete vytvořit tunely IPsec a SSL VPN pro privátní cloudy. Jsou podporovány běžné metody šifrování, včetně 128bajtových a 256bajtových AES. Data při přenosu (včetně ověřování, přístupu pro správu a zákaznických dat) jsou šifrována standardními šifrovacími mechanismy (SSH, TLS 1.2 a Secure RDP). Komunikace, která přenáší citlivé informace, používá standardní šifrovací mechanismy.

### <a name="secure-disposal"></a>Bezpečná likvidace

Pokud vaše služba CloudSimple vyprší nebo je ukončena, jste zodpovědní za odebrání nebo odstranění dat. CloudSimple s vámi bude spolupracovat na odstranění nebo vrácení všech zákaznických dat, jak je uvedeno ve smlouvě se zákazníkem, s výjimkou rozsahu, do kterého cloudsimple vyžaduje příslušný zákon k uchování některých nebo všech osobních údajů. Pokud je to nezbytné pro uchovávání jakýchkoli osobních údajů, CloudSimple bude archivovat data a provádět přiměřená opatření, aby se zabránilo zákaznických dat z dalšího zpracování.

### <a name="data-location"></a>Umístění dat

Při nastavování privátních cloudů zvolíte oblast Azure, kde se budou nasazovat. Data virtuálního počítače VMware se z tohoto fyzického datového centra nepřesouvají, pokud neprovedete migraci dat nebo zálohování dat mimo pracoviště. Můžete také hostovat úlohy a ukládat data v rámci více oblastí Azure, pokud je to vhodné pro vaše potřeby.

Zákaznická data, která jsou rezidentní v hyperkonvergovaných uzlech Privátního cloudu, neprocházejí umístěníbez explicitní akce správce klienta. Je vaší odpovědností implementovat vaše úlohy vysoce dostupným způsobem.

### <a name="data-backups"></a>Zálohy dat

CloudSimple nezálohuje ani archivuje zákaznická data. CloudSimple provádí pravidelné zálohování dat vCenter a NSX, aby poskytoval vysokou dostupnost serverů pro správu. Před zálohováním jsou všechna data šifrována ve zdroji vCenter pomocí rozhraní API vmware. Šifrovaná data se depozitovaná a uložená v objektu blob Azure. Šifrovací klíče pro zálohy jsou uloženy ve vysoce zabezpečeném spravovaném trezoru CloudSimple spuštěném ve virtuální síti CloudSimple v Azure.

## <a name="network-security"></a>Zabezpečení sítě

CloudSimple řešení závisí na vrstvách zabezpečení sítě.

### <a name="azure-edge-security"></a>Zabezpečení azure edge

CloudSimple služby jsou postavené na základní zabezpečení sítě poskytované Azure. Azure používá techniky ochrany do hloubky pro detekci a včasnou reakci na útoky založené na síti spojené s neobvyklé příchozí nebo odchozí provoz vzory a distribuované denial-of-service (DDoS) útoky. Tento ovládací prvek zabezpečení se vztahuje na prostředí privátního cloudu a software řídicí roviny vyvinutý společností CloudSimple.

### <a name="segmentation"></a>Segmentation

Služba CloudSimple má logicky samostatné sítě vrstvy 2, které omezují přístup k vlastním privátním sítím ve vašem prostředí privátního cloudu. Privátní cloudové sítě můžete dále chránit pomocí brány firewall. Portál CloudSimple umožňuje definovat pravidla řízení síťového provozu EW a NS pro veškerý síťový provoz, včetně provozu v rámci privátního cloudu, provozu mezi privátním cloudem, obecného provozu na internetu a síťového provozu do místní sítě přes Síť Ovou VPN nebo ExpressRoute připojení.

## <a name="vulnerability-and-patch-management"></a>Správa chyb zabezpečení a oprav

CloudSimple je zodpovědný za pravidelné opravy zabezpečení spravovaného softwaru VMware (ESXi, vCenter a NSX).

## <a name="identity-and-access-management"></a>Správa identit a přístupu

Zákazníci můžou ověřit svůj účet Azure (ve službě Azure AD) pomocí vícefaktorového ověřování nebo s přistahou k vlastnímu přistajení jako upřednostňované. Na webu Azure Portal můžete spustit portál CloudSimple bez opětovného zadání přihlašovacích údajů.

CloudSimple podporuje volitelnou konfiguraci zdroje identity pro privátní cloud vCenter. Můžete použít [místní zdroj identity](set-vcenter-identity.md), nový zdroj identity pro privátní cloud nebo [Azure AD](azure-ad.md).

Ve výchozím nastavení mají zákazníci oprávnění, která jsou nezbytná pro každodenní provoz centra vCenter v rámci privátního cloudu. Tato úroveň oprávnění nezahrnuje přístup správce k vCenter. Pokud je dočasně vyžadován přístup správce, můžete [oprávnění eskalovat](escalate-private-cloud-privileges.md) po omezenou dobu, zatímco budete plnit úkoly správy.
