---
title: Integrace služby Key Vault s SQL serverem na virtuálních počítačích s Windows v Azure (Resource Manager) | Dokumentace Microsoftu
description: Zjistěte, jak automatizovat konfiguraci systému SQL Server šifrování pro použití se službou Azure Key Vault. Toto téma vysvětluje, jak integrace se službou Azure Key Vault pomocí systému SQL Server na virtuálních počítačích vytvořených pomocí Resource Manageru.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 6ad8eea21c10726b2c3eaf1e10bfd5efba4d1e48
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358690"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Konfigurace integrace Azure Key Vaultu pro SQL Server na Azure Virtual Machines (Resource Manager)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Classic](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Přehled
Existuje více funkcí systému SQL Server šifrování, jako například [transparentní šifrování dat (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [šifrování na úrovni sloupce (Vymazat)](https://msdn.microsoft.com/library/ms173744.aspx), a [šifrování záloh](https://msdn.microsoft.com/library/dn449489.aspx). Tyto formy šifrování nutné ke správě a ukládání kryptografických klíčů, který používáte pro šifrování. Službu Azure Key Vaultu (AKV) je navržené pro zlepšení zabezpečení a správu těchto klíčů v zabezpečených a vysoce dostupných umístění. [Konektor SQL serveru](https://www.microsoft.com/download/details.aspx?id=45344) umožňuje SQL serveru používat tyto klíče ze služby Azure Key Vault.

Pokud používáte systém SQL Server pomocí místních počítačů, existují [kroky, pomocí kterých můžete pro přístup k Azure Key Vault z vašeho počítače systému SQL Server v místním](https://msdn.microsoft.com/library/dn198405.aspx). Ale pro SQL Server na virtuálních počítačích Azure, můžete ušetřit čas pomocí *integrace Azure Key Vault* funkce.

Pokud je tato funkce povolena, automaticky se nainstaluje konektor SQL serveru, nakonfiguruje zprostředkovatele EKM. pro přístup k Azure Key Vault a vytvoří přihlašovací údaj, který umožňuje přístup k trezoru. Pokud podívali se na postup v dokumentaci k výše uvedené v místním, uvidíte, že tato funkce automatizuje kroky 2 a 3. Jediné, co by stále muset provést ručně, je vytvořit trezor klíčů a klíče. Tady je automatické celé nastavení virtuálního počítače s SQL. Po dokončení této instalace a nastavení tuto funkci můžete spustit příkazy T-SQL k zahájit šifrování vašich databází nebo zálohy jako obvykle.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > Zprostředkovatele EKM. verze 1.0.4.0 je nainstalovaný na virtuálním počítači SQL serveru prostřednictvím [rozšíření SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). Upgrade rozšíření SQL IaaS neaktualizuje verze zprostředkovatele. Zvážení prosím ručně upgrade verze zprostředkovatele EKM. v případě potřeby (například při migraci do spravované Instance SQL).


## <a name="enabling-and-configuring-akv-integration"></a>Povolení a konfigurace integrace se službou AZURE
Můžete povolit integrace se službou AZURE během zřizování nebo ho nakonfigurovat pro stávající virtuální počítače.

### <a name="new-vms"></a>Nové virtuální počítače
Pokud zřizujete nového virtuálního počítače SQL serveru s využitím Resource Manageru, na webu Azure portal poskytuje způsob, jak povolit integrace Azure Key Vault. Funkce Azure Key Vault je dostupná jenom pro Enterprise, Developer a zkušební edice systému SQL Server.

![Integrace se službou Azure Key Vault pro SQL](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Podrobný postup zřizování, najdete v části [zřízení virtuálního počítače s SQL serverem na webu Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Stávající virtuální počítače
Existující virtuální počítače systému SQL Server vyberte virtuální počítač systému SQL Server. Vyberte **konfigurace systému SQL Server** část **nastavení** okno.

![Integrace se službou AZURE SQL pro stávající virtuální počítače](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

V **konfigurace systému SQL Server** okna, klikněte na tlačítko **upravit** tlačítko v části Integrace automatické služby Key Vault.

![Konfigurace integrace se službou AZURE SQL pro stávající virtuální počítače](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

Až budete hotovi, klikněte na tlačítko **OK** tlačítko v dolní části **konfigurace systému SQL Server** okno a uložte provedené změny.

> [!NOTE]
> Název přihlašovacího údaje, které jsme vytvořili v tomto poli se namapují na přihlašovací údaje pro SQL později. Díky tomu přihlašovací jméno SQL pro přístup k trezoru klíčů. 
>
>

> [!NOTE]
> Můžete také nakonfigurovat integrace se službou AZURE pomocí šablony. Další informace najdete v tématu [šablona rychlého startu Azure pro integraci služby Azure Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).
> 
> 

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

