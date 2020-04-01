---
title: Kurz – zálohování sdílených složek souborů Azure Files
description: V tomto kurzu se dozvíte, jak pomocí portálu Azure nakonfigurovat trezor služby Recovery Services a zálohovat sdílené složky Azure.
ms.date: 06/10/2019
ms.topic: tutorial
ms.openlocfilehash: ec9074a39f2ece7878c0c3ef828dc21748d0ab89
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76293925"
---
# <a name="back-up-azure-file-shares-in-the-azure-portal"></a>Zálohování sdílených složek Azure na webu Azure Portal

Tento kurz vysvětluje, jak používat portál Azure k zálohování [sdílených složek Azure](../storage/files/storage-files-introduction.md).

V této příručce se naučíte:
> [!div class="checklist"]
>
> * Konfigurace trezoru služby Recovery Services pro zálohování Souborů Azure
> * Spuštění úlohy zálohování na vyžádání pro vytvoření bodu obnovení

## <a name="prerequisites"></a>Požadavky

Než budete moct zálohovat sdílenou složku Azure, ujistěte se, že se nachází v jednom z [podporovaných typů účtu úložiště](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Po ověření můžete chránit své sdílené složky.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Omezení pro zálohování sdílení souborů Azure během náhledu

Zálohování sdílených složek Azure je ve verzi Preview. Sdílené složky Azure v účtech úložiště pro obecné účely v1 i pro obecné účely v2 jsou podporované. Následující scénáře zálohování se nepodporují u sdílených složek Azure:

* Pro ochranu souborů Azure pomocí Azure Backup není k dispozici žádné rozhraní příkazového řádku.
* Maximální počet plánovaných záloh je jedna za den.
* Maximální počet záloh na vyžádání jsou čtyři za den.
* Pomocí [uzamčení prostředků](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest) v účtu úložiště zabráníte náhodnému odstranění záloh v trezoru služby Recovery Services.
* Neodstraňujte snímky vytvořené službou Azure Backup. Odstranění snímků může způsobit ztrátu bodů obnovení nebo selhání obnovení.
* Neodstraňujte sdílené složky, které jsou chráněné službou Azure Backup. Aktuální řešení odstraní všechny snímky pořízené službou Azure Backup po odstranění sdílené složky a tím ztratí všechny body obnovení

Zálohování pro sdílené složky Azure v účtech úložiště s replikací [zónově redundantního úložiště](../storage/common/storage-redundancy-zrs.md) (ZRS) je momentálně dostupné jenom ve středních USA (CUS), východních USA (EUS), východních USA 2 (EUS2), severní Evropě (NE), jihovýchodní Asii (SEA), západní Evropě (WE) a západní CH2 (WUS2).

## <a name="configuring-backup-for-an-azure-file-share"></a>Konfigurace zálohování sdílené složky Azure

Tento kurz předpokládá, že už máte vytvořenou sdílenou složku Azure. Zálohování sdílené složky Azure:

1. Vytvořte trezor služby Recovery Services ve stejné oblasti, ve které je vaše sdílená složka. Pokud už trezor máte, otevřete stránku Přehled vašeho trezoru a klikněte na **Zálohování**.

    ![Klikněte na Zálohování na stránce Přehled trezoru.](./media/tutorial-backup-azure-files/overview-backup-page.png)

2. V nabídce **Cíl zálohování** z části Co **chcete zálohovat?**, zvolte Azure FileShare.

    ![Volba sdílené složky Azure jako cíle zálohování](./media/tutorial-backup-azure-files/choose-azure-fileshare-from-backup-goal.png)

3. Kliknutím na **Zálohovat** nakonfigurujete sdílenou složku Azure do trezoru služby Recovery Services.

   ![přidružení sdílené složky Azure k trezoru kliknutím na Zálohovat](./media/tutorial-backup-azure-files/set-backup-goal.png)

    Jakmile je úložiště přidruženo ke sdílené složce Azure, otevře se nabídka Zálohování a zobrazí výzvu k výběru účtu úložiště. V nabídce jsou zobrazeny všechny podporované účty úložiště v oblasti, kde trezor existuje a které ještě nejsou přidruženy k trezoru služby Recovery Services.

   ![Vyberte účet úložiště](./media/tutorial-backup-azure-files/list-of-storage-accounts.png)

4. V seznamu účtů úložiště vyberte účet a klikněte na **OK**. Azure v účtu úložiště vyhledá sdílené složky, které je možné zálohovat. Pokud jste sdílené složky přidali nedávno a v seznamu je nevidíte, chvíli počkejte, než se sdílené složky zobrazí.

   ![Jsou zjidovány sdílené složky.](./media/tutorial-backup-azure-files/discover-file-shares.png)

5. V seznamu **Sdílené soubory** vyberte jednu nebo více sdílených složek, které chcete zálohovat, a klepněte na **tlačítko OK**.

6. Po zvolení sdílených složek se nabídka Zálohování přepne na **Zásady zálohování**. V této nabídce buď vyberte existující zásadu zálohování, nebo vytvořte novou, a pak klikněte na **Povolit zálohování**.

   ![Vyberte zásadu zálohování nebo vytvořte novou](./media/tutorial-backup-azure-files/apply-backup-policy.png)

    Po vytvoření zásady zálohování se v naplánovaném čase pořídí snímek sdílených složek a po zvolenou dobu se bude uchovávat bod obnovení.

## <a name="create-an-on-demand-backup"></a>Vytvoření zálohy na vyžádání

Po konfiguraci zásad zálohování budete chtít vytvořit zálohu na vyžádání, abyste zajistili, že vaše data budou chráněna až do příštího naplánovaného zálohování.

### <a name="to-create-an-on-demand-backup"></a>Vytvoření zálohy na vyžádání

1. Otevřete trezor služby Recovery Services obsahující body obnovení sdílené složky a klikněte na **Zálohování položek**. Zobrazí se seznam typů zálohovaných položek.

   ![Seznam položek zálohování](./media/tutorial-backup-azure-files/list-of-backup-items.png)

2. V seznamu vyberte **Azure Storage (Soubory Azure)**. Zobrazí se seznam sdílených složek Azure.

   ![Seznam sdílených složek Azure](./media/tutorial-backup-azure-files/list-of-azure-files-backup-items.png)

3. V seznamu sdílených složek Azure vyberte požadovanou sdílenou složku. Otevře se nabídka Zálohovaná položka pro vybranou sdílenou složku.

   ![Nabídka Zálohovat položku pro vybranou sdílenou složku](./media/tutorial-backup-azure-files/backup-item-menu.png)

4. V nabídce Zálohovaná položka klikněte na **Zálohovat nyní**. Protože se jedná o úlohu zálohování na vyžádání, není k bodu obnovení přidružená žádná zásada uchovávání. Otevře se dialogové okno **Zálohovat nyní**. Zadejte poslední den, ke kterému chcete uchovávat bod obnovení.

   ![Zvolit datum pro uchování bodu obnovení](./media/tutorial-backup-azure-files/backup-now-menu.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste pomocí webu Azure Portal provedli následující kroky:

> [!div class="checklist"]
>
> * Konfigurace trezoru služby Recovery Services pro zálohování Souborů Azure
> * Spuštění úlohy zálohování na vyžádání pro vytvoření bodu obnovení

Pokračujte k dalšímu článku a obnovte ze zálohy sdílené složky Azure.

> [!div class="nextstepaction"]
> [Obnovení ze zálohy sdílených složek Azure](restore-afs.md)
