---
title: Azure Backup Server chrání stav systému a obnoví holý počítač
description: Použijte Azure Backup Server k zálohování stavu systému a zajištění ochrany úplné obnovení systému (BMR).
services: backup
author: markgalioto
manager: carmonm
keywords: ''
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: markgal
ms.openlocfilehash: d35f8667cb1ca9a0b3abd08450ebc647d6d12276
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607204"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Zálohování stavu systému a obnovení na holý počítač pomocí serveru Azure Backup

Azure Backup Server zálohuje stav systému a poskytuje ochranu úplné obnovení systému (BMR).

*   **Zálohování stavu systému**: zálohuje soubory operačního systému, takže můžete obnovit, pokud se počítač spustí, ale systém souborů a registr budou ztraceny. Zahrnuje zálohu stavu systému:
    * Člen domény: spouštěcí soubory, registrační databáze třídy modelu COM +, registru
    * Řadič domény: Windows Server Active Directory (NTDS), spouštěcí soubory, registrační databáze třídy modelu COM +, registru, systémový svazek (SYSVOL)
    * Počítač, který spouští služby clusteru: metadata clusterového serveru
    * Počítač, který spouští služby certificate services: certifikát dat
* **Úplné zálohování**: zálohuje soubory operačního systému a všechna data na nepostradatelné svazky (s výjimkou uživatelských dat). Podle definice zálohování BMR zahrnuje zálohu stavu systému. Když se počítač nespustí a je nutné obnovit vše poskytuje ochranu.

Následující tabulka shrnuje, co můžete zálohovat a obnovit. Podrobné informace o verzích aplikace, které se dají chránit pomocí stavu systému a úplné obnovení systému najdete v tématu [jaké serveru Azure Backup zálohuje?](backup-mabs-protection-matrix.md).

|Backup|Problém|Obnovení ze zálohy Azure Backup Server|Obnovení ze zálohy stavu systému|ÚPLNÉ OBNOVENÍ SYSTÉMU|
|----------|---------|---------------------------|------------------------------------|-------|
|**Data souborů**<br /><br />Zálohování regulární dat<br /><br />Zálohu BMR nebo stavu|Soubor ke ztrátě dat|Ano|Ne|Ne|
|**Data souborů**<br /><br />Azure Backup Server zálohování dat souborů<br /><br />Zálohu BMR nebo stavu|Ztraceného nebo poškozeného operačního systému|Ne|Ano|Ano|
|**Data souborů**<br /><br />Azure Backup Server zálohování dat souborů<br /><br />Zálohu BMR nebo stavu|Ztráta serveru (datové svazky v pořádku)|Ne|Ne|Ano|
|**Data souborů**<br /><br />Azure Backup Server zálohování dat souborů<br /><br />Zálohu BMR nebo stavu|Ztráta serveru (datové svazky ztraceny)|Ano|Ne|Ano (BMR následované pravidelným obnovením zálohovaných souborová data)|
|**Data služby SharePoint**:<br /><br />Zálohování Azure záložní Server farmy dat<br /><br />Zálohu BMR nebo stavu|Ke ztrátě lokality, seznamy, položky seznamu, dokumenty|Ano|Ne|Ne|
|**Data služby SharePoint**:<br /><br />Zálohování Azure záložní Server farmy dat<br /><br />Zálohu BMR nebo stavu|Ztraceného nebo poškozeného operačního systému|Ne|Ano|Ano|
|**Data služby SharePoint**:<br /><br />Zálohování Azure záložní Server farmy dat<br /><br />Zálohu BMR nebo stavu|Zotavení po havárii|Ne|Ne|Ne|
|Windows Server 2012 R2 Hyper-V<br /><br />Zálohování Azure zálohování serveru hostitele Hyper-V nebo hosta<br /><br />Zálohování BMR nebo systému stav hostitele|Ke ztrátě virtuálních počítačů|Ano|Ne|Ne|
|Hyper-V<br /><br />Zálohování Azure zálohování serveru hostitele Hyper-V nebo hosta<br /><br />Zálohování BMR nebo systému stav hostitele|Ztraceného nebo poškozeného operačního systému|Ne|Ano|Ano|
|Hyper-V<br /><br />Zálohování Azure zálohování serveru hostitele Hyper-V nebo hosta<br /><br />Zálohování BMR nebo systému stav hostitele|Ke ztrátě hostitele Hyper-V (virtuálních počítačů beze změn)|Ne|Ne|Ano|
|Hyper-V<br /><br />Zálohování Azure zálohování serveru hostitele Hyper-V nebo hosta<br /><br />Zálohování BMR nebo systému stav hostitele|Ke ztrátě hostitele Hyper-V (virtuální počítače ke ztrátě)|Ne|Ne|Ano<br /><br />BMR, následované pravidelným obnovením serveru Azure Backup|
|SQL Server nebo Exchange<br /><br />Azure zálohování aplikace Zálohování serveru<br /><br />Zálohu BMR nebo stavu|Data ke ztrátě aplikací|Ano|Ne|Ne|
|SQL Server nebo Exchange<br /><br />Azure zálohování aplikace Zálohování serveru<br /><br />Zálohu BMR nebo stavu|Ztraceného nebo poškozeného operačního systému|Ne|Y|Ano|
|SQL Server nebo Exchange<br /><br />Azure zálohování aplikace Zálohování serveru<br /><br />Zálohu BMR nebo stavu|Ztráta serveru (protokoly databáze nebo transakcí beze změn)|Ne|Ne|Ano|
|SQL Server nebo Exchange<br /><br />Azure zálohování aplikace Zálohování serveru<br /><br />Zálohu BMR nebo stavu|Ztráta serveru (protokoly transakcí databáze nebo ke ztrátě)|Ne|Ne|Ano<br /><br />Obnovení BMR následované pravidelným obnovením serveru Azure Backup|

## <a name="how-system-state-backup-works"></a>Jak funguje zálohování stavu systému

Při spuštění zálohy stavu systému, Backup Server komunikuje se zálohování serveru o zálohu stavu systému serveru. Ve výchozím nastavení zálohování serveru a zálohování serveru používají na jednotku, která má nejvíce dostupného volného místa. Informace o této jednotky se ukládají do soubor PSDataSourceConfig.xml. Toto je na jednotku, která používá zálohování serveru k zálohování.

Můžete přizpůsobit na jednotku, která používá záložní Server pro zálohování stavu systému. Na chráněném serveru přejděte na C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources. Otevřete soubor PSDataSourceConfig.xml pro úpravy. Změna \<FilesToProtect\> hodnotu pro písmeno jednotky. Uložte soubor a zavřete ho. Pokud je sada skupiny ochrany k ochraně stav systému počítače, spusťte kontrolu konzistence. Pokud je vygenerována výstraha, vyberte **upravit skupinu ochrany** ve výstraze a pak dokončete průvodce. Pak spusťte další kontrolu konzistence.

Všimněte si, že pokud ochranný server v clusteru, je možné, že disk klastru bude zvolen jako jednotku s největším místem na. Pokud změně vlastnictví této jednotky byla přepnuta na jiný uzel a spuštění zálohy stavu systému, jednotka není k dispozici a zálohování se nezdaří. V tomto scénáři upravte soubor PSDataSourceConfig.xml a nasměrovat na místní disk.

V dalším kroku zálohování serveru vytvoří složku s názvem WindowsImageBackup v kořenové složce obnovení. Jako Zálohování serveru Windows vytváření zálohy se všechna data je umístěn v této složce. Po dokončení zálohování se soubor přenese k počítači zálohování serveru. Všimněte si následujících informací:

* Tato složka a její obsah nejsou vyčistit až po dokončení zálohování nebo přenos. Nejlepší způsob, jak nad tímto problémem přemýšlet je, že pro příští zálohy je dokončena dochází k rezervaci místo.
* Vytvoření složky se pokaždé, když se provádí zálohu. Razítko času a data reflektovat čas poslední zálohy stavu systému.

## <a name="bmr-backup"></a>Zálohování BMR

Pro BMR (včetně zálohu stavu systému) úloha zálohování uložit přímo do sdílené složky v počítači zálohování serveru. Není uložit do složky na chráněném serveru.

Zálohování serveru volá zálohování serveru a sdílí svazek repliky pro tuto zálohu BMR. V takovém případě neříká zálohování Windows serveru, aby použila jednotku s nejvíce volného místa. Místo toho používá sdílenou složku, která byla vytvořena pro úlohu.

Po dokončení zálohování se soubor přenese k počítači zálohování serveru. Protokoly se ukládají do C:\Windows\Logs\WindowsServerBackup.

## <a name="prerequisites-and-limitations"></a>Požadavky a omezení

-   Úplné obnovení systému není podporována pro počítače se systémem Windows Server 2003 nebo pro počítače s operačním systémem klienta.

-   Nelze chránit BMR a stav systému pro stejný počítač v různých skupinách ochrany.

-   Zálohování serveru počítač nemůže chránit sám sebe pro BMR.

-   Krátkodobá ochrana na pásku (disk na pásku nebo D2T) není u BMR podporována. Dlouhodobé ukládání na pásku (disku na disk na pásku nebo D2D2T) je podporováno.

-   Pro ochranu BMR zálohování serveru musí být nainstalován na chráněném počítači.

-   Pro ochranu BMR na rozdíl od ochrany stavu systému, zálohovat Server nemá žádné požadavky na místo v chráněném počítači. Zálohování serveru přímo přenáší zálohy k počítači zálohování serveru. Úloha zálohování přenosu se nezobrazí v zálohování serveru **úlohy** zobrazení.

-   Zálohování serveru rezervuje pro BMR 30 GB místa na svazku repliky. Toto můžete změnit na **přidělení disku** v Průvodci změnou skupiny ochrany nebo pomocí rutiny Get-DatasourceDiskAllocation a Set-DatasourceDiskAllocation prostředí PowerShell. Na svazku bodu obnovení vyžaduje ochrana BMR přibližně 6 GB pro uchování 5 dní.
    * Všimněte si, že nemůžete snížit velikost svazku repliky na méně než 15 GB.
    * Zálohování serveru nepočítá velikost zdroje dat BMR. Předpokládá 30 GB pro všechny servery. Změňte hodnotu podle velikosti záloh BMR, které byste očekávali ve vašem prostředí. Velikost zálohy BMR může být přibližně počítá jako součet využitého místa na všech klíčových svazcích. Nepostradatelné svazky = spouštěcí svazek + systémový svazek + svazek, který je hostitelem dat stavu systému, jako je Active Directory.

-   Pokud změníte z ochrany stavu systému na ochranu BMR, ochrana BMR vyžaduje méně místa na *svazek bodu obnovení*. Volné místo na svazku se však neuvolní. Velikost svazku můžete zmenšit ručně na **upravit přidělení disku** Průvodce změnou skupiny ochrany nebo pomocí rutiny Get-DatasourceDiskAllocation a Set-DatasourceDiskAllocation prostředí PowerShell.

    Pokud změníte z ochrany stavu systému na ochranu BMR, ochrana BMR vyžaduje více místa na *svazek repliky*. Svazek je automaticky rozšířena. Pokud chcete změnit výchozí přidělení prostoru, použijte rutinu prostředí PowerShell Modify-DiskAllocation.

-   Pokud změníte z ochrany BMR na ochranu stavu systému, je třeba více místa na svazku bodu obnovení. Zálohování serveru se může pokusit automaticky zvětšit svazek. Pokud ve fondu úložiště není dostatek místa, dojde k chybě.

    Pokud změníte z ochrany BMR na ochranu stavu systému, musíte místo v chráněném počítači. Je to proto ochrany stavu systému nejprve zapíše repliku do místního počítače a pak ji přesune k počítači zálohování serveru.

## <a name="before-you-begin"></a>Než začnete

1.  **Nasadit Azure Backup Server**. Ověřte, zda je správně nasazeny zálohování serveru. Další informace naleznete v tématu:
    * [Požadavky na systém pro Azure Backup Server](http://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Matice ochrany zálohování serveru](backup-mabs-protection-matrix.md)

2.  **Nastavení úložiště**. Zálohovaná data můžete ukládat na disk na pásku a v cloudu s Azure. Další informace najdete v tématu [Příprava úložiště dat](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

3.  **Nastavte agenta ochrany**. Nainstalujte agenta ochrany na počítači, který chcete zálohovat. Další informace najdete v tématu [nasadit agenta ochrany DPM](http://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Zálohování stavu systému a úplné obnovení
Nastavit skupinu ochrany, jak je popsáno v [nasazení skupin ochrany](http://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups). Všimněte si, že nemůžete chránit BMR a stav systému pro stejný počítač v různých skupinách. Navíc když vyberete BMR, stav systému je automaticky povolené.


1.  Chcete-li spustit Průvodce vytvořením nové skupiny ochrany v konzoli správce zálohování serveru, vyberte **ochrany** > **akce** > **vytvořením skupiny ochrany** .

2.  Na **vybrat typ skupiny ochrany** vyberte **servery**a potom vyberte **Další**.

3.  Na **vybrat členy skupiny** stránky, rozbalte položku počítače a pak vyberte buď **BMR** nebo **stav systému**.

    Mějte na paměti, že nemůžete chránit jak BMR a stav systému pro stejný počítač v různých skupinách. Navíc když vyberete BMR, stav systému je automaticky povolené. Další informace najdete v tématu [nasazení skupin ochrany](http://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

4.  Na **vyberte způsob ochrany dat** vyberte, jakým způsobem chcete zpracovávat krátkodobé a dlouhodobé zálohování. Krátkodobé zálohování je vždy první disk, s možností zálohování z disku Azure v cloudu pomocí Azure Backup (krátkodobá nebo dlouhodobá). Alternativu k dlouhodobé zálohování do cloudu je nastavit dlouhodobé zálohování na samostatné pásky nebo zařízení knihovny pásků připojený k zálohování serveru.

5.  Na **vybrat krátkodobé cíle** vyberte, jakým způsobem chcete zálohování na krátkodobé úložiště na disku:
    1. Pro **rozsah uchování**, vyberte, jak dlouho chcete zachovat data na disku. 
    2. Pro **četnost synchronizace**, vyberte, jak často chcete spustit přírůstkové zálohování na disk. Pokud nechcete nastavit interval zálohování, můžete zkontrolovat **těsně před bodem obnovení** možnost. Zálohování serveru se spustí expresní úplné zálohování těsně před každým bodem obnovení je naplánováno.

6.  Pokud chcete uložit data na pásku pro dlouhodobé uložení na **zadat dlouhodobé cíle** vyberte, jak dlouho chcete zachovat data na pásce (1 – 99 let). 
    1. Pro **četnost záloh**, vyberte, jak často zálohování na pásku má spustit. Frekvence je založena na rozsahu uchování, kterou jste vybrali:
        * Když je rozsah uchování 1 – 99 let, můžete zvolit vytváření záloh každý denně, týdně, jednou za dva týdny, měsíčně, čtvrtletně, půl roku nebo každý rok.
        * Když je rozsah uchování 1 – 11 měsíců, můžete zvolit vytváření záloh každý den, každý týden, každých čtrnáct dní nebo každý měsíc.
        * Když je rozsah uchování 1 – 4 týdny, můžete zvolit vytváření záloh každý den nebo každý týden.

    2. Na **vyberte pásku a podrobnosti ke knihovně** vyberte pásku a knihovnu používat, a zda má komprimovaná a šifrovaná data.

7.  Na **zkontrolovat přidělení disku** si prohlédněte místo disku fondu úložiště, který je přidělen pro skupinu ochrany.

    1. **Celková velikost dat** je velikost dat, které chcete zálohovat.
    2. **Chcete-li být zřízena na serveru Azure Backup místa na disku** je místo, které doporučuje zálohovat Server pro skupinu ochrany. Zálohování serveru zvolí ideální zálohování svazku, na základě nastavení. Však můžete upravit možnosti zálohování svazku v **podrobnosti přidělení disku**. 
    3. Pro úlohy v rozevírací nabídce vyberte upřednostňovaný úložiště. Vaše úpravy změnit hodnoty nastavení **celkové úložiště** a **volného** v **úložiště k dispozici disku** podokně. Underprovisioned prostor je velikost úložiště, který Backup Server naznačuje, že přidáte do svazku, ujistěte se, technologie smooth zálohy.

8.  Na **vyberte způsob vytvoření repliky** vyberte, jakým způsobem chcete zpracovat počáteční úplná data replikace. Pokud se rozhodnete replikaci přes síť, doporučujeme, abyste zvolili dobu mimo špičku. Pro velké objemy dat nebo síťové podmínky, které jsou menší než optimální zvažte replikaci dat offline pomocí vyměnitelného média.

9. Na **zvolte možnosti kontroly konzistence** vyberte, jak chcete automatizovat kontroly konzistence. Můžete spustit kontrolu jenom v případě, že data repliky stane nekonzistentní, nebo podle plánu. Pokud nechcete konfigurovat automatickou kontrolu konzistence, můžete kdykoli spustit ruční kontrolu. Chcete-li spustit ruční kontrolu, ve **ochrany** konzoly Správce serveru zálohování, klikněte pravým tlačítkem na skupinu ochrany a potom vyberte **provést kontrolu konzistence**.

10. Pokud jste vybrali pro zálohování do cloudu pomocí Azure Backup **zadat Data Online ochrany** se ujistěte, že jste vybrali úlohy, které chcete zálohovat do Azure.

11. Na **zadejte plán Online zálohování** stránky, dojde k vyberte jak často přírůstkové zálohování do Azure. Můžete naplánovat zálohování spouštět každý den, týden, měsíc a rok, a vyberte čas a datum, na kterých by měly být spuštěny. Zálohování může dojít, až dvakrát za den. Při každém spuštění zálohy dat vytvoří bod obnovení je v Azure z kopie zálohovaná data uložená na disku zálohování serveru.

12. Na **zadejte zásady uchovávání Online** vyberte, jak se uchovají body obnovení vytvořené ze zálohy denní, týdenní, měsíční a roční v Azure.

13. Na **výběr Online replikace** vyberte, jak proběhne počáteční úplná replikace dat. Můžete replikovat přes síť nebo proveďte offline zálohování (offline synchronizace replik indexů). Zálohování offline používá funkce Import úložiště Azure. Další informace najdete v tématu [pracovní postup Offline zálohování v Azure Backup](backup-azure-backup-import-export.md).

14. Na **Souhrn** stránka, zkontrolujte nastavení. Po výběru **vytvořit skupinu**, dojde k počáteční replikaci dat. Po replikaci dat dokončení, na **stav** stránky, je stav skupiny ochrany **OK**. Potom se provede na ochranu nastavení skupiny.

## <a name="recover-system-state-or-bmr"></a>Obnovení stavu systému nebo úplné obnovení systému
Můžete obnovit BMR nebo stavu systém do umístění v síti. Pokud jste zálohovali BMR, použijte prostředí Windows Recovery Environment (WinRE) ke spuštění systému a připojit k síti. Pak pomocí zálohování serveru k obnovení z umístění v síti. Pokud jste zálohovali stav systému, použijte právě zálohování serveru k obnovení z umístění v síti.

### <a name="restore-bmr"></a>Obnovit BMR
Spusťte obnovení na počítači zálohování serveru:

1.  V **obnovení** , najít počítače, kterou chcete obnovit a pak vyberte **úplné obnovení systému**.

2.  Dostupné body obnovení jsou vypsány v kalendáři tučně. Vyberte datum a čas pro bod obnovení, který chcete použít.

3.  Na **vybrat typ obnovení** vyberte **kopírovat do síťové složky.**

4.  Na **zadejte cíl** vyberte, kam chcete zkopírovat data, která mají. Mějte na paměti, že vybraný cíl musí mít dostatek místa. Doporučujeme vám, že vytvoříte novou složku.

5.  Na **zadat možnosti obnovení** vyberte nastavení zabezpečení pro použití. Potom vyberte, zda chcete použít (SAN) storage area network – na základě snímků hardwaru pro rychlejší obnovení. (Toto je možnost, pouze v případě, že máte síť SAN se tato funkce je k dispozici a možnost vytvářet a rozdělovat je tak aby zapisovatelné. Kromě toho chráněného počítače a počítač zálohování serveru musí být připojen ke stejné síti.)

6.  Nastavte možnosti oznámení. Na **potvrzení** vyberte **obnovit**.

Nastavte umístění sdílené složky:

1.  V umístění pro obnovení přejděte do složky, která má zálohování.

2.  Sdílenou složku, která je jednu úroveň nad souborem WindowsImageBackup tak, aby kořenovém adresáři sdílené složky nacházela složka WindowsImageBackup. Pokud to neuděláte, obnovení zálohu nenalezne. Chcete-li připojit pomocí prostředí Windows Recovery Environment (WinRE), je třeba sdílené složky, která se zobrazí v prostředí WinRE se správnou IP adresu a přihlašovací údaje.

Obnovení systému:

1.  Spusťte počítač, na který chcete obnovit bitovou kopii pomocí disku DVD systému Windows pro systém, které obnovujete.

2.  Na první stránce ověřte nastavení jazyka a národního prostředí. Na **nainstalovat** vyberte **opravit tento počítač**.

3.  Na **možnosti obnovení systému** vyberte **obnovení počítače pomocí bitové kopie systému, který jste vytvořili dříve**.

4.  Na **výběr zálohy bitové kopie systému** vyberte **Vybrat bitovou kopii systému** > **Upřesnit** > **hledat bitovou kopii systému v síti**. Pokud se zobrazí upozornění, vyberte **Ano**. Přejděte cestu ke sdílené složce, zadejte přihlašovací údaje a potom vyberte bod obnovení. Tato funkce proskenuje pro konkrétní zálohování, které jsou k dispozici v daném bodě obnovení. Vyberte bod obnovení, který chcete použít.

5.  Na **Vvybrat způsob obnovení zálohy** vyberte **formátovat a znovu rozdělit disky**. Na další stránce ověřte nastavení. 

6.  Chcete-li zahájit obnovení, vyberte **Dokončit**. Je vyžadováno restartování.

### <a name="restore-system-state"></a>Obnovení stavu systému

Spuštění obnovení zálohování serveru:

1.  V **obnovení** , najít počítače, který chcete obnovit a pak vyberte **úplné obnovení systému**.

2.  Dostupné body obnovení jsou vypsány v kalendáři tučně. Vyberte datum a čas pro bod obnovení, který chcete použít.

3.  Na **vybrat typ obnovení** vyberte **kopírovat do síťové složky**.

4.  Na **zadejte cíl** vyberte, kam chcete zkopírovat data. Mějte na paměti, že vybrané cílové vyžaduje dostatek místa. Doporučujeme vám, že vytvoříte novou složku.

5.  Na **zadat možnosti obnovení** vyberte nastavení zabezpečení pro použití. Potom vyberte, zda chcete použít snímky hardwaru založené na síti SAN pro rychlejší obnovení. (Toto je možnost, pouze v případě, že máte síť SAN s Tato funkce a možnosti vytvoření a rozdělovat je tak aby zapisovatelné. Kromě toho chráněným počítačem a zálohování serveru musí být připojen ke stejné síti.)

6.  Nastavte možnosti oznámení. Na **potvrzení** vyberte **obnovit**.

Spusťte zálohování serveru:

1.  Vyberte **akce** > **obnovit** > **tento Server** > **Další**.

2.  Vyberte **jiný Server**, vyberte **zadat typ umístění** a pak vyberte **vzdálené sdílené složce**. Zadejte cestu ke složce, která obsahuje bod obnovení.

3.  Na **vybrat typ obnovení** vyberte **stav systému**. 

4. Na **vyberte umístění pro obnovení stavu systému** vyberte **původního umístění**.

5.  Na **potvrzení** vyberte **obnovit**. Po obnovení restartujte server.

6.  Také můžete spustit obnovení stavu systému na příkazovém řádku. K tomu spusťte zálohování serveru v počítači, který chcete obnovit. Chcete-li získat identifikátor verze, na příkazovém řádku, zadejte: ```wbadmin get versions -backuptarget \<servername\sharename\>```

    Identifikátor verze slouží ke spuštění obnovení stavu systému. Na příkazovém řádku zadejte: ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Potvrďte, že chcete spustit obnovení. Zobrazí se v procesu v okně příkazového řádku. Vytvoří se protokol obnovení. Po obnovení restartujte server.

