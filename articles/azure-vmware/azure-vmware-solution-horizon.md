---
title: Nasazení horizontu v řešení VMware Azure
description: Přečtěte si, jak nasadit VMware Horizon do řešení Azure VMware.
ms.topic: how-to
ms.date: 09/29/2020
ms.openlocfilehash: 2cf6fc5cb7662188650365cb019774d6c778d405
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684871"
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

### <a name="sizing-tables"></a>Změna velikosti tabulek

Specifické požadavky vCPU/vRAM pro virtuální plochy Horizon závisí na profilu konkrétního pracovního vytížení zákazníka.   Spolupracujte se svým prodejním týmem MSFT a VMware, abyste mohli určit požadavky vCPU/vRAM pro virtuální klienty. 

| vCPU na virtuální počítač | vRAM na virtuální počítač (GB) | Instance | 100 virtuálních počítačů | 200 virtuálních počítačů | 300 virtuálních počítačů | 400 virtuálních počítačů | 500 virtuálních počítačů | 600 virtuálních počítačů | 700 virtuálních počítačů | 800 virtuálních počítačů | 900 virtuálních počítačů | 1000 virtuálních počítačů | 2000 virtuálních počítačů | 3000 virtuálních počítačů | 4000 virtuálních počítačů | 5000 virtuálních počítačů | 6000 virtuálních počítačů | 6400 virtuálních počítačů |
|:-----------:|:----------------:|:--------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:-------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|:--------:|
|      2      |        3,5       |    AVS   |    3    |    3    |    4    |    4    |    5    |    6    |    6    |    7    |    8    |     9    |    17    |    25    |    33    |    41    |    49    |    53    |
|      2      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    6    |    7    |    8    |    9    |     9    |    18    |    26    |    34    |    42    |    51    |    54    |
|      2      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      2      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      2      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      2      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      4      |        3,5       |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         4        |    AVS   |    3    |    3    |    4    |    5    |    6    |    7    |    8    |    9    |    10   |    11    |    22    |    33    |    44    |    55    |    66    |    70    |
|      4      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    12   |    13    |    26    |    38    |    51    |    62    |    75    |    79    |
|      4      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      4      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      4      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      6      |        3,5       |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         4        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         6        |    AVS   |    3    |    4    |    5    |    6    |    7    |    9    |    10   |    11   |    13   |    14    |    27    |    41    |    54    |    68    |    81    |    86    |
|      6      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      6      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      6      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |
|      8      |        3,5       |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         4        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         6        |    AVS   |    3    |    4    |    6    |    7    |    9    |    10   |    12   |    14   |    15   |    17    |    33    |    49    |    66    |    82    |    98    |    105   |
|      8      |         8        |    AVS   |    3    |    5    |    6    |    8    |    9    |    11   |    12   |    14   |    16   |    18    |    34    |    51    |    67    |    84    |    100   |    106   |
|      8      |        12        |    AVS   |    4    |    6    |    9    |    11   |    13   |    16   |    19   |    21   |    23   |    26    |    51    |    75    |    100   |    124   |    149   |    158   |
|      8      |        16        |    AVS   |    5    |    8    |    11   |    14   |    18   |    21   |    24   |    27   |    30   |    34    |    67    |    100   |    133   |    165   |    198   |    211   |


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

### <a name="azure-instance-types"></a>Typy instancí Azure

Informace o velikostech virtuálních počítačů Azure, které se budou vyžadovat pro infrastrukturu Horizon, najdete v pokynech VMware, které najdete [tady](https://techzone.vmware.com/resource/horizon-on-azure-vmware-solution-configuration#horizon-installation-on-azure-vmware-solution).

## <a name="next-steps"></a>Další kroky
Další informace o VMware Horizon pro řešení Azure VMware najdete v [nejčastějších dotazech ke horizontu](https://www.vmware.com/content/dam/digitalmarketing/vmware/en/pdf/products/horizon/vmw-horizon-on-microsoft-azure-vmware-solution-faq.pdf)VMware.
