---
title: StorSimple 8000 série jako cíl zálohování s NetBackup | Dokumenty společnosti Microsoft
description: Popisuje konfiguraci cíle zálohování StorSimple pomocí programu Veritas NetBackup.
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
ms.date: 06/15/2017
ms.author: matd
ms.openlocfilehash: 957fff73f2406e0e057a7c978dd76a6bd9c156b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67876212"
---
# <a name="storsimple-as-a-backup-target-with-netbackup"></a>StorSimple jako cíl zálohování s NetBackup

## <a name="overview"></a>Přehled

Azure StorSimple je řešení hybridního cloudového úložiště od Microsoftu. StorSimple řeší složitosti exponenciálního růstu dat pomocí účtu úložiště Azure jako rozšíření místního řešení a automatického vrstvení dat napříč místním úložištěm a cloudovým úložištěm.

V tomto článku budeme diskutovat O integraci StorSimple s Veritas NetBackup a osvědčené postupy pro integraci obou řešení. Také jsme doporučení, jak nastavit Veritas NetBackup pro nejlepší integraci s StorSimple. Doporučujeme osvědčeným postupům společnosti Veritas, architektům zálohování a správcům, abychom měli nejlepší způsob, jak nastavit aplikaci Veritas NetBackup tak, aby splňovala individuální požadavky na zálohování a smlouvy o úrovni služeb (SLA).

I když ilustrujeme kroky konfigurace a klíčové koncepty, tento článek není v žádném případě podrobným průvodcem konfigurací nebo instalací. Předpokládáme, že základní komponenty a infrastruktura jsou v provozuschopném stavu a jsou připraveny podporovat koncepty, které popisujeme.

### <a name="who-should-read-this"></a>Kdo by to měl číst?

Informace v tomto článku budou velmi užitečné pro správce záloh, správce úložišť a architekty úložišť, kteří mají znalosti úložiště, Windows Server 2012 R2, Ethernet, cloudové služby a Veritas NetBackup.

### <a name="supported-versions"></a>Podporované verze

-   NetBackup 7.7.x a novější verze
-   [StorSimple Update 3 a novější verze](storsimple-overview.md#storsimple-workload-summary)


## <a name="why-storsimple-as-a-backup-target"></a>Proč StorSimple jako cíl zálohování?

StorSimple je dobrou volbou pro cíl zálohování, protože:

-   Poskytuje standardní místní úložiště pro zálohovací aplikace, které lze použít jako cíl rychlého zálohování bez jakýchkoli změn. Můžete také použít StorSimple pro rychlé obnovení posledních záloh.
-   Jeho vrstvení cloudu je bezproblémově integrované s účtem cloudového úložiště Azure, aby bylo možné využívat cenově výhodné úložiště Azure.
-   Automaticky poskytuje úložiště mimo lokalitu pro zotavení po havárii.

## <a name="key-concepts"></a>Klíčové koncepty

Stejně jako u všech řešení úložiště je pro úspěch rozhodující pečlivé posouzení výkonu úložiště, sla, rychlosti změn a potřeb růstu kapacity. Hlavní myšlenkou je, že zavedením cloudové vrstvy hrají vaše časy přístupu a propustnosti do cloudu zásadní roli ve schopnosti StorSimple dělat svou práci.

StorSimple je navržen tak, aby poskytoval úložiště aplikacím, které pracují s dobře definovanou pracovní sadou dat (hot data). V tomto modelu je pracovní sada dat uložena na místních úrovních a zbývající nepracovní/studená/archivovaná sada dat je vrstvena do cloudu. Tento model je znázorněn na následujícím obrázku. Téměř plochá zelená čára představuje data uložená na místních úrovních zařízení StorSimple. Červená čára představuje celkové množství dat uložených v řešení StorSimple napříč všemi vrstvami. Mezera mezi plochou zelenou čárou a exponenciální červenou křivkou představuje celkové množství dat uložených v cloudu.

**StorSimple stupňovitý**
![StorSimple vrstvení diagram](./media/storsimple-configure-backup-target-using-netbackup/image1.jpg)

S ohledem na tuto architekturu zjistíte, že StorSimple je ideální pro funkci jako cíl zálohování. StorSimple můžete použít k:
-   Proveďte nejčastější obnovení z místní pracovní sady dat.
-   Cloud použijte pro zotavení po havárii mimo lokalitu a starší data, kde jsou obnovení méně častá.

## <a name="storsimple-benefits"></a>StorSimple výhody

StorSimple poskytuje místní řešení, které je bezproblémově integrované s Microsoft Azure, a to díky využití bezproblémového přístupu k místnímu a cloudovému úložišti.

StorSimple používá automatické vrstvení mezi místním zařízením, které má zařízení SSD (SSD) a sériově připojené úložiště SCSI (SAS) a Azure Storage. Automatické vrstvení udržuje často přistupovat k datům místní, na úrovních SSD a SAS. Přesouvá zřídka přístupná data do Služby Azure Storage.

StorSimple nabízí tyto výhody:

-   Jedinečné algoritmy odstranění duplicit a komprese, které používají cloud k dosažení bezprecedentních úrovní odstranění duplicit
-   Vysoká dostupnost
-   Geografická replikace pomocí geografické replikace Azure
-   Integrace Azure
-   Šifrování dat v cloudu
-   Lepší zotavení po havárii a dodržování předpisů

Přestože StorSimple představuje dva hlavní scénáře nasazení (primární cíl zálohování a sekundární cíl zálohování), v podstatě je to prosté, blokové úložné zařízení. StorSimple provádí všechny komprese a deduplikace. Hladce odesílá a načítá data mezi cloudem a aplikací a souborovým systémem.

Další informace o StorSimple naleznete v [tématu StorSimple 8000 series: Hybridní řešení cloudového úložiště](storsimple-overview.md). Také si můžete prohlédnout [technické specifikace řady StorSimple 8000](storsimple-technical-specifications-and-compliance.md).

> [!IMPORTANT]
> Použití zařízení StorSimple jako cíle zálohování je podporováno pouze pro aktualizaci StorSimple 8000 Update 3 a novější verze.

## <a name="architecture-overview"></a>Přehled architektury

V následujících tabulkách jsou uvedeny počáteční pokyny modelu zařízení k architektuře.

**StorJednoduché kapacity pro místní a cloudové úložiště**

| Kapacita úložiště       | 8100          | 8600            |
|------------------------|---------------|-----------------|
| Kapacita místního úložiště | &lt;10 TiB\*  | &lt;20 TiB\*  |
| Kapacita cloudového úložiště | &gt;200 TiB\* | &gt;500 TiB\* |

\*Velikost úložiště předpokládá žádné odstranění duplicit nebo komprese.

**StorJednoduché kapacity pro primární a sekundární zálohování**

| Scénář zálohování  | Kapacita místního úložiště  | Kapacita cloudového úložiště  |
|---|---|---|
| Primární zálohování  | Nedávné zálohy uložené v místním úložišti pro rychlé obnovení, aby bylo splněno cíl bodu obnovení (RPO) | Historie zálohování (RPO) se vejde do kapacity cloudu |
| Sekundární zálohování | Sekundární kopii záložních dat lze uložit v kapacitě cloudu  | Není dostupné.  |

## <a name="storsimple-as-a-primary-backup-target"></a>StorSimple jako primární cíl zálohování

V tomto scénáři StorSimple svazky jsou prezentovány do zálohovací aplikace jako jediné úložiště pro zálohování. Následující obrázek znázorňuje architekturu řešení, ve které všechny zálohy používají vrstvené svazky StorSimple pro zálohování a obnovení.

![StorSimple jako primární cílový diagram zálohování](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Logické kroky primární cílové zálohy

1.  Záložní server kontaktuje cílového agenta zálohování a záložní agent přenáší data na záložní server.
2.  Záložní server zapisuje data do svazků storsimple vrstvených.
3.  Záložní server aktualizuje databázi katalogu a potom dokončí úlohu zálohování.
4.  Skript snímku aktivuje správce snímků StorSimple (spuštění nebo odstranění).
5.  Záložní server odstraní zálohy, jejichž platnost vypršela na základě zásad uchovávání informací.

### <a name="primary-target-restore-logical-steps"></a>Logické kroky obnovení primárního cíle

1.  Záložní server spustí obnovení příslušných dat z úložiště úložiště.
2.  Záložní agent obdrží data ze záložního serveru.
3.  Zálohovací server dokončí úlohu obnovení.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple jako sekundární cíl zálohování

V tomto scénáři StorSimple svazky primárně se používají pro dlouhodobé uchovávání nebo archivaci.

Následující obrázek znázorňuje architekturu, ve které počáteční zálohy a obnovení cílí na vysoce výkonný svazek. Tyto zálohy jsou zkopírovány a archivovány na vrstvený svazek StorSimple podle nastaveného plánu.

Je důležité zvětšit objem s vysokým výkonem, aby mohl zpracovat vaše požadavky na kapacitu a výkon zásad uchovávání informací.

![StorSimple jako sekundární záložní cílový diagram](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Logické kroky sekundární hotřské zálohy

1.  Záložní server kontaktuje cílového agenta zálohování a záložní agent přenáší data na záložní server.
2.  Záložní server zapisuje data do vysoce výkonného úložiště.
3.  Záložní server aktualizuje databázi katalogu a potom dokončí úlohu zálohování.
4.  Záložní server zkopíruje zálohy na StorSimple na základě zásad uchovávání informací.
5.  Skript snímku aktivuje správce snímků StorSimple (spuštění nebo odstranění).
6.  Záložní server odstraní zálohy, jejichž platnost vypršela, na základě zásad uchovávání informací.

### <a name="secondary-target-restore-logical-steps"></a>Logické kroky obnovení sekundárního cíle

1.  Záložní server spustí obnovení příslušných dat z úložiště úložiště.
2.  Záložní agent obdrží data ze záložního serveru.
3.  Zálohovací server dokončí úlohu obnovení.

## <a name="deploy-the-solution"></a>Nasazení řešení

Nasazení tohoto řešení vyžaduje tři kroky:
1. Připravte síťovou infrastrukturu.
2. Nasaďte zařízení StorSimple jako cíl zálohování.
3. Nasazení aplikace Veritas NetBackup.

Každý krok je podrobně popsán v následujících částech.

### <a name="set-up-the-network"></a>Nastavit síť

Vzhledem k tomu, že StorSimple je řešení, které je integrované s cloudem Azure, StorSimple vyžaduje aktivní a pracovní připojení ke cloudu Azure. Toto připojení se používá pro operace, jako jsou cloudové snímky, správa dat a přenos metadat, a na úroveň starších, méně přístupných dat do cloudového úložiště Azure.

Aby řešení fungovalo optimálně, doporučujeme dodržovat tyto doporučené postupy pro vytváření sítí:

-   Propojení, které spojuje vrstvení StorSimple s Azure musí splňovat vaše požadavky na šířku pásma. Chcete-li toho dosáhnout, použijte správnou úroveň kvality služby (QoS) na přepínače infrastruktury tak, aby odpovídaly vašim službám SLA cíle RPO a času obnovení (RTO).

-   Maximální latence přístupu k úložišti azure blob by měla být kolem 80 ms.

### <a name="deploy-storsimple"></a>Nasazení StorSimple

Podrobné pokyny pro nasazení StorSimple najdete v tématu [Nasazení místního zařízení StorSimple](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-netbackup"></a>Nasazení programu NetBackup

Podrobné pokyny k nasazení programu NetBackup 7.7.x naleznete v dokumentaci k [programu NetBackup 7.7.x](http://www.veritas.com/docs/000094423).

## <a name="set-up-the-solution"></a>Nastavení řešení

V této části předvedeme některé příklady konfigurace. Následující příklady a doporučení ilustrují nejzákladnější a nejzákladnější implementaci. Tato implementace nemusí platit přímo pro vaše konkrétní požadavky na zálohování.

### <a name="set-up-storsimple"></a>Nastavit StorSimple

| Úlohy nasazení StorSimple  | Další komentáře |
|---|---|
| Nasaďte místní zařízení StorSimple. | Podporované verze: Aktualizace 3 a novější verze. |
| Zapněte cíl zálohování. | Pomocí těchto příkazů můžete zapnout nebo vypnout režim cíle zálohování a získat stav. Další informace naleznete v [tématu Vzdálené připojení k zařízení StorSimple](storsimple-remote-connect.md).</br> Zapnutí režimu zálohování: `Set-HCSBackupApplianceMode -enable`. </br> Vypnutí režimu zálohování: `Set-HCSBackupApplianceMode -disable`. </br> Chcete-li získat aktuální stav `Get-HCSBackupApplianceMode`nastavení režimu zálohování: . |
| Vytvořte společný kontejner svazku pro svazek, který ukládá záložní data. Všechna data v kontejneru svazku jsou deduplikována. | Kontejnery svazků StorSimple definují domény deduplikace.  |
| Vytvořte svazky StorSimple. | Vytvořte svazky s velikostmi co nejblíže očekávanému využití, protože velikost svazku ovlivňuje dobu trvání snímku cloudu. Informace o velikosti svazku naleznete v [zásadách uchovávání informací](#retention-policies).</br> </br> Použijte vrstvené svazky StorSimple a zaškrtněte políčko **Použít tento svazek pro méně často přistupující archivní data.** </br> Použití pouze místně vázaných svazků není podporováno. |
| Vytvořte jedinečnou zásadu zálohování StorSimple pro všechny cílové svazky zálohování. | Zásada zálohování StorSimple definuje skupinu konzistence svazku. |
| Zakažte plán jako snímky vyprší. | Snímky jsou spuštěny jako operace následnézpracování. |

### <a name="set-up-the-host-backup-server-storage"></a>Nastavení úložiště hostitelského záložního serveru

Nastavte úložiště hostitelského záložního serveru podle těchto pokynů:  

- Nepoužívejte rozložené svazky (vytvořené programem Windows Disk Management); rozložené svazky nejsou podporovány.
- Naformátujte svazky pomocí systému souborů NTFS s velikostí přidělení 64 KB.
- Namapujte svazky StorSimple přímo na server NetBackup.
    - Použijte iSCSI pro fyzické servery.
    - Pro virtuální servery používejte předávací disky.


## <a name="best-practices-for-storsimple-and-netbackup"></a>Doporučené postupy pro StorSimple a NetBackup

Nastavte řešení podle pokynů v následujících několika částech.

### <a name="operating-system-best-practices"></a>Osvědčené postupy operačního systému

- Zakažte šifrování a odstranění duplicit systému Souborů Systému souborů Windows Server.
- Zakažte defragmentaci systému Windows Server na svazcích StorSimple.
- Zakažte indexování systému Windows Server na svazcích StorSimple.
- Spusťte antivirovou kontrolu u zdrojového hostitele (nikoli proti svazkům StorSimple).
- Vypněte výchozí [údržbu systému Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) ve Správci úloh. Udělejte to jedním z následujících způsobů:
  - Vypněte konfigurátor Údržba v Plánovači úloh systému Windows.
  - Stáhnout [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) z Windows Sysinternals. Po stažení programu PsExec spusťte prostředí Windows PowerShell jako správce a zadejte:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>StorSimple osvědčené postupy

-   Ujistěte se, že zařízení StorSimple je aktualizováno na [aktualizaci 3 nebo novější](storsimple-install-update-3.md).
-   Izolujte iSCSI a cloudový provoz. Pro přenosmezi StorSimple a záložním serverem použijte vyhrazená připojení iSCSI.
-   Ujistěte se, že vaše zařízení StorSimple je vyhrazený cíl zálohování. Smíšené úlohy nejsou podporovány, protože ovlivňují vaše RTO a RPO.

### <a name="netbackup-best-practices"></a>Osvědčené postupy programu NetBackup

-   Databáze NetBackup by měla být místní na serveru a neměla by být umístěna na svazku StorSimple.
-   Obnovení po havárii zálohujte databázi netbackup na svazku StorSimple.
-   Podporujeme NetBackup úplné a přírůstkové zálohy (označované také jako rozdílové přírůstkové zálohy v NetBackup) pro toto řešení. Doporučujeme nepoužívat syntetické a kumulativní přírůstkové zálohy.
-   Záložní datové soubory by měly obsahovat pouze data pro určitou úlohu. Například nejsou povoleny žádné připojení médií mezi různými úlohami.

Nejnovější nastavení programu NetBackup a doporučené postupy pro implementaci těchto požadavků naleznete v dokumentaci k programu NetBackup na [adrese www.veritas.com](https://www.veritas.com).


## <a name="retention-policies"></a>Zásady uchovávání informací

Jedním z nejběžnějších typů zásad uchovávání záloh je zásada Dědeček, Otec a Syn (GFS). V zásadách GFS se přírůstkové zálohování provádí denně a úplné zálohy se provádějí týdně a měsíčně. Výsledkem této zásady je šest vrstvených svazků StorSimple: jeden svazek obsahuje týdenní, měsíční a roční úplné zálohy. zbylých pět svazků ukládá denní přírůstkové zálohy.

V následujícím příkladu používáme rotaci GFS. Příklad předpokládá následující:

-   Používají se nedeedovaná nebo komprimovaná data.
-   Úplné zálohy jsou 1 TiB každý.
-   Denní přírůstkové zálohy jsou 500 GiB každý.
-   Čtyři týdenní zálohy jsou uchovávány po dobu jednoho měsíce.
-   Dvanáct měsíčnízálohy jsou uchovávány po dobu jednoho roku.
-   Jedna roční záloha je uchovávána po dobu 10 let.

Na základě předchozích předpokladů vytvořte 26-TiB StorSimple vrstvený svazek pro měsíční a roční úplné zálohy. Vytvořte 5-TiB StorSimple vrstvený svazek pro každou přírůstkovou denní zálohy.

| Uchovávání typu zálohy | Velikost (TiB) | Multiplikátor GFS\* | Celková kapacita (TiB)  |
|---|---|---|---|
| Týdenní plný | 1 | 4  | 4 |
| Denní přírůstkové | 0,5 | 20 (cykly stejný počet týdnů za měsíc) | 12 (2 pro dodatečnou kvótu) |
| Měsíčně plný | 1 | 12 | 12 |
| Ročně plné | 1  | 10 | 10 |
| Požadavek GFS |   | 38 |   |
| Dodatečná kvóta  | 4  |   | 42 celkový požadavek gfs  |

\*Multiplikátor GFS je počet kopií, které potřebujete chránit a uchovávat, aby splňovaly vaše požadavky na zásady zálohování.

## <a name="set-up-netbackup-storage"></a>Nastavení úložiště NetBackup

### <a name="to-set-up-netbackup-storage"></a>Nastavení úložiště NetBackup

1.  V konzole pro správu programu NetBackup vyberte**Devices** > **fondy disků** **media a zařízení pro správu.** >  V Průvodci konfigurací diskového fondu vyberte typ serveru úložiště **AdvancedDisk**a pak vyberte **další**.

    ![Konzola pro správu netbackup, Průvodce konfigurací fondu disků](./media/storsimple-configure-backup-target-using-netbackup/nbimage1.png)

2.  Vyberte server a pak vyberte **Další**.

    ![Konzola pro správu netzálohování, vyberte server](./media/storsimple-configure-backup-target-using-netbackup/nbimage2.png)

3.  Vyberte hlasitost StorSimple.

    ![Konzola pro správu netbackup, vyberte disk svazku StorSimple](./media/storsimple-configure-backup-target-using-netbackup/nbimage3.png)

4.  Zadejte název cíle zálohování a průvodce dokončete výběrem možnosti **Další** > **další.**

5.  Zkontrolujte nastavení a pak vyberte **Dokončit**.

6.  Na konci každého přiřazení svazku změňte nastavení zařízení úložiště tak, aby odpovídalo nastavení doporučenému v [části Doporučené postupy pro aplikace StorSimple a NetBackup](#best-practices-for-storsimple-and-netbackup).

7. Opakujte kroky 1-6, dokud nedokončíte přiřazení svazků StorSimple.

    ![Konzola pro správu netbackup, konfigurace disku](./media/storsimple-configure-backup-target-using-netbackup/nbimage5.png)

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Nastavení StorSimple jako primárního cíle zálohování

> [!NOTE]
> Obnovení dat ze zálohy, která byla vrstvena do cloudu dochází rychlostí cloudu.

Následující obrázek znázorňuje mapování typického svazku na záložní úlohu. V tomto případě všechny týdenní zálohy mapovat na celý disk v sobotu a přírůstkové zálohy mapovat na pondělí až pátek přírůstkové disky. Všechny zálohy a obnovení jsou z vrstveného svazku StorSimple.

![Logický diagram konfigurace primárního cíle zálohy](./media/storsimple-configure-backup-target-using-netbackup/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Příklad plánu GFS jako primární hod ovacího systému zálohování

Zde je příklad plánu rotace GFS na čtyři týdny, měsíčně a ročně:

| Typ frekvence/zálohování | Do bloku | Přírůstkové (dny 1-5)  |   
|---|---|---|
| Týdně (týdny 1-4) | Sobota | Pondělí až pátek |
| měsíčně  | Sobota  |   |
| Roční | Sobota  |   |

## <a name="assigning-storsimple-volumes-to-a-netbackup-backup-job"></a>Přiřazení svazků StorSimple k úloze zálohování netbackup

Následující pořadí předpokládá, že netbackup a cílový hostitel jsou konfigurovány v souladu s pokyny agenta NetBackup.

### <a name="to-assign-storsimple-volumes-to-a-netbackup-backup-job"></a>Přiřazení svazků StorSimple k úloze zálohování programu NetBackup

1. V konzoli pro správu programu NetBackup vyberte **položku NetBackup Management**, klepněte pravým tlačítkem myši na **položku Zásady**a potom vyberte **příkaz Nová zásada**.

   ![Konzola pro správu netbackup, vytvoření nové zásady](./media/storsimple-configure-backup-target-using-netbackup/nbimage6.png)

2. V **dialogovém** okně Přidat novou zásadu zadejte název zásady a zaškrtněte políčko **Použít Průvodce konfigurací zásad.** Vyberte **OK**.

   ![Konzola pro správu netzálohování, dialogové okno Přidat novou zásadu](./media/storsimple-configure-backup-target-using-netbackup/nbimage7.png)

3. V Průvodci konfigurací zásad zálohování zvolte požadovaný typ zálohy a pak vyberte **další**.

   ![Konzola pro správu netbackup, výběr typu zálohování](./media/storsimple-configure-backup-target-using-netbackup/nbimage8.png)

4. Chcete-li nastavit typ zásady, vyberte **možnost Standardní**a pak vyberte **Další**.

   ![Konzola pro správu netbackup, výběr typu zásad](./media/storsimple-configure-backup-target-using-netbackup/nbimage9.png)

5. Vyberte hostitele, zaškrtněte políčko **Rozpoznat operační systém klienta** a pak vyberte **Přidat**. Vyberte **další**.

   ![Konzola pro správu netbackup, seznam klientů v nové zásadě](./media/storsimple-configure-backup-target-using-netbackup/nbimage10.png)

6. Vyberte jednotky, které chcete zálohovat.

   ![Konzola pro správu netbackup, výběry záloh pro novou zásadu](./media/storsimple-configure-backup-target-using-netbackup/nbimage11.png)

7. Vyberte hodnoty frekvence a uchovávání, které splňují požadavky na střídání záloh.

   ![Konzola pro správu netbackup, frekvence zálohování a rotace pro novou zásadu](./media/storsimple-configure-backup-target-using-netbackup/nbimage12.png)

8. Vyberte **další** > **další** > **dokončení**.  Po vytvoření zásady můžete upravit plán.

9. Výběrem této možnosti rozbalte zásadu, kterou jste právě vytvořili, a pak vyberte **možnost Plány**.

   ![NetBackup Administrační konzole, plány pro novou zásadu](./media/storsimple-configure-backup-target-using-netbackup/nbimage13.png)

10. Klepněte pravým tlačítkem myši na **položku Differential-Inc**, vyberte **možnost Kopírovat do nové**a pak vyberte **ok**.

    ![Konzola pro správu netbackup, kopírování plánu do nové zásady](./media/storsimple-configure-backup-target-using-netbackup/nbimage14.png)

11. Klikněte pravým tlačítkem myši na nově vytvořený plán a potom vyberte **změnit**.

12. Na kartě **Atributy** zaškrtněte políčko **Přepsat výběr úložiště zásad** a pak vyberte svazek, na kterém přejít přírůstkové zálohy v pondělí.

    ![Konzola pro správu netbackup, plán změn](./media/storsimple-configure-backup-target-using-netbackup/nbimage15.png)

13. Na kartě **Počáteční okno** vyberte časové okno pro zálohy.

    ![Konzola pro správu netbackup, změna úvodního okna](./media/storsimple-configure-backup-target-using-netbackup/nbimage16.png)

14. Vyberte **OK**.

15. Opakujte kroky 10-14 pro každou přírůstkovou zálohu. Vyberte příslušný svazek a plán pro každou zálohu, kterou vytvoříte.

16. Klikněte pravým tlačítkem myši na plán **Differential-Inc** a potom jej odstraňte.

17. Upravte celý plán tak, aby vyhovoval vašim potřebám zálohování.

    ![Konzola pro správu netbackup, změna úplného plánu](./media/storsimple-configure-backup-target-using-netbackup/nbimage17.png)

18. Změňte počáteční okno.

    ![Konzola pro správu netzálohování, změna počátečního okna](./media/storsimple-configure-backup-target-using-netbackup/nbimage18.png)

19. Konečný plán vypadá takto:

    ![Konzola pro správu netbackup, konečný plán](./media/storsimple-configure-backup-target-using-netbackup/nbimage19.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Nastavení StorSimple jako sekundárního cíle zálohování

> [!NOTE]
>Obnovení dat ze zálohy, která byla vrstvena do cloudu dochází rychlostí cloudu.

V tomto modelu musíte mít paměťové médium (jiné než StorSimple) sloužit jako dočasné mezipaměti. Můžete například použít redundantní pole svazku nezávislých disků (RAID) pro umístění místa, vstupu/výstupu (I/O) a šířky pásma. Doporučujeme používat RAID 5, 50 a 10.

Následující obrázek znázorňuje typické místní svazky krátkodobého uchovávání (na server) a svazky archivů dlouhodobého uchovávání. V tomto scénáři všechny zálohy spustit na místním svazku (na server) RAID. Tyto zálohy jsou pravidelně duplikovány a archivovány do svazku archivů. Je důležité velikost místního (na server) raid svazek tak, aby mohl zvládnout krátkodobé retenční kapacity a požadavky na výkon.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple jako sekundární záložní cíl GFS příklad

![StorSimple jako sekundární záložní cílový diagram](./media/storsimple-configure-backup-target-using-netbackup/secondarybackuptargetdiagram.png)

Následující tabulka ukazuje, jak nastavit zálohy pro spuštění na místních a StorSimple disky. Zahrnuje individuální a celkové požadavky na kapacitu.

### <a name="backup-configuration-and-capacity-requirements"></a>Konfigurace zálohování a požadavky na kapacitu

| Typ zálohy a uchovávání informací | Nakonfigurované úložiště | Velikost (TiB) | Multiplikátor GFS | Celková\* kapacita (TiB) |
|---|---|---|---|---|
| Týden 1 (plný a přírůstkový) |Místní disk (krátkodobý)| 1 | 1 | 1 |
| StorJednoduché týdny 2-4 |StorSimple disk (dlouhodobý) | 1 | 4 | 4 |
| Měsíčně plný |StorSimple disk (dlouhodobý) | 1 | 12 | 12 |
| Ročně plné |StorSimple disk (dlouhodobý) | 1 | 1 | 1 |
|Požadavek na velikost objemů GFS |  |  |  | 18*|

\*Celková kapacita zahrnuje 17 TiB disků StorSimple a 1 TiB místního svazku RAID.


### <a name="gfs-example-schedule-gfs-rotation-weekly-monthly-and-yearly-schedule"></a>Příklad plánu GFS: Týdenní, měsíční a roční rozvrh GFS

| Týden | Do bloku | Přírůstkový den 1 | Přírůstkový den 2 | Přírůstkový den 3 | Přírůstkový den 4 | Přírůstkový den 5 |
|---|---|---|---|---|---|---|
| Týden 1 | Místní svazek RAID  | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID |
| Týden 2 | StorJednoduché týdny 2-4 |   |   |   |   |   |
| Týden 3 | StorJednoduché týdny 2-4 |   |   |   |   |   |
| Týden 4 | StorJednoduché týdny 2-4 |   |   |   |   |   |
| měsíčně | StorJednoduché měsíčně |   |   |   |   |   |
| Roční | StorSimple ročně  |   |   |   |   |   |


## <a name="assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Přiřazení svazků StorSimple k úloze archivu a duplikace aplikace NetBackup

Vzhledem k tomu, že program NetBackup nabízí širokou škálu možností pro správu úložišť a médií, doporučujeme konzultovat se společností Veritas nebo architektem programu NetBackup, abyste správně posoudili požadavky na zásady životního cyklu úložiště (SLP).

Po definování počátečních fondů disků je třeba definovat tři další zásady životního cyklu úložiště, celkem pro čtyři zásady:
* Místní RAIDVolume
* StorSimpleTýden2-4
* StorSimpleMěsíční syty
* StorSimpleYearlyFulls

### <a name="to-assign-storsimple-volumes-to-a-netbackup-archive-and-duplication-job"></a>Přiřazení svazků StorSimple k úloze archivu programu NetBackup a duplikace

1. V konzole pro správu netzálohování vyberte**zásady** > životního cyklu **úložiště** > **Nové zásady životního cyklu úložiště**.

   ![Konzola pro správu netbackup, nové zásady životního cyklu úložiště](./media/storsimple-configure-backup-target-using-netbackup/nbimage20.png)

2. Zadejte název snímku a pak vyberte **Přidat**.

3. V dialogovém okně **Nová operace** na kartě **Vlastnosti** v části **Operace**vyberte **možnost Zálohovat**. Vyberte požadované hodnoty pro **cílové úložiště**, **typ uchování**a **dobu uchovávání**. Vyberte **OK**.

   ![Konzola pro správu netbackup, dialogové okno Nová operace](./media/storsimple-configure-backup-target-using-netbackup/nbimage22.png)

   Definuje první operaci zálohování a úložiště.

4. Výběrem zvýrazněte předchozí operaci a pak vyberte **Přidat**. V dialogovém okně **Změnit operaci úložiště** vyberte hodnoty, které chcete pro cílové **úložiště**, **typ uchování**a **dobu uchování**.

   ![Konzola pro správu netzálohování,Dialogové okno Změnit provoz úložiště](./media/storsimple-configure-backup-target-using-netbackup/nbimage23.png)

5. Výběrem zvýrazněte předchozí operaci a pak vyberte **Přidat**. V dialogovém okně **Zásady životního cyklu nového úložiště** přidejte měsíční zálohy na jeden rok.

   ![Konzola pro správu netbackup, dialogové okno Zásady životního cyklu nového úložiště](./media/storsimple-configure-backup-target-using-netbackup/nbimage24.png)

6. Opakujte kroky 4-5, dokud nevytvoříte komplexní zásady uchovávání informací SLP, které potřebujete.

   ![Konzola pro správu netzálohování, přidání zásad v dialogovém okně Zásady životního cyklu nového úložiště](./media/storsimple-configure-backup-target-using-netbackup/nbimage25.png)

7. Po dokončení definování zásad uchovávání informací slp definujte v části **Zásady**zásady zálohování podle kroků popsaných v [části Přiřazení svazků StorSimple k úloze zálohování programu NetBackup](#assigning-storsimple-volumes-to-a-netbackup-backup-job).

8. V části **Plány**klikněte v dialogovém okně **Změnit plán** pravým tlačítkem myši na **příkaz Úplné**a potom vyberte **změnit**.

   ![Konzola pro správu netzálohování, dialogové okno Změnit plán](./media/storsimple-configure-backup-target-using-netbackup/nbimage26.png)

9. Zaškrtněte políčko **Přepsat výběr úložiště zásad** a pak vyberte zásady uchovávání informací SLP, které jste vytvořili v krocích 1-6.

   ![Konzola pro správu netbackup, volba přepsání úložiště zásad](./media/storsimple-configure-backup-target-using-netbackup/nbimage27.png)

10. Vyberte **OK**a opakujte pro plán přírůstkového zálohování.

    ![Konzola pro správu netzálohování, dialogové okno Změnit plán pro přírůstkové zálohy](./media/storsimple-configure-backup-target-using-netbackup/nbimage28.png)


| Uchovávání typu zálohy | Velikost (TiB) | Multiplikátor GFS\* | Celková kapacita (TiB)  |
|---|---|---|---|
| Týdenní plný |  1  |  4 | 4  |
| Denní přírůstkové  | 0,5  | 20 (cykly se rovnají počtu týdnů za měsíc) | 12 (2 pro dodatečnou kvótu) |
| Měsíčně plný  | 1 | 12 | 12 |
| Ročně plné | 1  | 10 | 10 |
| Požadavek GFS  |     |     | 38 |
| Dodatečná kvóta  | 4  |    | 42 celkový požadavek gfs |

\*Multiplikátor GFS je počet kopií, které potřebujete chránit a uchovávat, aby splňovaly vaše požadavky na zásady zálohování.

## <a name="storsimple-cloud-snapshots"></a>Snímky cloudu StorSimple

Snímky cloudu StorSimple chrání data, která se nacházejí ve vašem zařízení StorSimple. Vytvoření cloudového snímku je ekvivalentní k odesílání místních záložních pásek do zařízení mimo lokalitu. Pokud používáte geograficky redundantní úložiště Azure, vytvoření cloudového snímku se rovná odesílání záložních pásek do více lokalit. Pokud potřebujete obnovit zařízení po havárii, můžete převést další zařízení StorSimple online a provést převzetí služeb při selhání. Po převzetí služeb při selhání budete mít přístup k datům (rychlostí cloudu) z nejnovějšího snímku cloudu.

Následující část popisuje, jak vytvořit krátký skript pro spuštění a odstranění snímků cloudu StorSimple během zálohování po zpracování.

> [!NOTE]
> Snímky, které jsou ručně nebo programově vytvořené nedodržují zásady vypršení platnosti snímku StorSimple. Tyto snímky musí být ručně nebo programově odstraněny.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Spuštění a odstranění snímků cloudu pomocí skriptu

> [!NOTE]
> Pečlivě vyhodnoťte požadavky na dodržování předpisů a uchovávání dat před odstraněním snímku StorSimple. Další informace o spuštění skriptu po zálohování naleznete v [dokumentaci k programu NetBackup](http://www.veritas.com/docs/000094423).

### <a name="backup-lifecycle"></a>Životní cyklus zálohování

![Diagram životního cyklu zálohování](./media/storsimple-configure-backup-target-using-netbackup/backuplifecycle.png)

### <a name="requirements"></a>Požadavky

-   Server, který spouští skript, musí mít přístup ke cloudovým prostředkům Azure.
-   Uživatelský účet musí mít potřebná oprávnění.
-   Zásady zálohování StorSimple s přidruženými svazky StorSimple musí být nastaveny, ale nejsou zapnuty.
-   Budete potřebovat název prostředku StorSimple, registrační klíč, název zařízení a ID zásad zálohování.

### <a name="to-start-or-delete-a-cloud-snapshot"></a>Spuštění nebo odstranění snímku cloudu

1. [Nainstalujte Azure PowerShell](/powershell/azure/overview).
2. Stažení a nastavení [skriptu PowerShell u služby Manage-CloudSnapshots.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Manage-CloudSnapshots.ps1)
3. Na serveru, na který je skript spuštěn, spusťte PowerShell jako správce. Ujistěte se, že `-WhatIf $true` spustíte skript s chcete-li zjistit, jaké změny skript provede. Po dokončení ověření předavte . `-WhatIf $false` Spusťte níže uvedený příkaz:
   ```powershell
   .\Manage-CloudSnapshots.ps1 -SubscriptionId [Subscription Id] -TenantId [Tenant ID] -ResourceGroupName [Resource Group Name] -ManagerName [StorSimple Device Manager Name] -DeviceName [device name] -BackupPolicyName [backup policyname] -RetentionInDays [Retention days] -WhatIf [$true or $false]
   ```
4. Přidejte skript do zálohovací úlohy v programu NetBackup. Chcete-li to provést, upravte příkazy předběžného zpracování a následného zpracování možností úlohy netbackup.

> [!NOTE]
> Doporučujeme spustit zásady zálohování cloudových snímků StorSimple jako skript po zpracování na konci denní úlohy zálohování. Další informace o zálohování a obnovení prostředí zálohovací aplikace, které vám pomohou splnit váš RPO a RTO, obraťte se na svého architekta zálohování.

## <a name="storsimple-as-a-restore-source"></a>StorSimple jako zdroj obnovení

Obnoví z zařízení StorSimple pracovat jako obnovení z libovolného bloku paměťového zařízení. Obnovení dat, která je vrstvené do cloudu dochází při rychlostech cloudu. U místních dat dochází k obnovení při místní rychlosti disku zařízení. Informace o tom, jak provést obnovení, naleznete v [dokumentaci k programu NetBackup](http://www.veritas.com/docs/000094423). Doporučujeme, abyste vyhověli doporučeným postupům obnovení programu NetBackup.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorJednoduché převzetí služeb při selhání a zotavení po havárii

> [!NOTE]
> Pro scénáře cíle zálohování StorSimple Cloud Appliance není podporován jako cíl obnovení.

Katastrofa může být způsobena řadou faktorů. V následující tabulce jsou uvedeny běžné scénáře zotavení po havárii.

| Scénář | Dopad | Jak obnovit | Poznámky |
|---|---|---|---|
| Selhání zařízení StorSimple | Operace zálohování a obnovení jsou přerušeny. | Nahraďte neúspěšné zařízení a proveďte [storsimple převzetí služeb při selhání a zotavení po havárii](storsimple-device-failover-disaster-recovery.md). | Pokud potřebujete provést obnovení po obnovení zařízení, úplné pracovní sady dat se načtou z cloudu do nového zařízení. Všechny operace jsou v cloudu rychlostí. Proces opětovného prohledávání indexu a katalogu může způsobit, že všechny zálohovací sady budou zkontrolovány a vytaženy z cloudové vrstvy na místní úroveň zařízení, což může být časově náročný proces. |
| Selhání serveru NetBackup | Operace zálohování a obnovení jsou přerušeny. | Znovu sestavte záložní server a proveďte obnovení databáze. | Server NetBackup je nutné znovu vytvořit nebo obnovit v lokalitě pro zotavení po havárii. Obnovte databázi do nejnovějšího bodu. Pokud obnovená databáze NetBackup není synchronizována s nejnovějšími úlohami zálohování, je vyžadováno indexování a katalogizování. Tento proces opětovného prohledávání indexu a katalogu může způsobit, že všechny zálohovací sady budou zkontrolovány a staženy z cloudové vrstvy na místní úroveň zařízení. Díky tomu je to další časově náročné. |
| Selhání webu, které má za následek ztrátu záložního serveru a StorSimple | Operace zálohování a obnovení jsou přerušeny. | Nejprve obnovte StorSimple a potom obnovte program NetBackup. | Nejprve obnovte StorSimple a potom obnovte program NetBackup. Pokud potřebujete provést obnovení po obnovení zařízení, úplné pracovní sady dat se načtou z cloudu do nového zařízení. Všechny operace jsou v cloudu rychlostí. |

## <a name="references"></a>Odkazy

Pro tento článek byly uvedeny následující dokumenty:

- [StorJednoduché nastavení vícecestvých vstupně-nos](storsimple-configure-mpio-windows-server.md)
- [Scénáře úložiště: Tenké zřizování](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Použití jednotek GPT](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Nastavení stínových kopií pro sdílené složky](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Další kroky

- Další informace o [obnovení ze zálohovací sady](storsimple-restore-from-backup-set-u2.md).
- Další informace o tom, jak provést [převzetí služeb při selhání zařízení a zotavení po havárii](storsimple-device-failover-disaster-recovery.md).
