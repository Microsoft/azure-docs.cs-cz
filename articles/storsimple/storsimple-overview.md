---
title: Přehled řešení řady StorSimple 8000 | Microsoft Docs
description: Popisuje vrstvení StorSimple, zařízení, virtuální zařízení, služby a správu úložiště a zavádí klíčových termínů používaných v zařízení StorSimple.
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
ms.openlocfilehash: b0d6a797e4fe84ce2cb969d001ff96177f6ac641
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097042"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>Řady StorSimple 8000: řešení hybridní cloudové úložiště
## <a name="overview"></a>Přehled
Vítá vás Microsoft Azure StorSimple o řešení integrované úložiště, který spravuje úlohy úložiště mezi místními zařízeními a cloudového úložiště Microsoft Azure. StorSimple je řešení sítě SAN oblasti efektivní, nákladově efektivní a snadno spravovatelné úložiště, které eliminuje řadu problémů a náklady spojené s enterprise úložiště a ochranu dat. Vlastní zařízení řady StorSimple 8000 používá, se integruje s cloudovými službami a poskytuje sadu nástrojů pro správu pro bezproblémové zobrazení všech podnikového úložiště, včetně cloudového úložiště. (Informace o nasazení zařízení StorSimple publikované na webu Microsoft Azure platí pro pouze řadu zařízení StorSimple 8000. Pokud používáte zařízení řady StorSimple 5000/7000, přejděte na [StorSimple nápovědy](http://onlinehelp.storsimple.com/).)

Používá StorSimple [vrstvení úložiště](#automatic-storage-tiering) ke správě uložených dat na různých médiích úložiště. Aktuální pracovní sada je uloženého místně na jednotky SSD (Solid-State Drive), data, která se používá méně často je uložené na pevných disků (HDD) a archivaci dat vložena do cloudu. Kromě toho StorSimple používá odstraňování duplicitních dat a komprese ke snížení množství úložiště, který využívá data. Další informace, přejděte na [odstraňování duplicitních dat a komprese](#deduplication-and-compression). Definice jiné klíče podmínky a koncepty používané v dokumentaci řady StorSimple 8000, přejděte na [StorSimple terminologie](#storsimple-terminology) na konci tohoto článku.

Funkce ochrany dat StorSimple kromě správy úložiště, umožňují vytvářet na vyžádání a naplánované zálohy a poté je uložit místně nebo v cloudu. Ve formě přírůstkové snímky, což znamená, že může být vytvořen a rychle obnovit jsou provedeny zálohy. Cloudové snímky může být důležité ve scénářích zotavení po havárii, protože nahradit sekundární úložných systémů (například zálohování na pásku) a umožňují obnovit data do datového centra nebo do alternativní lokality v případě potřeby.

![Ikona video](./media/storsimple-overview/video_icon.png) Podívejte se na video pro rychlý úvod do Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Proč používat StorSimple?
Následující tabulka popisuje některé klíčové výhody, které poskytuje Microsoft Azure StorSimple.

| Funkce | Výhoda |
| --- | --- |
| Transparentní integrace |Tedy bez zásahu uživatele propojení zařízení úložiště dat používá protokol iSCSI. To zajistí, že data uložená v cloudu, v datovém centru, nebo na vzdálených serverech se zobrazí ukládaly na jednom místě. |
| Náklady na menší úložiště |Přiděluje dostatečná místní nebo cloudové úložiště splňovat požadavky na aktuální a rozšiřuje pouze v případě potřeby úložiště v cloudu. Dále snižuje požadavky na úložiště a náklady odstraněním redundantní verzích stejná data (odstranění duplicitních dat) a pomocí komprese. |
| Jednodušší správu úložišť |Poskytuje nástroje pro správu ke konfiguraci a správě dat uložených místně na vzdáleném serveru a v cloudu systému. Kromě toho můžete spravovat zálohování a obnovení funkce z modul snap-in konzoly Microsoft Management Console (MMC).|
| Zotavení po havárii vylepšené a dodržování předpisů |Nevyžaduje žádný čas rozšířené obnovení. Místo toho obnoví data, protože je potřeba. To znamená, že můžete pokračovat normální provozní podmínky s minimálním dopadem. Kromě toho můžete nakonfigurovat zásady k určení plánů zálohování a uchovávání. |
| Data nastavení mobilních zařízení |Data nahraná do cloudové služby Microsoft Azure je přístupný z jiných webů pro účely obnovení a migrace. Kromě toho můžete StorSimple konfigurace zařízení StorSimple cloudu na virtuálních počítačích (VM) běžící v Microsoft Azure. Virtuální počítače pak můžete použít virtuální zařízení pro přístup k uložená data pro účely testovací nebo obnovení. |
| Kontinuita podnikových procesů |Umožňuje uživatelům řady 5000 7000 migrovat data do zařízení řady StorSimple 8000 StorSimple. |
| Dostupnost v portálu Azure Government |StorSimple je k dispozici na portálu Azure Government. Další informace najdete v tématu [nasazení místního zařízení StorSimple na portálu Government](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Ochrana dat a dostupnost |Řady StorSimple 8000 podporuje zóny redundantní úložiště (ZRS), kromě místně redundantní úložiště (LRS) a geograficky redundantní úložiště (GRS). Odkazovat na [Tento článek na možnosti redundance úložiště Azure](https://azure.microsoft.com/documentation/articles/storage-redundancy/) podrobnosti ZRS. |
| Podpora pro kritické aplikace |StorSimple vám umožní identifikovat odpovídajících svazků podle místně vázaný zase zajišťuje, že data požadovaných důležitých aplikací není vrstvené do cloudu. Místně vázaných svazků se nevztahuje cloudu latenci nebo problémy s připojením. Další informace o místně vázaných svazků najdete v tématu [pomocí služby StorSimple Manager zařízení můžete spravovat svazky](storsimple-8000-manage-volumes-u2.md). |
| S nízkou latencí a vysoký výkon |Můžete vytvořit cloudu zařízení, které využívá výhod vysoký výkon, nízká latence funkce úložiště Azure premium Storage. Další informace o StorSimple premium cloudu zařízení najdete v tématu [nasadit a spravovat o cloudu zařízení StorSimple v Azure](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>Součásti StorSimple
Řešení Microsoft Azure StorSimple zahrnuje následující součásti:

* **Microsoft Azure StorSimple zařízení** – místní hybridní diskové pole, které obsahuje SSD a HDD, společně s redundantní řadiče a možnosti automatické převzetí služeb při selhání. V řadičích spravovat úložiště vrstvení, umístění aktuálně používá (nebo aktivní) data na místní úložiště (v zařízení nebo na místní servery), při přesouvání méně často používaná data do cloudu.
* **Zařízení StorSimple cloudu** – známý také jako virtuální zařízení StorSimple, to je software verzi zařízení StorSimple, která se replikují architekturu a většina možností fyzické hybridní zařízení úložiště. Cloud zařízení StorSimple běží na jednom uzlu ve virtuálním počítači Azure. Virtuální zařízení Premium, které můžete využít Azure premium storage, jsou k dispozici v aktualizaci Update 2 nebo novější.
* **Služba StorSimple Manager zařízení** – na rozšíření na portálu Azure, který vám umožní spravovat zařízení StorSimple nebo zařízení StorSimple cloudu z jedné webové rozhraní. Můžete vytvořit a spravovat služby, zobrazit a spravovat zařízení, Zobrazit výstrahy, správu svazků a zobrazit a spravovat zásady zálohování a zálohování katalog služby StorSimple Manager zařízení.
* **Prostředí Windows PowerShell pro StorSimple** – rozhraní příkazového řádku, který můžete použít ke správě zařízení StorSimple. Prostředí Windows PowerShell pro StorSimple je funkce, které vám umožní zaregistrovat zařízení StorSimple, konfigurace síťového rozhraní na vašem zařízení, instalace určité typy aktualizací, řešení potíží s zařízení s přístupem k relaci podpory a změňte zařízení stav. Prostředí Windows PowerShell pro StorSimple můžete přistupovat pomocí připojení ke konzole sériového portu nebo pomocí vzdálené komunikace Windows Powershellu.
* **Rutiny Azure PowerShell StorSimple** – kolekci rutin prostředí Windows PowerShell, které umožňují automatizovat úlohy na úrovni služby a migrace z příkazového řádku. Další informace o rutiny Azure Powershellu pro StorSimple, přejděte na [reference k rutině](/powershell/module/azure/?view=azuresmps-3.7.0#azure).
* **StorSimple Snapshot Manager** – modul snap-in konzoly MMC, které používá svazek skupin a služby Stínová kopie svazku systému Windows ke generování zálohování konzistentní s aplikací. Kromě toho můžete Snapshot Manager zařízení StorSimple vytvořit plánů zálohování a klonování nebo obnovit svazky.
* **Adaptér StorSimple pro službu SharePoint** – nástroj, který transparentně rozšiřuje ochranu Microsoft Azure StorSimple úložiště a data na SharePoint Server hospodářství při vytváření úložišti StorSimple lze zobrazit a spravovat z centrální služby SharePoint Portálu pro správu.

Následující obrázek poskytuje podrobný pohled Microsoft Azure StorSimple architektura a komponenty.

![Architektura StorSimple](./media/storsimple-overview/overview-big-picture.png)

Následující části popisují každou z těchto součástí podrobněji a popisují, jak řešení uspořádá data, přidělí úložiště a usnadňuje správu úložiště a ochranu dat. Poslední část obsahuje definice pro některé důležité termíny a koncepty týkající se součástí StorSimple a jejich správu.

## <a name="storsimple-device"></a>Zařízení StorSimple
Microsoft Azure StorSimple zařízení je místní hybridní diskové pole, které poskytuje primárního úložiště a iSCSI přístup k datům v něm uložená. Spravuje komunikace s cloudovým úložištěm a pomáhá zajistit zabezpečení a důvěrnost všechna data, která je uložena v řešení Microsoft Azure StorSimple.

Zařízení StorSimple zahrnuje SSD a HDD jednotky pevného disku a také podporu pro clustering a automatické převzetí služeb při selhání. Obsahuje sdílené procesor, sdíleného úložiště a dvě zrcadlené řadiče. Každý řadič obsahuje následující informace:

* Připojení k hostitelskému počítači
* Až šest síťové porty pro připojení k místní síti (LAN)
* Monitorování hardwaru
* Paměť s náhodným přístupem non-volatile (paměti NVRAM), která uchovává informace i v případě, že bude přerušen přívod energie
* Ke správě aktualizací softwaru na serverech v clusteru s podporou převzetí služeb při selhání tak, aby aktualizace minimální aktualizace pro clustery nebo žádný vliv na dostupnost služby
* Clusterová služba, která funguje jako back-end clusteru poskytuje vysokou dostupnost a současně minimalizujete její nežádoucí účinky, ke kterým může dojít, pokud na pevný disk nebo SSD selže nebo je uveden do režimu offline

Jenom jeden řadič je aktivní v libovolném bodě v čase. Pokud řadič active selže, druhý řadič se stane aktivní automaticky.

Další informace, přejděte na [StorSimple hardwarové součásti a stav](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
StorSimple můžete použít k vytvoření cloudové zařízení, který replikuje architektuře a možnosti úložiště zařízení fyzické hybridní. Cloud zařízení StorSimple (také označované jako StorSimple virtuální zařízení) se spouští v jednom uzlu ve virtuálním počítači Azure. (Cloudu zařízení lze vytvořit pouze na virtuální počítač Azure. Není možné ji vytvořit na zařízení StorSimple nebo na místním serveru.)

Cloud zařízení má následující funkce:

* Se chová jako fyzické zařízení a nabízejí rozhraní iSCSI pro virtuální počítače v cloudu.
* Můžete vytvořit neomezený počet zařízení cloudu v cloudu a zapnout je zapnout a vypnout podle potřeby.
* Může pomoci simulaci prostředí místní zotavení po havárii, vývoj a testovací scénáře a může pomoct s načítání na úrovni položek ze zálohy.

Cloud zařízení StorSimple je k dispozici v dva modely: zařízení 8010 (dříve označované jako 1100 modelu) a zařízení 8020. Zařízení 8010 má maximální kapacita 30 TB. Zařízení 8020, který využívá úložiště Azure premium Storage, má maximální kapacitu 64 TB. (V místních vrstvách Azure premium storage ukládá data na jednotkách SSD zatímco úložiště standard storage ukládá data na HDD.) Všimněte si, že musí mít účet úložiště Azure premium, který chcete používat úložiště úrovně premium. Další informace o storage úrovně premium, přejděte na [úložiště Premium: vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](../virtual-machines/windows/premium-storage.md).

Další informace o cloudu zařízení StorSimple, přejděte na [nasadit a spravovat o cloudu zařízení StorSimple v Azure](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple
Microsoft Azure StorSimple poskytuje webové uživatelské rozhraní (služba StorSimple Manager zařízení), které vám umožňuje centrálně spravovat datacenter a cloudového úložiště. Služby StorSimple Manager zařízení můžete provádět následující úlohy:

* Nakonfigurujte nastavení systému pro zařízení StorSimple.
* Konfigurovat a spravovat nastavení zabezpečení pro zařízení StorSimple.
* Nakonfigurujte pověření cloudu a vlastnosti.
* Konfigurovat a spravovat svazky na serveru.
* Nakonfigurujte skupiny svazku.
* Zálohování a obnovení dat.
* Sledování výkonu.
* Zkontrolujte nastavení systému a identifikovat možné problémy.

Služby StorSimple Manager zařízení můžete použít k plnění všech úloh správy kromě těch, které vyžadují systém výpadek, jako je počáteční nastavení a instalace aktualizací.

Další informace, přejděte na [použít službu StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Prostředí Windows PowerShell pro StorSimple
Prostředí Windows PowerShell pro StorSimple poskytuje rozhraní příkazového řádku, který vám pomůže vytvořit a spravovat službu Microsoft Azure StorSimple a nastavit a monitorování zařízení StorSimple. Je založených na prostředí Windows PowerShell, rozhraní příkazového řádku, která zahrnuje vyhrazené rutiny pro správu zařízení StorSimple. Prostředí Windows PowerShell pro StorSimple je funkce, které vám umožní:

* Registrovat zařízení.
* Nakonfigurujte rozhraní sítě na zařízení.
* Nainstalujte určité typy aktualizací.
* Řešení potíží s zařízení s přístupem k relaci podpory.
* Změňte stav zařízení.

Dostanete Windows Powershellu pro StorSimple z konzoly sériového portu (na hostitelském počítači připojené přímo k zařízení) nebo vzdáleně pomocí vzdálenou komunikaci prostředí Windows PowerShell. Všimněte si, že některé Windows Powershellu pro StorSimple úkoly, jako je registrace počáteční zařízení, můžete provést pouze na konzole sériového portu.

Další informace, přejděte na [pomocí Windows Powershellu pro StorSimple ke správě zařízení](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Rutiny Azure PowerShell StorSimple
Rutiny Azure PowerShell StorSimple jsou kolekce rutin prostředí Windows PowerShell, které umožňují automatizovat úlohy na úrovni služby a migrace z příkazového řádku. Další informace o rutiny Azure Powershellu pro StorSimple, přejděte na [reference k rutině](/powershell/module/azure/?view=azuresmps-3.7.0).

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
Snapshot Manager zařízení StorSimple je modul snap-in konzoly Microsoft Management Console (MMC), který můžete použít k vytvoření konzistentní, v okamžiku záložní kopie místní a Cloudová data. Modul snap-in běží na hostiteli se systémem Windows Server. Můžete použít StorSimple Snapshot Manager do:

* Konfigurace, zálohovat a odstraňte svazky.
* Konfigurace skupin svazku zajistit zálohovaných dat je konzistentní s aplikací.
* Spravovat zásady zálohování tak, aby se data zálohují na předem určeného plánu a uložené v určené umístění (místně nebo v cloudu).
* Obnovení svazků a jednotlivé soubory.

Zálohování se zaznamená jako snímky, které záznam pouze změny, protože byl proveden poslední snímek a vyžadovat mnohem méně místa než úplné zálohy. Můžete vytvořit plánů zálohování nebo provést okamžitou zálohování podle potřeby. Kromě toho můžete StorSimple Snapshot Manager k vytvoření zásady uchovávání informací, řídíte, kolik snímků se uloží. Pokud je následně nutné obnovit data ze zálohy, umožňuje Snapshot Manager zařízení StorSimple, vyberte z katalogu místní nebo cloudové snímky. 

Pokud dojde k havárii, nebo pokud potřebujete obnovit data z jiného důvodu, StorSimple Snapshot Manager obnoví se postupně dle potřeby. Obnovení dat nevyžaduje vypnout celého systému při obnovení souboru, nahraďte zařízení nebo operace přesunout do jiné lokality.

Další informace, přejděte na [co je StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>StorSimple Adapter pro SharePoint
Microsoft Azure StorSimple zahrnuje adaptér StorSimple pro službu SharePoint, volitelná komponenta, která rozšiřuje transparentně StorSimple úložiště a dat. funkce ochrany na serverové farmy služby SharePoint. Adaptér funguje pro zprostředkovatele vzdáleného úložiště objektů Blob (RBS) a funkci SQL Server RBS umožňuje přesunout objekty BLOB na server zálohovat systém Microsoft Azure StorSimple. Microsoft Azure StorSimple uloží data objektu BLOB místně nebo v cloudu, na základě využití.

Adaptér StorSimple pro službu SharePoint se spravují v rámci portálu Centrální správa SharePoint. V důsledku toho zůstává Centrální správa služby SharePoint a všechny úložiště zobrazuje ve farmě služby SharePoint.

Další informace, přejděte na [StorSimple adaptéru pro službu SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Technologie správy úložiště
Kromě vyhrazené zařízení StorSimple, virtuální zařízení a další součásti Microsoft Azure StorSimple používá následující technologie softwarově zajistit rychlý přístup k datům a snižovat spotřebu úložiště:

* [Automatické úložiště vrstvení](#automatic-storage-tiering) 
* [Dynamické zajišťování](#thin-provisioning) 
* [Odstranění duplicitních dat a komprese](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Automatické úložiště vrstvení
Microsoft Azure StorSimple automaticky uspořádá data v logické vrstvy na základě aktuálního využití, stáří a vztahů s jinými daty. Data, která je nejvíce aktivní uložené lokálně, zatímco se automaticky migrují méně aktivní i neaktivní data do cloudu. Následující diagram znázorňuje tento přístup úložiště.

![Vrstvy úložiště StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Povolit rychlý přístup, StorSimple ukládá velmi aktivní data (aktivní data) na jednotkách SSD v zařízení StorSimple. Ukládá data, která se někdy používá (tedy dat) na pevné disky v zařízení nebo na serverech v datovém centru. Přesune neaktivní data zálohovaná data a data uchovávat archivace nebo pro účely dodržování předpisů do cloudu. 

> [!NOTE]
> V aktualizaci Update 2 nebo novější můžete zadat jako místně vázaný svazek, v takovém případě data zůstane na místním zařízení a není vrstvené do cloudu. 


StorSimple upraví a změní uspořádání dat a změnit přiřazení úložiště jako vzorce používání. Některé informace mohou být například míň aktivní v čase. Jakmile je k progresivnímu míň aktivní, je migrované z jednotky SSD na pevné disky a pak do cloudu. Pokud tato data zase aktivní, je migrovat zpět na zařízení úložiště.

Proces vrstvení úložiště, dojde k následujícím způsobem:

1. Nastaví správce systému účet cloudového úložiště Microsoft Azure.
2. Správce používá konzole sériového portu a služby StorSimple Manager zařízení (spuštěnými na portálu Azure) ke konfiguraci serveru zařízení a soubor vytvářet svazky a data zásady ochrany. Místní počítače (například souborové servery) Internet Small Computer System Interface (iSCSI) používat k přístupu k zařízení StorSimple.
3. Na začátku StorSimple ukládá data ve vrstvě SSD rychlé zařízení.
4. Jako vrstvy SSD přiblíží kapacitě, StorSimple deduplicates a komprimaci nejstarší datových bloků a přesunou do vrstvy HDD.
5. Jako přístupy kapacitu vrstvy HDD StorSimple šifruje nejstarší datových bloků a odešle je bezpečně k účtu úložiště Microsoft Azure prostřednictvím protokolu HTTPS.
6. Microsoft Azure vytvoří víc replik dat svého datového centra a ve vzdáleném datacentru, zajistíte, že můžete obnovit data, pokud dojde k havárii.
7. Pokud souborový server požádá o data uložená v cloudu, StorSimple vrátí ji bezproblémově a ukládá kopie ve vrstvě SSD zařízení StorSimple.

#### <a name="how-storsimple-manages-cloud-data"></a>Jak StorSimple spravuje data v cloudu

StorSimple deduplicates zákaznická data mezi všechny snímky a primárními daty (data zapsaná hostitelé). Při odstraňování duplicitních dat je skvělá pro efektivity úložiště, je otázka "Jaký je v cloudu" komplikovanější. Vrstvený primární data a data snímku překrývat mezi sebou. Jeden bloků dat v cloudu by se použil jako vrstvené primární data a také označit několik snímků. Každý cloudový snímek zajistí, že kopii všech dat v daném okamžiku je uzamčen do cloudu, dokud nebude tento snímek se odstraní.

Data se odstraní pouze z cloudu, pokud nejsou žádné odkazy na tato data. Například pokud jsme cloudový snímek všech dat, který je v zařízení StorSimple, pak odstraňte některá primární data, by vidíme _primární data_ vyřadit okamžitě. _Cloudová data_ což zahrnuje vrstvený dat a zálohování, zůstává stejný. Je to proto, že je snímek stále odkazuje data v cloudu. Po cloudu je odstranění snímku (a další snímek, který odkazuje stejná data), cloudové vyřazuje spotřeby. Před jsme odebrat data v cloudu, zkontrolujte jsme, že žádné snímky stále referenční data. Tento proces se nazývá _uvolňování paměti_ a na zařízení běží služba pozadí. Odebrání data v cloudu není okamžitý, protože služba kolekce paměti zkontroluje pro další odkazy na data před odstranění. Rychlost uvolňování paměti závisí na celkový počet snímků a celková data. Data v cloudu je většinou vyčištěna za méně než týden.


### <a name="thin-provisioning"></a>Dynamické zajišťování
Dynamické zajišťování je technologie virtualizace, ve kterém se zobrazuje dostupné úložiště delší než fyzické prostředky. StorSimple používá k přidělení právě dostatek místa, aby splňovala aktuální požadavky místo rezervování dostatečné úložiště předem, dynamické zajišťování. Elastické povaha cloudového úložiště usnadňuje tento přístup, protože StorSimple můžete zvýšit nebo snížit úložiště v cloudu splňovat požadavky na změny.

> [!NOTE]
> Místně vázaných svazků nezřídil dynamicky. Úložiště přidělené pouze místní svazek je zajištěna v celé jeho šíři při vytváření svazku.


### <a name="deduplication-and-compression"></a>Odstranění duplicitních dat a komprese
Microsoft Azure StorSimple používá komprese dat a odstranění duplicitních dat k dalšímu snížení požadavků na úložiště.

Odstranění duplicitních dat snižuje celkové množství dat uložených odstraněním redundance v sadě uložená data. Informace o změní, StorSimple ignoruje nezměněná data a zaznamená pouze změny. Kromě toho StorSimple snižuje množství uložených dat identifikace a odebráním nepotřebných informace. 

> [!NOTE]
> Není data na místně vázaných svazků s odstraněním duplicitních dat nebo komprimován. Však jsou zálohy místně vázaných svazků s odstraněním duplicitních dat a komprimované.


## <a name="storsimple-workload-summary"></a>Souhrn úloh StorSimple
Souhrnné informace o podporovaných úlohách StorSimple v následující tabulce.

| Scénář | Úloha | Podporováno | Omezení | Verze |
| --- | --- | --- | --- | --- |
| Spolupráce |Sdílení souborů |Ano | |Všechny verze |
| Spolupráce |Sdílení souborů DFS |Ano | |Všechny verze |
| Spolupráce |SharePoint |Ano* |Podporuje jenom s místně vázaných svazků |Update 2 nebo novější |
| Archivace |Archivace jednoduchého souboru |Ano | |Všechny verze |
| Virtualizace |Virtuální počítače |Ano* |Podporuje jenom s místně vázaných svazků |Update 2 nebo novější |
| Databáze |SQL |Ano* |Podporuje jenom s místně vázaných svazků |Update 2 nebo novější |
| Video sledováním. |Video sledováním. |Ano* |Podporované, když zařízení StorSimple je vyhrazený pouze pro tuto úlohu |Update 2 nebo novější |
| Backup |Primární cíl zálohování |Ano* |Podporované, když zařízení StorSimple je vyhrazený pouze pro tuto úlohu |Aktualizace 3 nebo novější |
| Backup |Sekundární cíl zálohování |Ano* |Podporované, když zařízení StorSimple je vyhrazený pouze pro tuto úlohu |Aktualizace 3 nebo novější |

*Ano&#42; – bude použito řešení pokyny a omezení.*

Tyto úlohy nejsou podporovány řadu zařízení StorSimple 8000. Pokud se nasadí na zařízení StorSimple, povede tato zatížení nepodporované konfigurace.

* Lékařské vytvoření bitové kopie
* Výměna
* VDI
* Oracle
* SAP
* Velký objem dat
* Distribuce obsahu
* Spuštění z rozhraní SCSI

Následuje seznam součásti infrastruktury StorSimple podporována.

| Scénář | Úloha | Podporováno | Omezení | Verze |
| --- | --- | --- | --- | --- |
| Obecné |ExpressRoute |Ano | |Všechny verze |
| Obecné |DataCore FC |Ano* |Podporované s DataCore SANsymphony |Všechny verze |
| Obecné |DFSR |Ano* |Podporuje jenom s místně vázaných svazků |Všechny verze |
| Obecné |Indexování |Ano* |Pro vrstvené svazky, je podporováno pouze metadata indexování (žádná data).<br>Pro místně vázaných svazků je podporováno dokončení indexování. |Všechny verze |
| Obecné |Ochrana proti virům |Ano* |Pro vrstvené svazky je podporováno pouze kontroly při otevření a zavřete.<br> Úplné prohledávání místně vázaných svazků, je podporováno. |Všechny verze |

*Ano&#42; – bude použito řešení pokyny a omezení.*

Následuje seznam další software, který slouží k vytvoření řešení s StorSimple.

| Typ úlohy | Software použít s StorSimple | Podporované verze|Odkaz na Průvodce řešením| 
| --- | --- | --- | --- |
| Cíl zálohy |Veeam |V Veeam 9 a novějším |[StorSimple jako cíl zálohování s Veaam](storsimple-configure-backup-target-veeam.md)|
| Cíl zálohy |Exec – zálohování této společnosti |Zálohování Exec 16 a novější |[StorSimple jako cíl zálohování pomocí zálohování Exec](storsimple-configure-backup-target-using-backup-exec.md)|
| Cíl zálohy |NetBackup této společnosti |NetBackup 7.7.x a novější  |[StorSimple jako cíl zálohování s NetBackup](storsimple-configure-backuptarget-netbackup.md)|
| Sdílení souborů globální <br></br> Spolupráce |Talon  |[StorSimple s Talon](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>Terminologie služby StorSimple
Před nasazením řešení Microsoft Azure StorSimple, doporučujeme, abyste si prošli následující termíny a definice.

### <a name="key-terms-and-definitions"></a>Klíče termíny a definice
| Termín (zkratka nebo zkratka) | Popis |
| --- | --- |
| záznam řízení přístupu (ACR) |Záznam přidružený svazek na vašem zařízení Microsoft Azure StorSimple, která určuje, které hostitele může připojit k němu. Určení je založeno na iSCSI kvalifikovaný název (IQN) hostitele (obsažené v ACR), kteří se připojují k zařízení StorSimple. |
| AES-256 |Algoritmus Advanced Encryption (Standard AES) 256 bitů pro šifrování dat při jejich přesunu do a z cloudu. |
| velikost alokační jednotky (Austrálie) |Nejmenší velikost místa na disku, který může být přidělen k uložení souboru v systému Windows. systémy souborů. Pokud velikost souboru není násobkem velikost clusteru, místo navíc je třeba zadat pro uložení souboru (až další více velikost clusteru) což vede ke ztrátě místa a fragmentace pevného disku. <br>Doporučené Austrálie pro svazky Azure StorSimple je 64 KB, protože ho pracuje s algoritmy pro odstranění duplicit. |
| automatizované úložiště vrstvení |Automaticky přesouvání míň aktivní dat z jednotky SSD na pevné disky a pak do vrstvy v cloudu a pak povolení správy všech úložiště z centrální uživatelské rozhraní. |
| zálohování katalogu |Kolekce zálohování, které jsou obvykle spojené typu aplikace, který byl použit. Tato kolekce se zobrazí v okně Zálohování katalogu služby StorSimple Manager zařízení uživatelského rozhraní. |
| soubor zálohy katalogu |Soubor obsahující seznam dostupných snímky, které jsou aktuálně uloženy v databázi zálohování služby StorSimple Snapshot Manager. |
| zásady zálohování |Výběr svazků, typ zálohy a časový plán, který umožňuje vytvářet zálohy dle předdefinovaného plánu. |
| binární rozsáhlé objekty (objekty BLOB) |Kolekce binární data uložená jako jedna entita v systému pro správu databáze. Objekty BLOB jsou obvykle bitové kopie, zvuk nebo jiné multimédií objekty, i když někdy binární kód spustitelný soubor je uložen jako objekt BLOB. |
| Challenge Handshake Authentication Protocol (CHAP) |Protokol použitý k ověření partnera připojení na základě jeho protějšek sdílení hesla a tajný klíč. CHAP mohou být jednosměrné nebo vzájemné. Cíl s jednosměrný CHAP, ověřuje iniciátor. Vzájemné CHAP vyžaduje, že cíl ověřování iniciátoru a že je iniciátor ověřuje cíl. |
| Klonování |Duplicitní kopie svazku. |
| Cloud jako vrstvu (CaaT) |Cloudového úložiště, které jsou integrovány jako vrstvu v architektuře úložiště tak, aby všechny úložiště se zobrazí jako součást jedné podnikové sítě úložiště. |
| poskytovatele cloudové služby (CSP) |Zprostředkovatel cloud computing služby. |
| cloudový snímek |V okamžiku kopii svazku data, která je uložená v cloudu. Cloudový snímek je stejná jako snímek replikují na jiný, odlehlého úložiště systému. Cloudové snímky jsou obzvláště užitečná ve scénářích zotavení po havárii. |
| šifrovací klíč cloudového úložiště |Heslo nebo klíč používá zařízení StorSimple k přístupu k zašifrovaným datům poslal zařízení do cloudu. |
| aktualizace pro clustery |Správa aktualizací softwaru na serverech v clusteru s podporou převzetí služeb při selhání tak, aby aktualizace minimální nebo žádný vliv na dostupnost služeb. |
| DataPath |Kolekce funkční jednotek, které provádějí operace propojených zpracování dat. |
| deaktivovat |Trvalé akce, která dělí připojení mezi zařízení StorSimple a související cloudové služby. Cloudové snímky zařízení zůstaly po tento proces a může být klonovat nebo použít pro zotavení po havárii. |
| zrcadlení disku |Replikace pro logické diskové svazky na samostatné pevné disky v reálném čase zajistit trvalou dostupnost. |
| zrcadlení dynamický disk |Replikace pro logické diskové svazky na dynamické disky. |
| dynamické disky |Formát svazek disku, který používá k ukládání a správě dat mezi několik fyzických disků Správce logických disků (LDM). Dynamické disky můžete poskytnout další volné místo zvětšit. |
| Rozšířené skříň Bunch disky (EBOD) |Sekundární skříň vašeho zařízení Microsoft Azure StorSimple, který obsahuje disky velmi pevného disku pro další úložiště. |
| FAT zřizování |Konvenční zajišťování úložiště v úložišti, které je přiděleno místo na základě očekává potřebám (a je obvykle mimo aktuální potřebě). Viz také *dynamické zajišťování*. |
| jednotku pevného disku (HDD) |Jednotku, která k ukládání dat používá několik rotační ploten. |
| hybridní cloudové úložiště |Architektura úložiště, který používá místní a odlehlého prostředků, včetně cloudového úložiště. |
| Internet Small Computer System Interface (iSCSI) |Úložišť založených na protokolu IP síťového standard pro propojení zařízení úložiště dat nebo zařízení. |
| Iniciátor iSCSI |Softwarová součást, která umožňuje počítači hostitele se systémem Windows pro připojení k síti externího úložiště na základě iSCSI. |
| iSCSI kvalifikovaný název IQN () |Jedinečný název, který identifikuje cíle iSCSI nebo iniciátor. |
| cíl iSCSI |Softwarová součást, která poskytuje centralizovanou iSCSI diskových podsystémů v SAN. |
| Live archivováním |O přístupu úložiště, ve kterém archivaci dat je přístupný vždy (nebude uložen mimo server na pásku, třeba). Microsoft Azure StorSimple používá archivace za provozu. |
| místně vázaný svazek |svazek, který se nachází na zařízení a to se nikdy vrstvené do cloudu. |
| místní snímek |V okamžiku kopii svazku data, která je uložená v Microsoft Azure StorSimple zařízení. |
| Microsoft Azure StorSimple |Výkonné řešení skládající se z datacenter úložiště zařízení a softwaru, který umožňuje IT organizacím využít cloudového úložiště, jako by šlo úložišti datacentra. StorSimple zjednodušuje ochrany dat a správu dat při současném snížení nákladů. Řešení dojde ke konsolidaci primární úložiště, archivace, zálohování a zotavení po havárii (DR) prostřednictvím bezproblémovou integraci s cloudem. Kombinací SAN úložiště a cloudové správy dat na platformě podnikových zařízení StorSimple povolit rychlosti, jednoduchost a spolehlivost pro všechny požadavky související s úložištěm. |
| Napájení a chlazení modulu (PCM) |Hardwarové součásti zařízení StorSimple skládající se z zdroje napájení a chladicí ventilátory, proto název napájení a chlazení modulu. Skříni primární zařízení má dva PCMs 764W, zatímco skříni EBOD má dva PCMs 580W. |
| primární skříň |Hlavní skříň zařízení StorSimple, obsahující řadiče platformy aplikace. |
| cíli času obnovení (RTO) |Maximální množství času, které by měl být použito před obchodní proces nebo systém úplné obnovení po havárii. |
| sériově připojené SCSI (SAS) |Typ jednotky pevného disku (HDD). |
| šifrovací klíč dat služby |Klíč k dispozici pro všechny nové zařízení StorSimple, která zaregistruje u služby StorSimple Manager zařízení. Konfiguračních dat přenesených mezi služby StorSimple Manager zařízení a zařízení je zašifrován pomocí veřejného klíče a pak mohou ho dešifrovat jenom na zařízení pomocí soukromého klíče. Šifrovací klíč dat služby umožňuje službě získat tento soukromý klíč pro dešifrování. |
| registrační klíč služby |Klíč, který pomáhá registrace zařízení StorSimple pomocí služby Správce zařízení StorSimple, takže se zobrazí na portálu Azure pro další akce správy. |
| Small Computer System Interface (SCSI) |Sada standardů pro fyzicky propojení počítačů a předávání dat mezi nimi. |
| jednotky SSD (SSD) |Disk, který neobsahuje žádné přesunutí části; například flash disk. |
| účet úložiště |Sada pověření pro přístup k propojený s vaším účtem úložiště pro daný cloud poskytovatele služeb. |
| StorSimple Adapter pro SharePoint |Komponenty Microsoft Azure StorSimple, která rozšiřuje transparentně StorSimple úložiště a ochranu dat na serverové farmy služby SharePoint. |
| Služba Správce zařízení StorSimple |Rozšíření na portálu Azure, která umožňuje spravovat Azure StorSimple místní a virtuální zařízení. |
| StorSimple Snapshot Manager |Modul Microsoft Management Console (MMC) snap-in pro správu operace zálohování a obnovení v Microsoft Azure StorSimple. |
| proveďte zálohování |Funkce, která umožňuje uživatelům provést zálohu interaktivní svazku. Jedná se o alternativní způsob pořízení ručního zálohování svazku oproti trvá automatické zálohování prostřednictvím definovanou zásadu. |
| dynamické zajišťování |Metoda optimalizace efektivitu, ke které se používá místo k dispozici v systémech úložiště. V dynamickém zřizování se úložiště je rozdělena mezi více uživatelů, které jsou založené na minimální požadované každý uživatel, v každém okamžiku místo. Viz také *fat zřizování*. |
| Vrstvení |Uspořádání dat v logické seskupení na základě aktuálního využití, stáří a vztahů s jinými daty. StorSimple automaticky uspořádá data na úrovních. |
| svazek |Oblasti logické úložiště uvedené ve formě jednotky. Svazky zařízení StorSimple odpovídají svazky připojené pro hostitele, včetně těch, které zjištěny prostřednictvím iSCSI a zařízení StorSimple. |
| kontejner svazků |Seskupení svazky a nastavení, které se vztahují k nim. Všechny svazky v zařízení StorSimple se seskupují do kontejnery svazků. Nastavení kontejneru svazku zahrnují účty úložiště, nastavení šifrování pro data odesílaná do cloudu s přidružených šifrovacích klíčů a šířku pásma využívat pro operace s cloudem. |
| svazek skupiny |V zařízení StorSimple Snapshot Manager skupinu svazek je kolekce svazků, které jsou nakonfigurované tak, aby se usnadnilo zálohování zpracování. |
| Služby Stínová kopie svazku (VSS) |Služba operačního systému Windows Server, která usnadňuje konzistence aplikací tím, že komunikaci s aplikací používající stínovou kopii svazku pro koordinaci vytváření přírůstkové snímků. Služby Stínová kopie svazku zajišťuje, aby aplikace byly dočasně neaktivní při pořizování snímků. |
| Prostředí Windows PowerShell pro StorSimple |Založených na prostředí Windows PowerShell rozhraní příkazového řádku používá pro provoz a správě zařízení StorSimple. Toto rozhraní při zachování některé základní funkce prostředí Windows PowerShell, má další vyhrazený rutin, které jsou s ohledem na správu zařízení StorSimple. |

## <a name="next-steps"></a>Další postup
Další informace o [zabezpečení zařízení StorSimple](storsimple-8000-security.md).

