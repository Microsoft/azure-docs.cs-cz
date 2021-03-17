---
title: Pro zajištění zabezpečených připojení vyžadovat zabezpečený přenos
titleSuffix: Azure Storage
description: Naučte se vyžadovat zabezpečený přenos pro požadavky na Azure Storage. Pokud pro účet úložiště požadujete zabezpečený přenos, všechny požadavky pocházející z nezabezpečeného připojení se odmítnou.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a0a942daae6b106bce928f8214e92c0c6294203d
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555714"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Pro zajištění zabezpečených připojení vyžadovat zabezpečený přenos

Účet úložiště můžete nakonfigurovat tak, aby přijímal požadavky od zabezpečených připojení jenom nastavením vlastnosti **požadováno zabezpečeným přenosem** pro účet úložiště. Pokud vyžadujete zabezpečený přenos, všechny požadavky pocházející z nezabezpečeného připojení se odmítnou. Microsoft doporučuje, abyste vždycky vyžadovali zabezpečený přenos pro všechny vaše účty úložiště.

Je-li požadován zabezpečený přenos, musí být volání operace Azure Storage REST API provedeno prostřednictvím protokolu HTTPS. Všechny požadavky vytvořené přes protokol HTTP jsou odmítnuty.

Připojení ke sdílené složce Azure přes protokol SMB bez šifrování se nepovede, když se pro účet úložiště vyžaduje zabezpečený přenos. Mezi nezabezpečená připojení patří například ta, která se nastavila přes SMB 2,1, SMB 3,0 bez šifrování nebo některé verze klienta systému Linux SMB.

Ve výchozím nastavení je při vytváření účtu úložiště povolená vlastnost **požadovaná zabezpečeným přenosem** .

> [!NOTE]
> Vzhledem k tomu, že Azure Storage nepodporuje HTTPS pro vlastní názvy domén, tato možnost se nepoužije, když používáte vlastní název domény. A klasické účty úložiště se nepodporují.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Vyžadovat zabezpečený přenos v Azure Portal

Vlastnost **Required pro zabezpečený přenos** můžete zapnout při vytváření účtu úložiště v [Azure Portal](https://portal.azure.com). Můžete ho taky povolit pro existující účty úložiště.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Vyžadovat zabezpečený přenos pro nový účet úložiště

1. Otevřete podokno **vytvořit účet úložiště** v Azure Portal.
1. V části **požadovaný zabezpečený přenos** vyberte **povoleno**.

   ![Okno vytvořit účet úložiště](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Vyžadovat zabezpečený přenos pro existující účet úložiště

1. Vyberte existující účet úložiště v Azure Portal.
1. V podokně nabídky účtu úložiště v části **Nastavení** vyberte **Konfigurace**.
1. V části **požadovaný zabezpečený přenos** vyberte **povoleno**.

   ![Podokno nabídky účtu úložiště](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Vyžadovat zabezpečený přenos z kódu

Pokud chcete zabezpečený přenos vyžadovat programově, nastavte vlastnost _enableHttpsTrafficOnly_ na _hodnotu true_ v účtu úložiště. Tuto vlastnost můžete nastavit pomocí REST API poskytovatele prostředků úložiště, klientských knihoven nebo nástrojů:

* [REST API](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [Rozhraní příkazového řádku](/cli/azure/storage/account)
* [NodeJS](https://www.npmjs.com/package/@azure/arm-storage/)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [Python SDK](https://pypi.org/project/azure-mgmt-storage)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>Vyžadovat zabezpečený přenos pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tato ukázka vyžaduje modul Azure PowerShell AZ verze 0,7 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

Spuštěním příkazu `Connect-AzAccount` vytvořte připojení k Azure.

 Pro kontrolu nastavení použijte následující příkazový řádek:

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

K povolení nastavení použijte následující příkazový řádek:

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Vyžadovat zabezpečený přenos pomocí Azure CLI

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Pomocí následujícího příkazu ověřte nastavení:

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

K povolení nastavení použijte následující příkaz:

```azurecli-interactive
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Další kroky

[Doporučení zabezpečení pro úložiště objektů BLOB](../blobs/security-recommendations.md)
