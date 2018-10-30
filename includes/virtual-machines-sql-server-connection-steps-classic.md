---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 57f238a8f91df1271e91894b88a7f02118b1f123
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226547"
---
### <a name="determine-the-dns-name-of-the-virtual-machine"></a>Určení názvu DNS virtuálního počítače
Pokud se chcete připojit k databázovému stroji SQL Serveru z jiného počítače, musíte znát název DNS (Domain Name System) virtuálního počítače. (Je to název, který internet používá k identifikaci virtuálního počítače. Můžete použít IP adres, ale IP adresa se může změnit, když Azure přesune prostředky kvůli redundanci nebo údržbě. Název DNS bude stabilní, protože je možné ho přesměrovat na novou IP adresu.)  

1. Na webu Azure Portal (nebo z předchozího kroku) vyberte **Virtuální počítače (Classic)**.
2. Vyberte virtuální počítač SQL.
3. V okně **Virtuální počítač** zkopírujte **název DNS** pro tento virtuální počítač.
   
    ![Název DNS](./media/virtual-machines-sql-server-connection-steps/sql-vm-dns-name.png)

### <a name="connect-to-the-database-engine-from-another-computer"></a>Připojení k databázovému stroji z jiného počítače
1. Na počítači připojeném k internetu otevřete SQL Server Management Studio.
2. V dialogovém okně **Připojit k serveru** nebo **Connect to Database Engine** (Připojit k databázovému stroji) do pole **Název serveru** zadejte název DNS virtuálního počítače (zjištěný v předchozím úkolu) a číslo portu veřejného koncového bodu ve formátu*název_DNS,číslo_portu*, například **mysqlvm.cloudapp.net,57500**.
   
    ![Připojení pomocí SSMS](./media/virtual-machines-sql-server-connection-steps/33Connect-SSMS.png)
   
    Pokud si nepamatujete číslo portu veřejného koncového bodu, který jste vytvořili, najdete ho v okně **Virtuální počítač** v části **Koncové body**.
   
    ![Veřejný port](./media/virtual-machines-sql-server-connection-steps/sql-vm-port-number.png)
3. V poli **Ověřování** vyberte **Ověřování serveru SQL Server**.
4. Do pole **Přihlášení** zadejte název přihlášení, které jste vytvořili v předchozím úkolu.
5. Do pole **Heslo** zadejte heslo přihlášení, které jste vytvořili v předchozím úkolu.
6. Klikněte na **Připojit**.

