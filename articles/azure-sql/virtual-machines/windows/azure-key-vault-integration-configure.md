---
title: Integrace Key Vault s SQL Server na virtuálních počítačích s Windows v Azure (Správce prostředků) | Microsoft Docs
description: Naučte se automatizovat konfiguraci SQL Serverho šifrování pro použití s Azure Key Vault. V tomto tématu se dozvíte, jak používat integraci Azure Key Vault s virtuálními počítači SQL vytvořenými pomocí Správce prostředků.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: cd66dfb1-0e9b-4fb0-a471-9deaf4ab4ab8
ms.service: virtual-machines-sql
ms.subservice: security
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/30/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: a6955b7fc4948faaea6db426545f8cc3d1eece35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359893"
---
# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>Konfigurace integrace Azure Key Vault pro SQL Server na virtuálních počítačích Azure (Správce prostředků)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Existuje několik funkcí SQL Server šifrování, jako je [transparentní šifrování dat (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption), [šifrování na úrovni sloupce (CLE)](/sql/t-sql/functions/cryptographic-functions-transact-sql)a [šifrování záloh](/sql/relational-databases/backup-restore/backup-encryption). Tyto formy šifrování vyžadují, abyste mohli spravovat a ukládat kryptografické klíče, které používáte pro šifrování. Služba Azure Key Vault je navržená tak, aby vylepšila zabezpečení a správu těchto klíčů v zabezpečeném a vysoce dostupném umístění. [Konektor SQL serveru](https://www.microsoft.com/download/details.aspx?id=45344) umožňuje SQL Server používat tyto klíče z Azure Key Vault.

Pokud používáte SQL Server v místním prostředí, můžete postupovat podle pokynů pro [přístup k Azure Key Vault z vaší místní instance SQL Server](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server). Ale pro SQL Server na virtuálních počítačích Azure můžete ušetřit čas pomocí funkce *integrace Azure Key Vault* .

Když je tato funkce povolená, nainstaluje se Konektor SQL Serveru automaticky, nakonfiguruje poskytovatele EKM pro přístup k Azure Key Vault a vytvoří přihlašovací údaje, které vám umožní přístup k trezoru. Pokud jste si prohlédli postup uvedený v předchozí dokumentaci, vidíte, že tato funkce automatizuje kroky 2 a 3. Jedinou věcí, kterou byste pořád museli ručně udělat, je vytvoření trezoru klíčů a klíčů. Odtud je celá instalace SQL Server virtuálního počítače automatizovaná. Až tato funkce dokončí tuto instalaci, můžete spustit příkazy jazyka Transact-SQL (T-SQL) a začít šifrovat databáze nebo zálohy, jako byste to udělali normálně.

[!INCLUDE [Prepare for Key Vault integration](../../../../includes/virtual-machines-sql-server-akv-prepare.md)]

  >[!NOTE]
  > Zprostředkovatel EKM (Extensible Key Management) verze 1.0.4.0 je nainstalovaná na SQL Serverm virtuálním počítači prostřednictvím [rozšíření SQL infrastruktura jako služba (IaaS)](./sql-server-iaas-agent-extension-automate-management.md). Při upgradu rozšíření SQL IaaS se verze poskytovatele neaktualizuje. V případě potřeby prosím zvažte ruční upgrade verze zprostředkovatele EKM (například při migraci do spravované instance SQL).


## <a name="enabling-and-configuring-key-vault-integration"></a>Povolení a konfigurace integrace Key Vault
Integraci Key Vault můžete povolit během zřizování nebo nakonfigurovat pro stávající virtuální počítače.

### <a name="new-vms"></a>Nové virtuální počítače
Pokud zřizujete nový virtuální počítač SQL s Správce prostředků, Azure Portal poskytuje způsob, jak povolit Azure Key Vault integraci. Funkce Azure Key Vault je k dispozici pouze pro edice Enterprise, Developer a Evaluation pro SQL Server.

![Integrace se službou Azure Key Vault pro SQL](./media/azure-key-vault-integration-configure/azure-sql-arm-akv.png)

Podrobný návod k zřizování najdete v tématu [zřízení virtuálního počítače s SQL v Azure Portal](create-sql-vm-portal.md).

### <a name="existing-vms"></a>Existující virtuální počítače

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Pro existující virtuální počítače SQL otevřete [prostředek virtuálních počítačů SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) a v části **Nastavení** vyberte **zabezpečení** . Výběrem **Povolit** povolte integraci Azure Key Vault. 

![Integrace se službou SQL Key Vault pro existující virtuální počítače](./media/azure-key-vault-integration-configure/azure-sql-rm-akv-existing-vms.png)

Až skončíte, klikněte na tlačítko **použít** v dolní části stránky **zabezpečení** a uložte provedené změny.

> [!NOTE]
> Název přihlašovacího údaje, který jsme vytvořili tady, se později namapuje na přihlašovací údaje SQL. To umožňuje přihlašovacímu účtu SQL přístup k trezoru klíčů. 


> [!NOTE]
> Integraci Key Vault můžete nakonfigurovat také pomocí šablony. Další informace najdete v tématu [Šablona Azure pro rychlý Start pro Azure Key Vault integraci](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).


[!INCLUDE [Key Vault integration next steps](../../../../includes/virtual-machines-sql-server-akv-next-steps.md)]