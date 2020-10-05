---
title: Nasazení horizontu v řešení VMware Azure
description: Přečtěte si, jak nasadit VMware Horizon do řešení Azure VMware.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: bda4be049e360670cb7038bfbb3070c2a5f262c4
ms.sourcegitcommit: 638f326d02d108cf7e62e996adef32f2b2896fd5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91729045"
---
# <a name="deploy-horizon-on-azure-vmware-solution"></a>Nasazení horizontu v řešení VMware Azure 

>[!NOTE]
>Tento dokument se zaměřuje na produkt VMware Horizon. Tento postup se dřív jmenoval jako Horizontal 7 před změnou názvu produktu z horizontu 7 na horizont. Horizont je jiné řešení než Horizontal Cloud v Azure, i když existují některé sdílené součásti. Klíčové výhody řešení Azure VMware zahrnují jednodušší metodu určení velikosti a integraci správy VMware Cloud Foundation do Azure Portal.

[VMware Horizon](https://www.vmware.com/products/horizon.html)® je platforma virtuálních ploch a aplikací, která běží v datovém centru a poskytuje IT jednoduchou a centralizovanou správu. Poskytuje virtuálním plochám a aplikacím koncovým uživatelům na libovolném zařízení, a to kdekoli. Horizont umožňuje vytvářet a připojovat připojení k virtuálním plochám Windows, virtuálním plochám Linux, aplikacím hostovaným pro vzdálenou plochu (RDS), počítačům a fyzickým počítačům.

Tady se zaměřujeme konkrétně na nasazení nástroje Horizontal do řešení Azure VMware. Obecné informace o horizontu VMware najdete v dokumentaci k produkčnímu horizontu:

* [Co je VMware Horizon?](https://www.vmware.com/products/horizon.html)

* [Další informace o horizontu VMware](https://docs.vmware.com/en/VMware-Horizon/index.html)

* [Referenční architektura horizontu](https://techzone.vmware.com/resource/workspace-one-and-horizon-reference-architecture)

Se zavedením horizontu na řešení Azure VMware teď na platformě Azure existují dvě řešení infrastruktury virtuálních klientských počítačů (VDI). Následující diagram shrnuje klíčové rozdíly na vysoké úrovni.

:::image type="content" source="media/horizon/difference-horizon-azure-vmware-solution-horizon-cloud-azure.png" alt-text="Rozdíly mezi horizontem v cloudu řešení Azure VMware a Horizontal v Azure" border="false":::

Verze Horizontal 2006 a novější na řádku verze Horizontal 8 podporuje místní nasazení i nasazení řešení Azure VMware. K dispozici je několik funkcí horizontu, které jsou podporovány místně, ale ne v řešení Azure VMware. Podporovány jsou i další produkty v ekosystému Horizon. Informace najdete v tématu věnovaném [paritě funkcí a interoperabilitě](https://kb.vmware.com/s/article/80850).

## <a name="deploy-horizon-in-a-hybrid-cloud"></a>Nasazení horizontu v hybridním cloudu

Horizont můžete nasadit do hybridního cloudového prostředí, když použijete architekturu Horizontal Cloud pod (CPA) k propojení místních datových center a datacenter Azure. CPA se obvykle používá k horizontálnímu navýšení kapacity nasazení, vytvoření hybridního cloudu a zajištění kontinuity podnikových prostředí a zotavení po havárii. Podrobné informace o doprovodnéch materiálech pro provozní kontinuitu VMware najdete v tématu [rozšíření stávajících prostředí Horizontal 7](https://techzone.vmware.com/resource/business-continuity-vmware-horizon#_Toc41650874).

>[!IMPORTANT]
>CPA není roztažené nasazení; jednotlivé horizonty pod sebou jsou odlišné a všechny připojovací servery, které patří do jednotlivých lusků, se musí nacházet v jednom umístění a spouštět ve stejné doméně všesměrového vysílání z hlediska sítě.

Podobně jako v případě místního nebo privátního datového centra je možné horizont nasadit v privátním cloudu řešení Azure VMware. V následujících částech budeme projednávat klíčové rozdíly v nasazení horizontu do místního prostředí a řešení Azure VMware.

Privátní cloud Azure je koncepčně stejný jako SDDC VMware, což se obvykle používá v dokumentaci k horizontu. Zbývající část tohoto dokumentu bude používat výrazy Azure Private Cloud a VMware SDDC, které jsou zaměnitelné.

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

Typický návrh architektury Horizon používá strategii pod a blok. Blok je jeden vCenter, zatímco několik bloků se v kombinaci vytvoří pod. Horizont pod je jednotka organizace určená omezeními škálovatelnosti horizontu. Každý Horizont má samostatný portál pro správu, a proto standardní postupy návrhu slouží k minimalizaci počtu lusků.

Každý Cloud má vlastní schéma připojení k síti. V kombinaci se službou VMware SDDC Networking/NSX Edge nabízí připojení k síti řešení Azure VMware jedinečné požadavky na nasazení horizontu, které se liší od místních.

Každý privátní cloud nebo SDDC Azure je schopný zpracovávat 4 000 klientských nebo aplikačních relací, které předpokládají následující:

* Provoz úloh se zarovnává s tím, jak profil pracovního procesu LoginVSI úlohy.

* Bere se jenom provoz protokolu, ale žádná uživatelská data.

* NSX Edge je nakonfigurován tak, aby byl velký.

>[!NOTE]
>Váš profil úlohy a potřeby se můžou lišit, takže výsledky se můžou lišit v závislosti na vašem případu použití. Svazky uživatelských dat můžou v kontextu vaší úlohy snižovat omezení škálování. Velikost a naplánujte nasazení odpovídajícím způsobem. Další informace najdete v části pokyny pro změnu velikosti v části [Velikost hostitelů řešení Azure VMware pro nasazení na horizont](#size-azure-vmware-solution-hosts-for-horizon-deployments) .

Vzhledem k maximálnímu limitu privátního cloudu Azure nebo SDDC doporučujeme architekturu nasazení, kde jsou servery připojení a brány VMware Unified Access (UAGs) spuštěné v rámci Azure Virtual Network. To efektivně zapíná každý privátní cloud Azure nebo SDDC do bloku. To zase maximalizuje škálovatelnost horizontu běžícího na řešení Azure VMware.

Připojení z Azure Virtual Network k privátním cloudům Azure nebo SDDCs by se mělo konfigurovat s ExpressRoute FastPath. Následující diagram znázorňuje základní nasazení horizontu pod.

:::image type="content" source="media/horizon/horizon-pod-deployment-expresspath-fast-path.png" alt-text="Rozdíly mezi horizontem v cloudu řešení Azure VMware a Horizontal v Azure" border="false":::

## <a name="network-connectivity-to-scale-horizon-on-azure-vmware-solution"></a>Síťové připojení ke škále horizontálního škálování v řešení Azure VMware

V této části se dozvíte, jak vytvořit architekturu sítě na vysoké úrovni pro horizontální škálování v řešení Azure VMware s některými běžnými příklady nasazení. Tady je zaměření na důležité prvky sítě.

### <a name="single-horizon-pod-on-azure-vmware-solution"></a>Jeden horizont pod v řešení Azure VMware

:::image type="content" source="media/horizon/single-horizon-pod-azure-vmware-solution.png" alt-text="Rozdíly mezi horizontem v cloudu řešení Azure VMware a Horizontal v Azure" border="false":::

Jeden horizont je nejrovnější scénář nasazení. V tomto příkladu se rozhodnete, že chcete nasadit pouze jeden horizont pod v oblasti USA – východ. Vzhledem 4 000 k tomu, že se každý privátní cloud nebo SDDC odhaduje, aby se nasadila maximální velikost v rozsahu pod, můžete naplánovat nasazení až na tři privátní cloudy/SDDCs.

V tomto příkladu se v kombinaci s virtuálními počítači infrastruktury pro infrastrukturu nasazeným v Azure Virtual Network můžete spojit s 12 000 relacemi na horizontu na základě vašich úloh a datových potřeb. Připojení mezi jednotlivými privátními cloudy a SDDC do služby Azure Virtual Network je ExpressRoute rychlá cesta, což znamená, že není potřeba žádný přenos v oblasti východ mezi privátními cloudy.

Mezi klíčové předpoklady pro tento příklad základního nasazení patří:

* Nemáte místní horizont, ve kterém se chcete připojit k tomuto novému pod pomocí architektury v cloudu pod (CPA).

* Koncoví uživatelé se ke svým virtuálním plochám připojí přes Internet (vs. připojení prostřednictvím místního datového centra).

V tomto základním příkladu můžete připojit řadič domény služby AD v Azure Virtual Network s místní službou Active Directory prostřednictvím sítě VPN nebo okruhu ExpressRoute.

V popsaných příkladech základního příkladu může být podpora připojení k místním prostředkům. Může se jednat o uživatele, kteří přistupují k plochám a generují provoz aplikací pro virtuální počítače, nebo se připojují k místnímu horizontu pod pomocí CPA.

Následující obrázek ukazuje, jak to lze provést.Pokud chcete připojit podnikovou síť k Azure Virtual Network, budete potřebovat ExpressRoute.Také budete muset připojit podnikovou síť ke každému privátnímu cloudu nebo SDDCs pomocí Global Reach, což umožňuje připojení z SDDC k ExpressRoute a místním prostředkům.

:::image type="content" source="media/horizon/connect-corporate-network-azure-virtual-network.png" alt-text="Rozdíly mezi horizontem v cloudu řešení Azure VMware a Horizontal v Azure" border="false":::

### <a name="multiple-horizon-pods-on-azure-vmware-solution-across-multiple-regions"></a>Několik horizontů na řešení Azure VMware v různých oblastech

Pro jiný Horizontový příklad se podívejme na příklad, který zobrazuje horizont horizontálního škálování napříč více lusky.V tomto příkladu nasazujete dva časové lusky do dvou různých oblastí a federování je pomocí CPA.Konfigurace sítě je podobně jako v předchozím příkladu s dalšími dalšími odkazy mezi různými oblastmi. 

V každé oblasti budete muset připojit Virtual Network Azure k privátním cloudům/SDDCs v jiné oblasti, což umožňuje serverům pro připojení k horizontu, které jsou součástí federace CPA, připojit se ke všem plochám v rámci správy.Přidání dalších privátních cloudů/SDDCs do této konfigurace vám umožní škálovat na celkem 24 000 relací. 

I když tento příklad ukazuje více oblastí, platí stejný princip, pokud chcete ve stejné oblasti nasazovat dvě horizonty. Počítejte s tím, že byste se měli ujistit, že druhý horizont pod je nasazený v *samostatném Virtual Network Azure*.Nakonec stejně jako v předchozím příkladu v jednom z nich můžete připojit svou podnikovou síť a místní lokalitu pod tento příklad pro více nebo oblastí pomocí zákaznických ExpressRoute a Global Reach.

:::image type="content" source="media/horizon/multiple-horizon-pod-azure-vmware-solution.png" alt-text="Rozdíly mezi horizontem v cloudu řešení Azure VMware a Horizontal v Azure" border="false":::

## <a name="size-azure-vmware-solution-hosts-for-horizon-deployments"></a>Velikost hostitelů řešení Azure VMware pro nasazení v horizontu 

Metodologie změny velikosti v hostiteli běžícím v řešení Azure VMware je jednodušší než v místním prostředí, protože instance hostitele řešení Azure VMware je standardizovaná. Přesné změny velikosti hostitelů vám pomůžou určit počet hostitelů potřebných k podpoře požadavků vaší infrastruktury virtuálních počítačů (VDI) a centrálně určovat náklady na plochu.

### <a name="azure-vmware-solution-host-instance"></a>Instance hostitele řešení Azure VMware

* Server PowerEdge R640 – omezení DSS

* 36 jader ( \@ 2,3 GHz)

* 576 – GB RAM

* Řadič HBA HBA330 12 GB/s SAS (ne RAID)

* 1,92 TB standardu SSD SATA použití 6 GB/s 512 2.5 v hot-plug AG Drive, 3 DWPD, 10512 TBW

* Intel 1,6 TB, NVMe, smíšené použití Express Flash, 2,5 SFF Drive, U. 2, P4600 s operátorem

* 2 síti vSAN diskové skupiny: 1,6 x 4 (1.92 TB)

### <a name="horizon-sizing-inputs"></a>Vstupy pro změnu velikosti – Horizont

Pro plánované úlohy zjistíte následující:

* Počet souběžných ploch

* Požadovaná vCPU na plochu

* Požadované paměti vRAM na plochu

* Požadované úložiště na plochu

Obecně platí, že nasazení infrastruktury virtuálních klientských počítačů (VDI) jsou omezená na procesor nebo RAM, protože tyto faktory určují velikost hostitele. Pojďme tento příklad využít pro typ úlohy LoginVSI Knowledge Worker, který je ověřený při testování výkonu:

* 2 000 souběžného nasazení plochy

* 2vCPU na plochu.

* 4 GB paměti vRAM na stolní počítač.

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

Pokud nasazujete jak horizont, tak i místní řešení Azure VMware, jako v případě použití zotavení po havárii, zvolte licenci horizont Universal Subscription. Univerzální licence je vyšší cena, protože zahrnuje vSphere licenci pro místní nasazení.

Pracujte s prodejním týmem VMware EUC a určete náklady na licencování horizontu podle vašich potřeb.

### <a name="cost-of-the-horizon-infrastructure-vms-on-azure-virtual-network"></a>Náklady na virtuální počítače infrastruktury horizontu v Azure Virtual Network

V závislosti na standardní architektuře nasazení se virtuální počítače infrastruktury horizontu skládají z připojovacích serverů, UAGs a správců aplikací a jsou nasazené v Azure Virtual Network zákazníka. Další nativní instance Azure jsou nutné pro podporu služeb vysoké dostupnosti (HA), Microsoft SQL nebo služby Microsoft Active Directory (AD) v Azure. Níže je seznam instancí Azure, které jsou založené na příkladech nasazení 2 000-Desktop. 

| Součást infrastruktury horizontu | Instance Azure | Potřebný počet instancí (pro 2 000-desktopy)    | Komentář  |
|----------------------------------|----------------|----------------------------------------------------|----------|
| Server pro připojení                | D4sv3          | 2       | *Zahrnuje 1 instanci pro HA*             |    
| UAG                              | F2sv2          | 2       | *Zahrnuje 1 instanci pro HA*             |
| Správce svazků aplikace              | D4sv3          | 2       | *Zahrnuje 1 instanci pro HA*             |
| Cloudový konektor                  | D4sv3          | 1       |                                          |
| Řadič služby AD                    | D4sv3          | 2       | *Možnost používat službu MSFT AD v Azure* |
| MS-SQL Database                  | D4sv3          | 2       | *Možnost používat službu SQL v Azure*     |
| Sdílená složka systému Windows               | D4sv3          |         | *Volitelné*                               |

Náklady na virtuální počítač infrastruktury se \$ 0,36 na uživatele za měsíc pro nasazení 2 000-desktopu v předchozím příkladu. Všimněte si, že tento příklad používá USA – východ ceny instance Azure od června 2020. Ceny se můžou lišit v závislosti na oblasti, vybraných volbách a časování.
