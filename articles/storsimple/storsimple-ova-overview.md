---
title: Přehled Microsoft Azure StorSimple virtuálního pole
description: Popisuje virtuální pole StorSimple, integrované řešení úložiště, které spravuje úlohy úložiště mezi místním virtuálním polem a Microsoft Azureým cloudovým úložištěm.
author: alkohli
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 6d7d8c37d0e7ffacb3221a4f30a96ef6cccda819
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104674440"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Seznámení s virtuálním polem StorSimple

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Přehled

Microsoft Azure StorSimple Virtual Array je integrované řešení úložiště, které spravuje úlohy úložiště mezi místním virtuálním polem spuštěným v hypervisoru a Microsoft Azure cloudovém úložišti. Virtuální pole je efektivní, cenově výhodné a snadno spravované řešení souborového serveru nebo serveru iSCSI, které eliminuje mnohé problémy a výdaje spojené s podnikovým úložištěm a ochranou dat. Virtuální pole je zvlášť vhodné pro úložiště nečasto používaných archivačních dat.

Tento článek poskytuje přehled o virtuálním poli – tady jsou některé další zdroje informací:

* Osvědčené postupy najdete v tématu [osvědčené postupy pro virtuální pole StorSimple](storsimple-ova-best-practices.md).
* Přehled zařízení řady StorSimple 8000 najdete v části [StorSimple 8000 series: hybridní cloudové řešení](storsimple-overview.md).
* Informace o zařízeních řady StorSimple 5000/7000 najdete v [online nápovědě k StorSimple](http://onlinehelp.storsimple.com/).

Virtuální pole podporuje protokol iSCSI nebo protokol SMB (Server Message Block). Běží na vaší stávající infrastruktuře hypervisoru a poskytuje vrstvení do cloudu, zálohování v cloudu, rychlé obnovení, obnovení na úrovni položek a zotavení po havárii.

Následující tabulka shrnuje důležité funkce virtuálního pole StorSimple.

| Funkce | StorSimple Virtual Array |
| --- | --- |
| Požadavky na instalaci |Používá infrastrukturu virtualizace (Hyper-V nebo VMware). |
| Dostupnost |Jeden uzel |
| Celková kapacita (včetně cloudu) |Až 64 TB použitelné kapacity na virtuální pole |
| Místní kapacita |390 GB až 6,4 TB použitelné kapacity na virtuální pole (je potřeba zřídit 500 GB až 8 TB místa na disku) |
| Nativní protokoly |iSCSI nebo SMB |
| Plánovaná doba obnovení (RTO) |iSCSI: méně než 2 minuty bez ohledu na velikost |
| Cíl bodu obnovení (RPO) |Denní zálohy a zálohování na vyžádání |
| Vrstvení úložiště |Pomocí mapování tepla určí, jaká data mají být vrstvena nebo ven. |
| Podpora |Infrastruktura virtualizace podporovaná dodavatelem |
| Výkon |Liší se v závislosti na základní infrastruktuře. |
| Mobilita dat |Může obnovit stejné zařízení nebo provést obnovení na úrovni položek (souborový server). |
| Úrovně úložiště |Místní úložiště hypervisoru a Cloud |
| Velikost sdílené složky |Vrstvený: až 20 TB; místně připnuté: až 2 TB |
| Velikost svazku |Vrstvené: 500 GB až 5 TB; místně připnuté: 50 GB až 200 GB <br> Maximální místní rezervace pro vrstvené svazky je 200 GB. |
| Snímky |Konzistence na úrovni chybového ukončení |
| Obnovení na úrovni položek |Odpoví Uživatelé můžou obnovit ze sdílených složek. |

## <a name="why-use-storsimple"></a>Proč používat StorSimple?

StorSimple spojuje uživatele a servery s úložištěm Azure během několika minut bez úprav aplikace.

Následující tabulka popisuje některé klíčové výhody, které poskytuje řešení Virtual Array v StorSimple.

| Funkce | Výhoda |
| --- | --- |
| Transparentní integrace |Virtuální pole podporuje protokol iSCSI nebo protokol SMB. Pohyb dat mezi místní vrstvou a cloudovou vrstvou je pro uživatele hladce a transparentní. |
| Snížení nákladů na úložiště |Pomocí StorSimple zřídíte dostatečné místní úložiště, které bude plnit aktuální požadavky na nejčastěji používaná aktivní data. Jak úložiště potřebuje růst, StorSimple úrovně studených dat do nákladově efektivního cloudového úložiště. Data se před odesláním do cloudu ododstraňují a komprimují, aby se snížila nároky na úložiště a náklady. |
| Zjednodušená správa úložiště |StorSimple poskytuje centralizovanou správu v cloudu pomocí Správce zařízení StorSimple ke správě více zařízení. |
| Lepší zotavení po havárii a dodržování předpisů |StorSimple usnadňuje zotavení po havárii tím, že metadata okamžitě obnoví a v případě potřeby obnoví data. Běžné operace mohou pokračovat s minimálním přerušením. |
| Mobilita dat |Data vrstvená do cloudu jsou dostupná z jiných lokalit pro účely obnovení a migrace. Data můžete obnovit pouze v původním virtuálním poli. Funkce pro zotavení po havárii ale můžete použít k obnovení celého virtuálního pole do jiného virtuálního pole. |



## <a name="storsimple-workload-summary"></a>Souhrn úlohy StorSimple

Souhrn podporovaných úloh StorSimple je v tabulce.

|Scenario     |Úloha     |Podporováno      |Omezení               | Použitelné verze|
|-------------|-------------|---------------|---------------------------|--------------------|
|Vzdálená kancelář nebo pobočka (ROBO)  |Sdílení souborů     |Yes      |Zobrazení [maximálních limitů pro souborový server](storsimple-ova-limits.md).<br></br>[Podporované verze SMB najdete v tématu požadavky na systém](storsimple-ova-system-requirements.md).| Všechny verze     |
|Archivace v cloudu  |Sdílení souborů archivu     |Yes      |Zobrazení [maximálních limitů pro souborový server](storsimple-ova-limits.md).<br></br>[Podporované verze SMB najdete v tématu požadavky na systém](storsimple-ova-system-requirements.md).| Všechny verze     |

Virtuální pole StorSimple je nejvhodnější pro zřídka používaná data. I když má virtuální pole místní mezipaměť, aby zvýšila výkon, uživatelé by měli předpokládat, že soubory služeb zařízení jsou v nejnižší úrovni úložiště (Cloud). Každé virtuální pole může zapisovat a číst do úložiště Azure s přibližně 100 MB/s. Tento odkaz se sdílí mezi všemi požadavky přicházející do zařízení a může se stát kritickým bodem, jak je znázorněno na následujícím obrázku.

![Archivace v cloudu](./media/storsimple-ova-overview/cloud-archiving.png)

Když více souběžných uživatelů přistupuje k virtuálnímu poli, všichni sdílejí připojení k Azure, což vede k nižšímu výkonu. Na uživatele není Zaručený výkon a zařízení zpracovává jednotlivé požadavky při jejich doručení.

Virtuální pole StorSimple není vhodné pro úlohy, které vyžadují vysokou dostupnost. Virtuální pole je zařízení s jedním uzlem, při kterém dojde k výpadku při instalaci aktualizací softwaru. Správci by měli naplánovat časový interval pro správu a údržbu po dobu 30 minut 3-4 za rok.

## <a name="workflows"></a>Pracovní postupy

Virtuální pole StorSimple je zvlášť vhodné pro následující pracovní postupy:

* [Cloudová správa úložiště](#cloud-based-storage-management)
* [Zálohování nezávislé na umístění](#location-independent-backup)
* [Ochrana dat a zotavení po havárii](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Cloudová správa úložiště
Službu StorSimple Správce zařízení spuštěnou v Azure Portal můžete použít ke správě dat uložených na více zařízeních a ve více umístěních. To je zvláště užitečné ve scénářích distribuované větve. Pro správu virtuálních polí a fyzických zařízení StorSimple je nutné vytvořit samostatné instance služby StorSimple Správce zařízení. Virtuální pole teď používá nový Azure Portal místo na portálu Azure Classic.<!--Is the "now" element still in date? Could it go at this point? Just checking.-->

![cloudová správa úložiště](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Zálohování nezávislé na umístění
U virtuálních polí cloudové snímky poskytují nezávislou kopii svazku nebo sdílené složky v určitém umístění. Ve výchozím nastavení jsou povoleny cloudové snímky a nelze je zakázat. Všechny svazky a sdílené složky se zálohují současně pomocí jediné zásady každodenního zálohování a kdykoli to bude potřeba, můžete provést další zálohy ad hoc.

### <a name="data-protection-and-disaster-recovery"></a>Ochrana dat a zotavení po havárii
Virtuální pole podporuje následující scénáře ochrany dat a zotavení po havárii:

* **Obnovení svazku nebo sdílení** – k obnovení svazku nebo sdílení použijte pracovní postup obnovit jako nový. Pomocí tohoto postupu můžete obnovit celý svazek nebo sdílenou složku.
* **Obnovení na úrovni položek** – sdílené složky umožňují zjednodušený přístup k nejnovějším zálohám. Jednotlivé soubory můžete snadno obnovit ze speciální složky pro *zálohování* , která je dostupná v cloudu. Tato schopnost obnovení je řízená uživatelem a není nutná žádná administrativní zásah.
* **Zotavení po havárii** – pomocí možnosti převzetí služeb při selhání obnoví všechny svazky nebo sdílené složky na nové virtuální pole. Vytvoříte nové virtuální pole a zaregistrujete ho ve službě StorSimple Správce zařízení a pak převezmete původní virtuální pole. Nové virtuální pole pak bude předpokládat zřízené prostředky.

## <a name="storsimple-virtual-array-components"></a>Komponenty virtuálního pole StorSimple

Virtuální pole obsahuje následující komponenty:

* [Virtual Array](#virtual-array) – hybridní cloudové paměťové zařízení založené na virtuálním počítači zřízeném ve virtualizovaném prostředí nebo hypervisoru.
* [Služba StorSimple Správce zařízení](#storsimple-device-manager-service) – rozšíření Azure Portal, které vám umožní spravovat jedno nebo více zařízení StorSimple z jediného webového rozhraní, ke kterému můžete přistupovat z různých geografických umístění. Pomocí služby StorSimple Správce zařízení můžete vytvářet a spravovat služby, zobrazovat a spravovat zařízení a výstrahy a spravovat svazky, sdílené složky a stávající snímky.
* [Lokální webové uživatelské rozhraní](#local-web-user-interface) – webové uživatelské rozhraní, které se používá ke konfiguraci zařízení, aby se mohlo připojit k místní síti a pak zaregistrovat zařízení ve službě StorSimple Správce zařízení. 
* [Rozhraní příkazového řádku](#command-line-interface) – rozhraní Windows PowerShell, které můžete použít ke spuštění relace podpory ve virtuálním poli.
  V následujících částech jsou podrobněji popsány jednotlivé komponenty a vysvětluje, jak řešení uspořádává data, přiděluje úložiště a usnadňuje správu úložiště a ochranu dat.

### <a name="virtual-array"></a>Virtuální pole

Virtuální pole je řešení úložiště s jedním uzlem, které poskytuje primární úložiště, spravuje komunikaci s cloudovým úložištěm a pomáhá zajistit zabezpečení a důvěrnost všech dat uložených v zařízení.

Virtuální pole je dostupné v jednom modelu, který je k dispozici ke stažení. Virtuální pole má v zařízení maximální kapacitu 6,4 TB (s požadavkem na základní úložiště o velikosti 8 TB) a 64 TB, včetně cloudového úložiště.

Virtuální pole má následující funkce:

* Je nákladově efektivní. Využívá stávající infrastrukturu virtualizace a dá se nasadit na stávající hypervisor technologie Hyper-V nebo VMware.
* Je umístěný v datacentru a dá se nakonfigurovat jako server iSCSI nebo souborový server.
* Je integrována do cloudu.
* Zálohy se ukládají v cloudu, což může usnadnit zotavení po havárii a zjednodušit obnovení na úrovni položek (ILR).
* Ve virtuálním poli můžete použít aktualizace stejným způsobem, jako byste je použili u fyzického zařízení.

> [!NOTE]
> Virtuální pole nelze rozšířit. Proto je důležité zřídit adekvátní úložiště při vytváření virtuálního pole.

### <a name="storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple

Microsoft Azure StorSimple poskytuje webové uživatelské rozhraní, službu StorSimple Správce zařízení, která umožňuje centrálně spravovat úložiště StorSimple. Službu StorSimple Správce zařízení můžete použít k provádění následujících úloh:

* Umožňuje spravovat více virtuálních polí StorSimple z jedné služby.
* Konfigurace a Správa nastavení zabezpečení pro virtuální pole StorSimple. (Šifrování v cloudu závisí na rozhraních API Microsoft Azure.)
* Nakonfigurujte přihlašovací údaje a vlastnosti účtu úložiště.
* Nakonfigurujte a spravujte svazky nebo sdílené složky.
* Zálohujte a obnovte data na svazcích nebo sdílených složkách.
* Monitorujte výkon.
* Zkontrolujte nastavení systému a Identifikujte možné problémy.

Službu StorSimple Správce zařízení můžete použít ke každodenní správě virtuálního pole.

Další informace najdete v [části používání služby StorSimple Správce zařízení ke správě zařízení StorSimple](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Místní webové uživatelské rozhraní

Virtuální pole zahrnuje webové uživatelské rozhraní, které se používá pro jednorázovou konfiguraci a registraci zařízení ve službě StorSimple Správce zařízení. Můžete ji použít k vypnutí a restartování virtuálního pole, spuštění diagnostických testů, aktualizaci softwaru, změně hesla správce zařízení, zobrazení systémových protokolů a kontaktování podpora Microsoftu k zaznamenání žádosti o službu.

Další informace o používání webového uživatelského rozhraní najdete v [části použití webového uživatelského rozhraní pro správu StorSimple virtuálního pole](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Rozhraní příkazového řádku

Zahrnuté rozhraní Windows PowerShell umožňuje zahájit relaci podpory s podpora Microsoftu, aby vám mohla pomoct řešit problémy a řešit problémy, se kterými se můžete setkat ve virtuálním poli.

## <a name="storage-management-technologies"></a>Technologie správy úložiště

Kromě virtuálního pole a dalších komponent využívá řešení StorSimple následující softwarové technologie k zajištění rychlého přístupu k důležitým datům, snížení spotřeby úložiště a ochraně dat, která jsou uložená ve vašem virtuálním poli:

* [Automatické vrstvení úložiště](#automatic-storage-tiering) 
* [Místně připojené sdílené složky a svazky](#locally-pinned-shares-and-volumes)
* Odstranění duplicit a komprese dat vrstvených nebo zálohovaných do cloudu 
* [Plánované zálohy a zálohování na vyžádání](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Automatické vrstvení úložiště
Virtuální pole používá nový mechanismus vrstvení ke správě uložených dat napříč virtuálním polem a cloudem. K dispozici jsou jenom dvě úrovně: místní virtuální pole a cloudové úložiště Azure. Virtuální pole StorSimple automaticky uspořádává data do vrstev na základě Heat mapy, která sleduje aktuální využití, stáří a vztahy s ostatními daty. Data, která jsou aktivní (nejžhavějších), se ukládají místně, zatímco méně aktivní a neaktivní data se automaticky migrují do cloudu. (Všechny zálohy se ukládají v cloudu.) StorSimple upravuje a mění uspořádání dat a přiřazení úložiště při změně vzorců využití. Některé informace mohou být například méně aktivní v průběhu času. Vzhledem k tomu, že dojde k průběžnému menšímu množství aktivních vrstev, je na Cloud. Pokud se tato data znovu aktivují, bude se nacházet na úrovni pole úložiště.

Data pro konkrétní vrstvené sdílené složky nebo svazky zaručují vlastní prostor místní úrovně (přibližně 10 procent celkového zřízeného prostoru pro tuto sdílenou složku nebo svazek). I když se zmenší dostupné úložiště ve virtuálním poli pro tuto sdílenou složku nebo svazek, zajistíte tak, aby vrstvení pro jednu sdílenou složku nebo svazek neovlivnily požadavky na vrstvení dalších sdílených složek nebo svazků. To znamená, že velmi zaneprázdněné zatížení jedné sdílené složky nebo svazku nemůže vynutit všechny ostatní úlohy do cloudu.

Vrstvené svazky vytvořené pro iSCSI mají maximální místní rezervaci 200 GB, a to bez ohledu na velikost svazku.

![Automatické vrstvení úložiště](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Svazek můžete určit jako místně připnuté. v takovém případě data zůstanou ve virtuálním poli a nikdy se nevrství do cloudu. Další informace najdete v [místních připnutých sdílených složkách a svazcích](#locally-pinned-shares-and-volumes).

> [!IMPORTANT]
> Pokud používáte StorSimple, neprovádějte převod objektů blob do archivace, i když se vaše zařízení postupně vymění. Pokud chcete načíst data ze zařízení, budete muset znovu vyměnit objekty BLOB z archivace na horkou nebo studenou, což vede k významným nákladům.


### <a name="locally-pinned-shares-and-volumes"></a>Místně připojené sdílené složky a svazky

Můžete vytvořit vhodné sdílené složky a svazky jako místně připnuté. Tato možnost zajistí, aby data vyžadovaná důležitými aplikacemi zůstala ve virtuálním poli a nikdy se nevrstvena do cloudu. Místně připojené sdílené složky a svazky mají následující funkce:

* Nepodléhají latencím v cloudu ani problémům s připojením.
* Stále využívají StorSimple cloudové zálohování a funkce zotavení po havárii.

Místně připojenou sdílenou složku nebo svazek můžete obnovit jako vrstvené nebo vrstvené sdílené složky nebo svazky jako místně připnuté. 

Další informace o místně připnutém svazku najdete [v části používání služby StorSimple Správce zařízení ke správě svazků](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Odstranění duplicit a komprese dat vrstvených nebo zálohovaných do cloudu

StorSimple využívá odstranění duplicit a kompresi dat k dalšímu snížení požadavků na úložiště v cloudu. Odstranění duplicitních dat snižuje celkové množství uložených dat odstraněním redundance v uložené datové sadě. Když se změní informace, StorSimple ignoruje nezměněná data a zachytává jenom změny. StorSimple navíc snižuje množství uložených dat tím, že identifikuje a odstraní duplicitní informace.

> [!NOTE]
> Data uložená ve virtuálním poli nejsou Odstraněná a komprimovaná. Veškerá odstranění duplicit a komprese se projeví těsně před odesláním dat do cloudu.


### <a name="scheduled-and-on-demand-backups"></a>Plánované zálohy a zálohování na vyžádání

Funkce ochrany dat StorSimple umožňují vytvářet zálohy na vyžádání. Výchozí plán zálohování navíc zajišťuje, že se data zálohují denně. Zálohy jsou pořízeny ve formě přírůstkových snímků, které jsou uloženy v cloudu. Snímky, které zaznamenávají jenom změny od poslední zálohy, se dají vytvářet a obnovovat rychle. Tyto snímky můžou být kriticky důležité ve scénářích zotavení po havárii, protože nahrazují sekundární systémy úložiště (například zálohování na pásku) a umožňují v případě potřeby obnovovat data v datovém centru nebo v alternativních lokalitách.

## <a name="managing-personal-information"></a>Správa osobních údajů

StorSimple Správce zařízení pro virtuální řady shromažďuje osobní údaje ve dvou instancích klíče:
 - Výstraha nastavení uživatele, kde jsou konfigurovány e-mailové adresy uživatelů Tyto informace může správce vymazat. 
 - Uživatelé, kteří mají přístup k datům ve sdílených složkách. Zobrazí se seznam uživatelů, kteří mají přístup ke sdíleným datům, a mohou být exportováni. Tento seznam se odstraní, když se sdílená složka odstraní.

Další informace najdete v části [Zásady ochrany osobních údajů Microsoftu na webu Trust Center](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [připravit portál virtuálních polí](storsimple-virtual-array-deploy1-portal-prep.md).
