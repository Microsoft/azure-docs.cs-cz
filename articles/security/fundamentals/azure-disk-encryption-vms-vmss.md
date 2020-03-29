---
title: Azure Disk Encryption pro virtuální počítače a škálovací sady virtuálních počítačů
description: Tento článek obsahuje přehled šifrování disku Azure
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: c881b2b9743766e4d35e6cb05f6f3469803850bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062130"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>Azure Disk Encryption pro virtuální počítače a škálovací sady virtuálních počítačů

Šifrování disku Azure lze použít pro virtuální počítače S Linuxem i Windows, stejně jako pro škálovací sady virtuálních počítačů. 

## <a name="linux-virtual-machines"></a>Virtuální počítače s Linuxem

Následující články poskytují pokyny pro šifrování virtuálních počítačů Linuxu.

### <a name="current-version-of-azure-disk-encryption"></a>Aktuální verze šifrování disku Azure

- [Přehled služby Azure Disk Encryption pro virtuální počítače s Linuxem](../../virtual-machines/linux/disk-encryption-overview.md)
- [Scénáře služby Azure Disk Encryption na virtuálních počítačích s Linuxem](../../virtual-machines/linux/disk-encryption-linux.md)
- [Vytvoření a šifrování virtuálního počítače s Linuxem s využitím Azure CLI](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Vytvoření a šifrování virtuálního počítače s Linuxem s využitím Azure PowerShellu](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Vytvoření a šifrování virtuálního počítače s Linuxem s využitím webu Azure Portal](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Schéma rozšíření šifrování disku Azure pro Linux](../../virtual-machines/extensions/azure-disk-enc-linux.md)
- [Vytvoření a konfigurace trezoru klíčů pro službu Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Ukázkové skripty pro službu Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Řešení potíží se službou Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Nejčastější dotazy ke službě Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Šifrování disku Azure pomocí Azure AD (předchozí verze)

- [Přehled šifrování disku Azure pomocí virtuálních počítačů Azure AD pro Linux](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Šifrování disku Azure se scénáři Azure AD na virtuálních počítačích s Linuxem](../../virtual-machines/linux/disk-encryption-linux.md)
- [Vytvoření a konfigurace trezoru klíčů pro šifrování disku Azure pomocí Azure AD (předchozí verze)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Virtuální počítače s Windows

Následující články obsahují pokyny pro šifrování virtuálních počítačů s Windows.

### <a name="current-version-of-azure-disk-encryption"></a>Aktuální verze šifrování disku Azure

- [Přehled služby Azure Disk Encryption pro virtuální počítače s Windows](../../virtual-machines/windows/disk-encryption-overview.md)
- [Scénáře služby Azure Disk Encryption na virtuálních počítačích s Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Vytvoření a šifrování virtuálního počítače s Windows s využitím Azure CLI](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Vytvoření a šifrování virtuálního počítače s Windows s využitím Azure PowerShellu](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Vytvoření a šifrování virtuálního počítače s Windows s využitím webu Azure Portal](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Schéma rozšíření šifrování disku Azure pro Windows](../../virtual-machines/extensions/azure-disk-enc-windows.md)
- [Vytvoření a konfigurace trezoru klíčů pro službu Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Ukázkové skripty pro službu Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Řešení potíží se službou Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Nejčastější dotazy ke službě Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Šifrování disku Azure pomocí Azure AD (předchozí verze)

- [Přehled šifrování disku Azure pomocí azure ad pro virtuální počítače s Windows](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Šifrování disku Azure se scénáři Azure AD na virtuálních počítačích s Windows](../../virtual-machines/windows/disk-encryption-windows.md)
- [Vytvoření a konfigurace trezoru klíčů pro šifrování disku Azure pomocí Azure AD (předchozí verze)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>Škálovací sady virtuálních počítačů

Následující články obsahují pokyny pro šifrování škálovacích sad virtuálních strojů.

- [Přehled šifrování disku Azure pro škálovací sady virtuálních počítačů](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Šifrování škálovacích sad virtuálních strojů pomocí příkazového příkazového příkazu Azure](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [Šifrujte škálovací sady virtuálních strojů pomocí Azure Powershellu](../../virtual-machine-scale-sets/disk-encryption-powershell.md).
- [Šifrování škálovacích sad virtuálních strojů pomocí Správce prostředků Azure](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Vytvoření a konfigurace trezoru klíčů pro šifrování disku Azure](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [Použití Azure Disk Encryption s řazením rozšíření škálovací sady virtuálních počítačů](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>Další kroky

- [Přehled šifrování Azure](encryption-overview.md)
- [Šifrování dat v klidovém stavu](encryption-atrest.md)
- [Osvědčené postupy šifrování a zabezpečení dat](data-encryption-best-practices.md)
