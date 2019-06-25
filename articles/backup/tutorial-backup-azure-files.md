---
title: Zálohování sdílených složek soubory Azure pomocí služby Azure Backup
description: Tento kurz vysvětluje, jak k zálohování sdílených složek Azure.
services: backup
author: dcurwin
ms.author: dacurwin
ms.date: 06/10/2019
ms.topic: tutorial
ms.service: backup
manager: carmonm
ms.openlocfilehash: 474d5454e30c35d3f3ccf4ea994093ef47bd6ceb
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275989"
---
# <a name="back-up-azure-file-shares"></a>Zálohování sdílených složek Azure
Tento článek vysvětluje, jak pomocí webu Azure Portal zálohovat a obnovovat [sdílené složky Azure](../storage/files/storage-files-introduction.md).

V této příručce se naučíte:
> [!div class="checklist"]
> * Konfigurace trezoru služby Recovery Services pro zálohování Souborů Azure
> * Spuštění úlohy zálohování na vyžádání pro vytvoření bodu obnovení


## <a name="prerequisites"></a>Požadavky
Než budete moct zálohovat sdílenou složku Azure, ujistěte se, že se nachází v jednom z [podporovaných typů účtu úložiště](tutorial-backup-azure-files.md#limitations-for-azure-file-share-backup-during-preview). Po ověření můžete chránit své sdílené složky.

## <a name="limitations-for-azure-file-share-backup-during-preview"></a>Omezení pro zálohování sdílené složky Azure ve verzi preview
Zálohování sdílených složek Azure je ve verzi Preview. Sdílených složek Azure v obou pro obecné účely v1 a účty úložiště pro obecné účely v2 jsou podporovány. Následující scénáře zálohování se nepodporují u sdílených složek Azure:
- Nemůžete chránit sdílené složky Azure v účtech úložiště s povolenými virtuálními sítěmi nebo bránou firewall.
- Není k dispozici pro ochranu souborů Azure pomocí služby Azure Backup žádné rozhraní příkazového řádku.
- Maximální počet plánovaných záloh je jedna za den.
- Maximální počet záloh na vyžádání jsou čtyři za den.
- Používejte v účtu úložiště [zámky prostředků](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest), abyste zabránili nechtěnému odstranění záloh v trezoru služby Recovery Services.
- Neodstraňujte snímky vytvořené službou Azure Backup. Odstranění snímků může způsobit ztrátu bodů obnovení nebo selhání obnovení.
- Odstranění sdílené složky, které jsou chráněné službou Azure Backup. Aktuální řešení se odstraní přístup ke všem snímkům pořízeným službou Azure Backup po odstranění sdílené složky a proto dojít ke ztrátě všech bodů obnovení

Zpět do sdílených složek Azure v účtech úložiště s [zónově redundantního úložiště](../storage/common/storage-redundancy-zrs.md) replikace (ZRS) je teď dostupná jenom v centrální USA (CUS), východní USA (EUS), východní USA 2 (EUS2), Severní Evropa (NE), jihovýchodní Asie (SEA), západní Evropa (WE) a USA – západ 2 (WUS2).

## <a name="configuring-backup-for-an-azure-file-share"></a>Konfigurace zálohování sdílené složky Azure
Tento kurz předpokládá, že už máte vytvořenou sdílenou složku Azure. Zálohování sdílené složky Azure:

1. Vytvořte trezor služby Recovery Services ve stejné oblasti, ve které je vaše sdílená složka. Pokud už trezor máte, otevřete stránku Přehled vašeho trezoru a klikněte na **Zálohování**.

    ![Na stránce přehled vašeho trezoru kliknutím na zálohovat](./media/backup-file-shares/overview-backup-page.png)

2. V **cíl zálohování** nabídky, z **co chcete zálohovat?** , volba sdílené složky Azure.

    ![Volba sdílené složky Azure jako cíle zálohování](./media/backup-file-shares/choose-azure-fileshare-from-backup-goal.png)

3. Kliknutím na **Zálohovat** nakonfigurujte zálohování sdílené složky Azure do vašeho trezoru služby Recovery Services.

   ![přidružení sdílené složky Azure k trezoru kliknutím na Zálohovat](./media/backup-file-shares/set-backup-goal.png)

    Po trezor je spojen s sdílenou složku Azure, v nabídce Backup otevře a výzva k výběru účtu úložiště. V nabídce Zobrazit všechny podporované účty úložiště v oblasti, ve kterém je váš trezor, které nejsou přidruženy k trezoru služby Recovery Services.

   ![Vyberte svůj účet úložiště](./media/backup-file-shares/list-of-storage-accounts.png)

4. V seznamu účtů úložiště vyberte účet a klikněte na **OK**. Azure v účtu úložiště vyhledá sdílené složky, které je možné zálohovat. Pokud jste sdílené složky přidali nedávno a v seznamu je nevidíte, chvíli počkejte, než se sdílené složky zobrazí.

   ![Zjišťování sdílené složky](./media/backup-file-shares/discover-file-shares.png)

5. Z **sdílené složky** seznamu, vyberte jednu nebo více sdílených složek, kterou chcete zálohovat a klikněte na **OK**.

6. Po zvolení sdílených složek se nabídka Zálohování přepne na **Zásady zálohování**. V této nabídce buď vyberte existující zásadu zálohování, nebo vytvořte novou, a pak klikněte na **Povolit zálohování**.

   ![Zásady zálohování vyberte nebo vytvořte novou](./media/backup-file-shares/apply-backup-policy.png)

    Po vytvoření zásady zálohování se v naplánovaném čase pořídí snímek sdílených složek a po zvolenou dobu se bude uchovávat bod obnovení.

## <a name="create-an-on-demand-backup"></a>Vytvoření zálohy na vyžádání
Po konfiguraci zásady zálohování, budete chtít vytvořit zálohu na vyžádání k zajištění, že vaše data jsou chráněna až do další naplánované zálohování.


### <a name="to-create-an-on-demand-backup"></a>Vytvoření zálohy na vyžádání

1. Otevřete trezor služby Recovery Services obsahující body obnovení sdílené složky a klikněte na **Zálohování položek**. Zobrazí se seznam typů zálohovaných položek.

   ![Seznam zálohovaných položek](./media/backup-file-shares/list-of-backup-items.png)

2. V seznamu vyberte **Azure Storage (Soubory Azure)** . Zobrazí se seznam sdílených složek Azure.

   ![Seznam sdílených složek Azure](./media/backup-file-shares/list-of-azure-files-backup-items.png)

3. V seznamu sdílených složek Azure vyberte požadovanou sdílenou složku. Otevře se nabídka Zálohovaná položka pro vybranou sdílenou složku.

   ![Zálohování položek nabídky pro vybraný soubor sdílet](./media/backup-file-shares/backup-item-menu.png)

4. V nabídce Zálohovaná položka klikněte na **Zálohovat nyní**. Protože se jedná o úlohu zálohování na vyžádání, není k bodu obnovení přidružená žádná zásada uchovávání. Otevře se dialogové okno **Zálohovat nyní**. Zadejte poslední den, ke kterému chcete uchovávat bod obnovení.

   ![Zvolte datum pro uchování bodu obnovení](./media/backup-file-shares/backup-now-menu.png)


## <a name="next-steps"></a>Další postup

V tomto kurzu jste pomocí webu Azure Portal provedli následující kroky:

> [!div class="checklist"]
> * Konfigurace trezoru služby Recovery Services pro zálohování Souborů Azure
> * Spuštění úlohy zálohování na vyžádání pro vytvoření bodu obnovení

Pokračujte k dalšímu článku obnovit ze zálohy sdílené složky Azure.

> [!div class="nextstepaction"]
> [Obnovení ze zálohy sdílených složek Azure](./backup-azure-files.md#restore-from-backup-of-azure-file-share)
 
