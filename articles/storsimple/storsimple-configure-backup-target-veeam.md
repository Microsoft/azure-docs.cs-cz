---
title: Řady StorSimple 8000 jako cíl zálohování s Veeam | Microsoft Docs
description: Popisuje konfiguraci cíl zálohy zařízení StorSimple s Veeam.
services: storsimple
documentationcenter: ''
author: harshakirank
manager: matd
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2016
ms.author: hkanna
ms.openlocfilehash: cc1c7a3f77af76c451bb6e97a081a01c119333b5
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
ms.locfileid: "23877388"
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>StorSimple jako cíl zálohování s Veeam

## <a name="overview"></a>Přehled

Azure StorSimple je řešením hybridní cloudové úložiště od společnosti Microsoft. StorSimple řeší složitosti nárůst exponenciální dat pomocí účtu Azure Storage jako rozšíření v případě místních řešení a automaticky vrstvení dat mezi místní úložiště a úložiště v cloudu.

V tomto článku probereme StorSimple integrace s Veeam a osvědčené postupy pro integraci obě řešení. Také jsme zkontrolujte doporučení o tom, jak nastavit Veeam nejlépe integraci s StorSimple. Jsme odložení Veeam osvědčené postupy, zálohování architekty a správce pro nejlepší způsob, jak nastavit Veeam splňovat požadavky na jednotlivé zálohování a smlouvy o úrovni služeb (SLA).

I když jsme ilustrují kroky konfigurace a klíčové koncepty, tento článek je rozhodně není to podrobný Průvodce konfigurace nebo instalace. Předpokládáme, že základní součásti a infrastrukturu správně funguje a připravené k podpoře koncepty, které jsme popisují.

### <a name="who-should-read-this"></a>Komu je určen to?

Informace v tomto článku bude nejužitečnější zálohování správců, správců úložiště a úložiště architektům, kteří mají znalosti o úložiště, Windows Server 2012 R2, Ethernet, cloudové služby a Veeam.

### <a name="supported-versions"></a>Podporované verze

-   Veeam 9 a novějších verzí
-   [StorSimple Update 3 a novějších verzích](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Proč StorSimple jako cíl zálohování?

StorSimple je vhodná pro cíl zálohy, protože:

-   Poskytuje standardní, místní úložiště pro zálohování aplikace, které chcete použít jako cíl pro rychlé zálohování, beze změn. Můžete taky použít StorSimple pro rychlé obnovení poslední zálohy.
-   Jeho cloudu vrstvení je bezproblémově integrovaná s účtem úložiště Azure cloud používání nákladově efektivní úložiště Azure.
-   Automaticky poskytuje úložiště mimo pracoviště pro zotavení po havárii.


## <a name="key-concepts"></a>Klíčové koncepty

Stejně jako u jakékoli řešení úložiště, postupujte opatrně hodnocení výkonu úložiště na řešení, SLA, je důležité na úspěšné provedení míra změn a požadavků na kapacitu růstu. Main nápad je, že díky zavedení cloudovou vrstvu, čas přístupu a propustnosti na cloudu play roli základní možnosti StorSimple svou úlohu.

StorSimple slouží k poskytování úložiště pro aplikace, které fungují na dobře definovaný pracovní sady dat (aktivní data). V tomto modelu pracovní sady dat je uložena v místních vrstvách a vrstveny zbývající nepracovní, uloží málo používaná data nebo archivovat sadu dat do cloudu. Tento model je znázorněná na následujícím obrázku. Téměř nestrukturované zelená řádek představuje data uložená v místních vrstvách zařízení StorSimple. Červená čára představuje celkovou velikost dat uložených v řešení StorSimple napříč všechny úrovně. Mezera mezi nestrukturované zelená řádku a exponenciální red křivky představuje celkové množství dat uložených v cloudu.

**Vrstvení StorSimple**
![vrstvení diagram StorSimple](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

Tato architektura pamatovat zjistíte, že zařízení StorSimple je výborně fungují jako cíl zálohy. Můžete použít StorSimple na:

-   Vaše nejčastěji se vyskytující obnovení proveďte z místní pracovní sadu dat.
-   Použijte cloudu pro obnovení po havárii mimo pracoviště a starší data, kde jsou méně časté obnovení.

## <a name="storsimple-benefits"></a>Výhody StorSimple

V případě místních řešení, která je bezproblémově integrovaná s Microsoft Azure a využívají k bezproblémový přístup k místnímu poskytuje StorSimple a cloudového úložiště.

StorSimple používá automatické vrstvení mezi místní zařízení, která má SSD zařízení (SSD) a serial-attached SCSI (SAS), úložiště a Azure Storage. Automatické vrstvení udržuje často používaná data místní počítač, na vrstvy SSD a SAS. Přesune málokdy načítaná data do služby Azure Storage.

StorSimple nabízí tyto výhody:

-   Jedinečný odstraňování duplicitních dat a komprese algoritmy, které použít cloudu k dosažení úrovně nebývalá odstranění duplicitních dat
-   Vysoká dostupnost
-   Geografická replikace pomocí Azure geografická replikace
-   Integrace se službou Azure
-   Šifrování dat v cloudu
-   Zotavení po havárii vylepšené a dodržování předpisů

I když StorSimple uvede dva scénáře hlavní nasazení (primární cíl zálohy a sekundární cíl zálohy), od základu, je prostý, zařízení s blokovým úložištěm. StorSimple nemá všechny komprese a odstranění duplicit. Bezproblémově odešle a načte data mezi cloudu a aplikace a systému souborů.

Další informace o zařízení StorSimple, najdete v části [řady StorSimple 8000: hybridní cloudové úložiště řešení](storsimple-overview.md). Navíc můžete zkontrolovat [technických specifikací řady StorSimple 8000](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Pomocí StorSimple zařízení jako cíl zálohování je podporováno pouze pro zařízení StorSimple 8000 Update 3 a novějších verzích.

## <a name="architecture-overview"></a>Přehled architektury

Následující tabulky popisují počáteční pokyny modelu architektura zařízení.

**StorSimple kapacity pro místní a cloudové úložiště**

| Kapacita úložiště | 8100 | 8600 |
|---|---|---|
| Místní úložiště kapacity | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Kapacita cloudového úložiště | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* Velikost úložiště předpokládá bez odstranění duplicitních dat nebo kompresi.

**StorSimple kapacity pro primární a sekundární zálohování**

| Zálohování scénář  | Místní úložiště kapacity  | Kapacita cloudového úložiště  |
|---|---|---|
| Primární zálohu  | Nedávné zálohy uložený v místním úložišti pro rychlé obnovení ke splnění plánovaného bodu obnovení (RPO) | Historie zálohování (RPO) se vejde kapacity cloudu |
| Sekundární zálohování | Sekundární kopii zálohovaných dat mohou být uloženy ve kapacity cloudu  | neuvedeno  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple jako primární cíl zálohování

V tomto scénáři jsou pro aplikaci Zálohování, která jako jedinou úložiště pro zálohy zobrazí svazky zařízení StorSimple. Následující obrázek znázorňuje architekturu řešení, ve kterém použijte všechny zálohy zařízení StorSimple zřízeny vrstvené svazky pro zálohování a obnovení.

![StorSimple jako primární cíl zálohy logického diagramu](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Primární cíl zálohování logických kroků.

1.  Zálohování serveru kontaktovat agenta cíl zálohování a zálohování agent odesílá data na zálohování serveru.
2.  Zálohování serveru zapisuje data do StorSimple zřízeny vrstvené svazky.
3.  Zálohování serveru aktualizuje databázi katalogu a pak dokončí úlohu zálohování.
4.  Skript snímku aktivuje cloud snapshot manager zařízení StorSimple (spuštění nebo odstranění).
5.  Zálohování serveru odstraní vypršela platnost záloh na základě zásad uchovávání informací.

### <a name="primary-target-restore-logical-steps"></a>Primární cíl obnovení logických kroků

1.  Zálohování serveru spustí obnovit příslušná data z úložiště úložiště.
2.  Agenta zálohování přijímá data ze zálohování serveru.
3.  Záložní server dokončí úlohu obnovení.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple jako sekundární cíl zálohování

V tomto scénáři se svazky zařízení StorSimple primárně používají pro dlouhodobé uchovávání nebo archivovat.

Následující obrázek ukazuje architekturu, ve které prvotní zálohy a obnoví vysoce výkonné cílový svazek. Tyto zálohy jsou zkopírovány a jeho archivaci pro StorSimple vrstvené svazku podle nastaveného plánu.

Je důležité velikost svazku vysoce výkonné, tak, aby ji může zpracovat požadavky vaší uchování zásad kapacitu a výkon.

![StorSimple jako sekundární cíl zálohy logického diagramu](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Sekundární cíl zálohování logických kroků.

1.  Zálohování serveru kontaktovat agenta cíl zálohování a zálohování agent odesílá data na zálohování serveru.
2.  Zálohování serveru zapisuje data do úložiště s vysokým výkonem.
3.  Zálohování serveru aktualizuje databázi katalogu a pak dokončí úlohu zálohování.
4.  Zálohování serveru zkopíruje zálohy na StorSimple na základě zásad uchovávání informací.
5.  Skript snímku aktivuje cloud snapshot manager zařízení StorSimple (spuštění nebo odstranění).
6.  Zálohování serveru odstraní vypršela platnost záloh na základě zásad uchovávání informací.

### <a name="secondary-target-restore-logical-steps"></a>Sekundární cíl obnovení logických kroků

1.  Zálohování serveru spustí obnovit příslušná data z úložiště úložiště.
2.  Agenta zálohování přijímá data ze zálohování serveru.
3.  Záložní server dokončí úlohu obnovení.

## <a name="deploy-the-solution"></a>Nasazení řešení

Nasazení řešení vyžaduje tři kroky:

1. Připravte síťové infrastruktury.
2. Nasazení zařízení StorSimple jako cíl zálohování.
3. Nasaďte Veeam.

Každý krok je podrobněji v následujících částech.

### <a name="set-up-the-network"></a>Nastavení sítě

Protože StorSimple je řešení, které jsou integrovány v cloudu Azure, vyžaduje StorSimple aktivní a připojení pracovní do cloudu Azure. Toto připojení se používá pro operace, jako je cloudových snímků, Správa dat a přenos metadata, a do vrstvy starší, menší používaná data do cloudového úložiště Azure.

Pro řešení pro optimální výkon doporučujeme, postupujte podle těchto sítě osvědčené postupy:

-   Odkaz, která se připojuje k Azure vaše StorSimple vrstvení musí splňovat požadavky na šířku pásma. Dosáhnout použitím potřebné úrovně služby (QoS Quality Service) k infrastruktuře přepínače tak, aby odpovídaly plánovaný bod obnovení a obnovení čas SLA cíl (RTO).
-   Maximální latence přístupu k úložišti objektů Blob v Azure by měl být přibližně 80 ms.

### <a name="deploy-storsimple"></a>Nasazení zařízení StorSimple

Pokyny krok za krokem StorSimple nasazení najdete v tématu [nasazení místního zařízení StorSimple](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Nasazení Veeam

Osvědčené postupy instalace Veeam, najdete v části [Veeam zálohování a osvědčené postupy replikace](https://bp.veeam.expert/), a čtení v uživatelské příručce na [Centrum pro nápovědu Veeam (Technická dokumentace)](https://www.veeam.com/documentation-guides-datasheets.html).

## <a name="set-up-the-solution"></a>Nastavit řešení

V této části ukážeme některé příklady konfigurace. Následující příklady a doporučení znázorňují nejvíce základní a základní implementaci. Tato implementace nemusí vztahovat přímo na konkrétní požadavky zálohy.

### <a name="set-up-storsimple"></a>Nastavení pro zařízení StorSimple

| Úlohy nasazení zařízení StorSimple  | Další komentáře |
|---|---|
| Nasazení místního zařízení StorSimple. | Podporované verze: aktualizace 3 a vyšší verze. |
| Zapněte cíle zálohy. | Pomocí těchto příkazů k zapnutí nebo vypnutí režimu cíl zálohy a získat stav. Další informace najdete v tématu [připojit vzdáleně k zařízení StorSimple](storsimple-remote-connect.md).</br> Chcete zapnout režim zálohování: `Set-HCSBackupApplianceMode -enable`. </br> Chcete-li vypnout režim zálohování: `Set-HCSBackupApplianceMode -disable`. </br> Chcete-li získat aktuální stav nastavení režim zálohování: `Get-HCSBackupApplianceMode`. |
| Vytvořte kontejner svazků společné pro uložení zálohy dat svazku. Všechna data v kontejneru svazku se odstraňují. | Kontejnery svazků zařízení StorSimple definování domén odstranění duplicit.  |
| Vytvořte svazky zařízení StorSimple. | Vytvořte svazky s velikostí jako blízko předpokládaného využití nejdříve, protože velikost svazku ovlivňují cloud snímku doba trvání. Informace o tom, jak velikost svazku, přečtěte si informace o [zásady uchovávání informací](#retention-policies).</br> </br> Použití StorSimple zřízeny vrstvené svazky a vyberte **použít tento svazek pro archivní data s méně často používaná** zaškrtávací políčko. </br> Použití pouze místně vázaný svazky není podporováno. |
| Vytvoření jedinečné zásady StorSimple zálohování pro všechny svazky, cíl zálohy. | Zásady zálohování StorSimple definuje skupinu konzistence svazku. |
| Zakažte plán, protože platnost vyprší snímky. | Snímky jsou aktivovány jako následného zpracování operace. |

### <a name="set-up-the-host-backup-server-storage"></a>Nastavení úložiště zálohování serveru hostitele

Nastavení úložiště zálohování serveru hostitele podle těchto pokynů:  

- Nepoužívejte rozložené svazky (vytvořený Správa disků systému Windows). Rozložené svazky nejsou podporované.
- Formátování svazků pomocí systému souborů NTFS s 64 KB alokační jednotky.
- Mapovat přímo na Veeam server svazky zařízení StorSimple.
    - Použijte iSCSI pro fyzických serverů.


## <a name="best-practices-for-storsimple-and-veeam"></a>Osvědčené postupy pro StorSimple a Veeam

Nastavit řešení podle pokynů v následujícím několik oddílů.

### <a name="operating-system-best-practices"></a>Osvědčené postupy operačního systému

-   Zakažte šifrování na Windows serveru a odstranění duplicit pro systém souborů NTFS.
-   Zakážete defragmentace systému Windows Server na svazky zařízení StorSimple.
-   Zakažte indexování systému Windows Server na svazky zařízení StorSimple.
-   Spusťte antivirovým na zdrojovém hostiteli (ne proti svazky zařízení StorSimple).
-   Vypnout výchozí [údržby systému Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) ve Správci úloh. To udělat v jednom z následujících způsobů:
    - Vypněte Konfigurátor údržby v Plánovači úloh systému Windows.
    - Stáhněte si [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) z webu Windows Sysinternals. Po stažení nástroje PsExec, spusťte prostředí Windows PowerShell jako správce a zadejte:
      ```powershell
      psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
      ```

### <a name="storsimple-best-practices"></a>Osvědčené postupy StorSimple

-   Ujistěte se, že zařízení StorSimple se aktualizuje na [Update 3 nebo novější](storsimple-install-update-3.md).
-   Izolovat přenosy iSCSI a cloudu. Použijte vyhrazené iSCSI připojení pro přenos dat mezi StorSimple a zálohování serveru.
-   Ujistěte se, že zařízení StorSimple je vyhrazené cíl zálohy. Smíšené úlohy nejsou podporované, protože mají vliv na RTO a plánovaný bod obnovení.

### <a name="veeam-best-practices"></a>Osvědčené postupy Veeam

-   Databázi Veeam by měl být místní pro server a není umístěn v svazek StorSimple.
-   Pro zotavení po havárii zálohujte databázi Veeam na svazek StorSimple.
-   Podporujeme Veeam úplné a přírůstkové zálohování pro toto řešení. Doporučujeme vám, nepoužívejte syntetické a rozdílové zálohy.
-   Zálohování datových souborů by měl obsahovat pouze data pro konkrétní úlohu. Například žádné médium připojí přes různé úlohy jsou povoleny.
-   Vypněte úlohu ověření. V případě potřeby nutné naplánovat ověření po nejnovější úlohy zálohování. Je důležité si uvědomit, že tato úloha ovlivňuje okno zálohování.
-   Zapněte předběžné přidělení média.
-   Ujistěte se, že je zapnutý paralelní zpracování.
-   Vypnula kompresi.
-   Vypněte odstranění duplicit na úlohu zálohování.
-   Nastavit optimalizace **LAN cíl**.
-   Zapnout **active úplné zálohování vytvořit** (každé 2 týdny).
-   V úložišti zálohování nastavit **použijte záložní soubory pro jednotlivé virtuální počítače**.
-   Nastavit **použít víc datových proudů nahrávání na úlohu** k **8** (je povoleno maximálně 16). Nastavením této hodnoty nahoru nebo dolů na základě využití procesoru v zařízení StorSimple.

## <a name="retention-policies"></a>Zásady uchovávání informací

Jeden z nejběžnějších typů zásad uchovávání záloh je zásada Dědečka otec a SYN (GFS). V zásadách GFS přírůstkové zálohy se provádí denně a úplné zálohy se provádějí týdenní a měsíční. Výsledkem zásad šesti StorSimple zřízeny vrstvené svazky: jeden svazek obsahuje úplných záloh týdenní, měsíční nebo roční; pět svazky úložiště každodenní přírůstkové zálohování.

V následujícím příkladu použijeme rotaci GFS. Příklad předpokládá následující:

-   Bez zajištěná nebo komprimovaných dat se používá.
-   Úplné zálohy představují 1 TiB.
-   Každodenní přírůstkové zálohování jsou 500 GiB.
-   Čtyři týdenní zálohy jsou v měsíci.
-   Dvanáct měsíční zálohy jsou v roce.
-   Jeden rok zálohování je udržováno 10 let.

Podle předchozích předpokladů, vytvořit 26-TiB StorSimple vrstvené svazek pro měsíční a roční úplné zálohy. Vytvoření 5-TiB StorSimple vrstvené svazku pro každou přírůstkovou denní zálohy.

| Uchování typ zálohy | Velikost (TiB) | Násobitel GFS\* | Celková kapacita (TiB)  |
|---|---|---|---|
| Týdenní úplné | 1 | 4  | 4 |
| Každodenní přírůstkové | 0.5 | 20 (cykly stejný počet týdnů v měsíci) | 12 (2 pro další kvóty) |
| Měsíční úplné | 1 | 12 | 12 |
| Roční úplné | 1  | 10 | 10 |
| Požadavek GFS |   | 38 |   |
| Další kvóty  | 4  |   | 42 celkový požadavek GFS  |
\* Násobitel GFS je počet kopií, které potřebujete k ochraně a zachovat podle požadavků vaší zásady zálohování.

## <a name="set-up-veeam-storage"></a>Nastavení úložiště Veeam

### <a name="to-set-up-veeam-storage"></a>Instalace služby Veeam úložiště

1.  V konzole zálohování Veeam a replikace v **úložiště nástroje**, přejděte na **infrastruktura zálohování**. Klikněte pravým tlačítkem na **zálohování úložiště**a potom vyberte **přidejte úložiště zálohování**.

    ![Konzola pro správu Veeam, stránka úložiště zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  V **nového úložiště zálohování** dialogovém okně zadejte název a popis pro úložiště. Vyberte **Next** (Další).

    ![Veeam správy konzoly, název a popis stránky](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  Pro typ, vyberte **Microsoft Windows server**. Vyberte server, Veeam. Vyberte **Next** (Další).

    ![Konzola pro správu Veeam, vyberte typ úložiště zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  Chcete-li určit **umístění**, vyhledejte a vyberte svazek. Vyberte **omezit na maximální souběžných úloh:** zaškrtněte políčko a nastavte hodnotu na **4**. Tím se zajistí, že pouze čtyři virtuální disky jsou zpracovávány souběžně, při zpracovávání každý virtuální počítač (VM). Vyberte **Upřesnit** tlačítko.

    ![Konzola pro správu Veeam, vyberte svazek](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  V **nastavení kompatibility úložiště** dialogové okno, vyberte **použijte záložní soubory pro jednotlivé virtuální počítače** zaškrtávací políčko.

    ![Konzola pro správu Veeam, nastavení kompatibility úložiště](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  V **nového úložiště zálohování** dialogové okno, vyberte **povolte službu vPower systému souborů NFS na server připojit (doporučeno)** zaškrtávací políčko. Vyberte **Next** (Další).

    ![Konzola pro správu Veeam, stránka úložiště zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Zkontrolujte nastavení a potom vyberte **Další**.

    ![Konzola pro správu Veeam, stránka úložiště zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    Úložiště se přidá do Veeam serveru.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Nastavit StorSimple jako primární cíl zálohy

> [!IMPORTANT]
> Rychlostmi cloudu dojde k obnovení dat ze zálohy, které byly zřízeny vrstvené do cloudu.

Následující obrázek znázorňuje mapování svazek typické pro úlohu zálohování. V takovém případě všechny týdenní zálohy mapování na sobotu celého disku a mapování přírůstkové zálohování na disky přírůstkové pondělí – pátek. Všechny vytvořené zálohy a obnovení jsou z StorSimple vrstvené svazku.

![Logický diagram konfigurace primární cíl zálohy](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple jako primární cíl zálohování GFS naplánovat příklad

Tady je příklad plánu otočení GFS čtyři týdny, měsíční nebo roční:

| Typ frekvence nebo zálohování | Úplná | Přírůstkové (1-5 dnů)  |   
|---|---|---|
| Každý týden (1 – 4 týdny) | Sobota | Pondělí – pátek |
| Měsíční  | Sobota  |   |
| Ročně | Sobota  |   |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>Přiřadit úlohu zálohování Veeam svazky zařízení StorSimple

Pro scénář primární cíle zálohy vytvořte každodenní úlohu s primární Veeam StorSimple svazku. Pro scénář sekundární cíl zálohy vytvořte každodenní úlohu pomocí přímého připojené úložiště (DAS), síti připojené úložiště (NAS) nebo Just Bunch disky (JBOD) úložiště.

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>Přiřadit úlohu zálohování Veeam svazky zařízení StorSimple

1.  V konzole zálohování Veeam a replikace vyberte **zálohování & replikace**. Klikněte pravým tlačítkem na **zálohování**a potom vyberte **VMware** nebo **technologie Hyper-V**, v závislosti na vašem prostředí.

    ![Konzola pro správu Veeam, novou úlohu zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  V **novou úlohu zálohování** dialogovém okně zadejte název a popis pro každodenní úlohu zálohování.

    ![Konzola pro správu Veeam, nová stránka úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Vyberte virtuální počítač pro jejich zálohování.

    ![Konzola pro správu Veeam, nová stránka úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Vyberte hodnoty, které chcete použít pro **zálohování proxy** a **zálohování úložiště**. Vyberte hodnotu pro **bodů obnovení můžete zachovat na disku** podle definice plánovaný bod obnovení a RTO pro vaše prostředí na místně připojených úložišť. Vyberte **rozšířené**.

    ![Konzola pro správu Veeam, nová stránka úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. V **Upřesnit nastavení** v dialogovém **zálohování** vyberte **přírůstkové**. Ujistěte se, který **pravidelně vytvářet syntetické úplné zálohy** zaškrtnutí políčka. Vyberte **pravidelně vytvářet active úplné zálohy** zaškrtávací políčko. V části **Active úplné zálohování**, vyberte **každý týden ve vybrané dny** zaškrtnutí políčka pro sobotu.

    ![Konzole pro správu Veeam novou úlohu zálohování Upřesnit nastavení stránky](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. Na **úložiště** kartě, ujistěte se, že **povolení odstraňování duplicitních dat vložené** zaškrtnutí políčka. Vyberte **vyloučení odkládací soubor bloky** zaškrtněte políčko a vyberte **vyloučení odstranit soubor bloky** zaškrtávací políčko. Nastavit **úroveň komprese** k **žádné**. Pro vyrovnáváním výkonu a odstranění duplicitních dat, nastavte **optimalizace úložiště** k **LAN cíl**. Vyberte **OK**.

    ![Konzole pro správu Veeam novou úlohu zálohování Upřesnit nastavení stránky](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Informace o nastavení odstraňování duplicitních dat a komprese Veeam najdete v tématu [komprese dat a odstranění duplicit](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html).

7.  V **Upravit úlohu zálohování** dialogové okno, můžete vybrat **povolit zohledňující aplikace zpracování** políčko (volitelné).

    ![Konzola pro správu Veeam, nová stránka hosta zpracování úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Nastavte plán spuštění jednou denně v době, kterou lze zadat.

    ![Konzola pro správu Veeam, nová stránka plánu úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Nastavit StorSimple jako sekundární cíl zálohy

> [!NOTE]
> Rychlostmi cloudu dojít k obnovení dat ze zálohy, které byly zřízeny vrstvené do cloudu.

V tomto modelu musí mít na úložná média (jiné než StorSimple) sloužit jako dočasnou vyrovnávací paměť. Například můžete použít redundantní pole nezávislých disků (RAID) svazku pro uchování místa, vstupní a výstupní (I/O) a šířky pásma. Doporučujeme používat RAID 5, 50 a 10.

Následující obrázek znázorňuje typické krátkodobé uchování místní (pro server) a dlouhodobé uchovávání archivu svazky. V tomto scénáři spustit všechny zálohy na místní (pro server) svazek RAID. Tyto zálohy jsou pravidelně duplicitní a archivovat na svazek archivu. Je důležité velikost svazku RAID místní (pro server) tak, aby ji může zpracovat krátkodobé uchování požadavky kapacitu a výkon.

![StorSimple jako sekundární cíl zálohy logického diagramu](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple jako v příkladu GFS sekundární cíl zálohy

Následující tabulka ukazuje, jak nastavení zálohování ke spuštění na místní a StorSimple disky. Její součástí jsou požadavky na jednotlivé a celkové kapacity.

| Typ zálohování a uchovávání | Úložiště | Velikost (TiB) | Násobitel GFS | Celková kapacita\* (TiB) |
|---|---|---|---|---|
| Týden 1 (úplné a přírůstkové) |Místní disk (krátkodobé)| 1 | 1 | 1 |
| Týdny StorSimple 2 – 4 |StorSimple disku (dlouhodobé) | 1 | 4 | 4 |
| Měsíční úplné |StorSimple disku (dlouhodobé) | 1 | 12 | 12 |
| Roční úplné |StorSimple disku (dlouhodobé) | 1 | 1 | 1 |
|Požadavek na velikost svazků GFS |  |  |  | 18*|
\* Celkové kapacity zahrnuje 17 TiB StorSimple disky a 1 TiB místní svazek RAID.


### <a name="gfs-example-schedule"></a>Plán příklad GFS

Otočení GFS týdenní, měsíční a roční plán

| Týden | Úplná | Přírůstkové den 1 | Přírůstkové den 2 | Přírůstkové den 3 | Přírůstkové den 4 | Přírůstkové den 5 |
|---|---|---|---|---|---|---|
| 1 týden | Místní svazek RAID  | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID |
| Týden 2 | Týdny StorSimple 2 – 4 |   |   |   |   |   |
| Týden 3 | Týdny StorSimple 2 – 4 |   |   |   |   |   |
| Týden 4 | Týdny StorSimple 2 – 4 |   |   |   |   |   |
| Měsíční | Měsíčně StorSimple |   |   |   |   |   |
| Ročně | Ročně StorSimple  |   |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>Přiřadit úlohu kopírování Veeam svazky zařízení StorSimple

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>Přiřadit úlohu kopírování Veeam svazky zařízení StorSimple

1.  V konzole zálohování Veeam a replikace vyberte **zálohování & replikace**. Klikněte pravým tlačítkem na **zálohování**a potom vyberte **VMware** nebo **technologie Hyper-V**, v závislosti na vašem prostředí.

    ![Konzola pro správu Veeam, nová stránka úlohy záložní kopii.](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  V **novou úlohu zálohování kopírováním** dialogovém okně zadejte název a popis pro úlohu.

    ![Konzola pro správu Veeam, nová stránka úlohy záložní kopii.](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Vyberte virtuální počítače, které chcete zpracovat. Vyberte ze zálohy a pak vyberte denní zálohování, který jste vytvořili dříve.

    ![Konzola pro správu Veeam, nová stránka úlohy záložní kopii.](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  Vyloučení objektů z úlohu záložní kopii v případě potřeby.

5.  Vyberte zálohování úložiště a nastavte hodnotu pro **bodů obnovení můžete zachovat**. Je nutné vybrat **zachovat následující body obnovení pro archivační účely** zaškrtávací políčko. Zadejte četnost zálohování a pak vyberte **Upřesnit**.

    ![Konzola pro správu Veeam, nová stránka úlohy záložní kopii.](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Zadejte následující upřesňující nastavení:

    * Na **údržby** kartě, vypněte ochranu úrovně poškození úložiště.

    ![Konzoly pro správu Veeam novou úlohu zálohování kopírováním rozšířené nastavení stránky](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * Na **úložiště** kartě, ujistěte se, že odstranění duplicitních dat a komprese jsou vypnuté.

    ![Konzoly pro správu Veeam novou úlohu zálohování kopírováním rozšířené nastavení stránky](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Zadejte, zda přenos dat je SMB direct.

8.  Definujte plán okno záložní kopie podle vašich potřeb a pak dokončete průvodce.

Další informace najdete v tématu [vytvořit záložní kopii úlohy](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>Cloudové snímky StorSimple

Cloudové snímky StorSimple chránit data, která se nachází v zařízení StorSimple. Vytvoření snímku cloudu je ekvivalentní k přesouvání místní záložní pásky do zařízení mimo lokalitu. Pokud používáte Azure geograficky redundantní úložiště, vytvoření cloudový snímek je ekvivalentní k přesouvání záložní pásky do více lokalit. Pokud budete potřebovat obnovení po havárii zařízení, můžete převést online jiná zařízení StorSimple a selhání. Po převzetí služeb při selhání budou moct pro přístup k datům (cloudu rychlostmi) z nejnovější cloudový snímek.

Následující část popisuje, jak vytvořit krátký skript pro spuštění a odstranění zařízení StorSimple cloudových snímků během zálohování po zpracování.

> [!NOTE]
> Snímky vytvořené ručně nebo z programu nepostupujte podle zásad vypršení platnosti StorSimple snímku. Tyto snímky musíte ručně nebo z programu smazat.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Spuštění a odstraňte snímky cloudu pomocí skriptu

> [!NOTE]
> Pečlivě vyhodnoťte dodržování předpisů a dopady uchování dat před odstraněním snímku StorSimple. Další informace o tom, jak spustit skript po zálohování, naleznete v dokumentaci k Veeam.


### <a name="backup-lifecycle"></a>Zálohování životního cyklu

![Diagram zálohování životního cyklu](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

### <a name="requirements"></a>Požadavky

-   Server, který spouští skript musí mít přístup k prostředkům cloudu Azure.
-   Uživatelský účet musí mít potřebná oprávnění.
-   Zásady zálohování StorSimple s přidružené svazky zařízení StorSimple musí nastavit ale není zapnutá.
-   Budete potřebovat název prostředku StorSimple, registrační klíč ID zařízení zásad název a zálohování.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Ke spuštění nebo odstranění snímku cloudu

1. [Nainstalujte prostředí Azure PowerShell](/powershell/azure/overview).
2. Stažení a instalace [spravovat CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) skript prostředí PowerShell.
3. Na serveru, který spouští skript spusťte prostředí PowerShell jako správce. Ujistěte se, že spustíte skript s `-WhatIf $true` chcete zobrazit, jaké změny skript bude. Po dokončení ověření předat `-WhatIf $false`. Spustit následující příkaz:
```powershell
.\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
```
4. Chcete-li přidat skript do úlohy zálohování, upravte úlohu Veeam rozšířené možnosti.

    ![Karta skripty Veeam zálohování Upřesnit nastavení](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Doporučujeme vám spustit zásady zálohování StorSimple cloudových snímků jako skript následného zpracování na konci každodenní úlohy zálohování. Další informace o tom, jak zálohovat a obnovovat prostředí aplikace pro zálohování, aby vyhovovaly plánovaný bod obnovení a RTO, obraťte se na vaše zálohy architekti.

## <a name="storsimple-as-a-restore-source"></a>StorSimple jako zdroj obnovení

Obnoví z pracovní zařízení StorSimple jako obnovení ze všech zařízení s blokovým úložištěm. Rychlostmi cloudu dojde k obnovení dat, která je vrstvené do cloudu. Pro místní data dojde k obnovení rychlostí místní disk zařízení.

S Veeam získáte rychlý, podrobné, souborů obnovení prostřednictvím zařízení StorSimple pomocí předdefinovaných explorer zobrazení v konzole Veeam. Průzkumníci Veeam použijte k obnovení jednotlivých položek, jako je e-mailové zprávy, objektů služby Active Directory a SharePoint – položky ze zálohy. Obnovení lze provést bez přerušení místní počítač. Můžete také provést v daném okamžiku obnovení pro databáze Azure SQL Database a Oracle. Veeam a StorSimple provést proces obnovení na úrovni položek z Azure rychlé a snadné. Informace o tom, jak provést obnovení, naleznete v dokumentaci k Veeam:

- Pro [Exchange Server](https://www.veeam.com/microsoft-exchange-recovery.html)
- Pro [služby Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html)
- Pro [systému SQL Server](https://www.veeam.com/microsoft-sql-server-explorer.html)
- Pro [služby SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- Pro [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple převzetí služeb při selhání a zotavení po havárii

> [!NOTE]
> Pro scénáře cíl zálohy zařízení StorSimple cloudu nepodporuje jako cíl obnovení.

Havárie může být způsobeno různých faktorech. V následující tabulce jsou uvedeny běžné scénáře zotavení po havárii.

| Scénář | Dopad | Postup obnovení | Poznámky |
|---|---|---|---|
| Selhání zařízení StorSimple | Operace zálohování a obnovení jsou přerušena. | Nahraďte zařízení se nezdařilo a provádět [StorSimple převzetí služeb při selhání a zotavení po havárii](storsimple-device-failover-disaster-recovery.md). | Pokud potřebujete provést obnovení po obnovení zařízení, úplné datové sady pracovní se načítají z cloudu do nového zařízení. Všechny operace jsou rychlostmi cloudu. Index a katalog opětovná kontrola procesu může dojít k všechny zálohovací sklady na kontroloval a vyžádat z vrstvy cloudu k vrstvě místní zařízení, což může být zdlouhavý proces. |
| Selhání serveru Veeam | Operace zálohování a obnovení jsou přerušena. | Znovu sestavte zálohování serveru a proveďte obnovení databáze podle popisu v [centrum pro nápovědu Veeam (Technická dokumentace)](https://www.veeam.com/documentation-guides-datasheets.html).  | Musíte znovu sestavit nebo obnovit Veeam server na lokalitě pro obnovení po havárii. Obnovte databázi do posledního bodu. Pokud obnovenou databázi Veeam není synchronizované s vaší nejnovější úlohy zálohování, je třeba indexování a do katalogu. Tento index a katalog opětovná kontrola procesu může dojít k všechny zálohovací sklady na kontroloval a vyžádat z vrstvy cloudu k vrstvě místní zařízení. Díky tomu další časově náročný. |
| Selhání lokality, který vede ke ztrátě zálohování serveru a StorSimple | Operace zálohování a obnovení jsou přerušena. | Nejprve obnovit StorSimple a potom obnovte Veeam. | Nejprve obnovit StorSimple a potom obnovte Veeam. Pokud potřebujete provést obnovení po obnovení zařízení, pracovní úplné datových sad se načítají z cloudu do nového zařízení. Všechny operace jsou rychlostmi cloudu. |


## <a name="references"></a>Odkazy

Pro tento článek se odkazuje v následujících dokumentech:

- [Funkce multipath vstupně-výstupní operace instalace zařízení StorSimple](storsimple-configure-mpio-windows-server.md)
- [Scénáře úložiště: dynamické zajišťování](http://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Pomocí GPT jednotky](http://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Nastavení stínových kopií pro sdílené složky](http://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak [obnovení ze zálohovacího skladu](storsimple-restore-from-backup-set-u2.md).
- Další informace o tom, jak provést [zařízení převzetí služeb při selhání a zotavení po havárii](storsimple-device-failover-disaster-recovery.md).
