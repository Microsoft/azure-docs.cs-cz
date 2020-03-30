---
title: Ochrana proti obnově stavu systému a holého kovu
description: Pomocí Azure Backup Server zálohujte stav systému a zajistěte ochranu proti úplnému obnovení (BMR).
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 358a1c96d598788170993fc48e60daae2b6b036c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77505877"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-by-using-azure-backup-server"></a>Zálohování stavu systému a obnovení holého stavu pomocí azure backup serveru

Azure Backup Server zálohuje stav systému a poskytuje holé obnovení (BMR) ochranu.

* **Zálohování stavu systému**: Zálohuje soubory operačního systému. Tato záloha umožňuje obnovení při spuštění počítače, ale dojde ke ztrátě systémových souborů a registru. Záloha stavu systému obsahuje následující prvky:
  * Člen domény: Spouštěcí soubory, registrační databáze třídy modelu COM+, registr
  * Řadič domény: Windows Server Active Directory (NTDS), spouštěcí soubory, registrační databáze tříd modelu COM+, registr, systémový svazek (SYSVOL)
  * Počítač, ve kterých běží clusterové služby: Metadata clusterového serveru
  * Počítač, ve kterých jsou spuštěny certifikační služby: Data certifikátů
* **Zálohování holého kovu**: Zálohuje soubory operačního systému a všechna data na kritických svazcích, s výjimkou uživatelských dat. Podle definice záloha BMR obsahuje zálohu stavu systému. Poskytuje ochranu, když se počítač nespustí a musíte vše obnovit.

Následující tabulka shrnuje, co můžete zálohovat a obnovit. Informace o verzích aplikací, které může chránit stav systému a BMR, najdete v tématu [Co zálohuje server Azure Backup?](backup-mabs-protection-matrix.md).

|Zálohování|Problém|Obnovení ze zálohy serveru Azure|Obnovení ze zálohy stavu systému|Obnovení BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Data souborů**<br /><br />Zálohování běžných dat<br /><br />BMR nebo záloha stavu systému|Ztracená data souborů|Ano|Ne|Ne|
|**Data souborů**<br /><br />Zálohování dat souboru v Azure Backup Server<br /><br />BMR nebo záloha stavu systému|Ztracený nebo poškozený operační systém|Ne|Ano|Ano|
|**Data souborů**<br /><br />Zálohování dat souboru v Azure Backup Server<br /><br />BMR nebo záloha stavu systému|Ztracený server (datové svazky beze změny)|Ne|Ne|Ano|
|**Data souborů**<br /><br />Zálohování dat souboru v Azure Backup Server<br /><br />BMR nebo záloha stavu systému|Ztracený server (datové svazky ztracené)|Ano|Ne|Ano<br /><br />BMR, následované pravidelnou obnovou zálohovaných dat souborů|
|**Data služby SharePoint**<br /><br />Zálohování dat farmy v Azure Backup Server<br /><br />BMR nebo záloha stavu systému|Ztracený web, seznamy, položky seznamu, dokumenty|Ano|Ne|Ne|
|**Data služby SharePoint**<br /><br />Zálohování dat farmy v Azure Backup Server<br /><br />BMR nebo záloha stavu systému|Ztracený nebo poškozený operační systém|Ne|Ano|Ano|
|**Data služby SharePoint**<br /><br />Zálohování dat farmy v Azure Backup Server<br /><br />BMR nebo záloha stavu systému|Zotavení po havárii|Ne|Ne|Ne|
|Windows Server 2012 R2 Hyper-V<br /><br />Zálohování hostitele nebo hosta azure zálohovacího serveru<br /><br />BMR nebo záloha stavu systému hostitele|Ztracený virtuální počítač|Ano|Ne|Ne|
|Hyper-V<br /><br />Zálohování hostitele nebo hosta azure zálohovacího serveru<br /><br />BMR nebo záloha stavu systému hostitele|Ztracený nebo poškozený operační systém|Ne|Ano|Ano|
|Hyper-V<br /><br />Zálohování hostitele nebo hosta azure zálohovacího serveru<br /><br />BMR nebo záloha stavu systému hostitele|Ztracený hostitel Hyper-V (virtuální počítače beze změny)|Ne|Ne|Ano|
|Hyper-V<br /><br />Zálohování hostitele nebo hosta azure zálohovacího serveru<br /><br />BMR nebo záloha stavu systému hostitele|Ztracený hostitel Hyper-V (virtuální počítače ztracené)|Ne|Ne|Ano<br /><br />BMR, následované pravidelným obnovením serveru Zálohování Azure|
|SQL Server nebo Exchange<br /><br />Zálohování aplikace Azure Backup Server<br /><br />BMR nebo záloha stavu systému|Ztracená data aplikací|Ano|Ne|Ne|
|SQL Server nebo Exchange<br /><br />Zálohování aplikace Azure Backup Server<br /><br />BMR nebo záloha stavu systému|Ztracený nebo poškozený operační systém|Ne|Ano|Ano|
|SQL Server nebo Exchange<br /><br />Zálohování aplikace Azure Backup Server<br /><br />BMR nebo záloha stavu systému|Ztracený server (databáze a soubory protokolů transakcí beze změny)|Ne|Ne|Ano|
|SQL Server nebo Exchange<br /><br />Zálohování aplikace Azure Backup Server<br /><br />BMR nebo záloha stavu systému|Ztracený server (databáze a soubory protokolu transakcí ztracené)|Ne|Ne|Ano<br /><br />Obnovení BMR následované pravidelným obnovením serveru Azure Backup Server|

## <a name="how-system-state-backup-works"></a>Způsob fungování zálohy stavu systému

Po spuštění zálohy systému backup server komunikuje se službou Zálohování serveru a požaduje zálohu stavu systému serveru. Ve výchozím nastavení používají program Backup Server a Zálohování serveru jednotku, která má nejvíce volného místa. Informace o této jednotce jsou uloženy v souboru *PSDataSourceConfig.xml.* 

Jednotku, kterou záložní server používá pro zálohování stavu systému, můžete přizpůsobit: 

1. Na chráněném serveru přejděte na *adresu C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources*. 
1. Otevřete soubor *PSDataSourceConfig.xml* pro úpravy. 
1. Změňte \<hodnotu\> FilesToProtect pro písmeno jednotky. 
1. Uložte soubor a zavřete ho. 

Pokud je skupina ochrany nastavena na ochranu systémového stavu počítače, spusťte kontrolu konzistence. Pokud je generována výstraha, pak vyberte **Změnit skupinu ochrany** ve výstraze a potom vyplňte stránky v průvodci. Pak spusťte další kontrolu konzistence.

Pokud je server ochrany v clusteru, může být jako jednotka, která má nejvíce volného místa, vybrána jednotka clusteru. Pokud je vlastnictví této jednotky přepnuto na jiný uzel a spustí se záloha stavu systému, jednotka není k dispozici a záloha se nezdaří. V tomto scénáři upravte *soubor PSDataSourceConfig.xml* tak, aby přecštodal na místní jednotku.

Program Zálohování serveru dále vytvoří složku s názvem *WindowsImageBackup* v kořenovém adresáři složky obnovení. Při vytváření zálohy systému Windows Server Backup jsou všechna data umístěna do této složky. Po dokončení zálohování je soubor přenesen do počítače zálohovacího serveru. Všimněte si následujících informací:

* Tato složka a její obsah nejsou po dokončení zálohování nebo přenosu vyčištěny. Nejlepší způsob, jak si to je, že místo je vyhrazeno pro příští dokončení zálohy.
* Složka je vytvořena pro každou zálohu. Časové a časové razítko odráží čas poslední zálohy stavu systému.

## <a name="how-bmr-backup-works"></a>Jak funguje zálohování BMR

Pro BMR (včetně zálohy stavu systému) je úloha zálohování uložena přímo do sdílené složky v počítači se záložním serverem. Není uložen do složky na chráněném serveru.

Backup Server volá program Zálohování serveru a sdílí svazek replik pro tuto zálohu BMR. V takovém případě nevyžaduje zálohování serveru používat jednotku, která má nejvíce volného místa. Místo toho používá sdílenou složku, která byla vytvořena pro úlohu.

Po dokončení zálohování je soubor přenesen do počítače zálohovacího serveru. Protokoly jsou uloženy v *c:\Windows\Logs\WindowsServerBackup*.

## <a name="prerequisites-and-limitations"></a>Požadavky a omezení

* BMR není podporováno pro počítače se systémem Windows Server 2003 nebo pro počítače s klientským operačním systémem.

* BMR a stav systému nemůžete chránit u stejného počítače v různých skupinách ochrany.

* Záložní server počítač nemůže chránit sám pro BMR.

* Krátkodobá ochrana pásky (disk na pásku nebo D2T) není pro BMR podporována. Je podporováno dlouhodobé úložiště na pásku (z disku na disk na pásku nebo D2D2T).

* Z důvodu ochrany BMR musí být program Zálohování serveru nainstalován v chráněném počítači.

* Pro ochranu BMR, na rozdíl od ochrany stavu systému, backup server nemá žádné požadavky na místo v chráněném počítači. Program Zálohování serveru přímo přenáší zálohy do počítače zálohovacího serveru. Úloha záložního přenosu se nezobrazí v zobrazení **Úlohy** záložního serveru.

* Záložní server rezervuje 30 GB místa na svazku repliky pro BMR. Toto přidělení místa můžete změnit na stránce **Přidělení disku** v Průvodci změnou skupiny ochrany. Nebo můžete použít rutiny Get-DatasourceDiskAllocation a Set-DatasourceDiskAllocation PowerShell. Na svazku bodu obnovení vyžaduje ochrana BMR přibližně 6 GB pro uchovávání pěti dnů.
  * Velikost svazku repliky nelze zmenšit na méně než 15 GB.
  * Záložní server nevypočítá velikost zdroje dat BMR. Předpokládá 30 GB pro všechny servery. Změňte hodnotu na základě velikosti záloh BMR, které očekáváte ve vašem prostředí. Můžete zhruba vypočítat velikost zálohy BMR jako součet využitého místa na všech kritických svazcích. Kritické svazky = spouštěcí svazek + systémový svazek + data o stavu systému hostování svazku, například služba Active Directory.

* Pokud změníte ochranu stavu systému na ochranu BMR, pak ochrana BMR vyžaduje méně místa na *svazku bodu obnovení*. Další místo na svazku však není regenerované. Velikost svazku můžete ručně zmenšit na stránce **Změnit přidělení disku** Průvodce změnou skupiny ochrany. Nebo můžete použít rutiny Get-DatasourceDiskAllocation a Set-DatasourceDiskAllocation PowerShell.

    Pokud změníte ochranu stavu systému na ochranu BMR, pak ochrana BMR vyžaduje více místa na *svazku repliky*. Hlasitost se automaticky vysune. Pokud chcete změnit výchozí přidělení místa, použijte rutinu Modifikovat-diskallocationr powershellu.

* Pokud změníte ochranu BMR na ochranu stavu systému, budete potřebovat více místa na svazku bodu obnovení. Záložní server se může pokusit automaticky zvýšit svazek. Pokud fond úložiště nemá dostatek místa, dojde k chybě.

    Pokud změníte ochranu BMR na ochranu stavu systému, budete potřebovat místo v chráněném počítači. Budete potřebovat prostor, protože ochrana stavu systému nejprve zapíše repliku do místního počítače a potom přenese repliku do počítače zálohovacího serveru.

## <a name="before-you-begin"></a>Než začnete

1. **Nasazení serveru Azure Backup Server**. Ověřte, zda je záložní server správně nasazen. Další informace naleznete v tématu:
    * [Požadavky na systém pro server Azure Backup Server](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Matice ochrany záložního serveru](backup-mabs-protection-matrix.md)

1. **Nastavte úložiště**. Záložní data můžete ukládat na disk, na pásku a v cloudu pomocí Azure. Další informace naleznete v [tématu Příprava úložiště dat](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

1. **Nastavte agenta ochrany**. Nainstalujte agenta ochrany do počítače, který chcete zálohovat. Další informace naleznete [v tématu Deploy the DPM protection agent](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Zálohování stavu systému a úplné obnovení systému

Zálohování stavu systému a holého kovu:

1. Chcete-li otevřít Průvodce vytvořením nové skupiny ochrany, vyberte v konzole správce záložního serveru **položku Protection** > **Actions** > **Create Protection Group**.

1. Na stránce **Vybrat typ skupiny ochrany** vyberte **servery**a pak vyberte **Další**.

1. Na stránce **Vybrat členy skupiny** rozbalte počítač a vyberte **bmr** nebo **stav systému**.

    Nezapomeňte, že pro stejný počítač v různých skupinách nelze chránit bmr i stav systému. Také když vyberete BMR, stav systému je automaticky povolena. Další informace naleznete v [tématu Deploy protection groups](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

1. Na stránce **Vybrat metodu ochrany dat** zvolte, jak zpracovat krátkodobé zálohování a dlouhodobé zálohování. 

    Krátkodobé zálohování je vždy na disku jako první, s možností zálohování z disku do Azure pomocí Azure Backup (krátkodobé nebo dlouhodobé). Alternativou k dlouhodobému zálohování do cloudu je nastavení dlouhodobého zálohování do samostatného páskového zařízení nebo páskové knihovny, která je připojena k zálohovacímu serveru.

1. Na stránce **Vybrat krátkodobé cíle** zvolte, jak zálohovat krátkodobé úložiště na disku:
    * V **případě rozsahu uchování**zvolte, jak dlouho mají být data na disku uchována.
    * V **případě četnosti synchronizace**zvolte, jak často se má přírůstkové zálohování na disk spouštět. Pokud nechcete nastavit interval zálohování, můžete vybrat **těsně před bodem obnovení**. Záložní server spustí expresní úplnou zálohu těsně před plánovaným každým bodem obnovení.

1. Pokud chcete data ukládat na pásku pro dlouhodobé ukládání, pak na **stránce Zadat dlouhodobé cíle** zvolte, jak dlouho chcete data pásky uchovávat (1 až 99 let).
    1. V **části Frekvence zálohování**zvolte, jak často se má zálohování spouštět na pásku. Frekvence je založena na zvoleném rozsahu uchování:
        * Pokud je rozsah retencí 1 až 99 let, můžete zálohovat denně, týdně, dvakrát týdně, měsíčně, čtvrtletně, pololetně nebo ročně.
        * Pokud je rozsah retencí 1 až 11 měsíců, můžete zálohovat denně, týdně, dvakrát týdně nebo měsíčně.
        * Když retenční rozsah je 1 až 4 týdny, můžete zálohovat denně nebo týdně.

    1. Na stránce **Vybrat podrobnosti pásky a knihovny** vyberte pásku a knihovnu, kterou chcete použít. Zvolte také, zda mají být data komprimována a šifrována.

1. Na stránce **Zkontrolovat přidělení disku** zkontrolujte místo na disku fondu úložiště, které je k dispozici pro skupinu ochrany.

    * **Celková velikost dat** je velikost dat, která chcete zálohovat.
    * **Místo na disku, které má být zřízeno na Serveru Zálohování Azure,** je místo, které zálohovací server doporučuje pro skupinu ochrany. Backup Server používá tato nastavení k výběru ideálního záložního svazku. Možnosti svazku zálohování můžete upravit v **podrobnostech o přidělení disku**.
    * Pro úlohy vyberte v rozevírací nabídce upřednostňované úložiště. Úpravy, které provedete, změní hodnoty v polích **Celková velikost úložiště** a **Volný úložný prostor** v podokně **Dostupný úložný prostor na disku**. Nezřízené místo je velikost úložiště, které zálohovací server navrhuje přidat do svazku, abyste zajistili hladké zálohování.

1. Na stránce **Zvolit metodu vytvoření repliky** vyberte způsob zpracování počáteční replikace celých dat. 

    Pokud se rozhodnete replikovat v síti, doporučujeme zvolit čas mimo špičku. U velkého množství dat nebo pro síťové podmínky, které jsou méně než optimální, zvažte replikaci dat offline pomocí vyměnitelného média.

1. Na stránce **Zvolit možnosti kontroly konzistence** vyberte způsob automatizace kontrol konzistence. 

    Kontrolu můžete spustit pouze v případě, že data repliky budou nekonzistentní nebo podle plánu. Pokud nechcete konfigurovat automatickou kontrolu konzistence, můžete kdykoli spustit ruční kontrolu. Chcete-li spustit ruční kontrolu, klepněte v oblasti **Protection** konzole Backup Server Administrator Console pravým tlačítkem myši na skupinu ochrany a vyberte příkaz **Provést kontrolu konzistence**.

1. Pokud jste se rozhodli zálohovat do cloudu pomocí Azure Backup, na stránce **Zadat data ochrany online** vyberte úlohy, které chcete zálohovat do Azure.

1. Na stránce **Zadejte plán zálohování online** vyberte, jak často se má postupně zálohovat do Azure. 

    Zálohy můžete naplánovat tak, aby se spouštěla každý den, týden, měsíc a rok. Můžete také vybrat čas a datum, kdy mají být zálohy spuštěny. Zálohování se může spouštět až dvakrát denně. Při každém spuštění zálohy se v Azure vytvoří bod obnovení dat z kopie záložních dat, která jsou uložená na disku zálohovacího serveru.

1. Na stránce **Určit zásady uchovávání informací online** vyberte, jak se budou v Azure uchovávat body obnovení vytvořené z denního, týdenního, měsíčního a ročního zálohování.

1. Na stránce **Zvolit online replikaci** vyberte, jak dojde k počáteční úplné replikaci dat. 

    Můžete replikovat přes síť nebo zálohovat offline (offline osevní). Offline záloha používá funkci Import Azure. Další informace najdete [v tématu Offline pracovní postup zálohování v Azure Backup](offline-backup-azure-data-box.md).

1. Na stránce **Souhrn** zkontrolujte nastavení. Po výběru **možnosti Vytvořit skupinu**dojde k počáteční replikaci dat. Po dokončení replikace dat je na stránce **Stav** stav stav skupiny ochrany **v pořádku**. Zálohy se pak dějí podle nastavení skupiny ochrany.

## <a name="recover-system-state-or-bmr"></a>Obnovení stavu systému nebo BMR

Stav systému nebo BMR můžete obnovit do síťového umístění. Pokud jste zálohovali BMR, potom použijte prostředí Windows Recovery Environment (WinRE) ke spuštění systému a jeho připojení k síti. Potom pomocí zálohování Windows Serveru (WSB) proveďte obnovení ze síťového umístění. Pokud jste zálohovali stav systému, stačí použít program Zálohování serveru k obnovení ze síťového umístění.

### <a name="restore-bmr"></a>Obnovit BMR

Spuštění obnovení v počítači se záložním serverem:

1. V podokně **obnovení** vyhledejte počítač, který chcete obnovit. Pak vyberte **položku Holé obnovení kovu**.

1. Dostupné body obnovení jsou v kalendáři uvedené tučným písmem. Vyberte datum a čas bodu obnovení, který chcete použít.

1. Na stránce **Vybrat typ obnovení** vyberte Kopírovat do síťové **složky**.

1. Na stránce **Zadat cíl** vyberte cíl zkopírovaných dat. 

    Nezapomeňte, že cíl musí mít dostatek místa pro data. Doporučujeme vytvořit novou složku pro cíl.

1. Na stránce **Zadat možnosti obnovení** vyberte nastavení zabezpečení. Pak vyberte, zda chcete použít snímky hardwaru založené na síti SAN (Storage Area Network), pro rychlejší obnovení. Tato možnost je k dispozici pouze v případě, že:
    * Máte san, který poskytuje tuto funkci. 
    * Klon můžete vytvořit a rozdělit tak, aby byl zapisovatelný.
    * Chráněný počítač a počítač záložního serveru jsou připojeny ke stejné síti.

1. Nastavte možnosti oznámení. 
1. Na stránce **Potvrzení** vyberte **Obnovit**.

Nastavení umístění sdílené položky:

1. V umístění obnovení přejděte do složky, která má zálohu.

1. Sdílejte složku, která je o jednu úroveň vyšší než *WindowsImageBackup,* takže kořensdílené složky je složka *WindowsImageBackup.* 

    Pokud tuto složku nesdílíte, obnovení zálohu nenajde. Chcete-li se připojit pomocí funkce WinRE, potřebujete sdílenou složku, ke které máte přístup ve winre se správnou IP adresou a pověřeními.

Obnovení systému:

1. Spusťte počítač, ve kterém chcete obnovit bitovou kopii pomocí disku DVD se systémem Windows pro systém, který obnovujete.

1. Na první stránce ověřte nastavení jazyka a národního prostředí. Na stránce **Instalace** vyberte **Možnost Opravit počítač**.

1. Na stránce **Možnosti obnovení systému** vyberte **Obnovit počítač pomocí bitové kopie systému, kterou jste vytvořili dříve**.

1. Na stránce **Vybrat zálohu bitových obrázků systému** vyberte **vybrat bitovou kopii** > systému**Rozšířené** > **hledání bitové kopie systému v síti**. Pokud se zobrazí upozornění, vyberte **Ano**. Přejděte na cestu sdílení, zadejte pověření a vyberte bod obnovení. Systém hledá konkrétní zálohy, které jsou k dispozici v tomto bodě obnovení. Vyberte bod obnovení, který chcete použít.

1. Na stránce **Zvolit, jak obnovit zálohování,** vyberte **Formát a přerozdělit disky**. Na další stránce ověřte nastavení.

1. Chcete-li zahájit obnovení, vyberte **možnost Dokončit**. Je vyžadováno restartování.

### <a name="restore-system-state"></a>Obnovení stavu systému

Spuštění obnovení v zálohovacím serveru:

1. V podokně **Obnovení** vyhledejte počítač, který chcete obnovit, a vyberte **položku Holé obnovení .**

1. Dostupné body obnovení jsou v kalendáři uvedené tučným písmem. Vyberte datum a čas bodu obnovení, který chcete použít.

1. Na stránce **Vybrat typ obnovení** vyberte Kopírovat do síťové **složky**.

1. Na stránce **Zadat cíl** vyberte, kam se mají data kopírovat. 

    Nezapomeňte, že vybraný cíl musí mít dostatek místa pro data. Doporučujeme vytvořit novou složku pro cíl.

1. Na stránce **Zadat možnosti obnovení** vyberte nastavení zabezpečení. Pak vyberte, zda chcete použít snímky hardwaru založené na systému SAN pro rychlejší obnovení. Tato možnost je k dispozici pouze v případě, že: 
    * Máte san, který poskytuje tuto funkci.
    * Klon můžete vytvořit a rozdělit tak, aby byl zapisovatelný. 
    * Chráněný počítač a server backup serveru jsou připojeny ke stejné síti.

1. Nastavte možnosti oznámení. 
1. Na stránce **Potvrzení** vyberte **Obnovit**.

Spuštění programu Zálohování serveru:

1. Vyberte **možnost Akce** > **Obnovit** > **tento server** > **Další**.

1. Vyberte **Jiný server**, vyberte stránku **Zadat typ umístění** a pak vyberte **Vzdálená sdílená složka**. Zadejte cestu ke složce, která obsahuje bod obnovení.

1. Na stránce **Vybrat typ obnovení** vyberte stav **systému**.

1. Na stránce **Vybrat umístění pro obnovení stavu systému** vyberte původní **umístění**.

1. Na stránce **Potvrzení** vyberte **Obnovit**. 

1. Po obnovení restartujte server.

Obnovení stavu systému můžete spustit také na příkazovém řádku: 

1. Spusťte program Zálohování serveru v počítači, který chcete obnovit. 

1. Chcete-li získat identifer verze, zadejte na příkazovém řádku:

   ```wbadmin get versions -backuptarget \<servername\sharename\>```

1. Pomocí identifikátoru verze spusťte obnovení stavu systému. Na příkazovém řádku zadejte: 

    ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

1. Potvrďte, že chcete spustit obnovení. Proces můžete vidět v okně příkazového řádku. Vytvoří se protokol obnovení. 

1. Po obnovení restartujte server.
