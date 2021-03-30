---
title: Zálohování souborů ve Azure Stack virtuálních počítačích
description: Pomocí Azure Backup můžete zálohovat a obnovovat Azure Stack soubory a aplikace do prostředí Azure Stack.
ms.topic: conceptual
ms.date: 06/05/2018
ms.openlocfilehash: cae95c10c510969cc0553a54a506789d6be427d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89180979"
---
# <a name="back-up-files-and-applications-on-azure-stack"></a>Zálohování souborů a aplikací na Azure Stack

Pomocí Azure Backup můžete chránit (nebo zálohovat) soubory a aplikace v Azure Stack. Pokud chcete zálohovat soubory a aplikace, nainstalujte Microsoft Azure Backup Server jako virtuální počítač běžící na Azure Stack. Soubory můžete chránit na jakémkoli serveru Azure Stack ve stejné virtuální síti. Po instalaci Azure Backup Server přidejte disky Azure, abyste zvýšili místní úložiště, které je k dispozici pro krátkodobé zálohování dat. Azure Backup Server využívá Azure Storage pro dlouhodobé uchovávání.

> [!NOTE]
> I když Azure Backup Server a System Center Data Protection Manager (DPM) jsou podobné, aplikace DPM není podporována pro použití s Azure Stack.
>

Tento článek se nezabývá instalací Azure Backup Server ve Azure Stack prostředí. Postup instalace Azure Backup Server v Azure Stack najdete v článku [instalace Azure Backup Server](backup-mabs-install-azure-stack.md).

## <a name="back-up-files-and-folders-in-azure-stack-vms-to-azure"></a>Zálohování souborů a složek ve Azure Stack virtuálních počítačích do Azure

Pokud chcete nakonfigurovat Azure Backup Server k ochraně souborů v Azure Stack virtuálních počítačích, otevřete konzolu pro Azure Backup Server. Pomocí konzoly nástroje můžete nakonfigurovat skupiny ochrany a chránit data na virtuálních počítačích.

1. V konzole Azure Backup Server vyberte **ochrana** a na panelu nástrojů vyberte možnost **nové** a otevřete tak průvodce **vytvořením nové skupiny ochrany** .

   ![Konfigurace ochrany v konzole Azure Backup Server](./media/backup-mabs-files-applications-azure-stack/1-mabs-menu-create-protection-group.png)

    Otevření Průvodce může trvat několik sekund. Po otevření průvodce klikněte na tlačítko **Další** a přejděte na obrazovku **Vybrat typ skupiny ochrany** .

   ![Otevře se Průvodce novou skupinou ochrany.](./media/backup-mabs-files-applications-azure-stack/2-create-new-protection-group-wiz.png)

2. Na obrazovce **Vybrat typ skupiny ochrany** vyberte **servery** a **pak další**.

    ![Vybrat typ skupiny ochrany](./media/backup-mabs-files-applications-azure-stack/3-select-protection-group-type.png)

    Otevře se obrazovka **Vybrat členy skupiny** .

    ![Vybrat členy skupiny](./media/backup-mabs-files-applications-azure-stack/4-opening-screen-choose-servers.png)

3. Na obrazovce **Vybrat členy skupiny** vyberte možnost **+** pro rozbalení seznamu podpoložek. Pro všechny položky, které chcete chránit, zaškrtněte políčko. Po výběru všech položek vyberte **Další**.

    ![Vyberte každou položku, kterou chcete chránit.](./media/backup-mabs-files-applications-azure-stack/5-select-group-members.png)

    Společnost Microsoft doporučuje umístit všechna data, která budou sdílet zásady ochrany, do jedné skupiny ochrany. Kompletní informace o plánování a nasazování skupin ochrany najdete v článku aplikace System Center DPM, [nasazení skupin ochrany](/system-center/dpm/create-dpm-protection-groups).

4. Na obrazovce **Vybrat způsob ochrany dat** zadejte název skupiny ochrany. Zaškrtněte políčko pro možnost **krátkodobá ochrana pomocí:** a **Chci online ochranu**. Vyberte **Další**.

    ![Vyberte způsob ochrany dat](./media/backup-mabs-files-applications-azure-stack/6-select-data-protection-method.png)

    Chcete-li vybrat možnost Chci **online ochranu**, musíte nejprve vybrat možnost **Chci krátkodobou ochranu pomocí:** disk. Azure Backup Server není chráněn na pásku, takže disk je jedinou volbou pro krátkodobou ochranu.

5. Na obrazovce **zadat Short-Termé cíle** zvolte, jak dlouho se mají zachovat body obnovení uložené na disk a kdy se mají ukládat přírůstkové zálohy. Vyberte **Další**.

    > [!IMPORTANT]
    > **Neměli byste uchovávat data** obnovení (zálohování) na discích s Azure Backup Server po dobu delší než pět dní.
    >

    ![Určení Short-Termch cílů](./media/backup-mabs-files-applications-azure-stack/7-select-short-term-goals.png)

    Místo výběru intervalu pro přírůstkové zálohování, aby bylo možné spustit expresní úplné zálohování těsně před každým naplánovaným bodem obnovení, vyberte možnost **těsně před bodem obnovení**. Pokud chráníte úlohy aplikací, Azure Backup Server vytvoří body obnovení podle plánu četnosti synchronizací (Pokud aplikace podporuje přírůstkové zálohování). Pokud aplikace nepodporuje přírůstkové zálohování, Azure Backup Server spustí expresní úplné zálohování.

    V případě **bodů obnovení souborů** určete, kdy se mají vytvářet body obnovení. Vyberte **Upravit** a nastavte časy a dny v týdnu, kdy se mají body obnovení vytvářet.

6. Na obrazovce **Kontrola přidělení disku** zkontrolujte přidělené místo na disku fondu úložiště pro skupinu ochrany.

    **Celková velikost dat** je velikost dat, která chcete zálohovat, a **místo na disku, které se má zřídit** v Azure Backup Server je Doporučené místo pro skupinu ochrany. Azure Backup Server zvolí ideální zálohovací svazek na základě nastavení. Možnosti záložního svazku ale můžete upravit v části Podrobnosti přidělení disku. V rozevírací nabídce vyberte požadované úložiště pro úlohy. Úpravy, které provedete, změní hodnoty v polích Celková velikost úložiště a Volný úložný prostor v podokně Dostupný úložný prostor na disku. Nezřízené místo je velikost úložiště Azure Backup Server navrhne přidání do svazku, aby bylo možné v budoucnu pokračovat v zálohování.

7. V části **Vybrat způsob vytvoření repliky** vyberte, jak chcete zpracovat počáteční úplnou replikaci dat. Pokud se rozhodnete provést replikaci přes síť, Azure vám doporučuje vybrat dobu mimo špičku. Pro velké objemy dat nebo méně, než jsou optimální síťové podmínky, zvažte replikaci dat pomocí vyměnitelných médií.

8. V možnosti **Vybrat nastavení kontroly konzistence** vyberte, jak chcete kontroly konzistence automatizovat. Povolte kontroly konzistence, aby se spouštěly jenom v případě, že se replikace dat neshoduje, nebo podle plánu. Pokud nechcete konfigurovat automatickou kontrolu konzistence, spusťte ruční kontrolu kdykoli pomocí:
    * V oblasti **ochrana** konzoly Azure Backup Server klikněte pravým tlačítkem na skupinu ochrany a vyberte **provést kontrolu konzistence**.

9. Pokud se rozhodnete zálohovat do Azure, na stránce **zadat data online ochrany** se ujistěte, že jsou vybrané úlohy, které chcete zálohovat do Azure.

10. V **Nastavení plán online zálohování** určete, kdy se má provést přírůstkové zálohování do Azure.

    Spouštění zálohování můžete naplánovat na každý den, týden, měsíc nebo rok a k datu a v čase, ve kterém se mají spustit. Zálohování se může spouštět až dvakrát denně. Při každém spuštění úlohy zálohování se v Azure vytvoří bod obnovení dat z kopie zálohovaných dat uložených na Azure Backup Server disku.

11. V části **zadat zásady uchovávání online** určete způsob, jakým se v Azure uchovávají body obnovení vytvořené z denních, týdenních nebo měsíčních záloh nebo ročních záloh.

12. V části **Zvolit online replikaci** určete, jak probíhá počáteční Úplná replikace dat.

13. Na **Shrnutí** zkontrolujte nastavení. Když vyberete **vytvořit skupinu**, dojde k počáteční replikaci dat. Po dokončení replikace dat se na stránce **stav** zobrazuje stav skupiny ochrany jako **OK**. Úloha počátečního zálohování probíhá v souladu s nastavením skupiny ochrany.

## <a name="recover-file-data"></a>Obnovit data souboru

K obnovení dat na virtuální počítač použijte Azure Backup Server konzolu.

1. V konzole Azure Backup Server v navigačním panelu vyberte **obnovení** a vyhledejte data, která chcete obnovit. V podokně výsledků tato data vyberte.

2. V kalendáři v části body obnovení jsou data tučně označena jako body obnovení. Vyberte datum, které chcete obnovit.

3. V podokně **obnovitelná položka** vyberte položku, kterou chcete obnovit.

4. Kliknutím na tlačítko **obnovit** v podokně **Akce** otevřete Průvodce obnovením.

5. Data můžete obnovit následujícím postupem:

    * **Obnovit do původního umístění** – Pokud je klientský počítač připojený přes síť VPN, tato možnost nefunguje. Místo toho použijte alternativní umístění a potom zkopírujte data z tohoto umístění.
    * **Obnovit do alternativního umístění**

6. Zadejte možnosti obnovení:

    * V případě **chování obnovení existující verze** vyberte možnost **vytvořit kopii**, **Přeskočit** nebo **přepsat**. Přepsání je k dispozici pouze při obnovování do původního umístění.
    * V části **obnovit zabezpečení** vyberte **použít nastavení cílového počítače** , nebo **použít nastavení zabezpečení verze bodu obnovení**.
    * U **omezení využití šířky pásma sítě** vyberte **Upravit** a povolte omezení využití šířky pásma sítě.
    * **Oznámení** Vyberte možnost **po dokončení obnovení odeslat e-mail** a zadejte příjemce, kteří oznámení obdrží. E-mailové adresy oddělujte čárkami.
    * Po provedení výběrů vyberte **Další** .

7. Zkontrolujte nastavení obnovení a vyberte **obnovit**.

    >[!Note]
    >V průběhu úlohy obnovení se zruší všechny úlohy synchronizace pro vybrané položky obnovení.

Pokud používáte Moderní úložiště zálohování (MB/s), není podporováno obnovení koncového uživatele (EUR) u souborového serveru. Souborového serveru EUR má závislost na služba Stínová kopie svazku (VSS), která Moderní úložiště zálohování nepoužívá. Pokud je povolena EUR, obnovte data pomocí následujících kroků:

1. Přejděte k chráněným souborům a klikněte pravým tlačítkem na název souboru a vyberte **vlastnosti**.

2. V nabídce **vlastnosti** vyberte **předchozí verze** a zvolte verzi, kterou chcete obnovit.

## <a name="view-azure-backup-server-with-a-vault"></a>Zobrazení Azure Backup Server s trezorem

Chcete-li zobrazit Azure Backup Server entit v Azure Portal, můžete postupovat podle následujících kroků:

1. Otevřete Recovery Services trezor.
2. Vyberte infrastrukturu zálohování.
3. Zobrazit servery pro správu zálohování.

## <a name="next-steps"></a>Další kroky

Informace o použití Azure Backup Server k ochraně dalších úloh naleznete v jednom z následujících článků:

* [Zálohování farmy služby SharePoint](./backup-mabs-sharepoint-azure-stack.md)
* [Zálohování serveru SQL](./backup-mabs-sql-azure-stack.md)
