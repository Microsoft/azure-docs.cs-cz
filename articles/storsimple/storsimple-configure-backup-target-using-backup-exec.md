---
title: Série StorSimple 8000 jako cíl zálohování pomocí programu Backup Exec | Microsoft Docs
description: Popisuje konfiguraci cíle zálohování StorSimple pomocí programu Veritas Backup Exec.
services: storsimple
documentationcenter: ''
author: harshakirank
manager: matd
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/05/2016
ms.author: matd
ms.openlocfilehash: 66a1e22282864d0425173504735d6beb42b76ad7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94967257"
---
# <a name="storsimple-as-a-backup-target-with-backup-exec"></a>StorSimple jako cíl zálohování pomocí Backup Exec

## <a name="overview"></a>Přehled

Azure StorSimple je řešení hybridního cloudového úložiště od Microsoftu. StorSimple řeší složitosti exponenciálního nárůstu dat pomocí účtu Azure Storage jako rozšíření místního řešení a automaticky přenáší data napříč místním úložištěm a cloudovým úložištěm.

V tomto článku probereme integraci StorSimple s Veritas Backup Exec a osvědčenými postupy pro integraci obou řešení. Také jsme povedli doporučení, jak nastavit program Backup Exec tak, aby se co nejlépe integroval s StorSimple. Od společnosti Veritas osvědčené postupy, architekti zálohování a správců odložíme nejlepší způsob, jak nastavit Backup Exec pro splnění požadavků na jednotlivé zálohy a smluv o úrovni služeb (SLA).

I když ilustrují konfigurační kroky a klíčové koncepty, Tento článek není nijak podrobných konfigurací nebo Průvodce instalací nástroje. Předpokládáme, že základní komponenty a infrastruktura jsou v pracovním řádu a připravené k podpoře konceptů, které popisujeme.

### <a name="who-should-read-this"></a>Kdo by se měl číst?

Informace v tomto článku budou nejužitečnější pro správce zálohování, Správce úložiště a architekty úložiště, kteří mají znalosti o úložištích, Windows Serveru 2012 R2, Ethernet, Cloud Services a Backup Exec.

## <a name="supported-versions"></a>Podporované verze

-   [Backup Exec 16 a novější verze](https://www.veritas.com/content/support/en_US/article.100040087)
-   [StorSimple Update 3 a novější verze](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Proč StorSimple jako cíl zálohování?

StorSimple je vhodná volba pro cíl zálohování z těchto důvodů:

-   Poskytuje standardní místní úložiště pro aplikace pro zálohování, které se mají použít jako cíl pro rychlé zálohování, a to bez jakýchkoli změn. StorSimple můžete použít také pro rychlé obnovení nedávných záloh.
-   Jeho vrstva cloudu se bezproblémově integruje s účtem cloudového úložiště Azure, aby bylo možné využívat nákladově efektivní Azure Storage.
-   Pro zotavení po havárii automaticky zajišťuje ukládání mimo lokalitu.

## <a name="key-concepts"></a>Klíčové koncepty

Stejně jako u jakéhokoli řešení úložiště je velmi důležité vyhodnotit výkon úložiště, SLA, rychlost změny a potřeby růstu kapacity. Hlavním nápadem je to, že když zavedete cloudovou vrstvu, vaše doba přístupu a propustnost do cloudu hrají základní roli, která může StorSimple provádět jeho úlohu.

StorSimple je navržený tak, aby poskytoval úložiště pro aplikace, které pracují s dobře definovanou pracovní sadou dat (Hot data). V tomto modelu se pracovní sada dat ukládá v místních vrstvách a zbývající nepracovní/studená a archivní sada dat je vrstvená do cloudu. Tento model je reprezentován na následujícím obrázku. Skoro plochá zelená čára představuje data uložená v místních vrstvách zařízení StorSimple. Červená čára představuje celkové množství dat uložených v řešení StorSimple napříč všemi úrovněmi. Prostor mezi plochou zelenou čárou a exponenciální červenou křivkou představuje celkové množství dat uložených v cloudu.

**Vrstvení StorSimple** 
 ![ Diagram vrstvení StorSimple](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

V této architektuře se dozvíte, že StorSimple je ideálním řešením pro provoz jako cíl zálohování. StorSimple můžete použít k těmto akcím:
-   Proveďte nejčastější obnovení z místní pracovní sady dat.
-   Využijte Cloud pro zotavení po havárii mimo lokalitu a starší data, kde obnovení je méně časté.

## <a name="storsimple-benefits"></a>Výhody StorSimple

StorSimple poskytuje místní řešení, které se bez problémů integruje s Microsoft Azure díky využití bezproblémového přístupu k místnímu a cloudovém úložišti.

StorSimple využívá automatické vrstvení mezi místním zařízením, které má úložiště SSD (Solid-State Device) a SAS (Serial-Attached SCSI), a Azure Storage. Automatické vrstvení uchovává často používaná data na úrovních SSD a SAS. Přesune zřídka používaná data na Azure Storage.

StorSimple nabízí tyto výhody:

-   Jedinečné algoritmy pro odstranění duplicit a kompresi, které využívají Cloud k dosažení nepředchozích úrovní odstranění duplicit
-   Vysoká dostupnost
-   Geografická replikace pomocí geografické replikace Azure
-   Integrace Azure
-   Šifrování dat v cloudu
-   Lepší zotavení po havárii a dodržování předpisů

I když StorSimple představuje dva hlavní scénáře nasazení (primární cíl zálohování a sekundární cíl zálohování), v podstatě je to jednoduché, blokové úložné zařízení. StorSimple provádí všechna komprimaci a odstraňování duplicitních dat. Bez problémů odesílá a načítá data mezi cloudem a systémem souborů a aplikací.

Další informace o StorSimple najdete v tématu [StorSimple 8000 series: řešení hybridního cloudového úložiště](storsimple-overview.md). Můžete si také projít [specifikacemi řady Technical StorSimple 8000](./storsimple-8000-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Použití zařízení StorSimple jako cíle zálohování je podporované jenom pro StorSimple 8000 Update 3 a novější verze.

## <a name="architecture-overview"></a>Přehled architektury

V následujících tabulkách jsou uvedeny úvodní pokyny k modelům zařízení-architektura.

**StorSimple kapacity pro místní a cloudové úložiště**

| Kapacita úložiště       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| Kapacita místního úložiště | &lt; 10 TiB\*  | &lt; 20 TiB\*  |
| Kapacita cloudového úložiště | &gt; 200 TiB\* | &gt; 500 TiB\* |

\* Velikost úložiště nepředpokládá žádné odstranění duplicit ani kompresi.

**StorSimple kapacity pro primární a sekundární zálohy**

| Scénář zálohování  | Kapacita místního úložiště  | Kapacita cloudového úložiště  |
|---|---|---|
| Primární záloha  | Poslední zálohy uložené v místním úložišti pro rychlé obnovení, aby splňovaly cíl bodu obnovení (RPO) | Historie zálohování (RPO) se vejde do kapacity cloudu |
| Sekundární zálohování | Sekundární kopie zálohovaných dat se dá ukládat do kapacity cloudu.  | –  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple jako primární cíl zálohování

V tomto scénáři se StorSimple svazky prezentují do zálohovací aplikace jako jediné úložiště pro zálohy. Následující obrázek ukazuje architekturu řešení, ve které všechny zálohy používají StorSimple vrstvené svazky pro zálohování a obnovení.

![StorSimple jako primární Logický diagram cíle zálohování](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Logické kroky zálohování primárního cíle

1.  Záložní server kontaktuje cílového agenta Zálohování a Agent zálohování přenáší data na záložní server.
2.  Záložní server zapisuje data do vrstvených svazků StorSimple.
3.  Záložní server aktualizuje databázi katalogu a pak dokončí úlohu zálohování.
4.  Skript snímku spustí StorSimple Cloud Snapshot Manager (spustit nebo odstranit).
5.  Záložní server odstraní zálohy s vypršenou platností na základě zásad uchovávání informací.


### <a name="primary-target-restore-logical-steps"></a>Logické kroky obnovení primárního cíle

1.  Záložní server začne obnovovat vhodná data z úložiště úložiště.
2.  Agent zálohování obdrží data ze záložního serveru.
3.  Záložní server dokončí úlohu obnovení.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple jako cíl sekundárního zálohování

V tomto scénáři se StorSimple svazky primárně používají k dlouhodobému uchovávání nebo archivaci.

Následující obrázek ukazuje architekturu, ve které počáteční zálohy a obnovení cílí na svazek s vysokým výkonem. Tyto zálohy se zkopírují a archivují na StorSimple vrstvený svazek podle nastaveného plánu.

Je důležité mít velikost vysoce výkonného svazku, aby mohla zvládnout požadavky na kapacitu a výkon zásad uchovávání informací.

![Logický diagram StorSimple jako sekundární cíl zálohování](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Logické kroky zálohování sekundárního cíle

1.  Záložní server kontaktuje cílového agenta Zálohování a Agent zálohování přenáší data na záložní server.
2.  Záložní server zapisuje data do úložiště s vysokým výkonem.
3.  Záložní server aktualizuje databázi katalogu a pak dokončí úlohu zálohování.
4.  Záložní server kopíruje zálohy do StorSimple na základě zásad uchovávání informací.
5.  Skript snímku spustí StorSimple Cloud Snapshot Manager (spustit nebo odstranit).
6.  Záložní server odstraní zálohy s vypršenou platností na základě zásad uchovávání informací.

### <a name="secondary-target-restore-logical-steps"></a>Logické kroky obnovení sekundárního cíle

1.  Záložní server začne obnovovat vhodná data z úložiště úložiště.
2.  Agent zálohování obdrží data ze záložního serveru.
3.  Záložní server dokončí úlohu obnovení.

## <a name="deploy-the-solution"></a>Nasazení řešení

Nasazení řešení vyžaduje tři kroky:
1. Připravte síťovou infrastrukturu.
2. Nasaďte zařízení StorSimple jako cíl zálohování.
3. Nasaďte Backup Exec.

Jednotlivé kroky jsou podrobně popsány v následujících částech.

### <a name="set-up-the-network"></a>Nastavit síť

Vzhledem k tomu, že StorSimple je řešení, které je integrované s cloudem Azure, vyžaduje StorSimple aktivní a funkční připojení ke cloudu Azure. Toto připojení se používá pro operace, jako jsou cloudové snímky, Správa a přenos metadat, a to na úrovni starších a méně využitých dat do cloudového úložiště Azure.

Aby se řešení provádělo optimálně, doporučujeme dodržovat tyto osvědčené postupy sítě:

-   Odkaz, který připojuje vaše rozhraní StorSimple do Azure, musí splňovat požadavky na šířku pásma. Dosáhnete toho tak, že použijete potřebnou úroveň QoS (Quality of Service) na vaše přepínače infrastruktury tak, aby odpovídaly cíli RPO a času obnovení (RTO) SLA.
-   Maximální počet latencí přístupu k úložišti objektů BLOB v Azure by měl být okolo 80 MS.

### <a name="deploy-storsimple"></a>Nasazení StorSimple

Podrobné pokyny k nasazení StorSimple najdete v tématu [nasazení místního zařízení StorSimple](./storsimple-8000-deployment-walkthrough-u2.md).

### <a name="deploy-backup-exec"></a>Nasazení Backup Exec

Osvědčené postupy pro instalaci služby Backup Exec najdete v tématu [osvědčené postupy pro instalaci služby Backup Exec](https://www.veritas.com/content/support/en_US/doc/72686287-131623464-0/v70444238-131623464).

## <a name="set-up-the-solution"></a>Nastavení řešení

V této části předvádíme některé příklady konfigurace. Následující příklady a doporučení znázorňují základní a základní implementaci. Tato implementace se nemusí vztahovat přímo na vaše konkrétní požadavky na zálohování.

### <a name="set-up-storsimple"></a>Nastavení StorSimple

| StorSimple úlohy nasazení  | Další komentáře |
|---|---|
| Nasaďte vaše místní zařízení StorSimple. | Podporované verze: aktualizace 3 a novější verze. |
| Zapněte cíl zálohování. | Pomocí těchto příkazů můžete zapnout nebo vypnout režim cíle zálohování a získat stav. Další informace najdete v tématu [vzdálené připojení k zařízení StorSimple](./storsimple-8000-remote-connect.md).</br> Zapnutí režimu zálohování: `Set-HCSBackupApplianceMode -enable` . </br> Vypnutí režimu `Set-HCSBackupApplianceMode -disable` zálohování: </br> Získání aktuálního stavu nastavení režimu zálohování: `Get-HCSBackupApplianceMode` . |
| Vytvořte pro svazek společný kontejner svazků, ve kterém jsou uložena data záloh. Všechna data v kontejneru svazků mají za následek odstranění duplicitních dat. | Kontejnery svazků StorSimple definují domény odstranění duplicit.  |
| Vytvořte StorSimple svazky. | Vytvářejte svazky s velikostí co nejblíže předpokládanému využití, protože velikost svazku ovlivňuje dobu trvání snímku cloudu. Informace o tom, jak velikost svazku získat, najdete v tématu o [zásadách uchovávání informací](#retention-policies).</br> </br> Použijte StorSimple vrstvené svazky a zaškrtněte políčko **použít tento svazek pro archivní data, ke kterým se přistupuje méně často** . </br> Použití pouze místně připojených svazků není podporováno. |
| Vytvořte jedinečné zásady zálohování StorSimple pro všechny cílové svazky zálohy. | Zásada zálohování StorSimple definuje skupinu konzistence svazku. |
| Zakažte plán jako vypršení platnosti snímků. | Snímky se spouštějí jako operace následného zpracování. |

### <a name="set-up-the-host-backup-server-storage"></a>Nastavení úložiště záložního serveru hostitele

Nastavte úložiště záložního serveru hostitele podle těchto pokynů:  

- Nepoužívejte rozložené svazky (vytvořené pomocí správy disků systému Windows). Rozložené disky nejsou podporovány.
- Naformátujte svazky pomocí systému souborů NTFS s velikostí alokace 64 – KB.
- Namapujte svazky StorSimple přímo na Server Backup Exec.
    - Pro fyzické servery použijte iSCSI.
    - Používejte průchozí disky pro virtuální servery.

## <a name="best-practices-for-storsimple-and-backup-exec"></a>Osvědčené postupy pro StorSimple a Backup Exec

Nastavte své řešení podle pokynů v následujících částech.

### <a name="operating-system-best-practices"></a>Osvědčené postupy pro operační systém

- Zakažte šifrování a odstranění duplicitních dat Windows serveru pro systém souborů NTFS.
- Zakažte defragmentaci Windows serveru na svazcích StorSimple.
- Zakažte indexování Windows serveru na svazcích StorSimple.
- Spusťte kontrolu antivirové ochrany na zdrojovém hostiteli (nikoli na svazcích StorSimple).
- Vypněte výchozí [údržbu Windows serveru](/windows/win32/w8cookbook/automatic-maintenance) ve Správci úloh. Udělejte to jedním z následujících způsobů:
  - Vypněte Konfigurátor údržby ve Windows Plánovač úloh.
  - Stáhněte si [PsExec](/sysinternals/downloads/psexec) ze systému Windows Sysinternals. Po stažení PsExec spusťte Azure PowerShell jako správce a zadejte:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>Osvědčené postupy pro StorSimple

  -   Ujistěte se, že se zařízení StorSimple aktualizovalo na [aktualizaci 3 nebo novější](./index.yml).
  -   Izolujte provoz iSCSI a Cloud. Pro přenosy mezi StorSimple a záložním serverem používejte vyhrazená připojení iSCSI.
  -   Ujistěte se, že vaše zařízení StorSimple je vyhrazený cíl zálohování. Smíšené úlohy se nepodporují, protože mají vliv na RTO a RPO.

### <a name="backup-exec-best-practices"></a>Osvědčené postupy pro zálohování exec

-   Backup Exec se musí nainstalovat na místní disk serveru, a ne na StorSimple svazek.
-   Nastavte **Souběžné operace zápisu** úložiště Backup Exec na povolené maximum.
    -   Nastavte blok úložiště Backup Exec **a velikost vyrovnávací paměti** na 512 KB.
    -   Zapněte úložiště Backup Exec **pro čtení a zápis**.
-   StorSimple podporuje zálohování exec a úplné a přírůstkové zálohování. Doporučujeme, abyste nepoužívali syntetické a rozdílové zálohy.
-   Soubory dat zálohy by měly obsahovat pouze data pro konkrétní úlohu. Například připojení k médiím v různých úlohách není povoleno.
-   Zakáže ověřování úlohy. V případě potřeby by se mělo ověřování naplánovat po poslední úloze zálohování. Je důležité pochopit, že tato úloha má vliv na okno zálohování.
-   Vyberte **úložiště**  >    >  **vlastnosti podrobnosti o** disku  >  . Vypněte **volbu předem přidělit místo na disku**.

Nejnovější nastavení služby Backup Exec a osvědčené postupy pro implementaci těchto požadavků najdete [na webu Veritas](https://www.veritas.com).

## <a name="retention-policies"></a>Zásady uchovávání informací

Jedním z nejběžnějších typů zásad uchovávání záloh je zásada dědečka, otce a syn (GFS). V zásadách GFS se provádí přírůstkové zálohování každý den a úplné zálohování se provádí týdně a měsíčně. Tato zásada má za následek šest StorSimple vrstvených svazků. Jeden svazek obsahuje týdenní, měsíční a roční úplné zálohování. Druhými pěti svazky se ukládají každodenní přírůstkové zálohování.

V následujícím příkladu používáme GFS otočení. Příklad předpokládá následující:

-   Používají se neodstranění duplicit nebo komprimovaná data.
-   Úplné zálohy jsou 1 TiB.
-   Každodenní přírůstkové zálohování je 500 GiB.
-   Čtyři týdenní zálohy se uchovávají po dobu měsíce.
-   12 měsíčních záloh se uchovává po dobu roku.
-   Jedna roční záloha se uchovává po dobu 10 let.

Na základě předchozích předpokladů vytvořte TiB StorSimple vrstvený svazek pro měsíční a roční úplný počet záloh. Vytvořte TiB StorSimple vrstvený svazek pro každé přírůstkové denní zálohování.

| Uchování typu zálohování | Velikost (TiB) | Multiplikátor GFS\* | Celková kapacita (TiB)  |
|---|---|---|---|
| Týdně úplné | 1 | 4  | 4 |
| Denní přírůstkový | 0,5 | 20 (počet cyklů s rovným počtem týdnů za měsíc) | 12 (2 pro další kvótu) |
| Úplně měsíčně | 1 | 12 | 12 |
| Celý rok na celé | 1  | 10 | 10 |
| Požadavek GFS |   | 38 |   |
| Dodatečná kvóta  | 4  |   | 42 celková GFS požadavek  |

\* Multiplikátor GFS je počet kopií, které je třeba chránit a které je potřeba zachovat, aby splňovaly požadavky zásad zálohování.

## <a name="set-up-backup-exec-storage"></a>Nastavení úložiště služby Backup Exec

### <a name="to-set-up-backup-exec-storage"></a>Nastavení úložiště služby Backup Exec

1.  V konzole pro správu Backup Exec vyberte **úložiště**  >  **Konfigurovat** úložiště  >  **na disku**  >  .**Další**.

    ![Backup Exec – Konzola pro správu, stránka konfigurace úložiště](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

2.  Vyberte **Disk Storage** a pak vyberte **Další**.

    ![Backup Exec – Konzola pro správu, výběr stránky úložiště](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

3.  Zadejte název zástupce, například **Úplná sobota** a popis. Vyberte **Další**.

    ![Stránka pro správu nástroje Backup Exec, název a popis](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

4.  Vyberte disk, na kterém chcete vytvořit zařízení diskového úložiště, a pak vyberte **Další**.

    ![Konzola pro správu služby Backup Exec, stránka výběru úložného disku](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

5.  Zvyšte počet operací zápisu na **16** a pak vyberte **Další**.

    ![Stránka nastavení pro souběžné operace zápisu v konzole pro správu Backup Exec](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

6.  Zkontrolujte nastavení a pak vyberte **Dokončit**.

    ![Konzola pro správu služby Backup Exec, stránka Souhrn konfigurace úložiště](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

7.  Na konci každého přiřazení svazku změňte nastavení zařízení úložiště tak, aby odpovídalo doporučeným [postupům pro StorSimple a Backup Exec](#best-practices-for-storsimple-and-backup-exec).

    ![Konzola pro správu služby Backup Exec, stránka nastavení úložného zařízení](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

8.  Opakujte kroky 1-7, dokud nedokončíte přiřazení svazků StorSimple ke službě Backup Exec.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Nastavte StorSimple jako primární cíl zálohování.

> [!NOTE]
> Obnovení dat ze zálohy, která byla vrstvená do cloudu, probíhá při rychlosti cloudu.

Následující obrázek ukazuje mapování typického svazku na úlohu zálohování. V tomto případě se všechny týdenní zálohy mapují na celý disk v sobotu a mapa přírůstkových záloh na Monday-Friday přírůstkové disky. Všechny zálohy a obnovení jsou ze StorSimple vrstveného svazku.

![Logický diagram primární konfigurace cíle zálohování](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Příklad plánu StorSimple jako primární cíl zálohování GFS

Tady je příklad plánu GFS rotace na čtyři týdny, měsíčně a ročně:

| Frekvence/typ zálohování | Do bloku | Přírůstkové (dny 1-5)  |   
|---|---|---|
| Týdně (týdny 1-4) | Sobota | Monday-Friday |
| Měsíčně  | Sobota  |   |
| Ročně | Sobota  |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Přiřazení svazků StorSimple k úloze zálohování Backup Exec

Následující sekvence předpokládá, že Backup Exec a cílový hostitel jsou nakonfigurované v souladu s pokyny agenta Backup Exec.

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Přiřazení svazků StorSimple k úloze zálohování Backup Exec

1.  V konzole pro správu Backup **exec vyberte zálohovat zálohování záloh**  >    >  **na disk**.

    ![Backup Exec – Konzola pro správu, výběr hostitele, zálohování a zálohování na disk](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

2.  V dialogovém okně **vlastnosti definice zálohování** v části **zálohování** vyberte **Upravit**.

    ![Konzola pro správu nástroje Backup Exec, dialogové okno Vlastnosti definice zálohování](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

3.  Nastavte úplné a přírůstkové zálohování tak, aby splňovalo požadavky RPO a RTO a splňovaly osvědčené postupy od společnosti Veritas.

4.  V dialogovém okně **Možnosti zálohování** vyberte **úložiště**.

    ![Backup Exec – Konzola pro správu, dialogové okno Možnosti zálohování, úložiště](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

5.  K plánu zálohování přiřaďte odpovídající svazky StorSimple.

    > [!NOTE]
    > **Komprese** a **typ šifrování** jsou nastaveny na **žádné**.

6.  V části **ověřit** vyberte zaškrtávací políčko **Neověřovat data pro tuto úlohu** . Použití této možnosti může ovlivnit vrstvení StorSimple.

    > [!NOTE]
    > Defragmentace, indexování a ověřování na pozadí negativně ovlivňují StorSimpleing vrstev.

    ![Backup Exec – Konzola pro správu, možnosti zálohování ověření nastavení](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

7.  Po nastavení zbývajících možností zálohování tak, aby splňovaly vaše požadavky, vyberte **OK** a dokončete instalaci.

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Nastavení StorSimple jako sekundárního cíle zálohování

> [!NOTE]
>Data se obnoví ze zálohy, která byla vrstvená do cloudu, při rychlosti cloudu.

V tomto modelu musíte mít úložné médium (jiné než StorSimple), které slouží jako dočasnou mezipaměť. Můžete například použít redundantní pole svazku RAID (RAID) k umístění, vstupu a výstupu (I/O) a šířce pásma. Doporučujeme použít RAID 5, 50 a 10.

Následující obrázek ukazuje typické krátkodobé uchovávání místního úložiště (na server) a svazky pro dlouhodobé uchovávání. V tomto scénáři se všechny zálohy spouštějí na místním svazku (na serveru) RAID. Tyto zálohy jsou pravidelně duplikovány a archivovány do svazku archivu. Je důležité nastavit velikost místního svazku (na server) RAID tak, aby mohla zvládnout vaše krátkodobé kapacity uchovávání a požadavky na výkon.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple jako sekundární cíl zálohování – příklad GFS

![Logický diagram StorSimple jako sekundární cíl zálohování](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

Následující tabulka ukazuje, jak nastavit zálohování pro spouštění na místních a StorSimple discích. Zahrnuje individuální a celkové požadavky na kapacitu.

### <a name="backup-configuration-and-capacity-requirements"></a>Požadavky na konfiguraci a kapacitu zálohování

| Typ a uchování zálohy | Nakonfigurované úložiště | Velikost (TiB) | Multiplikátor GFS | Celková kapacita \* (TIB) |
|---|---|---|---|---|
| Týden 1 (úplný a přírůstkový) |Místní disk (krátkodobý)| 1 | 1 | 1 |
| StorSimple týdny 2-4 |StorSimple disk (dlouhodobě) | 1 | 4 | 4 |
| Úplně měsíčně |StorSimple disk (dlouhodobě) | 1 | 12 | 12 |
| Celý rok na celé |StorSimple disk (dlouhodobě) | 1 | 1 | 1 |
|Požadavek na velikost svazků GFS |  |  |  | let|

\* Celková kapacita zahrnuje 17 TiB disků StorSimple a 1 TiB místního svazku RAID.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>GFS příklad plánu: GFS rotace týdně, měsíčně a ročního plánu

| Týden | Do bloku | Přírůstkový den 1 | Přírůstkový den 2 | Přírůstkový den 3 | Přírůstkový den 4 | Přírůstkový den 5 |
|---|---|---|---|---|---|---|
| Týden 1 | Místní svazek RAID  | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID |
| Týden 2 | StorSimple týdny 2-4 |   |   |   |   |   |
| Týden 3 | StorSimple týdny 2-4 |   |   |   |   |   |
| Týden 4 | StorSimple týdny 2-4 |   |   |   |   |   |
| Měsíčně | StorSimple měsíčně |   |   |   |   |   |
| Ročně | StorSimple ročně  |   |   |   |   |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-archive-and-deduplication-job"></a>Přiřazení svazků StorSimple ke službě Backup Exec a úloze odstranění duplicitních dat

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-archive-and-duplication-job"></a>Přiřazení svazků StorSimple k archivačnímu archivu a úloze duplikace exec

1.  V konzole pro správu Backup Exec klikněte pravým tlačítkem na úlohu, kterou chcete archivovat do StorSimple svazku, a pak vyberte **vlastnosti definice zálohování**  >  **Upravit**.

    ![Konzola pro správu nástroje Backup Exec, karta vlastnosti definice zálohování](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

2.  Vyberte **Přidat**  >  **Duplikovat fázi do**  >  **Úpravy** disku.

    ![Backup Exec – Konzola pro správu, přidat fázi](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

3.  V dialogovém okně **Duplikovat možnosti** vyberte hodnoty, které chcete použít pro **zdroj** a **plán**.

    ![Backup Exec – Konzola pro správu, vlastnosti definice zálohování a duplicitní možnosti](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

4.  V rozevíracím seznamu **úložiště** vyberte svazek StorSimple, ve kterém má úloha archivace ukládat data.

    ![Snímek obrazovky, který zobrazuje seznam, ve kterém je nutné vybrat úložiště.](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

5.  Vyberte **ověřit** a potom zaškrtněte políčko **Neověřovat data pro tuto úlohu** .

    ![Snímek obrazovky, který ukazuje, kde jste vybrali možnost Neověřovat data pro tuto úlohu.](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

6.  Vyberte **OK**.

    ![Snímek obrazovky zobrazující vlastnosti definice zálohování](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

7.  Do sloupce **záloha** přidejte novou fázi. Pro zdroj použijte **přírůstkové**. Jako cíl vyberte svazek StorSimple, na kterém je Archivovaná úloha přírůstkového zálohování. Opakujte kroky 1-6.

## <a name="storsimple-cloud-snapshots"></a>StorSimple cloudové snímky

StorSimple cloudové snímky chrání data, která se nachází ve vašem zařízení StorSimple. Vytvoření snímku v cloudu je ekvivalentní k přenosu pásek místních záloh do zařízení mimo pracoviště. Pokud používáte geograficky redundantní úložiště Azure, vytvoření snímku v cloudu je ekvivalentní k přenosu pásek zálohování na více lokalit. Pokud po havárii potřebujete zařízení obnovit, můžete jiné zařízení StorSimple převést do online režimu a provést převzetí služeb při selhání. Po převzetí služeb při selhání byste měli získat přístup k datům (s rychlostí cloudu) z posledního snímku cloudu.

Následující část popisuje, jak vytvořit krátký skript pro spuštění a odstranění snímků StorSimple cloudu během následného zpracování zálohy.

> [!NOTE]
> Ručně nebo programově vytvořené snímky nedodržují zásady vypršení platnosti snímku StorSimple. Tyto snímky je nutné ručně nebo programově odstranit.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Spuštění a odstranění cloudových snímků pomocí skriptu

> [!NOTE]
> Před odstraněním snímku StorSimple pečlivě vyhodnoťte dodržování předpisů a uchování dat. Další informace o tom, jak spustit pozálohovací skript, najdete v dokumentaci ke službě [Backup Exec](https://www.veritas.com/support/en_US/article.100032497.html).

### <a name="backup-lifecycle"></a>Životní cyklus zálohování

![Diagram životního cyklu zálohování](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

### <a name="requirements"></a>Požadavky

-   Server, na kterém je spuštěný skript, musí mít přístup k prostředkům cloudu Azure.
-   Uživatelský účet musí mít potřebná oprávnění.
-   Zásady zálohování StorSimple s přidruženými svazky StorSimple musí být nastavené, ale nejsou zapnuté.
-   Budete potřebovat název prostředku StorSimple, registrační klíč, název zařízení a ID zásad zálohování.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Spuštění nebo odstranění snímku v cloudu

1. [Nainstalujte prostředí Azure PowerShell](/powershell/azure/).
2. Stáhněte a nastavte [Manage-CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1) powershellový skript.
3. Na serveru, na kterém je spuštěný skript, spusťte PowerShell jako správce. Ujistěte se, že spouštíte skript s nástrojem `-WhatIf $true` , kde zjistíte, jaké změny bude skript provádět. Až se ověření dokončí, předejte `-WhatIf $false` . Spusťte následující příkaz:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Přidejte skript do úlohy zálohování v nástroji Backup Exec tak, že upravíte příkazy pro předběžné zpracování a následné zpracování v možnosti úlohy Backup Exec.

   ![Konzola Backup Exec, možnosti zálohování, předem a následné zpracování – karta příkazy](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> Doporučujeme, abyste na konci každodenní úlohy zálohování spustili zásadu zálohování snímku StorSimple cloudu jako skript po zpracování. Další informace o tom, jak zálohovat a obnovit zálohovací prostředí aplikace, aby vám pomohly splnit požadavky RPO a RTO, najdete v rámci svého architekta zálohování.

## <a name="storsimple-as-a-restore-source"></a>StorSimple jako zdroj obnovení

Obnovení ze zařízení StorSimple funguje jako obnovení ze všech zařízení blokového úložiště. Obnovení dat, která jsou vrstvená do cloudu, probíhá při rychlosti cloudu. Pro místní data dojde k obnovení při rychlosti místního disku zařízení. Informace o tom, jak provést obnovení, najdete v dokumentaci ke službě Backup Exec. Doporučujeme, abyste v souladu s osvědčenými postupy pro obnovení záložního Exec.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple převzetí služeb při selhání a zotavení po havárii

> [!NOTE]
> U scénářů cíle zálohování se StorSimple Cloud Appliance nepodporuje jako cíl obnovení.

Havárie může být způsobeno nejrůznějšími faktory. V následující tabulce je uveden seznam běžných scénářů zotavení po havárii.

| Scenario | Dopad | Postup obnovení | Poznámky |
|---|---|---|---|
| Selhání zařízení StorSimple | Operace zálohování a obnovení jsou přerušeny. | Nahraďte neúspěšné zařízení a proveďte [převzetí služeb při selhání StorSimple a zotavení po havárii](./storsimple-8000-device-failover-disaster-recovery.md). | Pokud po obnovení zařízení potřebujete provést obnovení, všechny pracovní sady dat se načítají z cloudu do nového zařízení. Všechny operace jsou v cloudových rychlostech. Proces opakovaného prohledávání indexování a katalogu může způsobit, že se všechny zálohovací sklady kontrolují a nastavují z vrstvy cloudu na úroveň místního zařízení, což může být časově náročný proces. |
| Selhání serveru Backup Exec | Operace zálohování a obnovení jsou přerušeny. | Znovu sestavte záložní server a proveďte obnovení databáze podle podrobných postupů v tématu [Postup ručního zálohování a obnovení databáze Backup Exec (BEDB)](http://www.veritas.com/docs/000041083). | Na serveru pro zotavení po havárii je nutné znovu sestavit nebo obnovit Server Backup Exec. Obnovte databázi do nejnovějšího bodu. Pokud obnovená databáze Backup Exec není synchronizovaná s vašimi nejnovějšími úlohami zálohování, je nutné indexování a vytváření katalogu. Tento index a proces opětovného prohledání katalogu může způsobit, že se všechny zálohovací sklady prohledají a nastavují z vrstvy cloudu na úroveň místního zařízení. Díky tomu je tato operace časově náročná. |
| Selhání lokality, které vede ke ztrátě záložního serveru i StorSimple | Operace zálohování a obnovení jsou přerušeny. | Nejprve obnovte StorSimple a pak obnovte zálohovací Exec. | Nejprve obnovte StorSimple a pak obnovte zálohovací Exec. Pokud po obnovení zařízení potřebujete provést obnovení, všechny pracovní sady dat se z cloudu načtou do nového zařízení. Všechny operace jsou v cloudových rychlostech. |

## <a name="references"></a>Reference

Následující dokumenty byly odkazovány na tento článek:

- [StorSimple instalaci funkce Multipath I/O](./storsimple-8000-configure-mpio-windows-server.md)
- [Scénáře úložiště: dynamické zajišťování](/windows-hardware/drivers/storage/thin-provisioning)
- [Použití jednotek GPT](/previous-versions/windows/hardware/design/dn653580(v=vs.85)#EHD)
- [Nastavení stínových kopií pro sdílené složky](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771893(v=ws.11))

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o tom, jak [obnovit ze zálohovacího skladu](./storsimple-8000-restore-from-backup-set-u2.md).
- Další informace o tom, jak provést [převzetí služeb při selhání a zotavení po havárii zařízení](./storsimple-8000-device-failover-disaster-recovery.md).