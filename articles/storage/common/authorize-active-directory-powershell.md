---
title: Spuštění příkazů PowerShellu s přihlašovacími údaji Azure AD pro přístup k datům objektů BLOB nebo Queue
titleSuffix: Azure Storage
description: PowerShell podporuje přihlášení pomocí přihlašovacích údajů Azure AD ke spouštění příkazů v Azure Storage dat objektů BLOB a front. K dispozici je přístupový token pro relaci a slouží k autorizaci operací volání. Oprávnění závisí na roli RBAC přiřazené k objektu zabezpečení Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 373b939ac63f31ccaf6a9f01fac92920e19074ed
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75553446"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Spuštění příkazů PowerShellu s přihlašovacími údaji Azure AD pro přístup k datům objektů BLOB nebo Queue

Azure Storage poskytuje rozšíření pro PowerShell, která umožňují přihlášení a spouštění příkazů skriptování s přihlašovacími údaji Azure Active Directory (Azure AD). Když se přihlásíte k PowerShellu pomocí přihlašovacích údajů Azure AD, vrátí se přístupový token OAuth 2,0. Tento token se automaticky používá v PowerShellu k autorizaci následných operací s daty v úložišti objektů BLOB nebo Queue. U podporovaných operací už nemusíte předávat klíč účtu nebo token SAS pomocí příkazu.

Pomocí řízení přístupu na základě role (RBAC) můžete přiřadit k objektu zabezpečení služby Azure AD oprávnění k objektům blob a frontám. Další informace o rolích RBAC v Azure Storage najdete v tématu [Správa přístupových práv k datům Azure Storage pomocí RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Podporované operace

Rozšíření Azure Storage jsou podporovaná pro operace s daty objektů BLOB a front. Operace, které můžete volat, závisí na oprávněních udělených objektu zabezpečení služby Azure AD, se kterým se přihlašujete k PowerShellu. Oprávnění pro Azure Storage kontejnery nebo fronty jsou přiřazena prostřednictvím RBAC. Pokud jste například přiřadili roli **čtečky dat objektů BLOB** , můžete spustit skriptovací příkazy, které čtou data z kontejneru nebo fronty. Pokud jste přiřadili roli **Přispěvatel dat objektu BLOB** , můžete spustit skriptovací příkazy, které čtou, zapisují nebo odstraňují kontejner nebo frontu nebo data, která obsahují.

Podrobnosti o oprávněních potřebných pro jednotlivé operace Azure Storage v kontejneru nebo frontě najdete v tématu [volání operací úložiště s tokeny OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Volání příkazů PowerShellu pomocí přihlašovacích údajů Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud chcete použít Azure PowerShell k přihlášení a spuštění dalších operací s Azure Storage pomocí přihlašovacích údajů Azure AD, vytvořte kontext úložiště pro odkazování na účet úložiště a zahrňte parametr `-UseConnectedAccount`.

Následující příklad ukazuje, jak vytvořit kontejner v novém účtu úložiště z Azure PowerShell pomocí vašich přihlašovacích údajů Azure AD. Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami:

1. Přihlaste se ke svému účtu Azure pomocí příkazu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) :

    ```powershell
    Connect-AzAccount
    ```

    Další informace o přihlášení k Azure pomocí PowerShellu najdete v tématu věnovaném [přihlášení pomocí Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Vytvořte skupinu prostředků Azure voláním [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

    ```powershell
    $resourceGroup = "sample-resource-group-ps"
    $location = "eastus"
    New-AzResourceGroup -Name $resourceGroup -Location $location
    ```

1. Vytvořte účet úložiště voláním [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
      -Name "<storage-account>" `
      -SkuName Standard_LRS `
      -Location $location `
    ```

1. Získejte kontext účtu úložiště, který určuje nový účet úložiště voláním [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Když pracujete na účtu úložiště, můžete místo opakovaného předání přihlašovacích údajů odkazovat na kontext. Zahrňte parametr `-UseConnectedAccount` pro volání jakýchkoli následných operací s daty pomocí přihlašovacích údajů Azure AD:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Než vytvoříte kontejner, přiřaďte roli [Přispěvatel dat objektů BLOB úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) sami sobě. I když jste vlastníkem účtu, potřebujete explicitní oprávnění k provádění operací s daty v účtu úložiště. Další informace o přiřazování rolí RBAC najdete [v tématu udělení přístupu k datům objektů blob Azure a frontě pomocí RBAC v Azure Portal](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Rozšiřování přiřazení rolí RBAC může trvat několik minut.

1. Vytvořte kontejner voláním [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Vzhledem k tomu, že toto volání používá kontext vytvořený v předchozích krocích, kontejner se vytvoří pomocí vašich přihlašovacích údajů Azure AD.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Další kroky

- [Přiřazení role RBAC pro přístup k datům BLOB a Queue pomocí PowerShellu](storage-auth-aad-rbac-powershell.md)
- [Autorizace přístupu k datům BLOB a Queue pomocí spravovaných identit pro prostředky Azure](storage-auth-aad-msi.md)
