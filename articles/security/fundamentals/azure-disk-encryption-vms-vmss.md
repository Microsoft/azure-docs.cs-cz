---
title: Azure Disk Encryption pro virtuální počítače a sady škálování virtuálních počítačů
description: Tento článek poskytuje přehled Azure Disk Encryption
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 239bfb4f5a0e9115c113bb2d5ddbc931f41c0f3d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "72599952"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>Azure Disk Encryption pro virtuální počítače a sady škálování virtuálních počítačů

Azure Disk Encryption se dá použít pro virtuální počítače se systémem Linux i Windows i pro službu Virtual Machine Scale Sets. 

## <a name="linux-virtual-machines"></a>Virtuální počítače s Linuxem

Následující články obsahují pokyny k šifrování virtuálních počítačů se systémem Linux.

### <a name="current-version-of-azure-disk-encryption"></a>Aktuální verze Azure Disk Encryption

- [Přehled Azure Disk Encryption pro virtuální počítače se systémem Linux](../../virtual-machines/linux/disk-encryption-overview.md)
- [Scénáře Azure Disk Encryption na virtuálních počítačích se systémem Linux](../../virtual-machines/linux/disk-encryption-linux.md)
- [Vytvoření a šifrování virtuálního počítače se systémem Linux pomocí Azure CLI](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Vytvoření a šifrování virtuálního počítače se systémem Linux pomocí Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Vytvoření a šifrování virtuálního počítače se systémem Linux pomocí Azure Portal](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Azure Disk Encryption ukázkové skripty](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Řešení potíží s Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Azure Disk Encryption nejčastějších dotazech](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption s Azure AD (předchozí verze)

- [Přehled Azure Disk Encryption s Azure AD pro virtuální počítače se systémem Linux](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Azure Disk Encryption se scénáři Azure AD na virtuálních počítačích se systémem Linux](../../virtual-machines/linux/disk-encryption-linux.md)
- [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption s využitím Azure AD (předchozí verze)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Virtuální počítače s Windows

Následující články obsahují pokyny k šifrování virtuálních počítačů s Windows.

### <a name="current-version-of-azure-disk-encryption"></a>Aktuální verze Azure Disk Encryption

- [Přehled Azure Disk Encryption pro virtuální počítače s Windows](../../virtual-machines/windows/disk-encryption-overview.md)
- [Scénáře Azure Disk Encryption na virtuálních počítačích s Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Vytvoření a šifrování virtuálního počítače s Windows pomocí Azure CLI](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Vytvoření a šifrování virtuálního počítače s Windows pomocí Azure PowerShell](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Vytvoření a šifrování virtuálního počítače s Windows pomocí Azure Portal](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Azure Disk Encryption ukázkové skripty](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Řešení potíží s Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Azure Disk Encryption nejčastějších dotazech](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption s Azure AD (předchozí verze)

- [Přehled Azure Disk Encryption s Azure AD pro virtuální počítače s Windows](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Azure Disk Encryption se scénáři Azure AD na virtuálních počítačích s Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption s využitím Azure AD (předchozí verze)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>Škálovací sady virtuálních počítačů

Následující články obsahují pokyny k šifrování služby Virtual Machine Scale Sets.

- [Přehled Azure Disk Encryption pro Virtual Machine Scale Sets](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Šifrování sady škálování virtuálních počítačů pomocí Azure CLI](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [Pomocí Azure PowerShellu Zašifrujte služby Virtual Machine Scale Sets](../../virtual-machine-scale-sets/disk-encryption-powershell.md).
- [Šifrování virtuálních počítačů pomocí Azure Resource Manager](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [Použití Azure Disk Encryption s pořadím rozšíření pro škálování sady virtuálních počítačů](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>Další kroky

- [Přehled šifrování Azure](encryption-overview.md)
- [Šifrování dat v klidovém stavu](encryption-atrest.md)
- [Osvědčené postupy šifrování a zabezpečení dat](data-encryption-best-practices.md)
