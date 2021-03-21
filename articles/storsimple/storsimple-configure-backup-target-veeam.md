---
title: Řady StorSimple 8000 jako cíl zálohování s Veeam | Microsoft Docs
description: Přečtěte si o konfiguraci cílů zálohování StorSimple pomocí Veeam a osvědčených postupů pro integraci obou řešení.
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
ms.date: 12/06/2016
ms.author: matd
ms.openlocfilehash: bf28265de2b297dade545695c9369b8074eeb72c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94962548"
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>StorSimple jako cíl zálohování s Veeam

## <a name="overview"></a>Přehled

Azure StorSimple je řešení hybridního cloudového úložiště od Microsoftu. StorSimple řeší složitosti exponenciálního nárůstu dat pomocí Azure Storage účtu jako rozšíření místního řešení a Automatické vrstvení dat napříč místním úložištěm a cloudovým úložištěm.

V tomto článku probereme integraci StorSimple s Veeam a osvědčenými postupy pro integraci obou řešení. Také jsme povedli doporučení, jak nastavit Veeam tak, aby se co nejlépe integroval s StorSimple. Neodkladně Veeam osvědčené postupy, architekty pro zálohování a správce, aby bylo možné nastavit Veeam tak, aby splňovaly jednotlivé požadavky na zálohování a smlouvy o úrovni služeb (SLA).

I když ilustrují konfigurační kroky a klíčové koncepty, Tento článek není nijak podrobných konfigurací nebo Průvodce instalací nástroje. Předpokládáme, že základní komponenty a infrastruktura jsou v pracovním řádu a připravené k podpoře konceptů, které popisujeme.

### <a name="who-should-read-this"></a>Kdo by se měl číst?

Informace v tomto článku budou nejužitečnější pro správce zálohování, Správce úložiště a architekty úložiště, kteří mají zkušenosti s úložištěm, Windows Server 2012 R2, Ethernet, Cloud Services a Veeam.

### <a name="supported-versions"></a>Podporované verze

-   Veeam 9 a novější verze
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
 ![ Diagram vrstvení StorSimple](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

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

| Kapacita úložiště | 8100 | 8600 |
|---|---|---|
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

![StorSimple jako primární Logický diagram cíle zálohování](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

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

![Logický diagram StorSimple jako sekundární cíl zálohování](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

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
3. Nasaďte Veeam.

Jednotlivé kroky jsou podrobně popsány v následujících částech.

### <a name="set-up-the-network"></a>Nastavit síť

Vzhledem k tomu, že StorSimple je řešení, které je integrované s cloudem Azure, vyžaduje StorSimple aktivní a funkční připojení ke cloudu Azure. Toto připojení se používá pro operace, jako jsou cloudové snímky, Správa dat a přenos metadat, a to na úrovni starších a méně využitých dat do cloudového úložiště Azure.

Aby se řešení provádělo optimálně, doporučujeme dodržovat tyto osvědčené postupy sítě:

-   Odkaz, který připojuje vaše rozhraní StorSimple do Azure, musí splňovat požadavky na šířku pásma. Dosáhnete toho tím, že použijete potřebnou úroveň QoS (Quality of Service) na vaše přepínače infrastruktury tak, aby odpovídaly cíli RPO a času obnovení (RTO) SLA.
-   Maximální počet latencí přístupu k úložišti objektů BLOB v Azure by měl být okolo 80 MS.

### <a name="deploy-storsimple"></a>Nasazení StorSimple

Podrobné pokyny k nasazení StorSimple najdete v tématu [nasazení místního zařízení StorSimple](./storsimple-8000-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Nasazení Veeam

Osvědčené postupy k instalaci Veeam najdete v článku [osvědčené postupy pro replikaci Veeam Backup &](https://bp.veeam.expert/)a přečtěte si uživatelskou příručku v [centru pro technickou dokumentaci pro Veeam](https://www.veeam.com/documentation-guides-datasheets.html).

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

- Nepoužívejte rozložené svazky (vytvořené pomocí správy disků systému Windows). Rozložené svazky se nepodporují.
- Naformátujte svazky pomocí systému souborů NTFS s velikostí alokační jednotky 64-KB.
- Namapujte StorSimple svazky přímo na server Veeam.
    - Pro fyzické servery použijte iSCSI.


## <a name="best-practices-for-storsimple-and-veeam"></a>Osvědčené postupy pro StorSimple a Veeam

Nastavte své řešení podle pokynů v následujících částech.

### <a name="operating-system-best-practices"></a>Osvědčené postupy pro operační systém

- Zakažte šifrování a odstranění duplicitních dat Windows serveru pro systém souborů NTFS.
- Zakažte defragmentaci Windows serveru na svazcích StorSimple.
- Zakažte indexování Windows serveru na svazcích StorSimple.
- Spusťte kontrolu antivirové ochrany na zdrojovém hostiteli (nikoli na svazcích StorSimple).
- Vypněte výchozí [údržbu Windows serveru](/windows/win32/w8cookbook/automatic-maintenance) ve Správci úloh. Udělejte to jedním z následujících způsobů:
  - Vypněte Konfigurátor údržby ve Windows Plánovač úloh.
  - Stáhněte si [PsExec](/sysinternals/downloads/psexec) ze systému Windows Sysinternals. Po stažení PsExec spusťte Windows PowerShell jako správce a zadejte:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>Osvědčené postupy pro StorSimple

-   Ujistěte se, že se zařízení StorSimple aktualizovalo na [aktualizaci 3 nebo novější](./index.yml).
-   Izolujte provoz iSCSI a Cloud. Pro přenosy mezi StorSimple a záložním serverem používejte vyhrazená připojení iSCSI.
-   Ujistěte se, že vaše zařízení StorSimple je vyhrazený cíl zálohování. Smíšené úlohy se nepodporují, protože mají vliv na RTO a RPO.

### <a name="veeam-best-practices"></a>Osvědčené postupy pro Veeam

-   Databáze Veeam by měla být místní pro server a nesmí se nacházet na svazku StorSimple.
-   V případě zotavení po havárii zálohujte databázi Veeam na svazku StorSimple.
-   Podporujeme Veeam úplné a přírůstkové zálohování pro toto řešení. Doporučujeme nepoužívat syntetické a rozdílové zálohy.
-   Soubory dat zálohy by měly obsahovat pouze data pro konkrétní úlohu. Například připojení k médiím v různých úlohách není povoleno.
-   Vypněte ověřování úlohy. V případě potřeby by se mělo ověřování naplánovat po poslední úloze zálohování. Je důležité pochopit, že tato úloha má vliv na okno zálohování.
-   Zapněte předběžné přidělování médií.
-   Ujistěte se, že je zapnuté paralelní zpracování.
-   Vypněte kompresi.
-   Vypněte funkci odstranění duplicitních dat u úlohy zálohování.
-   Nastavte optimalizaci na **cíl sítě LAN**.
-   Zapněte **vytváření aktivních úplných záloh** (každé 2 týdny).
-   V úložišti zálohování nastavte **použít záložní soubory pro jednotlivé virtuální počítače**.
-   Nastavte **možnost použít více datových proudů pro nahrávání na úlohu** na **8** (maximálně 16). Upravte toto číslo nahoru nebo dolů na základě využití procesoru na zařízení StorSimple.

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

## <a name="set-up-veeam-storage"></a>Nastavení úložiště Veeam

### <a name="to-set-up-veeam-storage"></a>Nastavení úložiště Veeam

1.  V konzole Veeam Backup a replikace v části **nástroje úložiště** přejít na **infrastruktura zálohování**. Klikněte pravým tlačítkem na **zálohovat** úložiště a pak vyberte **Přidat úložiště záloh**.

    ![Snímek obrazovky, který zobrazuje konzolu pro správu Veeam, a zvýrazní možnost přidat úložiště záloh.](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  V dialogovém okně **nové úložiště záloh** zadejte název a popis úložiště. Vyberte **Další**.

    ![Veeam konzoly pro správu, název a popis](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  Jako typ vyberte **Microsoft Windows Server**. Vyberte server Veeam. Vyberte **Další**.

    ![Veeam konzoly pro správu vyberte typ úložiště zálohování.](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  Chcete-li určit **umístění**, vyhledejte a vyberte svazek. Vyberte zaškrtávací políčko **omezit maximální počet souběžných úloh na:** a nastavte hodnotu na **4**. Tím se zajistí, že se souběžně zpracovávají jenom čtyři virtuální disky, zatímco se zpracovávají jednotlivé virtuální počítače (VM). Vyberte tlačítko **Upřesnit** .

    ![Veeam konzoly pro správu vyberte svazek.](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  V dialogovém okně **nastavení kompatibility úložiště** zaškrtněte políčko **použít záložní soubory pro jednotlivé virtuální počítače** .

    ![Konzola pro správu Veeam, nastavení kompatibility úložiště](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  V dialogovém okně **nové úložiště záloh** vyberte zaškrtávací políčko **Povolit službu vPower NFS na serveru pro připojení (doporučeno)** . Vyberte **Další**.

    ![Snímek obrazovky, který zobrazuje konzolu pro správu Veeam, do které můžete přidat nové úložiště zálohování.](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Zkontrolujte nastavení a pak vyberte **Další**.

    ![Konzola pro správu Veeam, stránka úložiště zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    Do serveru Veeam se přidá úložiště.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Nastavte StorSimple jako primární cíl zálohování.

> [!IMPORTANT]
> Obnovení dat ze zálohy, která byla vrstvená do cloudu, probíhá při rychlosti cloudu.

Následující obrázek ukazuje mapování typického svazku na úlohu zálohování. V tomto případě se všechny týdenní zálohy mapují na celý disk v sobotu a mapa přírůstkových záloh na Monday-Friday přírůstkové disky. Všechny zálohy a obnovení jsou ze StorSimple vrstveného svazku.

![Logický diagram primární konfigurace cíle zálohování](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Příklad plánu StorSimple jako primární cíl zálohování GFS

Tady je příklad plánu GFS rotace na čtyři týdny, měsíčně a ročně:

| Frekvence/typ zálohování | Do bloku | Přírůstkové (dny 1-5)  |   
|---|---|---|
| Týdně (týdny 1-4) | Sobota | Monday-Friday |
| Měsíčně  | Sobota  |   |
| Ročně | Sobota  |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>Přiřazení svazků StorSimple k úloze zálohování Veeam

U primárního cílového scénáře zálohování vytvořte každodenní úlohu s primárním svazkem Veeam StorSimple. V případě sekundárního cílového scénáře zálohování vytvořte každodenní úlohu pomocí přímého připojeného úložiště (DAS), síťového připojeného úložiště (NAS) nebo pouze úložiště JBOD (disk of disks).

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>Přiřazení svazků StorSimple do úlohy zálohování Veeam

1.  V konzole Veeam Backup a replikace vyberte **backup & replikace**. Klikněte pravým tlačítkem na **zálohovat** a pak v závislosti na vašem prostředí vyberte **VMware** nebo **Hyper-V**.

    ![Konzola pro správu Veeam, nová úloha zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  V dialogovém okně **Nová úloha zálohování** zadejte název a popis úlohy denního zálohování.

    ![Snímek obrazovky s konzolou pro správu Veeam, do které přidáte název a popis.](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Vyberte virtuální počítač pro zálohování.

    ![Snímek obrazovky, který zobrazuje konzolu pro správu Veeam, kde vyberete virtuální počítač.](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Vyberte hodnoty, které chcete pro **záložní proxy** a **úložiště zálohování**. Vyberte hodnotu pro **body obnovení, které se budou uchovávat na disku** na základě definic RPO a RTO pro vaše prostředí místně připojeného úložiště. Vyberte **Upřesnit**.

    ![Konzola pro správu Veeam, stránka nové úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. V dialogovém okně **Upřesnit nastavení** na kartě **zálohování** vyberte **přírůstkové**. Ujistěte se, že je políčko **vytvořit syntetické úplné zálohování pravidelně** zaškrtnuté. Zaškrtněte políčko **vytvořit aktivní úplné zálohy pravidelně** . V části **aktivní úplná záloha** zaškrtněte políčko **týdně pro vybrané dny** pro sobotu.

    ![Snímek obrazovky zobrazující konzolu pro správu Veeam, specifickou stránku pro pokročilá nastavení úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. Na kartě **úložiště** ověřte, že není zaškrtnuto políčko **Povolit vloženou datovou odstranění duplicitních dat** . Zaškrtněte políčko **vyloučit zaměnitelné bloky souborů** a zaškrtněte políčko **vyloučit odstraněné bloky souborů** . Nastavte **úroveň komprese** na **žádná**. Pro vyvážený výkon a odstranění duplicit nastavte **optimalizaci úložiště** na **cíl sítě LAN**. Vyberte **OK**.

    ![Veeam konzoly pro správu, nové nastavení úlohy zálohování – stránka Upřesnit nastavení](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Informace o odstraňování duplicit a nastaveních komprese Veeam najdete v tématu [komprese a odstraňování duplicitních dat](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html).

7.  V dialogovém okně **Upravit úlohu zálohování** můžete zaškrtnout políčko **Povolit zpracování pomocí aplikace** (volitelné).

    ![Konzola pro správu Veeam, stránka pro zpracování nového hosta úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Nastavte plán, který se má spustit jednou denně, v čase, který můžete zadat.

    ![Konzola pro správu Veeam, nová stránka plánování úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Nastavení StorSimple jako sekundárního cíle zálohování

> [!NOTE]
> Data se obnoví ze zálohy, která byla vrstvená do cloudu, při rychlosti cloudu.

V tomto modelu musíte mít úložné médium (jiné než StorSimple), které slouží jako dočasnou mezipaměť. Můžete například použít redundantní pole svazku RAID (RAID) k umístění, vstupu a výstupu (I/O) a šířce pásma. Doporučujeme použít RAID 5, 50 a 10.

Následující obrázek ukazuje typické krátkodobé uchovávání místních dat (na server) a svazky archivu pro dlouhodobé uchovávání. V tomto scénáři se všechny zálohy spouštějí na místním svazku (na serveru) RAID. Tyto zálohy jsou pravidelně duplikovány a archivovány do svazku archivu. Je důležité nastavit velikost místního svazku (na server) RAID tak, aby mohla zvládnout vaše krátkodobé kapacity uchovávání a požadavky na výkon.

![Logický diagram StorSimple jako sekundární cíl zálohování](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple jako sekundární cíl zálohování – příklad GFS

Následující tabulka ukazuje, jak nastavit zálohování pro spouštění na místních a StorSimple discích. Zahrnuje individuální a celkové požadavky na kapacitu.

| Typ a uchování zálohy | Nakonfigurované úložiště | Velikost (TiB) | Multiplikátor GFS | Celková kapacita \* (TIB) |
|---|---|---|---|---|
| Týden 1 (úplný a přírůstkový) |Místní disk (krátkodobý)| 1 | 1 | 1 |
| StorSimple týdny 2-4 |StorSimple disk (dlouhodobě) | 1 | 4 | 4 |
| Úplně měsíčně |StorSimple disk (dlouhodobě) | 1 | 12 | 12 |
| Celý rok na celé |StorSimple disk (dlouhodobě) | 1 | 1 | 1 |
|Požadavek na velikost svazků GFS |  |  |  | let|

\* Celková kapacita zahrnuje 17 TiB disků StorSimple a 1 TiB místního svazku RAID.


### <a name="gfs-example-schedule"></a>GFS příklad plánu

GFS rotace týdně, měsíčně a ročního plánu

| Týden | Do bloku | Přírůstkový den 1 | Přírůstkový den 2 | Přírůstkový den 3 | Přírůstkový den 4 | Přírůstkový den 5 |
|---|---|---|---|---|---|---|
| Týden 1 | Místní svazek RAID  | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID |
| Týden 2 | StorSimple týdny 2-4 |   |   |   |   |   |
| Týden 3 | StorSimple týdny 2-4 |   |   |   |   |   |
| Týden 4 | StorSimple týdny 2-4 |   |   |   |   |   |
| Měsíčně | StorSimple měsíčně |   |   |   |   |   |
| Ročně | StorSimple ročně  |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>Přiřazení svazků StorSimple k úloze kopírování Veeam

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>Přiřazení svazků StorSimple k úloze kopírování Veeam

1.  V konzole Veeam Backup a replikace vyberte **backup & replikace**. Klikněte pravým tlačítkem na **zálohovat** a pak v závislosti na vašem prostředí vyberte **VMware** nebo **Hyper-V**.

    ![Snímek obrazovky, který zobrazuje konzolu pro správu Veeam s možnostmi VMware a Hyper-V, které můžete vybrat.](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  V dialogovém okně **Nová úloha zálohovacího kopírování** zadejte název a popis úlohy.

    ![Snímek obrazovky, který zobrazuje konzolu pro správu Veeam, kde zadáváte název a popis úlohy.](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Vyberte virtuální počítače, které chcete zpracovat. Vyberte možnost ze zálohy a potom vyberte denní zálohu, kterou jste vytvořili dříve.

    ![Konzola pro správu Veeam, stránka úloh vytvoření nové záložní kopie](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  V případě potřeby vylučte z úlohy záložní kopírování objekty.

5.  Vyberte úložiště záloh a nastavte hodnotu pro **body obnovení, které mají být zachovány**. Nezapomeňte zaškrtnout políčko **ponechat následující body obnovení pro účely archivace** . Definujte četnost zálohování a pak vyberte **Upřesnit**.

    ![Snímek obrazovky, který ukazuje, kde můžete definovat četnost zálohování.](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Zadejte následující upřesňující nastavení:

    * Na kartě **Údržba** vypněte ochranu proti poškození úrovně úložiště.

    ![Snímek obrazovky zobrazující kartu Údržba v konzole pro správu Veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * Na kartě **úložiště** se ujistěte, že je odstranění duplicitních dat a komprese vypnuté.

    ![Veeam – Konzola pro správu, stránka Rozšířená nastavení úlohy záložní kopie](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Určete, že přenos dat je přímý.

8.  Podle vašich potřeb Definujte plán okna pro záložní kopírování a pak průvodce dokončete.

Další informace najdete v tématu [Vytvoření úloh záložní kopie](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>StorSimple cloudové snímky

StorSimple cloudové snímky chrání data, která se nachází ve vašem zařízení StorSimple. Vytvoření snímku v cloudu je ekvivalentní k přenosu pásek místních záloh do zařízení mimo pracoviště. Pokud používáte geograficky redundantní úložiště Azure, vytvoření snímku v cloudu je ekvivalentní k přenosu pásek zálohování na více lokalit. Pokud po havárii potřebujete zařízení obnovit, můžete jiné zařízení StorSimple převést do online režimu a provést převzetí služeb při selhání. Po převzetí služeb při selhání byste měli získat přístup k datům (s rychlostí cloudu) z posledního snímku cloudu.

Následující část popisuje, jak vytvořit krátký skript pro spuštění a odstranění snímků StorSimple cloudu během následného zpracování zálohy.

> [!NOTE]
> Ručně nebo programově vytvořené snímky nedodržují zásady vypršení platnosti snímku StorSimple. Tyto snímky je nutné ručně nebo programově odstranit.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Spuštění a odstranění cloudových snímků pomocí skriptu

> [!NOTE]
> Před odstraněním snímku StorSimple pečlivě vyhodnoťte dodržování předpisů a uchování dat. Další informace o spuštění následného zálohovacího skriptu najdete v dokumentaci k Veeam.


### <a name="backup-lifecycle"></a>Životní cyklus zálohování

![Diagram životního cyklu zálohování](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

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
4. Pokud chcete přidat skript do úlohy zálohování, upravte rozšířené možnosti úlohy Veeam.

    ![Veeam zálohování – karta Rozšířená nastavení skriptů](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Doporučujeme, abyste na konci každodenní úlohy zálohování spustili zásadu zálohování snímku StorSimple cloudu jako skript po zpracování. Další informace o tom, jak zálohovat a obnovit zálohovací prostředí aplikace, aby vám pomohly splnit požadavky RPO a RTO, najdete v rámci svého architekta zálohování.

## <a name="storsimple-as-a-restore-source"></a>StorSimple jako zdroj obnovení

Obnovení ze zařízení StorSimple funguje jako obnovení ze všech zařízení blokového úložiště. Obnovení dat, která jsou vrstvená do cloudu, probíhá při rychlosti cloudu. Pro místní data dojde k obnovení při rychlosti místního disku zařízení.

Díky Veeam získáte rychlé a podrobné obnovení na úrovni souborů prostřednictvím StorSimple prostřednictvím vestavěných zobrazení Průzkumníka v konzole Veeam. Pomocí Veeam Exploreru obnovte jednotlivé položky, jako jsou e-mailové zprávy, objekty služby Active Directory a položky SharePointu ze záloh. Obnovení je možné provést bez přerušení místního virtuálního počítače. Můžete také provést obnovení k bodu v čase pro databáze Azure SQL Database a Oracle. Veeam a StorSimple usnadňují proces obnovení na úrovni položek z Azure rychleji a snadno. Informace o tom, jak provést obnovení, najdete v dokumentaci k Veeam:

- Pro [Exchange Server](https://www.veeam.com/microsoft-exchange-recovery.html)
- Pro [službu Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html)
- Pro [SQL Server](https://www.veeam.com/microsoft-sql-server-explorer.html)
- Pro [SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- Pro [Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>StorSimple převzetí služeb při selhání a zotavení po havárii

> [!NOTE]
> U scénářů cíle zálohování se StorSimple Cloud Appliance nepodporuje jako cíl obnovení.

Havárie může být způsobeno nejrůznějšími faktory. V následující tabulce je uveden seznam běžných scénářů zotavení po havárii.

| Scenario | Dopad | Postup obnovení | Poznámky |
|---|---|---|---|
| Selhání zařízení StorSimple | Operace zálohování a obnovení jsou přerušeny. | Nahraďte neúspěšné zařízení a proveďte [převzetí služeb při selhání StorSimple a zotavení po havárii](./storsimple-8000-device-failover-disaster-recovery.md). | Pokud po obnovení zařízení potřebujete provést obnovení, všechny pracovní sady dat se načítají z cloudu do nového zařízení. Všechny operace jsou v cloudových rychlostech. Proces opakovaného prohledání indexu a katalogu může způsobit, že se všechny zálohovací sklady kontrolují a nastavují z vrstvy cloudu na úroveň místního zařízení, což může být časově náročný proces. |
| Selhání serveru Veeam | Operace zálohování a obnovení jsou přerušeny. | Znovu sestavte záložní server a proveďte obnovení databáze podle podrobných [informace v centru Veeam Help Center (technická dokumentace)](https://www.veeam.com/documentation-guides-datasheets.html).  | Server Veeam je nutné znovu sestavit nebo obnovit na webu pro zotavení po havárii. Obnovte databázi do nejnovějšího bodu. Pokud obnovená databáze Veeam není synchronizovaná s nejnovějšími úlohami zálohování, je nutné indexování a vytváření katalogu. Tento index a proces opětovného prohledání katalogu může způsobit, že se všechny zálohovací sklady prohledají a nastavují z vrstvy cloudu na úroveň místního zařízení. Díky tomu je tato operace časově náročná. |
| Selhání lokality, které vede ke ztrátě záložního serveru i StorSimple | Operace zálohování a obnovení jsou přerušeny. | Nejprve obnovte StorSimple a pak obnovte Veeam. | Nejprve obnovte StorSimple a pak obnovte Veeam. Pokud po obnovení zařízení potřebujete provést obnovení, všechny pracovní sady dat se z cloudu načtou do nového zařízení. Všechny operace jsou v cloudových rychlostech. |


## <a name="references"></a>Reference

Následující dokumenty byly odkazovány na tento článek:

- [StorSimple instalaci funkce Multipath I/O](./storsimple-8000-configure-mpio-windows-server.md)
- [Scénáře úložiště: dynamické zajišťování](/windows-hardware/drivers/storage/thin-provisioning)
- [Použití jednotek GPT](/previous-versions/windows/hardware/design/dn653580(v=vs.85)#EHD)
- [Nastavení stínových kopií pro sdílené složky](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771893(v=ws.11))

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o tom, jak [obnovit ze zálohovacího skladu](./storsimple-8000-restore-from-backup-set-u2.md).
- Další informace o tom, jak provést [převzetí služeb při selhání a zotavení po havárii zařízení](./storsimple-8000-device-failover-disaster-recovery.md).