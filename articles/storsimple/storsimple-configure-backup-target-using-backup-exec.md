---
title: StorSimple 8000 série jako cíl zálohování s Zálohování Exec | Dokumenty společnosti Microsoft
description: Popisuje konfiguraci cíle zálohování StorSimple pomocí programu Veritas Backup Exec.
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
ms.author: matd
ms.openlocfilehash: 4dcda65384190050e11f1bf9b15c706b0e38c6b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561639"
---
# <a name="storsimple-as-a-backup-target-with-backup-exec"></a>StorSimple jako cíl zálohování s programem Backup Exec

## <a name="overview"></a>Přehled

Azure StorSimple je řešení hybridního cloudového úložiště od Microsoftu. StorSimple řeší složitosti exponenciálního růstu dat pomocí účtu úložiště Azure jako rozšíření místního řešení a automatického vrstvení dat napříč místním úložištěm a cloudovým úložištěm.

V tomto článku budeme diskutovat O integraci StorSimple s Veritas Backup Exec a osvědčených postupech pro integraci obou řešení. Také jsme doporučení, jak nastavit Zálohování Exec pro nejlepší integraci s StorSimple. Doporučujeme veritas osvědčené postupy, záložní architekti a správci pro nejlepší způsob, jak nastavit zálohování Exec tak, aby splňovaly individuální požadavky na zálohování a smlouvy o úrovni služeb (SLA).

I když ilustrujeme kroky konfigurace a klíčové koncepty, tento článek není v žádném případě podrobným průvodcem konfigurací nebo instalací. Předpokládáme, že základní komponenty a infrastruktura jsou v provozuschopném stavu a jsou připraveny podporovat koncepty, které popisujeme.

### <a name="who-should-read-this"></a>Kdo by to měl číst?

Informace v tomto článku budou velmi užitečné pro správce záloh, správce úložišť a architekty úložišť, kteří mají znalosti o úložišti, Windows Server 2012 R2, Ethernet, cloudové služby a Backup Exec.

## <a name="supported-versions"></a>Podporované verze

-   [Zálohování Exec 16 a novější verze](https://www.veritas.com/content/support/en_US/article.100040087)
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
![StorSimple vrstvení diagram](./media/storsimple-configure-backup-target-using-backup-exec/image1.jpg)

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

![StorSimple jako primární cílový diagram zálohování](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetlogicaldiagram.png)

### <a name="primary-target-backup-logical-steps"></a>Logické kroky primární cílové zálohy

1.  Záložní server kontaktuje cílového agenta zálohování a záložní agent přenáší data na záložní server.
2.  Záložní server zapisuje data do svazků storsimple vrstvených.
3.  Záložní server aktualizuje databázi katalogu a potom dokončí úlohu zálohování.
4.  Skript snímek aktivuje Správce snímků cloudu StorSimple (spuštění nebo odstranění).
5.  Záložní server odstraní zálohy, jejichž platnost vypršela na základě zásad uchovávání informací.


### <a name="primary-target-restore-logical-steps"></a>Logické kroky obnovení primárního cíle

1.  Záložní server spustí obnovení příslušných dat z úložiště úložiště.
2.  Záložní agent obdrží data ze záložního serveru.
3.  Zálohovací server dokončí úlohu obnovení.

## <a name="storsimple-as-a-secondary-backup-target"></a>StorSimple jako sekundární cíl zálohování

V tomto scénáři StorSimple svazky primárně se používají pro dlouhodobé uchovávání nebo archivaci.

Následující obrázek znázorňuje architekturu, ve které počáteční zálohy a obnovení cílí na vysoce výkonný svazek. Tyto zálohy jsou zkopírovány a archivovány na vrstvený svazek StorSimple podle nastaveného plánu.

Je důležité velikost i vysoký výkon svazku tak, aby jej můžete zpracovat vaše požadavky na kapacitu zásad uchovávání informací a výkonu.

![StorSimple jako sekundární záložní cílový diagram](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetlogicaldiagram.png)

### <a name="secondary-target-backup-logical-steps"></a>Logické kroky sekundární hotřské zálohy

1.  Záložní server kontaktuje cílového agenta zálohování a záložní agent přenáší data na záložní server.
2.  Záložní server zapisuje data do vysoce výkonného úložiště.
3.  Záložní server aktualizuje databázi katalogu a potom dokončí úlohu zálohování.
4.  Záložní server zkopíruje zálohy na StorSimple na základě zásad uchovávání informací.
5.  Skript snímek aktivuje Správce snímků cloudu StorSimple (spuštění nebo odstranění).
6.  Záložní server odstraní zálohy, jejichž platnost vypršela na základě zásad uchovávání informací.

### <a name="secondary-target-restore-logical-steps"></a>Logické kroky obnovení sekundárního cíle

1.  Záložní server spustí obnovení příslušných dat z úložiště úložiště.
2.  Záložní agent obdrží data ze záložního serveru.
3.  Zálohovací server dokončí úlohu obnovení.

## <a name="deploy-the-solution"></a>Nasazení řešení

Nasazení řešení vyžaduje tři kroky:
1. Připravte síťovou infrastrukturu.
2. Nasaďte zařízení StorSimple jako cíl zálohování.
3. Nasadit záložní exec.

Každý krok je podrobně popsán v následujících částech.

### <a name="set-up-the-network"></a>Nastavit síť

Vzhledem k tomu, že StorSimple je řešení, které je integrované s cloudem Azure, StorSimple vyžaduje aktivní a pracovní připojení ke cloudu Azure. Toto připojení se používá pro operace, jako jsou cloudové snímky, správa a přenos metadat a na úroveň starších, méně přístupných dat do cloudového úložiště Azure.

Aby řešení fungovalo optimálně, doporučujeme dodržovat tyto doporučené postupy pro vytváření sítí:

-   Propojení, které spojuje vaše StorSimple vrstvení s Azure musí splňovat vaše požadavky na šířku pásma. Chcete-li toho dosáhnout, použijte potřebnou úroveň kvality služby (QoS) na přepínače infrastruktury tak, aby odpovídaly vašim službám SLA cíle RPO a času obnovení (RTO).
-   Maximální latence přístupu k úložišti azure blob by měla být kolem 80 ms.

### <a name="deploy-storsimple"></a>Nasazení StorSimple

Podrobné pokyny pro nasazení StorSimple najdete v tématu [Nasazení místního zařízení StorSimple](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-backup-exec"></a>Nasazení programu Backup Exec

Doporučené postupy instalace programu Backup Exec naleznete v [tématu Doporučené postupy pro instalaci programu Backup Exec](https://www.veritas.com/content/support/en_US/doc/72686287-131623464-0/v70444238-131623464).

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

- Nepoužívejte rozložené svazky (vytvořené programem Windows Disk Management). Rozložené disky nejsou podporovány.
- Naformátujte svazky pomocí systému souborů NTFS s velikostí přidělení 64 KB.
- Namapujte svazky StorSimple přímo na server Backup Exec.
    - Použijte iSCSI pro fyzické servery.
    - Pro virtuální servery používejte předávací disky.

## <a name="best-practices-for-storsimple-and-backup-exec"></a>Doporučené postupy pro StorSimple a Backup Exec

Nastavte řešení podle pokynů v následujících částech.

### <a name="operating-system-best-practices"></a>Osvědčené postupy operačního systému

- Zakažte šifrování a odstranění duplicit systému Souborů Systému souborů Windows Server.
- Zakažte defragmentaci systému Windows Server na svazcích StorSimple.
- Zakažte indexování systému Windows Server na svazcích StorSimple.
- Spusťte antivirovou kontrolu u zdrojového hostitele (nikoli proti svazkům StorSimple).
- Vypněte výchozí [údržbu systému Windows Server](https://msdn.microsoft.com/library/windows/desktop/hh848037.aspx) ve Správci úloh. Udělejte to jedním z následujících způsobů:
  - Vypněte konfigurátor Údržba v Plánovači úloh systému Windows.
  - Stáhnout [PsExec](https://technet.microsoft.com/sysinternals/bb897553.aspx) z Windows Sysinternals. Po stažení psexec spusťte Azure PowerShell jako správce a zadejte:
    ```powershell
    psexec \\%computername% -s schtasks /change /tn “MicrosoftWindowsTaskSchedulerMaintenance Configurator" /disable
    ```

### <a name="storsimple-best-practices"></a>StorSimple osvědčené postupy

  -   Ujistěte se, že zařízení StorSimple je aktualizováno na [aktualizaci 3 nebo novější](storsimple-install-update-3.md).
  -   Izolujte iSCSI a cloudový provoz. Pro přenosmezi StorSimple a záložním serverem použijte vyhrazená připojení iSCSI.
  -   Ujistěte se, že vaše zařízení StorSimple je vyhrazený cíl zálohování. Smíšené úlohy nejsou podporovány, protože ovlivňují vaše RTO a RPO.

### <a name="backup-exec-best-practices"></a>Doporučené postupy pro zálohování exec

-   Záložní Exec musí být nainstalován na místní jednotce serveru a nikoli na svazku StorSimple.
-   Nastavte **operace souběžného zápisu** úložiště Backup Exec na maximální povolenou hodnotu.
    -   Nastavte blok úložiště Backup Exec **a velikost vyrovnávací paměti** na 512 kB.
    -   Zapněte možnost Čtení a zápis úložiště Backup Exec **ve vyrovnávací paměti**.
-   StorSimple podporuje úplné a přírůstkové zálohování Backup Exec. Doporučujeme nepoužívat syntetické a rozdílové zálohy.
-   Záložní datové soubory by měly obsahovat data pouze pro určitou úlohu. Například nejsou povoleny žádné připojení médií mezi různými úlohami.
-   Zakázat ověření úlohy. V případě potřeby by ověření mělo být naplánováno po poslední úloze zálohování. Je důležité si uvědomit, že tato úloha ovlivňuje okno zálohování.
-   Vyberte **možnost Úložiště** > **Your disk** > **vlastností****podrobností** > disku . Vypněte **předběžné přidělení místa na disku**.

Nejnovější nastavení programu Backup Exec a doporučené postupy pro implementaci těchto požadavků naleznete [na webu společnosti Veritas](https://www.veritas.com).

## <a name="retention-policies"></a>Zásady uchovávání informací

Jedním z nejběžnějších typů zásad uchovávání záloh je zásada Dědeček, Otec a Syn (GFS). V zásadách GFS se přírůstkové zálohování provádí denně a úplné zálohy se provádějí týdně a měsíčně. Výsledkem této zásady je šest vrstvených svazků StorSimple. Jeden svazek obsahuje týdenní, měsíční a roční úplné zálohy. Dalších pět svazků ukládá denní přírůstkové zálohy.

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

## <a name="set-up-backup-exec-storage"></a>Nastavení úložiště Backup Exec

### <a name="to-set-up-backup-exec-storage"></a>Nastavení úložiště Backup Exec

1.  V konzole pro správu Backup Exec vyberte **možnost** > **Konfigurace úložiště** > **na disku úložiště** > **Další**.

    ![Zálohování konzoly pro správu Exec, konfigurace stránky úložiště](./media/storsimple-configure-backup-target-using-backup-exec/image4.png)

2.  Vyberte **Disk Ové úložiště**a pak vyberte **Další**.

    ![Zálohování konzoly pro správu Exec, vyberte stránku úložiště](./media/storsimple-configure-backup-target-using-backup-exec/image5.png)

3.  Zadejte reprezentativní název, například **Sobota Plná**a popis. Vyberte **další**.

    ![Zálohování konzoly pro správu Exec, stránka s názvem a popisem](./media/storsimple-configure-backup-target-using-backup-exec/image7.png)

4.  Vyberte disk, na kterém chcete vytvořit diskové paměťové zařízení, a pak vyberte **další**.

    ![Konzola pro správu backup Exec, stránka pro výběr disku úložiště](./media/storsimple-configure-backup-target-using-backup-exec/image9.png)

5.  Zvýšení počtu operací zápisu na **16**a potom vyberte **další**.

    ![Zálohování konzoly pro správu Exec, stránka nastavení souběžných operací zápisu](./media/storsimple-configure-backup-target-using-backup-exec/image10.png)

6.  Zkontrolujte nastavení a pak vyberte **Dokončit**.

    ![Zálohovací konzola pro správu Exec, stránka souhrnu konfigurace úložiště](./media/storsimple-configure-backup-target-using-backup-exec/image11.png)

7.  Na konci každého přiřazení svazku změňte nastavení zařízení úložiště tak, aby odpovídalo nastavení doporučenému doporučenému [nastavení pro storsimple a backup exec](#best-practices-for-storsimple-and-backup-exec).

    ![Zálohovací konzola pro správu Exec, stránka nastavení paměťového zařízení](./media/storsimple-configure-backup-target-using-backup-exec/image12.png)

8.  Opakujte kroky 1-7, dokud nedokončíte přiřazení svazků StorSimple k programu Backup Exec.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Nastavení StorSimple jako primárního cíle zálohování

> [!NOTE]
> Obnovení dat ze zálohy, která byla vrstvena do cloudu dochází při rychlostech cloudu.

Následující obrázek znázorňuje mapování typického svazku na záložní úlohu. V tomto případě všechny týdenní zálohy mapovat na celý disk v sobotu a přírůstkové zálohy mapovat na pondělí až pátek přírůstkové disky. Všechny zálohy a obnovení jsou z vrstveného svazku StorSimple.

![Logický diagram konfigurace primárního cíle zálohy](./media/storsimple-configure-backup-target-using-backup-exec/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Příklad plánu GFS jako primární hod ovacího systému zálohování

Zde je příklad plánu rotace GFS na čtyři týdny, měsíčně a ročně:

| Typ frekvence/zálohování | Do bloku | Přírůstkové (dny 1-5)  |   
|---|---|---|
| Týdně (týdny 1-4) | Sobota | Pondělí až pátek |
| měsíčně  | Sobota  |   |
| Roční | Sobota  |   |


### <a name="assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Přiřazení svazků StorSimple k úloze zálohování Exec

Následující pořadí předpokládá, že Backup Exec a cílový hostitel jsou konfigurovány v souladu s pokyny agenta Backup Exec.

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-backup-job"></a>Přiřazení svazků StorSimple k úloze zálohování Exec

1.  V konzole pro správu programu Backup Exec vyberte **možnost Zálohování** > **hostitele** > na**disk**.

    ![Zálohování konzoly pro správu Exec, výběr hostitele, zálohování a zálohování na disk](./media/storsimple-configure-backup-target-using-backup-exec/image14.png)

2.  V dialogovém okně **Vlastnosti definice zálohy** vyberte v části **Zálohování** **možnost Upravit**.

    ![Konzola pro správu backup Exec, dialogové okno Vlastnosti definice zálohy](./media/storsimple-configure-backup-target-using-backup-exec/image15.png)

3.  Nastavte úplné a přírůstkové zálohy tak, aby splňovaly vaše požadavky rpo a RTO a splňovaly osvědčené postupy společnosti Veritas.

4.  V dialogovém okně **Možnosti zálohování** vyberte **možnost Úložiště**.

    ![Konzola pro správu programu Backup Exec, dialogové okno Úložiště možností zálohování](./media/storsimple-configure-backup-target-using-backup-exec/image16.png)

5.  Přiřaďte odpovídající svazky StorSimple k plánu zálohování.

    > [!NOTE]
    > **Typ Komprese** a **šifrování** jsou **nastaveny**na žádný .

6.  V **části Ověřit**zaškrtněte políčko **Neověřovat data pro tuto úlohu.** Použití této možnosti může ovlivnit StorSimple vrstvení.

    > [!NOTE]
    > Defragmentace, indexování a ověření na pozadí negativně ovlivnit StorSimple vrstvení.

    ![Konzola pro správu programu Backup Exec, možnosti zálohování ověřují nastavení](./media/storsimple-configure-backup-target-using-backup-exec/image17.png)

7.  Po nastavení zbývajících možností zálohování tak, aby vyhovovaly vašim požadavkům, vyberte tlačítko **OK,** které chcete dokončit.

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Nastavení StorSimple jako sekundárního cíle zálohování

> [!NOTE]
>Obnovení dat ze zálohy, která byla vrstvena do cloudu dochází rychlostí cloudu.

V tomto modelu musíte mít paměťové médium (jiné než StorSimple) sloužit jako dočasné mezipaměti. Můžete například použít redundantní pole svazku nezávislých disků (RAID) pro umístění místa, vstupu/výstupu (I/O) a šířky pásma. Doporučujeme používat RAID 5, 50 a 10.

Následující obrázek znázorňuje typické místní svazky krátkodobého uchovávání (na server) a svazky archivů dlouhodobého uchovávání. V tomto scénáři všechny zálohy spustit na místním svazku (na server) RAID. Tyto zálohy jsou pravidelně duplikovány a archivovány do svazku archivů. Je důležité velikost místního (na server) raid svazek tak, aby mohl zvládnout krátkodobé retenční kapacity a požadavky na výkon.

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple jako sekundární záložní cíl GFS příklad

![StorSimple jako sekundární cílová záloha logický diagram](./media/storsimple-configure-backup-target-using-backup-exec/secondarybackuptargetdiagram.png)

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


### <a name="assign-storsimple-volumes-to-a-backup-exec-archive-and-deduplication-job"></a>Přiřazení svazků StorSimple k úloze archivu Backup Exec a deduplikaci

#### <a name="to-assign-storsimple-volumes-to-a-backup-exec-archive-and-duplication-job"></a>Přiřazení svazků StorSimple k úloze archivu a duplikace aplikace Backup Exec

1.  V konzole pro správu programu Backup Exec klepněte pravým tlačítkem myši na úlohu, kterou chcete archivovat na svazku StorSimple, a potom vyberte**příkaz** **Úpravy vlastností definice** > zálohování .

    ![Konzola pro správu Backup Exec, karta Vlastnosti definice zálohování](./media/storsimple-configure-backup-target-using-backup-exec/image19.png)

2.  Vyberte Přidat**duplikát** >  **fáze** > do**nástroje Úpravy**disku .

    ![Zálohování konzoly pro správu Exec, přidání fáze](./media/storsimple-configure-backup-target-using-backup-exec/image20.png)

3.  V dialogovém okně **Možnosti duplikování** vyberte hodnoty, které chcete použít pro **zdroj** a **plán**.

    ![Zálohování konzoly pro správu Exec, vlastnosti definic záloh a duplicitní možnosti](./media/storsimple-configure-backup-target-using-backup-exec/image21.png)

4.  V rozevíracím seznamu **Úložiště** vyberte svazek StorSimple, na kterém má úloha archivu ukládat data.

    ![Zálohování konzoly pro správu Exec, vlastnosti definic záloh a duplicitní možnosti](./media/storsimple-configure-backup-target-using-backup-exec/image22.png)

5.  Vyberte **Ověřit**a zaškrtněte políčko **Neověřovat data pro tuto úlohu.**

    ![Zálohování konzoly pro správu Exec, vlastnosti definic záloh a duplicitní možnosti](./media/storsimple-configure-backup-target-using-backup-exec/image23.png)

6.  Vyberte **OK**.

    ![Zálohování konzoly pro správu Exec, vlastnosti definic záloh a duplicitní možnosti](./media/storsimple-configure-backup-target-using-backup-exec/image24.png)

7.  Ve sloupci **Zálohování** přidejte novou fázi. Pro zdroj použijte **přírůstkové**. Pro cíl zvolte svazek StorSimple, kde je archivována úloha přírůstkového zálohování. Opakujte kroky 1-6.

## <a name="storsimple-cloud-snapshots"></a>Snímky cloudu StorSimple

Snímky cloudu StorSimple chrání data, která se nacházejí ve vašem zařízení StorSimple. Vytvoření cloudového snímku je ekvivalentní k odesílání místních záložních pásek do zařízení mimo lokalitu. Pokud používáte geograficky redundantní úložiště Azure, vytvoření cloudového snímku se rovná odesílání záložních pásek do více lokalit. Pokud potřebujete obnovit zařízení po havárii, můžete převést další zařízení StorSimple online a provést převzetí služeb při selhání. Po převzetí služeb při selhání budete mít přístup k datům (rychlostí cloudu) z nejnovějšího snímku cloudu.

Následující část popisuje, jak vytvořit krátký skript pro spuštění a odstranění snímků cloudu StorSimple během zálohování po zpracování.

> [!NOTE]
> Snímky, které jsou ručně nebo programově vytvořené nedodržují zásady vypršení platnosti snímku StorSimple. Tyto snímky musí být ručně nebo programově odstraněny.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Spuštění a odstranění snímků cloudu pomocí skriptu

> [!NOTE]
> Pečlivě vyhodnoťte požadavky na dodržování předpisů a uchovávání dat před odstraněním snímku StorSimple. Další informace o spuštění skriptu po zálohování naleznete v [dokumentaci k programu Backup Exec](https://www.veritas.com/support/en_US/article.100032497.html).

### <a name="backup-lifecycle"></a>Životní cyklus zálohování

![Diagram životního cyklu zálohování](./media/storsimple-configure-backup-target-using-backup-exec/backuplifecycle.png)

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
4. Přidejte skript do zálohovací úlohy v aplikaci Backup Exec úpravou příkazů předběžného zpracování a následného zpracování možností zálohování Exec.

   ![Záloha Exec konzole, možnosti zálohování, pre- a post-zpracování příkazy kartu](./media/storsimple-configure-backup-target-using-backup-exec/image25.png)

> [!NOTE]
> Doporučujeme spustit zásady zálohování cloudových snímků StorSimple jako skript po zpracování na konci denní úlohy zálohování. Další informace o zálohování a obnovení prostředí zálohovací aplikace, které vám pomohou splnit váš RPO a RTO, obraťte se na svého architekta zálohování.

## <a name="storsimple-as-a-restore-source"></a>StorSimple jako zdroj obnovení

Obnoví z zařízení StorSimple pracovat jako obnovení z libovolného bloku paměťového zařízení. Obnovení dat, která je vrstvené do cloudu dochází při rychlostech cloudu. U místních dat dochází k obnovení při místní rychlosti disku zařízení. Informace o tom, jak provést obnovení, naleznete v dokumentaci k programu Backup Exec. Doporučujeme, abyste v souladu s zálohování Exec obnovení osvědčených postupů.

## <a name="storsimple-failover-and-disaster-recovery"></a>StorJednoduché převzetí služeb při selhání a zotavení po havárii

> [!NOTE]
> Pro scénáře cíle zálohování StorSimple Cloud Appliance není podporován jako cíl obnovení.

Katastrofa může být způsobena řadou faktorů. V následující tabulce jsou uvedeny běžné scénáře zotavení po havárii.

| Scénář | Dopad | Jak obnovit | Poznámky |
|---|---|---|---|
| Selhání zařízení StorSimple | Operace zálohování a obnovení jsou přerušeny. | Nahraďte neúspěšné zařízení a proveďte [storsimple převzetí služeb při selhání a zotavení po havárii](storsimple-device-failover-disaster-recovery.md). | Pokud potřebujete provést obnovení po obnovení zařízení, úplné pracovní sady dat se načtou z cloudu do nového zařízení. Všechny operace jsou v cloudu rychlostí. Proces opětovného prohledávání indexování a katalogizaci může způsobit, že všechny zálohovací sady budou zkontrolovány a staženy z cloudové vrstvy na místní úroveň zařízení, což může být časově náročný proces. |
| Selhání serveru Backup Exec | Operace zálohování a obnovení jsou přerušeny. | Znovu sestavte záložní server a proveďte obnovení databáze, jak je podrobně popsáno v části [Jak provést ruční zálohování a obnovení databáze BACKUP Exec (BEDB).](http://www.veritas.com/docs/000041083) | Server Backup Exec je nutné znovu sestavit nebo obnovit v lokalitě pro zotavení po havárii. Obnovte databázi do nejnovějšího bodu. Pokud obnovená databáze Backup Exec není synchronizována s nejnovějšími úlohami zálohování, je vyžadováno indexování a katalogizování. Tento proces opětovného prohledávání indexu a katalogu může způsobit, že všechny zálohovací sady budou zkontrolovány a staženy z cloudové vrstvy na místní úroveň zařízení. Díky tomu je to další časově náročné. |
| Selhání webu, které má za následek ztrátu záložního serveru a StorSimple | Operace zálohování a obnovení jsou přerušeny. | Nejprve obnovte StorSimple a potom obnovte program Backup Exec. | Nejprve obnovte StorSimple a potom obnovte program Backup Exec. Pokud potřebujete provést obnovení po obnovení zařízení, úplné pracovní sady dat se načtou z cloudu do nového zařízení. Všechny operace jsou v cloudu rychlostí. |

## <a name="references"></a>Odkazy

Pro tento článek byly uvedeny následující dokumenty:

- [StorJednoduché nastavení vícecestvých vstupně-nos](storsimple-configure-mpio-windows-server.md)
- [Scénáře úložiště: Tenké zřizování](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Použití jednotek GPT](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Nastavení stínových kopií pro sdílené složky](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Další kroky

- Další informace o [obnovení ze zálohovací sady](storsimple-restore-from-backup-set-u2.md).
- Další informace o tom, jak provést [převzetí služeb při selhání zařízení a zotavení po havárii](storsimple-device-failover-disaster-recovery.md).
