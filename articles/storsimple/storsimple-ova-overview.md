---
title: Přehled Microsoft Azure StorSimple virtuální pole | Microsoft Docs
description: Popisuje StorSimple virtuální pole o řešení integrované úložiště, který spravuje úlohy úložiště mezi virtuální pole místního a cloudového úložiště Microsoft Azure.
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
ms.date: 03/28/2018
ms.author: alkohli
ms.openlocfilehash: db06614ebd7dee4b0a320737ea8f575b3a3be70f
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Úvod do pole virtuální zařízení StorSimple

## <a name="overview"></a>Přehled

Pole virtuální Microsoft Azure StorSimple je řešení integrované úložiště, který spravuje úlohy úložiště mezi místní virtuální pole v hypervisoru a cloudového úložiště Microsoft Azure. Virtuální pole je efektivní, nákladově efektivní a snadno spravovaného souborového serveru nebo řešení serveru iSCSI, který eliminuje řadu problémů a náklady spojené s enterprise úložiště a ochranu dat. Virtuální pole je obzvláště vhodné pro úložiště archivace málokdy načítaná data.

Tento článek obsahuje přehled virtuální pole – zde jsou některé další prostředky:

* Osvědčené postupy, najdete v části [osvědčené postupy pole virtuální zařízení StorSimple](storsimple-ova-best-practices.md).
* Přehled řadu zařízení StorSimple 8000, přejděte na [řady StorSimple 8000: hybridní cloudové řešení](storsimple-overview.md).
* Informace o řadě zařízení StorSimple 5000/7000, přejděte na [StorSimple Online nápovědy](http://onlinehelp.storsimple.com/).

Pole virtuální podporuje iSCSI nebo zpráva bloku protokol Server (SMB). Je kompatibilní s vaší stávající infrastruktury hypervisoru a poskytuje vrstev do cloudu, zálohování, rychlé obnovení, obnovení na úrovni položek a funkce pro obnovení po havárii.

Následující tabulka shrnuje důležité funkce pole virtuální zařízení StorSimple.

| Funkce | StorSimple Virtual Array |
| --- | --- |
| Požadavky na instalaci |Používá infrastrukturu virtualizaci (Hyper-V nebo VMware) |
| Dostupnost |Jeden uzel |
| Celková kapacita (včetně cloudu) |Až 64 TB použitelné kapacity na virtuální pole |
| Místní kapacity |390 GB 6.4 TB kapacity použitelné na virtuální pole (třeba zřídit 500 GB až 8 TB místa na disku) |
| Nativní protokoly |iSCSI nebo SMB |
| Plánovaná doba obnovení (RTO) |iSCSI: méně než 2 minuty bez ohledu na velikost |
| Cíl bodu obnovení (RPO) |Denní zálohy a zálohy na vyžádání |
| Vrstvení úložiště |Používá vytápění mapování určit, jaká data by měla být rozvrstvena příchozí nebo odchozí |
| Podpora |Infrastruktury virtualizace podporuje dodavatele |
| Výkon |Se liší v závislosti na základní infrastruktury |
| Data nastavení mobilních zařízení |Můžete obnovit do stejného zařízení nebo obnovení na úrovni položek (souborový server) |
| Vrstvy úložiště |Úložiště na místním hypervisoru a cloud |
| Velikost sdílené složky |Víceúrovňová: až 20 TB; místně vázaný: až 2 TB |
| Velikost svazku |Vrstvený: 500 GB 5 TB; místně vázaný: 50 GB až 200 GB <br> Maximální místní rezervaci pro vrstvené svazky je 200 GB. |
| Snímky |Konzistentní havárií |
| Obnovení na úrovni položek |Ano; Uživatelé mohou obnovit ze sdílených složek |

## <a name="why-use-storsimple"></a>Proč používat StorSimple?

StorSimple připojí uživatelů a serverů do úložiště Azure v minutách, bez úprav aplikace.

Následující tabulka popisuje některé klíčové výhody, které poskytuje řešení pole virtuální zařízení StorSimple.

| Funkce | Výhoda |
| --- | --- |
| Transparentní integrace |Pole virtuální podporuje iSCSI nebo protokol SMB. Přesun dat mezi místní vrstvy a vrstvy cloudu je snadné a pro uživatele transparentní. |
| Náklady na menší úložiště |StorSimple můžete zřizovat dostatečná místní úložiště, aby splnily aktuální požadavky pro nejčastěji používaných aktivní data. Když je úložiště potřebovat růst, StorSimple vrstev pomaleji přístupná data do nákladově efektivní cloudového úložiště. Data jsou s odstraněním duplicitních dat a komprimované před odesláním do cloudu, pokud chcete dál snížit požadavky na úložiště a náklady. |
| Jednodušší správu úložišť |StorSimple nabízí centralizovanou správu v cloudu pomocí Správce zařízení StorSimple ke správě více zařízení. |
| Zotavení po havárii vylepšené a dodržování předpisů |StorSimple usnadňuje rychlejší zotavení po havárii obnovením metadata okamžitě a obnovení dat, podle potřeby. To znamená, že můžete pokračovat normální provozní podmínky s minimálním dopadem. |
| Data nastavení mobilních zařízení |Víceúrovňová data do cloudu lze přistupovat z jiných lokalitách pro účely obnovení a migrace. Všimněte si, že data můžete obnovit pouze do původní virtuální pole. Funkce obnovení po havárii však použít obnovit celý virtuální pole ke druhému virtuální. |

## <a name="storsimple-workload-summary"></a>Souhrn úloh StorSimple

Souhrnné informace o podporovaných úlohách StorSimple v následující tabulce.

|Scénář     |Úloha     |Podporováno      |Omezení               |
|-------------|-------------|---------------|---------------------------|
|ROBO  |Sdílení souborů     |Ano      |V tématu [maximální limit pro souborový server](storsimple-ova-limits.md).<br></br>V tématu [požadavky na systém pro podporované verze SMB](storsimple-ova-system-requirements.md).| Všechny verze     |
|Cloud archivaci  |Sdílení archivaci souborů     |Ano      |V tématu [maximální limit pro souborový server](storsimple-ova-limits.md).<br></br>V tématu [požadavky na systém pro podporované verze SMB](storsimple-ova-system-requirements.md).| Všechny verze     |

Pole virtuální zařízení StorSimple je nejvhodnější pro málokdy načítaná data. Při virtuální pole má místní mezipaměti pro zvýšení výkonu, uživatelé by měl předpokládá, že zařízení služby souborů je nejnižší úroveň úložiště (cloud). Každý virtuální pole můžete napsat a čtení do úložiště Azure v přibližně 100 MB/s. Tento odkaz je sdílen na všechny žádosti přicházející do zařízení a jak je znázorněno v následujícím diagramu se může stát úzkým místem.

![Cloud archivaci](./media/storsimple-ova-overview/cloud-archiving.png)

Když více souběžným uživatelům přístup k poli virtuální, budou všechny sdílet připojení k Azure úvodní nižší výkon. Neexistuje žádná zaručenou výkonu na uživatele a zařízení zpracovává jednotlivé požadavky po doručení.

Pole virtuální zařízení StorSimple není vhodný pro úlohy, které vyžadují vysokou dostupnost. Virtuální pole je jeden uzel zařízení, které narazí výpadek při instalaci aktualizací softwaru. Správci měli naplánovat časové období údržby 30 minut 3násobek - 4 za jeden rok.

## <a name="workflows"></a>Pracovní postupy

Pole virtuální zařízení StorSimple je obzvláště vhodný pro následující pracovních postupů:

* [správu cloudového úložiště](#cloud-based-storage-management)
* [Zálohování nezávislých na umístění](#location-independent-backup)
* [Data protection a zotavení po havárii](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>správu cloudového úložiště
Můžete použít službu StorSimple Manager zařízení na portálu Azure ke správě dat uložených na několika zařízeních a v několika umístěních. To je zvlášť užitečný ve scénářích, distribuované větev. Všimněte si, že musíte vytvořit samostatné instance služby StorSimple Manager zařízení ke správě pole virtuální a fyzické zařízení StorSimple. Všimněte si také, že virtuální pole teď používá nový portál Azure místo portálu Azure classic.

![správu cloudového úložiště](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Zálohování nezávislých na umístění
Cloudové snímky s virtuální pole, zadejte nezávislých na umístění, v okamžiku kopie svazku nebo sdílené složky. Cloudové snímky jsou ve výchozím nastavení povolené a nejde zakázat. Všechny svazky a sdílené složky jsou současně prostřednictvím jedna zásada denní zálohování zálohování nahoru a můžete provést další ad-hoc zálohy, kdykoli je to nezbytné.

### <a name="data-protection-and-disaster-recovery"></a>Data protection a zotavení po havárii
Pole virtuální podporuje následující ochrany dat a scénářů zotavení po havárii:

* **Svazek nebo sdílenou složku obnovení** – použijte obnovení jako nový pracovní postup obnovit svazek nebo sdílenou složku. Tuto metodu použijte, chcete-li obnovit celý svazek nebo sdílenou složku.
* **Obnovení na úrovni položek** – sdílené složky povolit zjednodušené přístup na poslední zálohy. Je možné snadno obnovit soubor z speciální *.backup* dostupnou složku v cloudu. Tato možnost obnovení je řízené uživatele a není nutný žádný zásah správce.
* **Zotavení po havárii** – použít funkci převzetí služeb při selhání obnovení všechny svazky nebo sdílené složky do nového virtuálního pole. Vytvořit nový virtuální pole a zaregistrovat ji pomocí služby StorSimple Manager zařízení a pak převzít původní virtuální pole. Nové virtuální pole pak převezme zřízené prostředky.

## <a name="storsimple-virtual-array-components"></a>Součásti pole virtuální zařízení StorSimple

Pole virtuální zahrnuje následující součásti:

* [Virtuální pole](#virtual-array) – zařízením hybridní cloudové úložiště založené na virtuálním počítači zřízenou ve virtualizovaném prostředí nebo hypervisoru.
* [Služba StorSimple Manager zařízení](#storsimple-device-manager-service) – na rozšíření na portálu Azure, který vám umožní spravovat jedno nebo více zařízení StorSimple z jedné webové rozhraní, které je přístupné z různých míst na zeměkouli. Služby StorSimple Manager zařízení můžete vytvořit a spravovat služby, zobrazit a spravovat zařízení a výstrahy a spravovat svazky, sdílené složky a existující snímky.
* [Místní webové uživatelské rozhraní](#local-web-user-interface) – uživatelské rozhraní založené na webu, které slouží ke konfiguraci zařízení tak, aby mohla připojit k místní síti a pak zařízení zaregistrovat službu StorSimple Manager zařízení. 
* [Rozhraní příkazového řádku](#command-line-interface) – rozhraní A Windows PowerShell, který můžete použít pro spuštění relace podpory na poli virtuální.
  Následující části popisují každou z těchto součástí podrobněji a popisují, jak řešení uspořádá data, přidělí úložiště a usnadňuje správu úložiště a ochranu dat.

### <a name="virtual-array"></a>Virtuální pole

Virtuální pole je jedním uzlem úložiště řešení, které poskytuje primárního úložiště, spravuje komunikace s cloudovým úložištěm a pomáhá zajistit zabezpečení a důvěrnost všechna data uložená na zařízení.

Virtuální pole je k dispozici v jednom modelu, který je k dispozici ke stažení. Virtuální pole má 6.4 TB na zařízení (s základní požadavek úložiště o velikosti 8 TB) a 64 TB včetně maximální kapacitu cloudového úložiště.

Virtuální pole má následující funkce:

* Je nákladově efektivní. Je díky použití existující infrastruktury virtualizace a dá se nasadit na vaše stávající technologie Hyper-V nebo VMware hypervisoru.
* Se nachází v datovém centru a může být nakonfigurován jako server se službou iSCSI nebo souborový server.
* Je integrovaný s cloudem.
* Zálohy jsou uložené v cloudu, což může usnadnit zotavení po havárii a zjednodušit obnovení na úrovni položek (ILR).
* Aktualizace můžete použít k poli virtuální stejně, jako je by použít na fyzické zařízení.

> [!NOTE]
> Virtuální pole nelze rozšířit. Proto je důležité ke zřízení odpovídající úložiště, když vytvoříte virtuální pole.

### <a name="storsimple-device-manager-service"></a>Služba Správce zařízení StorSimple

Microsoft Azure StorSimple poskytuje webové uživatelské rozhraní, služby StorSimple Manager zařízení, která umožňuje centrálně spravovat úložišti StorSimple. Služby StorSimple Manager zařízení můžete provádět následující úlohy:

* Několik polí virtuální zařízení StorSimple spravujte z jedné služby.
* Konfigurovat a spravovat nastavení zabezpečení pro pole virtuální zařízení StorSimple. (Šifrování v cloudu je závislá na rozhraní API Microsoft Azure.)
* Nakonfigurujte přihlašovací údaje účtu úložiště a vlastnosti.
* Konfigurovat a spravovat svazky nebo sdílené složky.
* Zálohování a obnovení dat na svazky nebo sdílené složky.
* Sledování výkonu.
* Zkontrolujte nastavení systému a identifikovat možné problémy.

Používat službu StorSimple Manager zařízení provádět každodenní správu virtuální pole.

Další informace, přejděte na [použít službu StorSimple Manager zařízení ke správě zařízení StorSimple](storsimple-virtual-array-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Místní webové uživatelské rozhraní

Pole virtuální zahrnuje uživatelského rozhraní založené na webu, který se používá pro jednorázovou konfiguraci a registraci zařízení v rámci služby StorSimple Manager zařízení. Můžete ji Pokud chcete vypnout a restartovat virtuální pole, spuštění diagnostických testů, aktualizace softwaru, změnit heslo správce zařízení, zobrazit protokoly systému a obraťte se na Microsoft Support do souboru žádosti o službu.

Informace o používání rozhraní založené na webu, přejděte na [pomocí uživatelského rozhraní založené na webu ke správě pole virtuální zařízení StorSimple](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Rozhraní příkazového řádku

Zahrnuté rozhraní Windows PowerShell lze zahájit relaci podporu s Microsoft Support tak, aby se vám může pomoct vyřešit problémy, které můžete narazit na vaše virtuální pole.

## <a name="storage-management-technologies"></a>Technologie správy úložiště

Kromě virtuální pole a další součásti používá řešení StorSimple následující technologie softwarově umožňují rychlý přístup k důležitých dat, snížení spotřeby úložiště a chránit data uložená na vaše virtuální pole:

* [Automatické úložiště vrstvení](#automatic-storage-tiering) 
* [Místně vázaný sdílených složek a svazků](#locally-pinned-shares-and-volumes)
* [Odstranění duplicitních dat a komprese dat vrstvené nebo zálohovat do cloudu](#deduplication-and-compression-for-data-tiered/backed-up-to-the-cloud) 
* [Zálohy plánované a na vyžádání](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Automatické úložiště vrstvení
Virtuální pole používá mechanismus nové vrstvení správu uložených dat přes virtuální pole a v cloudu. Existují pouze dvě úrovně: místní virtuální pole a Azure cloudového úložiště. Pole virtuální zařízení StorSimple automaticky uspořádá data do vrstvami podle toho, heat mapa, který sleduje aktuální využití, stáří a vztahy s jinými daty. Data, která je nejvíce aktivní (nejprodávanějších) ukládají se místně, zatímco se automaticky migrují méně aktivní i neaktivní data do cloudu. (Všechny zálohy jsou uložené v cloudu). StorSimple upraví a změní uspořádání dat a změnit přiřazení úložiště jako vzorce používání. Některé informace mohou být například míň aktivní v čase. Jakmile je k progresivnímu míň aktivní, je vrstvené se do cloudu. Pokud tato data zase aktivní, je vrstvené v k poli úložišť.

Data pro konkrétní vrstvené složky nebo svazku záruku, že svůj vlastní místní vrstvy místa (přibližně 10 % celkového místa zřízené pro tuto sdílenou složku nebo svazek). Když to snižuje úložiště k dispozici na virtuální pole pro tuto sdílenou složku nebo svazek, zajišťuje, že vrstvení pro jednu sdílenou složku nebo svazku se nevztahuje vrstvení potřebám jiné sdílené složky nebo svazky. Velmi zaneprázdněny zatížení v jedné sdílené složky nebo svazku, proto nemůže vynutit všechny ostatní úlohy do cloudu.

Vrstvené svazky vytvořené pro iSCSI mít maximální místní rezervace 200 GB bez ohledu na velikost svazku.

![Automatické úložiště vrstvení](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> Můžete zadat jako místně vázaný svazek, v takovém případě data zůstane na poli virtuální a nikdy vrstvené do cloudu. Další informace, přejděte na [místně vázaný sdílených složek a svazků](#locally-pinned-shares-and-volumes).


### <a name="locally-pinned-shares-and-volumes"></a>Místně vázaný sdílených složek a svazků

Můžete vytvořit odpovídající složkami a svazky jako místně vázaný. Tato možnost zajistí zůstane v poli virtuální dat požadovaných důležitých aplikací a nikdy vrstveny do cloudu. Místně vázaný složkami a svazky mají následující funkce:

* Nepodléhají cloudu latenci nebo problémy s připojením.
* Přesto využívat výhod StorSimple zálohování a po havárii obnovení funkce cloudu.

Můžete obnovit místně vázaný sdílené složky nebo svazku jako vrstvené nebo vrstvené sdílené složky nebo svazku jako místně připojené. 

Další informace o místně vázaných svazků, přejděte na [pomocí služby StorSimple Manager zařízení můžete spravovat svazky](storsimple-virtual-array-manage-volumes.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Odstranění duplicitních dat a komprese dat vrstvené nebo zálohovat do cloudu

StorSimple používá komprese dat a odstranění duplicit Pokud chcete dál snížit požadavky na úložiště v cloudu. Odstranění duplicitních dat snižuje celkové množství dat uložených odstraněním redundance v sadě uložená data. Informace o změní, StorSimple ignoruje nezměněná data a zaznamená pouze změny. Kromě toho StorSimple snižuje množství uložených dat určením a odebrání duplicitní informace.

> [!NOTE]
> Data uložená na virtuální pole není s odstraněním duplicitních dat nebo komprimován. Všechny odstranění duplicitních dat a komprese dojde k těsně před data se odesílají do cloudu.

### <a name="scheduled-and-on-demand-backups"></a>Zálohy plánované a na vyžádání

Funkce ochrany dat StorSimple umožňují vytvářet zálohy na vyžádání. Výchozí plán zálohování také zajišťuje, že data zálohovat denně. Zálohy jsou převzaty ve formě přírůstkové snímky, které jsou uložené v cloudu. Snímky, které záznam pouze změny od poslední zálohy, můžete vytvořit a rychle obnovit. Tyto snímky může být důležité ve scénářích zotavení po havárii, protože nahradit sekundární úložných systémů (například zálohování na pásku) a umožňují obnovit data do datového centra nebo do alternativní lokality v případě potřeby.

## <a name="gdpr-compliance"></a>GDPR dodržování předpisů
[Obecné Data Protection nařízení (GDPR)](http://ec.europa.eu/justice/data-protection/reform/index_en.htm) je data protection a o ochraně osobních údajů zákon Evropské unie (EU). GDPR obsahuje mnoho požadavky na to, jak shromažďování, ukládání a používat osobní údaje. Pravidla GDPR jsou vynucená pro společnosti, vládních organizací a dalšími organizacemi, které fungují v Evropa a shromažďovat, analyzovat data svázané s obyvatele Evropské unie.

Správce zařízení StorSimple řady, virtuální je GDPR kompatibilní. Existují dva klíče instance, kde je osobní údaje shromážděné a zobrazí v rámci služby:
 - Výstraha uživatelská nastavení, které jsou nakonfigurované e-mailové adresy uživatelů. Tyto informace může být zrušena správcem. 
 - Uživatelé, kteří měli přístup k datům, které se nacházejí na sdílené složky. Seznam uživatelů, kteří měli přístup k datům sdílenou složku se zobrazí a je možné exportovat. Tento seznam je taky odstranit po odstranění sdílené složky.

Další informace najdete v článku [Microsoft Privacy zásady v Centru zabezpečení](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Další postup

Zjistěte, jak [Příprava portálu virtuální pole](storsimple-virtual-array-deploy1-portal-prep.md).
