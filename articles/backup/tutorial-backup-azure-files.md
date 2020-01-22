---
title: Kurz – zálohování sdílených složek souborů Azure
description: V tomto kurzu se naučíte, jak pomocí Azure Portal nakonfigurovat trezor Recovery Services a zálohovat sdílené složky Azure.
ms.date: 06/10/2019
ms.topic: tutorial
ms.openlocfilehash: ec9074a39f2ece7878c0c3ef828dc21748d0ab89
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293925"
---
# <a name="back-up-azure-file-shares-in-the-azure-portal"></a>Zálohování sdílených složek Azure v Azure Portal

V tomto kurzu se dozvíte, jak použít Azure Portal k zálohování [sdílených složek Azure](../storage/files/storage-files-introduction.md).

V této příručce se naučíte:
> [!div class="checklist"]
>
> * Konfigurace trezoru služby Recovery Services pro zálohování Souborů Azure
> * Spuštění úlohy zálohování na vyžádání pro vytvoření bodu obnovení

## <a name="prerequisites"></a>Požadavky

Než budete moct zálohovat sdílenou složku Azure, ujistěte se, že se nachází v jednom z [podporovaných typů účtu úložiště](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Po ověření můžete chránit své sdílené složky.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Omezení zálohování sdílených složek Azure ve verzi Preview

Zálohování sdílených složek Azure je ve verzi Preview. Podporují se sdílené složky Azure v účtech úložiště pro obecné účely v1 i pro obecné účely v2. Následující scénáře zálohování se nepodporují u sdílených složek Azure:

* K ochraně souborů Azure pomocí Azure Backup není k dispozici žádné rozhraní příkazového řádku.
* Maximální počet plánovaných záloh je jedna za den.
* Maximální počet záloh na vyžádání jsou čtyři za den.
* Používejte v účtu úložiště [zámky prostředků](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest), abyste zabránili nechtěnému odstranění záloh v trezoru služby Recovery Services.
* Neodstraňujte snímky vytvořené službou Azure Backup. Odstranění snímků může způsobit ztrátu bodů obnovení nebo selhání obnovení.
* Neodstraňujte sdílené složky, které jsou chráněné pomocí Azure Backup. Aktuální řešení odstraní všechny snímky vybrané Azure Backup, když se odstraní sdílená složka, a proto ztratí všechny body obnovení.

Zálohování pro sdílené složky Azure v účtech úložiště s replikací [zóny redundantního úložiště](../storage/common/storage-redundancy-zrs.md) (ZRS) je aktuálně k dispozici pouze v střed USA (kapacitní jednotky), východní USA (EUS), východní USA 2 (EUS2), Severní Evropa (ne), jihovýchodní Asie (moře), západní Evropa (We) a západní USA 2 (WUS2).

## <a name="configuring-backup-for-an-azure-file-share"></a>Konfigurace zálohování sdílené složky Azure

Tento kurz předpokládá, že už máte vytvořenou sdílenou složku Azure. Zálohování sdílené složky Azure:

1. Vytvořte trezor služby Recovery Services ve stejné oblasti, ve které je vaše sdílená složka. Pokud už trezor máte, otevřete stránku Přehled vašeho trezoru a klikněte na **Zálohování**.

    ![Na stránce s přehledem vašeho trezoru klikněte na zálohovat.](./media/tutorial-backup-azure-files/overview-backup-page.png)

2. V nabídce **cíl zálohování** z části **co chcete zálohovat?** vyberte sdílená složka Azure.

    ![Volba sdílené složky Azure jako cíle zálohování](./media/tutorial-backup-azure-files/choose-azure-fileshare-from-backup-goal.png)

3. Kliknutím na **Zálohovat** nakonfigurujte zálohování sdílené složky Azure do vašeho trezoru služby Recovery Services.

   ![přidružení sdílené složky Azure k trezoru kliknutím na Zálohovat](./media/tutorial-backup-azure-files/set-backup-goal.png)

    Po přidružení trezoru ke sdílené složce Azure se otevře nabídka zálohování a zobrazí se výzva k výběru účtu úložiště. V nabídce se zobrazí všechny podporované účty úložiště v oblasti, ve které se nachází trezor, který už není přidružený k trezoru Recovery Services.

   ![Vyberte svůj účet úložiště.](./media/tutorial-backup-azure-files/list-of-storage-accounts.png)

4. V seznamu účtů úložiště vyberte účet a klikněte na **OK**. Azure v účtu úložiště vyhledá sdílené složky, které je možné zálohovat. Pokud jste sdílené složky přidali nedávno a v seznamu je nevidíte, chvíli počkejte, než se sdílené složky zobrazí.

   ![Hledají se sdílené složky.](./media/tutorial-backup-azure-files/discover-file-shares.png)

5. V seznamu **sdílené složky** vyberte jednu nebo více sdílených souborů, které chcete zálohovat, a klikněte na tlačítko **OK**.

6. Po zvolení sdílených složek se nabídka Zálohování přepne na **Zásady zálohování**. V této nabídce buď vyberte existující zásadu zálohování, nebo vytvořte novou, a pak klikněte na **Povolit zálohování**.

   ![Vyberte zásadu zálohování nebo vytvořte novou.](./media/tutorial-backup-azure-files/apply-backup-policy.png)

    Po vytvoření zásady zálohování se v naplánovaném čase pořídí snímek sdílených složek a po zvolenou dobu se bude uchovávat bod obnovení.

## <a name="create-an-on-demand-backup"></a>Vytvoření zálohy na vyžádání

Po nakonfigurování zásad zálohování budete chtít vytvořit zálohu na vyžádání, abyste zajistili ochranu dat před dalším naplánovaným zálohováním.

### <a name="to-create-an-on-demand-backup"></a>Vytvoření zálohy na vyžádání

1. Otevřete trezor služby Recovery Services obsahující body obnovení sdílené složky a klikněte na **Zálohování položek**. Zobrazí se seznam typů zálohovaných položek.

   ![Seznam zálohovaných položek](./media/tutorial-backup-azure-files/list-of-backup-items.png)

2. V seznamu vyberte **Azure Storage (Soubory Azure)** . Zobrazí se seznam sdílených složek Azure.

   ![Seznam sdílených složek Azure](./media/tutorial-backup-azure-files/list-of-azure-files-backup-items.png)

3. V seznamu sdílených složek Azure vyberte požadovanou sdílenou složku. Otevře se nabídka Zálohovaná položka pro vybranou sdílenou složku.

   ![Nabídka zálohovaná položka pro vybranou sdílenou složku](./media/tutorial-backup-azure-files/backup-item-menu.png)

4. V nabídce Zálohovaná položka klikněte na **Zálohovat nyní**. Protože se jedná o úlohu zálohování na vyžádání, není k bodu obnovení přidružená žádná zásada uchovávání. Otevře se dialogové okno **Zálohovat nyní**. Zadejte poslední den, ke kterému chcete uchovávat bod obnovení.

   ![Zvolit datum pro uchování bodu obnovení](./media/tutorial-backup-azure-files/backup-now-menu.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste pomocí webu Azure Portal provedli následující kroky:

> [!div class="checklist"]
>
> * Konfigurace trezoru služby Recovery Services pro zálohování Souborů Azure
> * Spuštění úlohy zálohování na vyžádání pro vytvoření bodu obnovení

Přejděte k dalšímu článku, který se má obnovit ze zálohy sdílené složky Azure.

> [!div class="nextstepaction"]
> [Obnovení ze zálohy sdílených složek Azure](restore-afs.md)
