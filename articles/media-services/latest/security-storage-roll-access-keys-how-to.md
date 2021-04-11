---
title: Po získání přístupových klíčů k úložišti aktualizovat Media Services V3 | Microsoft Docs
description: Tento článek obsahuje pokyny, jak aktualizovat Media Services V3 po získání přístupových klíčů k úložišti.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 385e6109a80c8e1e455f98b5d02ca5762f8051c7
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2021
ms.locfileid: "106281771"
---
# <a name="update-media-services-v3-after-rolling-storage-access-keys"></a>Po zavedení přístupových klíčů k úložišti aktualizovat Media Services V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Při vytváření nového účtu Azure Media Services (AMS) budete požádáni o výběr účtu Azure Storage.  K vašemu Media Services účtu můžete přidat více než jeden účet úložiště. Tento článek ukazuje, jak otočit klíče úložiště. Také ukazuje, jak přidat účty úložiště k účtu média.

K dokončení akcí popsaných v tomto článku byste měli používat [rozhraní Azure Resource Manager API](/rest/api/media/operations/azure-media-services-rest-api-reference) a [PowerShell](/powershell/module/az.media).  Další informace najdete v tématu [Správa prostředků Azure pomocí PowerShellu a správce prostředků](../../azure-resource-manager/management/manage-resource-groups-powershell.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="storage-access-key-generation"></a>Generování přístupového klíče k úložišti

Když se vytvoří nový účet úložiště, Azure vygeneruje 2 512 přístupové klíče k úložišti, které se používají k ověření přístupu k vašemu účtu úložiště. Aby vaše připojení úložiště byla bezpečnější, pravidelně znovu vygenerujte a otáčejte přístupový klíč úložiště. K dispozici jsou dva přístupové klíče (primární a sekundární), které vám umožní udržovat připojení k účtu úložiště pomocí jednoho přístupového klíče při opětovném vygenerování dalšího přístupového klíče. Tento postup se nazývá také "přístupové klíče pro vracení".

Media Services závisí na klíči úložiště, který je mu k dispozici. Konkrétně Lokátory, které se používají ke streamování nebo stahování vašich assetů, závisí na zadaném přístupovém klíči úložiště. Když se vytvoří účet AMS, ve výchozím nastavení se na primárním přístupovém klíči úložiště vybere závislost. Nicméně jako uživatel můžete aktualizovat klíč úložiště, který má AMS. Pomocí následujících kroků musíte dát Media Services, který klíč použít:

>[!NOTE]
> Pokud máte více účtů úložiště, provedete tento postup u každého účtu úložiště. Pořadí, ve kterém se klíče úložiště otočí, není pevně dané. Sekundární klíč můžete nejdřív otočit a pak primární klíč nebo naopak.
>
> Před provedením kroků v produkčním účtu se ujistěte, že je otestujete na předprodukčním účtu.
>

## <a name="steps-to-rotate-storage-keys"></a>Postup pro otočení klíčů úložiště
 
 1. Pomocí rutiny PowerShellu nebo webu [Azure](https://portal.azure.com/) Portal změňte primární klíč účtu úložiště.
 2. Zavolejte `Sync-AzMediaServiceStorageKeys` rutinu s příslušnými parametry k vynucení, aby účet média vybral klíče účtu úložiště.
 
    Následující příklad ukazuje, jak synchronizovat klíče s účty úložiště.
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. Počkejte hodinu nebo. Ověřte, jestli fungují scénáře streamování.
 4. Pomocí rutiny PowerShellu nebo Azure Portal změňte sekundární klíč účtu úložiště.
 5. Pokud `Sync-AzMediaServiceStorageKeys` chcete vynutit, aby se účet Media vybral nové klíče účtu úložiště, zavolejte PowerShell s příslušnými parametry.
 6. Počkejte hodinu nebo. Ověřte, jestli fungují scénáře streamování.
 
### <a name="a-powershell-cmdlet-example"></a>Příklad rutiny PowerShellu

Následující příklad ukazuje, jak získat účet úložiště a synchronizovat ho s účtem AMS.

```console
$regionName = "West US"
$resourceGroupName = "SkyMedia-USWest-App"
$mediaAccountName = "sky"
$storageAccountName = "skystorage"
$storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
```

## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Postup přidání účtů úložiště do účtu AMS

Následující článek ukazuje, jak přidat účty úložiště do účtu AMS: [připojení více účtů úložiště k účtu Media Services](storage-managing-multiple-storage-accounts-how-to.md).
