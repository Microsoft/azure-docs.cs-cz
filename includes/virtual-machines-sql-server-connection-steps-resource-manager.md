---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 51dc04fbef8d09878f33d7fda6f15039d3afba3e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028238"
---
### <a name="configure-a-dns-label-for-the-public-ip-address"></a>Konfigurace názvu DNS veřejné IP adresy

Pokud se chcete z internetu připojit k databázovému stroji SQL Serveru, zvažte vytvoření názvu DNS pro vaši veřejnou IP adresu. Můžete se připojit pomocí IP adresy, ale název DNS vytvoří záznam A, který je snadnější identifikovat a který abstrahuje základní veřejnou IP adresu.

> [!NOTE]
> Názvy DNS nejsou nutné, pokud se chcete připojit k instanci systému SQL Server jenom v rámci stejné virtuální sítě nebo jenom místně.

Pokud chcete vytvořit název DNS, nejdřív na portálu vyberte **Virtuální počítače**. Vyberte virtuální počítač se systémem SQL Server, abyste zobrazili jeho vlastnosti.

1. V přehledu virtuálního počítače vyberte své nastavení **Veřejná IP adresa**.

    ![Veřejná IP adresa](./media/virtual-machines-sql-server-connection-steps/rm-public-ip-address.png)

1. Ve vlastnostech veřejné IP adresa rozbalte položku **Konfigurace**.

1. Zadejte název DNS. Tento název je záznam A, který se dá použít k přímému připojení k vašemu virtuálnímu počítači se systémem SQL Server pomocí názvu, namísto pomocí IP adresy.

1. Klikněte na tlačítko **Uložit**.

    ![Název DNS](./media/virtual-machines-sql-server-connection-steps/rm-dns-label.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>Připojení k databázovému stroji z jiného počítače

1. Na počítači připojeném k internetu otevřete aplikaci SSMS (SQL Server Management Studio). Pokud aplikaci SQL Server Management Studio nemáte, [tady](/sql/ssms/download-sql-server-management-studio-ssms) si ji můžete stáhnout.

1. V dialogovém okně **Připojit k serveru** nebo **Connect to Database Engine** (Připojit k databázovému stroji) upravte hodnotu **Název serveru**. Zadejte IP adresu nebo úplný název DNS virtuálního počítače (určený v předchozí úloze). Můžete také přidat čárku a zadat port TCP SQL Serveru. Například, `mysqlvmlabel.eastus.cloudapp.azure.com,1433`.

1. V poli **Ověřování** vyberte **Ověřování serveru SQL Server**.

1. Do pole **Přihlášení** zadejte název platného přihlášení SQL.

1. Do pole **Heslo** zadejte heslo pro přihlášení.

1. Klikněte na **Připojit**.

    ![Připojení přes SSMS](./media/virtual-machines-sql-server-connection-steps/rm-ssms-connect.png)