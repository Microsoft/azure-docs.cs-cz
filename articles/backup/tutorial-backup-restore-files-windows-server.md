---
title: 'Kurz: obnovení položek do Windows serveru'
description: V tomto kurzu se naučíte používat agenta Microsoft Azure Recovery Services agenta (MARS) k obnovení položek z Azure do Windows serveru.
ms.topic: tutorial
ms.date: 02/14/2018
ms.custom: mvc
ms.openlocfilehash: 746c901747cf1c0b87612a31fbabcb657d5c4a0c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88263108"
---
# <a name="recover-files-from-azure-to-a-windows-server"></a>Obnovení souborů z Azure do Windows Serveru

Azure Backup umožňuje obnovení jednotlivých položek ze záloh Windows Serveru. Obnovení jednotlivých souborů je užitečné v případě, že musíte rychle obnovit náhodně odstraněné soubory. Tento kurz popisuje použití agenta služby Microsoft Azure Recovery Services (MARS) k obnovení souborů ze záloh, které jste už provedli v Azure. Co se v tomto kurzu naučíte:

> [!div class="checklist"]
>
> * Zahájení obnovení jednotlivých položek
> * Výběr bodu obnovení
> * Obnovení položek z bodu obnovení

V tomto kurzu se předpokládá, že jste už provedli postup [zálohování Windows serveru do Azure](backup-windows-with-mars-agent.md) a máte aspoň jednu zálohu souborů Windows serveru v Azure.

## <a name="initiate-recovery-of-individual-items"></a>Zahájení obnovení jednotlivých položek

Součástí instalace agenta služby Microsoft Azure Recovery Services (MARS) je i užitečný průvodce Microsoft Azure Backup s uživatelským rozhraním. Průvodce Microsoft Azure Backup spolupracuje s agentem služby Microsoft Azure Recovery Services (MARS) a načítá zálohovaná data z bodů obnovení uložených v Azure. Pomocí průvodce Microsoft Azure Backup můžete identifikovat soubory nebo složky, které chcete obnovit do Windows Serveru.

1. Otevřete modul snap-in **Microsoft Azure Backup**. Najdete ho vyhledáním **Microsoft Azure Backup** ve svém počítači.

    ![Modul snap-in Microsoft Azure Backup](./media/tutorial-backup-restore-files-windows-server/mars.png)

2. V průvodci vyberte možnost **obnovit data** v **podokně akce** konzoly agenta a spusťte tak průvodce **obnovením dat** .

    ![Výběr obnovení dat](./media/tutorial-backup-restore-files-windows-server/mars-recover-data.png)

3. Na stránce **Začínáme** vyberte možnost **Server (název serveru)** a klikněte na tlačítko **Další**.

4. Na stránce **Vybrat režim obnovení** vyberte **jednotlivé soubory a složky** a potom kliknutím na tlačítko **Další** zahajte proces výběru bodu obnovení.

5. Na stránce **Vybrat svazek a datum** vyberte svazek, který obsahuje soubory nebo složky, které chcete obnovit, a vyberte **připojit**. Vyberte datum a z rozevírací nabídky vyberte čas odpovídající bodu obnovení. Data zobrazená **tučně** značí, že je pro daný den dostupný alespoň jeden bod obnovení.

    ![Vybrat svazek a datum](./media/tutorial-backup-restore-files-windows-server/mars-select-date.png)

    Když vyberete **připojit**, Azure Backup bude bod obnovení dostupný jako disk. Soubory z disku můžete procházet a obnovit.

## <a name="restore-items-from-a-recovery-point"></a>Obnovení položek z bodu obnovení

1. Jakmile je svazek pro obnovení připojený, vyberte **Procházet** a spusťte Průzkumníka Windows a vyhledejte soubory a složky, které chcete obnovit.

    ![Vyberte Procházet.](./media/tutorial-backup-restore-files-windows-server/mars-browse-recover.png)

    Soubory můžete otevřít přímo ze svazku pro obnovení a ověřit je.

2. V Průzkumníku Windows zkopírujte soubory a složky, které chcete obnovit, a vložte je do libovolného požadovaného umístění na serveru.

    ![Zkopírování souborů a složek](./media/tutorial-backup-restore-files-windows-server/mars-final.png)

3. Po dokončení obnovování souborů a složek na stránce **Procházet a obnovit soubory** v průvodci **obnovením dat** vyberte **Odpojit**.

    ![Vybrat odpojit](./media/tutorial-backup-restore-files-windows-server/unmount-and-confirm.png)

4. Výběrem **Ano** potvrďte, že chcete odpojit svazek.

    Po odpojení snímku se v podokně **Úlohy** v konzole agenta zobrazí **Úloha dokončena**.

## <a name="next-steps"></a>Další kroky

Toto je konec kurzů věnovaných zálohování a obnovení dat Windows Serveru do Azure. Další informace o službě Azure Backup najdete v ukázce PowerShellu pro zálohování šifrovaných virtuálních počítačů.

> [!div class="nextstepaction"]
> [Zálohování šifrovaného virtuálního počítače](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
