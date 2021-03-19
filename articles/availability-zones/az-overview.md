---
title: Oblasti a Zóny dostupnosti v Azure
description: Přečtěte si o oblastech a Zóny dostupnosti v Azure, které odpovídají vašim technickým a zákonným požadavkům.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc
ms.openlocfilehash: dad8661de55fc90c9f3d3782c402deb519d16536
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596004"
---
# <a name="regions-and-availability-zones-in-azure"></a>Oblasti a Zóny dostupnosti v Azure

Služby Microsoft Azure Services jsou k dispozici globálně, aby bylo možné provozovat cloudové operace na optimální úrovni. Podle technických a regulativních hledisek si můžete vybrat nejlepší oblast pro potřeby: možnosti služby, zaregistrování dat, požadavky na dodržování předpisů a latence.

## <a name="terminology"></a>Terminologie

Pro lepší pochopení oblastí a Zóny dostupnosti v Azure vám pomůže pochopit klíčové pojmy nebo koncepty.

| Termín nebo koncept | Description |
| --- | --- |
| oblast | Sada Datacenter nasazených v hraničním prostředí určeném pro latenci a připojená přes vyhrazenou síť s nízkou latencí. |
| geografické | Oblast světa, která obsahuje alespoň jednu oblast Azure. Geografické oblasti definují diskrétní trh, který zachovává rozsahy dat a hranice dodržování předpisů. Zeměpisné oblasti umožňují zákazníkům se specifickými požadavky na rezidenci dat a dodržování předpisů, aby měli svoje data a aplikace blízko. Geografické oblasti jsou odolné proti chybám, které vydržely selhání celé oblasti prostřednictvím připojení k naší vyhrazené síťové infrastruktuře s vysokou kapacitou. |
| Zóna dostupnosti | Jedinečná fyzická umístění v rámci jedné oblasti. Každou zónu tvoří jedno nebo několik datacenter vybavených nezávislým napájením, chlazením a sítí. |
| Doporučená oblast | Oblast, která poskytuje nejširší škálu možností služeb a je navržena pro podporu Zóny dostupnosti nyní nebo v budoucnu. Ty jsou určené v Azure Portal jako **Doporučené**. |
| alternativní (jiná) oblast | Oblast, která rozšiřuje nároky na Azure v rámci hranice sídla dat, kde existuje i doporučená oblast. Alternativní oblasti vám pomůžou optimalizovat latenci a poskytnout druhou oblast pro potřeby zotavení po havárii. Nejsou navržené tak, aby podporovaly Zóny dostupnosti (i když Azure provádí pravidelné hodnocení těchto oblastí a určí, jestli by se měly stát doporučené oblasti). Tyto jsou označeny v Azure Portal jako **jiné**. |
| základní služba | Základní služba Azure, která je dostupná ve všech oblastech, když je oblast všeobecně dostupná. |
| Služba na běžném provozu | Služba Azure, která je k dispozici ve všech doporučených oblastech během 90 dnů od oblasti obecné dostupnosti nebo dostupnosti založené na poptávce v alternativních oblastech. |
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

- **Základní** – k dispozici ve všech doporučených a alternativních oblastech, pokud je oblast všeobecně dostupná, nebo do 90 dnů od nové základní služby, která se stane všeobecně dostupnou.
- General **– k** dispozici ve všech doporučených oblastech během 90 dnů od oblasti obecné dostupnosti; na základě poptávky v alternativních oblastech (mnoho už je nasazených do velké podmnožiny alternativních oblastí).
- **Specializované** – cílené nabídky služeb, často zaměřené na obor nebo na základě vlastního/specializovaného hardwaru. Dostupnost na základě požadavků napříč oblastmi (mnoho je již nasazena do velké podmnožiny doporučených oblastí).

Pokud chcete zjistit, které služby se v dané oblasti nasazují, a také budoucí plán pro náhled nebo obecnou dostupnost služeb v oblasti, přečtěte si článek [dostupné v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=all).

Pokud nabídka služeb není v konkrétní oblasti dostupná, můžete svůj zájem sdílet tím, že se obrátíte na prodejní zástupce Microsoftu.

| Typ oblasti | Bez regionu | Základní | Hlavní fáze | Specializovaná | Zóny dostupnosti | Rezidence dat |
| --- | --- | --- | --- | --- | --- | --- |
| Doporučeno | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | Řízený na základě poptávky | :heavy_check_mark: | :heavy_check_mark: |
| Střídat | :heavy_check_mark: | :heavy_check_mark: | Řízený na základě poptávky | Řízený na základě poptávky | – | :heavy_check_mark: |

### <a name="services-by-category"></a>Služby podle kategorie

Jak už bylo uvedeno výše, Azure klasifikuje služby do tří kategorií: základní, hlavní a specializované. Kategorie služeb jsou přiřazeny při obecné dostupnosti. Služby často spouštějí životní cyklus jako specializovanou službu a jako požadavek a zvýšení využití se můžou zvýšit na běžný nebo základní. V následující tabulce jsou uvedeny kategorie služeb jako základní, hlavní. Měli byste si uvědomit následující informace o tabulce:

- Některé služby jsou jiné než regionální. Informace a seznam neoblastních služeb najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/).
- Starší generace služeb nebo virtuálních počítačů nejsou v seznamu. Další informace najdete v dokumentaci v [předchozích generacích velikostí virtuálních počítačů](../virtual-machines/sizes-previous-gen.md) .
- . Službám není přiřazena žádná kategorie, dokud nebude Obecná dostupnost (GA). Informace a seznam služeb ve verzi Preview najdete v tématu [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). 

> [!div class="mx-tableFixed"]
> | Základní                           | Hlavní fáze                                        | 
> |----------------------------------------|---------------------------------------------------|
> | Účty úložiště                       | API Management                                    | 
> | Application Gateway                    | App Configuration                                 | 
> | Azure Backup                           | App Service                                       | 
> | Azure Cosmos DB                        | Automation                                        | 
> | Azure Data Lake Storage Gen2           | Azure Active Directory Domain Services            | 
> | Azure ExpressRoute                     | Azure Bastion                                     | 
> | Veřejná IP adresa Azure                        | Azure Cache for Redis                             | 
> | Azure SQL Database                     | Azure Cognitive Search                            | 
> | Spravovaná instance Azure SQL             | Azure Cognitive Services                          | 
> | Disk Storage                           | Azure Cognitive Services: Počítačové zpracování obrazu         | 
> | Event Hubs                             | Cognitive Services Azure: Content Moderator       | 
> | Key Vault                              | Azure Cognitive Services: Face                    | 
> | Nástroj pro vyrovnávání zatížení                          | Azure Cognitive Services: moderní čtečka        | 
> | Service Bus                            | Cognitive Services Azure: Language Understanding  | 
> | Service Fabric                         | Azure Cognitive Services: hlasové služby         | 
> | Storage: horká a studená Blob Storage vrstva   | Azure Cognitive Services: Analýza textu          | 
> | Úložiště: Managed Disks                 | Azure Cognitive Services: Translator              | 
> | Virtual Machine Scale Sets             | Průzkumník dat Azure                               | 
> | Virtual Machines                       | Azure Data Share                                  | 
> | Virtual Machines: vyhrazený hostitel Azure | Azure Database for MySQL                          | 
> | Virtual Machines: Av2-Series           | Azure Database for PostgreSQL                     | 
> | Virtual Machines: Bs-Series            | Azure DDoS Protection                             | 
> | Virtual Machines: DSv2-Series          | Azure Firewall                                    | 
> | Virtual Machines: DSv3-Series          | Azure Firewall Manager                            | 
> | Virtual Machines: Dv2-Series           | Azure Functions                                   | 
> | Virtual Machines: Dv3-Series           | Azure IoT Hub                                     |     
> | Virtual Machines: ESv3-Series          | Azure Kubernetes Service (AKS)                    | 
> | Virtual Machines: Ev3-Series           | Azure Machine Learning                            | 
> | Virtual Network                        | Azure Monitor: Application Insights               | 
> | VPN Gateway                            | Azure Monitor: Log Analytics                      | 
> |                                        | Azure Private Link                                | 
> |                                        | Azure Red Hat OpenShift                           | 
> |                                        | Azure Site Recovery                               | 
> |                                        | Azure Stream Analytics                            | 
> |                                        | Azure Synapse Analytics                           | 
> |                                        | Batch                                             | 
> |                                        | Cloud Services: řada M-Series                          | 
> |                                        | Container Instances                               | 
> |                                        | Container Registry                                | 
> |                                        | Data Factory                                      | 
> |                                        | Event Grid                                        | 
> |                                        | HDInsight                                         |  
> |                                        | Logic Apps                                        | 
> |                                        | Media Services                                    | 
> |                                        | Network Watcher                                   | 
> |                                        | Notification Hubs                                 | 
> |                                        | Blob Storage úrovně Premium                              | 
> |                                        | Úložiště Premium Files                             | 
> |                                        | Virtual Machines: Ddsv4-Series                    | 
> |                                        | Virtual Machines: Ddv4-Series                     | 
> |                                        | Virtual Machines: Dsv4-Series                     | 
> |                                        | Virtual Machines: Dv4-Series                      | 
> |                                        | Virtual Machines: Edsv4-Series                    | 
> |                                        | Virtual Machines: Edv4-Series                     | 
> |                                        | Virtual Machines: Esv4-Series                     | 
> |                                        | Virtual Machines: Ev4-Series                      | 
> |                                        | Virtual Machines: Fsv2-Series                     | 
> |                                        | Virtual Machines: řada M-Series                        | 
> |                                        | Virtuální síť WAN                                       | 



### <a name="specialized-services"></a>Specializované služby
Jak už bylo uvedeno výše, Azure klasifikuje služby do tří kategorií: základní, hlavní a specializované. Kategorie služeb jsou přiřazeny při obecné dostupnosti. Služby často spouštějí životní cyklus jako specializovanou službu a jako požadavek a zvýšení využití se můžou zvýšit na běžný nebo základní. V následující tabulce jsou uvedeny specializované služby. 

> [!div class="mx-tableFixed"]
> | Specializovaná                                          |
> |------------------------------------------------------|
> | Azure API for FHIR                                   |
> | Azure Analysis Services                              |
> | Azure Cognitive Services: detektor anomálií           |
> | Cognitive Services Azure: Custom Vision              |
> | Azure Cognitive Services: Nástroj pro rozpoznávání formulářů            |
> | Azure Cognitive Services: přizpůsobování               |
> | Cognitive Services Azure: QnA Maker                  |
> | Azure Database for MariaDB                           |
> | Azure Database Migration Service                     |
> | Rezervované HSM Azure                                  |
> | Azure Digital Twins                                  |
> | Azure Health Bot                                     |
> | Azure HPC Cache                                      |
> | Azure Lab Services                                   |
> | Azure NetApp Files                                   |
> | Služba Azure SignalR                                |
> | Jarní cloudová služba Azure                           |
> | Azure Time Series Insights                           |
> | Azure VMware Solution                                |
> | Azure VMware Solution by CloudSimple                 |
> | Data Lake Analytics                                  |
> | Azure Machine Learning Studio (klasický)              |
> | Spatial Anchors                                      |
> | Úložiště: Archiv služby Storage                             |
> | Ultra Disk Storage                                   |
> | Video Indexer                                        |
> | Virtual Machines: DASv4-Series                       |
> | Virtual Machines: DAv4-Series                        |
> | Virtual Machines: DCsv2-Series                       |
> | Virtual Machines: EASv4-Series                       |
> | Virtual Machines: EAv4-Series                        |
> | Virtual Machines: HBv1-Series                        |
> | Virtual Machines: HBv2-Series                        |
> | Virtual Machines: HCv1-Series                        |
> | Virtual Machines: řada H-Series                           |
> | Virtual Machines: LSv2-Series                        |
> | Virtual Machines: Mv2-Series                         |
> | Virtual Machines: NCv3-Series                        |
> | Virtual Machines: NDv2-Series                        |
> | Virtual Machines: NVv3-Series                        |
> | Virtual Machines: NVv4-Series                        | 
> | Virtual Machines: SAP HANA ve velkých instancích Azure  |




## <a name="next-steps"></a>Další kroky

- [Oblasti, které podporují Zóny dostupnosti v Azure](az-region.md)
- [Šablony pro rychlý start](https://aka.ms/azqs)
