---
title: Oblasti a Zóny dostupnosti v Azure
description: Přečtěte si o oblastech a Zóny dostupnosti v Azure, které odpovídají vašim technickým a zákonným požadavkům.
author: cynthn
ms.service: azure
ms.topic: article
ms.date: 08/27/2020
ms.author: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: b19f5c3ae0666a0b0e9b0255f848f5924d9d3910
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874735"
---
# <a name="regions-and-availability-zones-in-azure"></a>Oblasti a Zóny dostupnosti v Azure

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
| Střídat | :heavy_check_mark: | :heavy_check_mark: | Řízený na základě poptávky | Řízený na základě poptávky | – | :heavy_check_mark: |

### <a name="services-by-category"></a>Služby podle kategorie

Jak už bylo uvedeno výše, Azure klasifikuje služby do tří kategorií: základní, hlavní a specializované. Kategorie služeb jsou přiřazeny při obecné dostupnosti. Služby často spouštějí životní cyklus jako specializovanou službu a jako požadavek a zvýšení využití se můžou zvýšit na běžný nebo základní. V následující tabulce jsou uvedeny kategorie služeb jako základní, hlavní nebo specializované. Měli byste si uvědomit následující informace o tabulce:

- Některé služby jsou jiné než regionální. Informace a seznam neoblastních služeb najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/).
- Virtuální počítače starší generace nejsou uvedeny. Další informace najdete v dokumentaci v [předchozích generacích velikostí virtuálních počítačů](../virtual-machines/sizes-previous-gen.md) .
- . Službám není přiřazena žádná kategorie, dokud nebude Obecná dostupnost (GA). Informace a seznam služeb ve verzi Preview najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). 

> [!div class="mx-tableFixed"]
> | Základní                          | Hlavní fáze                                        | Specializovaná                                          |
> |---------------------------------------|---------------------------------------------------|------------------------------------------------------|
> | Účty úložiště                      | API Management                                    | Azure API for FHIR                                   |
> | Application Gateway                   | App Configuration                                 | Azure Analysis Services                              |
> | Azure Backup                          | App Service                                       | Azure Cognitive Services: detektor anomálií           |
> | Azure Cosmos DB                       | Automation                                        | Cognitive Services Azure: Custom Vision              |
> | Azure Data Lake Storage Gen2          | Azure Active Directory Domain Services            | Azure Cognitive Services: Nástroj pro rozpoznávání formulářů            |
> | Azure ExpressRoute                    | Azure Bastion                                     | Azure Cognitive Services: přizpůsobování               |
> | Veřejná IP adresa Azure                       | Azure Cache for Redis                             | Cognitive Services Azure: QnA Maker                  |
> | Azure SQL Database                    | Azure Cognitive Search                            | Azure Database for MariaDB                           |
> | Azure SQL: spravovaná instance          | Azure Cognitive Services                          | Azure Database Migration Service                     |
> | Cloud Services                        | Azure Cognitive Services: Počítačové zpracování obrazu         | Rezervované HSM Azure                                  |
> | Cloud Services: Av2-Series            | Cognitive Services Azure: Content Moderator       | Azure Digital Twins                                  |
> | Cloud Services: Dv2-Series            | Azure Cognitive Services: Face                    | Robot stavu Azure                                     |
> | Cloud Services: Dv3-Series            | Azure Cognitive Services: moderní čtečka        | Azure HPC Cache                                      |
> | Cloud Services: Ev3-Series            | Cognitive Services Azure: Language Understanding  | Azure Lab Services                                   |
> | Cloud Services: IP adresy na úrovni instance    | Azure Cognitive Services: hlasové služby         | Azure NetApp Files                                   |
> | Cloud Services: Vyhrazená IP adresa           | Azure Cognitive Services: Analýza textu          | Služba Azure SignalR                                |
> | Disk Storage                          | Azure Cognitive Services: Translator              | Jarní cloudová služba Azure                           |
> | Event Hubs                            | Průzkumník dat Azure                               | Azure Time Series Insights                           |
> | Key Vault                             | Azure Data Share                                  | Azure VMware Solution                                |
> | Nástroj pro vyrovnávání zatížení                         | Azure Database for MySQL                          | Azure VMware Solution by CloudSimple                 |
> | Service Bus                           | Azure Database for PostgreSQL                     | Cloud Services: řada H-Series                             |
> | Service Fabric                        | Azure Databricks                                  | Data Catalog                                         |
> | Storage: horká a studená Blob Storage vrstva  | Azure DDoS Protection                             | Data Lake Analytics                                  |
> | Úložiště: Managed Disks                | Azure DevTest Labs                                | Azure Machine Learning Studio (klasický)              |
> | Virtual Machine Scale Sets            | Azure Firewall                                    | Spatial Anchors                                      |
> | Virtual Machines                      | Azure Firewall Manager                            | Úložiště: Archiv služby Storage                             |
> | Virtual Machines: Av2-Series          | Azure Functions                                   | StorSimple                                           |
> | Virtual Machines: Bs-Series           | Azure IoT Hub                                     | Ultra Disk Storage                                   |
> | Virtual Machines: DSv2-Series         | Azure Kubernetes Service (AKS)                    | Video Indexer                                        |
> | Virtual Machines: DSv3-Series         | Azure Machine Learning                            | Virtual Machines: DASv4-Series                       |
> | Virtual Machines: Dv2-Series          | Azure Monitor: Application Insights               | Virtual Machines: DAv4-Series                        |
> | Virtual Machines: Dv3-Series          | Azure Monitor: Log Analytics                      | Virtual Machines: DCsv2-Series                       |
> | Virtual Machines: ESv3-Series         | Azure Private Link                                | Virtual Machines: EASv4-Series                       |
> | Virtual Machines: Ev3-Series          | Azure Red Hat OpenShift                           | Virtual Machines: EAv4-Series                        |
> | Virtual Machines: IP adresy na úrovni instance  | Azure Site Recovery                               | Virtual Machines: HBv1-Series                        |
> | Virtual Machines: Vyhrazená IP adresa         | Azure Stream Analytics                            | Virtual Machines: HBv2-Series                        |
> | Virtual Network                       | Azure Synapse Analytics                           | Virtual Machines: HCv1-Series                        |
> | VPN Gateway                           | Batch                                             | Virtual Machines: řada H-Series                           |
> |                                       | Cloud Services: řada M-Series                          | Virtual Machines: LSv2-Series                        |
> |                                       | Container Instances                               | Virtual Machines: Mv2-Series                         |
> |                                       | Container Registry                                | Virtual Machines: NCv3-Series                        |
> |                                       | Data Factory                                      | Virtual Machines: NDv2-Series                        |
> |                                       | Event Grid                                        | Virtual Machines: NVv3-Series                        |
> |                                       | HDInsight                                         | Virtual Machines: NVv4-Series                        |> 
> |                                       | Logic Apps                                        | Virtual Machines: SAP HANA ve velkých instancích Azure  |
> |                                       | Media Services                                    |                                                      |
> |                                       | Network Watcher                                   |                                                      |
> |                                       | Notification Hubs                                 |                                                      |
> |                                       | Blob Storage úrovně Premium                              |                                                      |
> |                                       | Úložiště Premium Files                             |                                                      |
> |                                       | Virtual Machines: Ddsv4-Series                    |                                                      |
> |                                       | Virtual Machines: Ddv4-Series                     |                                                      |
> |                                       | Virtual Machines: Dsv4-Series                     |                                                      |
> |                                       | Virtual Machines: Dv4-Series                      |                                                      |
> |                                       | Virtual Machines: Edsv4-Series                    |                                                      |
> |                                       | Virtual Machines: Edv4-Series                     |                                                      |
> |                                       | Virtual Machines: Esv4-Series                     |                                                      |
> |                                       | Virtual Machines: Ev4-Series                      |                                                      |
> |                                       | Virtual Machines: Fsv2-Series                     |                                                      |
> |                                       | Virtual Machines: řada M-Series                        |                                                      |
> |                                       | Virtual WAN                                       |                                                      |


## <a name="next-steps"></a>Další kroky

- [Oblasti, které podporují Zóny dostupnosti v Azure](az-region.md)
- [Šablony pro rychlý start](https://aka.ms/azqs)