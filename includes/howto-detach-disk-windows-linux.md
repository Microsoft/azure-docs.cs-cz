---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/25/2018
ms.author: cynthn
ms.openlocfilehash: 343ffb8daf19711d6c2a61b819ece92448146446
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735519"
---
Když už nepotřebujete datový disk připojený k virtuálnímu počítači, můžete ho jednoduše odpojit. Odpojení způsobí odebrání disku z virtuálního počítače, ale neodstraní disk z účtu úložiště Azure.

Pokud znovu chcete použít stávající data na disku, můžete ho znovu připojit ke stejnému nebo jinému virtuálnímu počítači.  

> [!NOTE]
> Pokud chcete odpojit disk operačního systému, musíte nejdřív odstranit příslušný virtuální počítač.
>

## <a name="find-the-disk"></a>Vyhledání disku
Pokud neznáte název disku nebo ho chcete před odpojením ověřit, postupujte podle těchto kroků.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Klikněte na **Virtuální počítače** a potom vyberte příslušný virtuální počítač.

3. Na levé straně řídicího panelu virtuálního počítače v části **Nastavení** klikněte na **Disky**.

 Na řídicím panelu virtuálního počítače se zobrazuje název a typ všech připojených disků. Třeba tato obrazovka ukazuje virtuální počítač s jedním diskem operačního systému (OS) a jedním datovým diskem:

    ![Vyhledání datového disku](./media/howto-detach-disk-windows-linux/vmwithdisklist.png)

## <a name="detach-the-disk"></a>Odpojení disku
1. Na webu Azure Portal klikněte na **Virtual Machines** a potom klikněte na název virtuálního počítače s datovým diskem, který chcete odpojit.

2. Na levé straně řídicího panelu virtuálního počítače v části **Nastavení** klikněte na **Disky**.

3. Klikněte na disk, který chcete odpojit.

  ![Určení odpojovaného disku](./media/howto-detach-disk-windows-linux/disklist.png)

4. Na panelu příkazů klikněte na **Odpojit**.

  ![Vyhledání příkazu pro odpojení](./media/howto-detach-disk-windows-linux/diskdetachcommand.png)

5. Kliknutím na **Ano** v potvrzovacím okně odpojíte příslušný disk.

  ![Potvrzení odpojení disku](./media/howto-detach-disk-windows-linux/confirmdetach.png)

Disk zůstává v úložišti, ale už není připojený k virtuálnímu počítači.
