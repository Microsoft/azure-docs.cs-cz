---
title: Microsoft Azure StorSimple Virtual Array – přehled
description: Popisuje StorSimple Virtual Array, integrované úložné řešení, které spravuje úlohy úložiště mezi místním virtuálním polem a cloudovým úložištěm Microsoft Azure.
author: alkohli
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 32781a83aec996b23f161f5fe695f39a0de38685
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273872"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Úvod do virtuálního pole StorSimple

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Přehled

Virtuální pole Microsoft Azure StorSimple je integrované úložné řešení, které spravuje úlohy úložiště mezi místním virtuálním polem běžícím v hypervisoru a cloudovým úložištěm Microsoft Azure. Virtuální pole je efektivní, nákladově efektivní a snadno spravované řešení souborového serveru nebo serveru iSCSI, které eliminuje mnoho problémů a výdajů spojených s podnikovým úložištěm a ochranou dat. Virtuální pole je obzvláště vhodné pro ukládání zřídka používaných archivních dat.

Tento článek obsahuje přehled virtuálnípole - zde jsou některé další prostředky:

* Doporučené postupy naleznete v [tématu StorSimple Virtual Array best practices](storsimple-ova-best-practices.md).
* Přehled zařízení řady StorSimple 8000 najdete v [článku Řada StorSimple 8000: hybridní cloudové řešení](storsimple-overview.md).
* Informace o zařízeních řady StorSimple 5000/7000 naleznete v [online nápovědě StorSimple](http://onlinehelp.storsimple.com/).

Virtuální pole podporuje protokol SMB (ISCSI nebo Server Message Block). Běží na vaší stávající infrastruktuře hypervisoru a poskytuje vrstvení do cloudu, zálohování v cloudu, rychlé obnovení, obnovení na úrovni položek a funkce zotavení po havárii.

Následující tabulka shrnuje důležité funkce virtuálního pole StorSimple.

| Funkce | StorSimple Virtual Array |
| --- | --- |
| Požadavky na instalaci |Používá virtualizační infrastrukturu (Hyper-V nebo VMware) |
| Dostupnost |Jeden uzel |
| Celková kapacita (včetně cloudu) |Použitelná kapacita až 64 TB na virtuální pole |
| Místní kapacita |Použitelná kapacita 390 GB až 6,4 TB na virtuální pole (potřeba zřídit 500 GB až 8 TB místa na disku) |
| Nativní protokoly |iSCSI nebo SMB |
| Plánovaná doba obnovení (RTO) |iSCSI: méně než 2 minuty bez ohledu na velikost |
| Cíl bodu obnovení (RPO) |Denní zálohy a zálohy na vyžádání |
| Vrstvení úložiště |Používá mapování tepla k určení, která data by měla být vrstvena nebo ven |
| Podpora |Virtualizační infrastruktura podporovaná dodavatelem |
| Výkon |Liší se v závislosti na základní infrastruktuře |
| Mobilita dat |Lze obnovit na stejné zařízení nebo provést obnovení na úrovni položky (souborový server) |
| Vrstvy úložiště |Místní úložiště hypervisorů a cloud |
| Velikost sdílené složky |Stupňovaný: až 20 TB; lokálně připnuté: až 2 TB |
| Velikost svazku |Vrstvené: 500 GB až 5 TB; místně připnutý: 50 GB až 200 GB <br> Maximální místní rezervace pro vrstvené svazky je 200 GB. |
| Snímky |Konzistence na úrovni chybového ukončení |
| Obnovení na úrovni položek |Ano, je to tak. uživatelé mohou obnovit ze sdílených složek |

## <a name="why-use-storsimple"></a>Proč použít StorSimple?

StorSimple připojí uživatele a servery k úložišti Azure během několika minut bez úprav y aplikací.

Následující tabulka popisuje některé klíčové výhody, které poskytuje řešení StorSimple Virtual Array.

| Funkce | Výhoda |
| --- | --- |
| Transparentní integrace |Virtuální pole podporuje protokol iSCSI nebo SMB. Přesun dat mezi místní vrstvou a cloudovou vrstvou je pro uživatele bezproblémový a transparentní. |
| Nižší náklady na úložiště |S StorSimple zřídíte dostatečné místní úložiště, aby splňovaly aktuální požadavky na nejpoužívanější horká data. S růstem potřeb úložiště vrstvy StorSimple zastudená data do nákladově efektivní cloudové úložiště. Data jsou před odesláním do cloudu deduplikována a komprimována, aby se dále snížily požadavky na úložiště a náklady. |
| Zjednodušená správa úložiště |StorSimple poskytuje centralizovanou správu v cloudu pomocí Správce zařízení StorSimple pro správu více zařízení. |
| Lepší zotavení po havárii a dodržování předpisů |StorSimple usnadňuje rychlejší zotavení po havárii obnovením metadat okamžitě a obnovení dat podle potřeby. To znamená, že normální operace mohou pokračovat s minimálním narušením. |
| Mobilita dat |Data vrstvená do cloudu jsou přístupná z jiných webů pro účely obnovení a migrace. Všimněte si, že můžete obnovit data pouze do původního virtuálního pole. Funkce zotavení po havárii však použít k obnovení celého virtuálního pole do jiného virtuálního pole. |

## <a name="storsimple-workload-summary"></a>Souhrn úlohy StorSimple

Souhrn podporovaných úloh StorSimple je uveden v tabulce níže.

|Scénář     |Úloha     |Podporuje se      |Omezení               | Použitelné verze|
|-------------|-------------|---------------|---------------------------|--------------------|
|Vzdálená kancelář/pobočka (ROBO)  |Sdílení souborů     |Ano      |Viz [maximální limity pro souborový server](storsimple-ova-limits.md).<br></br>Viz [systémové požadavky pro podporované verze SMB](storsimple-ova-system-requirements.md).| Všechny verze     |
|Archivace cloudu  |Archivní sdílení souborů     |Ano      |Viz [maximální limity pro souborový server](storsimple-ova-limits.md).<br></br>Viz [systémové požadavky pro podporované verze SMB](storsimple-ova-system-requirements.md).| Všechny verze     |

Virtuální pole StorSimple je nejvhodnější pro zřídka přístupná data. Zatímco virtuální pole má místní mezipaměť pro zvýšení výkonu, uživatelé by měli předpokládat, že soubory služeb zařízení na nejnižší úrovni úložiště (cloud). Každé virtuální pole může zapisovat a číst do úložiště Azure rychlostí přibližně 100 Mb/s. Toto propojení je sdíleno mezi všemi požadavky přicházejícími do zařízení a může se stát kritickým bodem, jak je znázorněno na obrázku níže.

![Archivace cloudu](./media/storsimple-ova-overview/cloud-archiving.png)

Když k virtuálnímu poli přistupuje více souběžných uživatelů, všichni sdílejí připojení k Azure, což vede k nižšímu výkonu. Neexistuje žádný zaručený výkon na uživatele a zařízení zpracovává jednotlivé požadavky při jejich doručení.

StorSimple Virtual Array není vhodný pro úlohy, které vyžadují vysokou dostupnost. Virtuální pole je jednouzelové zařízení, které při instalaci aktualizací softwaru dochází k prostojům. Správci by měli plánovat pro údržbu okno 30 minut 3-4 krát za rok.

## <a name="workflows"></a>Pracovní postupy

Virtuální pole StorSimple je vhodné zejména pro následující pracovní postupy:

* [Správa cloudových úložišť](#cloud-based-storage-management)
* [Zálohování nezávislé na poloze](#location-independent-backup)
* [Ochrana dat a zotavení po havárii](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Správa cloudových úložišť
Pomocí služby StorSimple Device Manager spuštěné na webu Azure Portal můžete spravovat data uložená na více zařízeních a na více místech. To je užitečné zejména v distribuovaných větvích scénáře. Všimněte si, že je nutné vytvořit samostatné instance služby StorSimple Device Manager pro správu virtuálních polí a fyzických zařízení StorSimple. Všimněte si také, že virtuální pole teď používá nový portál Azure namísto klasického portálu Azure.

![cloudová správa úložišť](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Zálohování nezávislé na poloze
S virtuální pole cloudové snímky poskytují nezávislé na umístění, bod v čase kopii svazku nebo sdílené složky. Snímky cloudu jsou ve výchozím nastavení povoleny a nelze je zakázat. Všechny svazky a sdílené složky jsou zálohovány současně prostřednictvím jedné denní zásady zálohování a můžete provést další zálohy ad hoc, kdykoli je to nutné.

### <a name="data-protection-and-disaster-recovery"></a>Ochrana dat a zotavení po havárii
Virtuální pole podporuje následující scénáře ochrany dat a zotavení po havárii:

* **Obnovení svazku nebo sdílené položky** – Obnovení použijte jako nový pracovní postup k obnovení svazku nebo sdílené položky. Tento přístup slouží k obnovení celého svazku nebo sdílené složky.
* **Obnovení na úrovni položky** – sdílené složky umožňují zjednodušený přístup k nedávným zálohám. Můžete snadno obnovit jednotlivé soubory ze speciální složky *.backup,* která je k dispozici v cloudu. Tato možnost obnovení je řízena uživatelem a není vyžadován žádný zásah správce.
* **Zotavení po havárii** – pomocí funkce převzetí služeb při selhání obnovit všechny svazky nebo sdílené složky do nového virtuálního pole. Vytvoříte nové virtuální pole a zaregistrujete jej pomocí služby StorSimple Device Manager a pak převezmete převzetí služeb při selhání původního virtuálního pole. Nové virtuální pole pak převezme zřízené prostředky.

## <a name="storsimple-virtual-array-components"></a>Komponenty virtuálního pole StorSimple

Virtuální pole obsahuje následující součásti:

* [Virtuální pole](#virtual-array) – hybridní cloudové úložné zařízení založené na virtuálním počítači zřízeném ve virtualizovaném prostředí nebo hypervisoru.
* [Služba StorSimple Device Manager](#storsimple-device-manager-service) – rozšíření portálu Azure, které umožňuje spravovat jedno nebo více zařízení StorSimple z jednoho webového rozhraní, ke kterému máte přístup z různých geografických umístění. Pomocí služby StorSimple Device Manager můžete vytvářet a spravovat služby, zobrazovat a spravovat zařízení a výstrahy a spravovat svazky, sdílené složky a existující snímky.
* [Místní webové uživatelské rozhraní](#local-web-user-interface) – webové uživatelské rozhraní, které slouží ke konfiguraci zařízení tak, aby se mohlo připojit k místní síti, a potom zaregistrovat zařízení pomocí služby StorSimple Device Manager. 
* [Rozhraní příkazového řádku](#command-line-interface) – rozhraní prostředí Windows PowerShell, které můžete použít ke spuštění relace podpory ve virtuálním poli.
  Následující části popisují každou z těchto součástí podrobněji a vysvětlují, jak řešení uspořádá data, přiděluje úložiště a usnadňuje správu úložiště a ochranu dat.

### <a name="virtual-array"></a>Virtuální pole

Virtuální pole je řešení úložiště s jedním uzly, které poskytuje primární úložiště, spravuje komunikaci s cloudovým úložištěm a pomáhá zajistit zabezpečení a důvěrnost všech dat uložených v zařízení.

Virtuální pole je k dispozici v jednom modelu, který je k dispozici ke stažení. Virtuální pole má maximální kapacitu 6,4 TB na zařízení (s základním požadavkem na úložiště 8 TB) a 64 TB včetně cloudového úložiště.

Virtuální pole má následující funkce:

* Je to nákladově efektivní. Využívá stávající virtualizační infrastrukturu a může být nasazen na stávající hypervisor Hyper-V nebo VMware.
* Je umístěn v datovém centru a může být nakonfigurován jako server iSCSI nebo souborový server.
* Je integrován s cloudem.
* Zálohy jsou uloženy v cloudu, což může usnadnit zotavení po havárii a zjednodušit obnovení na úrovni položky (ILR).
* Můžete použít aktualizace virtuální pole, stejně jako byste je použít na fyzické zařízení.

> [!NOTE]
> Virtuální pole nelze rozbalit. Proto je důležité zřídit odpovídající úložiště při vytváření virtuálnípole.

### <a name="storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple

Microsoft Azure StorSimple poskytuje webové uživatelské rozhraní, storsimple správce zařízení služby, která umožňuje centrálně spravovat úložiště StorSimple. Pomocí služby StorSimple Device Manager můžete provádět následující úkoly:

* Spravujte více virtuálních polí StorSimple z jedné služby.
* Konfigurace a správa nastavení zabezpečení pro virtuální pole StorSimple. (Šifrování v cloudu je závislé na rozhraních API Microsoft Azure.)
* Nakonfigurujte pověření a vlastnosti účtu úložiště.
* Konfigurace a správa svazků nebo sdílených složek.
* Zálohovat a obnovovat data na svazcích nebo sdílených sporecích.
* Sledujte výkon.
* Zkontrolujte nastavení systému a identifikujte možné problémy.

Službu StorSimple Device Manager slouží k provádění každodenní správy virtuálního pole.

Další informace naleznete v [části Správa zařízení StorSimple pomocí služby StorSimple Device Manager](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Místní webové uživatelské rozhraní

Virtuální pole obsahuje webové uživatelské rozhraní, které se používá pro jednorázovou konfiguraci a registraci zařízení pomocí služby StorSimple Device Manager. Můžete ji použít k vypnutí a restartování virtuálního pole, spuštění diagnostických testů, aktualizace softwaru, změny hesla správce zařízení, zobrazení systémových protokolů a kontaktování podpory společnosti Microsoft a podání žádosti o službu.

Informace o používání webového uživatelského prostředí naleznete v [části Správa virtuálního pole StorSimple pomocí webového uživatelského](storsimple-ova-web-ui-admin.md)prostředí .

### <a name="command-line-interface"></a>Rozhraní příkazového řádku

Zahrnuté rozhraní prostředí Windows PowerShell umožňuje zahájit relaci podpory s podporou společnosti Microsoft, aby vám pomohly vyřešit a vyřešit problémy, se kterými se můžete setkat ve virtuálním poli.

## <a name="storage-management-technologies"></a>Technologie správy úložišť

Kromě virtuálního pole a dalších komponent používá řešení StorSimple následující softwarové technologie, které poskytují rychlý přístup k důležitým datům, snižují spotřebu úložiště a chrání data uložená ve virtuálním poli:

* [Automatické vrstvení úložiště](#automatic-storage-tiering) 
* [Místně připnuté sdílené složky a svazky](#locally-pinned-shares-and-volumes)
* Odstranění duplicit a komprese pro data vrstvená nebo zálohovaná do cloudu 
* [Plánované zálohy a zálohy na vyžádání](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Automatické vrstvení úložiště
Virtuální pole používá nový mechanismus vrstvení ke správě uložených dat napříč virtuálním polem a cloudem. Existují pouze dvě vrstvy: místní virtuální pole a cloudové úložiště Azure. Virtuální pole StorSimple automaticky uspořádá data do úrovní na základě tepelné mapy, která sleduje aktuální využití, stáří a vztahy s jinými daty. Data, která jsou nejaktivnější (nejžhavější), se ukládají místně, zatímco méně aktivní a neaktivní data se automaticky migrují do cloudu. (Všechny zálohy jsou uloženy v cloudu.) StorSimple upravuje a mění uspořádání dat a přiřazení úložiště při změně vzorců použití. Některé informace mohou být například v průběhu času méně aktivní. Jak se stává postupně méně aktivní, je vrstvené do cloudu. Pokud se stejná data znovu stanou aktivními, budou vrstvena do pole úložiště.

Data pro konkrétní vrstvené sdílené složky nebo svazku je zaručena jeho vlastní místní vrstvy prostoru (přibližně 10 % z celkového zřízeného prostoru pro tuto sdílenou složku nebo svazek). I když to snižuje dostupné úložiště na virtuální pole pro tuto sdílenou složku nebo svazek, zajišťuje, že vrstvení pro jednu sdílenou složku nebo svazek nebude ovlivněno potřebami vrstvení jiných sdílených složek nebo svazků. Proto velmi zaneprázdněné úlohy na jedné sdílené složce nebo svazku nemůže vynutit všechny ostatní úlohy do cloudu.

Vrstvené svazky vytvořené pro iSCSI mají maximální místní rezervaci 200 GB bez ohledu na velikost svazku.

![automatické vrstvení úložiště](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Můžete zadat svazek jako místně připnutý, v takovém případě data zůstanou ve virtuálním poli a nikdy se nevrství do cloudu. Další informace naleznete v [místních připnutých sdílených složek a svazcích](#locally-pinned-shares-and-volumes).


### <a name="locally-pinned-shares-and-volumes"></a>Místně připnuté sdílené složky a svazky

Můžete vytvořit příslušné sdílené složky a svazky jako místně připnuté. Tato funkce zajišťuje, že data vyžadovaná kritickými aplikacemi zůstanou ve virtuálním poli a nikdy nebudou vrstvena do cloudu. Místně připnuté sdílené složky a svazky mají následující funkce:

* Nepodléhají cloudovým latencím nebo problémům s připojením.
* Stále těží z funkce zálohování v cloudu StorSimple a funkce zotavení po havárii.

Místně připnutou sdílenou složku nebo svazek můžete obnovit jako vrstvenou nebo vrstvenou sdílenou složku nebo svazek jako místně připnutou. 

Další informace o místně vázaných svazcích naleznete v [části Správa svazků pomocí služby StorSimple Device Manager](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Odstranění duplicit a komprese pro data vrstvená nebo zálohovaná do cloudu

StorSimple používá deduplikaci a kompresi dat k dalšímu snížení požadavků na úložiště v cloudu. Odstraněním redundance v uložené datové sadě snižuje odstraněním redundance v uložené datové sadě odstraněním duplicitní funkce. Jako změny informací StorSimple ignoruje nezměněná data a zachycuje pouze změny. Kromě toho StorSimple snižuje množství uložených dat identifikací a odebráním duplicitních informací.

> [!NOTE]
> Data uložená ve virtuálním poli nejsou deduplikována ani komprimována. Všechny deduplikace a komprese dochází těsně před data je odeslána do cloudu.

### <a name="scheduled-and-on-demand-backups"></a>Plánované zálohy a zálohy na vyžádání

Funkce ochrany dat StorSimple umožňují vytvářet zálohy na vyžádání. Výchozí plán zálohování navíc zajišťuje, že data jsou zálohována denně. Zálohy jsou prováděny ve formě přírůstkové snímky, které jsou uloženy v cloudu. Snímky, které zaznamenávají pouze změny od poslední zálohy, lze rychle vytvořit a obnovit. Tyto snímky mohou být kriticky důležité ve scénářích zotavení po havárii, protože nahrazují systémy sekundárního úložiště (například zálohování na pásku) a umožňují v případě potřeby obnovit data do datového centra nebo do alternativních lokalit.

## <a name="managing-personal-information"></a>Správa osobních údajů

Správce zařízení StorSimple pro virtuální řadu shromažďuje osobní údaje ve dvou klíčových případech:
 - Upozorněte nastavení uživatele, kde jsou konfigurovány e-mailové adresy uživatelů. Tyto informace mohou být vymazány správcem. 
 - Uživatelé, kteří mají přístup k datům s bydlištěm ve sdílených složek. Zobrazí se seznam uživatelů, kteří mají přístup k datům sdílené složky, a lze jej exportovat. Tento seznam je také odstraněn při odstranění sdílených složek.

Další informace naleznete v [zásadách ochrany osobních údajů společnosti Microsoft v Centru zabezpečení](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [připravit portál virtuálního pole](storsimple-virtual-array-deploy1-portal-prep.md).
