---
title: Spouštění příkazů Prostředí PowerShell s přihlašovacími údaji Azure AD pro přístup k datům objektů blob nebo fronty
titleSuffix: Azure Storage
description: PowerShell podporuje přihlášení pomocí přihlašovacích údajů Azure AD pro spouštění příkazů na datech blob a front azure storage. Přístupový token je k dispozici pro relaci a slouží k autorizaci volajících operací. Oprávnění závisí na roli RBAC přiřazené k objektu zabezpečení Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 373b939ac63f31ccaf6a9f01fac92920e19074ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75553446"
---
# <a name="run-powershell-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Spouštění příkazů Prostředí PowerShell s přihlašovacími údaji Azure AD pro přístup k datům objektů blob nebo fronty

Azure Storage poskytuje rozšíření pro PowerShell, která umožňují přihlášení a spouštění skriptovacích příkazů pomocí přihlašovacích údajů služby Azure Active Directory (Azure AD). Když se přihlásíte k PowerShellu s přihlašovacími údaji Azure AD, vrátí se přístupový token OAuth 2.0. Tento token se automaticky používá PowerShell k autorizaci následných datových operací proti úložišti objektů Blob nebo Fronty. U podporovaných operací již nemusíte s příkazem předavat klíč účtu nebo token SAS.

Data objektů blob a fronty můžete přiřadit k datům objektů blob a fronty k objektu zabezpečení Azure AD prostřednictvím řízení přístupu založeného na rolích (RBAC). Další informace o rolích RBAC ve službě Azure Storage najdete v [tématu Správa přístupových práv k datům služby Azure Storage pomocí RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Podporované operace

Rozšíření úložiště Azure jsou podporované pro operace s daty objektů blob a fronty. Operace, které můžete volat, závisí na oprávněních udělených objektu zabezpečení Azure AD, pomocí kterého se přihlásíte k Prostředí PowerShell. Oprávnění k kontejnerům nebo frontám úložiště Azure se přiřazují prostřednictvím RBAC. Pokud vám například byla přiřazena role Čtečka dat objektů **Blob,** můžete spustit skriptovací příkazy, které čtou data z kontejneru nebo fronty. Pokud vám byla přiřazena role **Přispěvatel dat objektů Blob,** můžete spustit skriptovací příkazy, které čtou, zapisují nebo odstraňují kontejner nebo frontu nebo data, která obsahují.

Podrobnosti o oprávněních požadovaných pro každou operaci úložiště Azure v kontejneru nebo frontě najdete v [tématu Volání operací úložiště s tokeny OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-powershell-commands-using-azure-ad-credentials"></a>Volání příkazů Prostředí PowerShell pomocí přihlašovacích údajů Azure AD

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud chcete pomocí Azure PowerShellu přihlásit a spustit následné operace proti Azure Storage pomocí přihlašovacích údajů `-UseConnectedAccount` Azure AD, vytvořte kontext úložiště pro odkaz na účet úložiště a zahrňte parametr.

Následující příklad ukazuje, jak vytvořit kontejner v novém účtu úložiště z Azure PowerShellu pomocí přihlašovacích údajů Azure AD. Nezapomeňte nahradit zástupné hodnoty v úhlových závorkách vlastními hodnotami:

1. Přihlaste se ke svému účtu Azure pomocí příkazu [Connect-AzAccount:](/powershell/module/az.accounts/connect-azaccount)

    ```powershell
    Connect-AzAccount
    ```

    Další informace o přihlašování k Azure pomocí PowerShellu najdete [v tématu Přihlášení pomocí Azure PowerShellu](/powershell/azure/authenticate-azureps).

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

1. Získejte kontext účtu úložiště, který určuje nový účet úložiště voláním [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext). Při jednání s účtem úložiště můžete odkazovat na kontext namísto opakovaného předávání pověření. Zahrňte `-UseConnectedAccount` parametr pro volání všech následných datových operací pomocí přihlašovacích údajů azure ad:

    ```powershell
    $ctx = New-AzStorageContext -StorageAccountName "<storage-account>" -UseConnectedAccount
    ```

1. Před vytvořením kontejneru přiřaďte roli [přispěvatele dat objektů blob úložiště](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) sami sobě. I když jste vlastníkem účtu, budete potřebovat explicitní oprávnění k provádění operací dat proti účtu úložiště. Další informace o přiřazování rolí RBAC najdete [v tématu Udělení přístupu k datům objektů blob Azure a fronty pomocí RBAC na webu Azure Portal](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > Přiřazení rolí RBAC může trvat několik minut k šíření.

1. Vytvořte kontejner voláním [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Vzhledem k tomu, že toto volání používá kontext vytvořený v předchozích krocích, kontejner se vytvoří pomocí přihlašovacích údajů Azure AD.

    ```powershell
    $containerName = "sample-container"
    New-AzStorageContainer -Name $containerName -Context $ctx
    ```

## <a name="next-steps"></a>Další kroky

- [Použití prostředí PowerShell k přiřazení role RBAC pro přístup k datům objektů blob a fronty](storage-auth-aad-rbac-powershell.md)
- [Autorizace přístupu k datům objektů blob a fronty se spravovanými identitami pro prostředky Azure](storage-auth-aad-msi.md)
