---
title: Integrace Key Vault s SQL Server na virtuálních počítačích s Windows v Azure (Správce prostředků) | Microsoft Docs
description: Naučte se automatizovat konfiguraci SQL Serverho šifrování pro použití s Azure Key Vault. V tomto tématu se dozvíte, jak používat Azure Key Vault integraci s SQL Servermi virtuálními počítači vytvořenými pomocí Správce prostředků.
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
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102146"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Konfigurace integrace Azure Key Vault pro SQL Server v Azure Virtual Machines (Správce prostředků)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Classic](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Přehled
Existuje několik funkcí SQL Server šifrování, jako je [transparentní šifrování dat (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [šifrování na úrovni sloupce (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)a [šifrování záloh](https://msdn.microsoft.com/library/dn449489.aspx). Tyto formy šifrování vyžadují, abyste mohli spravovat a ukládat kryptografické klíče, které používáte pro šifrování. Služba Azure Key Vault (integrace) je navržená tak, aby vylepšila zabezpečení a správu těchto klíčů v zabezpečeném a vysoce dostupném umístění. [Konektor SQL serveru](https://www.microsoft.com/download/details.aspx?id=45344) umožňuje SQL Server používat tyto klíče z Azure Key Vault.

Pokud používáte SQL Server s místními počítači, můžete postupovat [podle pokynů k přístupu k Azure Key Vault z místního počítače SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Ale pro SQL Server ve virtuálních počítačích Azure můžete ušetřit čas pomocí funkce *integrace Azure Key Vault* .

Když je tato funkce povolená, nainstaluje se Konektor SQL Serveru automaticky, nakonfiguruje poskytovatele EKM pro přístup k Azure Key Vault a vytvoří přihlašovací údaje, které vám umožní přístup k trezoru. Pokud jste si prohlédli postup uvedený v předchozí dokumentaci, vidíte, že tato funkce automatizuje kroky 2 a 3. Jedinou věcí, kterou byste pořád museli ručně udělat, je vytvoření trezoru klíčů a klíčů. Odtud je celá instalace vašeho virtuálního počítače SQL automatizovaná. Až tato funkce dokončí tuto instalaci, můžete spustit příkazy T-SQL, které zahájí šifrování databází nebo zálohování, jako byste to udělali normálně.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > Poskytovatel EKM verze 1.0.4.0 je nainstalovaný na virtuálním počítači s SQL Server prostřednictvím [rozšíření SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension). Při upgradu rozšíření SQL IaaS se verze poskytovatele neaktualizuje. V případě potřeby prosím zvažte ruční upgrade verze zprostředkovatele EKM (například při migraci do spravované instance SQL).


## <a name="enabling-and-configuring-akv-integration"></a>Povolení a konfigurace integrace integrace
Integraci integrace můžete povolit během zřizování nebo nakonfigurovat pro stávající virtuální počítače.

### <a name="new-vms"></a>Nové virtuální počítače
Pokud zřizujete nový SQL Server virtuální počítač s Správce prostředků, Azure Portal poskytuje způsob, jak povolit integraci Azure Key Vault. Funkce Azure Key Vault je k dispozici pouze pro edice Enterprise, Developer a Evaluation pro SQL Server.

![Integrace se službou Azure Key Vault pro SQL](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Podrobný návod k zřizování najdete v tématu [zřízení virtuálního počítače s SQL Server v Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Existující virtuální počítače

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

U stávajících virtuálních počítačů s SQL Server otevřete svůj [prostředek virtuálních počítačů SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) a v části **Nastavení**vyberte **zabezpečení** . Výběrem **Povolit** povolte integraci Azure Key Vault. 

![Integrace SQL integrace pro existující virtuální počítače](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

Po dokončení vyberte tlačítko **použít** v dolní části stránky **zabezpečení** a uložte provedené změny.

> [!NOTE]
> Název přihlašovacího údaje, který jsme vytvořili tady, se později namapuje na přihlašovací údaje SQL. To umožňuje přihlašovacímu účtu SQL přístup k trezoru klíčů. 


> [!NOTE]
> Integraci integrace můžete nakonfigurovat také pomocí šablony. Další informace najdete v tématu [Šablona Azure pro rychlý Start pro Azure Key Vault integraci](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]
