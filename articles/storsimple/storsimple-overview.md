---
title: Přehled řešení řady StorSimple 8000 | Dokumenty společnosti Microsoft
description: Popisuje StorSimple vrstvení, zařízení, virtuální zařízení, služby a správu úložiště a zavádí klíčové termíny používané v StorSimple.
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
ms.date: 07/10/2017
ms.author: timlt
ms.openlocfilehash: 2a6650cac975c575415a329361da00d4fbfcaa9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965122"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>Řada StorSimple 8000: řešení hybridního cloudového úložiště

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Přehled
Vítá vás Microsoft Azure StorSimple, integrované úložné řešení, které spravuje úlohy úložiště mezi místními zařízeními a cloudovým úložištěm Microsoft Azure. StorSimple je efektivní, nákladově efektivní a snadno spravovatelné řešení sítě SAN (Storage Area Network), které eliminuje mnoho problémů a výdajů spojených s podnikovým úložištěm a ochranou dat. Využívá vlastní zařízení řady StorSimple 8000, integruje se s cloudovými službami a poskytuje sadu nástrojů pro správu umožňující bezproblémové zobrazení veškerého podnikového úložiště, včetně cloudového úložiště. (Informace o nasazení StorSimple zveřejněné na webu Microsoft Azure se vztahují pouze na zařízení řady StorSimple 8000. Pokud používáte zařízení řady StorSimple 5000/7000, přejděte na [nápovědu StorSimple](http://onlinehelp.storsimple.com/).)

StorSimple používá [vrstvení úložiště](#automatic-storage-tiering) ke správě uložených dat na různých paměťových médiích. Aktuální pracovní sada je uložena místně na ssd discích (SSD), data, která se používají méně často, jsou uložena na pevných discích (HDD) a archivní data jsou zasunuta do cloudu. Kromě toho StorSimple používá odstranění duplicit a komprese ke snížení množství úložiště, které data spotřebovává. Další informace naleznete v části [Odstranění duplicit a komprese](#deduplication-and-compression). Definice jiných klíčových pojmů a konceptů použitých v dokumentaci řady StorSimple 8000 naleznete na konci tohoto článku na [webu StorSimple terminologie.](#storsimple-terminology)

Kromě správy úložiště umožňují funkce ochrany dat StorSimple vytvářet zálohy na vyžádání a naplánovat je a pak je ukládat místně nebo v cloudu. Zálohy jsou prováděny ve formě přírůstkových snímků, což znamená, že je lze rychle vytvořit a obnovit. Cloudové snímky mohou být kriticky důležité ve scénářích zotavení po havárii, protože nahrazují systémy sekundárního úložiště (například zálohování na pásku) a umožňují v případě potřeby obnovit data do datového centra nebo do alternativních lokalit.

![ikona videa](./media/storsimple-overview/video_icon.png) Podívejte se na video, kde nazvete rychlý úvod do Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Proč použít StorSimple?
Následující tabulka popisuje některé klíčové výhody, které poskytuje Microsoft Azure StorSimple.

| Funkce | Výhoda |
| --- | --- |
| Transparentní integrace |Používá protokol iSCSI k neviditelnému propojení zařízení pro ukládání dat. Tím je zajištěno, že data uložená v cloudu, v datovém centru nebo na vzdálených serverech se zdá být uložena na jednom místě. |
| Nižší náklady na úložiště |Přiděluje dostatečné místní nebo cloudové úložiště pro splnění aktuálních požadavků a rozšiřuje cloudové úložiště pouze v případě potřeby. Dále snižuje požadavky na úložiště a náklady tím, že eliminuje redundantní verze stejných dat (deduplikace) a pomocí komprese. |
| Zjednodušená správa úložiště |Poskytuje nástroje pro správu systému pro konfiguraci a správu dat uložených místně, na vzdáleném serveru a v cloudu. Kromě toho můžete spravovat funkce zálohování a obnovení z modulu snap-in Konzoly MMC (MMC).|
| Lepší zotavení po havárii a dodržování předpisů |Nevyžaduje prodlouženou dobu obnovení. Místo toho obnoví data podle potřeby. To znamená, že normální operace mohou pokračovat s minimálním narušením. Kromě toho můžete nakonfigurovat zásady pro určení plánů zálohování a uchovávání dat. |
| Mobilita dat |K datům nahraná do cloudových služeb Microsoft Azure se dá přistupovat z jiných webů pro účely obnovení a migrace. Kromě toho můžete použít StorSimple ke konfiguraci StorSimple cloudových zařízení na virtuálních počítačích (VMs) spuštěných v Microsoft Azure. Virtuální počítače pak můžete použít virtuální zařízení pro přístup k uloženým datům pro účely testování nebo obnovení. |
| Kontinuita podnikových procesů |Umožňuje uživatelům řady StorSimple 5000-7000 migrovat svá data do zařízení řady StorSimple 8000. |
| Dostupnost na portálu Azure Government Portal |StorSimple je k dispozici na portálu Azure Government Portal. Další informace najdete [v tématu Nasazení místního zařízení StorSimple na portálu pro správu](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Ochrana a dostupnost údajů |Řada StorSimple 8000 podporuje zónově redundantní úložiště (ZRS), kromě místně redundantního úložiště (LRS) a geograficky redundantního úložiště (GRS). Podrobnosti o ZRS najdete v [tomto článku o možnostech redundance úložiště Azure.](https://azure.microsoft.com/documentation/articles/storage-redundancy/) |
| Podpora kritických aplikací |StorSimple umožňuje určit příslušné svazky jako místně připnuté, což zase zajišťuje, že data vyžadovaná kritickými aplikacemi nejsou vrstvena do cloudu. Místně vázaných svazků nepodléhají cloudové latence nebo problémy s připojením. Další informace o místně vázaných svazcích naleznete [v tématu Správa svazků pomocí služby StorSimple Device Manager](storsimple-8000-manage-volumes-u2.md). |
| Nízká latence a vysoký výkon |Můžete vytvářet cloudová zařízení, která využívají funkce úložiště Azure Premium s vysokým výkonem a nízkou latencí. Další informace o prémiových cloudových zařízeních StorSimple najdete v [tématu Nasazení a správa storsimple cloudového zařízení v Azure](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>Komponenty StorSimple
Řešení Microsoft Azure StorSimple obsahuje následující součásti:

* **Zařízení Microsoft Azure StorSimple** – místní hybridní pole úložiště, které obsahuje disky SSD a pevné disky spolu s redundantními řadiči a automatickými možnostmi převzetí služeb při selhání. Řadiče spravují vrstvení úložiště, umísťují aktuálně používaná (nebo horká) data do místního úložiště (v zařízení nebo na místních serverech) a přesouvají méně často používaná data do cloudu.
* **StorSimple Cloud Appliance** – také známý jako StorSimple Virtual Appliance, jedná se o softwarovou verzi zařízení StorSimple, která replikuje architekturu a většinu možností fyzického hybridního paměťového zařízení. StorSimple Cloud Appliance běží na jednom uzlu ve virtuálním počítači Azure. Prémiová virtuální zařízení, která využívají výhod úložiště Azure Premium, jsou dostupná v aktualizaci 2 a novější.
* **Služba StorSimple Device Manager** – rozšíření portálu Azure, které umožňuje spravovat zařízení StorSimple nebo StorSimple Cloud Appliance z jediného webového rozhraní. Pomocí služby StorSimple Device Manager můžete vytvářet a spravovat služby, zobrazovat a spravovat zařízení, zobrazovat výstrahy, spravovat svazky a zobrazovat a spravovat zásady zálohování a katalog zálohování.
* **Prostředí Windows PowerShell for StorSimple** – rozhraní příkazového řádku, které můžete použít ke správě zařízení StorSimple. Prostředí Windows PowerShell for StorSimple obsahuje funkce, které umožňují zaregistrovat zařízení StorSimple, konfigurovat síťové rozhraní v zařízení, instalovat určité typy aktualizací, řešit potíže se zařízením přístupem k relaci podpory a měnit zařízení. Státu. K prostředí Windows PowerShell pro službu StorSimple můžete přistupovat připojením ke sériové konzoli nebo pomocí vzdálené komunikace prostředí Windows PowerShell.
* **Rutiny Azure PowerShell StorSimple** – kolekce rutin prostředí Windows PowerShell, které umožňují automatizovat úlohy na úrovni služeb a migrace z příkazového řádku. Další informace o rutinách prostředí Azure PowerShell pro StorSimple najdete v odkazu na [rutinu](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0#azure).
* **Správce snímků StorSimple** – modul snap-in konzoly MMC, který používá skupiny svazků a službu Stínové kopie svazků systému Windows ke generování záloh konzistentních s aplikací. Kromě toho můžete použít Správce snímků StorSimple k vytvoření plánů zálohování a klonování nebo obnovení svazků.
* **StorSimple Adapter for SharePoint** – nástroj, který transparentně rozšiřuje úložiště Microsoft Azure StorSimple úložiště a ochranu dat na farmy SharePoint Serveru a zároveň umožňuje, aby bylo úložiště StorSimple viditelné a spravovatelné z portálu SharePoint Central Administration.

Následující diagram poskytuje zobrazení architektury a komponent Microsoft Azure StorSimple na vysoké úrovni.

![Architektura StorSimple](./media/storsimple-overview/overview-big-picture.png)

Následující části popisují každou z těchto součástí podrobněji a vysvětlují, jak řešení uspořádá data, přiděluje úložiště a usnadňuje správu úložiště a ochranu dat. Poslední část obsahuje definice pro některé důležité termíny a koncepty týkající se komponent StorSimple a jejich řízení.

## <a name="storsimple-device"></a>StorJednoduché zařízení
Zařízení Microsoft Azure StorSimple je místní hybridní pole úložiště, které poskytuje primární úložiště a přístup iSCSI k datům uloženým v něm. Spravuje komunikaci s cloudovým úložištěm a pomáhá zajistit zabezpečení a důvěrnost všech dat uložených v řešení Microsoft Azure StorSimple.

Zařízení StorSimple obsahuje disky SSD a pevné disky pevné disky, stejně jako podporu clusteringu a automatického převzetí služeb při selhání. Obsahuje sdílený procesor, sdílené úložiště a dva zrcadlené řadiče. Každý řadič poskytuje následující:

* Připojení k hostitelskému počítači
* Až šest síťových portů pro připojení k místní síti (LAN)
* Monitorování hardwaru
* Nevolatilní paměť nvram (random access), která uchovává informace i v případě přerušení napájení
* Aktualizace podporující cluster pro správu aktualizací softwaru na serverech s podporou převzetí služeb při selhání, takže aktualizace mají minimální nebo žádný vliv na dostupnost služby
* Clusterová služba, která funguje jako back-end cluster, poskytuje vysokou dostupnost a minimalizuje všechny nežádoucí účinky, které mohou nastat v případě selhání nebo offline disku HDD nebo SSD

V libovolném okamžiku je aktivní pouze jeden řadič. Pokud aktivní řadič selže, druhý řadič se automaticky aktivuje.

Další informace naleznete v části [Hardwarové součásti A Stav StorSimple](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>Cloudové zařízení StorSimple
StorSimple můžete použít k vytvoření cloudového zařízení, které replikuje architekturu a možnosti fyzického hybridního paměťového zařízení. StorSimple Cloud Appliance (označované také jako StorSimple Virtual Appliance) běží na jednom uzlu ve virtuálním počítači Azure. (Cloudové zařízení lze vytvořit jenom na virtuálním počítači Azure. Nelze vytvořit na zařízení StorSimple nebo místní server.)

Cloudové zařízení má následující funkce:

* Chová se jako fyzické zařízení a může nabídnout rozhraní iSCSI virtuálním počítačům v cloudu.
* Můžete vytvořit neomezený počet cloudových zařízení v cloudu a podle potřeby je zapnout a vypnout.
* Může pomoci simulovat místní prostředí ve scénářích zotavení po havárii, vývoji a testování a může pomoci s načítáním na úrovni položek ze záloh.

StorSimple Cloud Appliance je k dispozici ve dvou modelech: zařízení 8010 (dříve známé jako model 1100) a zařízení 8020. Zařízení 8010 má maximální kapacitu 30 TB. Zařízení 8020, které využívá úložiště Azure Premium, má maximální kapacitu 64 TB. (V místních vrstvách úložiště Azure premium ukládá data na ssd disky, zatímco standardní úložiště ukládá data na pevné disky.) Všimněte si, že musíte mít účet úložiště Azure premium, abyste mohli používat úložiště premium.

Další informace o storsimple cloudovém zařízení najdete v části [Nasazení a správa storsimple cloudového zařízení v Azure](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple
Microsoft Azure StorSimple poskytuje webové uživatelské rozhraní (služba StorSimple Device Manager), které umožňuje centrální správu datového centra a cloudového úložiště. Pomocí služby StorSimple Device Manager můžete provádět následující úkoly:

* Konfigurace nastavení systému pro zařízení StorSimple.
* Konfigurace a správa nastavení zabezpečení pro zařízení StorSimple.
* Konfigurace cloudových přihlašovacích údajů a vlastností.
* Konfigurace a správa svazků na serveru.
* Konfigurace skupin svazků.
* Zálohovat a obnovovat data.
* Sledujte výkon.
* Zkontrolujte nastavení systému a identifikujte možné problémy.

Pomocí služby StorSimple Device Manager můžete provádět všechny úlohy správy s výjimkou těch, které vyžadují prostoje systému, jako je například počáteční nastavení a instalace aktualizací.

Další informace naleznete v [části Správa zařízení StorSimple pomocí služby StorSimple Device Manager](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Prostředí Windows PowerShell pro StorSimple
Prostředí Windows PowerShell for StorSimple poskytuje rozhraní příkazového řádku, které můžete použít k vytvoření a správě služby Microsoft Azure StorSimple a nastavení a monitorování zařízení StorSimple. Jedná se o rozhraní příkazového řádku založené na prostředí Windows PowerShell, které obsahuje vyhrazené rutiny pro správu zařízení StorSimple. Prostředí Windows PowerShell for StorSimple obsahuje funkce, které umožňují:

* Zaregistrujte zařízení.
* Nakonfigurujte síťové rozhraní na zařízení.
* Nainstalujte určité typy aktualizací.
* Poradce při potížích se zařízením přistupuje k relaci podpory.
* Změňte stav zařízení.

K prostředí Windows PowerShell pro StorSimple můžete přistupovat ze sériové konzole (v hostitelském počítači připojeném přímo k zařízení) nebo vzdáleně pomocí vzdálené komunikace prostředí Windows PowerShell. Všimněte si, že některé Windows PowerShell pro StorSimple úkoly, jako je například počáteční registrace zařízení, lze provést pouze na konzole sériového portu.

Další informace naleznete v [části Správa zařízení pomocí prostředí Windows PowerShell pro službu StorSimple](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Rutiny Azure PowerShell StorSimple
Rutiny Azure PowerShell StorSimple jsou kolekce rutin prostředí Windows PowerShell, které umožňují automatizovat úlohy na úrovni služeb a migrace z příkazového řádku. Další informace o rutinách prostředí Azure PowerShell pro StorSimple najdete v odkazu na [rutinu](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0).

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
StorSimple Snapshot Manager je modul snap-in konzoly MMC (MMC), který můžete použít k vytvoření konzistentních záložních kopií místních a cloudových dat v okamžiku. Modul snap-in je spuštěn na hostiteli založeném na systému Windows Server. Správce snímků StorSimple můžete použít k:

* Konfigurace, zálohování a odstranění svazků.
* Nakonfigurujte skupiny svazků, abyste zajistili, že zálohovaná data budou konzistentní s aplikací.
* Spravujte zásady zálohování tak, aby byla data zálohována podle předem stanoveného plánu a uložena v určeném umístění (místně nebo v cloudu).
* Obnovte svazky a jednotlivé soubory.

Zálohy jsou zachyceny jako snímky, které zaznamenávají pouze změny od posledního snímku byla pořízena a vyžadují mnohem méně místa než úplné zálohy. Podle potřeby můžete vytvořit plány zálohování nebo provést okamžité zálohování. Kromě toho můžete použít StorSimple Snapshot Manager k vytvoření zásad uchovávání informací, které řídí, kolik snímků budou uloženy. Pokud následně potřebujete obnovit data ze zálohy, StorSimple Snapshot Manager umožňuje vybrat z katalogu místních nebo cloudových snímků. 

Pokud dojde k havárii nebo pokud potřebujete obnovit data z jiného důvodu, StorSimple Snapshot Manager obnoví postupně, jak je potřeba. Obnovení dat nevyžaduje vypnutí celého systému při obnovení souboru, nahrazení zařízení nebo přesunutí operací do jiné lokality.

Další informace naleznete v najdete [ve Správci snímků StorSimple?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>StorSimple Adapter pro SharePoint
Microsoft Azure StorSimple zahrnuje StorSimple adaptér pro SharePoint, volitelnou komponentu, která transparentně rozšiřuje funkce úložiště StorSimple a ochrany dat na farmy serverů SharePoint. Adaptér pracuje s poskytovatelem vzdáleného úložiště objektů blob (RBS) a funkcí SQL Server RBS, což umožňuje přesunout objekty BLOB na server zálohovaný systémem Microsoft Azure StorSimple. Microsoft Azure StorSimple pak ukládá data BLOB místně nebo v cloudu, na základě využití.

StorSimple Adaptér pro SharePoint se spravuje z portálu SharePoint Central Administration. V důsledku toho zůstává správa služby SharePoint centralizovaná a zdá se, že veškeré úložiště je ve farmě služby SharePoint.

Další informace naleznete v částce [StorSimple Adapter for SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Technologie správy úložišť
Kromě vyhrazeného zařízení StorSimple, virtuálního zařízení a dalších komponent používá Microsoft Azure StorSimple následující softwarové technologie k zajištění rychlého přístupu k datům a ke snížení spotřeby úložiště:

* [Automatické vrstvení úložiště](#automatic-storage-tiering) 
* [Dynamické zajišťování](#thin-provisioning) 
* [Deduplikace a komprese](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Automatické vrstvení úložiště
Microsoft Azure StorSimple automaticky uspořádá data v logických vrstvách na základě aktuálního využití, věku a vztahu k jiným datům. Data, která jsou nejaktivnější, se ukládají místně, zatímco méně aktivní a neaktivní data se automaticky migrují do cloudu. Následující diagram znázorňuje tento přístup úložiště.

![Vrstvy úložiště StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Chcete-li povolit rychlý přístup, StorSimple ukládá velmi aktivní data (horká data) na SSD v zařízení StorSimple. Ukládá data, která se příležitostně používají (teplá data) na pevných discích v zařízení nebo na serverech v datovém centru. Přesouvá neaktivní data, záložní data a data uchovávaná pro účely archivace nebo dodržování předpisů do cloudu. 

> [!NOTE]
> V aktualizaci 2 nebo novější můžete zadat svazek jako místně připnutý, v takovém případě data zůstanou na místním zařízení a není vrstvené do cloudu. 


StorSimple upravuje a mění uspořádání dat a přiřazení úložiště při změně vzorců použití. Některé informace mohou být například v průběhu času méně aktivní. Jak se stává postupně méně aktivní, je migrována z SSD na pevné disky a pak do cloudu. Pokud se stejná data znovu stanou aktivními, budou přenesena zpět do úložného zařízení.

Proces vrstvení úložiště probíhá následujícím způsobem:

1. Správce systému nastaví účet cloudového úložiště Microsoft Azure.
2. Správce používá sériovou konzolu a službu StorSimple Device Manager (spuštěnou na portálu Azure) ke konfiguraci zařízení a souborového serveru, vytváření svazků a zásad ochrany dat. Místní počítače (například souborové servery) používají k přístupu k zařízení StorSimple rozhraní ISCSI (Internet Small Computer System Interface).
3. Zpočátku StorSimple ukládá data na rychlé vrstvě SSD zařízení.
4. Jako vrstva SSD se blíží kapacitě, StorSimple deduplikuje a komprimuje nejstarší datové bloky a přesune je do vrstvy HDD.
5. Jak se vrstva HDD blíží kapacitě, StorSimple šifruje nejstarší datové bloky a bezpečně je odesílá na účet úložiště Microsoft Azure prostřednictvím protokolu HTTPS.
6. Microsoft Azure vytvoří několik replik dat ve svém datovém centru a ve vzdáleném datovém centru, což zajistí, že data lze obnovit, pokud dojde k havárii.
7. Když souborový server požaduje data uložená v cloudu, StorSimple vrátí bez problémů a uloží kopii na vrstvu SSD zařízení StorSimple.

#### <a name="how-storsimple-manages-cloud-data"></a>Jak StorSimple spravuje cloudová data

StorSimple deduplikuje zákaznická data napříč všemi snímky a primárními daty (data zapsaná hostiteli). Zatímco deduplikace je skvělá pro efektivitu úložiště, komplikuje otázku "co je v cloudu". Vrstvené primární data a snímek data překrývají mezi sebou. Jeden blok dat v cloudu by mohl být použit jako vrstvená primární data a také odkazováno několika snímky. Každý snímek cloudu zajišťuje, že kopie všech dat v okamžiku v čase je uzamčena do cloudu, dokud se tento snímek neodstraní.

Data se odstraní z cloudu pouze v případě, že na tato data neexistují žádné odkazy. Například pokud vezmeme snímek cloudu všech dat, která je v zařízení StorSimple a potom odstraníme některá primární data, okamžitě se zobrazí primární pokles _dat._ _Cloudová data,_ která obsahují vrstvená data a zálohy, zůstávají stejná. Důvodem je, že je snímek stále odkazující na data cloudu. Po odstranění snímku cloudu (a jakýkoli jiný snímek, který odkazoval na stejná data), klesne spotřeba cloudu. Než odebereme cloudová data, zkontrolujeme, zda na tato data stále neodkazují žádné snímky. Tento proces _se_ nazývá uvolňování paměti a je služba na pozadí spuštěna na zařízení. Odebrání cloudových dat není okamžité, protože služba uvolňování paměti kontroluje další odkazy na tato data před odstraněním. Rychlost uvolňování paměti závisí na celkovém počtu snímků a celkový počet dat. Cloudová data se obvykle vyčistí za méně než týden.


### <a name="thin-provisioning"></a>Dynamické zajišťování
Tenké zřizování je virtualizační technologie, ve které se zdá, že dostupné úložiště překračuje fyzické prostředky. Místo vyhrazení dostatečné úložiště předem, StorSimple používá tenké zřizování přidělit dostatek místa pro splnění aktuálních požadavků. Elastická povaha cloudového úložiště usnadňuje tento přístup, protože StorSimple může zvýšit nebo snížit cloudové úložiště tak, aby splňovalo měnící se požadavky.

> [!NOTE]
> Místně vázaných svazků nejsou tence zřízená. Úložiště přidělené na místní svazek je zřízena v plném rozsahu při vytvoření svazku.


### <a name="deduplication-and-compression"></a>Deduplikace a komprese
Microsoft Azure StorSimple používá odstranění duplicit a komprese dat k dalšímu snížení požadavků na úložiště.

Odstraněním redundance v uložené datové sadě snižuje odstraněním redundance v uložené datové sadě odstraněním duplicitní funkce. Jako změny informací StorSimple ignoruje nezměněná data a zachycuje pouze změny. Kromě toho StorSimple snižuje množství uložených dat identifikací a odebráním nepotřebných informací. 

> [!NOTE]
> Data na místně vázaných svazcích nejsou deduplikována ani komprimována. Zálohy místně vázaných svazků jsou však deduplikovány a komprimovány.


## <a name="storsimple-workload-summary"></a>Souhrn úlohy StorSimple
Souhrn podporovaných úloh StorSimple je uveden v tabulce níže.

| Scénář | Úloha | Podporuje se | Omezení | Version |
| --- | --- | --- | --- | --- |
| Spolupráce |Sdílení souborů |Ano | |Všechny verze |
| Spolupráce |Distribuované sdílení souborů |Ano | |Všechny verze |
| Spolupráce |SharePoint |Ano* |Podporováno pouze u místně vázaných svazků |Aktualizace 2 a novější |
| Archivace |Jednoduchá archivace souborů |Ano | |Všechny verze |
| Virtualizace |Virtual Machines |Ano* |Podporováno pouze u místně vázaných svazků |Aktualizace 2 a novější |
| Databáze |SQL |Ano* |Podporováno pouze u místně vázaných svazků |Aktualizace 2 a novější |
| Střežení kamerami |Střežení kamerami |Ano* |Podporováno, když je zařízení StorSimple vyhrazeno pouze pro tuto úlohu |Aktualizace 2 a novější |
| Zálohování |Primární cílová záloha |Ano* |Podporováno, když je zařízení StorSimple vyhrazeno pouze pro tuto úlohu |Aktualizace 3 a novější |
| Zálohování |Sekundární cílová záloha |Ano* |Podporováno, když je zařízení StorSimple vyhrazeno pouze pro tuto úlohu |Aktualizace 3 a novější |

*Ano&#42; – měly by být použity pokyny a omezení řešení.*

Následující úlohy nejsou podporovány zařízeními řady StorSimple 8000. Pokud jsou nasazeny na StorSimple, tyto úlohy bude mít za následek nepodporovanou konfiguraci.

* Lékařské zobrazování
* Výměna
* Vdi
* Oracle
* SAP
* Velký objem dat
* Distribuce obsahu
* Spuštění z SCSI

Následuje seznam součástí podporované infrastruktury StorSimple.

| Scénář | Úloha | Podporuje se | Omezení | Version |
| --- | --- | --- | --- | --- |
| Obecné |ExpressRoute |Ano | |Všechny verze |
| Obecné |Datový jádro FC |Ano* |Podporováno s DataCore SANsymphony |Všechny verze |
| Obecné |DFSR |Ano* |Podporováno pouze u místně vázaných svazků |Všechny verze |
| Obecné |Indexování |Ano* |Pro vrstvené svazky je podporováno pouze indexování metadat (žádná data).<br>Pro místně vázaných svazků je podporováno úplné indexování. |Všechny verze |
| Obecné |Antivirus |Ano* |U vrstvených svazků je podporováno pouze skenování při otevření a zavření.<br> U místně vázaných svazků je podporováno úplné prohledávaní. |Všechny verze |

*Ano&#42; – měly by být použity pokyny a omezení řešení.*

Následuje seznam dalších software, které se používají s StorSimple k vytváření řešení.

| Typ pracovního vytížení | Software používaný s StorSimple | Podporované verze|Odkaz na průvodce řešením| 
| --- | --- | --- | --- |
| Cíl zálohování |Veeam |Veeam v 9 a novější |[StorSimple jako záložní cíl s Veaam](storsimple-configure-backup-target-veeam.md)|
| Cíl zálohování |Veritas Backup Exec |Zálohování Exec 16 a novější |[StorSimple jako cíl zálohování s programem Backup Exec](storsimple-configure-backup-target-using-backup-exec.md)|
| Cíl zálohování |Veritas NetBackup |NetBackup 7.7.x a novější  |[StorSimple jako cíl zálohování s NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Globální sdílení souborů <br></br> Spolupráce |Talon  |[StorSimple s Talonem](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>StorJednoduchá terminologie
Před nasazením řešení Microsoft Azure StorSimple doporučujeme zkontrolovat následující termíny a definice.

### <a name="key-terms-and-definitions"></a>Klíčové pojmy a definice
| Termín (zkratka nebo zkratka) | Popis |
| --- | --- |
| záznam řízení přístupu (ACR) |Záznam přidružený ke svazku na vašem zařízení Microsoft Azure StorSimple, který určuje, kteří hostitelé se k němu mohou připojit. Stanovení je založeno na kvalifikovaném názvu iSCSI (IQN) hostitelů (obsažených v ACR), kteří se připojují k vašemu zařízení StorSimple. |
| AES-256 |256bitový algoritmus Advanced Encryption Standard (AES) pro šifrování dat při přesunu do cloudu a z něj. |
| velikost alokační jednotky (AUS) |Nejmenší množství místa na disku, které lze přidělit pro uložení souboru v souborových systémech Windows. Pokud velikost souboru není sudý násobek velikosti clusteru, je nutné použít další místo k uložení souboru (až do dalšího násobku velikosti clusteru), což má za následek ztrátu místa a fragmentaci pevného disku. <br>Doporučené AUS pro Azure StorSimple svazky je 64 KB, protože funguje dobře s algoritmy odstranění duplicit. |
| automatizované vrstvení úložiště |Automatické přesouvání méně aktivních dat z disků SSD na pevné disky a potom na úroveň v cloudu a následné povolení správy veškerého úložiště z centrálního uživatelského rozhraní. |
| katalog záloh |Kolekce záloh, obvykle související typu aplikace, která byla použita. Tato kolekce se zobrazí v okně katalogu zálohování uživatelského rozhraní služby StorSimple Device Manager. |
| záložní soubor katalogu |Soubor obsahující seznam dostupných snímků, které jsou aktuálně uloženy v záložní databázi Správce snímků StorSimple. |
| zásady zálohování |Výběr svazků, typ zálohování a časový plán, který umožňuje vytvářet zálohy podle předdefinovaného plánu. |
| binární velké objekty (objekty BLOB) |Kolekce binárních dat uložených jako jedna entita v systému správy databáze. Objekty BLOB jsou obvykle obrázky, zvuk nebo jiné multimediální objekty, i když někdy binární spustitelný kód je uložen jako objekt BLOB. |
| Protokol CHAP (Challenge Handshake Authentication Protocol) |Protokol používaný k ověření partnera připojení na základě sdílení hesla nebo tajného klíče partnera. Chap může být jednosměrný nebo vzájemný. S jednosměrný CHAP cíl ověří iniciátor. Vzájemné CHAP vyžaduje, aby cíl ověřit iniciátora a že iniciátor ověří cíl. |
| clone |Duplicitní kopie svazku. |
| Cloud jako úroveň (CaaT) |Cloudové úložiště integrované jako vrstva v rámci architektury úložiště, takže všechna úložiště se zdá být součástí jedné podnikové sítě úložiště. |
| poskytovatel cloudových služeb (CSP) |Poskytovatel služeb cloud computingu. |
| snímek cloudu |Kopie dat svazku v čase, která je uložena v cloudu. Snímek cloudu je ekvivalentní snímku replikovaného v jiném systému úložiště mimo pracoviště. Cloudové snímky jsou užitečné zejména ve scénářích zotavení po havárii. |
| šifrovací klíč cloudového úložiště |Heslo nebo klíč používaný zařízením StorSimple pro přístup k šifrovaným datům odeslaných zařízením do cloudu. |
| aktualizace podporující cluster |Správa aktualizací softwaru na serverech v clusteru s podporou převzetí služeb při selhání tak, aby aktualizace měly minimální nebo žádný vliv na dostupnost služby. |
| datová cesta |Kolekce funkčních jednotek, které provádějí operace zpracování propojených dat. |
| deaktivovat |Trvalá akce, která přeruší připojení mezi zařízením StorSimple a přidruženou cloudovou službou. Cloudové snímky zařízení zůstávají po tomto procesu a lze je klonovat nebo použít pro zotavení po havárii. |
| zrcadlení disku |Replikace svazků logických disků na samostatných pevných discích v reálném čase pro zajištění nepřetržité dostupnosti. |
| dynamické zrcadlení disku |Replikace svazků logických disků na dynamických discích. |
| dynamické disky |Formát svazku disku, který používá Správce logických disků (LDM) k ukládání a správě dat na více fyzických discích. Dynamické disky lze zvětšit, aby poskytovaly více volného místa. |
| Rozšířená skříň s hromadou disků (EBOD) |Sekundární skříň vašeho zařízení Microsoft Azure StorSimple, která obsahuje další pevné disky pro další úložiště. |
| zřizování tuků |Konvenční zřizování úložiště, ve kterém je přidělen úložný prostor na základě očekávaných potřeb (a je obvykle nad rámec aktuální potřeby). Viz také *tenké zřizování*. |
| pevný disk (HDD) |Jednotka, která používá rotující talíře k ukládání dat. |
| hybridní cloudové úložiště |Architektura úložiště, která používá místní prostředky a prostředky mimo lokalitu, včetně cloudového úložiště. |
| Rozhraní systému malého počítače (iSCSI) |Standard sítě úložišť založený na protokolu IP pro propojení zařízení nebo zařízení pro ukládání dat. |
| Iniciátor iSCSI |Softwarová součást, která umožňuje hostitelskému počítači se systémem Windows připojit se k externí úložné síti založené na systému iSCSI. |
| Kvalifikovaný název iSCSI (IQN) |Jedinečný název, který identifikuje cíl nebo iniciátor iniciátori iSCSI. |
| Cíl iSCSI |Softwarová součást, která poskytuje centralizované diskové subsystémy iSCSI v sítích s plochou úložiště. |
| živá archivace |Přístup úložiště, ve kterém jsou archivní data přístupná po celou dobu (není uložena například mimo pracoviště na pásce). Microsoft Azure StorSimple používá živou archivaci. |
| místně vázaný svazek |svazek, který je umístěn v zařízení a nikdy není vrstvený do cloudu. |
| místní snímek |Kopie dat svazku v čase, která je uložena na zařízení Microsoft Azure StorSimple. |
| Microsoft Azure StorSimple |Výkonné řešení skládající se z úložiště datového centra a softwaru, který umožňuje IT organizacím využívat cloudové úložiště, jako by se jednalo o úložiště datových center. StorSimple zjednodušuje ochranu dat a správu dat a zároveň snižuje náklady. Řešení konsoliduje primární úložiště, archiv, zálohování a zotavení po havárii (DR) prostřednictvím bezproblémové integrace s cloudem. Díky kombinaci úložiště sítě SAN a správy cloudových dat na platformě podnikové třídy umožňují zařízení StorSimple rychlost, jednoduchost a spolehlivost pro všechny potřeby související s úložištěm. |
| Napájecí a chladicí modul (PCM) |Hardwarové komponenty zařízení StorSimple, které se skládají z napájecích zdrojů a chladicího ventilátoru, odtud název Modul napájení a chlazení. Primární skříň zařízení má dva 764W PCM, zatímco skříň EBOD má dva 580W PCM. |
| primární skříň |Hlavní skříň zařízení StorSimple, která obsahuje řadiče aplikační platformy. |
| cíl doby zotavení (RTO) |Maximální doba, která by měla být vynaložena před obchodní proces nebo systém je plně obnovena po havárii. |
| sériově připojené SCSI (SAS) |Typ pevného disku (HDD). |
| šifrovací klíč dat služby |Klíč zpřístupněný všem novým zařízením StorSimple, které se zaregistruje se službou StorSimple Device Manager. Konfigurační data přenášená mezi službou StorSimple Device Manager a zařízením jsou šifrována pomocí veřejného klíče a lze je dešifrovat pouze na zařízení pomocí soukromého klíče. Šifrovací klíč dat služby umožňuje službě získat tento soukromý klíč pro dešifrování. |
| registrační klíč služby |Klíč, který pomáhá zaregistrovat zařízení StorSimple se službou StorSimple Device Manager tak, aby se zobrazila na portálu Azure pro další akce správy. |
| SCSI (Small Computer System Interface) |Sada standardů pro fyzické připojení počítačů a předávání dat mezi nimi. |
| jednotka SSD (SSD) |Disk, který neobsahuje žádné pohyblivé části; například flash disk. |
| účet úložiště |Sada přístupových přihlašovacích údajů propojených s vaším účtem úložiště pro daného poskytovatele cloudových služeb. |
| StorSimple Adapter pro SharePoint |Součást Microsoft Azure StorSimple, která transparentně rozšiřuje úložiště StorSimple a ochranu dat na farmy serverů SharePoint. |
| Služba Správce zařízení StorSimple |Rozšíření portálu Azure, které vám umožní spravovat vaše místní a virtuální zařízení Azure StorSimple. |
| StorSimple Snapshot Manager |Modul snap-in konzoly MMC (MMC) pro správu operací zálohování a obnovení v Microsoft Azure StorSimple. |
| vzít zálohu |Funkce, která umožňuje uživateli provést interaktivní zálohu svazku. Jedná se o alternativní způsob, jak vzít ruční zálohování svazku na rozdíl od přijetí automatické zálohování prostřednictvím definované zásady. |
| tenké zřizování |Metoda optimalizace efektivity, s jakou se dostupný úložný prostor používá v úložných systémech. V tenké zřizování úložiště je přiděleno mezi více uživatelů na základě minimální místo vyžadované jednotlivými uživateli v daném okamžiku. Viz také *zřizování tuků*. |
| Tiering |Uspořádání dat v logických seskupeních na základě aktuálního využití, stáří a vztahu k jiným datům. StorSimple automaticky uspořádá data v úrovních. |
| Objem |Logické úložné prostory prezentované ve formě jednotek. Svazky StorSimple odpovídají svazkům připojeným hostitelem, včetně těch, které byly zjištěny pomocí iSCSI a zařízení StorSimple. |
| objemový kontejner |Seskupení svazků a nastavení, která se na ně vztahují. Všechny svazky v zařízení StorSimple jsou seskupeny do kontejnerů svazků. Nastavení kontejneru svazku zahrnuje účty úložiště, nastavení šifrování dat odeslaných do cloudu s přidruženými šifrovacími klíči a šířku pásma spotřebovanou pro operace zahrnující cloud. |
| skupina svazků |Ve Správci snímků StorSimple je skupina svazků kolekce svazků nakonfigurovaných pro usnadnění zpracování záloh. |
| Služba stínové kopie svazku (VSS) |Služba operačního systému Windows Server, která usnadňuje konzistenci aplikací komunikací s aplikacemi podporujícími službu VSS za účelem koordinace vytváření přírůstkových snímků. VSS zajišťuje, že aplikace jsou dočasně neaktivní při pořízení snímků. |
| Prostředí Windows PowerShell pro StorSimple |Rozhraní příkazového řádku založené na prostředí Windows PowerShell u používané k ovládání a správě zařízení StorSimple. Při zachování některé základní možnosti prostředí Windows PowerShell, toto rozhraní má další vyhrazené rutiny, které jsou zaměřeny na správu zařízení StorSimple. |

## <a name="next-steps"></a>Další kroky
Další informace o [zabezpečení StorSimple](storsimple-8000-security.md).

