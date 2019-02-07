---
title: Přehled Microsoft Azure StorSimple Virtual Array | Dokumentace Microsoftu
description: Popisuje StorSimple Virtual Array integrované řešení úložiště, které spravuje úlohy úložiště mezi virtuální pole v místním a cloudovým úložištěm Microsoft Azure.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/02/2018
ms.author: alkohli
ms.openlocfilehash: a7aace89ad4323d6fad6942fe03c4240fb53c8fd
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811192"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Úvod do služby StorSimple Virtual Array

## <a name="overview"></a>Přehled

Microsoft Azure StorSimple Virtual Array je integrované řešení úložiště, které spravuje úlohy úložiště mezi v místním virtuálním polem spuštěným v hypervizoru a cloudovým úložištěm Microsoft Azure. Virtuální pole je efektivní, cenově efektivní a snadno spravovatelné souborového serveru nebo iSCSI serverové řešení, které předchází problémům a náklady spojené s podnikové úložiště a ochranu dat. Virtuální pole je zvlášť vhodné pro ukládání zřídka využívaných dat archivace.

Tento článek obsahuje přehled virtuální pole – zde jsou některé další prostředky:

* Osvědčené postupy, najdete v části [osvědčené postupy pro StorSimple Virtual Array](storsimple-ova-best-practices.md).
* Přehled zařízení řady StorSimple 8000, přejděte na [řady StorSimple 8000 series: řešení hybridního cloudového](storsimple-overview.md).
* Informace o zařízení řady StorSimple 5000/7000 najdete v části [StorSimple Online nápověda](http://onlinehelp.storsimple.com/).

Virtuální pole podporuje iSCSI nebo protokol zprávy bloku SMB (Server). Běží na vaší stávající infrastruktury hypervisoru a poskytuje ovládání datových vrstev do cloudu, zálohování do cloudu, rychlé obnovení, obnovení na úrovni položky a funkce pro zotavení po havárii.

Následující tabulka shrnuje důležité funkce StorSimple Virtual Array.

| Funkce | StorSimple Virtual Array |
| --- | --- |
| Požadavky na instalaci |Používá infrastrukturu virtualizace (Hyper-V nebo VMware) |
| Dostupnost |Jeden uzel |
| Celková kapacita (včetně cloud) |Až 64 TB využitelné kapacity jedno virtuální pole |
| Místní kapacita |390 GB až 6.4 TB využitelné kapacity jedno virtuální pole (třeba zřídit 500 GB až 8 TB místa na disku) |
| Nativní protokoly |iSCSI nebo podepisování SMB |
| Plánovaná doba obnovení (RTO) |iSCSI: bez ohledu na velikost menší než 2 minuty |
| Cíl bodu obnovení (RPO) |Denní zálohy a zálohy na vyžádání |
| Ovládání datových vrstev úložiště |Zahřívá se používá mapování a určí, jaká data by měla být rozvrstvena snížení nebo navýšení kapacity |
| Podpora |Infrastruktury virtualizace, které jsou podporované od dodavatele |
| Výkon |Se liší v závislosti na základní infrastrukturu |
| Mobilita dat |Můžete obnovit do stejného zařízení nebo položky obnovení na úrovni (souborový server) |
| Vrstvy úložiště |Úložiště na místním hypervisoru a cloud |
| Velikost sdílené složky |Vrstvené: až 20 TB; místně připnuté: až 2 TB |
| Velikost svazku |Vrstvené: 500 GB až 5 TB; místně připnuté: 50 GB až 200 GB <br> Maximální místní rezervace pro vrstvených svazků je 200 GB. |
| Snímky |Konzistentní s havárií |
| Obnovení na úrovni položky |Ano. Uživatelé mohou obnovit ze sdílených složek |

## <a name="why-use-storsimple"></a>Proč používat službu StorSimple?

StorSimple připojí uživatelů a serverů do služby Azure storage během několika minut a žádné změny aplikace.

Následující tabulka popisuje některé z klíčových výhod, které poskytuje řešení StorSimple Virtual Array.

| Funkce | Výhoda |
| --- | --- |
| Transparentní integrace |Virtuální pole podporuje iSCSI nebo protokol SMB. Přesun dat mezi místní a cloudovou vrstvu je bezproblémové a pro uživatele transparentní. |
| Náklady na úložiště nižší |Se StorSimple zřídit dostatečné místní úložiště s cílem splnit aktuální požadavky pro nejpoužívanějších horká data. Jak rostou potřeby úložiště, StorSimple úrovně studených dat do nákladově efektivní cloudové úložiště. Data jsou s odstraněním duplicitních dat a komprimované před odesláním do cloudu chcete dál snížit požadavky na úložiště a náklady. |
| Jednodušší správu úložišť |StorSimple nabízí centralizovanou správu v cloudu pomocí Správce zařízení StorSimple ke správě více zařízení. |
| Zlepšení zotavení po havárii a dodržování předpisů |StorSimple usnadňuje rychlejší zotavení po havárii tím, že metadata okamžitě obnovení a obnovení dat podle potřeby. To znamená, že můžete pokračovat normální provoz s minimálním dopadem. |
| Mobilita dat |Data Vrstvená do cloudu je přístupná z jiných webů pro účely obnovení a migrace. Všimněte si, že data můžete obnovit pouze na původní virtuální pole. Funkce zotavení po havárii však použít k obnovení celého virtuálního pole na jiné virtuální pole. |

## <a name="storsimple-workload-summary"></a>Souhrn úloh StorSimple

Přehled podporovaných úloh StorSimple v následující tabulce.

|Scénář     |Úloha     |Podporováno      |Omezení               |
|-------------|-------------|---------------|---------------------------|
|Vzdálené kanceláře či firemní Office (ROBO)  |Sdílení souborů     |Ano      |Zobrazit [maximálními limity pro souborový server se](storsimple-ova-limits.md).<br></br>Zobrazit [požadavky na systém pro podporované verze protokolu SMB](storsimple-ova-system-requirements.md).| Všechny verze     |
|Archivace v cloudu  |Archivní soubor pro sdílení obsahu     |Ano      |Zobrazit [maximálními limity pro souborový server se](storsimple-ova-limits.md).<br></br>Zobrazit [požadavky na systém pro podporované verze protokolu SMB](storsimple-ova-system-requirements.md).| Všechny verze     |

StorSimple Virtual Array je nejvhodnější pro zřídka používaná data. Virtuální pole obsahuje místní mezipaměti pro zvýšení výkonu, uživatelé by měl předpokládat, že zařízení služby soubory na nejnižší úrovni úložiště (cloud). Jednotlivá virtuální pole může zapisovat a číst do služby Azure storage na přibližně 100 MB/s. Tento odkaz se sdílí napříč všemi požadavky přicházející do zařízení a jak je znázorněno v následujícím diagramu se může stát kritickým bodem.

![Archivace v cloudu](./media/storsimple-ova-overview/cloud-archiving.png)

V případě více souběžným uživatelům přístup k virtuální pole, ale všechny sdílet připojení k Azure, což nižší výkon. Neexistuje žádné zaručené výkonu na uživatele a zařízení zpracovává jednotlivé požadavky při jejich doručení.

StorSimple Virtual Array není vhodný pro úlohy, které vyžadují vysokou dostupnost. Virtuální pole se zařízením s jedním uzlem, dojde k výpadku při instalaci aktualizací softwaru. Správci by měl plán pro okno údržby 30 minut 3 - 4 časy za rok.

## <a name="workflows"></a>Pracovní postupy

StorSimple Virtual Array je obzvláště vhodný pro následující pracovní postupy:

* [Správa cloudové úložiště](#cloud-based-storage-management)
* [Zálohování bez ohledu na umístění](#location-independent-backup)
* [Data ochrana a zotavení po havárii](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Správa cloudové úložiště
Služba Správce zařízení StorSimple spuštěná na webu Azure Portal můžete použít ke správě dat uložených na několika zařízeních a v několika umístěních. To je zvlášť užitečné v situacích distribuovaných větve. Všimněte si, že musíte vytvořit samostatné instance služby Správce zařízení StorSimple můžete spravovat virtuální pole a fyzickým zařízením StorSimple. Všimněte si také, že virtuální pole na novém portálu Azure nyní používá místo na portálu Azure classic.

![Správa cloudové úložiště](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Zálohování bez ohledu na umístění
Cloudové snímky poskytovat virtuální pole, bez ohledu na umístění, v okamžiku kopie svazku nebo sdílené složky. Cloudové snímky jsou ve výchozím nastavení povolené a nejde zakázat. Všech svazků a sdílených složek jsou v době prostřednictvím jedné zásady pro denní zálohování, zálohování a můžete provést další ad-hoc zálohy, kdykoli je to zapotřebí.

### <a name="data-protection-and-disaster-recovery"></a>Data ochrana a zotavení po havárii
Virtuální pole podporuje následující ochranu dat a scénáře zotavení po havárii:

* **Obnovit svazek nebo sdílenou složku** – použít obnovení jako nový pracovní postup obnovit svazek nebo sdílenou složku. Pomocí tohoto postupu můžete obnovit celý svazek nebo sdílenou složku.
* **Položky obnovení na úrovni** – povolit zjednodušený přístup ke nedávných záloh sdílených složek. Je možné snadno obnovit soubor z speciální *.backup* složky v cloudu. Tato možnost obnovení je řízené uživatele a není potřeba žádný zásah správce.
* **Zotavení po havárii** – obnovit všechny svazky nebo sdílené složky do nového virtuálního pole pomocí funkce převzetí služeb při selhání. Vytvořit nové virtuální pole a zaregistrovat ho ve službě Správce zařízení StorSimple a potom převzetí služeb při selhání původní virtuální pole. Nový virtuální pole pak převezme zřízené prostředky.

## <a name="storsimple-virtual-array-components"></a>StorSimple Virtual Array komponenty

Virtuální pole zahrnuje následující součásti:

* [Virtuální pole](#virtual-array) – hybridní cloud úložné zařízení podle virtuálních počítačů zřízené v hypervizoru nebo virtualizovaném prostředí.
* [Služba Správce zařízení StorSimple](#storsimple-device-manager-service) – což je rozšíření na webu Azure portal, který vám umožní spravovat jedno nebo více zařízení StorSimple z jediného webového rozhraní, které se dá dostat z různých geografických umístěních. Služba Správce zařízení StorSimple můžete vytvářet a spravovat služby, zobrazit a spravovat zařízení a upozornění a správa svazků, sdílených složek a existující snímky.
* [Místní webové uživatelské rozhraní](#local-web-user-interface) – uživatelské rozhraní založené na web, umožňující nakonfigurovat zařízení tak, aby ho připojit k místní síti a pak zařízení zaregistrovat do služby Správce zařízení StorSimple. 
* [Rozhraní příkazového řádku](#command-line-interface) – A Windows PowerShell rozhraní, které vám umožní spustit relaci ve virtuálním poli.
  Následující části popisují každou z těchto součástí podrobněji a popisují, jak toto řešení uspořádá data, přidělí úložiště a usnadňuje správu úložiště a ochranu dat.

### <a name="virtual-array"></a>Virtuální pole

Virtuální pole je jedním uzlem úložiště řešení, které poskytuje primární úložiště, spravuje komunikace s cloudovým úložištěm a pomáhá zajistit zabezpečení a důvěrnost všechna data, která je uložena v zařízení.

Virtuální pole je k dispozici v jednom modelu, který je k dispozici ke stažení. Virtuální pole má maximální kapacitu 6.4 TB na zařízení (s podkladový požadavek úložiště o velikosti 8 TB) a 64 TB včetně cloudového úložiště.

Virtuální pole má následující funkce:

* To je nákladově efektivní. To usnadňuje použití existující infrastruktury virtualizace a je možné nasadit na existující hypervisoru Hyper-V nebo VMware.
* Se nachází v datovém centru a může být nakonfigurován jako serveru iSCSI nebo souborovém serveru.
* Je integrovaná s cloudem.
* Zálohy jsou uložené v cloudu, který může usnadnit zotavení po havárii a zjednodušit obnovení na úrovni položek (ILR).
* Aktualizace můžete použít pro virtuální pole, stejně jako by použít je u fyzických zařízení.

> [!NOTE]
> Virtuální pole nelze rozšířit. Proto je potřeba zřídit dostatečné úložiště při vytváření virtuálního pole.

### <a name="storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple

Microsoft Azure StorSimple poskytuje webové uživatelské rozhraní, služby Správce zařízení StorSimple, která umožňuje centrálně spravovat úložiště StorSimple. Služba Správce zařízení StorSimple můžete provádět následující úlohy:

* Správa více virtuálních polí StorSimple z jedné služby.
* Konfigurovat a spravovat nastavení zabezpečení pro virtuální pole StorSimple. (Šifrování v cloudu je závislá na rozhraní API Microsoft Azure.)
* Konfigurace vlastností a přihlašovací údaje účtu úložiště.
* Konfigurace a správa svazků nebo sdílených složek.
* Zálohování a obnovení data na svazky nebo sdílené složky.
* Sledování výkonu.
* Zkontrolujte nastavení systému a identifikovat možné problémy.

Provádět každodenní správu vaše virtuální pole pomocí služby Správce zařízení StorSimple.

Další informace najdete v části [použití služby Správce zařízení StorSimple ke správě zařízení StorSimple](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Místního webového uživatelského rozhraní

Virtuální pole zahrnuje uživatelského rozhraní založeného na webu, který se používá pro jednorázovou konfiguraci a registraci zařízení ve službě Správce zařízení StorSimple. Můžete ji vypnout a restartovat virtuální pole, spusťte diagnostické testy, aktualizace softwaru, změna hesla správce zařízení, zobrazit protokoly systému a obraťte se na Microsoft Support do souboru žádosti o službu.

Informace o používání uživatelského rozhraní založeného na webu, přejděte na [pomocí uživatelského rozhraní založeného na webu ke správě StorSimple Virtual Array](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Rozhraní příkazového řádku

Součástí rozhraní prostředí Windows PowerShell lze zahájit relaci s Microsoft Support tak, aby se můžete odstraňovat potíže a řešit problémy, které můžete narazit na vaše virtuální pole.

## <a name="storage-management-technologies"></a>Technologie správy úložiště

Kromě virtuální pole a další komponenty používá řešení StorSimple následující technologie softwarově poskytují rychlý přístup k důležitým datům, snížit spotřebu úložiště a chránit data uložená na vaše virtuální pole:

* [Automatické vrstvení](#automatic-storage-tiering) 
* [Místně připojené sdílené složky a svazky](#locally-pinned-shares-and-volumes)
* Deduplikace a komprese dat vrstvené nebo zálohovat do cloudu 
* [Zálohování na vyžádání a plánované](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Automatické vrstvení
Virtuální pole používá nový mechanismus vrstvení ke správě uložená data pro virtuální pole i cloud. Existují pouze dvě úrovně: místní virtuální pole a Azure cloudového úložiště. StorSimple Virtual Array automaticky uspořádá data do vrstvami podle toho, heat mapa, která sleduje aktuální využití, věk a vztahy k jiným datům. Data, která jsou nejaktivnější (Nejaktivnější) uložená místně, zatímco méně aktivní i neaktivní data se automaticky migrovat do cloudu. (Všechny zálohy jsou uložené v cloudu). StorSimple upraví a mění uspořádání dat a změnit přiřazení úložiště jako vzorce používání. Některé informace mohou být například míň aktivní v čase. Jakmile bude postupně míň aktivní, je vrstvené si do cloudu. Pokud ke stejným datům opět aktivní, je vrstvený v k poli úložišť.

Data pro konkrétní vrstvené sdílené složky nebo svazku je zaručeno, že svůj vlastní místní vrstvy místa (přibližně 10 % celkové zřízené místo pro tuto sdílenou složku nebo svazek). Když to snižuje dostupného úložiště na virtuální pole pro tuto sdílenou složku nebo svazek, zajišťuje, že pro jednu sdílenou složku nebo svazek ovládání datových vrstev na nebudou vztahovat vrstvení potřeby dalších složek nebo svazků. Proto velmi vytížený úlohy v jedné sdílené složky nebo svazku nemůže vynutit všechny ostatní úlohy do cloudu.

Vrstvené svazky, které jsou pro ni vytvořily mají maximální místní rezervace 200 GB bez ohledu na velikost svazku.

![Automatické vrstvení](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Můžete zadat jako místně připojené svazku, v takovém případě data zůstávají ve virtuální pole a není nikdy Vrstvená do cloudu. Další informace najdete v části [místně připojené sdílené složky a svazky](#locally-pinned-shares-and-volumes).


### <a name="locally-pinned-shares-and-volumes"></a>Místně připojené sdílené složky a svazky

Můžete vytvořit odpovídající sdílené složky a tak místně připojené svazky. Tato možnost zajišťuje, že data potřebné pro kritické aplikace zůstává ve virtuální pole a je nikdy Vrstvená do cloudu. Místně připojené sdílené složky a svazky mají tyto funkce:

* Nejsou předmětem cloudu latenci nebo problémy s připojením.
* Stále těžit z StorSimple cloudové zálohování a po havárii obnovení funkce.

Můžete obnovit místně připojené sdílené složky nebo svazku jako vrstvené nebo vrstvenou sdílenou složku nebo svazek místně připnutý. 

Další informace o místně připojené svazky, přejděte na [použití služby Správce zařízení StorSimple pro správu svazků](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Deduplikace a komprese dat vrstvené nebo zálohovat do cloudu

StorSimple využívá komprese dat a odstranění duplicit dále snížit požadavky na úložiště v cloudu. Odstranění duplicitních dat snižuje celkové množství dat uložených odstraněním redundance v sadě dat uložené. Jak se změní informace, StorSimple ignoruje se zobrazila původní data a zachytí pouze změny. StorSimple navíc snižuje množství uložených dat identifikace a odebráním duplicitní informace.

> [!NOTE]
> Data uložená ve virtuálním poli není s odstraněním duplicitních dat nebo komprimovány. Všechny odstranění duplicitních dat a kompresi nastane těsně před plánovaným data se odesílají do cloudu.

### <a name="scheduled-and-on-demand-backups"></a>Zálohování na vyžádání a plánované

Funkcí ochrany dat StorSimple umožňuje vytvářet zálohy na vyžádání. Výchozí plán zálohování navíc zajišťuje, že se data zálohují každý den. Zálohy jsou prováděny ve formě přírůstkových snímků, které jsou uložené v cloudu. Snímky, které zaznamenává pouze změny od posledního zálohování, se dají vytvářet a rychle obnovit. Tyto snímky může být kriticky důležité pro scénáře zotavení po havárii, protože nahradit sekundární úložných systémů (např. zálohování na pásku) a umožňují obnovit data do vašeho datového centra nebo do alternativní lokality v případě potřeby.

## <a name="managing-personal-information"></a>Správa osobních údajů

Správce zařízení StorSimple pro virtuálních řad shromažďuje osobní údaje v instancích dvou klíčů:
 - Pošle upozornění, uživatelská nastavení, které jsou nakonfigurované e-mailové adresy uživatelů. Tyto informace může být zrušena správcem. 
 - Uživatelé, kteří můžou přistupovat k datům na sdílené složky. Seznam uživatelů, kteří můžou přistupovat k datům sdílené složky se zobrazí a je možné exportovat. Tento seznam se také odstraní při odstranění sdílené složky.

Další informace najdete v článku [Microsoft Privacy zásad v Centru zabezpečení](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Další postup

Zjistěte, jak [Příprava portálu virtuální pole](storsimple-virtual-array-deploy1-portal-prep.md).
