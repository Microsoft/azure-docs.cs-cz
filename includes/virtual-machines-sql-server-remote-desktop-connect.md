---
author: rockboyfor
ms.service: virtual-machines-sql
ms.topic: include
origin.date: 10/26/2018
ms.date: 11/26/2018
ms.author: v-yeche
ms.openlocfilehash: fe5daa38c43723c85fb464e191ee4a3e85700e0b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129755"
---
1. Po vytvoření a spuštění virtuálního počítače Azure klikněte na webu Azure Portal na ikonu služby Virtual Machines a zobrazte vaše virtuální počítače.

1. Klikněte na tři tečky (**...**) u vašeho nového virtuálního počítače.

1. Klikněte na **Připojit**.

   ![Připojení k virtuálnímu počítači na portálu](./media/virtual-machines-sql-server-remote-desktop-connect/azure-virtual-machine-connect.png)

1. Otevřete soubor **RDP**, který prohlížeč stáhne pro virtuální počítač.

1. Připojení ke vzdálené ploše vás upozorní, že nelze identifikovat vydavatele tohoto vzdáleného připojení. Pokračujte kliknutím na **Připojit**.

1. V dialogovém okně **Zabezpečení systému Windows** klikněte na **Použít jiný účet**. Možná budete muset kliknout na **Další možnosti**, aby se tato možnost zobrazila. Zadejte uživatelské jméno a heslo, které jste nakonfigurovali při vytváření virtuálního počítače. Před uživatelské jméno je nutné přidat zpětné lomítko.

   ![Ověřování vzdálené plochy](./media/virtual-machines-sql-server-remote-desktop-connect/remote-desktop-connect.png)

1. Připojte se kliknutím na **OK**.

<!--Update_Description: wording update, update link-->