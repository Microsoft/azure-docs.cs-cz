---
title: Zálohování sdílených složek Azure v Azure Portal
description: Naučte se používat Azure Portal k zálohování sdílených složek Azure v trezoru Recovery Services.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: e7f44a71388468be432bdfcb0eb2bf67c0fcc8ef
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519931"
---
# <a name="back-up-azure-file-shares"></a>Zálohování sdílených složek Azure

Tento článek vysvětluje, jak zálohovat [sdílené složky Azure](../storage/files/storage-files-introduction.md) z Azure Portal.

V tomto článku se naučíte:

* Vytvořte trezor služby Recovery Services.
* Konfigurace zálohování z trezoru Recovery Services
* Konfigurace zálohování v podokně sdílení souborů
* Spuštění úlohy zálohování na vyžádání pro vytvoření bodu obnovení

## <a name="prerequisites"></a>Požadavky

* [Přečtěte si](azure-file-share-backup-overview.md) o řešení zálohování založeném na snímku Azure File Share.
* Ujistěte se, že se sdílená složka nachází v jednom z [podporovaných typů účtů úložiště](azure-file-share-support-matrix.md).
* Identifikujte nebo vytvořte [trezor Recovery Services](#create-a-recovery-services-vault) ve stejné oblasti jako účet úložiště, který hostuje sdílenou složku.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="configure-backup-from-the-recovery-services-vault"></a>Konfigurace zálohování z trezoru Recovery Services

Následující postup vysvětluje, jak můžete v podokně Recovery Services trezoru nakonfigurovat zálohování pro více sdílených složek:

1. V [Azure Portal](https://portal.azure.com/)otevřete trezor Recovery Services, který chcete použít pro konfiguraci zálohování sdílené složky.

1. V podokně **Recovery Services trezor** vyberte **+ Backup** v nabídce v horní části.

   ![Trezor služby Recovery Services](./media/backup-afs/recovery-services-vault.png)

    1. V podokně **cíl zálohování** nastavte v rozevíracím seznamu možnost **Azure** v rozevíracím seznamu, **kde je spuštěná vaše úloha?** do **Azure** .

          ![Výběr úlohy Azure jako úlohy](./media/backup-afs/backup-goal.png)

    2. V části **co chcete zálohovat?** v rozevíracím seznamu vyberte **Azure File Share** .

          ![Vybrat sdílenou složku Azure](./media/backup-afs/select-azure-file-share.png)

    3. Vyberte **zálohování** pro registraci rozšíření sdílené složky Azure v trezoru.

          ![Vyberte zálohování pro přidružení sdílené složky Azure k trezoru.](./media/backup-afs/register-extension.png)

1. Po výběru **zálohy** se otevře podokno **zálohování** . Pokud chcete vybrat účet úložiště, který hostuje sdílenou složku, kterou chcete chránit, vyberte v textovém poli **účtu úložiště** text odkazu **Vybrat** odkaz.

   ![Vybrat odkaz pro výběr](./media/backup-afs/choose-select-link.png)

1. Na pravé straně se otevře **podokno vybrat účet úložiště** , ve kterém se zobrazí sada zjištěných podporovaných účtů úložiště. Jsou buď přidruženy k tomuto trezoru, nebo přítomny ve stejné oblasti jako trezor, ale ještě nejsou přidružené k žádnému Recovery Servicesmu trezoru.

1. V seznamu zjištěných účtů úložiště vyberte účet a vyberte **OK**.

   ![Vyberte ze zjištěných účtů úložiště.](./media/backup-afs/select-discovered-storage-account.png)

1. V dalším kroku vyberte sdílené složky, které chcete zálohovat. V části **sdílené složky pro zálohování** vyberte tlačítko **Přidat** .

   ![Vyberte sdílené složky, které chcete zálohovat.](./media/backup-afs/select-file-shares-to-back-up.png)

1. Na pravé straně se otevře podokno **vybrat sdílené složky pro sdílení souborů** . Azure vyhledá v účtu úložiště sdílené složky, které se dají zálohovat. Pokud jste nedávno přidali sdílené složky a nevidíte je v seznamu, počkejte, než se sdílené složky zobrazí.

1. V seznamu **vybrat sdílené složky** vyberte jednu nebo více sdílených složek, které chcete zálohovat. Vyberte **OK**.

   ![Vybrat sdílené složky](./media/backup-afs/select-file-shares.png)

1. Pokud chcete pro sdílenou složku zvolit zásady zálohování, máte tři možnosti:

   * Vyberte výchozí zásadu.<br>
   Tato možnost umožňuje povolit každodenní zálohování, které se bude uchovávat po dobu 30 dnů. Pokud v trezoru nemáte existující zásady zálohování, otevře se podokno zálohování s výchozími nastaveními zásad. Pokud chcete zvolit výchozí nastavení, můžete vybrat možnost **Povolit zálohování** přímo.

   * Vytvoření nové zásady <br>

      1. Pokud chcete pro sdílenou složku vytvořit nové zásady zálohování, vyberte v části **zásady zálohování** text odkazu pod rozevíracím seznamem.<br>

         ![Vytvořit novou zásadu](./media/backup-afs/create-new-policy.png)

      1. Na pravé straně se otevře kontextový podokno **zásady zálohování** . Do textového pole zadejte název zásady a v závislosti na vašem požadavku zvolte dobu uchování. Ve výchozím nastavení je povolená jenom možnost denní uchování. Pokud chcete mít týdenní, měsíční nebo roční uchování, zaškrtněte příslušné políčko a zadejte požadovanou hodnotu pro uchování.

      1. Po zadání hodnot uchování a platného názvu zásady vyberte **OK**.<br>

         ![Zadejte název zásady a hodnoty uchování.](./media/backup-afs/policy-name.png)

   * Vyberte jednu z existujících zásad zálohování. <br>

      Pokud chcete zvolit jednu ze stávajících zásad zálohování pro konfiguraci ochrany, vyberte požadovanou zásadu v rozevíracím seznamu **zásady zálohování** .<br>

      ![Zvolit existující zásadu](./media/backup-afs/choose-existing-policy.png)

1. Chcete-li začít chránit sdílenou složku, vyberte možnost **Povolit zálohování** .

   ![Zvolit povolit zálohování](./media/backup-afs/enable-backup.png)

Po nastavení zásad zálohování se v naplánovaném čase povede snímek sdílených složek. Bod obnovení je uložen také pro zvolené období.

>[!NOTE]
>Azure Backup teď podporuje zásady s denním/týdenním/měsíčním/ročním uchováváním záloh sdílené složky Azure.

## <a name="configure-backup-from-the-file-share-pane"></a>Konfigurace zálohování v podokně sdílení souborů

Následující postup vysvětluje, jak můžete nakonfigurovat zálohování pro jednotlivé sdílené složky z příslušného podokna sdílení souborů:

1. V [Azure Portal](https://portal.azure.com/)otevřete účet úložiště, který hostuje sdílenou složku, kterou chcete zálohovat.

1. V účtu úložiště vyberte dlaždici s názvem **sdílené složky**. Můžete také přejít ke **sdíleným složkám souborů** prostřednictvím obsahu účtu úložiště.

   ![Účet úložiště](./media/backup-afs/storage-account.png)

1. V seznamu sdílení souborů byste měli vidět všechny sdílené složky, které jsou v účtu úložiště k dispozici. Vyberte sdílenou složku, kterou chcete zálohovat.

   ![Seznam sdílených složek](./media/backup-afs/file-shares-list.png)

1. V části **operace** v podokně sdílení souborů vyberte **zálohovat** . Napravo se načte podokno **Konfigurace zálohování** .

   ![Konfigurovat podokno zálohování](./media/backup-afs/configure-backup.png)

1. Pro výběr trezoru Recovery Services proveďte jednu z následujících akcí:

    * Pokud už máte trezor, vyberte přepínač **Vybrat existující** Recovery Services trezor a v rozevírací nabídce **název trezoru** zvolte jednu z existujících trezorů.

       ![Vybrat existující trezor](./media/backup-afs/select-existing-vault.png)

    * Pokud nemáte trezor, vyberte přepínač **vytvořit nový** Recovery Services trezor. Zadejte název trezoru. Vytvoří se ve stejné oblasti jako sdílená složka. Ve výchozím nastavení se trezor vytvoří ve stejné skupině prostředků jako sdílená složka. Pokud chcete zvolit jinou skupinu prostředků, vyberte **vytvořit nový** odkaz pod rozevíracím tlačítkem **typ prostředku** a zadejte název skupiny prostředků. Pokračujte výběrem tlačítka **OK**.

       ![Vytvořit nový trezor](./media/backup-afs/create-new-vault.png)

      >[!IMPORTANT]
      >Pokud je účet úložiště zaregistrovaný v trezoru nebo pokud je v rámci účtu úložiště hostující sdílená složka, kterou chcete chránit, název Recovery Services trezoru bude předem vyplněný a nebudete ho moct upravit. [Další informace najdete tady](backup-azure-files-faq.yml#why-can-t-i-change-the-vault-to-configure-backup-for-the-file-share-).

1. Pro výběr **zásad zálohování** proveďte jednu z následujících akcí:

    * Ponechte výchozí zásadu. Naplánuje denní zálohy se uchováváním po dobu 30 dnů.

    * Vyberte existující zásadu zálohování (pokud ji máte) z rozevírací nabídky **zásady zálohování** .

       ![Zvolit zásady zálohování](./media/backup-afs/choose-backup-policy.png)

    * Vytvořte novou zásadu s denním/týdenním/měsíčním a ročním uchováváním podle vašeho požadavku.  

         1. Vyberte odkaz **vytvořit nový text zásady** .

         2. Na pravé straně se otevře kontextový podokno **zásady zálohování** . Do textového pole zadejte název zásady a v závislosti na vašem požadavku zvolte dobu uchování. Ve výchozím nastavení je povolená jenom možnost denní uchování. Pokud chcete mít týdenní, měsíční nebo roční uchování, zaškrtněte příslušné políčko a zadejte požadovanou hodnotu pro uchování.

         3. Po zadání hodnot uchování a platného názvu zásady vyberte **OK**.

            ![Vytvořit nové zásady zálohování](./media/backup-afs/create-new-backup-policy.png)

1. Chcete-li začít chránit sdílenou složku, vyberte možnost **Povolit zálohování** .

   ![Vyberte povolit zálohování.](./media/backup-afs/select-enable-backup.png)

1. Průběh konfigurace můžete sledovat v oznámeních na portálu nebo sledovat úlohy zálohování v trezoru, který používáte k ochraně sdílení souborů.

   ![Oznámení na portálu](./media/backup-afs/portal-notifications.png)

1. Po dokončení operace konfigurace zálohování vyberte v části **operace** v podokně sdílení souborů možnost **zálohování** . V kontextovém podokně se zobrazí **základy trezoru** , které se načtou napravo. Odtud můžete aktivovat operace zálohování a obnovení na vyžádání.

   ![Základy trezoru](./media/backup-afs/vault-essentials.png)

## <a name="run-an-on-demand-backup-job"></a>Spuštění úlohy zálohování na vyžádání

V některých případech můžete chtít vytvořit snímek zálohy nebo bod obnovení mimo čas naplánovaný v zásadách zálohování. Běžným důvodem pro vygenerování zálohy na vyžádání je napravo od nakonfigurované zásady zálohování. Na základě plánu v zásadách zálohování může být hodiny nebo dny až do chvíle, kdy se snímek povede. Pokud chcete svá data chránit před zapojením zásady zálohování, vyvolejte zálohování na vyžádání. Vytvoření zálohy na vyžádání se často vyžaduje předtím, než provedete plánované změny sdílených složek.

### <a name="from-the-recovery-services-vault"></a>Z trezoru Recovery Services

1. Otevřete Recovery Services trezor, který jste použili k zálohování sdílené složky. V podokně **Přehled** vyberte **položky zálohování** v části **chráněné položky** .

   ![Vybrat zálohované položky](./media/backup-afs/backup-items.png)

1. Po výběru **položek zálohování** se vedle podokna **přehledu** zobrazí nové podokno se seznamem všech **typů správy zálohování** .

   ![Seznam typů správy zálohování](./media/backup-afs/backup-management-types.png)

1. V seznamu **typ správy zálohování** vyberte možnost **Azure Storage (soubory Azure)**. Zobrazí se seznam všech sdílených složek a odpovídajících účtů úložiště, které se zálohují pomocí tohoto trezoru.

   ![Zálohované položky Azure Storage (soubory Azure)](./media/backup-afs/azure-files-backup-items.png)

1. V seznamu sdílených složek Azure vyberte sdílenou složku, kterou chcete. Zobrazí se podrobnosti o **zálohovaných položkách** . V nabídce **zálohovaná položka** vyberte **Zálohovat nyní**. Vzhledem k tomu, že je tato úloha zálohování na vyžádání, nejsou k bodu obnovení přidruženy žádné zásady uchovávání.

   ![Vyberte zálohovat hned](./media/backup-afs/backup-now.png)

1. Otevře se podokno **Zálohovat nyní** . Zadejte poslední den, ke kterému chcete uchovávat bod obnovení. Pro zálohování na vyžádání můžete mít maximálně 10 let.

   ![Zvolit datum uchovávání](./media/backup-afs/retention-date.png)

1. Výběrem **OK** potvrďte spuštěnou úlohu zálohování na vyžádání.

1. Monitorujte oznámení na portálu, abyste měli přehled o dokončení spuštění úlohy zálohování. Průběh úlohy můžete monitorovat na řídicím panelu trezoru. Vyberte **probíhající úlohy zálohování**  >  .

### <a name="from-the-file-share-pane"></a>Z podokna sdílení souborů

1. Otevřete podokno s **přehledem** sdílené složky, ve kterém chcete provést zálohování na vyžádání.

1. V části **operace** vyberte **zálohování** . V kontextovém podokně se zobrazí **základy trezoru** , které se načtou napravo. Vyberte **Zálohovat nyní** a proveďte zálohování na vyžádání.

   ![Vyberte zálohovat hned](./media/backup-afs/select-backup-now.png)

1. Otevře se podokno **Zálohovat nyní** . Zadejte uchování bodu obnovení. Pro zálohování na vyžádání můžete mít maximálně 10 let.

   ![Uchovat datum zálohování](./media/backup-afs/retain-backup-date.png)

1. Vyberte **OK**. Tím akci potvrdíte.

>[!NOTE]
>Azure Backup uzamkne účet úložiště při konfiguraci ochrany pro jakoukoli sdílenou složku v příslušném účtu. Tato operace zajišťuje ochranu před náhodným odstraněním účtu úložiště s zálohovanými sdílenými složkami.

## <a name="best-practices"></a>Osvědčené postupy

* Neodstraňujte snímky vytvořené pomocí Azure Backup. Odstranění snímků může způsobit ztrátu bodů obnovení nebo selhání obnovení.

* Neodstraňujte zámek pořízený na účtu úložiště pomocí Azure Backup. Pokud zámek odstraníte, váš účet úložiště bude náchylný k nechtěnému odstranění a pokud je odstraněný, ztratíte snímky nebo zálohy.

## <a name="next-steps"></a>Další kroky

Naučte se:

* [Obnovení sdílených složek Azure](restore-afs.md)
* [Správa záloh sdílených složek Azure](manage-afs-backup.md)
