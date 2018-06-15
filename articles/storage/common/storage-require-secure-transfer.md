---
title: Vyžadovat bezpečnému přenosu ve službě Azure Storage | Microsoft Docs
description: Další informace o funkci "Zabezpečení přenosu požadované" pro Azure Storage a jak jej povolit.
services: storage
documentationcenter: na
author: fhryo-msft
manager: Jason.Hogg
editor: fhryo-msft
ms.assetid: ''
ms.service: storage
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage
ms.date: 06/20/2017
ms.author: fryu
ms.openlocfilehash: 0ec36864377d724795197851341cbb837f47c4c6
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31599794"
---
# <a name="require-secure-transfer-in-azure-storage"></a>Vyžadovat bezpečnému přenosu ve službě Azure Storage

Možnost "Zabezpečení přenosu požadované" zvyšuje zabezpečení vašeho účtu úložiště tím, že pouze požadavky na účet z zabezpečené připojení. Například pokud jste volání rozhraní REST API pro přístup k účtu úložiště, je nutné připojit pomocí protokolu HTTPS. "Zabezpečení přenosu požadované" odmítne požadavky, které používají protokol HTTP.

Pokud používáte službu Azure soubory, žádné připojení bez šifrování se nezdaří, pokud "Zabezpečení přenosu požadované" je povolena. To zahrnuje scénáře, které používají protokol SMB 2.1, protokolu SMB 3.0 bez šifrování a některé verze klienta Linux SMB. 

Ve výchozím nastavení je možnost "Zabezpečení přenosu požadované" zakázaná.

> [!NOTE]
> Vzhledem k tomu, že úložiště Azure nepodporuje protokol HTTPS pro vlastní názvy domén, není tato možnost použít, pokud používáte vlastní název domény. A nepodporuje klasické účty úložiště.

## <a name="enable-secure-transfer-required-in-the-azure-portal"></a>Povolit "Zabezpečení přenosu požadované" na portálu Azure

Můžete zapnout "zabezpečený přenos požadované" při vytvoření účtu úložiště v nastavení [portál Azure](https://portal.azure.com). Můžete také povolit ho pro existující účty úložiště.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Vyžadovat bezpečnému přenosu pro nový účet úložiště

1. Otevřete **vytvořit účet úložiště** podokně na portálu Azure.
1. V části **zabezpečení přenosu požadované**, vyberte **povoleno**.

  ![Vytvoření okna pro účet úložiště](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Vyžadovat bezpečnému přenosu pro stávající účet úložiště

1. Vyberte stávající účet úložiště na portálu Azure.
1. V úložišti účet nabídky podokně v části **nastavení**, vyberte **konfigurace**.
1. V části **zabezpečení přenosu požadované**, vyberte **povoleno**.

  ![Podokno nabídce účtů úložiště](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="enable-secure-transfer-required-programmatically"></a>Povolit "Zabezpečení přenosu požadované" prostřednictvím kódu programu

Pokud chcete vyžadovat zabezpečené přenos prostřednictvím kódu programu, použijte nastavení _supportsHttpsTrafficOnly_ ve vlastnosti účtu úložiště pomocí rozhraní REST API, nástroje nebo knihovny:

* [Rozhraní REST API](https://docs.microsoft.com/rest/api/storagerp/storageaccounts) (verze: 2016-12-01)
* [Prostředí PowerShell](https://docs.microsoft.com/powershell/module/azurerm.storage/set-azurermstorageaccount?view=azurermps-4.1.0) (verze: 4.1.0)
* [Rozhraní příkazového řádku](https://pypi.python.org/pypi/azure-cli-storage/2.0.11) (verze: 2.0.11)
* [NodeJS](https://www.npmjs.com/package/azure-arm-storage/) (verze: 1.1.0)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/6.3.0-preview) (verze: 6.3.0)
* [Python SDK](https://pypi.python.org/pypi/azure-mgmt-storage/1.1.0) (verze: 1.1.0)
* [Poznámky Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage) (verze: 0.11.0)

### <a name="enable-secure-transfer-required-setting-with-powershell"></a>Povolit "Zabezpečený přenos požadované" nastavení pomocí prostředí PowerShell

Tato ukázka vyžaduje prostředí Azure PowerShell verze modulu 4.1 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

Spuštěním příkazu `Connect-AzureRmAccount` vytvořte připojení k Azure.

 Zkontrolujte nastavení, použijte následující příkazový řádek:

```powershell
> Get-AzureRmStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Povolit nastavení, použijte následující příkazový řádek:

```powershell
> Set-AzureRmStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

### <a name="enable-secure-transfer-required-setting-with-cli"></a>Povolit "Zabezpečený přenos požadované" nastavení pomocí rozhraní příkazového řádku

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Zkontrolujte nastavení, použijte následující příkazový řádek:

```azurecli-interactive
> az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Povolit nastavení, použijte následující příkazový řádek:

```azurecli-interactive
> az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Další postup
Úložiště Azure poskytuje komplexní sadu funkcí zabezpečení, které společně umožňují vývojářům vytvářet aplikace zabezpečené. Další podrobnosti, přejděte na [Průvodce zabezpečením úložiště](storage-security-guide.md).
