---
title: Řada StorSimple 8000 jako záložní cíl s veeam | Dokumenty společnosti Microsoft
description: Popisuje konfiguraci cíle zálohování StorSimple pomocí nástroje Veeam.
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
ms.author: matd
ms.openlocfilehash: 3ebf464fed1480e7452f246f04f3906faf0dd219
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67875304"
---
# <a name="storsimple-as-a-backup-target-with-veeam"></a>StorSimple jako záložní cíl s Veeam

## <a name="overview"></a>Přehled

Azure StorSimple je řešení hybridního cloudového úložiště od Microsoftu. StorSimple řeší složitosti exponenciálního růstu dat pomocí účtu Azure Storage jako rozšíření místního řešení a automatického vrstvení dat napříč místním úložištěm a cloudovým úložištěm.

V tomto článku budeme diskutovat o integraci StorSimple se společností Veeam a osvědčených postupech pro integraci obou řešení. Také doporučujeme, jak nastavit veeam tak, aby se co nejlépe integrovala se StorSimple. Nejlepší způsob, jak nastavit veeam tak, aby splňovaly individuální požadavky na zálohování a smlouvy o úrovni služeb (SLA), se podřídíme osvědčeným postupům společnosti Veeam, architektům zálohování a správcům.

I když ilustrujeme kroky konfigurace a klíčové koncepty, tento článek není v žádném případě podrobným průvodcem konfigurací nebo instalací. Předpokládáme, že základní komponenty a infrastruktura jsou v provozuschopném stavu a jsou připraveny podporovat koncepty, které popisujeme.

### <a name="who-should-read-this"></a>Kdo by to měl číst?

Informace v tomto článku budou velmi užitečné pro správce záloh, správce úložišť a architekty úložišť, kteří mají znalosti o úložišti, Windows Server 2012 R2, Ethernet, cloudové služby a Veeam.

### <a name="supported-versions"></a>Podporované verze

-   Veeam 9 a novější verze
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
![StorSimple vrstvení diagram](./media/storsimple-configure-backup-target-using-veeam/image1.jpg)

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

| Kapacita úložiště | 8100 | 8600 |
|---|---|---|
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

![StorSimple jako primární cílový diagram zálohování](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetlogicaldiagram.png)

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

![StorSimple jako sekundární záložní cílový diagram](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetlogicaldiagram.png)

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
3. Nasaďte veeam.

Každý krok je podrobně popsán v následujících částech.

### <a name="set-up-the-network"></a>Nastavit síť

Vzhledem k tomu, že StorSimple je řešení, které je integrované s cloudem Azure, StorSimple vyžaduje aktivní a pracovní připojení ke cloudu Azure. Toto připojení se používá pro operace, jako jsou cloudové snímky, správa dat a přenos metadat, a na úroveň starších, méně přístupných dat do cloudového úložiště Azure.

Aby řešení fungovalo optimálně, doporučujeme dodržovat tyto doporučené postupy pro vytváření sítí:

-   Propojení, které spojuje vaše StorSimple vrstvení s Azure musí splňovat vaše požadavky na šířku pásma. Toho dosáhnete použitím nezbytné úrovně kvality služeb (QoS) na přepínače infrastruktury tak, aby odpovídaly vašim službám SLA s cílem RPO a času obnovení (RTO).
-   Maximální latence přístupu k úložišti azure blob by měla být kolem 80 ms.

### <a name="deploy-storsimple"></a>Nasazení StorSimple

Podrobné pokyny pro nasazení StorSimple najdete v tématu [Nasazení místního zařízení StorSimple](storsimple-deployment-walkthrough-u2.md).

### <a name="deploy-veeam"></a>Nasazení veeamu

Doporučené postupy instalace veeamu naleznete [v doporučených postupech pro zálohování & replikace veeam](https://bp.veeam.expert/)a v uživatelské příručce k [centru nápovědy veeam (technická dokumentace).](https://www.veeam.com/documentation-guides-datasheets.html)

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

- Nepoužívejte rozložené svazky (vytvořené programem Windows Disk Management). Rozložené svazky nejsou podporovány.
- Naformátujte svazky pomocí systému souborů NTFS s velikostí alokační jednotky 64 KB.
- Namapujte svazky StorSimple přímo na server Veeam.
    - Použijte iSCSI pro fyzické servery.


## <a name="best-practices-for-storsimple-and-veeam"></a>Osvědčené postupy pro StorSimple a Veeam

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

### <a name="veeam-best-practices"></a>Osvědčené postupy veeam

-   Databáze Veeam by měla být místní na serveru a neměla by být umístěna na svazku StorSimple.
-   Obnovení po havárii zálohovat databázi Veeam na svazku StorSimple.
-   Podporujeme úplné a přírůstkové zálohování veeam pro toto řešení. Doporučujeme nepoužívat syntetické a rozdílové zálohy.
-   Záložní datové soubory by měly obsahovat pouze data pro určitou úlohu. Například nejsou povoleny žádné připojení médií mezi různými úlohami.
-   Vypněte ověření úlohy. V případě potřeby by ověření mělo být naplánováno po poslední úloze zálohování. Je důležité si uvědomit, že tato úloha ovlivňuje okno zálohování.
-   Zapněte předběžné přidělení médií.
-   Ujistěte se, že je zapnuto paralelní zpracování.
-   Vypněte kompresi.
-   Vypněte odstranění duplicit v úloze zálohování.
-   Nastavte optimalizaci na **CÍL SÍTĚ LAN**.
-   Zapněte **vytvořit aktivní úplné zálohování** (každé 2 týdny).
-   V úložišti záloh nastavte **záložní soubory Use per-VM**.
-   Nastavit **použít více datových proudů pro nahrávání na úlohu** na **8** (je povoleno maximálně 16). Upravte toto číslo nahoru nebo dolů na základě využití procesoru na zařízení StorSimple.

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

## <a name="set-up-veeam-storage"></a>Nastavení úložiště Veeam

### <a name="to-set-up-veeam-storage"></a>Nastavení úložiště Veeam

1.  V konzole Veeam Backup and Replication přejděte v **nástrojích úložiště**na **název Backup Infrastructure**. Klepněte pravým tlačítkem myši na **položku Úložiště záloh**a potom vyberte **příkaz Přidat úložiště záloh**.

    ![Konzola pro správu Veeam, stránka úložiště záloh](./media/storsimple-configure-backup-target-using-veeam/veeamimage1.png)

2.  V dialogovém okně **Nové úložiště záloh** zadejte název a popis úložiště. Vyberte **další**.

    ![Konzola pro správu Veeam, stránka s názvem a popisem](./media/storsimple-configure-backup-target-using-veeam/veeamimage2.png)

3.  Chcete-li tento typ, vyberte **server systému Microsoft Windows**. Vyberte server Veeam. Vyberte **další**.

    ![Konzola pro správu Veeam, výběr typu úložiště záloh](./media/storsimple-configure-backup-target-using-veeam/veeamimage3.png)

4.  Chcete-li zadat **umístění**, procházejte a vyberte svazek. Zaškrtněte políčko **Omezit maximální počet souběžných úloh na:** a nastavte hodnotu na **4**. Tím je zajištěno, že pouze čtyři virtuální disky jsou zpracovávány souběžně při zpracování každého virtuálního počítače (VM). Vyberte tlačítko **Upřesnit.**

    ![Konzola pro správu Veeam, výběr hlasitosti](./media/storsimple-configure-backup-target-using-veeam/veeamimage4.png)


5.  V dialogovém okně **Nastavení kompatibility úložiště** zaškrtněte políčko **Použít záložní soubory pro virtuální počítače.**

    ![Konzola pro správu Veeam, nastavení kompatibility úložiště](./media/storsimple-configure-backup-target-using-veeam/veeamimage5.png)

6.  V dialogovém okně **Nové úložiště záloh** zaškrtněte políčko **Povolit službu vPower NFS na připojovacím serveru (doporučeno).** Vyberte **další**.

    ![Konzola pro správu Veeam, stránka úložiště záloh](./media/storsimple-configure-backup-target-using-veeam/veeamimage6.png)

7.  Zkontrolujte nastavení a pak vyberte **Další**.

    ![Konzola pro správu Veeam, stránka úložiště záloh](./media/storsimple-configure-backup-target-using-veeam/veeamimage7.png)

    Na server Veeam je přidáno úložiště.

## <a name="set-up-storsimple-as-a-primary-backup-target"></a>Nastavení StorSimple jako primárního cíle zálohování

> [!IMPORTANT]
> Obnovení dat ze zálohy, která byla vrstvena do cloudu dochází při rychlostech cloudu.

Následující obrázek znázorňuje mapování typického svazku na záložní úlohu. V tomto případě všechny týdenní zálohy mapovat na celý disk v sobotu a přírůstkové zálohy mapovat na pondělí až pátek přírůstkové disky. Všechny zálohy a obnovení jsou z vrstveného svazku StorSimple.

![Logický diagram konfigurace primárního cíle zálohy](./media/storsimple-configure-backup-target-using-veeam/primarybackuptargetdiagram.png)

### <a name="storsimple-as-a-primary-backup-target-gfs-schedule-example"></a>Příklad plánu GFS jako primární hod ovacího systému zálohování

Zde je příklad plánu rotace GFS na čtyři týdny, měsíčně a ročně:

| Typ frekvence/zálohování | Do bloku | Přírůstkové (dny 1-5)  |   
|---|---|---|
| Týdně (týdny 1-4) | Sobota | Pondělí až pátek |
| měsíčně  | Sobota  |   |
| Roční | Sobota  |   |


### <a name="assign-storsimple-volumes-to-a-veeam-backup-job"></a>Přiřazení svazků StorSimple k úloze zálohování Veeam

Pro primární scénář cíle zálohování vytvořte denní úlohu s primárním svazkem Veeam StorSimple. Pro scénář sekundárního cíle zálohování vytvořte denní úlohu pomocí úložiště Direct Attached Storage (DAS), network attached storage (NAS) nebo Just a Bunch of Disks (JBOD).

#### <a name="to-assign-storsimple-volumes-to-a-veeam-backup-job"></a>Přiřazení svazků StorSimple k úloze zálohování Veeam

1.  V konzole Veeam Backup and Replication vyberte **Možnost Zálohování & replikace**. V závislosti na prostředí klepněte pravým tlačítkem myši na **položku Zálohování**a vyberte položku **VMware** nebo **Technologie Hyper-V**.

    ![Konzola pro správu Veeam, nová úloha zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage8.png)

2.  V dialogovém okně **Nová zálohovací úloha** zadejte název a popis denní úlohy zálohování.

    ![Veeam management console, nová stránka úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage9.png)

3.  Vyberte virtuální počítač, ke který chcete zálohovat.

    ![Veeam management console, nová stránka úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage10.png)

4.  Vyberte hodnoty, které chcete pro **záložní proxy** a **úložiště zálohování**. Vyberte hodnotu bodů **obnovení, které chcete zachovat na disku** podle definic RPO a RTO pro vaše prostředí v místně připojeném úložišti. Vyberte **Upřesnit**.

    ![Veeam management console, nová stránka úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage11.png)

5. V dialogovém okně **Upřesnit nastavení** vyberte na kartě **Zálohování** **přírůstkové**. Ujistěte se, že není zaškrtnuto políčko **Pravidelně vytvářet syntetické úplné zálohy.** Pravidelně zaškrtněte políčko **Vytvářet aktivní úplné zálohy.** V části **Aktivní úplné zálohování**zaškrtněte políčko **Týdně ve vybraných dnech** pro sobotu.

    ![Veeam management console, nová stránka s pokročilými nastaveními úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage12.png)

6. Na kartě **Úložiště** zkontrolujte, zda není zaškrtnuto políčko Povolit odstranění **duplicitních dat.** Zaškrtněte políčko **Vyloučit bloky odkládacích souborů** a zaškrtněte políčko Vyloučit **odstraněné bloky souborů.** Nastavte **úroveň komprese** na **žádnou**. Pro vyvážený výkon a odstranění duplicit nastavte **optimalizaci úložiště** na **cíl LAN**. Vyberte **OK**.

    ![Veeam management console, nová stránka s pokročilými nastaveními úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage13.png)

    Informace o nastavení odstranění duplicit a komprese veea naleznete v [tématu Komprese a odstranění duplicit dat](https://helpcenter.veeam.com/backup/vsphere/compression_deduplication.html).

7.  V dialogovém okně **Upravit úlohu zálohování** můžete zaškrtnout políčko **Povolit zpracování s ohledem na aplikaci** (volitelné).

    ![Veeam management console, nová stránka pro zpracování úloh pro zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage14.png)

8.  Nastavte plán tak, aby se spouštěl jednou denně, v době, kterou můžete zadat.

    ![Veeam management console, nová stránka plánu úloh zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage15.png)

## <a name="set-up-storsimple-as-a-secondary-backup-target"></a>Nastavení StorSimple jako sekundárního cíle zálohování

> [!NOTE]
> Obnovení dat ze zálohy, která byla vrstvena do cloudu dochází rychlostí cloudu.

V tomto modelu musíte mít paměťové médium (jiné než StorSimple) sloužit jako dočasné mezipaměti. Můžete například použít redundantní pole svazku nezávislých disků (RAID) pro umístění místa, vstupu/výstupu (I/O) a šířky pásma. Doporučujeme používat RAID 5, 50 a 10.

Následující obrázek znázorňuje typické místní svazky krátkodobého uchovávání (na server) a dlouhodobé archivní svazky. V tomto scénáři všechny zálohy spustit na místním svazku (na server) RAID. Tyto zálohy jsou pravidelně duplikovány a archivovány do archivního svazku. Je důležité velikost místního (na server) raid svazek tak, aby mohl zvládnout krátkodobé retenční kapacity a požadavky na výkon.

![StorSimple jako sekundární cílová záloha logický diagram](./media/storsimple-configure-backup-target-using-veeam/secondarybackuptargetdiagram.png)

### <a name="storsimple-as-a-secondary-backup-target-gfs-example"></a>StorSimple jako sekundární záložní cíl GFS příklad

Následující tabulka ukazuje, jak nastavit zálohy pro spuštění na místních a StorSimple disky. Zahrnuje individuální a celkové požadavky na kapacitu.

| Typ zálohy a uchovávání informací | Nakonfigurované úložiště | Velikost (TiB) | Multiplikátor GFS | Celková\* kapacita (TiB) |
|---|---|---|---|---|
| Týden 1 (plný a přírůstkový) |Místní disk (krátkodobý)| 1 | 1 | 1 |
| StorJednoduché týdny 2-4 |StorSimple disk (dlouhodobý) | 1 | 4 | 4 |
| Měsíčně plný |StorSimple disk (dlouhodobý) | 1 | 12 | 12 |
| Ročně plné |StorSimple disk (dlouhodobý) | 1 | 1 | 1 |
|Požadavek na velikost objemů GFS |  |  |  | 18*|

\*Celková kapacita zahrnuje 17 TiB disků StorSimple a 1 TiB místního svazku RAID.


### <a name="gfs-example-schedule"></a>Příklad plánu GFS

Týdenní, měsíční a roční rozvrh gfs

| Týden | Do bloku | Přírůstkový den 1 | Přírůstkový den 2 | Přírůstkový den 3 | Přírůstkový den 4 | Přírůstkový den 5 |
|---|---|---|---|---|---|---|
| Týden 1 | Místní svazek RAID  | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID | Místní svazek RAID |
| Týden 2 | StorJednoduché týdny 2-4 |   |   |   |   |   |
| Týden 3 | StorJednoduché týdny 2-4 |   |   |   |   |   |
| Týden 4 | StorJednoduché týdny 2-4 |   |   |   |   |   |
| měsíčně | StorJednoduché měsíčně |   |   |   |   |   |
| Roční | StorSimple ročně  |   |   |   |   |   |

### <a name="assign-storsimple-volumes-to-a-veeam-copy-job"></a>Přiřazení svazků StorSimple ke kopírovací úloze Veeam

#### <a name="to-assign-storsimple-volumes-to-a-veeam-copy-job"></a>Přiřazení svazků StorSimple ke kopírovací úloze Veeam

1.  V konzole Veeam Backup and Replication vyberte **Možnost Zálohování & replikace**. V závislosti na prostředí klepněte pravým tlačítkem myši na **položku Zálohování**a vyberte položku **VMware** nebo **Technologie Hyper-V**.

    ![Veeam management console, nová stránka úlohy záložní kopie](./media/storsimple-configure-backup-target-using-veeam/veeamimage16.png)

2.  V dialogovém okně **Nová úloha záložní kopie** zadejte název a popis úlohy.

    ![Veeam management console, nová stránka úlohy záložní kopie](./media/storsimple-configure-backup-target-using-veeam/veeamimage17.png)

3.  Vyberte virtuální chod, které chcete zpracovat. Vyberte ze záloh a pak vyberte denní zálohu, kterou jste vytvořili dříve.

    ![Veeam management console, nová stránka úlohy záložní kopie](./media/storsimple-configure-backup-target-using-veeam/veeamimage18.png)

4.  V případě potřeby vylučte objekty z úlohy záložní kopie.

5.  Vyberte úložiště záloh a nastavte hodnotu pro **body obnovení, které chcete zachovat**. Nezapomeňte zaškrtnout políčko **Zachovat následující body obnovení pro účely archivace.** Definujte frekvenci zálohování a pak vyberte **Upřesnit**.

    ![Veeam management console, nová stránka úlohy záložní kopie](./media/storsimple-configure-backup-target-using-veeam/veeamimage19.png)

6.  Zadejte následující upřesňující nastavení:

    * Na kartě **Údržba** vypněte ochranu proti poškození úrovně úložiště.

    ![Veeam management console, nová stránka s upřesňujícími nastaveními úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage20.png)

    * Na kartě **Úložiště** se ujistěte, že jsou vypnuté odstranění duplicit a komprese.

    ![Veeam management console, nová stránka s upřesňujícími nastaveními úlohy zálohování](./media/storsimple-configure-backup-target-using-veeam/veeamimage21.png)

7.  Určete, že přenos dat je přímý.

8.  Definujte plán okna zálohování kopií podle svých potřeb a dokončete průvodce.

Další informace naleznete v [tématu Vytvoření úloh záložní kopie](https://helpcenter.veeam.com/backup/hyperv/backup_copy_create.html).

## <a name="storsimple-cloud-snapshots"></a>Snímky cloudu StorSimple

Snímky cloudu StorSimple chrání data, která se nacházejí ve vašem zařízení StorSimple. Vytvoření cloudového snímku je ekvivalentní k odesílání místních záložních pásek do zařízení mimo lokalitu. Pokud používáte geograficky redundantní úložiště Azure, vytvoření cloudového snímku se rovná odesílání záložních pásek do více lokalit. Pokud potřebujete obnovit zařízení po havárii, můžete převést další zařízení StorSimple online a provést převzetí služeb při selhání. Po převzetí služeb při selhání budete mít přístup k datům (rychlostí cloudu) z nejnovějšího snímku cloudu.

Následující část popisuje, jak vytvořit krátký skript pro spuštění a odstranění snímků cloudu StorSimple během zálohování po zpracování.

> [!NOTE]
> Snímky, které jsou ručně nebo programově vytvořené nedodržují zásady vypršení platnosti snímku StorSimple. Tyto snímky musí být ručně nebo programově odstraněny.

### <a name="start-and-delete-cloud-snapshots-by-using-a-script"></a>Spuštění a odstranění snímků cloudu pomocí skriptu

> [!NOTE]
> Pečlivě vyhodnoťte požadavky na dodržování předpisů a uchovávání dat před odstraněním snímku StorSimple. Další informace o spuštění skriptu po zálohování naleznete v dokumentaci společnosti Veeam.


### <a name="backup-lifecycle"></a>Životní cyklus zálohování

![Diagram životního cyklu zálohování](./media/storsimple-configure-backup-target-using-veeam/backuplifecycle.png)

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
4. Chcete-li přidat skript do zálohovací úlohy, upravte pokročilé možnosti úlohy Veeam.

    ![Karta Skripty pro zálohování upřesňujících nastavení veeam](./media/storsimple-configure-backup-target-using-veeam/veeamimage22.png)

Doporučujeme spustit zásady zálohování cloudových snímků StorSimple jako skript po zpracování na konci denní úlohy zálohování. Další informace o zálohování a obnovení prostředí zálohovací aplikace, které vám pomohou splnit váš RPO a RTO, obraťte se na svého architekta zálohování.

## <a name="storsimple-as-a-restore-source"></a>StorSimple jako zdroj obnovení

Obnoví z zařízení StorSimple pracovat jako obnovení z libovolného bloku paměťového zařízení. Obnovení dat, která je vrstvené do cloudu dochází při rychlostech cloudu. U místních dat dochází k obnovení při místní rychlosti disku zařízení.

Se službou Veeam získáte rychlé a podrobné obnovení na úrovni souborů prostřednictvím technologie StorSimple prostřednictvím integrovaných zobrazení průzkumníků v konzoli Veeam. Pomocí průzkumníků Veeam můžete obnovit jednotlivé položky, jako jsou e-mailové zprávy, objekty služby Active Directory a položky služby SharePoint ze záloh. Obnovení lze provést bez přerušení místního virtuálního počítače. Můžete také provést obnovení v čase pro Azure SQL Database a Oracle databáze. Veeam a StorSimple usnadňují a usnadňují proces obnovy na úrovni položek z Azure. Informace o tom, jak provést obnovení, naleznete v dokumentaci společnosti Veeam:

- Pro [Exchange Server](https://www.veeam.com/microsoft-exchange-recovery.html)
- Pro [službu Active Directory](https://www.veeam.com/microsoft-active-directory-explorer.html)
- Pro [SQL Server](https://www.veeam.com/microsoft-sql-server-explorer.html)
- Pro [službu SharePoint](https://www.veeam.com/microsoft-sharepoint-recovery-explorer.html)
- Pro [společnost Oracle](https://www.veeam.com/oracle-backup-recovery-explorer.html)


## <a name="storsimple-failover-and-disaster-recovery"></a>StorJednoduché převzetí služeb při selhání a zotavení po havárii

> [!NOTE]
> Pro scénáře cíle zálohování StorSimple Cloud Appliance není podporován jako cíl obnovení.

Katastrofa může být způsobena řadou faktorů. V následující tabulce jsou uvedeny běžné scénáře zotavení po havárii.

| Scénář | Dopad | Jak obnovit | Poznámky |
|---|---|---|---|
| Selhání zařízení StorSimple | Operace zálohování a obnovení jsou přerušeny. | Nahraďte neúspěšné zařízení a proveďte [storsimple převzetí služeb při selhání a zotavení po havárii](storsimple-device-failover-disaster-recovery.md). | Pokud potřebujete provést obnovení po obnovení zařízení, úplné pracovní sady dat se načtou z cloudu do nového zařízení. Všechny operace jsou v cloudu rychlostí. Proces opětovného prohledávání indexu a katalogu může způsobit, že všechny zálohovací sady budou zkontrolovány a vytaženy z cloudové vrstvy na místní úroveň zařízení, což může být časově náročný proces. |
| Selhání serveru Veeam | Operace zálohování a obnovení jsou přerušeny. | Znovu sestavte záložní server a proveďte obnovení databáze, jak je podrobně popsáno v [Centru nápovědy Veeam (technická dokumentace).](https://www.veeam.com/documentation-guides-datasheets.html)  | Server Veeam musíte znovu sestavit nebo obnovit v lokalitě pro zotavení po havárii. Obnovte databázi do nejnovějšího bodu. Pokud obnovená databáze Veeam není synchronizována s nejnovějšími úlohami zálohování, je vyžadováno indexování a katalogizaci. Tento proces opětovného prohledávání indexu a katalogu může způsobit, že všechny zálohovací sady budou zkontrolovány a staženy z cloudové vrstvy na místní úroveň zařízení. Díky tomu je to další časově náročné. |
| Selhání webu, které má za následek ztrátu záložního serveru a StorSimple | Operace zálohování a obnovení jsou přerušeny. | Nejprve obnovte StorSimple a pak obnovte Veeam. | Nejprve obnovte StorSimple a pak obnovte Veeam. Pokud potřebujete provést obnovení po obnovení zařízení, úplné pracovní sady dat se načtou z cloudu do nového zařízení. Všechny operace jsou v cloudu rychlostí. |


## <a name="references"></a>Odkazy

Pro tento článek byly uvedeny následující dokumenty:

- [StorJednoduché nastavení vícecestvých vstupně-nos](storsimple-configure-mpio-windows-server.md)
- [Scénáře úložiště: Tenké zřizování](https://msdn.microsoft.com/library/windows/hardware/dn265487.aspx)
- [Použití jednotek GPT](https://msdn.microsoft.com/windows/hardware/gg463524.aspx#EHD)
- [Nastavení stínových kopií pro sdílené složky](https://technet.microsoft.com/library/cc771893.aspx)

## <a name="next-steps"></a>Další kroky

- Další informace o [obnovení ze zálohovací sady](storsimple-restore-from-backup-set-u2.md).
- Další informace o tom, jak provést [převzetí služeb při selhání zařízení a zotavení po havárii](storsimple-device-failover-disaster-recovery.md).
