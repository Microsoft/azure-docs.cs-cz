---
title: Zálohování souborů ve Azure Stack virtuálních počítačích
description: Pomocí Azure Backup můžete zálohovat a obnovovat Azure Stack soubory a aplikace do prostředí Azure Stack.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 6/5/2018
ms.author: dacurwin
ms.openlocfilehash: 25e511a1596c1119d1db8c9270ce216cd5186e72
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68735477"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Zálohování souborů a aplikací ve službě Azure Stack
Pomocí Azure Backup můžete chránit (nebo zálohovat) soubory a aplikace v Azure Stack. Pokud chcete zálohovat soubory a aplikace, nainstalujte Microsoft Azure Backup Server jako virtuální počítač běžící na Azure Stack. Soubory můžete chránit na jakémkoli serveru Azure Stack ve stejné virtuální síti. Po instalaci Azure Backup Server přidejte disky Azure, abyste zvýšili místní úložiště, které je k dispozici pro krátkodobé zálohování dat. Azure Backup Server využívá Azure Storage pro dlouhodobé uchovávání.

> [!NOTE]
> I když Azure Backup Server a System Center Data Protection Manager (DPM) jsou podobné, aplikace DPM není podporována pro použití s Azure Stack.
>

Tento článek se nezabývá instalací Azure Backup Server v prostředí Azure Stack. Postup instalace Azure Backup Server v Azure Stack najdete v článku [instalace Azure Backup Server](backup-mabs-install-azure-stack.md).


## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Zálohování souborů a složek ve Azure Stack virtuálních počítačích do Azure

Pokud chcete nakonfigurovat Azure Backup Server k ochraně souborů v Azure Stack virtuálních počítačích, otevřete konzolu pro Azure Backup Server. Pomocí konzoly nástroje můžete nakonfigurovat skupiny ochrany a chránit data na virtuálních počítačích.

1. V konzole Azure Backup Server klikněte na **ochrana** a na panelu nástrojů klikněte na **Nový** . otevře se průvodce **vytvořením nové skupiny ochrany** .

   ![Konfigurace ochrany v konzole Azure Backup Server](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Otevření Průvodce může trvat několik sekund. Po otevření průvodce klikněte na tlačítko **Další** a přejděte na obrazovku **Vybrat typ skupiny ochrany** .

   ![Otevře se Průvodce novou skupinou ochrany.](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Na obrazovce **Vybrat typ skupiny ochrany** zvolte **servery** a klikněte na **Další**.

    ![Otevře se Průvodce novou skupinou ochrany.](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    Otevře se obrazovka **Vybrat členy skupiny** . 

    ![Otevře se Průvodce novou skupinou ochrany.](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. Na obrazovce **Vybrat členy skupiny** kliknutím **+** rozbalte seznam podpoložek. Pro všechny položky, které chcete chránit, zaškrtněte políčko. Po výběru všech položek klikněte na **Další**.

    ![Otevře se Průvodce novou skupinou ochrany.](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Společnost Microsoft doporučuje umístit všechna data, která budou sdílet zásady ochrany, do jedné skupiny ochrany. Kompletní informace o plánování a nasazování skupin ochrany najdete v článku aplikace System Center DPM, [nasazení skupin ochrany](https://docs.microsoft.com/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-1801).

4. Na obrazovce **Vybrat způsob ochrany dat** zadejte název skupiny ochrany. Zaškrtněte políčko pro možnost **krátkodobá ochrana pomocí:** a **Chci online ochranu**. Klikněte na **Další**.

    ![Otevře se Průvodce novou skupinou ochrany.](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Chcete-li vybrat možnost Chci **online ochranu**, musíte nejprve vybrat možnost **Chci krátkodobou ochranu pomocí:** Disk. Azure Backup Server není chráněn na pásku, takže disk je jedinou volbou pro krátkodobou ochranu.

5. Na obrazovce **zadat krátkodobé cíle** zvolte, jak dlouho chcete zachovat body obnovení uložené na disk a kdy se mají ukládat přírůstkové zálohy. Klikněte na **Další**.

    > [!IMPORTANT]
    > Neměli byste uchovávat data obnovení (zálohování) na discích s Azure Backup Server po dobu delší než pět dní.
    >

    ![Otevře se Průvodce novou skupinou ochrany.](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png) 

    Místo výběru intervalu pro přírůstkové zálohování pro spuštění expresního úplného zálohování těsně před každým naplánovaným bodem obnovení klikněte na **těsně před bodem obnovení**. Pokud chráníte úlohy aplikací, Azure Backup Server vytvoří body obnovení podle plánu četnosti synchronizací (Pokud aplikace podporuje přírůstkové zálohování). Pokud aplikace nepodporuje přírůstkové zálohování, Azure Backup Server spustí expresní úplné zálohování.

    V případě **bodů obnovení souborů**určete, kdy se mají vytvářet body obnovení. Klikněte na **Upravit** a nastavte časy a dny v týdnu, kdy se mají body obnovení vytvářet.

6. Na obrazovce **Kontrola přidělení disku** zkontrolujte přidělené místo na disku fondu úložiště pro skupinu ochrany.

    **Celková velikost dat** je velikost dat, která chcete zálohovat, a **místo na disku, které se má zřídit** v Azure Backup Server je Doporučené místo pro skupinu ochrany. Azure Backup Server zvolí ideální zálohovací svazek na základě nastavení. Můžete ale upravit volby svazku pro zálohování v podrobnostech přidělení disku. V rozevírací nabídce vyberte preferované úložiště pro úlohy. Vaše úpravy mění hodnoty celkového úložiště a bezplatného úložiště v podokně Disk Storage k dispozici. Nezřízené místo je velikost úložiště Azure Backup Server navrhne přidání do svazku, aby bylo možné v budoucnu pokračovat v zálohování.

7. V části **Vybrat způsob vytvoření repliky**vyberte, jak chcete zpracovat počáteční úplnou replikaci dat. Pokud se rozhodnete provést replikaci přes síť, Azure vám doporučuje vybrat dobu mimo špičku. Pro velké objemy dat nebo méně, než jsou optimální síťové podmínky, zvažte replikaci dat pomocí vyměnitelných médií.

8. V **možnosti vybrat nastavení**kontroly konzistence vyberte, jak chcete automatizovat kontroly konzistence. Povolte kontroly konzistence, aby se spouštěly jenom v případě, že se replikace dat neshoduje, nebo podle plánu. Pokud nechcete konfigurovat automatickou kontrolu konzistence, spusťte ruční kontrolu kdykoli pomocí:
    * V oblasti **ochrana** konzoly Azure Backup Server klikněte pravým tlačítkem na skupinu ochrany a vyberte **provést kontrolu konzistence**.

9. Pokud se rozhodnete zálohovat do Azure, na stránce **zadat data online ochrany** se ujistěte, že jsou vybrané úlohy, které chcete zálohovat do Azure.

10. V **Nastavení plán online zálohování**určete, kdy se má provést přírůstkové zálohování do Azure. 

    Můžete naplánovat, aby se zálohy spouštěly každý den, týden, měsíc a rok a datum a čas, kdy se mají spustit. Zálohování se může provádět až dvakrát denně. Při každém spuštění úlohy zálohování se v Azure vytvoří bod obnovení dat z kopie zálohovaných dat uložených na Azure Backup Server disku.

11. V části **zadat zásady uchovávání online**určete způsob, jakým se v Azure uchovávají body obnovení vytvořené z denních, týdenních nebo měsíčních záloh nebo ročních záloh.

12. V části **Zvolit online replikaci**určete, jak probíhá počáteční Úplná replikace dat. 

13. Na **Shrnutí**zkontrolujte nastavení. Po kliknutí na **vytvořit skupinu**dojde k počáteční replikaci dat. Po dokončení replikace dat se na stránce **stav** zobrazuje stav skupiny ochrany jako **OK**. Úloha počátečního zálohování probíhá v souladu s nastavením skupiny ochrany.

## <a name="recover-file-data"></a>Obnovit data souboru

K obnovení dat na virtuální počítač použijte Azure Backup Server konzolu.

1. V konzole Azure Backup Server klikněte na navigačním panelu na možnost **obnovení** a vyhledejte data, která chcete obnovit. V podokně výsledků vyberte data.

2. V kalendáři v části body obnovení jsou data tučně označena jako body obnovení. Vyberte datum, které chcete obnovit.

3. V podokně **obnovitelná položka** vyberte položku, kterou chcete obnovit.

4. Kliknutím na tlačítko **obnovit** v podokně **Akce** otevřete Průvodce obnovením.

5. Data můžete obnovit následujícím způsobem:

    * **Obnovit do původního umístění** – Pokud je klientský počítač připojený přes síť VPN, tato možnost nefunguje. Místo toho použijte alternativní umístění a potom zkopírujte data z tohoto umístění.
    * **Obnovit do alternativního umístění**

6. Zadejte možnosti obnovení:

    * V případě **chování obnovení existující verze**vyberte možnost **vytvořit kopii**, **Přeskočit**nebo **přepsat**. Přepsání je k dispozici pouze při obnovování do původního umístění.
    * V části **obnovit zabezpečení**vyberte **použít nastavení cílového počítače** , nebo **použít nastavení zabezpečení verze bodu obnovení**.
    * V případě **omezení využití šířky pásma sítě**klikněte na **Upravit** a povolte omezení využití šířky pásma sítě.
    * **Oznámení** Klikněte na možnost **po dokončení obnovy zaslat e-mail**a zadejte příjemce, kteří budou oznámení dostávat. E-mailové adresy oddělujte čárkami.
    * Po provedení výběrů klikněte na **Další** .

7. Zkontrolujte nastavení obnovení a klikněte na tlačítko **obnovit**. 

    > [!Note] 
    > V průběhu úlohy obnovení se zruší všechny úlohy synchronizace pro vybrané položky obnovení.
    >

Pokud používáte Moderní úložiště zálohování (MB/s), není podporováno obnovení koncového uživatele (EUR) u souborového serveru. Souborového serveru EUR má závislost na služba Stínová kopie svazku (VSS), která Moderní úložiště zálohování nepoužívá. Pokud je povolena EUR, obnovte data pomocí následujících kroků:

1. Přejděte k chráněným souborům a klikněte pravým tlačítkem na název souboru a vyberte **vlastnosti**.

2. V nabídce **vlastnosti** klikněte na **předchozí verze** a vyberte verzi, kterou chcete obnovit.

## <a name="view-azure-backup-server-with-a-vault"></a>Zobrazení Azure Backup Server s trezorem
Pokud chcete zobrazit Azure Backup Server entity na webu Azure Portal, můžete postupovat podle následujících kroků:
1. Otevřete Recovery Services trezor.
2. Klikněte na infrastruktura zálohování.
3. Zobrazit servery pro správu zálohování.

## <a name="see-also"></a>Viz také:
Informace o použití Azure Backup Server k ochraně dalších úloh naleznete v jednom z následujících článků:
- [Zálohování farmy služby SharePoint](https://docs.microsoft.com/azure/backup/backup-mabs-sharepoint-azure-stack)
- [Zálohování SQL serveru](https://docs.microsoft.com/azure/backup/backup-mabs-sql-azure-stack)
