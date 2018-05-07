---
title: Trezor klíčů integraci se službou SQL Server na virtuálních počítačích Windows v Azure (Resource Manager) | Microsoft Docs
description: Zjistěte, jak k automatizaci konfigurace systému SQL Server šifrování pro použití s Azure Key Vault. Toto téma vysvětluje, jak používat Azure Key Vault integrace s SQL Server na virtuální počítače vytvořené pomocí Resource Manageru.
services: virtual-machines-windows
documentationcenter: ''
author: rothja
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
ms.author: jroth
ms.openlocfilehash: 2b398f59aed1610825f495a6089990d393531305
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Konfigurace integrace Azure Key Vault pro SQL Server na virtuálních počítačích Azure (Resource Manager)

> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-ps-sql-keyvault.md)
> * [Classic](../sqlclassic/virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Přehled
Existuje více funkcí šifrování systému SQL Server, například [transparentní šifrování dat (šifrování TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [šifrování na úrovni sloupce (Vymazat)](https://msdn.microsoft.com/library/ms173744.aspx), a [zálohu šifrovacího](https://msdn.microsoft.com/library/dn449489.aspx). Tyto formuláře šifrování vyžadovat ke správě a ukládání kryptografických klíčů, které používáte pro šifrování. Službu službou Azure Key Vault (AZURE) slouží k vylepšení zabezpečení a správu tyto klíče v umístění zabezpečené a vysoce dostupné. [Konektor služby serveru SQL](http://www.microsoft.com/download/details.aspx?id=45344) umožňuje serveru SQL pro použití těchto klíčů z Azure Key Vault.

Pokud používáte systém SQL Server s místním počítačům, jsou [kroky, pomocí kterých můžete pro přístup k Azure Key Vault z vašeho místního počítače systému SQL Server](https://msdn.microsoft.com/library/dn198405.aspx). Ale pro SQL Server ve virtuálních počítačích Azure, můžete ušetřit čas pomocí *Azure Key Vault integrace* funkce.

Pokud je tato funkce povolena, automaticky se nainstaluje konektor serveru SQL, nakonfiguruje zprostředkovatele EKM. pro přístup k Azure Key Vault a vytvoří pověření umožňují přístup k trezoru. Pokud zvážení kroky v dokumentaci k výše uvedených v místě, uvidíte, že tato funkce automatizuje kroky 2 a 3. Jediné, co by se stále potřeba udělat ručně je vytvoření trezoru klíčů a klíče. Z tohoto místa se automatizované celé nastavení virtuálního počítače SQL. Po dokončení této instalace této funkce můžete spustit příkazů T-SQL zahájíte šifrování databáze nebo zálohy běžným způsobem.

[!INCLUDE [AKV Integration Prepare](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="enabling-and-configuring-akv-integration"></a>Povolení a konfigurace integrace se službou AZURE
Můžete povolit integrace se službou AZURE při zřizování nebo ho nakonfigurovat pro existující virtuální počítače.

### <a name="new-vms"></a>Nové virtuální počítače
Pokud zřizujete nového virtuálního počítače systému SQL Server s Resource Managerem, portál Azure poskytuje způsob, jak povolit integrace se službou Azure Key Vault. Funkce Azure Key Vault je dostupná pouze pro Enterprise, Developer a zkušební edice systému SQL Server.

![Integrace se službou Azure Key Vault pro SQL](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Podrobný návod k zřizování, najdete v části [zřízení virtuálního počítače s SQL serverem na portálu Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>Existující virtuální počítače
Pro existující virtuální počítače systému SQL Server vyberte virtuální počítač systému SQL Server. Vyberte **konfigurace systému SQL Server** části **nastavení** okno.

![Integrace se službou AZURE SQL pro existující virtuální počítače](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

V **konfigurace systému SQL Server** okně klikněte **upravit** tlačítko v části Integrace automatizované Key Vault.

![Konfigurace integrace se službou AZURE SQL pro existující virtuální počítače](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

Po dokončení klikněte **OK** tlačítko v dolní části **konfigurace systému SQL Server** okno a uložte změny.

> [!NOTE]
> Název přihlašovacího údaje, které jsme vytvořili tady budou mapována na přihlášení SQL později. To umožňuje přihlášení k SQL pro přístup k trezoru klíčů. 
>
>

> [!NOTE]
> Můžete také nakonfigurovat integrace se službou AZURE pomocí šablony. Další informace najdete v tématu [šablony Azure rychlý start pro integraci Azure Key Vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).
> 
> 

[!INCLUDE [AKV Integration Next Steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]

