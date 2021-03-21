---
title: Přehled řešení řady StorSimple 8000 | Microsoft Docs
description: Popisuje vrstvení StorSimple, zařízení, virtuální zařízení, služby a správu úložiště a zavádí Klíčové výrazy používané v StorSimple.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 7144d218-db21-4495-88fb-e3b24bbe45d1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/02/2021
ms.author: timlt
ms.openlocfilehash: 3f0ec053d806208b4a3c15604714f6bdb87399e7
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102613217"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>Série StorSimple 8000: řešení hybridního cloudového úložiště

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Přehled
Vítá vás Microsoft Azure StorSimple integrované řešení úložiště, které spravuje úlohy úložiště mezi místními zařízeními a Microsoft Azure cloudového úložiště. StorSimple je efektivní, cenově výhodné a snadno spravovatelné řešení sítě SAN (Storage Area Network), které eliminuje mnohé problémy a výdaje spojené s podnikovým úložištěm a ochranou dat. Využívá vlastní zařízení řady StorSimple 8000, integruje se s cloudovými službami a poskytuje sadu nástrojů pro správu umožňující bezproblémové zobrazení veškerého podnikového úložiště, včetně cloudového úložiště. (Informace o nasazení StorSimple publikované na webu Microsoft Azure se vztahují jenom na zařízení řady StorSimple 8000. Pokud používáte zařízení řady StorSimple 5000/7000, přečtěte si [StorSimple Help](http://onlinehelp.storsimple.com/).)

StorSimple využívá [vrstvení úložiště](#automatic-storage-tiering) ke správě uložených dat napříč různými úložnými médii. Aktuální pracovní sada je uložená místně na jednotkách SSD (Solid State Drive) (SSD). Data, která se používají méně často, se ukládají na jednotky pevného disku (HDD) a Archivovaná data se odesílají do cloudu. StorSimple navíc využívá odstranění duplicit a kompresi ke snížení velikosti úložiště, které data spotřebovávají. Další informace najdete na webu [odstraňování duplicit a komprimaci](#deduplication-and-compression). Definice dalších klíčových pojmů a konceptů, které se používají v dokumentaci k řadě StorSimple 8000, najdete na konci tohoto článku v tématu [terminologie StorSimple](#storsimple-terminology) .

Kromě správy úložiště vám funkce ochrany dat StorSimple umožňují vytvářet a naplánovaná zálohování na vyžádání a pak je ukládat místně nebo v cloudu. Zálohy jsou pořízeny ve formě přírůstkových snímků, což znamená, že je můžete rychle vytvořit a obnovit. Cloudové snímky můžou být kriticky důležité ve scénářích zotavení po havárii, protože nahrazují sekundární systémy úložiště (například zálohování na pásku) a umožňují v případě potřeby obnovovat data v datovém centru nebo v alternativních lokalitách.

![ikona videa](./media/storsimple-overview/video_icon.png) Podívejte se na video, kde se dozvíte, jak Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Proč používat StorSimple?
Následující tabulka popisuje některé klíčové výhody, které Microsoft Azure StorSimple poskytuje.

| Funkce | Výhoda |
| --- | --- |
| Transparentní integrace |Používá protokol iSCSI k neviditelnému propojení úložných zařízení s daty. Data uložená v cloudu, v datacentru nebo na vzdálených serverech se zdají být uložená v jednom umístění. |
| Snížení nákladů na úložiště |Přiděluje dostatečné místní nebo cloudové úložiště pro splnění aktuálních požadavků a rozšiřuje cloudové úložiště pouze v případě potřeby. Tím se snižuje nároky na úložiště a výdaje tím, že odstraňují redundantní verze stejných dat (odstranění duplicitních dat) a pomocí komprese. |
| Zjednodušená správa úložiště |Poskytuje nástroje pro správu systému, které slouží ke konfiguraci a správě místně uložených dat, na vzdáleném serveru a v cloudu. Kromě toho můžete spravovat funkce zálohování a obnovení z modulu snap-in konzoly MMC (Microsoft Management Console).|
| Lepší zotavení po havárii a dodržování předpisů |Nevyžaduje prodloužený čas obnovení. Místo toho obnoví data, jak je potřeba, aby běžné operace mohly pokračovat s minimálním přerušením. Kromě toho můžete nakonfigurovat zásady, které určují plány zálohování a uchovávání dat. |
| Mobilita dat |Data nahraná do Microsoft Azure Cloud Services se dají z jiných lokalit vycházet z důvodů obnovení a migrace. Kromě toho můžete pomocí StorSimple nakonfigurovat cloudová zařízení StorSimple na virtuálních počítačích běžících v systému Microsoft Azure. Virtuální počítače pak můžou pomocí virtuálních zařízení přistupovat k uloženým datům pro účely testování nebo obnovení. |
| Kontinuita podnikových procesů |Umožňuje uživatelům řady StorSimple 5000-7000 migrovat data do zařízení StorSimple 8000 series. |
| Dostupnost na portálu Azure Government |StorSimple je k dispozici na portálu Azure Government. Další informace najdete v tématu [nasazení místního zařízení StorSimple na portálu pro státní](storsimple-8000-deployment-walkthrough-gov-u2.md)správu. |
| Ochrana dat a dostupnost |Řada StorSimple 8000 podporuje úložiště ZRS (Zone redundantní úložiště), kromě místně redundantního úložiště (LRS) a geograficky redundantního úložiště (GRS). Podrobnosti o ZRS najdete [v tomto článku týkajícím se Azure Storage možností redundance](../storage/common/storage-redundancy.md) . |
| Podpora pro kritické aplikace |StorSimple umožňuje identifikovat vhodné svazky jako místně připnuté, aby se zajistilo, že data, která jsou nutná pro kritické aplikace, nebudou vrstvena do cloudu. Místně připnuté svazky nepodléhají latencím v cloudu ani problémům s připojením. Další informace o místně připojených svazcích najdete v tématu věnovaném [správě svazků pomocí služby StorSimple Správce zařízení](storsimple-8000-manage-volumes-u2.md). |
| Nízká latence a vysoký výkon |Můžete vytvářet cloudová zařízení, která využívají vysoce výkonné a nízké latence Azure Premium Storage. Další informace o cloudových zařízeních StorSimple Premium najdete v tématu [nasazení a správa StorSimple Cloud Appliance v Azure](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>Součásti StorSimple
Řešení Microsoft Azure StorSimple zahrnuje následující součásti:

* **Microsoft Azure StorSimple zařízení** – místní pole hybridního úložiště, které obsahuje SSD a HDD, společně s redundantními řadiči a možnostmi automatického převzetí služeb při selhání. Řadiče spravují vrstvení úložiště, umísťují aktuálně používaná (nebo horká) data do místního úložiště (v zařízení nebo na místních serverech) a při přesunu méně často využívaných dat do cloudu.
* **StorSimple Cloud Appliance** – také označované jako virtuální zařízení StorSimple. Softwarová verze zařízení StorSimple, která replikuje architekturu a většinu funkcí fyzického hybridního úložného zařízení. StorSimple Cloud Appliance běží na jednom uzlu na virtuálním počítači Azure. Virtuální zařízení úrovně Premium, která využívají službu Azure Premium Storage, jsou k dispozici v Update 2 a novějších.
* **Služba StorSimple Správce zařízení** – rozšíření Azure Portal, které umožňuje spravovat zařízení StorSimple nebo StorSimple Cloud Appliance z jednoho webového rozhraní. Pomocí služby StorSimple Správce zařízení můžete vytvářet a spravovat služby, zobrazovat a spravovat zařízení, zobrazovat výstrahy, spravovat svazky a zobrazovat a spravovat zásady zálohování a katalog záloh.
* **Windows PowerShell pro StorSimple** – rozhraní příkazového řádku, které můžete použít ke správě zařízení StorSimple. Windows PowerShell pro StorSimple mají funkce, které vám umožní zaregistrovat zařízení StorSimple, nakonfigurovat síťové rozhraní na zařízení, nainstalovat určité typy aktualizací, vyřešit potíže s vaším zařízením a získat přístup k relaci podpory a změnit stav zařízení. Přístup k Windows PowerShell pro StorSimple získáte tak, že se připojíte ke konzole sériového prostředí nebo pomocí vzdálené komunikace Windows PowerShellu.
* **Azure PowerShell rutiny StorSimple** – kolekce rutin prostředí Windows PowerShell, které umožňují automatizovat úlohy na úrovni služby a migrace z příkazového řádku. Další informace o rutinách Azure PowerShell pro StorSimple najdete v části [Reference k rutinám](/powershell/module/servicemanagement/azure.service/#azure).
* **StorSimple Snapshot Manager** – modul snap-in konzoly MMC, který používá skupiny svazků a služba Stínová kopie svazku Windows ke generování záloh konzistentních vzhledem k aplikacím. Kromě toho můžete k vytváření plánů zálohování a klonování a obnovování svazků použít Snapshot Manager StorSimple.
* **Adaptér StorSimple pro službu SharePoint** – nástroj, který transparentně rozšiřuje Microsoft Azure StorSimple úložiště a ochrany dat na serverové farmy služby SharePoint, a současně umožňuje zobrazit a spravovat úložiště StorSimple z portálu centrální správy služby SharePoint.

Diagram níže poskytuje podrobný pohled na architekturu Microsoft Azure StorSimple a součásti.

![Architektura StorSimple](./media/storsimple-overview/overview-big-picture.png)

Následující části popisují každou z těchto komponent podrobněji a vysvětlují, jak řešení uspořádává data, přiděluje úložiště a usnadňuje správu úložiště a ochranu dat. Poslední část poskytuje definice pro některé z důležitých podmínek a konceptů, které se vztahují k StorSimple komponentám a jejich správě.

## <a name="storsimple-device"></a>Zařízení StorSimple
Zařízení Microsoft Azure StorSimple je místní pole hybridního úložiště, které poskytuje primární úložiště a přístup k iSCSI k datům uloženým v něm. Spravuje komunikaci s cloudovým úložištěm a pomáhá zajistit zabezpečení a důvěrnost všech dat uložených v řešení Microsoft Azure StorSimple.

Zařízení StorSimple zahrnuje SSD a jednotky pevného disku (HDD) a také podporu clusteringu a automatického převzetí služeb při selhání. Obsahuje sdílený procesor, sdílené úložiště a dva zrcadlené řadiče. Každý kontroler poskytuje následující:

* Připojení k hostitelskému počítači
* Až šest síťových portů pro připojení k místní síti (LAN)
* Monitorování hardwaru
* Nestálá paměť s náhodným přístupem (NVRAM), která uchovává informace i v případě přerušení napájení
* Aktualizace pro clustery pro správu aktualizací softwaru na serverech v clusteru s podporou převzetí služeb při selhání, aby aktualizace měly minimální nebo žádný vliv na dostupnost služby
* Clusterová služba, což funguje jako back-end cluster, poskytuje vysokou dostupnost a minimalizuje případné nepříznivé důsledky, které mohou nastat, pokud dojde k chybě pevného disku nebo SSD nebo pokud je offline

V každém okamžiku je aktivní jenom jeden kontroler. Pokud dojde k chybě aktivního řadiče, bude druhý řadič aktivní automaticky.

Další informace najdete v [části hardwarové komponenty a stav StorSimple](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
Pomocí StorSimple můžete vytvořit cloudové zařízení, které bude replikovat architekturu a možnosti fyzického hybridního úložného zařízení. StorSimple Cloud Appliance (označované také jako virtuální zařízení StorSimple) běží na jednom uzlu na virtuálním počítači Azure. (Cloudové zařízení se dá vytvořit jenom na virtuálním počítači Azure. Nemůžete ho vytvořit na zařízení StorSimple nebo na místním serveru.)

Cloudové zařízení má následující funkce:

* Chová se jako fyzické zařízení a může nabídnout rozhraní iSCSI pro virtuální počítače v cloudu.
* V cloudu můžete vytvářet neomezený počet cloudových zařízení a v případě potřeby je zapnout nebo vypnout.
* Může přispět k simulaci místních prostředí v rámci scénářů zotavení po havárii, vývoje a testování a může pomáhat s načítáním na úrovni položek ze záloh.

StorSimple Cloud Appliance je k dispozici ve dvou modelech: zařízení 8010 (dříve označované jako 1100 model) a zařízení 8020. Zařízení 8010 má maximální kapacitu 30 TB. Zařízení 8020, které využívá výhod služby Azure Premium Storage, má maximální kapacitu 64 TB. (V místních vrstvách Azure Premium Storage ukládá data na SSD, zatímco úložiště úrovně Standard ukládá data na HDD.) Abyste mohli používat Premium Storage, musíte mít účet služby Azure Premium Storage.

Další informace o StorSimple Cloud Appliance najdete [v části nasazení a správa StorSimple Cloud Appliance v Azure](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple
Microsoft Azure StorSimple poskytuje webové uživatelské rozhraní (Služba StorSimple Správce zařízení), které umožňuje centrální správu datacentra a cloudového úložiště. Službu StorSimple Správce zařízení můžete použít k provádění následujících úloh:

* Nakonfigurujte systémová nastavení pro zařízení StorSimple.
* Konfigurace a Správa nastavení zabezpečení pro zařízení StorSimple.
* Nakonfigurujte přihlašovací údaje a vlastnosti cloudu.
* Konfigurace a správa svazků na serveru.
* Nakonfigurujte skupiny svazků.
* Zálohování a obnovení dat.
* Monitorujte výkon.
* Zkontrolujte nastavení systému a Identifikujte možné problémy.

Službu StorSimple Správce zařízení můžete použít k provádění všech úloh správy s výjimkou úloh, které vyžadují čas v systému, například počáteční nastavení a instalace aktualizací.

Další informace najdete v [části používání služby StorSimple Správce zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell pro StorSimple
Windows PowerShell pro StorSimple poskytuje rozhraní příkazového řádku, které můžete použít k vytvoření a správě služby Microsoft Azure StorSimple a nastavení a monitorování zařízení StorSimple. Jedná se o rozhraní příkazového řádku založené na Windows PowerShellu, které zahrnuje vyhrazené rutiny pro správu zařízení StorSimple. Windows PowerShell pro StorSimple mají funkce, které vám umožní:

* Zaregistrujte zařízení.
* Nakonfigurujte síťové rozhraní na zařízení.
* Nainstalujte určité typy aktualizací.
* Pomocí přístupu k relaci podpory vyřešte potíže s vaším zařízením.
* Změňte stav zařízení.

K Windows PowerShell pro StorSimple můžete přistupovat ze sériové konzoly (na hostitelském počítači připojeném přímo k zařízení) nebo vzdáleně pomocí vzdálené komunikace Windows PowerShellu. Některé úlohy Windows PowerShell pro StorSimple, jako je například počáteční registrace zařízení, lze provádět pouze v konzole sériového portu.

Další informace najdete na webu [použití Windows PowerShell pro StorSimple ke správě zařízení](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Rutiny Azure PowerShell StorSimple
Rutiny Azure PowerShell StorSimple jsou kolekcí rutin prostředí Windows PowerShell, které umožňují automatizovat úlohy na úrovni služby a migrace z příkazového řádku. Další informace o rutinách Azure PowerShell pro StorSimple najdete v části [Reference k rutinám](/powershell/module/servicemanagement/azure.service/).

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
StorSimple Snapshot Manager je modul snap-in konzoly MMC (Microsoft Management Console), který můžete použít k vytvoření konzistentních záložních kopií místních a cloudových dat v určitém časovém okamžiku. Modul snap-in běží na hostiteli založeném na systému Windows Server. StorSimple Snapshot Manager můžete použít k těmto akcím:

* Nakonfigurujte, zálohujte a odstraňte svazky.
* Nakonfigurujte skupiny svazků, aby se zajistilo, že zálohovaná data jsou konzistentní vzhledem k aplikacím.
* Spravujte zásady zálohování tak, aby se data zálohovali podle předem určeného plánu a ukládala v určeném umístění (místně nebo v cloudu).
* Obnovte svazky a jednotlivé soubory.

Zálohy jsou zachyceny jako snímky, které zaznamenávají pouze změny od posledního snímku a vyžadují mnohem méně místa úložiště než úplné zálohování. Můžete vytvářet plány zálohování nebo podle potřeby provádět okamžité zálohování. Kromě toho můžete pomocí StorSimple Snapshot Manager vytvořit zásady uchovávání informací, které určují, kolik snímků bude uloženo. Pokud budete později potřebovat obnovit data ze zálohy, StorSimple Snapshot Manager umožní vybrat z katalogu místních nebo cloudových snímků. 

Pokud dojde k havárii nebo pokud potřebujete obnovit data z jiného důvodu, StorSimple Snapshot Manager v případě potřeby obnoví přírůstkově. Obnovení dat nevyžaduje, abyste při obnovení souboru vypnuli celý systém, nahradili zařízení nebo přesunuli operace do jiné lokality.

Další informace najdete v [StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>StorSimple Adapter pro SharePoint
Microsoft Azure StorSimple zahrnuje adaptér StorSimple pro službu SharePoint, volitelná součást, která transparentně rozšiřuje funkce úložiště StorSimple a ochrany dat na serverové farmy služby SharePoint. Adaptér spolupracuje se zprostředkovatelem vzdáleného Blob Storage (RBS) a funkcí SQL Server RBS, což vám umožní přesunout objekty blob na server zálohovaný systémem Microsoft Azure StorSimple. Microsoft Azure StorSimple pak data objektů BLOB uloží místně nebo v cloudu na základě využití.

Adaptér StorSimple pro SharePoint je spravován z portálu centrální správy služby SharePoint. Správa SharePointu zůstane centralizovaná a všechna úložiště se zdají být ve farmě SharePointu.

Další informace najdete v [StorSimple adapteru pro SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Technologie správy úložiště
Kromě vyhrazeného zařízení StorSimple, virtuálního zařízení a dalších součástí Microsoft Azure StorSimple používá následující softwarové technologie k poskytování rychlého přístupu k datům a snížení spotřeby úložiště:

* [Automatické vrstvení úložiště](#automatic-storage-tiering) 
* [Dynamické zajišťování](#thin-provisioning) 
* [Odstranění duplicit a komprese](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Automatické vrstvení úložiště
Microsoft Azure StorSimple automaticky uspořádá data v logických vrstvách na základě aktuálního využití, stáří a vztahu k dalším datům. Data, která jsou aktivní, se ukládají místně, zatímco méně aktivní a neaktivní data se automaticky migrují do cloudu. Následující diagram znázorňuje tento přístup k úložišti.

![StorSimple úrovně úložiště](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Pokud chcete povolit rychlý přístup, StorSimple ukládá na SSD na zařízení StorSimple velmi aktivní data (Hot data). Ukládá data, která se používají občas (teplé data) na HDD v zařízení nebo na serverech v datacentru. Přesouvá neaktivní data, zálohovaná data a data uchovávaná pro účely archivace nebo dodržování předpisů do cloudu. 

> [!NOTE]
> V části aktualizace 2 nebo novější můžete určit svazek jako připnuté místně. v takovém případě data zůstanou na místním zařízení a nevrstvená do cloudu. 


StorSimple upravuje a mění uspořádání dat a přiřazení úložiště při změně vzorců využití. Některé informace mohou být například méně aktivní v průběhu času. Vzhledem k tomu, že dojde k průběžnému menšímu množství aktivních, migruje se z SSD do HDD a potom do cloudu. Pokud se tato data znovu aktivují, migrují se zpátky na paměťové zařízení.

Proces vrstvení úložiště probíhá takto:

1. Správce systému nastaví Microsoft Azure účet cloudového úložiště.
2. Správce používá sériovou konzoli a službu StorSimple Správce zařízení (spuštěnou v Azure Portal) ke konfiguraci zařízení a souborového serveru, vytváření svazků a zásad ochrany dat. Místní počítače (například souborové servery) k přístupu k zařízení StorSimple používají rozhraní iSCSI (Internet Small Computer System Interface).
3. Zpočátku StorSimple ukládá data do vrstvy SSD (Fast SSD) zařízení.
4. V rámci přístupů do vrstvy SSD StorSimple deduplikuje a komprimuje nejstarší datové bloky a přesouvá je do vrstvy HDD.
5. S ohledem na kapacitu úrovně HDD StorSimple šifruje nejstarší datové bloky a bezpečně je odesílá do Microsoft Azure účtu úložiště prostřednictvím protokolu HTTPS.
6. Microsoft Azure vytvoří více replik dat ve svém datovém centru a ve vzdáleném datovém centru, aby bylo zajištěno, že data bude možné obnovit, pokud dojde k havárii.
7. Když souborový server požaduje data uložená v cloudu, StorSimple ho bezproblémově vrátí a uloží kopii do vrstvy SSD zařízení StorSimple.

> [!IMPORTANT]
> Pokud používáte StorSimple, neprovádějte převod objektů blob do archivace, i když se vaše zařízení postupně vymění. Pokud chcete načíst data ze zařízení, budete muset znovu vyměnit objekty BLOB z archivace na horkou nebo studenou, což vede k významným nákladům.

#### <a name="how-storsimple-manages-cloud-data"></a>Jak StorSimple spravuje cloudová data

StorSimple provádí odstranění duplicitních dat zákazníků napříč všemi snímky a primárními daty (daty zapsanými hostiteli). I když je odstranění duplicitních dat Skvělé pro efektivitu úložiště, je otázka "Co je v cloudu" složitá. Vrstvená primární data a data snímků se vzájemně překrývají. Jeden blok dat v cloudu se dá použít jako vrstvené primární data a taky se na něj odkazuje několika snímky. Každý snímek v cloudu zajišťuje, aby se kopie všech dat v určitém bodě v čase uzamknul do cloudu, dokud se tento snímek neodstraní.

Data se z cloudu odstraní jenom v případě, že na tato data neexistují žádné odkazy. Pokud jste například zavedli cloudový snímek všech dat, která se nacházejí v zařízení StorSimple, a pak některá primární data odstranili, uvidíme, že se _primární_ vynechává okamžitě. _Data v cloudu_, která zahrnují vrstvená data a zálohy, zůstávají stejná, protože snímek stále odkazuje na cloudová data. Po odstranění snímku v cloudu (a všech dalších snímků, které odkazovaly na stejná data), dojde k poklesu využití cloudu. Před odebráním cloudových dat zkontrolujeme, že žádné snímky ještě neodkazují na tato data. Tento proces se nazývá _uvolňování paměti_ a na zařízení běží služba na pozadí. Odebrání dat z cloudu není okamžité, protože služba uvolňování paměti kontroluje další odkazy na tato data před odstraněním. Rychlost uvolňování paměti závisí na celkovém počtu snímků a v celkových datech. Data v cloudu se většinou vyčistí za méně než týden.


### <a name="thin-provisioning"></a>Dynamické zajišťování
Dynamické zajišťování je virtualizační technologie, ve které se zdá, že dostupné úložiště překračuje fyzické prostředky. Místo opětovné obsluhy dostatečného úložného prostoru StorSimple využívá dynamické zajišťování k přidělení dostatečného prostoru pro splnění aktuálních požadavků. Elastická povaha cloudového úložiště usnadňuje tento přístup, protože StorSimple může zvýšit nebo snížit cloudové úložiště tak, aby splňovalo měnící se požadavky.

> [!NOTE]
> Místně připnuté svazky nejsou dynamicky zřízeny. Úložiště přidělené k místnímu svazku se zřídí v celém rozsahu, kdy se svazek vytvoří.


### <a name="deduplication-and-compression"></a>Odstranění duplicit a komprese
Microsoft Azure StorSimple využívá odstranění duplicit a kompresi dat k dalšímu snížení požadavků na úložiště.

Odstranění duplicitních dat snižuje celkové množství uložených dat odstraněním redundance v uložené datové sadě. Když se změní informace, StorSimple ignoruje nezměněná data a zachytává jenom změny. StorSimple navíc snižuje množství uložených dat tím, že identifikuje a odebírá nepotřebné informace. 

> [!NOTE]
> Data na místně připojených svazcích nejsou Odstraněná a komprimovaná. Zálohy místně připnutého svazku jsou však odstraněny a komprimovány.


## <a name="storsimple-workload-summary"></a>Souhrn úlohy StorSimple
Souhrn podporovaných úloh StorSimple je v tabulce.

| Scenario | Úloha | Podporováno | Omezení | Verze |
| --- | --- | --- | --- | --- |
| Spolupráce |Sdílení souborů |Yes | |Všechny verze |
| Spolupráce |Sdílení distribuovaných souborů |Yes | |Všechny verze |
| Spolupráce |SharePoint |Ano* |Podporováno pouze s místně připnuté svazky |Aktualizace 2 a novější |
| Archivace |Jednoduché archivace souborů |Yes | |Všechny verze |
| Virtualizace |Virtuální počítače |Ano* |Podporováno pouze s místně připnuté svazky |Aktualizace 2 a novější |
| databáze |SQL |Ano* |Podporováno pouze s místně připnuté svazky |Aktualizace 2 a novější |
| Střežení kamerami |Střežení kamerami |Ano* |Podporováno, pokud je zařízení StorSimple vyhrazené jenom pro tuto úlohu. |Aktualizace 2 a novější |
| Backup |Primární cílová záloha |Ano* |Podporováno, pokud je zařízení StorSimple vyhrazené jenom pro tuto úlohu. |Aktualizace 3 a novější |
| Backup |Zálohování sekundárního cíle |Ano* |Podporováno, pokud je zařízení StorSimple vyhrazené jenom pro tuto úlohu. |Aktualizace 3 a novější |

*Ano&#42; – měla by se použít pravidla a omezení řešení.*

Zařízení řady StorSimple 8000 nepodporují následující úlohy. Při nasazení na StorSimple budou tyto úlohy mít za následek nepodporovanou konfiguraci.

* Lékařské zpracování
* Výměna
* VDI
* Oracle
* SAP
* Velký objem dat
* Distribuce obsahu
* Spuštění z SCSI

Níže je seznam podporovaných komponent infrastruktury StorSimple.

| Scenario | Úloha | Podporováno | Omezení | Verze |
| --- | --- | --- | --- | --- |
| Obecné |Express Route |Yes | |Všechny verze |
| Obecné |DataCore FC |Ano* |Podporováno s DataCore SANsymphony |Všechny verze |
| Obecné |DFSR |Ano* |Podporováno pouze s místně připnuté svazky |Všechny verze |
| Obecné |Indexování |Ano* |Pro vrstvené svazky se podporují jenom indexování metadat (žádná data).<br>Pro místně připojené svazky se podporuje kompletní indexování. |Všechny verze |
| Obecné |Antivirus |Ano* |V případě vrstvených svazků je podporována pouze kontrola otevřených a zavírání.<br> Pro místně připnuté svazky se podporuje úplná kontrola. |Všechny verze |

*Ano&#42; – měla by se použít pravidla a omezení řešení.*

Následuje seznam jiného softwaru používaného pro StorSimple k vytváření řešení.

| Typ úlohy | Software používaný s StorSimple | Podporované verze|Odkaz na průvodce řešením| 
| --- | --- | --- | --- |
| Cíl zálohování |Veeam |Veeam v 9 a novější |[StorSimple jako cíl zálohování s Veaam](storsimple-configure-backup-target-veeam.md)|
| Cíl zálohování |Veritas Backup Exec |Backup Exec 16 a novější |[StorSimple jako cíl zálohování pomocí Backup Exec](storsimple-configure-backup-target-using-backup-exec.md)|
| Cíl zálohování |VERITAS NetBackup |NetBackup 7.7. x a novější  |[StorSimple jako cíl zálohování s NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Globální sdílení souborů <br></br> Spolupráce |Talon  |[StorSimple s Talon](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>Terminologie StorSimple
Před nasazením řešení Microsoft Azure StorSimple doporučujeme projít si následující pojmy a definice.

### <a name="key-terms-and-definitions"></a>Klíčové pojmy a definice
| Výraz (akronym nebo zkratka) | Description |
| --- | --- |
| záznam řízení přístupu (ACR) |Záznam přidružený ke svazku na vašem zařízení Microsoft Azure StorSimple, který určuje, kteří hostitelé se k němu mohou připojit. Stanovení vychází z kvalifikovaného názvu iSCSI (IQN) hostitelů (obsažených v ACR), které se připojují k vašemu zařízení StorSimple. |
| AES-256 |Algoritmus 256 standard AES (Advanced Encryption Standard) (AES) pro šifrování dat při jejich přesunu do cloudu a z něj. |
| velikost alokační jednotky (Austrálie) |Nejmenší množství místa na disku, které se dá přidělit pro uložení souboru v systémech souborů Windows. Pokud velikost souboru není sudým násobkem velikosti clusteru, je nutné použít k uložení souboru navíc místo (až na další násobek velikosti clusteru), což vede ke ztrátě místa a fragmentaci pevného disku. <br>Doporučené jednotky Austrálie pro svazky Azure StorSimple jsou 64 KB, protože dobře funguje s algoritmy odstranění duplicit. |
| automatizované vrstvení úložiště |Automatické přesouvání méně aktivních dat z SSD do HDD a potom do vrstvy v cloudu a následné povolení správy všech úložišť z centrálního uživatelského rozhraní. |
| katalog záloh |Kolekce záloh, které se obvykle vztahují k používanému typu aplikace. Tato kolekce se zobrazí v okně katalog záloh v uživatelském rozhraní služby StorSimple Správce zařízení. |
| záložní soubor katalogu |Soubor obsahující seznam dostupných snímků aktuálně uložených v záložní databázi StorSimple Snapshot Manager. |
| zásady zálohování |Výběr svazků, typu zálohování a času, který umožňuje vytvářet zálohy podle předdefinovaného plánu. |
| binární velké objekty (bloby) |Kolekce binárních dat uložených jako jediná entita v systému správy databáze. Objekty blob jsou obvykle obrázky, zvuk nebo jiné multimediální objekty, i když je binární spustitelný kód uložený jako objekt BLOB. |
| Protokol CHAP (Challenge Handshake Authentication Protocol) |Protokol, který slouží k ověření partnerského vztahu připojení, na základě partnerského sdílení hesla nebo tajného klíče. Protokol CHAP může být jednosměrný nebo společný. V případě jednosměrného protokolu CHAP cíl ověřuje iniciátor. Vzájemné ověřování protokolem CHAP vyžaduje, aby cíl ověřil iniciátora a aby iniciátor ověřil cíl. |
| clone |Duplicitní kopie svazku. |
| Cloud jako úroveň (CaaT) |Cloudové úložiště integrované jako vrstva v rámci architektury úložiště, aby se všechny úložiště zobrazovalo jako součást jedné podnikové sítě úložiště. |
| poskytovatel cloudových služeb (CSP) |Poskytovatel služeb cloud computingu. |
| snímek cloudu |K určitému bodu v čase se ukládají data svazků, která jsou uložená v cloudu. Snímek v cloudu je ekvivalentní snímku replikovanému na jiném systému úložiště mimo lokalitu. Cloudové snímky jsou zvláště užitečné ve scénářích zotavení po havárii. |
| šifrovací klíč cloudového úložiště |Heslo nebo klíč, pomocí kterého zařízení StorSimple přistupují k šifrovaným datům odesílaným vaším zařízením do cloudu. |
| aktualizace pro clustery |Správa aktualizací softwaru na serverech v clusteru s podporou převzetí služeb při selhání, aby aktualizace měly minimální nebo žádný vliv na dostupnost služby. |
| Cesta k DataPath |Kolekce funkčních jednotek, které provádějí operace zpracování dat mezi propojenými daty. |
| deaktivovat |Trvalá akce, která ruší spojení mezi zařízením StorSimple a přidruženou cloudovou službou. Cloudové snímky zařízení zůstanou po tomto procesu a dají se klonovat nebo použít k zotavení po havárii. |
| zrcadlení disku |Replikace svazků logických disků na samostatných pevných discích v reálném čase, aby se zajistila nepřetržitá dostupnost. |
| zrcadlení dynamického disku |Replikace svazků logických disků na dynamických discích. |
| dynamické disky |Formát svazku disku, který používá správce logických disků (LDM) k ukládání a správě dat napříč několika fyzickými disky. Dynamické disky je možné zvětšit, aby poskytovaly více volného místa. |
| EBOD (Extended Disks) – skříň |Sekundární skříň zařízení Microsoft Azure StorSimple, která obsahuje další disky pevného disku pro další úložiště. |
| zřizování systému souborů FAT |Konvenční zřizování úložiště, ve kterém je prostor úložiště přidělený na základě předpokládaných potřeb (a obvykle je mimo aktuální potřebu). Viz také *dynamické zajišťování*. |
| pevný disk (HDD) |Jednotka, která k ukládání dat používá otáčení Platters |
| Hybridní cloudové úložiště |Architektura úložiště, která používá místní a mimo lokalitu, včetně cloudového úložiště. |
| Rozhraní iSCSI (Internet Small Computer System Interface) |Síť Standard úložiště založená na Internet Protocol (IP) pro propojení úložného zařízení nebo zařízení. |
| Iniciátor iSCSI |Softwarová součást, která umožňuje hostitelskému počítači se systémem Windows připojit se k externí síti úložiště založené na standardu iSCSI. |
| Kvalifikovaný název iSCSI (IQN) |Jedinečný název, který identifikuje cíl iSCSI nebo iniciátora. |
| cíl iSCSI |Softwarová součást, která poskytuje centralizované subsystémy disků iSCSI v sítích San. |
| živá archivace |Přístup k úložišti, ve kterém jsou k dispozici archivní data po celou dobu (například není uloženo mimo lokalitu na pásce). Microsoft Azure StorSimple používá živou archivaci. |
| místně připojený svazek |svazek, který se nachází v zařízení a nikdy není vrstvený do cloudu. |
| místní snímek |Data svazku v čase, která jsou uložená na Microsoft Azure StorSimplem zařízení. |
| Microsoft Azure StorSimple |Výkonné řešení skládající se z úložného zařízení a softwaru datového centra, které organizacím IT umožňuje využívat cloudové úložiště, jako by šlo o datacentrum úložiště. StorSimple zjednodušuje ochranu dat a správu dat a současně snižuje náklady. Řešení konsoliduje primární úložiště, archiv, zálohování a zotavení po havárii (DR) prostřednictvím bezproblémové integrace s cloudem. Díky kombinaci úložiště sítě SAN a cloudové správy dat na platformě na podnikové úrovni StorSimple zařízení umožňují zrychlit, jednoduchost a spolehlivost pro všechny potřeby související s úložištěm. |
| Modul Power and chlazení (PCM) |Hardwarové součásti zařízení StorSimple sestávající z napájení a chladicího ventilátoru; Proto název modulu napájení a chlazení. Primární skříň zařízení má dvě 764W PCMs, zatímco skříň EBOD má dvě 580W PCMs. |
| primární skříň |Hlavní skříň zařízení StorSimple, která obsahuje řadiče aplikační platformy. |
| cíl doby obnovení (RTO) |Maximální doba, která by měla být vynaložena před úplným obnovením obchodního procesu nebo systému po havárii. |
| SAS (Serial Attached SCSI) |Typ jednotky pevného disku (HDD). |
| šifrovací klíč dat služby |Klíč zpřístupněný pro jakékoli nové zařízení StorSimple, které se registruje ve službě StorSimple Správce zařízení. Konfigurační data přenesená mezi službou StorSimple Správce zařízení a zařízením jsou šifrovaná pomocí veřejného klíče a je pak možné je dešifrovat jenom v zařízení pomocí privátního klíče. Šifrovací klíč dat služby umožňuje službě získat tento privátní klíč k dešifrování. |
| registrační klíč služby |Klíč, který pomáhá zaregistrovat zařízení StorSimple ve službě StorSimple Správce zařízení, aby se zobrazila v Azure Portal pro další akce správy. |
| SCSI (Small Computer System Interface) |Sada standardů pro fyzické propojení počítačů a předávání dat mezi nimi. |
| SSD (Solid State Drive) |Disk, který neobsahuje žádné pohybující se části; například jednotka Flash. |
| účet úložiště |Sada přístupových přihlašovacích údajů propojených s vaším účtem úložiště pro daného poskytovatele cloudové služby. |
| StorSimple Adapter pro SharePoint |Microsoft Azure StorSimple komponenta, která transparentně rozšiřuje úložiště StorSimple a ochranu dat na serverové farmy služby SharePoint. |
| Služba Správce zařízení StorSimple |Rozšíření Azure Portal, které umožňuje správu místních a virtuálních zařízení Azure StorSimple. |
| StorSimple Snapshot Manager |Modul snap-in konzoly Microsoft Management Console (MMC) pro správu operací zálohování a obnovení v Microsoft Azure StorSimple. |
| provést zálohování |Funkce, která umožňuje uživateli provést interaktivní zálohování svazku. Je to alternativní způsob ručního zálohování svazku na rozdíl od automatického zálohování pomocí definovaných zásad. |
| dynamické zajišťování |Metoda optimalizace efektivity, se kterou se k dostupnému prostoru úložiště používá v systémech úložišť. Při dynamickém zajišťování je úložiště přiděleno mezi více uživatelů na základě minimálního prostoru požadovaného jednotlivými uživateli v daném okamžiku. Viz také *zřizování systému souborů FAT*. |
| vrstvení |Uspořádání dat v logických seskupeních na základě aktuálního využití, stáří a vztahu k dalším datům. StorSimple automaticky uspořádá data na úrovních. |
| svazků |Oblasti logického úložiště prezentované ve formě jednotek. StorSimple svazky odpovídají svazkům připojeným k hostiteli, včetně svazků zjištěných pomocí iSCSI a zařízení StorSimple. |
| kontejner svazků |Seskupení svazků a nastavení, která se na ně vztahují. Všechny svazky v zařízení StorSimple jsou seskupené do kontejnerů svazků. Nastavení kontejneru svazků zahrnuje účty úložiště, nastavení šifrování pro data odesílaná do cloudu s přidruženými šifrovacími klíči a šířku pásma spotřebovaná pro operace zahrnující Cloud. |
| skupina svazků |V StorSimple Snapshot Manager je skupina svazků kolekcí svazků nakonfigurovaných pro usnadnění zpracování zálohování. |
| Služba Stínová kopie svazku (VSS) |Služba operačního systému Windows Server, která usnadňuje konzistenci aplikací tím, že komunikuje s aplikacemi podporujícími STÍNovou kopii, aby koordinovala vytváření přírůstkových snímků. Služba Stínová kopie svazku zajišťuje, aby byly aplikace dočasně neaktivní při pořízení snímků. |
| Windows PowerShell pro StorSimple |Rozhraní příkazového řádku založené na Windows PowerShellu, které slouží k provozu a správě zařízení StorSimple. Při zachování některých základních funkcí prostředí Windows PowerShell má toto rozhraní další vyhrazené rutiny, které se přiřadí ke správě zařízení StorSimple. |

## <a name="next-steps"></a>Další kroky
Přečtěte si o [zabezpečení StorSimple](storsimple-8000-security.md).