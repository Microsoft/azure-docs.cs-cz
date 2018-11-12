---
title: Azure Backup Server chrání stav systému a obnoví do holých počítačů
description: Použití Azure Backup serveru k zálohování stavu systému a zajistit ochranu úplným obnovením (BMR).
services: backup
author: markgalioto
manager: carmonm
keywords: ''
ms.service: backup
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: markgal
ms.openlocfilehash: 7cb87847d6a1e191fb20dfa9cdf263066704eb6d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238807"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Zálohování stavu systému a obnovit do holých počítačů pomocí Azure Backup serveru

Azure Backup Server zálohuje stav systému a poskytuje ochranu úplné obnovení systému (BMR).

*   **Zálohování stavu systému**: zálohuje soubory operačního systému, tak můžete obnovit při spuštění počítače, ale systém souborů a registr se ztratí. Zahrnuje i zálohu stavu systému:
    * Člen domény: spouštěcí soubory, registrační databáze třídy modelu COM +, registr
    * Řadič domény: Windows Server Active Directory (NTDS), spouštěcí soubory, registrační databáze třídy modelu COM +, registr, systémový svazek (SYSVOL)
    * Počítač, na kterém běží služba cluster services: metadata clusterového serveru
    * Počítač, který spouští služby certificate services: data certifikátu
* **Úplné zálohování**: zálohuje soubory operačního systému a všechna data na klíčové svazky (s výjimkou uživatelských dat). Podle definice zahrnuje BMR i zálohu stavu systému. Poskytuje ochranu, když se počítač nespustí a je nutné obnovit vše.

Následující tabulka shrnuje možnosti zálohování a obnovení. Podrobné informace o verzích aplikací, které se dají chránit pomocí stavu systému a BMR, najdete v části [čemu zálohování Azure Backup serveru?](backup-mabs-protection-matrix.md).

|Backup|Problém|Obnovení ze zálohy Azure Backup serveru|Obnovení ze zálohy stavu systému|ÚPLNÉ OBNOVENÍ SYSTÉMU|
|----------|---------|---------------------------|------------------------------------|-------|
|**Data souborů**<br /><br />Zálohování běžných dat<br /><br />BMR nebo záloha stavu systému|Ztracená data souborů|Ano|Ne|Ne|
|**Data souborů**<br /><br />Azure Backup Server zálohování dat souborů<br /><br />BMR nebo záloha stavu systému|Ztracený nebo poškozený operační systém|Ne|Ano|Ano|
|**Data souborů**<br /><br />Azure Backup Server zálohování dat souborů<br /><br />BMR nebo záloha stavu systému|Ztráta serveru (datové svazky v pořádku)|Ne|Ne|Ano|
|**Data souborů**<br /><br />Azure Backup Server zálohování dat souborů<br /><br />BMR nebo záloha stavu systému|Ztráta serveru (datové svazky ztracené)|Ano|Ne|Ano (BMR následované pravidelným obnovením zálohovaných souborová data)|
|**Data Sharepointu**:<br /><br />Azure Backup Server zálohování dat farmy<br /><br />BMR nebo záloha stavu systému|Ztracený web, seznamy, položky seznamu, dokumenty|Ano|Ne|Ne|
|**Data Sharepointu**:<br /><br />Azure Backup Server zálohování dat farmy<br /><br />BMR nebo záloha stavu systému|Ztracený nebo poškozený operační systém|Ne|Ano|Ano|
|**Data Sharepointu**:<br /><br />Azure Backup Server zálohování dat farmy<br /><br />BMR nebo záloha stavu systému|Zotavení po havárii|Ne|Ne|Ne|
|Windows Server 2012 R2 Hyper-V<br /><br />Zálohování Azure Backup Server hostitele technologie Hyper-V nebo hosta<br /><br />BMR nebo záloha stavu systému hostitele|Ztracený virtuální počítač|Ano|Ne|Ne|
|Hyper-V<br /><br />Zálohování Azure Backup Server hostitele technologie Hyper-V nebo hosta<br /><br />BMR nebo záloha stavu systému hostitele|Ztracený nebo poškozený operační systém|Ne|Ano|Ano|
|Hyper-V<br /><br />Zálohování Azure Backup Server hostitele technologie Hyper-V nebo hosta<br /><br />BMR nebo záloha stavu systému hostitele|Ztracený hostitel Hyper-V (virtuální počítače beze změny)|Ne|Ne|Ano|
|Hyper-V<br /><br />Zálohování Azure Backup Server hostitele technologie Hyper-V nebo hosta<br /><br />BMR nebo záloha stavu systému hostitele|Ztracený hostitel Hyper-V (virtuální počítače ztracené)|Ne|Ne|Ano<br /><br />BMR následované pravidelným obnovením Azure Backup serveru|
|SQL Server nebo Exchange<br /><br />Zálohování aplikace Azure Backup serveru<br /><br />BMR nebo záloha stavu systému|Ztracená data aplikací|Ano|Ne|Ne|
|SQL Server nebo Exchange<br /><br />Zálohování aplikace Azure Backup serveru<br /><br />BMR nebo záloha stavu systému|Ztracený nebo poškozený operační systém|Ne|Y|Ano|
|SQL Server nebo Exchange<br /><br />Zálohování aplikace Azure Backup serveru<br /><br />BMR nebo záloha stavu systému|Ztracený server (databáze a soubory protokolů transakcí beze změny)|Ne|Ne|Ano|
|SQL Server nebo Exchange<br /><br />Zálohování aplikace Azure Backup serveru<br /><br />BMR nebo záloha stavu systému|Ztracený server (databáze a soubory protokolu transakcí ztracené)|Ne|Ne|Ano<br /><br />Obnovení úplné zálohy systému následované pravidelným obnovením Azure Backup serveru|

## <a name="how-system-state-backup-works"></a>Způsob fungování zálohy stavu systému

Po spuštění zálohy stavu systému Backup Server komunikuje s zálohování Windows serveru pro žádost o zálohu stavu systému serveru. Ve výchozím nastavení zálohování serveru a Windows Server Backup použít na jednotku, která má nejvíce dostupného volného místa. Informace o této jednotce se uloží do souboru PSDataSourceConfig.xml. Je to jednotka, která používá zálohování Windows serveru pro zálohování.

Můžete přizpůsobit na jednotku, kterou používá záložní Server pro zálohování stavu systému. Na chráněném serveru přejděte do C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources. Otevřete soubor PSDataSourceConfig.xml pro úpravy. Změnit \<FilesToProtect\> písmeno jednotky v hodnotě. Uložte soubor a zavřete ho. Pokud sadu ochranné skupiny ochrany stavu systému počítače, spusťte kontrolu konzistence. Pokud je vygenerována výstraha, vyberte **upravit skupinu ochrany** ve výstraze a pak dokončete průvodce. Pak spusťte další kontrolu konzistence.

Všimněte si, že pokud je server ochrany v clusteru, je možné, že disk klastru bude zvolen jako disk s největším množstvím volného místa. Pokud vlastnictví této jednotky byla přepnuta na jiný uzel a spuštění zálohy stavu systému, jednotka není k dispozici a zálohování se nezdaří. V tomto scénáři upravte soubor PSDataSourceConfig.xml a nasměrovat na místní disk.

V dalším kroku zálohování serveru vytvoří složku s názvem WindowsImageBackup v kořenovém adresáři složky pro obnovení. Protože zálohování Windows serveru vytváření zálohy se všechna data nachází v této složce. Po dokončení zálohování se soubor se přenesou do počítače záložní Server. Všimněte si následujících informací:

* Tato složka a její obsah se vymaže se po dokončení zálohování nebo přenos. Nejlepší způsob, jak nad tímto problémem přemýšlet je, že dochází místo k dalším dokončení zálohování rezervaci.
* Složka se vytvoří při každém provedení zálohy. Razítko času a data reflektovat čas poslední zálohy stavu systému.

## <a name="bmr-backup"></a>Zálohování BMR

Pro BMR (které zahrnuje i zálohu stavu systému) úlohy zálohování uložit přímo do sdílené složky v počítači zálohování serveru. Se neukládá do složky na chráněném serveru.

Zálohování serveru vyžaduje zálohování Windows serveru a nasdílí svazek repliky pro tuto zálohu BMR. V takovém případě neříká zálohování Windows serveru, aby použila jednotku s největším množstvím volného místa. Místo toho používá sdílenou složku, která byla vytvořena pro úlohu.

Po dokončení zálohování se soubor se přenesou do počítače záložní Server. Protokoly se ukládají do C:\Windows\Logs\WindowsServerBackup.

## <a name="prerequisites-and-limitations"></a>Požadavky a omezení

-   BMR není podporováno pro počítače se systémem Windows Server 2003 nebo počítačích, na kterých běží klientský operační systém.

-   BMR a stav systému u stejného počítače ve různých ochranných skupinách nelze chránit.

-   Zálohování serveru nemůže chránit sám sebe pro BMR.

-   Krátkodobá ochrana na pásku (disk na pásku nebo D2T) není u BMR podporována. Dlouhodobé ukládání na pásku (disk na disk na pásku nebo D2D2T) se podporuje.

-   U ochrany BMR na ochranu je potřeba nainstalovat Windows Server Backup v chráněném počítači.

-   Pro ochranu BMR na rozdíl od ochrany stavu systému, zálohu serveru nemá žádné požadavky na místo v chráněném počítači. Zálohování Windows serveru přímo přenáší zálohy k zálohování serveru. Úloha zálohování přenosu se už nebude na záložní Server **úlohy** zobrazení.

-   Záložní Server rezervuje pro BMR 30 GB místa na svazku repliky. Toto můžete změnit na **přidělení disku** stránky v Průvodci změnou skupiny ochrany nebo pomocí rutiny Get-DatasourceDiskAllocation a Set-DatasourceDiskAllocation prostředí PowerShell. Na svazku bodu obnovení vyžaduje ochrana BMR přibližně 6 GB pro ukládání na pět dní.
    * Všimněte si, že velikost svazku repliky nemůžete zmenšit na méně než 15 GB.
    * Zálohování serveru nepočítá velikost zdroje dat BMR. Předpokládá 30 GB pro všechny servery. Změňte hodnotu podle velikosti záloh BMR, které očekáváte, že ve vašem prostředí. Velikost zálohy BMR můžete přibližně vypočítat jako součet využitého místa na všech nepostradatelných svazcích. Nepostradatelné svazky = spouštěcí svazek + systémový svazek + svazek, který je hostitelem dat stavu systému, jako je Active Directory.

-   Pokud změníte z ochrany stavu systému na ochranu BMR, ochrana BMR vyžaduje méně místa na *svazek bodu obnovení*. Přebytečné místo na svazku se však neuvolní. Velikost svazku můžete zmenšit ručně na **upravit alokaci disku** stránky Průvodce změnou skupiny ochrany nebo pomocí rutiny Get-DatasourceDiskAllocation a Set-DatasourceDiskAllocation prostředí PowerShell.

    Pokud změníte z ochrany stavu systému na ochranu BMR, ochrana BMR vyžaduje více místa na *svazku repliky*. Svazek se automaticky rozšíří. Pokud chcete změnit výchozí alokaci místa, použijte rutinu Powershellu rutinu Modify-DiskAllocation.

-   Pokud se změní z ochrany BMR na ochranu stavu systému potřebujete více místa na svazku bodu obnovení. Zálohování serveru může pokusit automaticky zvětšit svazek. Pokud není dostatek místa ve fondu úložiště, dojde k chybě.

    Pokud změníte z ochrany BMR na ochranu stavu systému, musíte místo v chráněném počítači. To je, protože ochrana stavu systému nejprve zapíše repliku do místního počítače a přenese je na počítači zálohování serveru.

## <a name="before-you-begin"></a>Než začnete

1.  **Nasazení Azure Backup Server**. Ověřte, zda je správně nasazeny zálohování serveru. Další informace naleznete v tématu:
    * [Požadavky na systém pro Azure Backup serveru](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Systém ochrany zálohování serveru](backup-mabs-protection-matrix.md)

2.  **Nastavení úložiště**. Zálohovaná data můžete ukládat na disk, na pásku a v cloudu s Azure. Další informace najdete v tématu [Příprava datového úložiště](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

3.  **Nastavte agenta ochrany**. Nainstalujte agenta ochrany na počítači, který chcete zálohovat. Další informace najdete v tématu [nasazení agenta ochrany aplikace DPM](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Zálohování stavu systému a úplné obnovení systému
Nastavte skupinu ochrany, jak je popsáno v [nasazení skupin ochrany](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups). Všimněte si, že BMR a stav systému u stejného počítače v různých skupinách nelze chránit. Také když vyberete BMR, stav systému bude automaticky povolený.


1.  Chcete-li spustit Průvodce vytvořením nové skupiny ochrany v konzole správce zálohování serveru, vyberte **ochrany** > **akce** > **vytvořením skupiny ochrany** .

2.  Na **vybrat typ skupiny ochrany** stránce **servery**a pak vyberte **Další**.

3.  Na **vybrat členy skupiny** stránce, rozbalte počítač a pak vyberte buď **BMR** nebo **stavu systému**.

    Mějte na paměti, že BMR a systém stav u stejného počítače v různých skupinách nelze chránit. Také když vyberete BMR, stav systému bude automaticky povolený. Další informace najdete v tématu [nasazení skupin ochrany](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

4.  Na **vybrat způsob ochrany dat** stránky, vyberte požadovaný způsob zpracování krátkodobého a dlouhodobého zálohování. Krátkodobé zálohy se vždy nejprve na disku, s možností zálohování z disku do Azure v cloudu pomocí Azure Backup (krátkodobé nebo dlouhodobé uložení). O alternativu k dlouhodobému zálohování do cloudu je nastavení dlouhodobého zálohování na samostatná zařízení nebo na pásce knihovnu pásek, která je připojena k zálohování serveru.

5.  Na **vybrat krátkodobé cíle** stránky, vyberte požadovaný způsob zálohování do krátkodobého úložiště na disk:
    1. Pro **rozsah uchování**, vyberte, jak dlouho chcete data na disku uchovávat. 
    2. Pro **četnost synchronizací**, vyberte, jak často chcete spouštět přírůstkové zálohování na disk. Pokud nechcete nastavit interval zálohování, můžete zkontrolovat **těsně před bodem obnovení** možnost. Zálohování serveru se spustí expresní úplné zálohování těsně před každým bodem obnovení naplánován.

6.  Pokud chcete ukládat data na pásku pro dlouhodobé ukládání na **zadat dlouhodobé cíle** vyberte, jak dlouho chcete uchovat data pásky (1 – 99 let). 
    1. Pro **četnost záloh**, by měly být spuštěny vyberte četnost zálohování na pásku. Četnost je založená na rozsahu uchování, kterou jste vybrali:
        * Když je rozsah uchování 1 – 99 let, můžete zvolit vytváření záloh dojde k denně, týdně, jednou za dva týdny, měsíčně, čtvrtletně, půlročně nebo ročně.
        * Když je rozsah uchování 1 – 11 měsíců, můžete zvolit vytváření záloh každý den, každý týden, každých čtrnáct dní nebo každý měsíc.
        * Když je rozsah uchování 1 – 4 týdny, můžete zvolit vytváření záloh každý den nebo každý týden.

    2. Na **vyberte pásku a podrobnosti ke knihovně** stránky, vyberte pásku a knihovnu použít, a určuje, zda by měl komprimovaná a šifrovaná data.

7.  Na **zkontrolovat přidělení disku** stránky, zkontrolujte úložiště fondu místo na disku, který je přidělen do skupiny ochrany.

    1. **Celková velikost dat** je velikost dat, které chcete zálohovat.
    2. **Místo, které se mají zřídit v Azure Backup serveru na disku** místo zálohovat Server doporučuje pro skupinu ochrany. Zálohování serveru vybere ideální záložní svazek na základě nastavení. Však můžete upravit možnosti záložního svazku v **podrobnosti přidělení disku**. 
    3. Pro úlohy v rozevírací nabídce vyberte požadované úložiště. Úpravy změnit hodnoty **celkový úložiště** a **volné úložiště** v **dostupné diskové úložiště** podokně. Nevytížené místo představuje velikost úložiště, který Backup Server navrhuje že přidat do svazku pro zajištění plynulého zálohování.

8.  Na **vyberte způsob vytvoření repliky** stránky, vyberte požadovaný způsob počáteční úplné replikace dat. zpracování. Pokud se rozhodnete pro replikaci přes síť, doporučujeme, abyste zvolili dobu mimo špičku. Pro velké objemy dat nebo ne úplně optimální síťové podmínky zvažte replikaci dat offline pomocí vyměnitelného média.

9. Na **zvolte možnosti kontroly konzistence** vyberte, jak chcete automatizovat kontroly konzistence. Můžete spustit kontrolu jenom v případě, že data repliky stane nekonzistentní, nebo podle plánu. Pokud nechcete konfigurovat automatickou kontrolu konzistence, můžete kdykoli spustit ruční kontrolu. Chcete-li spustit ruční kontrolu, v **ochrany** oblasti konzoly pro správu zálohování serveru, klikněte pravým tlačítkem na skupinu ochrany a pak vyberte **provést kontrolu konzistence**.

10. Pokud jste vybrali možnost zálohování do cloudu pomocí Azure Backup na **zadat Data Online ochrany** stránky, ujistěte se, že jste vybrali úlohy, které chcete zálohovat do Azure.

11. Na **zadat plán Online zálohování** stránky, dojde k vyberte četnost přírůstkové zálohování do Azure. Můžete naplánovat zálohování na spouštění každý den, týden, měsíc a rok a vyberte čas a datum, za které se mají spustit. Zálohování se může spouštět až dvakrát denně. Pokaždé, když běží zálohování, bodem obnovení dat se vytvoří v Azure z kopie zálohovaných dat uložených na disku pro zálohování serveru.

12. Na **zadat zásady Online uchovávání** vyberte, jak jsou body obnovení, které jsou vytvořeny z denní, týdenní, měsíční a roční záloh uchovávaných v Azure.

13. Na **zvolit Online replikace** vyberte, jak proběhne počáteční úplné replikace dat. Můžete replikovat přes síť nebo provést offline zálohování (offline předvyplnění). Zálohování offline používá funkci Azure Import. Další informace najdete v tématu [pracovní postup Offline zálohování ve službě Azure Backup](backup-azure-backup-import-export.md).

14. Na **Souhrn** stránky, zkontrolujte nastavení. Po výběru **vytvořit skupinu**, dojde k počáteční replikaci dat. Při replikaci dat dokončí, dále **stav** stránka, stav skupiny ochrany je **OK**. Potom se provede na ochranu nastavení skupiny.

## <a name="recover-system-state-or-bmr"></a>Obnovení stavu systému nebo BMR
Můžete obnovit BMR nebo stav systému do umístění v síti. Pokud jste zálohovali BMR, použijte prostředí Windows Recovery Environment (WinRE) ke spuštění systému a připojení k síti. Potom použijte zálohování Windows serveru k obnovení ze síťového umístění. Pokud jste zálohovali stav systému, použijte zálohování Windows serveru k obnovení ze síťového umístění.

### <a name="restore-bmr"></a>Obnovit BMR
Spustíte obnovení na počítači zálohování serveru:

1.  V **obnovení** podokno, najít počítače, kterou chcete obnovit a pak vyberte **úplné obnovení systému**.

2.  Dostupné body obnovení jsou vypsány v kalendáři tučně. Vyberte datum a čas bodu obnovení, který chcete použít.

3.  Na **vybrat typ obnovení** stránce **kopírovat do síťové složky.**

4.  Na **zadejte cíl** stránky, vyberte ve které chcete data zkopírovat. Mějte na paměti, že vybraný cíl musí mít dostatek volného místa. Doporučujeme vytvořit novou složku.

5.  Na **zadat možnosti obnovení** vyberte nastavení zabezpečení, které chcete použít. Vyberte, jestli chcete použít síti storage area network (SAN) – na základě snímků hardwaru pro rychlejší obnovení. (Toto je možné, pouze v případě, že máte síť SAN se tato funkce je dostupná a možnost vytvářet a rozdělovat je umožnit zápis. Kromě toho chráněného počítače a záloha serveru musí být připojený ke stejné síti.)

6.  Nastavte možnosti oznámení. Na **potvrzení** stránce **obnovit**.

Nastavte umístění sdílené složky:

1.  V umístění pro obnovení přejděte do složky, která má zálohování.

2.  Sdílejte složku, která je jednu úroveň nad souborem WindowsImageBackup tak, aby nacházela složka WindowsImageBackup kořenovém adresáři sdílené složky. Pokud to neuděláte, obnovení zálohu nenalezne. Připojení pomocí prostředí Windows Recovery Environment (WinRE), budete potřebovat sdílenou složku, můžete přistupovat v prostředí WinRE otevřít pomocí správné IP adresy a přihlašovacích údajů.

Obnovení systému:

1.  Spusťte počítač, na kterém chcete obnovit bitovou kopii pomocí disku DVD Windows pro systém, který provádíte obnovení.

2.  Na stránce první ověřte nastavení jazyka a národního prostředí. Na **nainstalovat** stránce **opravit tento počítač**.

3.  Na **možnosti obnovení systému** stránce **obnovení počítače pomocí bitové kopie systému, který jste vytvořili dříve**.

4.  Na **výběr zálohy bitové kopie systému** stránce **Vybrat bitovou kopii systému** > **Upřesnit** > **hledat bitovou kopii systému v síti**. Pokud se zobrazí upozornění, vyberte **Ano**. Přejděte do sdílené cesty, zadejte přihlašovací údaje a pak vyberte bod obnovení. Tato funkce proskenuje pro konkrétní zálohy, které jsou k dispozici v daném bodě obnovení. Vyberte bod obnovení, který chcete použít.

5.  Na **Vvybrat způsob obnovení zálohy** stránce **formátovat a znovu rozdělit disky**. Na další stránce ověřte nastavení. 

6.  Zahajte obnovení, vyberte **Dokončit**. Je vyžadováno restartování.

### <a name="restore-system-state"></a>Obnovení stavu systému

Spuštění obnovení zálohování serveru:

1.  V **obnovení** podokno, najít počítače, který chcete obnovit a pak vyberte **úplné obnovení systému**.

2.  Dostupné body obnovení jsou vypsány v kalendáři tučně. Vyberte datum a čas bodu obnovení, který chcete použít.

3.  Na **vybrat typ obnovení** stránce **kopírovat do síťové složky**.

4.  Na **zadejte cíl** stránky, vyberte místo, kam chcete zkopírovat data. Mějte na paměti, že vybraný cíl by měl dostatek volného místa. Doporučujeme vytvořit novou složku.

5.  Na **zadat možnosti obnovení** vyberte nastavení zabezpečení, které chcete použít. Vyberte, zda chcete použít snímky hardwaru založené na síti SAN pro rychlejší obnovení. (To je možné, pouze v případě, že máte síť SAN s tuto funkci a možnost vytvářet a rozdělovat je tak, aby byl zapisovatelný. Kromě toho chráněného počítače a záloha serveru musí být připojený ke stejné síti.)

6.  Nastavte možnosti oznámení. Na **potvrzení** stránce **obnovit**.

Spuštění zálohování Windows serveru:

1.  Vyberte **akce** > **obnovit** > **tento Server** > **Další**.

2.  Vyberte **jiný Server**, vyberte **zadat typ umístění** stránce a pak vyberte **Vzdálená sdílená složka**. Zadejte cestu ke složce, která obsahuje bod obnovení.

3.  Na **vybrat typ obnovení** stránce **stavu systému**. 

4. Na **vybrat umístění obnovení stavu systému** stránce **původního umístění**.

5.  Na **potvrzení** stránce **obnovit**. Po obnovení restartujte server.

6.  Obnovení stavu systému také můžete spustit z příkazového řádku. K tomu spusťte zálohování Windows serveru na počítači, který chcete obnovit. Chcete-li získat identifikátor verze z příkazového řádku, zadejte: ```wbadmin get versions -backuptarget \<servername\sharename\>```

    Identifikátor verze slouží ke spuštění obnovení stavu systému. Na příkazovém řádku zadejte: ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Potvrďte, že chcete spustit obnovení. Proces v okně příkazového řádku můžete vidět. Vytvoří se protokol obnovení. Po obnovení restartujte server.

