---
title: Vytvoření a konfigurace trezoru klíčů pro službu Azure Disk Encryption
description: Tento článek popisuje kroky pro vytvoření a konfiguraci trezoru klíčů pro použití s Azure Disk Encryption na virtuálním počítači se systémem Linux.
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 29f6f01ece0536d93ca18ca54379757adc120308
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818777"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Vytvoření a konfigurace trezoru klíčů pro službu Azure Disk Encryption

Azure Disk Encryption používá Azure Key Vault k řízení a správě šifrovacích klíčů a tajných klíčů disku.  Další informace o trezorech klíčů najdete v tématu [Začínáme s Azure Key Vault](../../key-vault/general/overview.md) a [zabezpečení trezoru klíčů](../../key-vault/general/security-features.md). 

> [!WARNING]
> - Pokud jste předtím používali Azure Disk Encryption se službou Azure AD k šifrování virtuálního počítače, musíte tuto možnost použít k zašifrování virtuálního počítače. Podrobnosti najdete v tématu [Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption s Azure AD (předchozí verze)](disk-encryption-key-vault-aad.md) .

Vytvoření a konfigurace trezoru klíčů pro použití s Azure Disk Encryption zahrnuje tři kroky:

1. V případě potřeby vytvořte skupinu prostředků.
2. Vytváří se Trezor klíčů. 
3. Nastavují se zásady rozšířeného přístupu trezoru klíčů.

Tyto kroky jsou znázorněné v následujících rychlých startech:

- [Vytvoření a šifrování virtuálního počítače s Linuxem s využitím Azure CLI](disk-encryption-cli-quickstart.md)
- [Vytvoření a šifrování virtuálního počítače s Linuxem s využitím Azure PowerShellu](disk-encryption-powershell-quickstart.md)

Můžete také, pokud chcete, vygenerovat nebo importovat klíč šifrování klíče (KEK).

> [!Note]
> Kroky v tomto článku jsou automatizovány ve skriptu rozhraní příkazového [řádku Azure Disk Encryption předpoklady](https://github.com/ejarvi/ade-cli-getting-started) a v tématu [Azure Disk Encryption předpoklady PowerShellu](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Nainstalovat nástroje a připojit se k Azure

Kroky v tomto článku se dají dokončit pomocí [Azure CLI](/cli/azure/), [Azure PowerShell Az Module](/powershell/azure/)nebo [Azure Portal](https://portal.azure.com). 

I když je portál přístupný prostřednictvím prohlížeče, Azure CLI a Azure PowerShell vyžadují místní instalaci; Podrobnosti najdete v tématu [Azure Disk Encryption pro Linux: Instalace nástrojů](disk-encryption-linux.md#install-tools-and-connect-to-azure) .

### <a name="connect-to-your-azure-account"></a>Připojení k účtu Azure

Než začnete používat rozhraní příkazového řádku Azure CLI nebo Azure PowerShell, musíte se nejdřív připojit k předplatnému Azure. Provedete to tak, že [se přihlásíte pomocí Azure CLI](/cli/azure/authenticate-azure-cli), [přihlásíte se pomocí Azure PowerShellu](/powershell/azure/authenticate-azureps)nebo po zobrazení výzvy dodáte přihlašovací údaje do Azure Portal.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
 
## <a name="next-steps"></a>Další kroky

- [Skript CLI pro Azure Disk Encryption předpoklady](https://github.com/ejarvi/ade-cli-getting-started)
- [Skript prostředí PowerShell pro Azure Disk Encryption předpoklady](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Informace o [Azure Disk Encryption scénářích pro virtuální počítače se systémem Linux](disk-encryption-linux.md)
- Postup [řešení potíží s Azure Disk Encryption](disk-encryption-troubleshooting.md)
- Přečtěte si [ukázkové skripty Azure Disk Encryption](disk-encryption-sample-scripts.md)
