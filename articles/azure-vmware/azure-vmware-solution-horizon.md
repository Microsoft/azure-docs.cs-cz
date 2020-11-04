---
title: Nasazení horizontu v řešení VMware Azure
description: Přečtěte si, jak nasadit VMware Horizon do řešení Azure VMware.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: babce512b896009c08165d2e3d9aec7c33724bf4
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321349"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Nasazení horizontu v řešení VMware Azure 

>[!NOTE]
>Tento dokument se zaměřuje na produkt VMware Horizon, dříve označovaný jako Horizontal 7. Horizont je jiné řešení než Horizontal Cloud v Azure, i když existují některé sdílené součásti. Klíčové výhody řešení Azure VMware zahrnují jak pružnější způsob určení velikosti, tak i integraci správy VMware Cloud Foundation do Azure Portal.

[VMware Horizon](https://www.vmware.com/products/horizon.html)®, virtuální desktop a aplikace, které běží v datovém centru a poskytuje jednoduchou a centralizovanou správu. Poskytuje virtuální plochy a aplikace na libovolném zařízení, a to kdekoli. Horizont umožňuje vytvářet a připojovat připojení k virtuálním plochám Windows a Linux, aplikacím hostovaným na vzdálené ploše (RDS), stolním počítačům a fyzickým počítačům.

Tady se zaměřujeme konkrétně na nasazení nástroje Horizontal do řešení Azure VMware. Obecné informace o horizontu VMware najdete v dokumentaci k produkčnímu horizontu:

* [Co je VMware Horizon?](https://www.vmware.com/products/horizon.html)

* [Další informace o horizontu VMware](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Referenční architektura horizontu](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Se zavedením horizontu na řešení Azure VMware teď na platformě Azure existují dvě řešení infrastruktury virtuálních klientských počítačů (VDI). Následující diagram shrnuje klíčové rozdíly na vysoké úrovni.

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Horizont pro cloud řešení Azure VMware a Horizontal v Azure" border="false":::

Verze Horizontal 2006 a novější na řádku verze Horizontal 8 podporuje místní nasazení i nasazení řešení Azure VMware. K dispozici je několik funkcí horizontu, které jsou podporovány místně, ale ne v řešení Azure VMware. Podporovány jsou i další produkty v ekosystému Horizon. Informace najdete v tématu věnovaném [paritě funkcí a interoperabilitě](https://kb.vmware.com/s/article/80850).

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Nasazení horizontu v hybridním cloudu

Horizont můžete nasadit v hybridním cloudovém prostředí, když použijete architekturu Horizontal Cloud pod (CPA) k propojení místních a datových center Azure. CPA škáluje vaše nasazení, vytváří hybridní cloud a zajišťuje redundanci pro zajištění kontinuity podnikových prostředí a zotavení po havárii.  Další informace najdete v tématu [rozšíření stávajících prostředí Horizontal 7](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874).

>[!IMPORTANT]
>CPA není roztažené nasazení; jednotlivé horizonty pod sebou jsou odlišné a všechny připojovací servery, které patří do jednotlivých lusků, se musí nacházet v jednom umístění a spouštět ve stejné doméně všesměrového vysílání z hlediska sítě.

Podobně jako v případě místního nebo privátního datového centra je možné horizont nasadit v privátním cloudu řešení Azure VMware. Probereme klíčové rozdíly v nasazení horizontu v místním prostředí a řešení Azure VMware v následujících oddílech.

Privátní cloud Azure je koncepčně stejný jako SDDC VMware, což se obvykle používá v dokumentaci k horizontu. Zbytek tohoto dokumentu používá výrazy, které jsou pro vás privátní cloudy Azure a VMware SDDC zaměnitelné.

Cloudový konektor pro horizonty se vyžaduje ke správě licencí předplatného v řešení Azure VMware. Cloud Connector se dá nasadit v Azure Virtual Network spolu se servery připojení Horizon.

>[!IMPORTANT]
>Podpora roviny ovládacího prvku horizontu pro horizont v řešení Azure VMware není ještě dostupná. Nezapomeňte si stáhnout verzi VHD Cloud Connector.

## <a name="vcenter-cloud-admin-role"></a>role správce cloudu vCenter

Vzhledem k tomu, že řešení Azure VMware je služba SDDC a Azure spravuje životní cyklus SDDC řešení Azure VMware, model oprávnění vCenter v řešení Azure VMware je omezený návrhem.

Zákazníci se musí používat k používání role správce cloudu, která má omezené sady oprávnění vCenter. Produkt Horizon byl upraven tak, aby fungoval s rolí správce cloudu v řešení Azure VMware, konkrétně:

* Zřizování okamžitého klonování bylo upraveno pro běh řešení Azure VMware.

* V řešení Azure VMware se vytvořila určitá zásada síti vSAN (VMware_Horizon) pro práci s horizontem, která musí být dostupná a používaná v SDDCs nasazeném pro horizont.

* vSphere mezipaměť pro čtení (CBRC) založenou na obsahu, která se také označuje jako akcelerátor úložiště Azure, je při spuštění v řešení Azure VMware zakázaná.

>[!IMPORTANT]
>CBRC se nesmí znovu zapnout.

>[!NOTE]
>Řešení Azure VMware automaticky konfiguruje konkrétní nastavení horizontu, pokud nasadíte horizont 2006 (neboli horizont 8) a vyšší ve větvi Horizon 8 a vyberete možnost **Azure** v instalačním programu serveru pro připojení k horizontu.

## <a name="horizon-on-azure-vmware-solution-deployment-architecture"></a>Architektura nasazení řešení VMware pro Azure – Horizont

Typický návrh architektury Horizon používá strategii pod a blok. Blok je jeden vCenter, zatímco několik bloků se v kombinaci vytvoří pod. Horizont pod je jednotka organizace určená omezeními škálovatelnosti horizontu. Každý Horizont má samostatný portál pro správu, a proto standardní postupy návrhu minimalizují počet lusků.

Každý Cloud má vlastní schéma připojení k síti. V kombinaci se službou VMware SDDC Networking/NSX Edge nabízí připojení k síti řešení Azure VMware jedinečné požadavky na nasazení horizontu, které se liší od místních.

Každý privátní cloud Azure a SDDC může zpracovávat 4 000 klientských nebo aplikačních relací za předpokladu, že:

* Provoz zatížení se zarovnává s profilem pracovního procesu LoginVSI úloh.

* Bere se jenom provoz protokolu, ale žádná uživatelská data.

* NSX Edge je nakonfigurován tak, aby byl velký.

>[!NOTE]
>Váš profil úlohy a potřeby se můžou lišit, takže výsledky se můžou lišit v závislosti na vašem případu použití. Svazky uživatelských dat můžou v kontextu vaší úlohy snižovat omezení škálování. Velikost a naplánujte nasazení odpovídajícím způsobem. Další informace najdete v části pokyny pro změnu velikosti v části [Velikost hostitelů řešení Azure VMware pro nasazení na horizont](#size-azure-vmware-solution-hosts-for-horizon-deployments) .

Vzhledem k privátnímu cloudu Azure a SDDC maximálnímu limitu doporučujeme architekturu nasazení, kde jsou servery připojení Horizon a brány VMware Unified Access (UAGs) spuštěné v rámci Azure Virtual Network. To efektivně zapíná každý privátní cloud Azure a SDDC do bloku. Zase maximalizujete škálovatelnost horizontu běžícího na řešení Azure VMware.

Připojení z Azure Virtual Network k privátním cloudům Azure nebo SDDCs by se mělo konfigurovat s ExpressRoute FastPath. Následující diagram znázorňuje základní nasazení horizontu pod.

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Obvyklé horizonty pod nasazením pomocí rychlé cesty ExpressPath" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Síťové připojení ke škále horizontálního škálování v řešení Azure VMware

V této části se dozvíte, jak vytvořit architekturu sítě na vysoké úrovni s některými běžnými příklady nasazení, které vám pomůžou škálovat horizonty na řešení Azure VMware. Fokus je zaměřen konkrétně na kritické síťové prvky. 

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Jeden horizont pod v řešení Azure VMware

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Jeden horizont pod v řešení Azure VMware" border="false":::

Jeden horizont je nejrovnější scénář nasazení, protože v USA – východ oblasti nasadíte pouze jeden horizont.  Vzhledem k tomu, že každý privátní cloud a SDDC jsou odhadované na zpracování 4 000 relací pro stolní počítače, nasadíte maximální velikost v rozsahu pod.  Nasazení můžete naplánovat až na tři privátní cloudy nebo SDDCs.

U virtuálních počítačů infrastruktury Horizon nasazených v Azure Virtual Network můžete dosáhnout relací 12 000 na horizontu pod. Připojení mezi jednotlivými privátními cloudy a SDDC do Azure Virtual Network je ExpressRoute rychlá cesta.  Mezi privátními cloudy je potřeba žádný přenos v západním východě. 

Mezi klíčové předpoklady pro tento příklad základního nasazení patří:

* Nemáte místní horizont, ve kterém se chcete připojit k tomuto novému pod pomocí architektury v cloudu pod (CPA).

* Koncoví uživatelé se připojují ke svým virtuálním plochám prostřednictvím Internetu (vs. připojení prostřednictvím místního datového centra).

Řadič domény služby AD se připojujete v Azure Virtual Network s místní službou AD prostřednictvím sítě VPN nebo okruhu ExpressRoute.

Variantou základního příkladu může být podpora připojení k místním prostředkům. Například uživatelé přistupují k plochám a generují provoz aplikací pro virtuální počítače nebo se připojují k místnímu horizontu pod pomocí CPA.

Diagram ukazuje, jak podporovat možnosti připojení k místním prostředkům. Pokud se chcete připojit k podnikové síti k Azure Virtual Network, budete potřebovat okruh ExpressRoute.  Také budete muset připojit podnikovou síť ke každému privátnímu cloudu a SDDCs pomocí ExpressRoute Global Reach.  Umožňuje připojení z SDDC k okruhu ExpressRoute a místním prostředkům. 

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Připojení podnikové sítě k Azure Virtual Network" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Několik horizontů na řešení Azure VMware v různých oblastech

Dalším scénářem je horizontální škálování v různých luskech.  V tomto scénáři nasadíte dvě lusky do dvou různých oblastí a federovat je pomocí CPA. Je podobný konfiguraci sítě v předchozím příkladu, ale s některými dalšími odkazy pro více oblastí. 

Azure Virtual Network v každé oblasti připojíte k privátním cloudům/SDDCs v jiné oblasti. Umožňuje, aby se servery připojení Horizon v rámci federace CPA připojily ke všem plochám v rámci správy. Přidání dalších privátních cloudů/SDDCs do této konfigurace vám umožní škálovat na celkem 24 000 relací. 

Stejné zásady platí i v případě, že ve stejné oblasti nasadíte dvě lusky.  V *samostatném Virtual Network Azure* je třeba nasadit druhý horizont. Stejně jako v případě jednoho z nich můžete k tomuto příkladu typu multi-pod a oblastí připojit svou podnikovou síť a místní, a to pomocí ExpressRoute a Global Reach. 

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text=" Několik horizontů na řešení Azure VMware v různých oblastech" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Velikost hostitelů řešení Azure VMware pro nasazení v horizontu 

Metodologie změny velikosti v hostiteli běžícím v řešení Azure VMware je jednodušší než v místním prostředí.  Důvodem je to, že hostitel řešení Azure VMware je standardizovaný.  Přesná velikost hostitele pomáhá určit počet hostitelů potřebných k podpoře požadavků vaší infrastruktury virtuálních klientských počítačů.  Je střední pro určení nákladů na Desktop.

### <a name="azure-vmware-solution-host-instance"></a>Instance hostitele řešení Azure VMware

* Server PowerEdge R640 – omezení DSS

* 36 jader ( \@ 2,3 GHz)

* 576 – GB RAM

* Řadič HBA HBA330 12 GB/s SAS (ne RAID)

* 1,92 TB standardu SSD SATA použití 6 GB/s 512 2.5 v hot-plug AG Drive, 3 DWPD, 10512 TBW

* Intel 1,6 TB, NVMe, smíšené použití Express Flash, 2,5 SFF Drive, U. 2, P4600 s operátorem

* 2 síti vSAN diskové skupiny: 1,6 x 4 (1.92 TB)

### <a name="horizon-sizing-inputs"></a>Vstupy pro změnu velikosti – Horizont

Tady je seznam toho, co budete potřebovat ke shromáždění pro plánované úlohy:

* Počet souběžných ploch

* Požadovaná vCPU na plochu

* Požadované paměti vRAM na plochu

* Požadované úložiště na plochu

Obecně platí, že nasazení VDI jsou omezená na procesor nebo RAM, což určuje velikost hostitele. Pojďme tento příklad využít pro typ úlohy LoginVSI Knowledge Worker, který je ověřený při testování výkonu:

* 2 000 souběžného nasazení plochy

* 2vCPU na plochu.

* 4 GB paměti vRAM na plochu.

* 50 GB úložiště na stolní počítač

V tomto příkladu celkový počet hostitelů vybere více než 18, což vede k tomu, že je hustota virtuálních počítačů na hostiteli 111.

>[!IMPORTANT]
>Úlohy zákazníků se budou lišit od tohoto příkladu pracovního procesu LoginVSI Knowledge. V rámci plánování nasazení můžete pracovat s VMware EUC pro vaše konkrétní požadavky na velikost a výkon. Nezapomeňte spustit vlastní testování výkonu pomocí skutečné plánované úlohy před dokončením změny velikosti hostitele a odpovídajícím způsobem upravit.

## <a name="horizon-on-azure-vmware-solution-licensing"></a>Horizont na licencování řešení Azure VMware 

Existují čtyři komponenty s celkovými náklady na spuštění nástroje horizont v řešení Azure VMware. 

### <a name="azure-vmware-solution-capacity-cost"></a>Náklady na kapacitu řešení Azure VMware

Informace o cenách najdete na stránce s [cenami řešení Azure VMware](https://azure.microsoft.com/pricing/details/azure-vmware/) .

### <a name="horizon-licensing-cost"></a>Náklady na licencování na horizont

K dispozici jsou dvě dostupné licence pro řešení Azure VMware, což může být buď souběžný uživatel (CCU) nebo pojmenovaný uživatel (NU):

* Licence pro předplatné horizontu

* Licence horizont Universal Subscription

Pokud v předvídatelné budoucnosti jenom nasazujete horizonty do řešení Azure VMware, pak použijte licenci pro předplatné horizontu, protože se jedná o nižší náklady.

Pokud je nasazená v řešení Azure VMware a v místním prostředí, jako u případu použití zotavení po havárii, vyberte licenci horizont Universal Subscription. Zahrnuje vSphere licenci pro místní nasazení, takže má vyšší náklady.

Pracujte s prodejním týmem VMware EUC a určete náklady na licencování horizontu podle vašich potřeb.

### <a name="cost-of-the-horizon-infrastructure-vms-on-azure-virtual-network"></a>Náklady na virtuální počítače infrastruktury horizontu v Azure Virtual Network

V závislosti na standardní architektuře nasazení se virtuální počítače infrastruktury horizontu skládají z připojovacích serverů, UAGs a správců hromadných aplikací. Nasazují se na Virtual Network Azure zákazníka. Další nativní instance Azure jsou nutné pro podporu služeb vysoké dostupnosti (HA), Microsoft SQL nebo služby Microsoft Active Directory (AD) v Azure. Tabulka uvádí instance Azure na základě příkladu nasazení 2 000-Desktop. 

>[!NOTE]
>Aby bylo možné zpracovat selhání, nasaďte další server, než je vyžadováno pro počet připojení (n + 1). Minimální doporučený počet instancí serveru pro připojení, UAG a Správce svazků aplikace je 2 a požadovaný počet bude růst na základě množství uživatelů, které bude prostředí podporovat.  Jeden server pro připojení podporuje maximálně 4 000 relací, i když se jako osvědčený postup doporučuje 2 000. U každého z nich se podporuje až sedm serverů s doporučením celkem 12 000 aktivních relací za sekundu. Nejaktuálnější čísla najdete v [článku znalostní báze VMware – omezení velikosti a doporučení pro změny velikosti VMware Horizon](https://kb.vmware.com/s/article/2150348).

| Součást infrastruktury horizontu | Instance Azure | Potřebný počet instancí (pro 2 000-desktopy)    | Komentář  |
|----------------------------------|----------------|----------------------------------------------------|----------|
| Server pro připojení                | D4sv3          | 2       | *Viz poznámku výše*                         |    
| UAG                              | F2sv2          | 2       | *Viz poznámku výše*                         |
| Správce svazků aplikace              | D4sv3          | 2       | *Viz poznámku výše*                         |
| Cloudový konektor                  | D4sv3          | 1       |                                          |
| Řadič služby AD                    | D4sv3          | 2       | *Možnost používat službu MSFT AD v Azure* |
| MS-SQL Database                  | D4sv3          | 2       | *Možnost používat službu SQL v Azure*     |
| Sdílená složka systému Windows               | D4sv3          |         | *Volitelné*                               |

Náklady na virtuální počítač infrastruktury se \$ 0,36 na uživatele za měsíc pro nasazení 2 000-desktopu v předchozím příkladu. V tomto příkladu se používá USA – východ ceny Azure instance z června 2020. Ceny se můžou lišit v závislosti na oblasti, vybraných volbách a časování.

## <a name="next-steps"></a>Další kroky
Další informace o VMware Horizon pro řešení Azure VMware najdete v [nejčastějších dotazech ke horizontu](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/horizon/vmw-horizon-on-microsoft-azure-vmware-solution-faq.pdf)VMware.
