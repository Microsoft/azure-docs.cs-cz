---
title: Oblasti a zóny dostupnosti v Azure
description: Přečtěte si o oblastech a Zóny dostupnosti v Azure, které odpovídají vašim technickým a zákonným požadavkům.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 08/27/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: 5178bf6955203c6a5625b941dd0102f2f4458abc
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078370"
---
# <a name="regions-and-availability-zones-in-azure"></a>Oblasti a zóny dostupnosti v Azure

Služby Microsoft Azure Services jsou k dispozici globálně, aby bylo možné provozovat cloudové operace na optimální úrovni. Podle technických a regulativních hledisek si můžete vybrat nejlepší oblast pro potřeby: možnosti služby, zaregistrování dat, požadavky na dodržování předpisů a latence.

## <a name="terminology"></a>Terminologie

Pro lepší pochopení oblastí a Zóny dostupnosti v Azure vám pomůže pochopit klíčové pojmy nebo koncepty.

| Termín nebo koncept | Popis |
| --- | --- |
| oblast | Sada Datacenter nasazených v hraničním prostředí určeném pro latenci a připojená přes vyhrazenou síť s nízkou latencí. |
| geografické | Oblast světa, která obsahuje alespoň jednu oblast Azure. Geografické oblasti definují diskrétní trh, který zachovává rozsahy dat a hranice dodržování předpisů. Zeměpisné oblasti umožňují zákazníkům se specifickými požadavky na rezidenci dat a dodržování předpisů, aby měli svoje data a aplikace blízko. Geografické oblasti jsou odolné proti chybám, které vydržely selhání celé oblasti prostřednictvím připojení k naší vyhrazené síťové infrastruktuře s vysokou kapacitou. |
| Zóna dostupnosti | Jedinečná fyzická umístění v rámci jedné oblasti. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. |
| Doporučená oblast | Oblast, která poskytuje nejširší škálu možností služeb a je navržena pro podporu Zóny dostupnosti nyní nebo v budoucnu. Ty jsou určené v Azure Portal jako **Doporučené**. |
| alternativní (jiná) oblast | Oblast, která rozšiřuje nároky na Azure v rámci hranice sídla dat, kde existuje i doporučená oblast. Alternativní oblasti vám pomůžou optimalizovat latenci a poskytnout druhou oblast pro potřeby zotavení po havárii. Nejsou navržené tak, aby podporovaly Zóny dostupnosti (i když Azure provádí pravidelné hodnocení těchto oblastí a určí, jestli by se měly stát doporučené oblasti). Tyto jsou označeny v Azure Portal jako **jiné**. |
| základní služba | Základní služba Azure, která je dostupná ve všech oblastech, když je oblast všeobecně dostupná. |
| Služba na běžném provozu | Služba Azure, která je dostupná ve všech doporučených oblastech během 12 měsíců od všeobecné dostupnosti oblasti nebo služby a dostupnosti na základě poptávky v alternativních oblastech. |
| Specializovaná služba | Služba Azure, která je k dispozici v rámci různých oblastí, které jsou zajištěny vlastním/specializovaným hardwarem. |
| místní služba | Služba Azure nasazená v regionu a umožňuje zákazníkům určit oblast, do které bude služba nasazena. Úplný seznam najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=all). |
| neregionální služba | Služba Azure, pro kterou neexistuje žádná závislost na konkrétní oblasti Azure. Neregionální služby se nasazují do dvou nebo více oblastí, a pokud dojde k místnímu selhání, instance služby v jiné oblasti bude dál obsluhovat zákazníky. Úplný seznam najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=all). |

## <a name="regions"></a>Oblasti

Oblast je sada Datacenter nasazených v hraničním prostředí definovaném latencí a připojená přes vyhrazenou síť s nízkou latencí. Azure vám nabízí flexibilitu při nasazování aplikací, které potřebujete, včetně napříč různými oblastmi a zajištění odolnosti mezi oblastmi. Další informace najdete v tématu [Přehled pilíře odolnosti](/azure/architecture/framework/resiliency/overview)proti chybám.

## <a name="availability-zones"></a>Zóny dostupnosti

Zóna dostupnosti je nabídka s vysokou dostupností, která chrání vaše aplikace a data při selhání datacentra. Zóny dostupnosti jsou jedinečná fyzická umístění uvnitř oblasti Azure. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. Kvůli odolnosti ve všech aktivovaných oblastech existují minimálně tři samostatné zóny. Fyzické oddělení Zóny dostupnosti v rámci oblasti chrání aplikace a data před selháními datových center. Redundantní služby v zóně replikují aplikace a data napříč Zóny dostupnosti, aby se chránily před jednotlivými chybami. Díky Zóny dostupnosti Azure nabízí nejlepší smlouvu SLA 99,99% provozu virtuálního počítače. Úplná smlouva [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) vysvětluje garantovanou dostupnost Azure jako celku.

Zóna dostupnosti v oblasti Azure je kombinací domény selhání a aktualizační domény. Pokud například vytvoříte tři nebo více virtuálních počítačů ve třech zónách v oblasti Azure, budou vaše virtuální počítače efektivně distribuovány mezi tři domény selhání a tři aktualizační domény. Platforma Azure tuto distribuci rozpoznává mezi aktualizačními doménami, aby se zajistilo, že virtuální počítače v různých zónách nejsou naplánované na stejnou dobu.

Společné umístění výpočetních operací, úložiště, sítě a datových prostředků v rámci zóny a replikace v jiných zónách vám může vytvořit vysokou dostupnost architektury aplikace. Služby Azure, které podporují zóny dostupnosti, spadají do dvou kategorií:

- Hraniční **služby** – kde je prostředek připnutý ke konkrétní zóně (například virtuální počítače, spravované disky, standardní IP adresy) nebo
- **Redundantní služby v zóně** – když se platforma Azure automaticky replikuje mezi zónami (například redundantní úložiště zóny, SQL Database).

Pro zajištění komplexní provozní kontinuity v Azure Sestavte architekturu aplikace pomocí kombinace Zóny dostupnosti s páry oblastí Azure. Můžete synchronně replikovat aplikace a data pomocí Zóny dostupnosti v oblasti Azure pro zajištění vysoké dostupnosti a asynchronní replikace napříč oblastmi Azure pro ochranu proti havárii.
 
![koncepční zobrazení jedné zóny v oblasti](./media/az-overview/az-graphic-two.png)

> [!IMPORTANT]
> Identifikátory zóny dostupnosti (čísla 1, 2 a 3 na obrázku výše) jsou logicky mapovány na skutečné fyzické zóny pro každé předplatné nezávisle na sobě. To znamená, že dostupnost Zóna 1 v daném předplatném může odkazovat na jinou fyzickou zónu než Zóna 1 dostupnosti v jiném předplatném. V důsledku toho se doporučuje nespoléhat na ID zón dostupnosti napříč různými předplatnými pro umístění virtuálního počítače.

## <a name="region-and-service-categories"></a>Kategorie oblasti a služby

Přístup k Azure v oblasti dostupnosti služeb Azure v různých oblastech je nejlépe popsaný v tématu expressing Services, které jsou dostupné v doporučených oblastech a alternativních oblastech.

- **Doporučená oblast** – oblast, která poskytuje nejširší škálu možností služeb a je navržená tak, aby podporovala zóny dostupnosti nyní nebo v budoucnu. Ty jsou určené v Azure Portal jako **Doporučené**.
- **Alternativním (jiné) oblasti** – oblast, která rozšiřuje nároky na Azure v rámci hranice zasídla dat, kde existuje i doporučená oblast. Alternativní oblasti vám pomůžou optimalizovat latenci a poskytnout druhou oblast pro potřeby zotavení po havárii. Nejsou navržené tak, aby podporovaly Zóny dostupnosti (i když Azure provádí pravidelné hodnocení těchto oblastí a určí, jestli by se měly stát doporučené oblasti). Tyto jsou označeny v Azure Portal jako **jiné**.

### <a name="comparing-region-types"></a>Porovnání typů oblastí

Služby Azure se seskupují do tří kategorií: základní, běžné a specializované služby. Obecné zásady Azure týkající se nasazování služeb do jakékoli dané oblasti se řídí podle typu oblasti, kategorií služeb a poptávky zákazníků:

- **Základní** – k dispozici ve všech doporučených a alternativních oblastech, pokud je oblast všeobecně dostupná nebo do 12 měsíců od nové základní služby, která se stane všeobecně dostupnou.
- General **– k** dispozici ve všech doporučených oblastech do 12 měsíců od obecné dostupnosti oblasti nebo služby; na základě poptávky v alternativních oblastech (mnoho už je nasazených do velké podmnožiny alternativních oblastí).
- **Specializované** – cílené nabídky služeb, často zaměřené na obor nebo na základě vlastního/specializovaného hardwaru. Dostupnost na základě požadavků napříč oblastmi (mnoho je již nasazena do velké podmnožiny doporučených oblastí).

Pokud chcete zjistit, které služby se v dané oblasti nasazují, a také budoucí plán pro náhled nebo obecnou dostupnost služeb v oblasti, přečtěte si článek [dostupné v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=all).

Pokud nabídka služeb není v konkrétní oblasti dostupná, můžete svůj zájem sdílet tím, že se obrátíte na prodejní zástupce Microsoftu.

| Typ oblasti | Bez regionu | Základní | Hlavní fáze | Specializovaná | Zóny dostupnosti | Rezidence dat |
| --- | --- | --- | --- | --- | --- | --- |
| Doporučeno | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Řízený na základě poptávky | :heavy_check_mark: | :heavy_check_mark: |
| Střídat | :heavy_check_mark: | :heavy_check_mark: | Řízený na základě poptávky | Řízený na základě poptávky | Není k dispozici | :heavy_check_mark: |

### <a name="services-by-category"></a>Služby podle kategorie

Jak už bylo uvedeno výše, Azure klasifikuje služby do tří kategorií: základní, hlavní a specializované. Kategorie služeb jsou přiřazeny při obecné dostupnosti. Služby často spouštějí životní cyklus jako specializovanou službu a jako požadavek a zvýšení využití se můžou zvýšit na běžný nebo základní. V následující tabulce jsou uvedeny kategorie služeb jako základní, hlavní nebo specializované. Měli byste si uvědomit následující informace o tabulce:

- Některé služby jsou jiné než regionální. Informace a seznam neoblastních služeb najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/).
- Virtuální počítače starší generace nejsou uvedeny. Další informace najdete v dokumentaci na [předchozích generacích velikostí virtuálních počítačů](../virtual-machines/sizes-previous-gen.md).

> [!div class="mx-tableFixed"]
> | Základní | Hlavní fáze | Specializovaná |
> | --- | --- | --- |
> | Úložiště účtů | API Management | Azure API for FHIR |
> | Application Gateway | App Configuration | Azure Analysis Services |
> | Azure Backup | App Service | Služba Azure Blockchain |
> | Azure Cosmos DB | Automation | Azure Blueprint |
> | Azure Data Lake Storage Gen2 | Azure Active Directory Domain Services | Azure Database for MariaDB |
> | Azure ExpressRoute | Azure Bastion | Rezervované HSM Azure |
> | Azure SQL Database | Azure Cache for Redis | Azure Dev Spaces |
> | Cloud Services | Azure Cognitive Search | Azure Digital Twins |
> | Cloud Services: Av2-Series | Průzkumník dat Azure | Azure Lab Services |
> | Cloud Services: Dv2-Series | Azure Data Share | Azure NetApp Files |
> | Cloud Services: Dv3-Series | Azure Database for MySQL | Azure |
> | Cloud Services: Ev3-Series | Azure Database for PostgreSQL | Jarní cloudová služba Azure |
> | Cloud Services: IP adresy na úrovni instance | Azure Database Migration Service | Azure Time Series Insights |
> | Cloud Services: Vyhrazená IP adresa | Azure Databricks | Azure VMware Solution by CloudSimple |
> | Disk Storage | Azure DDoS Protection | Cloud Services: Řada G |
> | Event Hubs | Azure DevTest Labs | Cloud Services: řada H-Series |
> | Key Vault | Azure Firewall | Cognitive Services: Custom Vision |
> | Nástroj pro vyrovnávání zatížení | Azure Firewall Manager | Cognitive Services: rozpoznávání mluvčího |
> | Service Bus | Azure Functions | Data Box Heavy |
> | Service Fabric | Azure HPC Cache | Data Catalog |
> | Virtual Machine Scale Sets | Azure IoT Hub | Data Factory: Data Factory v1 |
> | Virtual Machines | Azure Kubernetes Service (AKS) | Data Lake Analytics |
> | Virtual Machines: Av2-Series | Azure Machine Learning | Azure Machine Learning Studio (klasický)|
> | Virtual Machines: Bs-Series | Azure Private Link | Microsoft Genomics |
> | Virtual Machines: DSv2-Series | Azure Red Hat OpenShift | Remote Rendering |
> | Virtual Machines: DSv3-Series | Služba Azure SignalR | Spatial Anchors |
> | Virtual Machines: Dv2-Series | Azure Site Recovery | StorSimple |
> | Virtual Machines: Dv3-Series | Azure Stack Hub | Video Indexer |
> | Virtual Machines: ESv3-Series | Azure Stream Analytics | Virtual Machines: DASv4-Series |
> | Virtual Machines: Ev3-Series | Azure Synapse Analytics | Virtual Machines: DAv4-Series |
> | Virtual Machines: řada F-Series | Batch | Virtual Machines: DCsv2-Series |
> | Virtual Machines: FS-Series | Cloud Services: řada M-Series | Virtual Machines: EASv4-Series |
> | Virtual Machines: IP adresy na úrovni instance | Cognitive Services | Virtual Machines: EAv4-Series |
> | Virtual Machines: Vyhrazená IP adresa | Cognitive Services: Počítačové zpracování obrazu | Virtual Machines: Řada G |
> | Virtual Network | Cognitive Services: Content Moderator | Virtual Machines: GS-Series |
> | VPN Gateway | Cognitive Services: Face | Virtual Machines: HBv1-Series |
> |  | Cognitive Services: Nástroj pro rozpoznávání formulářů | Virtual Machines: HBv2-Series |
> |  | Cognitive Services: Language Understanding | Virtual Machines: HCv1-Series |
> |  | Cognitive Services: QnA Maker | Virtual Machines: řada H-Series |
> |  | Cognitive Services: hlasové služby | Virtual Machines: LS-Series |
> |  | Container Instances | Virtual Machines: LSv2-Series |
> |  | Container Registry | Virtual Machines: Mv2-Series |
> |  | Data Factory | Virtual Machines: NC-Series |
> |  | Event Grid | Virtual Machines: NCv2-Series |
> |  | HDInsight | Virtual Machines: NCv3-Series |
> |  | Logic Apps | Virtual Machines: NDs-Series |
> |  | Media Services | Virtual Machines: NDv2-Series |
> |  | Network Watcher | Virtual Machines: NV-Series |
> |  | Notification Hubs | Virtual Machines: NVv3-Series |
> |  | Power BI Embedded | Virtual Machines: NVv4-Series |
> |  | Blob Storage úrovně Premium | Virtual Machines: SAP HANA ve velkých instancích Azure |
> |  | Úložiště Premium Files | Visual Studio App Center |
> |  | Úložiště: Archiv služby Storage |  |
> |  | Ultra Disk Storage |  |
> |  | Virtual Machines: Ddsv4-Series |  |
> |  | Virtual Machines: Ddv4-Series |  |
> |  | Virtual Machines: Dsv4-Series |  |
> |  | Virtual Machines: Dv4-Series |  |
> |  | Virtual Machines: Edsv4-Series |  |
> |  | Virtual Machines: Edv4-Series |  |
> |  | Virtual Machines: Esv4-Series |  |
> |  | Virtual Machines: Ev4-Series |  |
> |  | Virtual Machines: Fsv2-Series |  |
> |  | Virtual Machines: řada M-Series |  |
> |  | Virtual WAN |  |

###  <a name="services-resiliency"></a>Odolnost služeb

Všechny služby správy Azure jsou navržené tak, aby byly odolné proti selháním na úrovni jednotlivých oblastí. V případě selhání má jedna nebo více selhání zóny dostupnosti v rámci oblasti menší poloměr selhání v porovnání s selháním celé oblasti. Azure se může zotavit z neúspěšného selhání služeb správy v rámci oblasti nebo z jiné oblasti Azure. Azure provádí kritickou údržbu jedné zóny v čase v rámci určité oblasti, aby nedocházelo k chybám, které mají vliv na prostředky zákazníka nasazené napříč Zóny dostupnosti v rámci oblasti.

### <a name="pricing-for-vms-in-availability-zones"></a>Ceny pro virtuální počítače v Zóny dostupnosti

Pro virtuální počítače nasazené v zóně dostupnosti se neúčtují žádné další náklady. 99,99% doba provozu virtuálního počítače se nabízí, když se v rámci jedné nebo více Zóny dostupnosti v oblasti Azure nasadí nejméně dva virtuální počítače. Budou se účtovat další poplatky za přenos dat mezi virtuálními počítači v zóně dostupnosti. Další informace najdete na stránce s [cenami za šířku pásma](https://azure.microsoft.com/pricing/details/bandwidth/) .

### <a name="get-started-with-availability-zones"></a>Začínáme s Zóny dostupnosti

- [Vytvoření virtuálního počítače](../virtual-machines/windows/create-portal-availability-zone.md)
- [Přidání spravovaného disku pomocí PowerShellu](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Vytvoření sady škálování virtuálních počítačů v zóně redundantní](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Vyrovnávání zatížení virtuálních počítačů napříč zónami pomocí Standard Load Balancer se zónou redundantního front-endu](../load-balancer/quickstart-load-balancer-standard-public-cli.md)
- [Vyrovnávání zatížení virtuálních počítačů v rámci zóny pomocí Standard Load Balancer s oblastí front-endu](../load-balancer/quickstart-load-balancer-standard-public-cli.md)
- [Zónově redundantní úložiště](../storage/common/storage-redundancy.md)
- [SQL Database](../azure-sql/database/high-availability-sla.md#zone-redundant-configuration)
- [Geografické zotavení po havárii služby Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geografické zotavení po havárii služby Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Vytvoření zónově redundantní brány virtuální sítě](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Přidat redundantní oblast zóny pro Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Začínáme Azure cache pro Zóny dostupnosti Redis](https://aka.ms/redis/az/getstarted)
- [Vytvoření instance Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Vytvoření clusteru služby Azure Kubernetes (AKS), který používá Zóny dostupnosti](../aks/availability-zones.md)

## <a name="next-steps"></a>Další kroky

- [Oblasti, které podporují Zóny dostupnosti v Azure](az-region.md)
- [Šablony pro rychlý start](https://aka.ms/azqs)