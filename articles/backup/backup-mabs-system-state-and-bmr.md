---
title: Ochrana stavu systému a úplné obnovení systému
description: Použijte Azure Backup Server k zálohování stavu systému a zajištění ochrany úplného obnovení systému (BMR).
ms.topic: conceptual
ms.date: 05/15/2017
ms.openlocfilehash: 0e89b149fe8b06bdd70c72aa442f50125c5e3786
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025499"
---
# <a name="back-up-system-state-and-restore-to-bare-metal-with-azure-backup-server"></a>Zálohování stavu systému a obnovení do holých počítačů pomocí Azure Backup Server

Azure Backup Server zálohuje stav systému a poskytuje ochranu úplného obnovení (BMR).

* **Zálohování stavu systému**: zálohuje soubory operačního systému, takže se můžete zotavit při spuštění počítače, ale systémové soubory a Registry budou ztraceny. Záloha stavu systému zahrnuje:
  * Člen domény: spouštěcí soubory, registrační databáze tříd modelu COM+, registr
  * Řadič domény: Windows Server Active Directory (NTDS), spouštěcí soubory, registrační databáze tříd modelu COM+, registr, systémový svazek (SYSVOL)
  * Počítač se spuštěnou Clusterové služby: metadata serveru clusteru
  * Počítač, který spouští službu Certificate Services: data certifikátu
* **Úplné zálohování**: zálohuje soubory operačního systému a všechna data na nepostradatelných svazcích (kromě uživatelských dat). V rámci definice BMR záloha zahrnuje zálohu stavu systému. Poskytuje ochranu v případě, že se počítač nespustí a vy budete muset všechno obnovit.

Následující tabulka shrnuje, co můžete zálohovat a obnovit. Podrobné informace o verzích aplikací, které se dají chránit pomocí stavu systému a BMR, najdete v článku [co se Azure Backup Server zálohovat?](backup-mabs-protection-matrix.md).

|Backup|Problém|Obnovení ze zálohy služby Azure Backup Server|Obnovení ze zálohy stavu systému|BMR|
|----------|---------|---------------------------|------------------------------------|-------|
|**Data souborů**<br /><br />Zálohování běžných dat<br /><br />BMR/zálohování stavu systému|Ztracená data souborů|Ano|N|N|
|**Data souborů**<br /><br />Azure Backup Server zálohování dat souborů<br /><br />BMR/zálohování stavu systému|Ztracený nebo poškozený operační systém|N|Ano|Ano|
|**Data souborů**<br /><br />Azure Backup Server zálohování dat souborů<br /><br />BMR/zálohování stavu systému|Ztracený Server (datové svazky nedotčeny)|N|N|Ano|
|**Data souborů**<br /><br />Azure Backup Server zálohování dat souborů<br /><br />BMR/zálohování stavu systému|Ztracený Server (datové svazky se ztratily)|Ano|Ne|Ano (BMR, následované pravidelným obnovením zálohovaných dat souborů)|
|**Data služby SharePoint**:<br /><br />Azure Backup Server zálohování dat farmy<br /><br />BMR/zálohování stavu systému|Ztracené weby, seznamy, položky seznamu, dokumenty|Ano|N|N|
|**Data služby SharePoint**:<br /><br />Azure Backup Server zálohování dat farmy<br /><br />BMR/zálohování stavu systému|Ztracený nebo poškozený operační systém|N|Ano|Ano|
|**Data služby SharePoint**:<br /><br />Azure Backup Server zálohování dat farmy<br /><br />BMR/zálohování stavu systému|Zotavení po havárii|N|N|N|
|Windows Server 2012 R2 Hyper-V<br /><br />Azure Backup Server zálohování hostitele nebo hosta technologie Hyper-V<br /><br />BMR/zálohování stavu systému hostitele|Ztracený virtuální počítač|Ano|N|N|
|Hyper-V<br /><br />Azure Backup Server zálohování hostitele nebo hosta technologie Hyper-V<br /><br />BMR/zálohování stavu systému hostitele|Ztracený nebo poškozený operační systém|N|Ano|Ano|
|Hyper-V<br /><br />Azure Backup Server zálohování hostitele nebo hosta technologie Hyper-V<br /><br />BMR/zálohování stavu systému hostitele|Ztracené hostitele Hyper-V (virtuální počítače nedotčeny)|N|N|Ano|
|Hyper-V<br /><br />Azure Backup Server zálohování hostitele nebo hosta technologie Hyper-V<br /><br />BMR/zálohování stavu systému hostitele|Ztracené hostitele Hyper-V (virtuální počítače se ztratily)|N|N|Ano<br /><br />BMR, následované pravidelným obnovením Azure Backup Server|
|SQL Server/Exchange<br /><br />Zálohování aplikace Azure Backup Server<br /><br />BMR/zálohování stavu systému|Ztracená data aplikací|Ano|N|N|
|SQL Server/Exchange<br /><br />Zálohování aplikace Azure Backup Server<br /><br />BMR/zálohování stavu systému|Ztracený nebo poškozený operační systém|N|Y|Ano|
|SQL Server/Exchange<br /><br />Zálohování aplikace Azure Backup Server<br /><br />BMR/zálohování stavu systému|Ztracený Server (protokoly databáze/transakce nedotčeny)|N|N|Ano|
|SQL Server/Exchange<br /><br />Zálohování aplikace Azure Backup Server<br /><br />BMR/zálohování stavu systému|Ztracený Server (ztracené protokoly databáze nebo transakcí)|N|N|Ano<br /><br />BMR obnovení, následované pravidelným obnovením Azure Backup Server|

## <a name="how-system-state-backup-works"></a>Jak funguje zálohování stavu systému

Po spuštění zálohy stavu systému Server Backup komunikuje s Zálohování Windows Serveru a požádá o zálohu stavu systému serveru. Ve výchozím nastavení používá záložní server a Zálohování Windows Serveru jednotku s největším dostupným volným místem. Informace o této jednotce jsou uloženy v souboru soubor PSDataSourceConfig. XML. Toto je jednotka, kterou Zálohování Windows Serveru používá k zálohování.

Jednotku, kterou záložní server používá, můžete přizpůsobit pro zálohování stavu systému. Na chráněném serveru otevřete umístění C:\Program Files\Microsoft Data Protection Manager\MABS\Datasources. Otevřete soubor soubor PSDataSourceConfig. XML pro úpravy. Změňte hodnotu \<FilesToProtect\> pro písmeno jednotky. Uložte soubor a zavřete ho. Pokud je pro ochranu stavu systému počítače nastavena skupina ochrany, spusťte kontrolu konzistence. Pokud je vygenerována výstraha, v upozornění vyberte možnost **Upravit skupinu ochrany** a pak dokončete průvodce. Pak spusťte další kontrolu konzistence.

Všimněte si, že pokud je server ochrany v clusteru, je možné, že jednotka clusteru bude vybrána jako jednotka s největším množstvím volného místa. Pokud je vlastnictví této jednotky přepnuto na jiný uzel a spustí se záloha stavu systému, jednotka nebude k dispozici a zálohování bude neúspěšné. V tomto scénáři upravte soubor PSDataSourceConfig. XML tak, aby odkazoval na místní disk.

Dále Zálohování Windows Serveru v kořenu složky pro obnovení vytvoří složku s názvem WindowsImageBackup. Když Zálohování Windows Serveru vytvoří zálohu, všechna data se umístí do této složky. Po dokončení zálohování se soubor přenese na počítač se záložním serverem. Všimněte si následujících informací:

* Tato složka a její obsah se po dokončení zálohování nebo přenosu nevyčistí. Nejlepším způsobem, jak si to představit, je, že je místo rezervované pro příští dokončení zálohování.
* Složka se vytvoří při každém provedení zálohy. Razítko data a času odrážejí čas poslední zálohy stavu systému.

## <a name="bmr-backup"></a>Zálohování BMR

V případě BMR (včetně zálohování stavu systému) se úloha zálohování uloží přímo do sdílené složky na počítači se záložním serverem. Není uložen do složky na chráněném serveru.

Záložní server volá Zálohování Windows Serveru a sdílí svazek repliky pro tuto zálohu BMR. V takovém případě neříká Zálohování Windows Serveru používat jednotku s největším množstvím volného místa. Místo toho používá sdílenou složku, která byla vytvořena pro úlohu.

Po dokončení zálohování se soubor přenese na počítač se záložním serverem. Protokoly se ukládají v C:\Windows\Logs\WindowsServerBackup.

## <a name="prerequisites-and-limitations"></a>Požadavky a omezení

* BMR se nepodporuje pro počítače se systémem Windows Server 2003 nebo pro počítače, na kterých běží klientský operační systém.

* BMR a stav systému nemůžete chránit pro stejný počítač v různých skupinách ochrany.

* Počítač se záložním serverem nemůže chránit sám sebe pro BMR.

* Krátkodobá ochrana na pásku (disk na pásku nebo D2T) není pro BMR podporovaná. Dlouhodobé ukládání na pásku (z disku na disk na pásku nebo D2D2T) je podporováno.

* Pro BMR Protection musí být na chráněném počítači nainstalovaná Zálohování Windows Serveru.

* U BMR Protection na rozdíl od ochrany stavu systému nemá záložní server žádné požadavky na místo na chráněném počítači. Zálohování Windows Serveru přímo přenáší zálohy na počítač se záložním serverem. Úloha přenos záloh se nezobrazuje v zobrazení zálohovacích **úloh** serveru.

* Záložní server rezervuje pro BMR 30 GB místa na svazku repliky. Tuto možnost můžete změnit na stránce **alokace disku** v průvodci úpravou skupiny ochrany nebo pomocí rutin Get-DatasourceDiskAllocation a set-DatasourceDiskAllocation prostředí PowerShell. Na svazku bodu obnovení vyžaduje ochrana BMR přibližně 6 GB po dobu 5 dní.
  * Počítejte s tím, že velikost svazku repliky nemůžete snížit na méně než 15 GB.
  * Záložní server nepočítá velikost zdroje dat BMR. Předpokládá pro všechny servery 30 GB. Změňte hodnotu na základě velikosti záloh BMR očekávaných ve vašem prostředí. Velikost zálohy BMR může být přibližně vypočítána jako součet využitého místa na všech důležitých svazcích. Nepostradatelné svazky = spouštěcí svazek + systémový svazek + svazek, který je hostitelem dat stavu systému, jako je například služba Active Directory.

* Změníte-li z ochrany stavu systému na BMR Protection, bude ochrana BMR vyžadovat méně místa ve *svazku bodu obnovení*. Dodatečné místo na svazku se ale neuvolní. Velikost svazku můžete ručně zmenšit na stránce **Upravit alokaci disku** v průvodci úpravou skupiny ochrany nebo pomocí rutin Get-DatasourceDiskAllocation a set-DatasourceDiskAllocation prostředí PowerShell.

    Pokud změníte z ochrany stavu systému na BMR Protection, bude ochrana BMR vyžadovat více místa na *svazku repliky*. Svazek je automaticky rozšířen. Pokud chcete změnit výchozí přidělení místa, použijte rutinu prostředí PowerShell pro úpravy DiskAllocation.

* Pokud změníte z ochrany BMR na ochranu stavu systému, budete potřebovat více místa na svazku bodu obnovení. Záložní server se může pokusit o automatické zvětšení svazku. Pokud ve fondu úložiště není dostatek místa, dojde k chybě.

    Pokud změníte z ochrany BMR na ochranu stavu systému, budete potřebovat prostor na chráněném počítači. Je to proto, že ochrana stavu systému nejprve zapíše repliku do místního počítače a pak ji přenese na počítač se záložním serverem.

## <a name="before-you-begin"></a>Než začnete

1. **Nasazení Azure Backup Server**. Ověřte, jestli je záložní server správně nasazený. Další informace:
    * [Požadavky na systém pro Azure Backup Server](https://docs.microsoft.com/system-center/dpm/install-dpm#setup-prerequisites)
    * [Matice ochrany záložního serveru](backup-mabs-protection-matrix.md)

2. **Nastavte úložiště**. Zálohovaná data můžete ukládat na disk, na pásku a do cloudu s Azure. Další informace najdete v tématu [Příprava úložiště dat](https://docs.microsoft.com/system-center/dpm/plan-long-and-short-term-data-storage).

3. **Nastavte agenta ochrany**. Nainstalujte agenta ochrany na počítač, který chcete zálohovat. Další informace najdete v tématu [nasazení agenta ochrany aplikace DPM](https://docs.microsoft.com/system-center/dpm/deploy-dpm-protection-agent).

## <a name="back-up-system-state-and-bare-metal"></a>Zálohování stavu systému a úplného obnovení systému

Nastavte skupinu ochrany, jak je popsáno v tématu [nasazení skupin ochrany](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups). Všimněte si, že nemůžete chránit BMR a stav systému pro stejný počítač v různých skupinách. I když vyberete možnost BMR, bude stav systému automaticky povolen.

1. Chcete-li otevřít Průvodce vytvořením nové skupiny ochrany v konzole správce záložního serveru, vyberte možnost **ochrana** > **Akce** > **vytvořit skupinu ochrany**.

2. Na stránce **Vybrat typ skupiny ochrany** vyberte **servery**a pak vyberte **Další**.

3. Na stránce **Vybrat členy skupiny** rozbalte počítač a pak vyberte buď **BMR** nebo **stav systému**.

    Pamatujte, že nemůžete chránit BMR i stav systému pro stejný počítač v různých skupinách. I když vyberete možnost BMR, bude stav systému automaticky povolen. Další informace najdete v tématu [nasazení skupin ochrany](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups).

4. Na stránce **Vybrat způsob ochrany dat** vyberte, jak chcete zpracovat krátkodobé a dlouhodobé zálohování. Krátkodobé zálohování je vždycky vždy na disku, s možností zálohování z disku do cloudu Azure pomocí Azure Backup (krátkodobá nebo dlouhodobá). Alternativou k dlouhodobému zálohování do cloudu je nastavení dlouhodobé zálohy na samostatné páskové zařízení nebo páskové knihovny, která je připojená k záložnímu serveru.

5. Na stránce **Vybrat krátkodobé cíle** vyberte, jak se má zálohovat do krátkodobého úložiště na disku:
    1. V poli **Rozsah uchování**vyberte, jak dlouho chcete data na disku uchovávat.
    2. V poli **Frekvence synchronizace**vyberte, jak často chcete spouštět přírůstkové zálohování na disk. Pokud nechcete nastavit interval zálohování, můžete zaškrtnout možnost **těsně před bodem obnovení** . Záložní server spustí expresní a úplné zálohování těsně před každým naplánovaným bodem obnovení.

6. Pokud chcete ukládat data na pásku pro dlouhodobé ukládání, na stránce **zadat dlouhodobé cíle** vyberte, jak dlouho chcete data na pásce uchovávat (1-99 let).
    1. V případě **četnosti zálohování**vyberte, jak často se má spustit zálohování na pásku. Frekvence je založena na rozsahu uchování, který jste vybrali:
        * Když je rozsah uchování 1-99 let, můžete zvolit vytváření záloh každý den, každý týden, dva týdny, měsíčně, čtvrtletně, pololetí nebo ročně.
        * Když je rozsah uchování 1-11 měsíců, můžete zvolit vytváření záloh každý den, každý týden, dva týdny nebo každý měsíc.
        * Když je rozsah uchování 1-4 týdnů, můžete zvolit vytváření záloh každý den nebo každý týden.

    2. Na stránce **Vybrat podrobnosti pásky a knihovny** vyberte pásku a knihovnu, které se mají použít, a určete, jestli mají být data komprimovaná a šifrovaná.

7. Na stránce **zkontrolovat přidělení disku** Zkontrolujte místo na disku fondu úložiště, které je přidělené skupině ochrany.

    1. **Celková velikost dat** je velikost dat, která chcete zálohovat.
    2. **Místo na disku, které se má zřídit v Azure Backup Server** je místo, které server pro ochranu doporučuje pro skupinu ochrany. Záložní server zvolí na základě nastavení ideální zálohovací svazek. Můžete ale upravit volby svazku pro zálohování v **podrobnostech přidělení disku**.
    3. Pro úlohy v rozevírací nabídce vyberte preferované úložiště. Vaše úpravy mění hodnoty **celkového úložiště** a **bezplatného úložiště** v podokně **Disk Storage k dispozici** . Za nezřízené místo je velikost úložiště, které server pro zálohování navrhne k přidání do svazku, aby bylo zajištěno hladké zálohování.

8. Na stránce **Vybrat způsob vytvoření repliky** vyberte, jak chcete zpracovat počáteční úplnou replikaci dat. Pokud se rozhodnete provést replikaci přes síť, doporučujeme vám vybrat dobu mimo špičku. Pro velké objemy dat nebo pro síťové podmínky, které jsou menší než optimální, zvažte replikaci dat offline pomocí vyměnitelného média.

9. Na stránce **Vybrat možnosti kontroly konzistence** vyberte, jak chcete automatizovat kontroly konzistence. Kontrolu můžete spustit pouze v případě, že se data repliky neshodují, nebo podle plánu. Pokud nechcete konfigurovat automatickou kontrolu konzistence, můžete kdykoli spustit ruční kontrolu. Ruční kontrolu spustíte tak, že v oblasti **ochrana** konzoly správce záložního serveru kliknete pravým tlačítkem na skupinu ochrany a pak vyberete **provést kontrolu konzistence**.

10. Pokud jste vybrali možnost zálohování do cloudu pomocí Azure Backup, na stránce **zadat data online ochrany** ověřte, že jste vybrali úlohy, které chcete zálohovat do Azure.

11. Na stránce **zadat plán online zálohování** vyberte, jak často bude provedeno přírůstkové zálohování do Azure. Můžete naplánovat spuštění zálohování každý den, týden, měsíc a rok a vybrat čas a datum, kdy se mají spustit. Zálohování se může provádět až dvakrát denně. Pokaždé, když se zálohování spustí, vytvoří se v Azure bod obnovení dat z kopie zálohovaných dat uložených na disku záložního serveru.

12. Na stránce **zadat zásady uchovávání online** vyberte způsob, jakým se v Azure uchovávají body obnovení vytvořené z denního, týdenního, měsíčního a ročního zálohování.

13. Na stránce **Zvolte online replikaci** vyberte, jak se má počáteční Úplná replikace dat nacházet. Můžete provést replikaci přes síť nebo provést offline zálohování (offline předvyplnění). Offline zálohování používá funkci importu Azure. Další informace najdete v tématu [pracovní postup offline zálohování v Azure Backup](offline-backup-azure-data-box.md).

14. Na stránce **Souhrn** zkontrolujte nastavení. Po výběru **vytvořit skupinu**se spustí počáteční replikace dat. Po dokončení replikace dat je stav skupiny ochrany **OK**na stránce **stav** . Pak proběhne zálohování podle nastavení skupiny ochrany.

## <a name="recover-system-state-or-bmr"></a>Obnovení stavu systému nebo BMR

BMR nebo stav systému můžete obnovit do umístění v síti. Pokud jste zálohovali BMR, pomocí prostředí Windows Recovery Environment (WinRE) spusťte systém a připojte ho k síti. Pak použijte Zálohování Windows Serveru k obnovení z umístění v síti. Pokud jste zálohovali stav systému, stačí použít Zálohování Windows Serveru k obnovení z umístění v síti.

### <a name="restore-bmr"></a>Obnovit BMR

Spustit obnovení na počítači se záložním serverem:

1. V podokně **obnovení** vyhledejte počítač, který chcete obnovit, a pak vyberte **úplné obnovení systému**.

2. Dostupné body obnovení jsou v kalendáři označeny tučně. Vyberte datum a čas bodu obnovení, který chcete použít.

3. Na stránce **Vybrat typ obnovení** vyberte možnost **Kopírovat do síťové složky.**

4. Na stránce **zadání cíle** vyberte, kam chcete data zkopírovat. Mějte na paměti, že vybraný cíl potřebuje dostatek místa. Doporučujeme vytvořit novou složku.

5. Na stránce **zadat možnosti obnovení** vyberte nastavení zabezpečení, které chcete použít. Pak vyberte, jestli chcete pro rychlejší obnovení použít snímky hardwaru založené na síti SAN (Storage Area Network). (Tato možnost je dostupná pouze v případě, že máte síť SAN s touto funkcí dostupnou a možnost vytvořit a rozdělit klon, aby bylo možné zapisovat do ní. Kromě toho musí být počítač s chráněným počítačem a záložním serverem připojený ke stejné síti.)

6. Nastavení možností oznámení Na stránce **potvrzení** vyberte **obnovit**.

Nastavte umístění sdílené složky:

1. V umístění obnovení přejdete do složky, která obsahuje zálohu.

2. Sdílejte složku, která je o jednu úroveň výše než WindowsImageBackup, aby kořen sdílené složky byla složka WindowsImageBackup. Pokud to neuděláte, obnovení zálohování nenalezne. Chcete-li se připojit pomocí prostředí Windows Recovery Environment (WinRE), potřebujete sdílenou složku, ke které máte přístup v rozhraní WinRE se správnou IP adresou a přihlašovacími údaji.

Obnovte systém:

1. Spusťte počítač, ve kterém chcete bitovou kopii obnovit, pomocí disku DVD se systémem Windows pro systém, který obnovujete.

2. Na první stránce Ověřte nastavení jazyka a národního prostředí. Na stránce **instalace** vyberte **opravit počítač**.

3. Na stránce **Možnosti obnovení systému** vyberte možnost **Obnovit počítač pomocí bitové kopie systému, kterou jste vytvořili dříve**.

4. Na stránce **Výběr zálohy bitové kopie systému** vyberte **možnost vybrat bitovou kopii systému** > **Upřesnit** > **Vyhledat bitovou kopii systému v síti**. Pokud se zobrazí upozornění, vyberte **Ano**. Přejděte na cestu ke sdílené složce, zadejte přihlašovací údaje a potom vyberte bod obnovení. Tato kontrola vyhledá konkrétní zálohy, které jsou k dispozici v tomto bodu obnovení. Vyberte bod obnovení, který chcete použít.

5. Na stránce **Zvolte způsob obnovení zálohy** vyberte možnost **Formátovat a znovu rozdělit disky na oddíly**. Na další stránce Ověřte nastavení.

6. Pokud chcete zahájit obnovení, vyberte **Dokončit**. Vyžaduje se restartování.

### <a name="restore-system-state"></a>Obnovit stav systému

Spustit obnovení na záložním serveru:

1. V podokně **obnovení** vyhledejte počítač, který chcete obnovit, a pak vyberte **úplné obnovení systému**.

2. Dostupné body obnovení jsou v kalendáři označeny tučně. Vyberte datum a čas bodu obnovení, který chcete použít.

3. Na stránce **Vybrat typ obnovení** vyberte možnost **Kopírovat do síťové složky**.

4. Na stránce **zadání cíle** vyberte, kam chcete data zkopírovat. Mějte na paměti, že vybraný cíl potřebuje dostatek místa. Doporučujeme vytvořit novou složku.

5. Na stránce **zadat možnosti obnovení** vyberte nastavení zabezpečení, které chcete použít. Pak vyberte, jestli chcete pro rychlejší obnovení použít snímky hardwaru založené na síti SAN. (Tato možnost je k dispozici pouze v případě, že máte síť SAN s touto funkcí a možnost vytvořit a rozdělit klon, aby bylo možné zapisovat do něj. Kromě toho musí být chráněný počítač a server záložního serveru připojen ke stejné síti.)

6. Nastavení možností oznámení Na stránce **potvrzení** vyberte **obnovit**.

Spustit Zálohování Windows Serveru:

1. Vyberte **akce** > **obnovit** > **tomto serveru** > **Další**.

2. Vyberte **jiný server**, vyberte stránku **zadat typ umístění** a pak vyberte **Vzdálená sdílená složka**. Zadejte cestu ke složce, která obsahuje bod obnovení.

3. Na stránce **Vybrat typ obnovení** vyberte možnost **stav systému**.

4. Na stránce **Vyberte umístění pro obnovení stavu systému** vyberte **původní umístění**.

5. Na stránce **potvrzení** vyberte **obnovit**. Po obnovení restartujte server aplikace.

6. Obnovení stavu systému můžete spustit také na příkazovém řádku. Provedete to tak, že na počítači, který chcete obnovit, spustíte Zálohování Windows Serveru. Pokud chcete získat verzi identifikátorem, zadejte na příkazovém řádku: ```wbadmin get versions -backuptarget \<servername\sharename\>```

    Pomocí identifikátoru verze spusťte obnovení stavu systému. Do příkazového řádku zadejte: ```wbadmin start systemstaterecovery -version:<versionidentified> -backuptarget:<servername\sharename>```

    Potvrďte, že chcete spustit obnovení. Proces můžete vidět v okně příkazového řádku. Vytvoří se protokol obnovení. Po obnovení restartujte server aplikace.
