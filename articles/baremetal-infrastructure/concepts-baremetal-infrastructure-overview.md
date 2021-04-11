---
title: Přehled infrastruktury BareMetal v Azure
description: Poskytuje přehled infrastruktury BareMetal v Azure.
ms.custom: references_regions
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/08/2021
ms.openlocfilehash: 7a4998a096a5c5d9e793c34d5046dce59262a2ae
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257564"
---
#  <a name="what-is-baremetal-infrastructure-on-azure"></a>Co je BareMetal infrastruktura v Azure?

Microsoft Azure nabízí cloudovou infrastrukturu se široké škálou integrovaných cloudových služeb, které vyhovují vašim obchodním potřebám. V některých případech ale možná budete muset spouštět služby na holé servery bez vrstvy virtualizace. Je možné, že budete potřebovat kořenový přístup a kontrolu nad operačním systémem (OS). Pro splnění takových potřeb Azure nabízí infrastrukturu BareMetal pro několik nejdůležitějších a důležitých aplikací.

Infrastruktura BareMetal se skládá z vyhrazených instancí BareMetal (výpočetních instancí), vysoce výkonného úložiště a vhodného úložiště (NFS, ISCSI a Fibre Channel), jakož i sady virtuálních sítí LAN (VLAN) specifických pro funkci v izolovaném prostředí. Úložiště se dá sdílet mezi BareMetal instancemi, aby se povolily funkce jako clustery se škálováním na více systémů nebo vytváření párů s vysokou dostupností pomocí STONITH.
 
Toto prostředí má také speciální sítě VLAN, ke kterým máte přístup, pokud v předplatném Azure spouštíte virtuální počítače v jedné nebo několika virtuálních sítích Azure (virtuální sítě). Celé prostředí se reprezentuje jako skupina prostředků ve vašem předplatném Azure.

Infrastruktura BareMetal se nabízí ve více než 30 SKU od 2 do 24 jednotek a v rozsahu od 1,5 TB do 24 TBs. K dispozici je také velká sada SKU s Octane pamětí. Azure nabízí největší škálu holých instancí v cloudu s škálovatelným škálováním.

## <a name="why-baremetal-infrastructure"></a>Proč BareMetal infrastrukturu?  

Některé centrální úlohy v podniku se skládají z technologií, které nejsou určené ke spuštění v typickém virtualizovaném cloudovém nastavení. Vyžadují speciální architekturu, certifikovaný hardware nebo neobyčejně velké velikosti. I když tyto technologie mají nejbezpečnější funkce ochrany dat a kontinuity podnikových aplikací, tyto funkce nejsou vytvořené pro virtualizovaný Cloud. Jsou citlivější na latence, okolní okolí a vyžadují mnohem větší kontrolu nad aktivitou správy změn a údržby.

Infrastruktura BareMetal je sestavená, certifikovaná a testována pro výběrovou sadu takových aplikací. Azure je první, co nabízí taková řešení, a má od sebe největší portfolio a nejvíc sofistikované systémy.

BareMetal infrastruktura nabízí tyto výhody: 

- Vyhrazené instance
- Certifikovaný hardware pro specializované úlohy
    - SAP (viz [Poznámka k sap #1928533](https://launchpad.support.sap.com/#/notes/1928533))
    - Oracle (viz [ID dokumentu oracle #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52))
- Holý počítač (bez virtualizace COMPUTE)
- Nízká latence mezi virtuálními počítači hostované aplikace Azure a instancemi BareMetal (0,35 MS)
- Všechny flash SSD a NVMe
    - Až 1 PB/tenant 
    - IOPS až do 1,2 milionů/tenantů 
    - 40/100-GB šířky pásma sítě
    - Přístup přes systém souborů NFS, ISCSI a FC
- Redundantní napájení, napájení, síťové karty, tory, porty, sítě WAN, úložiště a Správa
- Výměna za chodu při selhání (bez nutnosti překonfigurování)
- Okna koordinovaných údržby pro zákazníky
- Snímky s podporou aplikací, archivace, zrcadlení a klonování


## <a name="sku-availability-in-azure-regions"></a>Dostupnost skladové položky v oblastech Azure

Infrastruktura BareMetal nabízí několik SKU certifikovaných pro specializované úlohy. Použijte skladové jednotky specifické pro úlohy, které vyhovují vašim potřebám.

- Velké instance – od dvou až po systémy se čtyřmi sokety.  
- Velmi velké instance – od čtyř soketů až po systémy s dvaceti sokety. 

BareMetal infrastruktura pro specializované úlohy je k dispozici v následujících oblastech Azure:
- West Europe
- Severní Evropa
- Podpora zón Německo – středozápad *
- Podpora zón Východní USA 2 *
- Podpora zón Východní USA *
- Podpora zón Západní USA *
- Podpora zón Západní USA 2 *
- Středojižní USA

>[!NOTE]
>**Podpora zón** odkazuje na zóny dostupnosti v rámci oblasti, kde BareMetal instance můžou být nasazené mezi zónami pro zajištění vysoké odolnosti a dostupnosti. Tato schopnost povoluje podporu pro škálování aktivních a aktivních více lokalit.

## <a name="managing-baremetal-instances-in-azure"></a>Správa instancí BareMetal v Azure 

V závislosti na vašich potřebách může být topologie aplikací infrastruktury BareMetal složitá. Můžete nasadit více instancí v jednom nebo více umístěních se sdíleným nebo vyhrazeným úložištěm a specializovanými připojeními k síti LAN a WAN. V případě infrastruktury BareMetal nabízí Azure konzultační zachycení těchto informací pomocí CSA/GBB v poli na portálu zřizování. 

V době, kdy se zřídí infrastruktura BareMetal, operační systém, sítě, svazky úložiště, umístění v zónách a oblastech a připojení WAN mezi umístěními jsou už předem nakonfigurovaná. Nastavili jste registraci licencí pro operační systém (BYOL), konfigurace operačního systému a instalaci aplikační vrstvy.

V Azure Portal budete moci zobrazit všechny prostředky infrastruktury BareMetal a jejich stav a atributy. Můžete také provozovat instance a žádosti o otevření služby a z ní lístky podpory. 

## <a name="operational-model"></a>Provozní model
Infrastruktura BareMetal je ISO 27001, ISO 27017, SOC 1 a SOC 2 vyhovující. Používá také model BYOL (Přineste si vlastní licenci): operační systém, specializované úlohy a aplikace třetích stran.  

Jakmile obdržíte kořenový přístup a úplnou kontrolu, předpokládáte zodpovědnost za:
- Navrhování a implementace řešení pro zálohování a obnovení, vysoká dostupnost a zotavení po havárii.
- Licencování, zabezpečení a podpora pro operační systém a software třetích stran.

Společnost Microsoft zodpovídá za:
- Poskytování hardwaru pro specializované úlohy. 
- Zřizování operačního systému.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-support-model.png" alt-text="Diagram modelu podpory infrastruktury BareMetal" border="false":::

## <a name="baremetal-instance-stamp"></a>Razítko instance BareMetal

Samotné razítko instance BareMetal kombinuje následující komponenty:

- **Výpočet:** Servery založené na generaci procesorů Intel Xeon, které poskytují nezbytné výpočetní funkce a jsou certifikované pro specializované úlohy.

- **Síť:** Jednotná vysokorychlostní topologie sítě propojuje výpočetní součásti, úložiště a sítě LAN.

- **Úložiště:** Infrastruktura, ke které se přistupoval prostřednictvím sjednocené síťové infrastruktury

V rámci víceklientské infrastruktury BareMetal razítka se zákazníci nasazují v izolovaných klientech. Při nasazování tenanta pojmenujte předplatné Azure v rámci registrace Azure. Toto předplatné Azure se fakturuje za vaše BareMetal instance.

>[!NOTE]
>Zákazník, který nasazuje instanci BareMetal, je v tenantovi izolovaný. Tenant je izolovaný v síti, úložišti a výpočetní vrstvě z jiných tenantů. Úložiště a výpočetní jednotky přiřazené různým klientům se vzájemně nevidí ani vzájemně komunikují na svých BareMetal instancích.

## <a name="operating-system"></a>Operační systém
Během zřizování instance BareMetal můžete vybrat operační systém, který chcete na počítačích nainstalovat. 

>[!NOTE]
>Nezapomeňte, že infrastruktura BareMetal je BYOL model.

Dostupné verze operačních systémů Linux:
- Red Hat Enterprise Linux (RHEL)
- SUSE Linux Enterprise Server (SLES)

## <a name="storage"></a>Storage
Infrastruktura BareMetal poskytuje vysoce redundantní úložiště NFS a úložiště Fibre Channel. Infrastruktura nabízí rozsáhlou integraci pro podnikové úlohy, jako je SAP, SQL a další. Poskytuje taky ochranu dat a možnosti pro správu dat konzistentní s aplikacemi. Nástroje pro samoobslužné řízení nabízí prostorově efektivní možnosti pro vytváření snímků, klonování a podrobné replikace spolu s jedním podoknem monitorování skla. Infrastruktura umožňuje 0 RPO a RTO možnosti pro potřeby dostupnosti dat a kontinuitu podnikových aplikací. 

Infrastruktura úložiště nabízí:
- Až 4 x 100 až GB odchozích připojení.
- Až 32 GB odesílání Fibre Channel.
- Všechny jednotky Flash SSD a NVMe Drive.
- Extrémně nízká latence a vysoká propustnost.
- Škáluje až 4 PB nezpracovaného úložiště. 
- Až 11 000 000 IOPS.

Podporují se tyto protokoly pro přístup k datům: 
- iSCSI 
- Systém souborů NFS (V3 nebo v4) 
- Fiber Channel 
- NVMe přes FC  

## <a name="networking"></a>Sítě
Architektura síťových služeb Azure je klíčovou součástí úspěšného nasazení specializovaných úloh v instancích BareMetal. Je nejspíš to, že ne všechny systémy IT jsou umístěné v Azure už. Azure nabízí síťovou technologii, aby Azure vypadala jako virtuální datová centra pro vaše místní nasazení softwaru. Mezi funkce sítě Azure požadované pro instance BareMetal patří:

- Azure Virtual Networks připojené k okruhu Azure ExpressRoute, který se připojuje k místním síťovým prostředkům.
- Okruh ExpressRoute, který se připojuje místně k Azure, by měl mít minimální šířku pásma 1 GB/s nebo vyšší.
- Rozšířená služba Active Directory a DNS v Azure nebo plně spuštěná v Azure.

ExpressRoute umožňuje rozmístit místní síť do cloudu Microsoftu přes soukromé připojení s podporou poskytovatele připojení. **ExpressRoute Local** můžete použít k nákladově efektivnímu přenosu dat mezi vaším místním umístěním a oblastí Azure, kterou chcete. Pro rozšiřování připojení mezi geopolitickými hranicemi můžete povolit **ExpressRoute Premium**. 

Instance BareMetal se zřídí v rámci rozsahu IP adres serveru virtuální sítě Azure.

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" alt-text="Diagram architektury diagramu infrastruktury Azure BareMetal" lightbox="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" border="false":::

Zobrazená architektura je rozdělená na tři části:
- **Vlevo:** Zobrazuje místní infrastrukturu zákazníka, která spouští různé aplikace, připojení prostřednictvím partnerského nebo místního hraničního směrovače, jako je Equinix. Další informace najdete v tématu [poskytovatelé a umístění připojení: Azure ExpressRoute](../expressroute/expressroute-locations.md).
- **Vycentrovat:** Zobrazuje [ExpressRoute](../expressroute/expressroute-introduction.md) zřízené pomocí předplatného Azure, které nabízí připojení k síti Azure Edge.
- **Vpravo:** Zobrazuje Azure IaaS a v tomto případě použití virtuálních počítačů k hostování aplikací, které jsou zřízené v rámci vaší virtuální sítě Azure.
- **Dolní:** Ukazuje použití brány ExpressRoute povolené s [ExpressRoute FastPath](../expressroute/about-fastpath.md) pro připojení BareMetal, které nabízí nízkou latenci.   
   >[!TIP]
   >Pro zajištění podpory by měla být brána ExpressRoute UltraPerformance. Další informace najdete v tématu [informace o branách virtuální sítě ExpressRoute](../expressroute/expressroute-about-virtual-network-gateways.md).

## <a name="next-steps"></a>Další kroky

V dalším kroku se dozvíte, jak identifikovat instance BareMetal pomocí Azure Portal a pracovat s nimi.

> [!div class="nextstepaction"]
> [Správa instancí BareMetal pomocí Azure Portal](connect-baremetal-infrastructure.md)
