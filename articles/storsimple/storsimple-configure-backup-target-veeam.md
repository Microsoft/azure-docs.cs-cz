---
title: StorSimple řady 8000 jako cíl zálohování s Veeam | Dokumentace Microsoftu
description: Popisuje konfiguraci cíl zálohování StorSimple s Veeam.
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
ms.openlocfilehash: f06b74493bad546997f82ed6eef0a89cffb7c75b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51261974"
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>StorSimple s Veeam jako cíl zálohování

## <a name="overview"></a>Přehled

Azure StorSimple je řešení hybridního cloudového úložiště od Microsoftu. StorSimple řeší složitosti exponenciálního nárůstu dat pomocí účtu služby Azure Storage jako rozšíření místního řešení a automaticky vrstvení dat napříč úložiště v místním a cloudovým úložištěm.

V tomto článku se podíváme na integraci StorSimple s Veeam a osvědčené postupy pro integraci obou řešení. Doporučení Usnadňujeme také o tom, jak nastavit Veeam nejlépe integrovat StorSimple. Jsme smyslu můžete využít osvědčené postupy Veeam, zálohování architekty a správce pro nejlepší způsob, jak nastavit Veeam splňovat požadavky jednotlivých záloh a smlouvy o úrovni služeb (SLA).

I když jsme ukazují postup konfigurace a klíčových konceptů, tento článek není podrobný Průvodce konfigurace nebo instalace. Jsme předpokládají, že základní komponenty a infrastruktury ve funkčním stavu a připravena k podpoře koncepty, které jsme popsali.

### <a name="who-should-read-this"></a>Kdo by měl najdete v tomto?

Informace v tomto článku bude nejužitečnější zálohování správci, Správci úložiště a úložiště architekty, znalostmi úložiště, Windows Server 2012 R2, Ethernet, cloudových služeb a Veeam.

### <a name="supported-versions"></a>Podporované verze

-   Sada Veeam 9 a novějších verzích
-   [StorSimple Update 3 a novějších verzích](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Proč StorSimple jako cíl zálohování?

StorSimple je dobrou volbou pro cíl zálohování, protože:

-   Poskytuje standardní, místní úložiště pro zálohování aplikací pro použití jako cíl pro rychlé zálohování, bez nutnosti jakkoli měnit. StorSimple taky můžete použít pro rychlé obnovení poslední zálohy.
-   Jeho cloudu ovládání datových vrstev na bezproblémově integruje s účtem úložiště Azure cloud používání nákladově efektivní úložiště Azure.
-   Automaticky poskytuje úložiště mimo pracoviště pro zotavení po havárii.


## <a name="key-concepts"></a>Klíčové koncepty

Stejně jako u jakékoli řešení úložiště, pečlivě hodnocení výkonu úložiště řešení, smlouvy o úrovni služeb, počet změn a růstu potřeb kapacity je zásadní pro úspěch. Hlavním cílem je, že zavedením cloudovou vrstvu, čas přístupu a propustnosti na cloud play klíčovou roli v schopnost StorSimple fungovala správně.

StorSimple je navržená k poskytování úložiště pro aplikace, které pracují na jasně definovaných pracovního sadě dat (horká data). V tomto modelu pracovní sadu dat je uložena v místních vrstvách a zbývající nepracovní/studeného nebo archivovat sadu dat je Vrstvená do cloudu. Tento model je vyjádřena na následujícím obrázku. Téměř stejné zelená čára představuje data uložená v místních vrstvách zařízení StorSimple. Červená čára představuje celkové množství dat uložených v řešení StorSimple ve všech vrstvách. Mezera mezi paušální zelenou čáru a exponenciální red křivky představuje celkové množství dat uložených v cloudu.

**Ovládání datových vrstev na StorSimple**
![diagram vrstev StorSimple](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

Tato architektura v úvahu zjistíte, že StorSimple je ideální pro provoz jako cíl zálohování. Můžete používat StorSimple pro:

-   Nejčastěji se vyskytujících obnovení proveďte z místní pracovní sadu dat.
-   Použijte cloud pro zotavení po havárii mimo pracoviště a starší data, kde jsou méně časté zálohování.

## <a name="storsimple-benefits"></a>Výhody StorSimple

StorSimple poskytuje v místním řešení, která se bez problémů integruje s Microsoft Azure s využitím bezproblémový přístup k místním a cloudovém úložišti.

StorSimple využívá automatické vrstvení mezi místní zařízení, jehož zařízení SSD (Solid-State Drive) a sériově připojené úložiště SCSI (SAS) a Azure Storage. Automatické vrstvení udržuje často používaná data místní, vrstev SSD a SAS. Přesune zřídka používaná data do služby Azure Storage.

StorSimple nabízí tyto výhody:

-   Jedinečné deduplikace a komprese algoritmy, které díky cloudu budete aplikace dosáhnout úrovně bezprecedentní odstranění duplicitních dat
-   Vysoká dostupnost
-   Geografickou replikaci s použitím Azure geografické replikace
-   Integrace s Azure
-   Šifrování dat v cloudu
-   Zlepšení zotavení po havárii a dodržování předpisů

I když StorSimple v podstatě představuje dva scénáře nasazení hlavní (primární, záložní cíl a sekundární záložní cíl), je jednoduché, zařízení s blokovým úložištěm. StorSimple nemá všechny komprese a odstranění duplicit. Bez problémů odešle a načítá data mezi cloudem a aplikace a systém souborů.

Další informace o StorSimple najdete v tématu [řady StorSimple 8000 series: řešení hybridního cloudového úložiště](storsimple-overview.md). Navíc můžete zkontrolovat [technických specifikací řady StorSimple 8000](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Použití StorSimple zařízení jako cíl zálohování je podporováno pouze pro StorSimple 8000 Update 3 a novějších verzích.

## <a name="architecture-overview"></a>Přehled architektury

Následující tabulky popisují základní pokyny k architektuře modelu zařízení.

**StorSimple kapacity pro místní a cloudové úložiště**

| Kapacita úložiště | 8100 | 8600 |
|---|---|---|
| Kapacita místního úložiště | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Kapacita cloudového úložiště | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* Velikost úložiště předpokládá bez odstranění duplicitních dat nebo kompresi.

**StorSimple kapacity pro primární a sekundární zálohování**

| Zálohování scénář  | Kapacita místního úložiště  | Kapacita cloudového úložiště  |
|---|---|---|
| Primární zálohu  | Poslední zálohy uložené v místním úložišti pro rychlé obnovení pro splnění cíle bodu obnovení (RPO) | Zálohy historie (RPO) vejde kapacity cloudu |
| Sekundární zálohování | Sekundární kopii zálohovaných dat můžou být uložené v cloudu kapacity  | neuvedeno  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple jako primární cíl zálohování

V tomto scénáři se zobrazí svazky zařízení StorSimple do zálohovací aplikace jako výhradní úložiště pro zálohy. Následující obrázek znázorňuje architekturu řešení, ve kterém všechna zálohování pomocí StorSimple vrstvené svazky pro zálohování a obnovení.

![StorSimple jako primární, záložní cíl Logický diagram](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Primární cíl zálohování logických kroků

1.  Záložní server kontaktuje cílovém agentovi pro zálohování a zálohování agent odesílá data na záložní server.
2.  Záložní server zapíše data do StorSimple vrstvené svazky.
3.  Zálohování serveru aktualizuje databázi katalogu a pak dokončí úlohu zálohování.
4.  Snímek skript spustí cloud snapshot Manageru zařízení StorSimple (spuštění nebo odstranění).
5.  Záložní server Odstraní vypršela platnost záloh na základě zásad uchovávání informací.

### <a name="primary-target-restore-logical-steps"></a>Primární cíl obnovení logických kroků

1.  Zálohování serveru spustí obnovení příslušná data z úložiště úložiště.
2.  Agenta zálohování přijímá data ze zálohy serveru.
3.  Záložní server dokončí úlohu obnovení.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple jako sekundární záložní cíl

V tomto scénáři svazky zařízení StorSimple se používá především pro dlouhodobé uchovávání a archivaci.

Následující obrázek ukazuje architekturu, ve které prvotní zálohy a obnoví vysoce výkonné cílový svazek. Tyto zálohy jsou zkopírovány a archivovat do StorSimple vrstveného svazku podle nastaveného plánu.

Je důležité pro nastavení velikosti svazku výkonné tak, že dokáže zpracovat požadavky vaší uchování zásad kapacitu a výkon.

![StorSimple jako sekundární záložní cíl Logický diagram](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Sekundární cíl zálohování logických kroků

1.  Záložní server kontaktuje cílovém agentovi pro zálohování a zálohování agent odesílá data na záložní server.
2.  Záložní server zapíše data do vysoce výkonné úložiště.
3.  Zálohování serveru aktualizuje databázi katalogu a pak dokončí úlohu zálohování.
4.  Záložní server zkopíruje do StorSimple na základě zásad uchovávání záloh.
5.  Snímek skript spustí cloud snapshot Manageru zařízení StorSimple (spuštění nebo odstranění).
6.  Záložní server Odstraní vypršela platnost záloh na základě zásad uchovávání informací.

### <a name="secondary-target-restore-logical-steps"></a>Sekundární cíl obnovení logických kroků

1.  Zálohování serveru spustí obnovení příslušná data z úložiště úložiště.
2.  Agenta zálohování přijímá data ze zálohy serveru.
3.  Záložní server dokončí úlohu obnovení.

## <a name="deploy-the-solution"></a>Nasazení řešení

Nasazení řešení vyžaduje tři kroky:

1. Připravte síťové infrastruktury.
2. Nasazení zařízení StorSimple jako cíl zálohování.
3. Nasazení řešení Veeam.

Jednotlivé kroky jsou popsány podrobně v následující části.

### <a name="set-up-the-network"></a>Nastavit síť

Protože StorSimple je řešení, která je integrovaná s Azure cloud, StorSimple vyžaduje aktivní a pracovní připojení ke cloudu Azure. Toto připojení se používá pro operace, jako je cloudové snímky, Správa dat a přenos metadata a na úroveň starší, méně používaná data do cloudového úložiště Azure.

Řešení tak, aby fungoval optimálně doporučujeme, abyste postupovali podle těchto síťových osvědčených postupů:

-   Odkaz, který se připojuje k Azure StorSimple ovládání datových vrstev musí splňovat požadavky na šířku pásma. Můžete toho dosáhnout použitím potřebnou úroveň kvality služby (QoS) k vaší infrastruktuře čas přepínače tak, aby odpovídaly cíle bodu obnovení a obnovení smlouvy o úrovni služeb cíle (RTO).
-   Maximální latence přístupu k úložišti objektů Blob v Azure by měla být přibližně 80 ms.

### <a name="deploy-storsimple"></a>Nasazení StorSimple

Pokyny krok za krokem StorSimple nasazení najdete v tématu [nasazení zařízení StorSimple v místním](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Nasazení řešení Veeam

Osvědčené postupy instalace Veeam, naleznete v tématu [Veeam zálohování a osvědčené postupy replikace](https://bp.veeam.expert/), a přečtěte si uživatelskou příručku na [Centrum pro nápovědu Veeam (technické dokumentace)](https://www.veeam.com/documentation-guides-datasheets.html).

## <a name="set-up-the-solution"></a>Nastavení řešení

V této části ukážeme některé příklady konfigurace. Následující příklady a doporučení pro ilustraci nejvíce základní a základní implementaci. Tato implementace nemusí platit přímo k vašim konkrétním požadavkům zálohování.

### <a name="set-up-storsimple"></a>Nastavit StorSimple

| Úlohy nasazení StorSimple  | Další komentáře |
|---|---|
| Nasazení místního zařízení StorSimple. | Podporované verze: 3 a novější verze aktualizace. |
| Zapněte cíl zálohování. | Pomocí těchto příkazů zapnout nebo vypnout režim cíl zálohování a získat stav. Další informace najdete v tématu [připojit vzdáleně k zařízení StorSimple](storsimple-remote-connect.md).</br> Zapnout režim zálohování: `Set-HCSBackupApplianceMode -enable`. </br> Chcete-li vypnout režim zálohování: `Set-HCSBackupApplianceMode -disable`. </br> Chcete-li získat aktuální stav nastavení režim zálohování: `Get-HCSBackupApplianceMode`. |
| Vytvořte kontejner svazků běžné svazku, který uchovává zálohovaná data. Všechna data v kontejneru svazku se odstraňují duplicity. | Kontejnery svazků StorSimple definování domén odstranění duplicit.  |
| Vytvořte svazky zařízení StorSimple. | Vytvořte svazky s velikostí jako blízko očekávané využití nejdříve, protože velikost svazku ovlivňuje dobu trvání snímku v cloudu. Informace o tom, jak upravit velikost svazku, přečtěte si informace o [zásady uchovávání informací](#retention-policies).</br> </br> Použití StorSimple vrstvené svazky a vyberte **použít tento svazek pro archivní data s méně častým** zaškrtávací políčko. </br> Použití pouze místně připojené svazky se nepodporuje. |
| Vytvoření jedinečné zásady zálohování StorSimple pro všechny svazky, záložní cíl. | Zásady zálohování StorSimple definuje skupiny konzistence svazků. |
| Zakážete plán vyprší snímky. | Snímky se spouštějí jako operaci následného zpracování. |

### <a name="set-up-the-host-backup-server-storage"></a>Nastavení úložiště zálohování serveru hostitele

Nastavení úložiště zálohování serveru hostitele podle následujících pokynů:  

- Nepoužívejte rozložené svazky (vytvořený ve správě disků Windows). Rozložené svazky nepodporují.
- Formátujte svazky systému souborů NTFS pomocí velikosti 64 KB alokační jednotky.
- Namapujte přímo na Veeam server svazky zařízení StorSimple.
    - Pomocí iSCSI pro fyzické servery.


## <a name="best-practices-for-storsimple-and-veeam"></a>Osvědčené postupy pro StorSimple a Veeam

Nastavení řešení podle pokynů v následujících částech.

### <a name="operating-system-best-practices"></a>Osvědčené postupy operačního systému

-   Zakážete šifrování Windows serveru a odstranění duplicitních dat pro systém souborů NTFS.
-   Zakážete defragmentace serveru systému Windows na svazky zařízení StorSimple.
-   Zakážete indexování serveru systému Windows na svazky zařízení StorSimple.
-   Spusťte antivirovou kontrolu na zdrojovém hostiteli (ne u svazků StorSimple).
-   Vypnout výchozí [údržby systému Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) ve Správci úloh. To lze proveďte v jednom z následujících způsobů:
    - Vypněte configuratoru údržby v Plánovači úloh Windows.
    - Stáhněte si [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) z webu Windows Sysinternals. Po stažení nástroje PsExec, spusťte prostředí Windows PowerShell jako správce a zadejte:
      ```powershell
      psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
      ```

### <a name="storsimple-best-practices"></a>Osvědčené postupy pro StorSimple

-   Ujistěte se, že zařízení StorSimple se aktualizuje na [s aktualizací Update 3 nebo novější](storsimple-install-update-3.md).
-   Izoluje přenosy iSCSI a cloudu. Použijte vyhrazený iSCSI připojení pro přenos dat mezi StorSimple a záložní server.
-   Ujistěte se, že zařízení StorSimple je vyhrazené cíl zálohování. Smíšené úlohy nejsou podporovány, protože ovlivňují RTO a RPO.

### <a name="veeam-best-practices"></a>Sada Veeam osvědčené postupy

-   Sada Veeam databáze by měla být místní pro server a není umístěný na svazek StorSimple.
-   Pro zotavení po havárii proveďte zálohu databáze Veeam na svazek StorSimple.
-   Podporujeme Veeam úplných a přírůstkových záloh pro toto řešení. Doporučujeme vám, nepoužívejte syntetické a rozdílové zálohy.
-   Zálohování datových souborů by mělo obsahovat pouze data pro konkrétní úlohu. Například žádné médium připojí přes různé úlohy jsou povoleny.
-   Vypněte úlohu ověření. V případě potřeby ověřování má být naplánováno po nejnovější úlohy zálohování. Je důležité pochopit, že tato úloha má vliv okno zálohování.
-   Zapněte předběžné přidělení média.
-   Ujistěte se, že je zapnutá paralelní zpracování.
-   Vypněte kompresi.
-   Vypněte odstranění duplicitních dat u úlohy zálohování.
-   Nastavit optimalizace na **cílové sítě LAN**.
-   Zapnout **vytvořit úplnou zálohu aktivní** (každé 2 týdny).
-   Úložiště zálohování, nastavení **použijte záložní soubory pro jednotlivé virtuální počítače**.
-   Nastavte **pomocí různých datových proudů nahrávání na úlohu** k **8** (je povoleno maximálně 16). Upravte tento počet navýšit nebo snížit kapacitu podle využití procesoru na zařízení StorSimple.

## <a name="retention-policies"></a>Zásady uchovávání informací

Jeden z nejběžnějších typů zásad uchovávání záloh je zásada Dědečka ze strany otce a SYN (GFS). V zásadách GFS přírůstkové zálohy se provádí denně a úplné zálohy jsou prováděny každý týden a každý měsíc. Výsledkem zásad šest StorSimple vrstvené svazky: jeden svazek obsahuje Týdenní, měsíční a roční úplné zálohy. pět svazky ukládání denních přírůstkových záloh.

V následujícím příkladu používáme GFS otočení. V příkladu se předpokládá následující:

-   Bez zajištěná nebo komprimovaných dat se používá.
-   Úplné zálohování je 1 TB.
-   Každodenní přírůstkové zálohování je 500 GB.
-   Čtyři týdenní zálohy se uchovávají po dobu jednoho měsíce.
-   Dvanáct měsíční zálohy zůstanou po dobu jednoho roku.
-   Jeden roční zálohu uchovávají po dobu 10 let.

Podle předchozí předpoklady, vytvořte 26-TiB StorSimple vrstveného svazku pro měsíční a roční úplné zálohování. Vytvoření 5 TiB StorSimple vrstveného svazku pro každý přírůstkové zálohování denně.

| Typ zálohování uchovávání | Velikost (TiB) | Násobitel GFS\* | Celková kapacita (TB)  |
|---|---|---|---|
| Týdenní úplné | 1 | 4  | 4 |
| Každodenní přírůstkové | 0,5 | 20 (cykly stejný počet týdnů za měsíc) | 12 (2 pro další kvótu) |
| Měsíční úplné | 1 | 12 | 12 |
| Ročně úplné | 1  | 10 | 10 |
| Požadavek GFS |   | 38 |   |
| Další kvótu  | 4  |   | 42 celkový požadavek GFS  |
\* Násobitel GFS je počet kopií, které potřebujete k ochraně a uchovat pro splnění požadavků na zásady zálohování.

## <a name="set-up-veeam-storage"></a>Nastavení úložiště Veeam

### <a name="to-set-up-veeam-storage"></a>Nastavení úložiště Veeam

1.  V konzole Veeam zálohování a replikace v **úložiště nástroje**, přejděte na stránku **infrastruktura zálohování**. Klikněte pravým tlačítkem na **úložišť zálohování**a pak vyberte **přidat úložiště zálohování**.

    ![Konzola pro správu Veeam, stránka úložiště zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  V **nové úložiště zálohování** dialogového okna zadejte název a popis pro úložiště. Vyberte **Další**.

    ![Sada Veeam správy konzoly, název a popis stránky](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  Pro typ, vyberte **Microsoft Windows server**. Vyberte server, Veeam. Vyberte **Další**.

    ![Konzola pro správu Veeam, vyberte typ úložiště zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  Chcete-li určit **umístění**, procházet a vyberte svazek. Vyberte **omezení na maximální souběžných úloh:** zaškrtněte políčko a nastavte hodnotu na **4**. Tím se zajistí, že jsou zpracovávány pouze čtyři virtuální disky současně, zatímco se zpracuje každý virtuální počítač (VM). Vyberte **Upřesnit** tlačítko.

    ![Konzola pro správu Veeam, vyberte svazek](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  V **nastavení kompatibility úložiště** dialogové okno, vyberte **použijte záložní soubory pro jednotlivé virtuální počítače** zaškrtávací políčko.

    ![Konzola pro správu Veeam, nastavení kompatibility pro úložiště](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  V **nové úložiště zálohování** dialogové okno, vyberte **povolte službu vPower systému souborů NFS na server připojit (doporučeno)** zaškrtávací políčko. Vyberte **Další**.

    ![Konzola pro správu Veeam, stránka úložiště zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Zkontrolujte nastavení a pak vyberte **Další**.

    ![Konzola pro správu Veeam, stránka úložiště zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    Úložiště se přidá do serveru Veeam.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Nastavit StorSimple jako primární cíl zálohování

> [!IMPORTANT]
> Rychlostí cloudu dojde k obnovení dat ze zálohy, která byla Vrstvená do cloudu.

Následující obrázek znázorňuje mapování svazku typické pro úlohu zálohování. V takovém případě všechny týdenní zálohy namapovat na sobotu celého disku a mapování přírůstkové zálohování na disky přírůstkové pondělí až pátek. Všechno, co zálohování a obnovení jsou ze StorSimple vrstveného svazku.

![Primární cíl zálohování konfigurace Logický diagram](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple jako cíl zálohování primární GFS naplánovat příklad

Tady je příklad plánu otočení GFS čtyři týdny, měsíční nebo roční:

| Typ frekvence/zálohování | Úplná | Přírůstkové (1-5 dní)  |   
|---|---|---|
| Každý týden (1 – 4 týdny) | Sobota | Pondělí – pátek |
| Měsíční  | Sobota  |   |
| Ročně | Sobota  |   |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>Veeam úlohy zálohování přiřadit svazky zařízení StorSimple

Pro scénář primární, záložní cíl vytvořte každodenní úlohu s primární svazek StorSimple s Veeam. Pro scénář sekundární záložní cíl vytvořte každodenní úlohu pomocí přímé připojené úložiště (DAS), úložiště NAS (Network Attached) nebo pouze úložiště Bunch disky (JBOD).

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>Veeam úlohy zálohování přiřadit svazky zařízení StorSimple

1.  V konzole Veeam zálohování a replikaci vyberte **zálohování a replikace**. Klikněte pravým tlačítkem na **zálohování**a pak vyberte **VMware** nebo **Hyper-V**, v závislosti na vašem prostředí.

    ![Konzola pro správu Veeam, novou úlohu zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  V **novou úlohu zálohování** dialogového okna zadejte název a popis pro každodenní úlohu zálohování.

    ![Konzola pro správu Veeam, nová stránka úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Vyberte virtuální počítač k zálohování.

    ![Konzola pro správu Veeam, nová stránka úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Vyberte hodnoty, které chcete pro **zálohování proxy** a **úložiště zálohování**. Vyberte hodnotu pro **bodů obnovení můžete zachovat na disku** podle RPO a RTO definice pro vaše prostředí na místně připojené úložiště. Vyberte **Upřesnit**.

    ![Konzola pro správu Veeam, nová stránka úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. V **Upřesnit nastavení** dialogovém okně **zálohování** kartu, vyberte možnost **přírůstkové**. Ujistěte se, že **pravidelně vytvářet syntetické úplné zálohy** zrušení zaškrtnutí políčka. Vyberte **pravidelně vytvářet aktivní úplné zálohy** zaškrtávací políčko. V části **aktivní úplná záloha**, vyberte **každý týden ve vybrané dny** zaškrtávací políčko pro sobota.

    ![Konzoly pro správu Veeam novou úlohu zálohování Upřesnit nastavení](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. Na **úložiště** kartu, ujistěte se, že **povolení odstranění duplicitních dat vložené** zrušení zaškrtnutí políčka. Vyberte **vyloučení odkládací soubor bloky** zaškrtněte políčko a vybrat **vyloučení odstranit soubor bloky** zaškrtávací políčko. Nastavte **úroveň komprese** k **žádný**. Vyvážený výkonu a odstraňování duplicitních dat, nastavte **optimalizace úložiště** k **cílové sítě LAN**. Vyberte **OK**.

    ![Konzoly pro správu Veeam novou úlohu zálohování Upřesnit nastavení](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Informace o nastavení odstraňování duplicitních dat a provádí kompresi, Veeam najdete v tématu [komprese dat a odstranění duplicit](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html).

7.  V **Upravit úlohu zálohování** dialogovém okně můžete vybrat **povolit s ohledem na aplikace zpracování** (volitelné) zaškrtněte políčko.

    ![Konzola pro správu Veeam, nová stránka hosta zpracování úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Nastavte plán pro spuštění jednou denně v době, můžete zadat.

    ![Konzola pro správu Veeam, nová stránka plán úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Nastavit službu StorSimple jako sekundární záložní cíl

> [!NOTE]
> Rychlostí cloudu dojde k obnovení dat ze zálohy, která byla Vrstvená do cloudu.

V tomto modelu musí mít úložných médií (jiné než StorSimple) která bude sloužit jako dočasná mezipaměť. Například můžete použít redundantní pole nezávislých disků (RAID) svazek tak, aby vyhovovaly místa, vstupní a výstupní (I/O) a šířku pásma. Doporučujeme používat RAID 5, 50 a 10.

Následující obrázek ukazuje typické krátkodobé uchování místní (pro server) a dlouhodobé uchovávání archivu svazky. V tomto scénáři všechna zálohování spustit na místní (pro server) svazek RAID. Tyto zálohy jsou pravidelně duplicitní a archivovat do archivu svazku. Je důležité pro nastavení velikosti místní (pro server,) svazku RAID, takže zvládne krátkodobé uchování požadavky kapacitu a výkon.

![StorSimple jako sekundární záložní cíl Logický diagram](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple jako příklad GFS sekundární záložní cíl

Následující tabulka ukazuje, jak nastavit zálohování pro místní i StorSimple disky. Obsahuje požadavky na kapacitu jednotlivých a celkový počet.

| Typ zálohování a uchovávání | Úložiště | Velikost (TiB) | Násobitel GFS | Celková kapacita\* (TB) |
|---|---|---|---|---|
| Týden 1 (úplných a přírůstkových) |Místní disk (krátkodobé)| 1 | 1 | 1 |
| StorSimple týdnů 2 až 4 |StorSimple disku (dlouhodobé) | 1 | 4 | 4 |
| Měsíční úplné |StorSimple disku (dlouhodobé) | 1 | 12 | 12 |
| Ročně úplné |StorSimple disku (dlouhodobé) | 1 | 1 | 1 |
|Požadavek na velikost svazků GFS |  |  |  | 18*|
\* Celková kapacita zahrnuje 17 TiB StorSimple disky a 1 TiB místní svazek RAID.


### <a name="gfs-example-schedule"></a>GFS Příklad plánu

Otočení GFS týdenní, měsíční a roční plán

| Týden | Úplná | Přírůstkové dne 1 | Přírůstkové den 2 | Přírůstkové den 3 | Přírůstkové den 4 | Přírůstková denně, 5 |
|---|---|---|---|---|---|---|
| 1 týden | Místní svazek RAID  | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID |
| Týdnu 2 | StorSimple týdnů 2 až 4 |   |   |   |   |   |
| Týdnu 3 | StorSimple týdnů 2 až 4 |   |   |   |   |   |
| Týdnu 4 | StorSimple týdnů 2 až 4 |   |   |   |   |   |
| Měsíční | Každý měsíc StorSimple |   |   |   |   |   |
| Ročně | Každý rok StorSimple  |   |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>Úloha kopírování Veeam přiřadit svazky zařízení StorSimple

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>Úloha kopírování Veeam přiřadit svazky zařízení StorSimple

1.  V konzole Veeam zálohování a replikaci vyberte **zálohování a replikace**. Klikněte pravým tlačítkem na **zálohování**a pak vyberte **VMware** nebo **Hyper-V**, v závislosti na vašem prostředí.

    ![Konzola pro správu Veeam, nová stránka projektu záložní kopii.](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  V **novou úlohu zálohování kopírování** dialogového okna zadejte název a popis pro úlohu.

    ![Konzola pro správu Veeam, nová stránka projektu záložní kopii.](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Vyberte virtuální počítače, které chcete zpracovat. Vyberte ze zálohy a pak vyberte denní zálohování, který jste vytvořili dříve.

    ![Konzola pro správu Veeam, nová stránka projektu záložní kopii.](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  Vyloučení objektů z úloh záložní kopii v případě potřeby.

5.  Vyberte záložní úložiště a nastavte hodnotu **zachovat body obnovení**. Je nutné vybrat **zachovat následující body obnovení pro archivační účely** zaškrtávací políčko. Definovat frekvenci zálohování a pak vyberte **Upřesnit**.

    ![Konzola pro správu Veeam, nová stránka projektu záložní kopii.](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Zadejte následující upřesňující nastavení:

    * Na **údržby** kartu, vypněte úložiště úrovně poškození guard.

    ![Veeam Konzola pro správu, Nová úloha záložní kopie rozšířené nastavení stránky](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * Na **úložiště** kartu, ujistěte se, že odstranění duplicitních dat a provádí kompresi, jsou vypnuté.

    ![Veeam Konzola pro správu, Nová úloha záložní kopie rozšířené nastavení stránky](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Určete, že je přenos dat s přímým přístupem.

8.  Definujte plán okno zálohy podle vašich potřeb a pak dokončete průvodce.

Další informace najdete v tématu [vytvořit záložní kopii úlohy](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>Cloudové snímky StorSimple

Cloudové snímky StorSimple chránit data uložená v zařízení StorSimple. Vytváří se snímek v cloudu je ekvivalentní přesouvání místní záložní pásky do zařízení mimo pracoviště. Pokud používáte geografické redundantní úložiště Azure, vytváří se snímek v cloudu je ekvivalentní přesouvání záložní pásky k více lokalitám. Pokud budete potřebovat obnovení po havárii zařízení, může být online jiného zařízení StorSimple a služeb při selhání. Po převzetí služeb při selhání bude mít přístup k datům (cloud rychlostí) z nejnovější snímek v cloudu.

Následující část popisuje, jak vytvořit krátké skript ke spuštění a odstranění StorSimple cloudové snímky během zálohování následného zpracování.

> [!NOTE]
> Snímky vytvořené ručně nebo programově podle zásad vypršení platnosti StorSimple snapshot. Tyto snímky musíte ručně nebo programově odstranit.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Spuštění a odstranění cloudové snímky pomocí skriptu

> [!NOTE]
> Pečlivě vyhodnoťte dodržování předpisů a dopady uchování dat před odstraněním StorSimple snapshot. Další informace o způsobu spuštění předzálohovacího skriptu najdete v dokumentaci Veeam.


### <a name="backup-lifecycle"></a>Zálohování životního cyklu

![Diagram zálohování životního cyklu](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

### <a name="requirements"></a>Požadavky

-   Server, na kterém běží skriptu musí mít přístup k prostředkům cloudu Azure.
-   Uživatelský účet musí mít potřebná oprávnění.
-   Zásady zálohování StorSimple se přidružené svazky zařízení StorSimple musíte nastavit, ale není zapnutá.
-   Budete potřebovat název prostředku StorSimple, registrační klíč, ID zařízení zásadu název a zálohování.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Ke spuštění nebo odstranění snímek v cloudu

1. [Nainstalujte prostředí Azure PowerShell](/powershell/azure/overview).
2. Stažení a instalace [spravovat CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) skript prostředí PowerShell.
3. Na serveru, na kterém běží, spuštění skriptu prostředí PowerShell jako správce. Zkontrolujte, jestli jste spustili skript s `-WhatIf $true` chcete zobrazit, co se změní skript provede. Po dokončení ověření předat `-WhatIf $false`. Spustit následující příkaz:
```powershell
.\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
```
4. Chcete-li přidat skript pro vaši úlohu zálohování, upravit úlohu Veeam pokročilé možnosti.

    ![Karta skripty Veeam zálohování upřesňující nastavení](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Doporučujeme, abyste spustili zásady zálohování StorSimple cloudových snímků jako následného zpracování skriptu na konci vaší každodenní úlohy zálohování. Další informace o tom, jak zálohovat a obnovovat vaše aplikace pro zálohování prostředí, které pomáhá splnit i ty RPO a RTO, obraťte se na zálohování architect.

## <a name="storsimple-as-a-restore-source"></a>StorSimple jako zdroj obnovení

Obnoví ze pracovní zařízení StorSimple jako je obnovení z jakékoli zařízení s blokovým úložištěm. Rychlostí cloudu dojde k obnovení dat, která je Vrstvená do cloudu. Pro místní data dojde k obnovení rychlostí místní disk zařízení.

S Veeam získáte rychlý, detailní a úrovni souboru obnovení prostřednictvím StorSimple prostřednictvím vestavěné explorer zobrazení v konzole Veeam. Průzkumníci Veeam použijte pro obnovení jednotlivých položek, jako jsou e-mailové zprávy, objektů služby Active Directory a položky služby SharePoint ze zálohy. Obnovení můžete udělat bez narušení místních virtuálních počítačů. Můžete také provést obnovení bodu v čase pro databáze Azure SQL Database a Oracle. Sada Veeam a Azure StorSimple je proces obnovení na úrovni položky z Azure, rychle a jednoduše. Informace o tom, jak provést obnovení, najdete v dokumentaci k Veeam:

- Pro [Exchange Server](https://www.veeam.com/microsoft-exchange-recovery.html)
- Pro [služby Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html)
- Pro [systému SQL Server](https://www.veeam.com/microsoft-sql-server-explorer.html)
- Pro [služby SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- Pro [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple převzetí služeb při selhání a zotavení po havárii

> [!NOTE]
> Pro zálohování cílové scénáře řešení StorSimple Cloud Appliance není podporováno jako cíl obnovení.

Havárii může být způsobeno různých faktorech. V následující tabulce jsou uvedeny běžné scénáře zotavení po havárii.

| Scénář | Dopad | Postup obnovení | Poznámky |
|---|---|---|---|
| Selhání zařízení StorSimple | Operace zálohování a obnovení je přerušeno. | Nahraďte neúspěšných zařízení a provádět [StorSimple převzetí služeb při selhání a zotavení po havárii](storsimple-device-failover-disaster-recovery.md). | Pokud potřebujete provést obnovení po obnovení zařízení, úplná data pracovní sady jsou načteny z cloudu do nového zařízení. Všechny operace jsou rychlostí cloudu. Index a katalog opětovná kontrola procesu může dojít k všechny zálohovacích skladů určených k zkontrolovat a získaná z vrstvy cloudu do úrovně místního zařízení, což může být časově náročný proces. |
| Selhání serveru Veeam | Operace zálohování a obnovení je přerušeno. | Znovu sestavit server zálohování a obnovení databáze podle popisu v [centrum pro nápovědu Veeam (technické dokumentace)](https://www.veeam.com/documentation-guides-datasheets.html).  | Musíte znovu sestavit nebo obnovit Veeam serveru v lokalitě zotavení po havárii. Obnovení databáze do nejnovějšího bodu. Pokud není synchronizované s nejnovější úlohy zálohování obnovené databáze Veeam, je vyžadována indexování a katalogizace. Tento index a katalog opětovná kontrola procesu může dojít k všechny zálohovacích skladů určených k zkontrolovat a získaná z vrstvy cloudu do úrovně místního zařízení. To umožňuje dále náročné na čas. |
| Selhání lokality, který vede ke ztrátě záložní server a StorSimple | Operace zálohování a obnovení je přerušeno. | Nejprve obnovit StorSimple a potom obnovte Veeam. | Nejprve obnovit StorSimple a potom obnovte Veeam. Pokud potřebujete provést obnovení po obnovení zařízení, úplná data pracovní sady jsou načteny z cloudu do nového zařízení. Všechny operace jsou rychlostí cloudu. |


## <a name="references"></a>Odkazy

Pro účely tohoto článku bylo odkazováno v následujících dokumentech:

- [StorSimple více cest vstupně-výstupní operace instalace](storsimple-configure-mpio-windows-server.md)
- [Scénáře využití služby Storage: dynamické zajišťování](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Pomocí GPT jednotky](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Nastavení stínových kopií pro sdílené složky](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak [obnovení ze zálohovacího skladu](storsimple-restore-from-backup-set-u2.md).
- Další informace o tom, jak provádět [zařízení převzetí služeb při selhání a zotavení po havárii](storsimple-device-failover-disaster-recovery.md).
