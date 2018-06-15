---
title: Obnovení souborů z Azure do Windows Serveru
description: Tento kurz podrobně popisuje obnovení položek z Azure do Windows Serveru.
services: backup
author: saurabhsensharma
manager: shivamg
keywords: windows server back up; restore files windows server; back up and disaster recovery
ms.service: backup
ms.topic: tutorial
ms.date: 2/14/2018
ms.author: saurse
ms.custom: mvc
ms.openlocfilehash: e05c80e52605e051bdd6815608ca8c12e1393727
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34607017"
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Obnovení souborů z Azure do Windows Serveru

Azure Backup umožňuje obnovení jednotlivých položek ze záloh Windows Serveru. Obnovení jednotlivých souborů je užitečné v případě, že musíte rychle obnovit náhodně odstraněné soubory. Tento kurz popisuje použití agenta služby Microsoft Azure Recovery Services (MARS) k obnovení souborů ze záloh, které jste už provedli v Azure. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Zahájení obnovení jednotlivých položek 
> * Výběr bodu obnovení 
> * Obnovení položek z bodu obnovení

Tento kurz předpokládá, že jste už provedli postup [Zálohování Windows Serveru do Azure](backup-configure-vault.md) a že máte v Azure alespoň jednu zálohu vašich souborů Windows Serveru.

## <a name="initiate-recovery-of-individual-items"></a>Zahájení obnovení jednotlivých položek

Součástí instalace agenta služby Microsoft Azure Recovery Services (MARS) je i užitečný průvodce Microsoft Azure Backup s uživatelským rozhraním. Průvodce Microsoft Azure Backup spolupracuje s agentem služby Microsoft Azure Recovery Services (MARS) a načítá zálohovaná data z bodů obnovení uložených v Azure. Pomocí průvodce Microsoft Azure Backup můžete identifikovat soubory nebo složky, které chcete obnovit do Windows Serveru. 

1. Otevřete modul snap-in **Microsoft Azure Backup**. Najdete ho vyhledáním **Microsoft Azure Backup** ve svém počítači.

    ![Zálohování čeká na zpracování](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. V průvodci klikněte na **Obnovit data** v **podokně Akce** konzoly agenta. Spustí se průvodce **Obnovení dat**.

    ![Zálohování čeká na zpracování](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. Na stránce **Začínáme** vyberte **Tento server (název serveru)** a klikněte na **Další**.

4. Na stránce **Vybrat režim obnovení** vyberte **Jednotlivé soubory a složky** a pak kliknutím na **Další** zahajte proces výběru bodu obnovení.
 
5. Na stránce **Vybrat svazek a datum** vyberte svazek obsahující soubory nebo složky, které chcete obnovit, a klikněte na **Připojit**. Vyberte datum a z rozevírací nabídky vyberte čas odpovídající bodu obnovení. Data zobrazená **tučně** značí, že je pro daný den dostupný alespoň jeden bod obnovení.

    ![Zálohování čeká na zpracování](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)
 
    Když kliknete na **Připojit**, Azure Backup zpřístupní bod obnovení jako disk. Soubory z disku můžete procházet a obnovit.

## <a name="restore-items-from-a-recovery-point"></a>Obnovení položek z bodu obnovení

1. Po připojení svazku pro obnovení kliknutím na **Procházet** otevřete Průzkumníka Windows a vyhledejte soubory a složky, které chcete obnovit. 

    ![Zálohování čeká na zpracování](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    Soubory můžete otevřít přímo ze svazku pro obnovení a ověřit je.

2. V Průzkumníku Windows zkopírujte soubory a složky, které chcete obnovit, a vložte je do libovolného umístění na serveru.

    ![Zálohování čeká na zpracování](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. Jakmile budete hotovi s obnovováním souborů a složek, na stránce **Procházet a obnovit soubory** průvodce **Obnovení dat** klikněte na **Odpojit**. 

    ![Zálohování čeká na zpracování](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4.  Kliknutím na **Ano** potvrďte, že chcete svazek odpojit.

    Po odpojení snímku se v podokně **Úlohy** v konzole agenta zobrazí **Úloha dokončena**.

## <a name="next-steps"></a>Další kroky

Toto je konec kurzů věnovaných zálohování a obnovení dat Windows Serveru do Azure. Další informace o službě Azure Backup najdete v ukázce PowerShellu pro zálohování šifrovaných virtuálních počítačů.

> [!div class="nextstepaction"]
> [Zálohování šifrovaného virtuálního počítače](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
