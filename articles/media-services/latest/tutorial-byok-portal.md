---
title: Použití klíčů spravovaných zákazníkem nebo BYOK v Azure Portal
description: V tomto kurzu pomocí Azure Portal povolíte klíče spravované zákazníkem nebo Přineste vlastní klíč (BYOK) s účtem úložiště Azure Media Services.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: 61df4b3424a4d73eca7a946c6bd8e5160fd6c529
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935378"
---
# <a name="tutorial-use-the-azure-portal-to-use-customer-managed-keys-or-byok-with-media-services"></a>Kurz: použití Azure Portal k použití klíčů spravovaných zákazníkem nebo BYOK s Media Services

S rozhraním API 2020-05-01 můžete použít klíč RSA spravovaný zákazníkem s účtem Azure Media Services, který má systémově spravovanou identitu. Tento kurz se věnuje postupům Azure Portal.

Používají se tyto služby:

- Azure Storage
- Azure Key Vault
- Azure Media Services

V tomto kurzu se naučíte používat Azure Portal k těmto akcím:

> [!div class="checklist"]
> - Vytvořte skupinu prostředků.
> - Vytvořte účet úložiště s identitou spravovanou systémem.
> - Vytvořte účet Media Services s identitou spravovanou systémem.
> - Vytvořte Trezor klíčů pro uložení klíče RSA spravovaného zákazníkem.

## <a name="prerequisites"></a>Požadavky

Předplatné Azure.

Pokud nemáte předplatné Azure, [Vytvořte si bezplatný zkušební účet](https://azure.microsoft.com/free/).

## <a name="system-managed-keys"></a>Klíče spravované systémem

<!-- Create a resource group -->
[!INCLUDE [create a resource group in the portal](./includes/task-create-resource-group-portal.md)]

> [!IMPORTANT]
> U následujících kroků při vytváření účtu úložiště vyberete volbu klíč spravovaný systémem v části Upřesnit nastavení.

<!-- Create a media services account -->

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

<!-- Create a key vault -->

[!INCLUDE [create a key vaultl](./includes/task-create-key-vault-portal.md)]

<!-- Enable CMK BYOK on the account -->
[!INCLUDE [enable CMK](./includes/task-enable-cmk-byok-portal.md)]

> [!IMPORTANT]
> U následujících kroků šifrování úložiště vyberete **volbu klíč spravovaný zákazníkem**.

<!-- Set encryption for storage account -->
[!INCLUDE [Set encryption for storage account](./includes/task-set-storage-encryption-portal.md)]

## <a name="change-the-key"></a>Změnit klíč

Media Services automaticky detekuje, kdy se klíč změnil. Volitelné: pro otestování tohoto procesu vytvořte další verzi klíče pro stejný klíč. Media Services by měl zjistit, že byl klíč změněn.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete nadále používat prostředky, které jste vytvořili, a nechcete, aby se *vám nadále účtují*, odstraňte je.

## <a name="next-steps"></a>Další kroky

Podívejte se na další článek, kde se dozvíte, jak:
> [!div class="nextstepaction"]
> [Kódování vzdáleného souboru na základě adresy URL a streamování videa pomocí REST](stream-files-tutorial-with-rest.md)
