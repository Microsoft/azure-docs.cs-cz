---
title: Vyžadovat zabezpečený přenos pro zajištění zabezpečeného připojení
titleSuffix: Azure Storage
description: Zjistěte, jak vyžadovat zabezpečený přenos pro požadavky do Azure Storage. Pokud požadujete zabezpečený převod pro účet úložiště, všechny požadavky pocházející z nezabezpečeného připojení jsou odmítnuty.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/12/2019
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 3b2d78bd929e23d49a57f337022f6678114bb5fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75457443"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Vyžadovat zabezpečený přenos pro zajištění zabezpečeného připojení

Účet úložiště můžete nakonfigurovat tak, aby přijímal požadavky ze zabezpečených připojení, a to pouze nastavením **vlastnosti Požadované zabezpečeného převodu** pro účet úložiště. Pokud požadujete zabezpečený přenos, všechny požadavky pocházející z nezabezpečeného připojení jsou odmítnuty. Společnost Microsoft doporučuje, abyste vždy vyžadovali zabezpečený přenos pro všechny účty úložiště.

Když je vyžadován zabezpečený přenos, musí být provedeno volání operace rozhraní API úložiště Azure STORAGE přes protokol HTTPS. Jakýkoli požadavek poslaná přes protokol HTTP je odmítnut.

Připojení ke sdílené složce Azure přes SMB bez šifrování se nezdaří, když je pro účet úložiště vyžadován zabezpečený přenos. Příklady nezabezpečených připojení zahrnují připojení přes SMB 2.1, SMB 3.0 bez šifrování nebo některé verze klienta SMB linuxu.

Ve výchozím nastavení je **vlastnost Požadováno zabezpečeného přenosu** povolena při vytváření účtu úložiště na webu Azure Portal. Je však zakázána při vytvoření účtu úložiště s sadou SDK.

> [!NOTE]
> Vzhledem k tomu, že Azure Storage nepodporuje protokol HTTPS pro vlastní názvy domén, tato možnost se nepoužije, když používáte vlastní název domény. A klasické účty úložiště nejsou podporovány.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Vyžadovat zabezpečený přenos na webu Azure Portal

Vlastnost **Požadováno zabezpečeného přenosu** můžete zapnout při vytváření účtu úložiště na [webu Azure Portal](https://portal.azure.com). Můžete ji také povolit pro existující účty úložiště.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Vyžadovat zabezpečený převod pro nový účet úložiště

1. Otevřete podokno **Vytvořit účet úložiště** na webu Azure Portal.
1. V části **Zabezpečené přenosy**vyberte **Možnost Povoleno**.

   ![Vytvořit okno účtu úložiště](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Vyžadovat zabezpečený převod pro existující účet úložiště

1. Vyberte existující účet úložiště na webu Azure Portal.
1. V podokně nabídky účtu úložiště vyberte v části **NASTAVENÍ** **položku Konfigurace**.
1. V části **Zabezpečené přenosy**vyberte **Možnost Povoleno**.

   ![Podokno nabídky účtu úložiště](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Vyžadovat zabezpečený přenos z kódu

Chcete-li vyžadovat zabezpečený přenos programově, nastavte _vlastnost supportsHttpsTrafficOnly_ v účtu úložiště. Tuto vlastnost můžete nastavit pomocí rozhraní REST API zprostředkovatele prostředků úložiště, klientských knihoven nebo nástrojů:

* [ROZHRANÍ API PRO ODPOČINEK](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [Cli](/cli/azure/storage/account)
* [Nodejs](https://www.npmjs.com/package/azure-arm-storage/)
* [Sada SDK rozhraní .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [Python SDK](https://pypi.org/project/azure-mgmt-storage)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>Vyžadovat zabezpečený přenos pomocí PowerShellu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tato ukázka vyžaduje modul Azure PowerShell Az verze 0.7 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

Spuštěním příkazu `Connect-AzAccount` vytvořte připojení k Azure.

 Toto nastavení zkontrolujte pomocí následujícího příkazového řádku:

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Toto nastavení povolte pomocí následujícího příkazového řádku:

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Vyžadovat zabezpečený přenos pomocí azure cli

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Chcete-li zkontrolovat nastavení, použijte následující příkaz:

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Chcete-li toto nastavení povolit, použijte následující příkaz:

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

[Doporučení zabezpečení pro úložiště objektů Blob](../blobs/security-recommendations.md)
