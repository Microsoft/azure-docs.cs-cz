---
title: Ochrana stavu systému a úplné obnovení systému
description: Použijte Azure Backup Server k zálohování stavu systému a zajištění ochrany úplného obnovení (BMR).
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: c5096158ca0e76ca03577347d8dd3e1419a33ca0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021618"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-by-using-azure-backup-server"></a>Zálohování stavu systému a obnovení do holého počítače pomocí Azure Backup Server

Azure Backup Server zálohuje stav systému a poskytuje ochranu úplného obnovení (BMR).

* **Zálohování stavu systému**: zálohuje soubory operačního systému. Tato záloha umožňuje obnovení při spuštění počítače, ale systémové soubory a Registry jsou ztraceny. Zálohování stavu systému zahrnuje následující prvky:
  * Člen domény: Spouštěcí soubory, registrační databáze třídy modelu COM+, registr
  * Řadič domény: Windows Server Active Directory (NTDS), spouštěcí soubory, registrační databáze tříd modelu COM+, registr, systémový svazek (SYSVOL)
  * Počítač se spuštěnou Clusterové služby: metadata serveru clusteru
  * Počítač, který spouští službu Certificate Services: data certifikátu
* **Úplné zálohování**: zálohuje soubory operačního systému a všechna data na nepostradatelných svazcích s výjimkou uživatelských dat. V rámci definice BMR záloha zahrnuje zálohu stavu systému. Poskytuje ochranu v případě, že se počítač nespustí a vy budete muset všechno obnovit.

Následující tabulka shrnuje, co můžete zálohovat a obnovit. Informace o verzích aplikace, které může stav systému a BMR chránit, najdete v tématu [co Azure Backup Server zálohování](backup-mabs-protection-matrix.md).

|Backup|Problém|Obnovení ze zálohy služby Azure Backup Server|Obnovení ze zálohy stavu systému|Obnovení BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Data souborů**<br /><br />Zálohování běžných dat<br /><br />BMR nebo záloha stavu systému|Ztracená data souborů|Y|N|N|
|**Data souborů**<br /><br />Azure Backup Server zálohování dat souborů<br /><br />BMR nebo záloha stavu systému|Ztracený nebo poškozený operační systém|N|Y|Y|
|**Data souborů**<br /><br />Azure Backup Server zálohování dat souborů<br /><br />BMR nebo záloha stavu systému|Ztracený Server (datové svazky nedotčeny)|N|N|Y|
|**Data souborů**<br /><br />Azure Backup Server zálohování dat souborů<br /><br />BMR nebo záloha stavu systému|Ztracený server (datové svazky ztracené)|Y|N|Y<br /><br />BMR, následované pravidelným obnovením zálohovaných dat souborů|
|**Data služby SharePoint**<br /><br />Azure Backup Server zálohování dat farmy<br /><br />BMR nebo záloha stavu systému|Ztracené weby, seznamy, položky seznamu, dokumenty|Y|N|N|
|**Data služby SharePoint**<br /><br />Azure Backup Server zálohování dat farmy<br /><br />BMR nebo záloha stavu systému|Ztracený nebo poškozený operační systém|N|Y|Y|
|**Data služby SharePoint**<br /><br />Azure Backup Server zálohování dat farmy<br /><br />BMR nebo záloha stavu systému|Zotavení po havárii|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Azure Backup Server zálohování hostitele nebo hosta technologie Hyper-V<br /><br />BMR nebo záloha stavu systému hostitele|Ztracený virtuální počítač|Y|N|N|
|Hyper-V<br /><br />Azure Backup Server zálohování hostitele nebo hosta technologie Hyper-V<br /><br />BMR nebo záloha stavu systému hostitele|Ztracený nebo poškozený operační systém|N|Y|Y|
|Hyper-V<br /><br />Azure Backup Server zálohování hostitele nebo hosta technologie Hyper-V<br /><br />BMR nebo záloha stavu systému hostitele|Ztracený hostitel Hyper-V (virtuální počítače beze změny)|N|N|Y|
|Hyper-V<br /><br />Azure Backup Server zálohování hostitele nebo hosta technologie Hyper-V<br /><br />BMR nebo záloha stavu systému hostitele|Ztracený hostitel Hyper-V (virtuální počítače ztracené)|N|N|Y<br /><br />BMR, následované pravidelným obnovením Azure Backup Server|
|SQL Server nebo Exchange<br /><br />Zálohování aplikace Azure Backup Server<br /><br />BMR nebo záloha stavu systému|Ztracená data aplikací|Y|N|N|
|SQL Server nebo Exchange<br /><br />Zálohování aplikace Azure Backup Server<br /><br />BMR nebo záloha stavu systému|Ztracený nebo poškozený operační systém|N|Y|Y|
|SQL Server nebo Exchange<br /><br />Zálohování aplikace Azure Backup Server<br /><br />BMR nebo záloha stavu systému|Ztracený server (databáze a soubory protokolů transakcí beze změny)|N|N|Y|
|SQL Server nebo Exchange<br /><br />Zálohování aplikace Azure Backup Server<br /><br />BMR nebo záloha stavu systému|Ztracený server (databáze a soubory protokolu transakcí ztracené)|N|N|Y<br /><br />BMR obnovení, následované pravidelným obnovením Azure Backup Server|

## <a name="how-system-state-backup-works"></a>Způsob fungování zálohy stavu systému

Po spuštění zálohy stavu systému Server Backup komunikuje s Zálohování Windows Serveru a požádá o zálohu stavu systému serveru. Ve výchozím nastavení používá záložní server a Zálohování Windows Serveru jednotku s největším dostupným volným místem. Informace o této jednotce se uloží do souboru *PSDataSourceConfig.xml* .

Můžete přizpůsobit jednotku, kterou záložní server používá pro zálohování stavu systému:

1. Na chráněném serveru otevřete umístění *C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources*.
1. Otevřete soubor *PSDataSourceConfig.xml* pro úpravy.
1. Změňte písmeno jednotky v hodnotě \<FilesToProtect\>.
1. Uložte soubor a zavřete ho.

Pokud je skupina ochrany nastavena tak, aby chránila stav systému počítače, spusťte kontrolu konzistence. Pokud je vygenerována výstraha, vyberte v výstraze možnost **Upravit skupinu ochrany** a pak dokončete stránky v průvodci. Pak spusťte další kontrolu konzistence.

Pokud je server ochrany v clusteru, může být jednotka clusteru vybraná jako jednotka, která má nejvíce volného místa. Pokud je vlastnictví této jednotky přepnuto na jiný uzel a spustí se záloha stavu systému, jednotka nebude k dispozici a zálohování bude neúspěšné. V tomto scénáři změňte *PSDataSourceConfig.xml* tak, aby odkazovaly na místní disk.

Dále Zálohování Windows Serveru v kořenu složky pro obnovení vytvoří složku s názvem *WindowsImageBackup* . Když Zálohování Windows Serveru vytvoří zálohu, všechna data se umístí do této složky. Po dokončení zálohování se soubor přenese na počítač se záložním serverem. Všimněte si následujících informací:

* Tato složka a její obsah se po dokončení zálohování nebo přenosu nevyčistí. Nejlepším způsobem, jak si to představit, je, že je místo vyhrazené pro další dokončení zálohování.
* Složka se vytvoří pro každou zálohu. Razítko data a času odrážejí čas poslední zálohy stavu systému.

## <a name="how-bmr-backup-works"></a>Jak funguje zálohování BMR

V případě BMR (včetně zálohování stavu systému) se úloha zálohování uloží přímo do sdílené složky na počítači se záložním serverem. Není uložen do složky na chráněném serveru.

Záložní server volá Zálohování Windows Serveru a sdílí svazek repliky pro tuto zálohu BMR. V takovém případě nevyžaduje Zálohování Windows Serveru používání jednotky, která má nejvíce volného místa. Místo toho používá sdílenou složku, která byla vytvořena pro úlohu.

Po dokončení zálohování se soubor přenese na počítač se záložním serverem. Protokoly se ukládají do *C:\Windows\Logs\WindowsServerBackup*.

## <a name="prerequisites-and-limitations"></a>Požadavky a omezení

* BMR se nepodporuje pro počítače se systémem Windows Server 2003 nebo pro počítače, na kterých běží klientský operační systém.

* BMR a stav systému nemůžete chránit u stejného počítače v různých skupinách ochrany.

* Počítač se záložním serverem nemůže chránit sám sebe pro BMR.

* Krátkodobá ochrana na pásku (disk na pásku nebo D2T) není pro BMR podporovaná. Dlouhodobé ukládání na pásku (disk na disk na pásku nebo D2D2T) je podporováno.

* Pro BMR Protection musí být na chráněném počítači nainstalovaná Zálohování Windows Serveru.

* Pro BMR Protection na rozdíl od ochrany stavu systému nemá záložní server na chráněném počítači žádné požadavky na místo. Zálohování Windows Serveru přímo přenáší zálohy na počítač se záložním serverem. Úloha přenos záloh se nezobrazuje v zobrazení zálohovacích **úloh** serveru.

* Záložní server rezervuje pro BMR 30 GB místa na svazku repliky. Toto plnění tohoto místa můžete změnit na stránce **alokace disku** v průvodci úpravou skupiny ochrany. Můžete také použít rutiny Get-DatasourceDiskAllocation a Set-DatasourceDiskAllocation prostředí PowerShell. Na svazku bodu obnovení vyžaduje ochrana BMR přibližně 6 GB po dobu 5 dní.
  * Velikost svazku repliky nemůžete snížit na méně než 15 GB.
  * Záložní server nepočítá velikost zdroje dat BMR. Předpokládá pro všechny servery 30 GB. Změňte hodnotu na základě velikosti záloh BMR očekávaných ve vašem prostředí. Velikost zálohy BMR můžete zhruba vypočítat jako součet využitého místa na všech důležitých svazcích. Nepostradatelné svazky = spouštěcí svazek + systémový svazek + svazek, který je hostitelem dat stavu systému, jako je například služba Active Directory.

* Pokud změníte z ochrany stavu systému na BMR Protection, pak BMR Protection vyžaduje méně místa ve *svazku bodu obnovení*. Dodatečné místo na svazku se ale neuvolní. Velikost svazku můžete zmenšit ručně na stránce **Upravit alokaci disku** Průvodce úpravou skupiny ochrany. Můžete také použít rutiny Get-DatasourceDiskAllocation a Set-DatasourceDiskAllocation prostředí PowerShell.

    Pokud změníte z ochrany stavu systému na BMR Protection, pak ochrana BMR vyžaduje více místa na *svazku repliky*. Svazek je automaticky rozšířen. Pokud chcete změnit výchozí přidělení místa, použijte rutinu Modify-DiskAllocation PowerShellu.

* Pokud změníte z ochrany BMR na ochranu stavu systému, budete potřebovat více místa na svazku bodu obnovení. Záložní server se může pokusit o automatické zvětšení svazku. Pokud fond úložiště nemá dostatek místa, dojde k chybě.

    Pokud změníte z ochrany BMR na ochranu stavu systému, budete potřebovat prostor na chráněném počítači. Potřebujete místo, protože ochrana stavu systému nejprve zapíše repliku do místního počítače a pak ji přenese na počítač se záložním serverem.

## <a name="before-you-begin"></a>Než začnete

1. **Nasazení Azure Backup Server**. Ověřte, jestli je záložní server správně nasazený. Další informace naleznete v tématu:
    * [Požadavky na systém pro Azure Backup Server](/system-center/dpm/install-dpm#setup-prerequisites)
    * [Matice ochrany záložního serveru](backup-mabs-protection-matrix.md)

1. **Nastavte úložiště**. Zálohovaná data můžete ukládat na disk, na pásku a do cloudu s Azure. Další informace najdete v tématu [Příprava úložiště dat](/system-center/dpm/plan-long-and-short-term-data-storage).

1. **Nastavte agenta ochrany**. Nainstalujte agenta ochrany na počítač, který chcete zálohovat. Další informace najdete v tématu [nasazení agenta ochrany aplikace DPM](/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Zálohování stavu systému a úplné obnovení systému

Zálohování stavu systému a holé součásti:

1. Chcete-li spustit příkaz Create Průvodce vytvořením nové skupiny ochrany, vyberte v konzole správce záložního serveru možnost Akce **ochrany**  >    >  **vytvořit skupinu ochrany**.

1. Na stránce **Vybrat typ skupiny ochrany** vyberte **servery** a pak vyberte **Další**.

1. Na stránce **Vybrat členy skupiny** rozbalte počítač a pak vyberte buď **BMR** nebo **stav systému**.

    Pamatujte, že nemůžete chránit BMR i stav systému pro stejný počítač v různých skupinách. I když vyberete možnost BMR, bude stav systému automaticky povolen. Další informace najdete v tématu [nasazení skupin ochrany](/system-center/dpm/create-dpm-protection-groups).

1. Na stránce **Vybrat způsob ochrany dat** vyberte, jak se má zpracovat krátkodobá záloha a dlouhodobá záloha.

    Krátkodobé zálohování je vždycky na disku nejprve s možností zálohování z disku do Azure pomocí Azure Backup (krátkodobá nebo dlouhodobá). Alternativou k dlouhodobému zálohování do cloudu je nastavení dlouhodobé zálohy na samostatné páskové zařízení nebo páskové knihovny, která je připojená k záložnímu serveru.

1. Na stránce **Vybrat cíle Short-Term** zvolte způsob zálohování do krátkodobého úložiště na disku:
    * V poli **Rozsah uchování** určete, jak dlouho chcete data na disku uchovávat.
    * V případě **četnosti synchronizací** vyberte, jak často se má spouštět přírůstkové zálohování na disk. Pokud nechcete nastavit interval zálohování, můžete vybrat **těsně před bodem obnovení**. Záložní server spustí expresní úplné zálohování těsně před každým naplánovaným bodem obnovení.

1. Pokud chcete data pro dlouhodobé ukládání ukládat na pásku, pak na stránce **zadat Long-Termé cíle** zvolte, jak dlouho chcete data na pásce uchovávat (1 až 99 let).
    1. V případě **četnosti zálohování** vyberte, jak často se má zálohování spouštět na pásku. Frekvence je založena na rozsahu uchování, který jste vybrali:
        * Když je rozsah uchování 1 až 99 let, můžete zálohovat každý den, každý týden, dva týdny, měsíčně, čtvrtletně, pololetí nebo každý rok.
        * Když je rozsah uchování 1 až 11 měsíců, můžete zálohovat každý den, každý týden, dva týdny nebo každý měsíc.
        * Když je rozsah uchování 1 až 4 týdny, můžete zálohovat každý den nebo každý týden.

    1. Na stránce **Vybrat podrobnosti pásky a knihovny** vyberte pásku a knihovnu, které chcete použít. Také vyberte, zda mají být data komprimována a zašifrována.

1. Na stránce **zkontrolovat přidělení disku** Zkontrolujte místo na disku fondu úložiště, které je k dispozici pro skupinu ochrany.

    * **Celková velikost dat** je velikost dat, která chcete zálohovat.
    * **Místo na disku, které se má zřídit v Azure Backup Server** je místo, které server pro ochranu doporučuje pro skupinu ochrany. Záložní server používá tato nastavení k výběru ideálního záložního svazku. Volby zálohovacího svazku můžete upravit v **podrobnostech přidělení disku**.
    * Pro úlohy v rozevírací nabídce vyberte preferované úložiště. Úpravy, které provedete, změní hodnoty v polích **Celková velikost úložiště** a **Volný úložný prostor** v podokně **Dostupný úložný prostor na disku**. Za nezřízené místo je velikost úložiště, které server pro zálohování navrhne, abyste mohli zajistit hladké zálohování.

1. Na stránce **Vybrat způsob vytvoření repliky** vyberte, jak se má zpracovat počáteční Úplná replikace dat.

    Pokud se rozhodnete provést replikaci přes síť, doporučujeme vám vybrat dobu mimo špičku. Pro velké objemy dat nebo pro síťové podmínky, které jsou menší než optimální, zvažte replikaci dat offline pomocí vyměnitelného média.

1. Na stránce **Vybrat možnosti kontroly konzistence** vyberte, jak chcete automatizovat kontroly konzistence.

    Kontrolu můžete spustit pouze v případě, že se data repliky neshodují, nebo podle plánu. Pokud nechcete konfigurovat automatickou kontrolu konzistence, můžete kdykoli spustit ruční kontrolu. Ruční kontrolu spustíte tak, že v oblasti **ochrana** konzoly správce záložního serveru kliknete pravým tlačítkem na skupinu ochrany a pak vyberete **provést kontrolu konzistence**.

1. Pokud se rozhodnete zálohovat do cloudu pomocí Azure Backup, na stránce **zadat data online ochrany** vyberte úlohy, které chcete zálohovat do Azure.

1. Na stránce **zadat plán online zálohování** vyberte, jak často se má přírůstkově zálohovat do Azure.

    Zálohování můžete naplánovat tak, aby se spouštěla každý den, týden, měsíc a rok. Můžete také vybrat datum a čas, kdy se mají zálohy spouštět. Zálohování se může spouštět až dvakrát denně. Při každém spuštění zálohování se v Azure vytvoří bod obnovení dat z kopie zálohovaných dat, která jsou uložená na disku záložního serveru.

1. Na stránce **zadat zásady uchovávání online** vyberte způsob, jakým se v Azure uchovávají body obnovení vytvořené z denního, týdenního, měsíčního a ročního zálohování.

1. Na stránce **Zvolte online replikaci** vyberte, jak se má počáteční Úplná replikace dat nacházet.

    Replikaci můžete provést po síti nebo při zálohování do offline režimu (offline osazení). Offline zálohování používá funkci importu Azure. Další informace najdete v tématu [pracovní postup offline zálohování v Azure Backup](offline-backup-azure-data-box.md).

1. Na stránce  **Souhrn** zkontrolujte nastavení. Po výběru **vytvořit skupinu** se spustí počáteční replikace dat. Po dokončení replikace dat je stav skupiny ochrany **OK** na stránce **stav** . K zálohování pak dojde podle nastavení skupiny ochrany.

## <a name="recover-system-state-or-bmr"></a>Obnovení stavu systému nebo BMR

Stav systému nebo BMR můžete obnovit do síťového umístění. Pokud jste zálohovali BMR, použijte prostředí Windows Recovery Environment (WinRE) a spusťte systém a připojte ho k síti. Potom pomocí zálohování Windows Serveru (WSB) proveďte obnovení ze síťového umístění. Pokud jste zálohovali stav systému, stačí použít Zálohování Windows Serveru k obnovení z umístění v síti.

### <a name="restore-bmr"></a>Obnovit BMR

Spuštění obnovení na počítači se záložním serverem:

1. V podokně **obnovení** vyhledejte počítač, který chcete obnovit. Pak vyberte **úplné obnovení systému**.

1. Dostupné body obnovení jsou v kalendáři uvedené tučným písmem. Vyberte datum a čas bodu obnovení, který chcete použít.

1. Na stránce  **Vybrat typ obnovení** vyberte možnost **Kopírovat do síťové složky**.

1. Na stránce **Zadejte cíl** vyberte cíl kopírovaných dat.

    Nezapomeňte, že cíl potřebuje dostatek místa pro data. Doporučujeme pro cíl vytvořit novou složku.

1. Na stránce **zadat možnosti obnovení** vyberte nastavení zabezpečení. Pak pro rychlejší obnovení vyberte, jestli se mají použít snímky hardwaru založené na síti SAN (Storage Area Network). Tato možnost je k dispozici pouze v případě, že:
    * Máte síť SAN, která tuto funkci poskytuje.
    * Klon můžete vytvořit a rozdělit tak, aby bylo možné zapisovat do něj.
    * Počítač s chráněným počítačem a záložním serverem je připojený ke stejné síti.

1. Nastavení možností oznámení
1. Na stránce **potvrzení** vyberte **obnovit**.

Nastavení umístění sdílené složky:

1. V umístění obnovení přejdete do složky, která obsahuje zálohu.

1. Sdílejte složku, která je jedna úroveň výše než *WindowsImageBackup* , aby kořen sdílené složky byla složka *WindowsImageBackup* .

    Pokud tuto složku nesdílíte, obnovení nenalezne zálohu. Chcete-li se připojit pomocí rozhraní WinRE, potřebujete sdílenou složku, ke které máte přístup v rozhraní WinRE se správnou IP adresou a přihlašovacími údaji.

Postup obnovení systému:

1. Spusťte počítač, ve kterém chcete bitovou kopii obnovit, pomocí disku DVD se systémem Windows pro systém, který obnovujete.

1. Na první stránce Ověřte nastavení pro jazyk a národní prostředí. Na stránce **instalace** vyberte **opravit počítač**.

1. Na stránce **Možnosti obnovení systému** vyberte možnost **Obnovit počítač pomocí bitové kopie systému, kterou jste vytvořili dříve**.

1. Na stránce **Výběr zálohy bitové kopie systému** vyberte **možnost vybrat bitovou kopii systému**  >  **Rozšířené**  >  **hledání systémové image v síti**. Pokud se zobrazí upozornění, vyberte **Ano**. Přejděte na cestu ke sdílené složce, zadejte přihlašovací údaje a potom vyberte bod obnovení. Systém vyhledá konkrétní zálohy, které jsou k dispozici v tomto bodu obnovení. Vyberte bod obnovení, který chcete použít.

1. Na stránce **Zvolte způsob obnovení zálohy** vyberte možnost **Formátovat a znovu rozdělit disky na oddíly**. Na další stránce Ověřte nastavení.

1. Pokud chcete zahájit obnovení, vyberte **Dokončit**. Vyžaduje se restartování.

### <a name="restore-system-state"></a>Obnovení stavu systému

Spuštění obnovení na záložním serveru:

1. V podokně **obnovení** vyhledejte počítač, který chcete obnovit, a pak vyberte **úplné obnovení systému**.

1. Dostupné body obnovení jsou v kalendáři uvedené tučným písmem. Vyberte datum a čas bodu obnovení, který chcete použít.

1. Na stránce **Vybrat typ obnovení** vyberte možnost **Kopírovat do síťové složky**.

1. Na stránce **zadání cíle** vyberte, kam se mají kopírovat data.

    Nezapomeňte, že vybraný cíl potřebuje dostatek místa pro data. Doporučujeme pro cíl vytvořit novou složku.

1. Na stránce **zadat možnosti obnovení** vyberte nastavení zabezpečení. Pak pro rychlejší obnovení vyberte, jestli se mají používat snímky hardwaru založené na síti SAN. Tato možnost je k dispozici pouze v případě, že:
    * Máte síť SAN, která tuto funkci poskytuje.
    * Klon můžete vytvořit a rozdělit tak, aby bylo možné zapisovat do něj.
    * Chráněný počítač a server záložního serveru jsou připojené ke stejné síti.

1. Nastavení možností oznámení
1. Na stránce **potvrzení** vyberte **obnovit**.

Spuštění Zálohování Windows Serveru:

1. Vyberte **Akce**  >  **obnovit**  >  **Tento server**  >  **dále**.

1. Vyberte **jiný server**, vyberte stránku **zadat typ umístění** a pak vyberte **Vzdálená sdílená složka**. Zadejte cestu ke složce, která obsahuje bod obnovení.

1. Na stránce **Vybrat typ obnovení** vyberte možnost **stav systému**.

1. Na stránce **Vyberte umístění pro obnovení stavu systému** vyberte  **původní umístění**.

1. Na stránce **potvrzení** vyberte **obnovit**.

1. Po obnovení restartujte server aplikace.

Obnovení stavu systému můžete spustit také na příkazovém řádku:

1. Spusťte Zálohování Windows Serveru na počítači, který chcete obnovit.

1. Pokud chcete získat verzi identifikátorem, zadejte do příkazového řádku:

   ```wbadmin get versions -backuptarget \<servername\sharename\>```

1. Pomocí identifikátoru verze spusťte obnovení stavu systému. Na příkazovém řádku zadejte:

    ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

1. Potvrďte, že chcete spustit obnovení. Proces můžete vidět v okně příkazového řádku. Vytvoří se protokol obnovení.

1. Po obnovení restartujte server aplikace.
