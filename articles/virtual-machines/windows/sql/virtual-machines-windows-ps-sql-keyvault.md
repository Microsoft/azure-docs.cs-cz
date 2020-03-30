---
title: Integrace trezoru klíčů se serverem SQL Server na virtuálních počítačích se systémem Windows v Azure (Resource Manager) | Dokumenty společnosti Microsoft
description: Zjistěte, jak automatizovat konfiguraci šifrování serveru SQL Server pro použití s trezorem klíčů Azure. Toto téma vysvětluje, jak používat Azure Key Vault Integrace s virtuálními počítači SQL Server vytvořenými pomocí Správce prostředků.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: cad70169e88e1fafa129c02f30d5288d39e30a9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70102146"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Konfigurace integrace Azure Key Vault pro SQL Server ve službě Azure Virtual Machines (Resource Manager)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Classic](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Přehled
Existuje více funkcí šifrování serveru SQL Server, jako je [například transparentní šifrování dat (TDE),](https://msdn.microsoft.com/library/bb934049.aspx) [šifrování na úrovni sloupců (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)a [šifrování záloh](https://msdn.microsoft.com/library/dn449489.aspx). Tyto formy šifrování vyžadují správu a ukládání kryptografických klíčů, které používáte pro šifrování. Služba Azure Key Vault (AKV) je navržena tak, aby zlepšila zabezpečení a správu těchto klíčů v zabezpečeném a vysoce dostupném umístění. [Sql Server Connector](https://www.microsoft.com/download/details.aspx?id=45344) umožňuje SQL Server používat tyto klíče z Azure Key Vault.

Pokud používáte SQL Server s místními počítači, existují [kroky, které můžete postupovat pro přístup k Azure Key Vault z místního počítače SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Ale pro SQL Server ve virtuálních počítačích Azure můžete ušetřit čas pomocí funkce *Integrace úložiště klíčů Azure.*

Pokud je tato funkce povolena, automaticky nainstaluje konektor SQL Server, nakonfiguruje zprostředkovatele EKM pro přístup k úložišti klíčů Azure a vytvoří pověření, které vám umožní přístup k trezoru. Pokud jste se podívali na kroky ve výše uvedené místní dokumentaci, uvidíte, že tato funkce automatizuje kroky 2 a 3. Jediné, co byste ještě museli udělat ručně, je vytvořit trezor klíčů a klíče. Odtud je automatizováno celé nastavení virtuálního počítače SQL. Jakmile tato funkce dokončí toto nastavení, můžete spustit příkazy T-SQL a začít šifrovat databáze nebo zálohy obvyklým způsobem.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > Zprostředkovatel EKM verze 1.0.4.0 je nainstalován na virtuálním počítači SQL Server prostřednictvím [rozšíření SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). Inovace rozšíření SQL IaaS nebude aktualizovat verzi zprostředkovatele. Zvažte ruční upgrade verze zprostředkovatele EKM v případě potřeby (například při migraci na spravovanou instanci SQL).


## <a name="enabling-and-configuring-akv-integration"></a>Povolení a konfigurace integrace AKV
Můžete povolit integraci AKV během zřizování nebo nakonfigurovat pro existující virtuální počítače.

### <a name="new-vms"></a>Nové virtuální společnosti
Pokud zřazujete nový virtuální počítač SQL Serveru pomocí Správce prostředků, portál Azure poskytuje způsob, jak povolit integraci Azure Key Vault. Funkce Azure Key Vault je k dispozici jenom pro edice Enterprise, Developer a Evaluation Edition sql serveru.

![Integrace se službou Azure Key Vault pro SQL](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Podrobný návod zřizování najdete [v tématu Zřízení virtuálního počítače SQL Serveru na webu Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Existující virtuální chod

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

U existujících virtuálních počítačů SQL Serveru otevřete [prostředek virtuálních počítačů SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) a v části **Nastavení**vyberte **Zabezpečení** . Chcete-li povolit integraci úložiště klíčů Azure, vyberte **povolit.** 

![Integrace SQL AKV pro existující virtuální aplikace](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Po dokončení vyberte tlačítko **Použít** v dolní části stránky **Zabezpečení** a uložte změny.

> [!NOTE]
> Název pověření, který jsme zde vytvořili, bude později namapován na přihlášení SQL. To umožňuje přihlášení SQL pro přístup k trezoru klíčů. 


> [!NOTE]
> Můžete také nakonfigurovat integraci AKV pomocí šablony. Další informace najdete v [tématu Azure quickstart šablona pro integraci Azure Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
