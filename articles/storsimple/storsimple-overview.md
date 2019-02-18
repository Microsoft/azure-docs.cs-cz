---
title: Přehled řešení StorSimple 8000 series | Dokumentace Microsoftu
description: Popisuje ovládání datových vrstev na StorSimple, zařízení, virtuální zařízení, služby a správu úložiště a zavádí klíčových termínů používaných v StorSimple.
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
ms.author: v-sharos@microsoft.com
ms.openlocfilehash: 63906e65acb8e8aa836e6e59714bddca24ea21eb
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56326921"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>StorSimple 8000 series: řešení hybridního cloudového úložiště
## <a name="overview"></a>Přehled
Vítá vás Microsoft Azure StorSimple, integrované řešení úložiště, které spravuje úlohy úložiště mezi místními zařízeními a cloudovým úložištěm Microsoft Azure. StorSimple je efektivní, cenově efektivní a snadno spravovatelné úložiště sítě SAN řešení oblasti, které předchází problémům a náklady spojené s podnikové úložiště a ochranu dat. Využívá speciální zařízení řady StorSimple 8000, integruje s cloudovými službami a poskytuje sadu nástrojů pro správu pro umožňující bezproblémové zobrazení veškerého podnikového úložiště, včetně cloudového úložiště. (Informace o nasazení StorSimple publikované na webu Microsoft Azure platí pro pouze zařízení řady StorSimple 8000. Pokud používáte zařízení řady StorSimple 5000/7000, přejděte na [StorSimple nápovědy](http://onlinehelp.storsimple.com/).)

StorSimple využívá [vrstvení úložiště](#automatic-storage-tiering) spravovat data uložená na různých úložných médií. Aktuální pracovní sada je uložený místně na jednotky SSD (Solid-State Drive), data, která se používá méně často se ukládají na pevných disků (HDD) a archivních dat se vloží do cloudu. Kromě toho StorSimple používá odstranění duplicitních dat a provádí kompresi, snížit objem úložiště, který zpracovává data. Další informace najdete v části [Deduplikace a komprese](#deduplication-and-compression). Definice jiných klíčové pojmy a koncepty používané v dokumentaci k řady StorSimple 8000, přejděte na [StorSimple terminologie](#storsimple-terminology) na konci tohoto článku.

Funkce pro ochranu dat StorSimple kromě správy úložiště umožňují vytvářet na vyžádání a plánované zálohy a pak je uložíte místně nebo v cloudu. Zálohy jsou prováděny ve formě přírůstkových snímků, což znamená, že může být vytvořeno a rychle obnovit. Cloudové snímky může být kriticky důležité pro scénáře zotavení po havárii, protože nahradit sekundární úložných systémů (např. zálohování na pásku) a umožňují obnovit data do vašeho datového centra nebo do alternativní lokality v případě potřeby.

![Ikona videa](./media/storsimple-overview/video_icon.png) Ve videu najdete stručný úvod do Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Proč používat službu StorSimple?
Následující tabulka popisuje některé z klíčových výhod, které poskytuje Microsoft Azure StorSimple.

| Funkce | Výhoda |
| --- | --- |
| Transparentní integrace |Nezaregistroval propojit zařízení úložiště dat pomocí protokolu iSCSI. To zajišťuje, že data uložená v cloudu, v datacentru, nebo na vzdálených serverech se zdá být uložené na jednom místě. |
| Náklady na úložiště nižší |Přidělí dostatečnou místní nebo cloudové úložiště s cílem splnit aktuální požadavky a rozšiřuje cloudovém úložišti pouze v případě potřeby. Dále snižuje požadavky na úložiště a výdajů odstraněním redundantní verzích stejná data (odstranění duplicitních dat) a pomocí komprese. |
| Jednodušší správu úložišť |Poskytuje systém nástroje pro správu můžete konfigurovat a spravovat data uložená místně, na vzdáleném serveru a v cloudu. Kromě toho můžete spravovat zálohování a obnovení funkce z modulu snap-in konzoly Microsoft Management Console (MMC).|
| Zlepšení zotavení po havárii a dodržování předpisů |Nevyžaduje, aby čas rozšířené obnovení. Místo toho jej obnoví data dle potřeby. To znamená, že můžete pokračovat normální provoz s minimálním dopadem. Kromě toho můžete nakonfigurovat zásady k určení plány zálohování a uchovávání dat. |
| Mobilita dat |Data nahraná do cloudové služby Microsoft Azure je přístupný z jiných webů pro účely obnovení a migrace. Kromě toho můžete použít StorSimple ke konfiguraci řešení StorSimple Cloud Appliance na virtuálních počítačích (VM) v Microsoft Azure. Virtuální počítače pak můžete použít virtuální zařízení pro přístup k uložená data pro účely zkušební nebo obnovení. |
| Kontinuita podnikových procesů |Uživatelé StorSimple 5000 – 7000 series migrovat svoje data na zařízení řady StorSimple 8000. |
| Dostupnost na portálu Azure Government |StorSimple je dostupná na portálu Azure Government. Další informace najdete v tématu [nasazení místních zařízení StorSimple na portálu Government](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Ochrana dat a dostupnost |StorSimple 8000 series podporuje Zónově redundantní úložiště (ZRS), kromě místně redundantní úložiště (LRS) a geograficky redundantní úložiště (GRS). Odkazovat na [Tento článek o možnostech redundance služby Azure Storage](https://azure.microsoft.com/documentation/articles/storage-redundancy/) ZRS podrobnosti. |
| Podpora pro klíčové aplikace |StorSimple vám umožní identifikovat příslušné jako místně připojené svazky, které pak zajistí, že se data potřebné pro kritické aplikace Vrstvená do cloudu. Místně připojené svazky nejsou v souladu s latencí cloudu nebo problémy s připojením. Další informace o místně připojené svazky, naleznete v tématu [použití služby Správce zařízení StorSimple pro správu svazků](storsimple-8000-manage-volumes-u2.md). |
| Vysoký výkon a nízkou latencí |Můžete vytvořit Cloudová zařízení, které budou využívat vysoký výkon s nízkou latencí funkce Azure premium storage. Další informace o Cloudová zařízení StorSimple premium najdete v tématu [nasadit a spravovat řešení StorSimple Cloud Appliance v Azure](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>StorSimple komponenty
Řešení Microsoft Azure StorSimple zahrnuje následující součásti:

* **Microsoft Azure StorSimple zařízení** – místní hybridní pole úložiště, který obsahuje vlastnosti jednotek SSD a HDD, společně s redundantní řadiče a automatické převzetí služeb při selhání. Kontrolery spravovat úložiště ovládání datových vrstev, umístění aktuálně používá (nebo hot) dat v místním úložišti (v zařízení nebo na místní servery), při přesouvání méně často používaná data do cloudu.
* **Řešení StorSimple Cloud Appliance** – označované také jako virtuální zařízení StorSimple, toto je verze softwaru zařízení StorSimple, která se replikují na architekturu a většina možností fyzické hybridní zařízení úložiště. Řešení StorSimple Cloud Appliance běží na jednom uzlu ve virtuálním počítači Azure. Virtuální zařízení úrovně Premium, které můžete využít Azure premium storage, jsou k dispozici ve verzi Update 2 nebo novější.
* **Služba Správce zařízení StorSimple** – což je rozšíření na webu Azure portal, který vám umožní spravovat zařízení StorSimple nebo řešení StorSimple Cloud Appliance z jediného webového rozhraní. Služba Správce zařízení StorSimple můžete vytvořit a spravovat služby, zobrazit a spravovat zařízení, zobrazovat výstrahy, Správa svazků a zobrazit a spravovat zásady zálohování a katalog záloh.
* **Prostředí Windows PowerShell pro StorSimple** – rozhraní příkazového řádku, který můžete použít ke správě zařízení StorSimple. Prostředí Windows PowerShell pro StorSimple je funkce, které umožní zaregistrovat zařízení StorSimple, konfigurace síťového rozhraní na zařízení, nainstalovat určité typy aktualizací, potíží se zařízením pomocí přístupu k relaci podpory a změnit zařízení stav. Připojení ke konzole sériového portu nebo pomocí vzdálené komunikace Windows Powershellu, lze použít prostředí Windows PowerShell pro StorSimple.
* **Rutiny Azure Powershellu StorSimple** – kolekce rutiny Windows Powershellu, které umožňují automatizaci úrovně služby a úlohy migrace z příkazového řádku. Další informace o rutinách prostředí Azure PowerShell pro StorSimple, přejděte [Reference k rutinám](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0#azure).
* **StorSimple Snapshot Manageru** – modul snap-in konzoly MMC, který používá skupin svazků a služby Stínová kopie svazku Windows k vygenerování zálohy konzistentní s aplikací. Kromě toho můžete použít StorSimple Snapshot Manageru vytvořit plány zálohování a klonování nebo obnovte svazky.
* **StorSimple Adapter pro SharePoint** – nástroj, který rozšiřuje transparentně Microsoft Azure StorSimple úložiště a ochranu dat na SharePoint Server hospodářství při nastavování úložiště StorSimple může zobrazit a spravovat z centrální služby SharePoint Portál pro správu.

Následující obrázek poskytuje souhrnný přehled komponent a architektury Microsoft Azure StorSimple.

![Architektura StorSimple](./media/storsimple-overview/overview-big-picture.png)

Následující části popisují každou z těchto součástí podrobněji a popisují, jak toto řešení uspořádá data, přidělí úložiště a usnadňuje správu úložiště a ochranu dat. Poslední část obsahuje definice pro některé důležité terminologie a koncepty související s StorSimple komponenty a jejich správu.

## <a name="storsimple-device"></a>Zařízení StorSimple
Zařízení Microsoft Azure StorSimple je v místním pole hybridních cloudových úložišť, které poskytuje primární úložiště a iSCSI přístup k datům uloženým na něj. Spravuje komunikace s cloudovým úložištěm a pomáhá zajistit zabezpečení a důvěrnost všechna data, která je uložena v řešení Microsoft Azure StorSimple.

Zařízení StorSimple zahrnuje vlastnosti jednotek SSD a pevných disků HDD, jakož i podporu pro clustering a automatické převzetí služeb při selhání. Obsahuje zpracovatel sdílené, sdílené úložiště a dvě zrcadlené řadiče. Každý adaptér poskytuje následující:

* Připojení k hostitelskému počítači
* Až šest síťové porty pro připojení k místní síti (LAN)
* Monitorování hardwaru
* Paměť s náhodným přístupem non-volatile (NVRAM), která si zachovává informace, i když dojde k přerušení napájení
* Funkce aktualizace pro správu aktualizací softwaru na serverech v clusteru převzetí služeb při selhání tak, aby se aktualizace minimální nebo žádný vliv na dostupnost služby
* Clusterová služba, která funguje jako cluster back-end, a tím vysokou dostupnost a minimalizovat nežádoucí účinky, které mohou nastat, pokud je pevný disk nebo SSD selže nebo je převedeno do režimu offline

Pouze jeden kontroler je aktivní v libovolném bodě v čase. Pokud se nezdaří aktivní kontroler, druhý řadič se aktivuje automaticky.

Další informace najdete v části [StorSimple hardwarové součásti a stav](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
StorSimple můžete použít k vytvoření cloudového zařízení, která se replikují architekturu a funkce fyzického hybridní úložné zařízení. Řešení StorSimple Cloud Appliance (označované také jako virtuální zařízení StorSimple) spouští v jednom uzlu ve virtuálním počítači Azure. (Cloudového zařízení můžete vytvořit pouze na virtuálním počítači Azure. Nelze vytvořit na zařízení StorSimple nebo na místním serveru.)

Cloudové zařízení má následující funkce:

* Se chová jako fyzické zařízení a může nabídnout rozhraní iSCSI pro virtuální počítače v cloudu.
* Můžete vytvářet neomezený počet cloud Appliance v cloudu a zapnout a vypnout podle potřeby.
* Může pomoct simulace v místním prostředí v zotavení po havárii, vývoj a testovací scénáře a pomáhají s načítání na úrovni položek ze záloh.

Řešení StorSimple Cloud Appliance je k dispozici ve dvou modelů: zařízení 8010 (dříve označované jako 1100 model) a zařízení 8020. Zařízení 8010 má maximální kapacitu s 30 TB. Zařízení 8020, který využívá Azure premium storage, má maximální kapacitu o velikosti 64 TB. (V místních vrstvách služby Azure premium storage ukládá data na disky SSD že standard storage ukládá data na HDD.) Všimněte si, že musí mít účet úložiště Azure premium storage úrovně premium.

Další informace o řešení StorSimple Cloud Appliance, přejděte na [nasadit a spravovat řešení StorSimple Cloud Appliance v Azure](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple
Microsoft Azure StorSimple poskytuje webové uživatelské rozhraní (ve službě Správce zařízení StorSimple), která umožňuje centrálně spravovat datacentra a cloudové úložiště. Služba Správce zařízení StorSimple můžete provádět následující úlohy:

* Konfigurujte systémová nastavení pro zařízení StorSimple.
* Konfigurovat a spravovat nastavení zabezpečení pro zařízení StorSimple.
* Konfigurace vlastností a přihlašovací údaje do cloudu.
* Konfigurace a správa svazků na serveru.
* Konfigurace skupin svazků.
* Zálohovat a obnovovat data.
* Sledování výkonu.
* Zkontrolujte nastavení systému a identifikovat možné problémy.

Služba Správce zařízení StorSimple můžete použít k plnění všech úloh správy s výjimkou těch, které vyžadují systém výpadkům, jako je počáteční nastavení a instalace aktualizací.

Další informace najdete v části [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Prostředí Windows PowerShell pro StorSimple
Windows PowerShell pro StorSimple nabízí rozhraní příkazového řádku, který vám pomůže vytvořit a spravovat služby Microsoft Azure StorSimple a nastavení a monitorovat zařízení StorSimple. Je založené na prostředí Windows PowerShell, rozhraní příkazového řádku, které obsahuje jednoúčelové rutiny určené ke správě zařízení StorSimple. Prostředí Windows PowerShell pro StorSimple má funkce, které vám umožní:

* Registrace zařízení.
* Konfigurace síťového rozhraní na zařízení.
* Nainstalujte určité typy aktualizací.
* Řešení potíží s zařízení s přístupem k relaci podpory.
* Změňte stav zařízení.

Lze použít prostředí Windows PowerShell pro StorSimple z konzoly sériového portu (na hostitelském počítači připojené přímo k zařízení) nebo vzdáleně pomocí vzdálené komunikace Windows Powershellu. Všimněte si, že Windows Powershellu pro StorSimple úkoly, jako je například počáteční registrace zařízení, lze provést pouze v konzole sériového portu.

Další informace najdete v části [pomocí Windows Powershellu pro StorSimple ke správě vašeho zařízení](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Rutiny Azure Powershellu StorSimple
Rutiny Azure Powershellu StorSimple jsou kolekce rutiny Windows Powershellu, které umožňují automatizaci úrovně služby a úlohy migrace z příkazového řádku. Další informace o rutinách prostředí Azure PowerShell pro StorSimple, přejděte [Reference k rutinám](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0).

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
StorSimple Snapshot Manageru je modul snap-in konzoly Microsoft Management Console (MMC), který můžete použít k vytvoření konzistentní vzhledem k aplikacím, v okamžiku záložní kopie místní a Cloudová data. Modul snap-in běží na hostiteli se systémem Windows Server. Můžete použít StorSimple Snapshot Manageru do:

* Konfigurace zálohování a odstraňte svazky.
* Konfigurace skupin svazků, aby zálohovaných dat je konzistentních s aplikací.
* Správa zásad zálohování tak, aby data se zálohují na předem určeného plánu a uložená v určeném místě (místně nebo v cloudu).
* Obnovení svazků a jednotlivé soubory.

Zálohy jsou zachyceny jako snímky, které zaznamenává pouze změny, protože byl proveden poslední snímek a vyžaduje mnohem méně místa než úplné zálohy. Můžete vytvořit plány zálohování nebo provést okamžité zálohování podle potřeby. Kromě toho můžete StorSimple Snapshot Manageru k vytvoření zásady uchovávání informací tento ovládací prvek, kolik snímků se uloží. Pokud potřebujete následně data obnovit ze zálohy, umožňuje StorSimple Snapshot Manageru vyberte z katalogu místních nebo cloudových snímků. 

Pokud dojde k havárii, nebo pokud potřebujete obnovit data z nějakého jiného důvodu, StorSimple Snapshot Manageru obnoví se postupně dle potřeby. Obnovení dat nevyžaduje, aby během obnovení souboru, nahraďte zařízení nebo přesunout do jiné lokality operace vypnutý celý systém.

Další informace najdete v části [co je StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>StorSimple Adapter pro SharePoint
Microsoft Azure StorSimple zahrnuje StorSimple Adapter pro SharePoint, volitelná komponenta, která rozšiřuje transparentně StorSimple úložiště a data funkce ochrany do Sharepointové serverové farmy. Adaptér pracuje s zprostředkovatele vzdálené úložiště objektů Blob (RBS) a funkce SQL Server RBS umožňuje přesunout objekty BLOB na server zálohovány v systému Microsoft Azure StorSimple. Microsoft Azure StorSimple pak uloží data objektů BLOB místně nebo v cloudu, na základě využití.

StorSimple Adapter pro SharePoint je spravovat z portálu pro centrální správu služby SharePoint. V důsledku toho zůstane Centrální správa služby SharePoint a veškeré součásti úložiště se zobrazí ve farmě služby SharePoint.

Další informace najdete v části [StorSimple Adapter pro SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Technologie správy úložiště
Kromě vyhrazené zařízení StorSimple, virtuální zařízení a další součásti Microsoft Azure StorSimple používá následující technologie softwarově poskytují rychlý přístup k datům a snížit spotřebu úložiště:

* [Automatické vrstvení](#automatic-storage-tiering) 
* [dynamické zajišťování](#thin-provisioning) 
* [Deduplikace a komprese](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Automatické vrstvení
Microsoft Azure StorSimple automaticky uspořádá data na logické úrovni na základě aktuálního využití, věk a relace s jinými daty. Data, která jsou nejaktivnější uložená místně, zatímco méně aktivní i neaktivní data se automaticky migrovat do cloudu. Následující diagram znázorňuje tento přístup úložiště.

![Vrstvy úložiště StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Umožňuje rychlý přístup StorSimple ukládá velmi aktivní data (horká data) na jednotkách SSD v zařízení StorSimple. Ukládá data, která se někdy používá (vyzkoušeli dat) na pevné disky v zařízení nebo na serverech v datovém centru. Přesune neaktivní data zálohovaná data a data uchovávají po archivaci nebo pro účely dodržování předpisů do cloudu. 

> [!NOTE]
> V aktualizaci Update 2 nebo novější můžete zadat jako místně připojené svazku, v takovém případě data zůstanou na místním zařízení a není Vrstvená do cloudu. 


StorSimple upraví a mění uspořádání dat a změnit přiřazení úložiště jako vzorce používání. Některé informace mohou být například míň aktivní v čase. Jakmile bude postupně míň aktivní, migrována z disků SSD na pevné disky a pak do cloudu. Pokud ke stejným datům opět aktivní, je migrovat zpět na zařízení úložiště.

Proces vrstvení úložiště se provádí následujícím způsobem:

1. Správce systému nastaví účtu cloudového úložiště Microsoft Azure.
2. Správce používá sériové konzoly a služby Správce zařízení StorSimple (spuštěné na webu Azure Portal) ke konfiguraci zařízení a soubor serveru, vytváří svazky a zásady ochrany. Místní počítače (třeba souborové servery) použít pro přístup k zařízení StorSimple Internet Small Computer System Interface (iSCSI).
3. Na začátku StorSimple ukládá data ve vrstvě SSD rychlé zařízení.
4. Jako u vrstvy SSD blíží kapacitě, StorSimple deduplicates nejstarší datové bloky komprimuje a přesune do vrstvy HDD.
5. Jako kapacity přístupy vrstvy HDD StorSimple šifruje nejstarší bloky dat a bezpečně je odešle do účtu úložiště Microsoft Azure přes protokol HTTPS.
6. Microsoft Azure vytvoří několik replik dat svého datového centra a ve vzdáleném datovém centru, zajištění, že data půjde obnovit v případě, že dojde k havárii.
7. Když souborový server vyžádá data uložená v cloudu, StorSimple vrátí bez problémů a ukládá jejich kopii ve vrstvě SSD zařízení StorSimple.

#### <a name="how-storsimple-manages-cloud-data"></a>Jak StorSimple spravuje data v cloudu

StorSimple deduplicates zákaznická data napříč všechny snímky a primární daty (data zapsaná hostiteli). Odstranění duplicitních dat jsou skvělé pro efektivitu úložiště, je otázka "Jaký je v cloudu" složité. Primární Vrstvená data a data snímku se mezi sebou. Jediný neodkazovaný blok dat v cloudu by se použil jako primární Vrstvená data a také odkazovat několik snímků. Každý snímek v cloudu se zajistí, že kopie všech dat v daném okamžiku je uzamčen do cloudu až do odstranění tohoto snímku.

Data se odstraní pouze z cloudu, když neexistují žádné odkazy na tato data. Například, pokud jsme pořídit snímek v cloudu všechna data, která je v zařízení StorSimple a potom odstraňte některá primární data, uvidíme _primární datové_ vyřadit okamžitě. _Cloudová data_ zahrnuje Vrstvená data a zálohy, zůstává stejný. Je to proto, že je stále odkazuje Cloudová data snímku. Po cloudu snímek odstraní (a další, na který odkazuje na stejná data snímku), drops využití cloudu. Předtím, než jsme odebrat data v cloudu, zkontrolujeme, že žádné snímky stále odkazují na tato data. Tento proces se nazývá _uvolňování_ a na zařízení běží na pozadí služby. Odstranění dat cloud není okamžitě, protože služba uvolňování paměti kolekce kontroluje žádné odkazy na tato data před odstranění. Rychlost uvolňování závisí na celkovém počtu snímků a celkové množství dat. Cloudová data je obvykle vyčištěn za méně než jeden týden.


### <a name="thin-provisioning"></a>dynamické zajišťování
Dynamické zajišťování je technologie virtualizace, ve kterém se zobrazí dostupné úložiště překročí fyzické prostředky. Místo předem rezervace dostatečné úložiště StorSimple používá dynamické zajišťování přidělit jenom dostatek místa, aby splňovala aktuální požadavky. K elastické povaze cloudu úložiště usnadňuje tento přístup, protože StorSimple můžete zvýšit nebo snížit cloudové úložiště s cílem splnit měnící požadavky.

> [!NOTE]
> Místně připojené svazky nejsou tence zřízený. Úložiště přidělené k pouze místní svazek je zřízený v celém rozsahu při vytvoření svazku.


### <a name="deduplication-and-compression"></a>Deduplikace a komprese
Microsoft Azure StorSimple používá komprese dat a odstranění duplicit dále snížit požadavky na úložiště.

Odstranění duplicitních dat snižuje celkové množství dat uložených odstraněním redundance v sadě dat uložené. Jak se změní informace, StorSimple ignoruje se zobrazila původní data a zachytí pouze změny. StorSimple navíc snižuje množství uložených dat a odebrat nepotřebné informace. 

> [!NOTE]
> Není data na místně připojených svazků s odstraněním duplicitních dat nebo komprimované. Ale zálohování místně připojené svazky se odstraňují duplicity a komprimované.


## <a name="storsimple-workload-summary"></a>Souhrn úloh StorSimple
Přehled podporovaných úloh StorSimple v následující tabulce.

| Scénář | Úloha | Podporováno | Omezení | Verze |
| --- | --- | --- | --- | --- |
| Spolupráce |Sdílení souborů |Ano | |Všechny verze |
| Spolupráce |Sdílení souborů DFS |Ano | |Všechny verze |
| Spolupráce |SharePoint |Ano* |Podporuje jenom s místně připojené svazky |S aktualizací Update 2 a novější |
| Archivace |Archivace jednoduchého souboru |Ano | |Všechny verze |
| Virtualizace |Virtuální počítače |Ano* |Podporuje jenom s místně připojené svazky |S aktualizací Update 2 a novější |
| Databáze |SQL |Ano* |Podporuje jenom s místně připojené svazky |S aktualizací Update 2 a novější |
| Sledování videí |Sledování videí |Ano* |Podporováno, pokud zařízení StorSimple je vyhrazen pouze pro tuto úlohu |S aktualizací Update 2 a novější |
| Backup |Primární cíl zálohování |Ano* |Podporováno, pokud zařízení StorSimple je vyhrazen pouze pro tuto úlohu |S aktualizací Update 3 a novější |
| Backup |Sekundární cíl zálohování |Ano* |Podporováno, pokud zařízení StorSimple je vyhrazen pouze pro tuto úlohu |S aktualizací Update 3 a novější |

*Ano&#42; – bude použito řešení pokyny a omezení.*

Následující úlohy nejsou podporovány zařízeních StorSimple řady 8000. Pokud nasadíte na StorSimple, způsobí tyto úlohy má nepodporovanou konfiguraci.

* Lékařské vytvoření bitové kopie
* Výměna
* VDI
* Oracle
* SAP
* Velký objem dat
* Distribuce obsahu
* Spuštění z rozhraní SCSI

Tady je seznam komponent infrastruktury StorSimple nepodporuje.

| Scénář | Úloha | Podporováno | Omezení | Verze |
| --- | --- | --- | --- | --- |
| Obecné |ExpressRoute |Ano | |Všechny verze |
| Obecné |DataCore FC |Ano* |Podporuje DataCore SANsymphony |Všechny verze |
| Obecné |DFSR |Ano* |Podporuje jenom s místně připojené svazky |Všechny verze |
| Obecné |Indexování |Ano* |Pro vrstvených svazků je podporována pouze metadata indexování (žádná data).<br>Úplné indexování se podporuje pro místně připojené svazky. |Všechny verze |
| Obecné |Ochrana proti virům |Ano* |Vrstvené svazky jsou podporovány pouze kontroly při otevření a zavření.<br> Místně vázaných svazků se podporuje úplnou kontrolu. |Všechny verze |

*Ano&#42; – bude použito řešení pokyny a omezení.*

Tady je seznam dalšího softwaru, které se používají storsimple a vytvářet tak řešení.

| Typ úlohy | Software používaný službou StorSimple | Podporované verze|Odkaz na Průvodce řešením| 
| --- | --- | --- | --- |
| Cíl zálohování |Veeam |Sada Veeam v 9 a novější |[StorSimple jako cíl zálohování s Veaam](storsimple-configure-backup-target-veeam.md)|
| Cíl zálohování |Veritas Backup Exec |Backup Exec 16 a novější |[StorSimple jako cíl zálohování se službou Backup Exec](storsimple-configure-backup-target-using-backup-exec.md)|
| Cíl zálohování |VERITAS NetBackup |NetBackup 7.7.x a novější  |[StorSimple jako cíl zálohování s NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Globální soubor pro sdílení obsahu <br></br> Spolupráce |Talon  |[StorSimple s Talon](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>Terminologie StorSimple
Před nasazením řešení Microsoft Azure StorSimple, doporučujeme, abyste si následující pojmy a definice.

### <a name="key-terms-and-definitions"></a>Klíčové pojmy a definice
| Výraz (zkratka nebo zkratky) | Popis |
| --- | --- |
| záznam řízení přístupu (ACR) |Záznam přidružené k svazku na zařízení s Microsoft Azure StorSimple, která určuje, které hostitele může připojit k němu. Určení je založeno na iSCSI kvalifikovaný název (IQN) hostitele (obsažené v ACR), které se připojujete k zařízení StorSimple. |
| AES-256 |Algoritmus šifrování AES (Advanced Standard) 256 bitů pro šifrování dat při jejich přesunu do a z cloudu. |
| velikost alokační jednotky (AU) |Nejmenší velikost místa na disku, který je možné přidělit pro uložení souboru ve vaší Windows systémů souborů. Pokud velikost souboru není násobkem velikosti clusteru, místo navíc je nutné použít k uložení souboru (až na další násobek velikosti clusteru) ke ztrátě místa a fragmentace pevného disku. <br>Doporučené jednotek AU pro svazky zařízení Azure StorSimple je 64 KB, protože funguje s algoritmy pro odstranění duplicit. |
| Automatické vrstvení |Automaticky přesouvá míň aktivní data z disků SSD na pevné disky a potom na úrovni v cloudu a pak umožní spravovat všechna úložiště z centrální uživatelského rozhraní. |
| katalog záloh |Kolekce zálohování obvykle souvisí podle typu aplikace, která byla použita. Tato kolekce se zobrazí v okně Katalog zálohování ve službě Správce zařízení StorSimple uživatelského rozhraní. |
| soubor katalogu záloh |Soubor obsahující seznam dostupných snímků, které jsou aktuálně uloženy v databázi zálohování StorSimple Snapshot Manageru. |
| zásady zálohování |Výběr svazků, typ zálohy a časového plánu, který umožňuje vytvářet zálohy podle předdefinovaného plánu. |
| binární rozsáhlé objekty (objekty BLOB) |Kolekce binární data uložená jako jedna entita v systému správy databáze. Objekty BLOB jsou obvykle obrázky, zvuk nebo jiných objektů multimédií, i když někdy binární spustitelný kód je uložen jako objekt BLOB. |
| Challenge Handshake Authentication Protocol (CHAP) |Protokol používaný k ověření sdílené připojení podle rovnocenné sdílení heslo nebo tajný klíč. Protokol CHAP mohou být jednosměrné nebo vzájemné. S jednosměrnou CHAP ověřuje cíl iniciátor. Vzájemné CHAP vyžaduje, že cíl ověřování iniciátor a že je iniciátor ověří cíl. |
| Klonování |Duplicitní kopie svazku. |
| Cloud jako vrstvu (CaaT) |Cloudové úložiště, které jsou integrovány jako úrovně v rámci architektury úložiště tak, aby veškeré součásti úložiště se zobrazí jako součást jedna podniková síť úložiště. |
| poskytovatele cloudových služeb (CSP) |Poskytovatel služeb cloud computingu. |
| snímek v cloudu |Bodu v čase kopii svazku, který je uložen v cloudu. Snímek v cloudu je ekvivalentní ke snímku replikovat i na jiné vzdálené úložiště systému. Cloudové snímky jsou zvláště užitečné ve scénářích zotavení po havárii. |
| šifrovací klíč cloudového úložiště |Heslo nebo klíč, vaše zařízení StorSimple používá k přístupu k zašifrovaným datům ve vašem zařízení odesílají do cloudu. |
| aktualizace pro clustery |Správa aktualizací softwaru na serverech v clusteru převzetí služeb při selhání tak, aby se aktualizace minimální nebo žádný vliv na dostupnost služeb. |
| DataPath |Kolekce funkční jednotky, které provádějí operace propojených dat zpracování. |
| deaktivovat |Trvalé akce, která přestane fungovat připojení mezi zařízením StorSimple a související cloudové služby. Cloudové snímky zařízení zůstat po dokončení tohoto procesu a můžete klonovat nebo je použitý pro zotavení po havárii. |
| zrcadlení disků |Replikace pro logické diskové svazky na samostatné pevné disky v reálném čase k zajištění trvalé dostupnosti. |
| zrcadlení dynamického disku |Replikace pro logické diskové svazky na dynamických discích. |
| dynamické disky |Formát disku svazku, který používá Správce logických disků (LDM) k ukládání a správě dat mezi několik fyzických disků. Dynamické disky můžete zvětšit poskytnout další volné místo. |
| Rozšířené skříň Bunch disky (EBOD) |Sekundární skříň zařízení Microsoft Azure StorSimple, která obsahuje disky velmi pevného disku pro další úložiště. |
| FAT zřizování |Konvenční zajišťování úložiště v úložišti, které je přiděleno místo na základě předpokládaných potřeb (a je obvykle nad aktuální potřebě). Viz také *zajišťování*. |
| jednotku pevného disku (HDD) |Jednotka, která používá několik otáčení ploten ukládat data. |
| hybridní cloudové úložiště |Architektura úložiště, který používá místní a vzdálené prostředky, včetně cloudového úložiště. |
| Internet Small Computer System Interface (iSCSI) |Úložiště založené na protokolu IP sítě standard pro propojení zařízení úložiště dat nebo zařízení. |
| Iniciátor iSCSI |Softwarová součást, která umožňuje počítači hostitele se systémem Windows k připojení k síti externí úložiště založené na standardu iSCSI. |
| iSCSI kvalifikovaný název (IQN) |Jedinečný název, který identifikuje iniciátoru nebo cíl iSCSI. |
| cíl iSCSI |Softwarová součást, která poskytuje centralizovanou iSCSI diskových podsystémů v SAN. |
| Live archivace |S přístupem úložiště, ve kterém archivních dat je dostupné neustále (nebude uložen mimo na pásku, například). Microsoft Azure StorSimple využívá živé archivaci. |
| místně připojený svazek |svazek, který se nachází na zařízení a není nikdy Vrstvená do cloudu. |
| místní snímek |V okamžiku kopie objemu dat uložených na zařízení Microsoft Azure StorSimple. |
| Microsoft Azure StorSimple |Výkonné řešení skládající se z datacenter zařízení úložiště a software, který umožňuje IT organizacím využívat cloudové úložiště, jako by šlo úložiště datového centra. StorSimple usnadňuje ochranu dat a správu dat při současném snížení nákladů. Toto řešení spojuje primární úložiště, archivace, zálohování a zotavení po havárii (DR) díky bezproblémové integraci s cloudem. Díky spojení SAN úložištím a cloudovým správu dat na podnikové úrovni platformě, povolte zařízení StorSimple rychlost, jednoduchost a spolehlivost pro všechny potřeby týkající se úložiště. |
| Napájení, chlazení modulu (PCM) |Hardwarové součásti vašeho zařízení StorSimple skládající se z napájení a chladicí ventilátory, tedy název napájení a chlazení modulu. Primární zařízení zařízení je dvě PCMs 764W, zatímco skříň EBOD jsou dvě PCMs 580W. |
| Primární zařízení |Hlavní skříň, která obsahuje řadiče aplikací platformy zařízení StorSimple. |
| Plánovaná doba obnovení (RTO) |Maximální množství času, který by měl být vynaloženého před obchodních procesů nebo systém plně obnovení po havárii. |
| sériově připojené SCSI (SAS) |Typ pevného disku (HDD). |
| šifrovací klíč dat služby |Klíč k dispozici nové zařízení StorSimple, který se zaregistruje ve službě Správce zařízení StorSimple. Konfigurační data přenášená mezi ve službě Správce zařízení StorSimple a zařízení je zašifrovaná pomocí veřejného klíče a pak mohou ho dešifrovat jenom na zařízení pomocí soukromého klíče. Šifrovací klíč dat služby umožňuje službě získat tento privátní klíč pro dešifrování. |
| registrační klíč služby |Klíč, který umožňuje registraci zařízení StorSimple ve službě Správce zařízení StorSimple, tak, aby se zobrazí na webu Azure Portal pro další akce správy. |
| Small Computer System Interface (SCSI) |Sada standardů pro fyzicky připojení počítačů a předávání dat mezi nimi. |
| jednotky SSD (Solid-State Drive) |Disk, který neobsahuje žádné části přesunutí; například flash disk. |
| účet úložiště |Sadu přihlašovacích údajů pro přístup propojený s vaším účtem úložiště pro poskytovatele služeb daného cloudu. |
| StorSimple Adapter pro SharePoint |Komponenty Microsoft Azure StorSimple, která rozšiřuje transparentně StorSimple úložiště a ochranu dat na Sharepointové serverové farmy. |
| Služba Správce zařízení StorSimple |Rozšíření na webu Azure portal, který vám umožní spravovat Azure StorSimple místní a virtuální zařízení. |
| StorSimple Snapshot Manager |Microsoft Management Console (MMC) snap-in pro správu zálohování a obnovení operací v Microsoft Azure StorSimple. |
| vytvořit zálohu |Funkce, která umožňuje uživateli vytvořit interaktivní zálohu svazku. Je to alternativní způsob vytvoření ruční zálohy svazku, na rozdíl od vytvoření automatizované zálohy prostřednictvím definované zásady. |
| dynamické zajišťování |Metoda optimalizace efektivity, pomocí kterého dostupným úložným prostorem slouží v úložných systémů. V dynamické zajišťování úložiště přidělené mezi víc uživateli založené na minimální prostor každý uživatel v daném okamžiku. Viz také *fat zřizování*. |
| ovládání datových vrstev |Uspořádání dat v logické skupiny na základě aktuálního využití, věk a relace s jinými daty. StorSimple se automaticky uspořádá data ve vrstvách. |
| svazek |Oblasti logického úložiště zobrazí ve formuláři jednotek. Svazky zařízení StorSimple odpovídají svazků připojených hostitele, včetně těch, které zjištěných pomocí iSCSI a zařízení StorSimple. |
| kontejner svazků |Seskupení svazky a nastavení, které se vztahují k nim. Všechny svazky v zařízení StorSimple jsou seskupené do kontejnerů svazků. Nastavení kontejneru svazků patří účty úložiště, nastavení šifrování pro data odesílaná do cloudu s využitím přidružených šifrovacích klíčů a využitá šířka pásma pro operace, které zahrnují cloudu. |
| skupiny svazků |Ve StorSimple Snapshot Manageru skupiny svazků je kolekce svazků nakonfigurovaných pro usnadnění zálohování zpracování. |
| Služby Stínová kopie svazku (VSS) |Služba operačního systému Windows Server, která usnadňuje konzistentnost aplikací tím, že komunikuje s aplikací podporujících službu VSS pro koordinaci vytváření přírůstkových snímků. Stínové kopie svazku zajišťuje, že aplikace budou dočasně neaktivní při snímky. |
| Prostředí Windows PowerShell pro StorSimple |Založené na prostředí Windows PowerShell – rozhraní příkazového řádku používají k provozování a správě zařízení StorSimple. Při zachování některé základní funkce Windows Powershellu, toto rozhraní má další jednoúčelové rutiny určené, která jsou zaměřených na správu zařízení StorSimple. |

## <a name="next-steps"></a>Další postup
Další informace o [zabezpečení zařízení StorSimple](storsimple-8000-security.md).

