---
title: Přehled infrastruktury BareMetal ve verzi Preview v Azure
description: Přehled infrastruktury BareMetal v Azure
ms.custom: references_regions
ms.topic: conceptual
ms.date: 1/4/2021
ms.openlocfilehash: eb4dc129719dc410f7101598e3d72e68f17809c1
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2021
ms.locfileid: "97860989"
---
#  <a name="what-is-baremetal-infrastructure-preview-on-azure"></a>Co je BareMetal infrastruktura ve verzi Preview v Azure?

Infrastruktura Azure BareMetal poskytuje zabezpečené řešení pro migraci podnikových vlastních úloh. Instance BareMetal jsou vám přiřazený hardware, který není sdílený pro hostitele nebo server. Odemyká vaše řešení Prem s využitím specializovaných úloh, které vyžadují certifikované hardwarové, licencování a smlouvy o podpoře. Azure zajišťuje monitorování a údržbu infrastruktury za vás a monitorování aplikací v hostovaném operačním systému (OS) a monitorování aplikací spadá do svého vlastnictví.

Infrastruktura BareMetal poskytuje cestu k modernizovatí vaší infrastruktury na šířku a zároveň přitom udržuje stávající investice a architekturu. S infrastrukturou BareMetal můžete do Azure přinášet specializované úlohy, které vám umožní přístup a integraci se službami Azure s nízkou latencí.

## <a name="sku-availability-in-azure-regions"></a>Dostupnost skladové položky v oblastech Azure
K dispozici je infrastruktura BareMetal pro úlohy specializované na obecné účely, počínaje čtyřmi oblastmi na základě revizí 4,2 (Rev 4,2) razítek:
- West Europe
- Severní Evropa
- USA – východ 2
- Středojižní USA

>[!NOTE]
>**Rev 4,2** je nejnovější přepálená infrastruktura BareMetal s využitím existující architektury Rev 4.  Rev 4 poskytuje užší blízkost pro hostitele virtuálních počítačů Azure. Má významná vylepšení latence sítě mezi virtuálními počítači Azure a jednotkami instancí BareMetal nasazenými v razítkech a na řádcích Rev 4.  Pomocí Azure Portal můžete přistupovat k instancím služby BareMetal a spravovat je. 

## <a name="support"></a>Podpora
Infrastruktura BareMetal je ISO 27001, ISO 27017, SOC 1 a SOC 2 vyhovující.  Používá také model BYOL (Přineste si vlastní licenci): operační systém, specializované úlohy a aplikace třetích stran.  

Jakmile obdržíte kořenový přístup a úplnou kontrolu, předpokládáte zodpovědnost za:
- Navrhování a implementace řešení pro zálohování a obnovení, vysoká dostupnost a zotavení po havárii
- Licencování, zabezpečení a podpora pro operační systém a software třetích stran

Společnost Microsoft zodpovídá za:
- Poskytování hardwaru pro specializované úlohy 
- Zřizování operačního systému

:::image type="content" source="media/baremetal-support-model.png" alt-text="Model podpory infrastruktury BareMetal" border="false":::

## <a name="compute"></a>Compute
Infrastruktura BareMetal nabízí několik SKU pro specializované úlohy. Dostupné SKU jsou dostupné v rozsahu od menšího počítače se dvěma sokety až po systém se 24 sokety. Použijte skladové jednotky specifické pro úlohy pro specializované úlohy.

Samotné razítko instance BareMetal kombinuje následující komponenty:

- **Výpočet:** Servery založené na různých generacích procesorů Intel Xeon, které poskytují nezbytné výpočetní funkce a jsou certifikované pro specializované úlohy.

- **Síť:** Jednotná vysokorychlostní topologie sítě propojuje výpočetní součásti, úložiště a sítě LAN.

- **Úložiště:** Infrastruktura, ke které se přistupoval prostřednictvím sjednocené síťové infrastruktury

V rámci víceklientské infrastruktury BareMetal razítka se zákazníci nasazují v izolovaných klientech. Při nasazování tenanta pojmenujte předplatné Azure v rámci registrace Azure. Toto předplatné Azure je takové, které BareMetal instance se účtují.

>[!NOTE]
>Zákazník nasazený v instanci BareMetal se dostane do tenanta jako izolované. Tenant je izolovaný v síti, úložišti a výpočetní vrstvě z jiných tenantů. Úložiště a výpočetní jednotky přiřazené různým klientům se vzájemně nevidí ani vzájemně komunikují na instancích BareMetal.

## <a name="os"></a>Operační systém
Během zřizování instance BareMetal můžete vybrat operační systém, který chcete na počítačích nainstalovat. 

>[!NOTE]
>Nezapomeňte, že infrastruktura BareMetal je BYOL model.

Dostupné verze operačních systémů Linux:
- Red Hat Enterprise Linux (RHEL) 7,6
- SUSE Linux Enterprise Server (SLES)
   - SLES 12 SP2
   - SLES 12 SP3
   - SLES 12 SP4
   - SLES 12 SP5
   - SLES 15 SP1

## <a name="storage"></a>Úložiště
Instance BareMetal založené na konkrétním typu SKU přicházejí s předdefinovaným úložištěm NFS pro konkrétní typ úlohy. Když zřizujete BareMetal, můžete zřídit další úložiště na základě odhadovaného nárůstu tím, že odešlete žádost o podporu. Veškeré úložiště obsahuje disk s veškerým bliknutím v revizi 4,2 s podporou pro NFSv3 a názvů NFSv4. K dispozici bude novější revize 4,5 NVMe SSD. Další informace o velikosti úložiště najdete v části [BareMetal úlohy typ](../../../virtual-machines/workloads/sap/get-started.md) .

>[!NOTE]
>Úložiště používané pro BareMetal splňuje požadavky [140-2 publikace standardu FIPS (Federal Information Processing Standard)](/microsoft-365/compliance/offering-fips-140-2) . ve výchozím nastavení nabízí šifrování v klidovém stavu. Data jsou bezpečně uložená na discích.

## <a name="networking"></a>Sítě
Architektura síťových služeb Azure je klíčovou součástí úspěšného nasazení specializovaných úloh v instancích BareMetal. Je nejspíš to, že ne všechny systémy IT jsou umístěné v Azure už. Azure nabízí síťovou technologii, aby Azure vypadala jako virtuální datová centra pro vaše místní nasazení softwaru. Pro instance BareMetal jsou potřebné funkce sítě Azure:

- Virtuální sítě Azure jsou připojené k okruhu ExpressRoute, který se připojuje k vašim prostředkům v místní síti.
- Okruh ExpressRoute, který se připojuje místně k Azure, by měl mít minimální šířku pásma 1 GB/s nebo vyšší.
- Rozšířená služba Active Directory a DNS v Azure nebo plně běžící v Azure.

Pomocí ExpressRoute můžete své místní sítě v cloudu Microsoftu rozmístit prostřednictvím privátního připojení s použitím pomoci poskytovatele připojení. **ExpressRoute Premium** můžete povolit pro rozšiřování připojení napříč geopolitickými hranicemi nebo pro nákladově efektivní přenos dat mezi umístěním v blízkosti požadované oblasti Azure pomocí **ExpressRoute Local** .

Instance BareMetal se zřídí v rámci rozsahu IP adres serveru virtuální sítě Azure.

:::image type="content" source="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" alt-text="Diagram infrastruktury Azure BareMetal" lightbox="media/baremetal-infrastructure-portal/baremetal-infrastructure-diagram.png" border="false":::

Zobrazená architektura je rozdělená na tři části:
- **Left:** zobrazuje místní infrastrukturu zákazníka, která spouští různé aplikace, připojení prostřednictvím partnerského nebo místního hraničního směrovače, jako je Equinix. Další informace najdete v tématu [poskytovatelé a umístění připojení: Azure ExpressRoute](../../../expressroute/expressroute-locations.md).
- **Center:** zobrazuje [ExpressRoute](../../../expressroute/expressroute-introduction.md) zřízené pomocí předplatného Azure, které nabízí připojení k síti Azure Edge.
- **Right:** zobrazuje Azure IaaS a v tomto případě použití virtuálních počítačů k hostování aplikací, které jsou zřízené v rámci vaší virtuální sítě Azure.
- **Dole:** zobrazuje použití brány ExpressRoute povolené s [ExpressRoute FastPath](../../../expressroute/about-fastpath.md) pro připojení BareMetal, které nabízí nízkou latenci.   
   >[!TIP]
   >Pro zajištění podpory by měla být brána ExpressRoute UltraPerformance.  Další informace najdete v tématu [informace o branách virtuální sítě ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md).

## <a name="next-steps"></a>Další kroky

V dalším kroku se dozvíte, jak identifikovat a interagovat s jednotkami instancí BareMetal prostřednictvím Azure Portal.

> [!div class="nextstepaction"]
> [Správa instancí BareMetal pomocí webu Azure Portal](baremetal-infrastructure-portal.md)