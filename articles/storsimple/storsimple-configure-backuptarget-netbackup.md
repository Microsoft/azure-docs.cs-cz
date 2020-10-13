---
title: Řady StorSimple 8000 jako cíl zálohování s NetBackup | Microsoft Docs
description: Přečtěte si o konfiguraci cílů zálohování StorSimple pomocí VERITAS NetBackup a osvědčených postupů pro integraci obou řešení.
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
ms.date: 06/15/2017
ms.author: matd
ms.openlocfilehash: 23afa82ffda5341242c01cbe024fb71f482345d5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91710919"
---
# <a name="storsimple-as-a-backup-target-with-netbackup"></a>StorSimple jako cíl zálohování s NetBackup

## <a name="overview"></a>Přehled

Azure StorSimple je řešení hybridního cloudového úložiště od Microsoftu. StorSimple řeší složitosti exponenciálního nárůstu dat pomocí účtu Azure Storage jako rozšíření místního řešení a automaticky přenáší data napříč místním úložištěm a cloudovým úložištěm.

V tomto článku probereme integraci StorSimple s VERITAS NetBackup a osvědčenými postupy pro integraci obou řešení. Poskytujeme také doporučení, jak nastavit VERITAS NetBackup, aby se co nejlépe integroval s StorSimple. Od společnosti Veritas osvědčené postupy, architekti zálohování a správců odložíme nejlepší způsob, jak nastavit VERITAS NetBackup tak, aby splňovaly jednotlivé požadavky na zálohování a smlouvy o úrovni služeb (SLA).

I když ilustrují konfigurační kroky a klíčové koncepty, Tento článek není nijak podrobných konfigurací nebo Průvodce instalací nástroje. Předpokládáme, že základní komponenty a infrastruktura jsou v pracovním řádu a připravené k podpoře konceptů, které popisujeme.

### <a name="who-should-read-this"></a>Kdo by se měl číst?

Informace v tomto článku budou nejužitečnější pro správce zálohování, Správce úložiště a architekty úložiště, kteří mají zkušenosti s úložištěm, Windows Server 2012 R2, Ethernet, Cloud Services a VERITAS NetBackup.

### <a name="supported-versions"></a>Podporované verze

-   NetBackup 7.7. x a novější verze
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
 ![ Diagram vrstvení StorSimple](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

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

Další informace o StorSimple najdete v tématu [StorSimple 8000 series: řešení hybridního cloudového úložiště](storsimple-overview.md). Můžete si také projít [specifikacemi řady Technical StorSimple 8000](storsimple-technical-specifications-and-compliance.md).

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
| Sekundární zálohování | Sekundární kopie zálohovaných dat se dá ukládat do kapacity cloudu.  | Není k dispozici  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple jako primární cíl zálohování

V tomto scénáři se StorSimple svazky prezentují do zálohovací aplikace jako jediné úložiště pro zálohy. Následující obrázek ukazuje architekturu řešení, ve které všechny zálohy používají StorSimple vrstvené svazky pro zálohování a obnovení.

![StorSimple jako primární Logický diagram cíle zálohování](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Logické kroky zálohování primárního cíle

1.  Záložní server kontaktuje cílového agenta Zálohování a Agent zálohování přenáší data na záložní server.
2.  Záložní server zapisuje data do vrstvených svazků StorSimple.
3.  Záložní server aktualizuje databázi katalogu a pak dokončí úlohu zálohování.
4.  Skript snímku spustí StorSimple Snapshot Manager (spustit nebo odstranit).
5.  Záložní server odstraní zálohy s vypršenou platností na základě zásad uchovávání informací.

### <a name="primary-target-restore-logical-steps"></a>Logické kroky obnovení primárního cíle

1.  Záložní server začne obnovovat vhodná data z úložiště úložiště.
2.  Agent zálohování obdrží data ze záložního serveru.
3.  Záložní server dokončí úlohu obnovení.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple jako cíl sekundárního zálohování

V tomto scénáři se StorSimple svazky primárně používají k dlouhodobému uchovávání nebo archivaci.

Následující obrázek ukazuje architekturu, ve které počáteční zálohy a obnovení cílí na svazek s vysokým výkonem. Tyto zálohy se zkopírují a archivují na StorSimple vrstvený svazek podle nastaveného plánu.

Je důležité mít velikost vysoce výkonného svazku, aby mohla zvládnout požadavky na kapacitu a výkon zásad uchovávání informací.

![Diagram znázorňující architekturu, ve které se počáteční zálohy a obnovení cíle na vysoký výkon.](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Logické kroky zálohování sekundárního cíle

1.  Záložní server kontaktuje cílového agenta Zálohování a Agent zálohování přenáší data na záložní server.
2.  Záložní server zapisuje data do úložiště s vysokým výkonem.
3.  Záložní server aktualizuje databázi katalogu a pak dokončí úlohu zálohování.
4.  Záložní server kopíruje zálohy do StorSimple na základě zásad uchovávání informací.
5.  Skript snímku spustí StorSimple Snapshot Manager (spustit nebo odstranit).
6.  Záložní server odstraní zálohy s vypršenou platností na základě zásad uchovávání informací.

### <a name="secondary-target-restore-logical-steps"></a>Logické kroky obnovení sekundárního cíle

1.  Záložní server začne obnovovat vhodná data z úložiště úložiště.
2.  Agent zálohování obdrží data ze záložního serveru.
3.  Záložní server dokončí úlohu obnovení.

## <a name="deploy-the-solution"></a>Nasazení řešení

Nasazení tohoto řešení vyžaduje tři kroky:
1. Připravte síťovou infrastrukturu.
2. Nasaďte zařízení StorSimple jako cíl zálohování.
3. Nasaďte VERITAS NetBackup.

Jednotlivé kroky jsou podrobně popsány v následujících částech.

### <a name="set-up-the-network"></a>Nastavit síť

Vzhledem k tomu, že StorSimple je řešení, které je integrované s cloudem Azure, vyžaduje StorSimple aktivní a funkční připojení ke cloudu Azure. Toto připojení se používá pro operace, jako jsou cloudové snímky, Správa dat a přenos metadat, a to na úrovni starších a méně využitých dat do cloudového úložiště Azure.

Aby se řešení provádělo optimálně, doporučujeme dodržovat tyto osvědčené postupy sítě:

-   Odkaz, který připojuje vrstvu StorSimple do Azure, musí splňovat požadavky na šířku pásma. Chcete-li toho dosáhnout, použijte správnou úroveň QoS (Quality of Service) na přepínače infrastruktury, aby odpovídaly cíli RPO a času obnovení (RTO) SLA.

-   Maximální počet latencí přístupu k úložišti objektů BLOB v Azure by měl být okolo 80 MS.

### <a name="deploy-storsimple"></a>Nasazení StorSimple

Podrobné pokyny k nasazení StorSimple najdete v tématu [nasazení místního zařízení StorSimple](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-netbackup"></a>Nasazení NetBackup

Podrobné pokyny k nasazení NetBackup 7.7. x najdete v [dokumentaci k NetBackup 7.7. x](http://www.veritas.com/docs/000094423).

## <a name="set-up-the-solution"></a>Nastavení řešení

V této části předvádíme některé příklady konfigurace. Následující příklady a doporučení znázorňují základní a základní implementaci. Tato implementace se nemusí vztahovat přímo na vaše konkrétní požadavky na zálohování.

### <a name="set-up-storsimple"></a>Nastavení StorSimple

| StorSimple úlohy nasazení  | Další komentáře |
|---|---|
| Nasaďte vaše místní zařízení StorSimple. | Podporované verze: aktualizace 3 a novější verze. |
| Zapněte cíl zálohování. | Pomocí těchto příkazů můžete zapnout nebo vypnout režim cíle zálohování a získat stav. Další informace najdete v tématu [vzdálené připojení k zařízení StorSimple](storsimple-remote-connect.md).</br> Zapnutí režimu zálohování: `Set-HCSBackupApplianceMode -enable` . </br> Vypnutí režimu `Set-HCSBackupApplianceMode -disable` zálohování: </br> Získání aktuálního stavu nastavení režimu zálohování: `Get-HCSBackupApplianceMode` . |
| Vytvořte pro svazek společný kontejner svazků, ve kterém jsou uložena data záloh. Všechna data v kontejneru svazků mají za následek odstranění duplicitních dat. | Kontejnery svazků StorSimple definují domény odstranění duplicit.  |
| Vytvořte StorSimple svazky. | Vytvářejte svazky s velikostí co nejblíže předpokládanému využití, protože velikost svazku ovlivňuje dobu trvání snímku cloudu. Informace o tom, jak velikost svazku získat, najdete v tématu o [zásadách uchovávání informací](#retention-policies).</br> </br> Použijte StorSimple vrstvené svazky a zaškrtněte políčko **použít tento svazek pro archivní data, ke kterým se přistupuje méně často** . </br> Použití pouze místně připojených svazků není podporováno. |
| Vytvořte jedinečné zásady zálohování StorSimple pro všechny cílové svazky zálohy. | Zásada zálohování StorSimple definuje skupinu konzistence svazku. |
| Zakažte plán jako vypršení platnosti snímků. | Snímky se spouštějí jako operace následného zpracování. |

### <a name="set-up-the-host-backup-server-storage"></a>Nastavení úložiště záložního serveru hostitele

Nastavte úložiště záložního serveru hostitele podle těchto pokynů:  

- Nepoužívat rozložené svazky (vytvořené nástrojem Správa disků systému Windows); rozložené svazky se nepodporují.
- Naformátujte svazky pomocí systému souborů NTFS s velikostí alokace 64 – KB.
- Namapujte StorSimple svazky přímo na server NetBackup.
    - Pro fyzické servery použijte iSCSI.
    - Používejte průchozí disky pro virtuální servery.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Osvědčené postupy pro StorSimple a NetBackup

Nastavte své řešení podle pokynů v následujících částech.

### <a name="operating-system-best-practices"></a>Osvědčené postupy pro operační systém

- Zakažte šifrování a odstranění duplicitních dat Windows serveru pro systém souborů NTFS.
- Zakažte defragmentaci Windows serveru na svazcích StorSimple.
- Zakažte indexování Windows serveru na svazcích StorSimple.
- Spusťte kontrolu antivirové ochrany na zdrojovém hostiteli (nikoli na svazcích StorSimple).
- Vypněte výchozí [údržbu Windows serveru](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) ve Správci úloh. Udělejte to jedním z následujících způsobů:
  - Vypněte Konfigurátor údržby ve Windows Plánovač úloh.
  - Stáhněte si [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) ze systému Windows Sysinternals. Po stažení PsExec spusťte Windows PowerShell jako správce a zadejte:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>Osvědčené postupy pro StorSimple

-   Ujistěte se, že se zařízení StorSimple aktualizovalo na [aktualizaci 3 nebo novější](storsimple-install-update-3.md).
-   Izolujte provoz iSCSI a Cloud. Pro přenosy mezi StorSimple a záložním serverem používejte vyhrazená připojení iSCSI.
-   Ujistěte se, že vaše zařízení StorSimple je vyhrazený cíl zálohování. Smíšené úlohy se nepodporují, protože mají vliv na RTO a RPO.

### <a name="netbackup-best-practices"></a>Osvědčené postupy pro NetBackup

-   Databáze NetBackup by měla být místní pro server a nesmí se nacházet na svazku StorSimple.
-   V případě zotavení po havárii zálohujte databázi NetBackup na svazku StorSimple.
-   Pro toto řešení podporujeme NetBackup úplné a přírůstkové zálohování (označované také jako rozdílové přírůstkové zálohování v NetBackup). Doporučujeme nepoužívat syntetické a kumulativní přírůstkové zálohování.
-   Soubory dat zálohy by měly obsahovat pouze data pro konkrétní úlohu. Například připojení k médiím v různých úlohách není povoleno.

Nejnovější nastavení NetBackup a osvědčené postupy pro implementaci těchto požadavků naleznete v dokumentaci NetBackup na adrese [www.Veritas.com](https://www.veritas.com).


## <a name="retention-policies"></a>Zásady uchovávání informací

Jedním z nejběžnějších typů zásad uchovávání záloh je zásada dědečka, otce a syn (GFS). V zásadách GFS se provádí přírůstkové zálohování každý den a úplné zálohování se provádí týdně a měsíčně. Výsledkem této zásady jsou šest StorSimple vrstvených svazků: jeden svazek obsahuje týdenní, měsíční a roční úplné zálohování. druhými pěti svazky se ukládají každodenní přírůstkové zálohování.

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

## <a name="set-up-netbackup-storage"></a>Nastavení úložiště NetBackup

### <a name="to-set-up-netbackup-storage"></a>Nastavení úložiště NetBackup

1.  V konzole pro správu NetBackup vyberte **média a zařízení Správa zařízení**  >  **Devices**  >  **fondy disků**. V Průvodci konfigurací fondu disků vyberte typ serveru úložiště **AdvancedDisk**a pak vyberte **Další**.

    ![Konzola pro správu NetBackup, Průvodce konfigurací fondu disků](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  Vyberte server a pak vyberte **Další**.

    ![NetBackup konzoly pro správu vyberte server](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  Vyberte StorSimple svazek.

    ![NetBackup konzoly pro správu vyberte disk s svazkem StorSimple](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  Zadejte název cíle zálohování a pak kliknutím na **Další**  >  **Další** dokončete průvodce.

5.  Zkontrolujte nastavení a pak vyberte **Dokončit**.

6.  Na konci každého přiřazení svazku změňte nastavení zařízení úložiště tak, aby se shodovalo s doporučenými [postupy pro StorSimple a NetBackup](#best-practices-for-storsimple-and-netbackup).

7. Opakujte kroky 1-6, dokud nedokončíte přiřazování svazků StorSimple.

    ![Konzola pro správu NetBackup, konfigurace disku](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Nastavte StorSimple jako primární cíl zálohování.

> [!NOTE]
> Data se obnoví ze zálohy, která byla vrstvená do cloudu, při rychlosti cloudu.

Následující obrázek ukazuje mapování typického svazku na úlohu zálohování. V tomto případě se všechny týdenní zálohy mapují na celý disk v sobotu a mapa přírůstkových záloh na Monday-Friday přírůstkové disky. Všechny zálohy a obnovení jsou ze StorSimple vrstveného svazku.

![Logický diagram primární konfigurace cíle zálohování](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Příklad plánu StorSimple jako primární cíl zálohování GFS

Tady je příklad plánu GFS rotace na čtyři týdny, měsíčně a ročně:

| Frekvence/typ zálohování | Do bloku | Přírůstkové (dny 1-5)  |   
|---|---|---|
| Týdně (týdny 1-4) | Sobota | Monday-Friday |
| Měsíčně  | Sobota  |   |
| Ročně | Sobota  |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>Přiřazení svazků StorSimple k úloze zálohování NetBackup

Následující sekvence předpokládá, že NetBackup a cílový hostitel jsou nakonfigurovány v souladu s pokyny pro agenta NetBackup.

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>Přiřazení svazků StorSimple do úlohy zálohování NetBackup

1. V konzole pro správu NetBackup vyberte **Správa NetBackup**, klikněte pravým tlačítkem na **zásady**a pak vyberte **Nová zásada**.

   ![Konzola pro správu NetBackup, vytvoření nové zásady](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2. V dialogovém okně **Přidat novou zásadu** zadejte název zásady a potom zaškrtněte políčko **použít Průvodce konfigurací zásad** . Vyberte **OK**.

   ![Konzola pro správu NetBackup, dialogové okno Přidat novou zásadu](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3. V Průvodci konfigurací zásady zálohování zvolte požadovaný typ zálohování a pak vyberte **Další**.

   ![NetBackup konzoly pro správu, vyberte typ zálohování.](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4. Chcete-li nastavit typ zásady, vyberte možnost **standardní**a potom vyberte možnost **Další**.

   ![Konzola pro správu NetBackup, výběr typu zásad](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5. Vyberte hostitele, zaškrtněte políčko **rozpoznat klientský operační systém** a potom vyberte **Přidat**. Vyberte **Další**.

   ![Konzola pro správu NetBackup, seznam klientů v nové zásadě](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6. Vyberte jednotky, které chcete zálohovat.

   ![Konzola pro správu NetBackup, výběry zálohování pro nové zásady](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7. Vyberte četnost a hodnoty uchování, které splňují vaše požadavky na rotaci zálohování.

   ![Konzola pro správu NetBackup, četnost zálohování a rotace pro nové zásady](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8. Vyberte **Další**  >  **Další**  >  **dokončení**.  Po vytvoření zásady můžete plán upravit.

9. Tuto možnost vyberte, pokud chcete rozšířit zásadu, kterou jste právě vytvořili, a pak vyberte **plány**.

   ![NetBackup konzoly pro správu a plánuje nové zásady](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10. Klikněte pravým tlačítkem na možnost **diferenciál – Inc**, vyberte možnost **Kopírovat do nového**a pak vyberte **OK**.

    ![Konzola pro správu NetBackup, kopírování plánu do nové zásady](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11. Klikněte pravým tlačítkem na nově vytvořený plán a pak vyberte **změnit**.

12. Na kartě **atributy** zaškrtněte políčko pro **Výběr úložiště zásad přepsání** a potom vyberte svazek, ve kterém pondělí přírůstkové zálohy projdou.

    ![Konzola pro správu NetBackup, změna plánu](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13. Na kartě **Start okna** vyberte časový interval pro zálohování.

    ![Konzola pro správu NetBackup, změna okna Start](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14. Vyberte **OK**.

15. Opakujte kroky 10-14 pro každé přírůstkové zálohování. Vyberte příslušný svazek a plán pro každou vytvořenou zálohu.

16. Klikněte pravým tlačítkem na plán **diferenciál-Inc** a odstraňte ho.

17. Upravte úplný plán tak, aby vyhovoval vašim potřebám zálohování.

    ![Konzola pro správu NetBackup, změna úplného plánu](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18. Změňte počáteční okno.

    ![Konzola pro správu NetBackup, Změna počátečního okna](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19. Konečný plán vypadá takto:

    ![Konzola pro správu NetBackup, konečný plán](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Nastavení StorSimple jako sekundárního cíle zálohování

> [!NOTE]
>Data se obnoví ze zálohy, která byla vrstvená do cloudu, při rychlosti cloudu.

V tomto modelu musíte mít úložné médium (jiné než StorSimple), které slouží jako dočasnou mezipaměť. Můžete například použít redundantní pole svazku RAID (RAID) k umístění, vstupu a výstupu (I/O) a šířce pásma. Doporučujeme použít RAID 5, 50 a 10.

Následující obrázek ukazuje typické krátkodobé uchovávání místního úložiště (na server) a svazky pro dlouhodobé uchovávání. V tomto scénáři se všechny zálohy spouštějí na místním svazku (na serveru) RAID. Tyto zálohy jsou pravidelně duplikovány a archivovány do svazku archivu. Je důležité nastavit velikost místního svazku (na server) RAID tak, aby mohla zvládnout vaše krátkodobé kapacity uchovávání a požadavky na výkon.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple jako sekundární cíl zálohování – příklad GFS

![Logický diagram StorSimple jako sekundární cíl zálohování](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

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


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Přiřazení svazků StorSimple k úloze archivace a duplikování NetBackup

Vzhledem k tomu, že NetBackup nabízí celou řadu možností pro správu úložiště a médií, doporučujeme vám poradit s Veritas nebo architektem NetBackup, abyste správně vyhodnotili požadavky na zásady životního cyklu úložiště (SLP).

Po definování počátečních fondů disků musíte definovat tři další zásady životního cyklu úložiště, celkem tedy čtyři zásady:
* LocalRAIDVolume
* StorSimpleWeek2-4
* StorSimpleMonthlyFulls
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Přiřazení svazků StorSimple k úloze archivace a duplikování NetBackup

1. V konzole pro správu NetBackup vyberte **Storage**  >  **Zásady životního cyklu**úložiště úložiště  >  **nové zásady životního cyklu úložiště**.

   ![Konzola pro správu NetBackup, nová zásada životního cyklu úložiště](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2. Zadejte název snímku a pak vyberte **Přidat**.

3. V dialogovém okně **Nová operace** na kartě **vlastnosti** pro **operace**vyberte možnost **zálohování**. Vyberte hodnoty, které chcete pro **cílové úložiště**, **typ uchování**a **dobu uchování**. Vyberte **OK**.

   ![Konzola pro správu NetBackup, dialogové okno Nová operace](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

   Tím se definuje první operace zálohování a úložiště.

4. Výběrem této možnost zvýrazníte předchozí operaci a pak vyberete **Přidat**. V dialogovém okně **změnit operaci úložiště** vyberte požadované hodnoty pro **cílové úložiště**, **typ uchování**a **dobu uchování**.

   ![Konzola pro správu NetBackup, dialogové okno změnit operaci úložiště](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5. Výběrem této možnost zvýrazníte předchozí operaci a pak vyberete **Přidat**. V dialogovém okně **nové zásady životního cyklu úložiště** přidejte měsíční zálohy za rok.

   ![Konzola pro správu NetBackup, dialogové okno nové zásady životního cyklu úložiště](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6. Opakujte kroky 4-5, dokud nevytvoříte komplexní zásadu uchovávání SLP, kterou potřebujete.

   ![Konzola pro správu NetBackup, přidání zásad v dialogovém okně nové zásady životního cyklu úložiště](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7. Až dokončíte definování zásad uchovávání SLP, v části **zásady**Definujte zásadu zálohování podle kroků popsaných v části [přiřazení svazků StorSimple k úloze zálohování NetBackup](#assigning-storsimple-volumes-to-a-netbackup-backup-job).

8. V části **plány**v dialogovém okně **změnit plán** klikněte pravým tlačítkem na **úplný**a pak vyberte **změnit**.

   ![Konzola pro správu NetBackup, dialogové okno změnit plán](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9. Zaškrtněte políčko **přepsat výběr úložiště zásad** a potom vyberte zásady uchovávání SLP, které jste vytvořili v krocích 1-6.

   ![Konzola pro správu NetBackup, přepsání výběru úložiště zásad](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10. Vyberte **OK**a opakujte postup pro plán přírůstkového zálohování.

    ![Konzola pro správu NetBackup, dialogové okno změnit plán pro přírůstkové zálohování](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| Uchování typu zálohování | Velikost (TiB) | Multiplikátor GFS\* | Celková kapacita (TiB)  |
|---|---|---|---|
| Týdně úplné |  1  |  4 | 4  |
| Denní přírůstkový  | 0,5  | 20 (počet cyklů se rovná počtu týdnů za měsíc) | 12 (2 pro další kvótu) |
| Úplně měsíčně  | 1 | 12 | 12 |
| Celý rok na celé | 1  | 10 | 10 |
| Požadavek GFS  |     |     | 38 |
| Dodatečná kvóta  | 4  |    | 42 celková GFS požadavek |

\* Multiplikátor GFS je počet kopií, které je třeba chránit a které je potřeba zachovat, aby splňovaly požadavky zásad zálohování.

## <a name="storsimple-cloud-snapshots"></a>StorSimple cloudové snímky

StorSimple cloudové snímky chrání data, která se nachází ve vašem zařízení StorSimple. Vytvoření snímku v cloudu je ekvivalentní k přenosu pásek místních záloh do zařízení mimo pracoviště. Pokud používáte geograficky redundantní úložiště Azure, vytvoření snímku v cloudu je ekvivalentní k přenosu pásek zálohování na více lokalit. Pokud po havárii potřebujete zařízení obnovit, můžete jiné zařízení StorSimple převést do online režimu a provést převzetí služeb při selhání. Po převzetí služeb při selhání byste měli získat přístup k datům (s rychlostí cloudu) z posledního snímku cloudu.

Následující část popisuje, jak vytvořit krátký skript pro spuštění a odstranění snímků StorSimple cloudu během následného zpracování zálohy.

> [!NOTE]
> Ručně nebo programově vytvořené snímky nedodržují zásady vypršení platnosti snímku StorSimple. Tyto snímky je nutné ručně nebo programově odstranit.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Spuštění a odstranění cloudových snímků pomocí skriptu

> [!NOTE]
> Před odstraněním snímku StorSimple pečlivě vyhodnoťte dodržování předpisů a uchování dat. Další informace o spuštění následného zálohovacího skriptu najdete v [dokumentaci k NetBackup](http://www.veritas.com/docs/000094423).

### <a name="backup-lifecycle"></a>Životní cyklus zálohování

![Diagram životního cyklu zálohování](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

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
4. Přidejte skript do úlohy zálohování v NetBackup. Provedete to tak, že upravíte příkazy pro předběžné zpracování a následné zpracování úloh NetBackup.

> [!NOTE]
> Doporučujeme, abyste na konci každodenní úlohy zálohování spustili zásadu zálohování snímku StorSimple cloudu jako skript po zpracování. Další informace o tom, jak zálohovat a obnovit zálohovací prostředí aplikace, aby vám pomohly splnit požadavky RPO a RTO, najdete v rámci svého architekta zálohování.

## <a name="storsimple-as-a-restore-source"></a>StorSimple jako zdroj obnovení

Obnovení ze zařízení StorSimple funguje jako obnovení ze všech zařízení blokového úložiště. Obnovení dat, která jsou vrstvená do cloudu, probíhá při rychlosti cloudu. Pro místní data dojde k obnovení při rychlosti místního disku zařízení. Informace o tom, jak provést obnovení, najdete v [dokumentaci k NetBackup](http://www.veritas.com/docs/000094423). Doporučujeme, abyste dodržovali osvědčené postupy pro obnovení NetBackup.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple převzetí služeb při selhání a zotavení po havárii

> [!NOTE]
> U scénářů cíle zálohování se StorSimple Cloud Appliance nepodporuje jako cíl obnovení.

Havárie může být způsobeno nejrůznějšími faktory. V následující tabulce je uveden seznam běžných scénářů zotavení po havárii.

| Scénář | Dopad | Postup obnovení | Poznámky |
|---|---|---|---|
| Selhání zařízení StorSimple | Operace zálohování a obnovení jsou přerušeny. | Nahraďte neúspěšné zařízení a proveďte [převzetí služeb při selhání StorSimple a zotavení po havárii](storsimple-device-failover-disaster-recovery.md). | Pokud po obnovení zařízení potřebujete provést obnovení, všechny pracovní sady dat se načítají z cloudu do nového zařízení. Všechny operace jsou v cloudových rychlostech. Proces opakovaného prohledání indexu a katalogu může způsobit, že se všechny zálohovací sklady kontrolují a nastavují z vrstvy cloudu na úroveň místního zařízení, což může být časově náročný proces. |
| Selhání serveru NetBackup | Operace zálohování a obnovení jsou přerušeny. | Znovu sestavte záložní server a proveďte obnovení databáze. | Server NetBackup je nutné znovu sestavit nebo obnovit na webu pro zotavení po havárii. Obnovte databázi do nejnovějšího bodu. Pokud obnovená databáze NetBackup není synchronizovaná s nejnovějšími úlohami zálohování, je nutné indexování a vytváření katalogu. Tento index a proces opětovného prohledání katalogu může způsobit, že se všechny zálohovací sklady prohledají a nastavují z vrstvy cloudu na úroveň místního zařízení. Díky tomu je tato operace časově náročná. |
| Selhání lokality, které vede ke ztrátě záložního serveru i StorSimple | Operace zálohování a obnovení jsou přerušeny. | Nejprve obnovte StorSimple a pak obnovte NetBackup. | Nejprve obnovte StorSimple a pak obnovte NetBackup. Pokud po obnovení zařízení potřebujete provést obnovení, všechny pracovní sady dat se z cloudu načtou do nového zařízení. Všechny operace jsou v cloudových rychlostech. |

## <a name="references"></a>Reference

Následující dokumenty byly odkazovány na tento článek:

- [StorSimple instalaci funkce Multipath I/O](storsimple-configure-mpio-windows-server.md)
- [Scénáře úložiště: dynamické zajišťování](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Použití jednotek GPT](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Nastavení stínových kopií pro sdílené složky](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o tom, jak [obnovit ze zálohovacího skladu](storsimple-restore-from-backup-set-u2.md).
- Další informace o tom, jak provést [převzetí služeb při selhání a zotavení po havárii zařízení](storsimple-device-failover-disaster-recovery.md).
