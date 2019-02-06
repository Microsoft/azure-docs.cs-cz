---
title: StorSimple řady 8000 jako cíl zálohování pomocí zálohování Exec | Dokumentace Microsoftu
description: Popisuje konfiguraci cíl zálohování StorSimple s Veritas Backup Exec.
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
ms.date: 12/05/2016
ms.author: hkanna
ms.openlocfilehash: 8cde3402ef52747e61333c56903309259e07599a
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55747590"
---
# <a name="storsimple-as-a-backup-target-with-backup-exec"></a>StorSimple jako cíl zálohování se službou Backup Exec

## <a name="overview"></a>Přehled

Azure StorSimple je řešení hybridního cloudového úložiště od Microsoftu. StorSimple řeší složitosti exponenciálního nárůstu dat pomocí účtu služby Azure storage jako rozšíření místního řešení a automatickému vrstvení dat napříč úložiště v místním a cloudovým úložištěm.

V tomto článku se podíváme na integraci StorSimple s Veritas Backup Exec a osvědčené postupy pro integraci obou řešení. Doporučení Usnadňujeme také o tom, jak nastavit Backup Exec nejlépe integrovat StorSimple. Jsme smyslu můžete využít osvědčené postupy Veritas, zálohování architekty a správce pro nejlepší způsob, jak nastavit Backup Exec splňovat požadavky jednotlivých záloh a smlouvy o úrovni služeb (SLA).

I když jsme ukazují postup konfigurace a klíčových konceptů, tento článek není podrobný Průvodce konfigurace nebo instalace. Jsme předpokládají, že základní komponenty a infrastruktury ve funkčním stavu a připravena k podpoře koncepty, které jsme popsali.

### <a name="who-should-read-this"></a>Kdo by měl najdete v tomto?

Informace v tomto článku bude nejužitečnější zálohování správci, Správci úložiště a úložiště architekty, znalostmi úložiště, Windows Server 2012 R2, Ethernet, cloudových služeb a Backup Exec.

## <a name="supported-versions"></a>Podporované verze

-   [Backup Exec 16 a novějších verzích](http://backupexec.com/compatibility)
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
![diagram vrstev StorSimple](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

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

Další informace o StorSimple najdete v tématu [StorSimple řady 8000: Řešení hybridního cloudového úložiště](storsimple-overview.md). Navíc můžete zkontrolovat [technických specifikací řady StorSimple 8000](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Použití StorSimple zařízení jako cíl zálohování je podporováno pouze pro StorSimple 8000 Update 3 a novějších verzích.

## <a name="architecture-overview"></a>Přehled architektury

Následující tabulky popisují základní pokyny k architektuře modelu zařízení.

**StorSimple kapacity pro místní a cloudové úložiště**

| Kapacita úložiště       | 8100          | 8600            |
|------------------------|---------------|-----------------|
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

![StorSimple jako primární, záložní cíl Logický diagram](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

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

![StorSimple jako sekundární záložní cíl Logický diagram](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

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
3. Nasazení Backup Exec.

Jednotlivé kroky jsou popsány podrobně v následující části.

### <a name="set-up-the-network"></a>Nastavit síť

Protože StorSimple je řešení, která je integrovaná s Azure cloud, StorSimple vyžaduje aktivní a pracovní připojení ke cloudu Azure. Toto připojení se používá pro operace, jako je cloudové snímky, správu a přenosy metadata a na úroveň starší, méně používaná data do cloudového úložiště Azure.

Řešení tak, aby fungoval optimálně doporučujeme, abyste postupovali podle těchto síťových osvědčených postupů:

-   Odkaz, který se připojuje k Azure StorSimple ovládání datových vrstev musí splňovat požadavky na šířku pásma. Za tím účelem vztahují k vaší infrastruktuře potřebnou úroveň kvality služby (QoS) čas přepínače tak, aby odpovídaly cíle bodu obnovení a obnovení smlouvy o úrovni služeb cíle (RTO).
-   Maximální latence přístupu k úložišti objektů Blob v Azure by měla být přibližně 80 ms.

### <a name="deploy-storsimple"></a>Nasazení StorSimple

Podrobné pokyny StorSimple nasazení najdete v tématu [nasazení zařízení StorSimple v místním](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-backup-exec"></a>Nasazení Backup Exec

Osvědčené postupy instalace Backup Exec, naleznete v tématu [osvědčené postupy pro instalaci Backup Exec](https://www.veritas.com/content/support/en_US/doc/72686287-131623464-0/v70444238-131623464).

## <a name="set-up-the-solution"></a>Nastavení řešení

V této části ukážeme některé příklady konfigurace. Následující příklady a doporučení pro ilustraci nejvíce základní a základní implementaci. Tato implementace nemusí platit přímo k vašim konkrétním požadavkům zálohování.

### <a name="set-up-storsimple"></a>Nastavit StorSimple

| Úlohy nasazení StorSimple  | Další komentáře |
|---|---|
| Nasazení místního zařízení StorSimple. | Podporované verze: Aktualizací Update 3 a novějších verzích. |
| Zapněte cíl zálohování. | Pomocí těchto příkazů zapnout nebo vypnout režim cíl zálohování a získat stav. Další informace najdete v tématu [připojit vzdáleně k zařízení StorSimple](storsimple-remote-connect.md).</br> Zapnout režim zálohování: `Set-HCSBackupApplianceMode -enable`. </br> Chcete-li vypnout režim zálohování: `Set-HCSBackupApplianceMode -disable`. </br> Chcete-li získat aktuální stav nastavení režim zálohování: `Get-HCSBackupApplianceMode`. |
| Vytvořte kontejner svazků běžné svazku, který uchovává zálohovaná data. Všechna data v kontejneru svazku se odstraňují duplicity. | Kontejnery svazků StorSimple definování domén odstranění duplicit.  |
| Vytvořte svazky zařízení StorSimple. | Vytvořte svazky s velikostí jako blízko očekávané využití nejdříve, protože velikost svazku ovlivňuje dobu trvání snímku v cloudu. Informace o tom, jak upravit velikost svazku, přečtěte si informace o [zásady uchovávání informací](#retention-policies).</br> </br> Použití StorSimple vrstvené svazky a vyberte **použít tento svazek pro archivní data s méně častým** zaškrtávací políčko. </br> Použití pouze místně připojené svazky se nepodporuje. |
| Vytvoření jedinečné zásady zálohování StorSimple pro všechny svazky, záložní cíl. | Zásady zálohování StorSimple definuje skupiny konzistence svazků. |
| Zakážete plán vyprší snímky. | Snímky se spouštějí jako operaci následného zpracování. |

### <a name="set-up-the-host-backup-server-storage"></a>Nastavení úložiště zálohování serveru hostitele

Nastavení úložiště zálohování serveru hostitele podle následujících pokynů:  

- Nepoužívejte rozložené svazky (vytvořený ve správě disků Windows). Rozložené disky nejsou podporovány.
- Formátujte svazky systému souborů NTFS pomocí velikosti 64 KB alokační.
- Namapujte přímo na server Backup Exec svazky zařízení StorSimple.
    - Pomocí iSCSI pro fyzické servery.
    - Použijte průchozí disky pro virtuální servery.

## <a name="best-practices-for-storsimple-and-backup-exec"></a>Osvědčené postupy pro StorSimple a Backup Exec

Nastavení řešení podle pokynů v následujících částech.

### <a name="operating-system-best-practices"></a>Osvědčené postupy operačního systému

-   Zakážete šifrování Windows serveru a odstranění duplicitních dat pro systém souborů NTFS.
-   Zakážete defragmentace serveru systému Windows na svazky zařízení StorSimple.
-   Zakážete indexování serveru systému Windows na svazky zařízení StorSimple.
-   Spusťte antivirovou kontrolu na zdrojovém hostiteli (ne u svazků StorSimple).
-   Vypnout výchozí [údržby systému Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) ve Správci úloh. To lze proveďte v jednom z následujících způsobů:
   - Vypněte configuratoru údržby v Plánovači úloh Windows.
   - Stáhněte si [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) z webu Windows Sysinternals. Po stažení nástroje PsExec, spusťte prostředí Azure PowerShell jako správce a zadejte:
      ```powershell
      psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
      ```

### <a name="storsimple-best-practices"></a>Osvědčené postupy pro StorSimple

  -   Ujistěte se, že zařízení StorSimple se aktualizuje na [s aktualizací Update 3 nebo novější](storsimple-install-update-3.md).
  -   Izoluje přenosy iSCSI a cloudu. Použijte vyhrazený iSCSI připojení pro přenos dat mezi StorSimple a záložní server.
  -   Ujistěte se, že zařízení StorSimple je vyhrazené cíl zálohování. Smíšené úlohy nejsou podporovány, protože ovlivňují RTO a RPO.

### <a name="backup-exec-best-practices"></a>Osvědčené postupy zálohování Exec

-   Backup Exec musí nainstalovat na místním disku serveru a nikoli na svazek StorSimple.
-   Nastavení úložiště zálohování Exec **souběžných operací zápisu** maximální povolený.
    -   Nastavení úložiště zálohování Exec **velikosti bloku a vyrovnávací paměť** na 512 KB.
    -   Zapnout Backup Exec úložiště **ukládány do vyrovnávací paměti pro čtení a zápisu**.
-   StorSimple podporuje Backup Exec úplnými a přírůstkovými zálohami. Doporučujeme vám, že je velmi riskantní používat syntetické a rozdílové zálohy.
-   Zálohování datových souborů by mělo obsahovat data jenom pro konkrétní úlohu. Například žádné médium připojí přes různé úlohy jsou povoleny.
-   Zakážete ověřování úlohy. V případě potřeby ověřování má být naplánováno po nejnovější úlohy zálohování. Je důležité pochopit, že tato úloha má vliv okno zálohování.
-   Vyberte **úložiště** > **disku** > **podrobnosti** > **vlastnosti**. Vypnout **předem přidělte místo na disku**.

Nejnovější nastavení Backup Exec a osvědčené postupy při implementaci těchto požadavků najdete v tématu [webu Veritas](https://www.veritas.com).

## <a name="retention-policies"></a>Zásady uchovávání informací

Jeden z nejběžnějších typů zásad uchovávání záloh je zásada Dědečka ze strany otce a SYN (GFS). V zásadách GFS přírůstkové zálohy se provádí denně a úplné zálohy jsou prováděny každý týden a každý měsíc. Výsledkem zásad šest StorSimple vrstvené svazky. Jeden svazek obsahuje zálohy úplné týdenní, měsíční nebo roční. Pět svazky ukládání denních přírůstkových záloh.

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

## <a name="set-up-backup-exec-storage"></a>Nastavení úložiště Backup Exec

### <a name="to-set-up-backup-exec-storage"></a>Nastavení úložiště Backup Exec

1.  V konzole pro správu Backup Exec vyberte **úložiště** > **konfigurace úložiště** > **diskové úložiště**  >   **Další**.

    ![Zálohování konzoly pro správu Exec, stránka úložiště konfigurace](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

2.  Vyberte **diskové úložiště**a pak vyberte **Další**.

    ![Zálohování Exec konzoly pro správu, vyberte úložiště stránky](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

3.  Například zadejte název reprezentativní **sobota úplné**a popis. Vyberte **Další**.

    ![Zálohování stránku správy konzoly, název a popis Exec](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

4.  Vyberte disk, ve kterém chcete vytvořit úložné zařízení disk a potom vyberte **Další**.

    ![Zálohování konzoly pro správu Exec, stránka pro výběr úložiště disku](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

5.  Zvýší počet operací zápisu na **16**a pak vyberte **Další**.

    ![Backup Exec konzoly pro správu souběžné zápisu operace nastavení stránky](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

6.  Zkontrolujte nastavení a pak vyberte **Dokončit**.

    ![Zálohování konzoly pro správu Exec, stránka souhrnu konfigurace úložiště](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

7.  Na konci každé přiřazení svazku změní nastavení zařízení úložiště tak, aby odpovídaly na tyto doporučené [osvědčené postupy pro službu StorSimple a Backup Exec](#best-practices-for-storsimple-and-backup-exec).

    ![Zálohování konzoly pro správu Exec, stránku s nastavením zařízení úložiště](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

8.  Opakujte kroky 1-7, až budete hotovi, Exec zálohování přiřadit svazky zařízení StorSimple.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Nastavit StorSimple jako primární cíl zálohování

> [!NOTE]
> Rychlostí cloudu dojde k obnovení dat ze zálohy, která byla Vrstvená do cloudu.

Následující obrázek znázorňuje mapování svazku typické pro úlohu zálohování. V takovém případě všechny týdenní zálohy namapovat na sobotu celého disku a mapování přírůstkové zálohování na disky přírůstkové pondělí až pátek. Všechno, co zálohování a obnovení jsou ze StorSimple vrstveného svazku.

![Primární cíl zálohování konfigurace Logický diagram](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>StorSimple jako cíl zálohování primární GFS naplánovat příklad

Tady je příklad plánu otočení GFS čtyři týdny, měsíční nebo roční:

| Typ frekvence/zálohování | Úplná | Přírůstkové (1-5 dní)  |   
|---|---|---|
| Každý týden (1 – 4 týdny) | Sobota | Pondělí – pátek |
| Měsíční  | Sobota  |   |
| Ročně | Sobota  |   |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Backup Exec úlohy zálohování přiřadit svazky zařízení StorSimple

K následujícímu pořadí předpokládá, že jsou v souladu s pokyny Backup Exec agent nakonfigurován Backup Exec a cílový hostitel.

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Přiřadit svazky zařízení StorSimple pro úlohu zálohování Backup Exec

1.  V konzole pro správu Backup Exec vyberte **hostitele** > **zálohování** > **zálohování na Disk**.

    ![Backup Exec konzoly pro správu, vyberte hostitele, zálohování a zálohování na disk](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

2.  V **vlastnosti definice zálohování** dialogovém okně **zálohování**vyberte **upravit**.

    ![Zálohování konzoly pro správu Exec, dialogové okno Vlastnosti definice zálohování](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

3.  Nastavte úplné a přírůstkové zálohování tak, aby splňovaly vaše požadavky RPO a RTO a v souladu s Veritas osvědčené postupy.

4.  V **možnosti zálohování** dialogu **úložiště**.

    ![Zálohování konzoly pro správu Exec, dialogové okno Možnosti úložiště záloh](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

5.  Přiřaďte odpovídající svazky zařízení StorSimple do vašeho záložního plánu.

    > [!NOTE]
    > **Komprese** a **typ šifrování** jsou nastaveny na **žádný**.

6.  V části **ověřte**, vyberte **Neověřovat data pro tuto úlohu** zaškrtávací políčko. Pomocí této možnosti může ovlivnit ovládání datových vrstev na StorSimple.

    > [!NOTE]
    > Defragmentace, indexování a ověřování na pozadí negativně ovlivnit StorSimple ovládání datových vrstev.

    ![Ověřte nastavení zálohování Exec konzoly pro správu možnosti zálohování](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

7.  Pokud jste nastavili zbývající možnosti zálohování, aby splňovaly vaše požadavky, vyberte **OK** na dokončení.

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Nastavit službu StorSimple jako sekundární záložní cíl

> [!NOTE]
>Rychlostí cloudu dojde k obnovení dat ze zálohy, která byla Vrstvená do cloudu.

V tomto modelu musí mít úložných médií (jiné než StorSimple) která bude sloužit jako dočasná mezipaměť. Například můžete použít redundantní pole nezávislých disků (RAID) svazek tak, aby vyhovovaly místa, vstupní a výstupní (I/O) a šířku pásma. Doporučujeme používat RAID 5, 50 a 10.

Následující obrázek ukazuje typické krátkodobé uchování (pro server) místní svazky a dlouhodobé uchovávání archivuje svazky. V tomto scénáři všechna zálohování spustit na místní (pro server) svazek RAID. Tyto zálohy jsou pravidelně duplicitní a archivovat do svazku archivy. Je důležité pro nastavení velikosti místní (pro server,) svazku RAID, takže zvládne krátkodobé uchování požadavky kapacitu a výkon.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple jako příklad GFS sekundární záložní cíl

![StorSimple jako sekundární záložní cíl Logický diagram](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

Následující tabulka ukazuje, jak nastavit zálohování pro místní i StorSimple disky. Obsahuje požadavky na kapacitu jednotlivých a celkový počet.

### <a name="backup-configuration-and-capacity-requirements"></a>Konfigurace zálohování a požadavky na kapacitu

| Typ zálohování a uchovávání | Úložiště | Velikost (TiB) | Násobitel GFS | Celková kapacita\* (TB) |
|---|---|---|---|---|
| Týden 1 (úplných a přírůstkových) |Místní disk (krátkodobé)| 1 | 1 | 1 |
| StorSimple týdnů 2 až 4 |StorSimple disku (dlouhodobé) | 1 | 4 | 4 |
| Měsíční úplné |StorSimple disku (dlouhodobé) | 1 | 12 | 12 |
| Ročně úplné |StorSimple disku (dlouhodobé) | 1 | 1 | 1 |
|Požadavek na velikost svazků GFS |  |  |  | 18*|
\* Celková kapacita zahrnuje 17 TiB StorSimple disky a 1 TiB místní svazek RAID.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>GFS Příklad plánu: Otočení GFS týdenní, měsíční a roční plán

| Týden | Úplná | Přírůstkové dne 1 | Přírůstkové den 2 | Přírůstkové den 3 | Přírůstkové den 4 | Přírůstková denně, 5 |
|---|---|---|---|---|---|---|
| 1 týden | Místní svazek RAID  | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID |
| Týdnu 2 | StorSimple týdnů 2 až 4 |   |   |   |   |   |
| Týdnu 3 | StorSimple týdnů 2 až 4 |   |   |   |   |   |
| Týdnu 4 | StorSimple týdnů 2 až 4 |   |   |   |   |   |
| Měsíční | Každý měsíc StorSimple |   |   |   |   |   |
| Ročně | Každý rok StorSimple  |   |   |   |   |   |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-archive-and-deduplication-job"></a>Přiřadit Backup Exec archivní svazky zařízení StorSimple a úlohy odstranění duplicitních dat

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-archive-and-duplication-job"></a>Přiřadit svazky zařízení StorSimple na úlohu archivace a duplikování Backup Exec

1.  V konzole pro správu Backup Exec, klikněte pravým tlačítkem na úlohy, které chcete archivovat svazek StorSimple a pak vyberte **vlastnosti definice zálohování** > **upravit**.

    ![Zálohování konzoly pro správu Exec, karta Vlastnosti definice zálohování](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

2.  Vyberte **přidat fázi** > **duplicitní disk** > **upravit**.

    ![Zálohování konzoly pro správu Exec, přidat fázi](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

3.  V **duplicitní možnosti** dialogového okna, vyberte hodnoty, které chcete použít pro **zdroj** a **plán**.

    ![Exec konzoly pro správu, zálohování definice vlastnosti a duplicitní možnosti zálohování](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

4.  V **úložiště** rozevíracího seznamu vyberte svazek StorSimple, kam chcete úlohy archivu pro ukládání dat, ze seznamu.

    ![Exec konzoly pro správu, zálohování definice vlastnosti a duplicitní možnosti zálohování](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

5.  Vyberte **ověřte**a pak vyberte **Neověřovat data pro tuto úlohu** zaškrtávací políčko.

    ![Exec konzoly pro správu, zálohování definice vlastnosti a duplicitní možnosti zálohování](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

6.  Vyberte **OK**.

    ![Exec konzoly pro správu, zálohování definice vlastnosti a duplicitní možnosti zálohování](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

7.  V **zálohování** sloupce, přidat nové fázi. Pro zdroj, použijte **přírůstkové**. Pro cíl vyberte svazek StorSimple, ve kterém se úloha přírůstkového zálohování archivuje. Opakujte kroky 1 až 6.

## <a name="storsimple-cloud-snapshots"></a>Cloudové snímky StorSimple

Cloudové snímky StorSimple chránit data uložená v zařízení StorSimple. Vytváří se snímek v cloudu je ekvivalentní přesouvání místní záložní pásky do zařízení mimo pracoviště. Pokud používáte geografické redundantní úložiště Azure, vytváří se snímek v cloudu je ekvivalentní přesouvání záložní pásky k více lokalitám. Pokud budete potřebovat obnovení po havárii zařízení, může být online jiného zařízení StorSimple a služeb při selhání. Po převzetí služeb při selhání bude mít přístup k datům (cloud rychlostí) z nejnovější snímek v cloudu.

Následující část popisuje, jak vytvořit krátké skript ke spuštění a odstranění StorSimple cloudové snímky během zálohování následného zpracování.

> [!NOTE]
> Snímky vytvořené ručně nebo programově podle zásad vypršení platnosti StorSimple snapshot. Tyto snímky musíte ručně nebo programově odstranit.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Spuštění a odstranění cloudové snímky pomocí skriptu

> [!NOTE]
> Pečlivě vyhodnoťte dodržování předpisů a dopady uchování dat před odstraněním StorSimple snapshot. Další informace o způsobu spuštění předzálohovacího skriptu, najdete v článku [dokumentace ke službě Backup Exec](https://www.veritas.com/support/en_US/article.100032497.html).

### <a name="backup-lifecycle"></a>Zálohování životního cyklu

![Zálohování diagram životního cyklu](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

### <a name="requirements"></a>Požadavky

-   Server, na kterém běží skriptu musí mít přístup k prostředkům cloudu Azure.
-   Uživatelský účet musí mít potřebná oprávnění.
-   Zásady zálohování StorSimple se přidružené svazky zařízení StorSimple musíte nastavit, ale není zapnutá.
-   Budete potřebovat název prostředku StorSimple, registrační klíč, ID zařízení zásadu název a zálohování.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Ke spuštění nebo odstranění snímek v cloudu

1.  [Nainstalujte prostředí Azure PowerShell](/powershell/azure/overview).
2. Stažení a instalace [spravovat CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) skript prostředí PowerShell.
3. Na serveru, na kterém běží, spuštění skriptu prostředí PowerShell jako správce. Zkontrolujte, jestli jste spustili skript s `-WhatIf $true` chcete zobrazit, co se změní skript provede. Po dokončení ověření předat `-WhatIf $false`. Spustit následující příkaz:
```powershell
.\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
```
4.  Přidáte skript pro vaši úlohu zálohování v Backup Exec úpravou možnosti Backup Exec úlohy předběžného zpracování a následné zpracování příkazů.

    ![Zálohování konzoly Exec, možnosti zálohování, karta provedení před instrumentací a následného zpracování příkazů](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> Doporučujeme, abyste spustili zásady zálohování StorSimple cloudových snímků jako následného zpracování skriptu na konci vaší každodenní úlohy zálohování. Další informace o tom, jak zálohovat a obnovovat vaše aplikace pro zálohování prostředí, které pomáhá splnit i ty RPO a RTO, obraťte se na zálohování architect.

## <a name="storsimple-as-a-restore-source"></a>StorSimple jako zdroj obnovení

Obnoví ze pracovní zařízení StorSimple jako je obnovení z jakékoli zařízení s blokovým úložištěm. Rychlostí cloudu dojde k obnovení dat, která je Vrstvená do cloudu. Pro místní data dojde k obnovení rychlostí místní disk zařízení. Informace o tom, jak provést obnovení, najdete v dokumentaci k Backup Exec. Doporučujeme, abyste odpovídají Backup Exec obnovení osvědčené postupy.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple převzetí služeb při selhání a zotavení po havárii

> [!NOTE]
> Pro zálohování cílové scénáře řešení StorSimple Cloud Appliance není podporováno jako cíl obnovení.

Havárii může být způsobeno různých faktorech. V následující tabulce jsou uvedeny běžné scénáře zotavení po havárii.

| Scénář | Dopad | Postup obnovení | Poznámky |
|---|---|---|---|
| Selhání zařízení StorSimple | Operace zálohování a obnovení je přerušeno. | Nahraďte neúspěšných zařízení a provádět [StorSimple převzetí služeb při selhání a zotavení po havárii](storsimple-device-failover-disaster-recovery.md). | Pokud potřebujete provést obnovení po obnovení zařízení, úplná data pracovní sady jsou načteny z cloudu do nového zařízení. Všechny operace jsou rychlostí cloudu. Indexování a vytváření katalogu urychlen rescanning procesu může dojít k všechny zálohovacích skladů určených k zkontrolovat a získaná z vrstvy cloudu do úrovně místního zařízení, což může být časově náročný proces. |
| Selhání zálohování serveru Exec | Operace zálohování a obnovení je přerušeno. | Znovu sestavit server zálohování a obnovení databáze podle popisu v [postup ručního zálohování a obnovení ze zálohy Exec (BEDB) databáze](http://www.veritas.com/docs/000041083). | Musíte znovu sestavit nebo obnovit server Backup Exec v lokalitě zotavení po havárii. Obnovení databáze do nejnovějšího bodu. Pokud obnovené databáze Backup Exec není synchronizované s nejnovější úlohy zálohování, je vyžadována indexování a katalogizace. Tento index a katalog opětovná kontrola procesu může dojít k všechny zálohovacích skladů určených k zkontrolovat a získaná z vrstvy cloudu do úrovně místního zařízení. To umožňuje dále náročné na čas. |
| Selhání lokality, který vede ke ztrátě záložní server a StorSimple | Operace zálohování a obnovení je přerušeno. | Nejprve obnovit StorSimple a potom obnovte Backup Exec. | Nejprve obnovit StorSimple a potom obnovte Backup Exec. Pokud potřebujete provést obnovení po obnovení zařízení, úplná data pracovní sady jsou načteny z cloudu do nového zařízení. Všechny operace jsou rychlostí cloudu. |

## <a name="references"></a>Odkazy

Pro účely tohoto článku bylo odkazováno v následujících dokumentech:

- [StorSimple více cest vstupně-výstupní operace instalace](storsimple-configure-mpio-windows-server.md)
- [Scénáře využití služby Storage: dynamické zajišťování](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Pomocí GPT jednotky](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Nastavení stínových kopií pro sdílené složky](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Další postup

- Další informace o tom, jak [obnovení ze zálohovacího skladu](storsimple-restore-from-backup-set-u2.md).
- Další informace o tom, jak provádět [zařízení převzetí služeb při selhání a zotavení po havárii](storsimple-device-failover-disaster-recovery.md).
