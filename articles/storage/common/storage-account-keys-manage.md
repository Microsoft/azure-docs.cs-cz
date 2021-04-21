---
title: Správa přístupových klíčů pro účet
titleSuffix: Azure Storage
description: Naučte se zobrazovat, spravovat a střídat přístupové klíče k účtu úložiště.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/24/2020
ms.author: tamram
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 82d272f22295a5b68d1e8de3fb5a70c45d4c14a3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791206"
---
# <a name="manage-storage-account-access-keys"></a>Správa přístupových klíčů účtu úložiště

Když vytvoříte účet úložiště, Azure vygeneruje 2 512 přístupové klíče účtu úložiště. Tyto klíče se dají použít k autorizaci přístupu k datům v účtu úložiště prostřednictvím autorizace pomocí sdíleného klíče.

Microsoft doporučuje použít Azure Key Vault ke správě přístupových klíčů a k pravidelnému střídání a opětovnému vygenerování klíčů. Použití Azure Key Vault usnadňuje střídání klíčů bez přerušení v aplikacích. Klíče můžete také ručně otáčet.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>Zobrazit přístupové klíče účtu

Přístupové klíče k účtu můžete zobrazit a zkopírovat pomocí Azure Portal, PowerShellu nebo rozhraní příkazového řádku Azure. Azure Portal taky poskytuje připojovací řetězec pro váš účet úložiště, který můžete kopírovat.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Zobrazení a zkopírování přístupových klíčů účtu úložiště nebo připojovacího řetězce z Azure Portal:

1. V [Azure Portal](https://portal.azure.com)přejděte na svůj účet úložiště.
1. V části **Nastavení** vyberte **Přístupové klíče**. Zobrazí se přístupové klíče vašeho účtu a také úplný připojovací řetězec pro jednotlivé klíče.
1. Vyhledejte hodnotu **klíče** pod **klíč1** a kliknutím na tlačítko **Kopírovat** Zkopírujte klíč účtu.
1. Alternativně můžete zkopírovat celý připojovací řetězec. V části **key1** vyhledejte hodnotu **Připojovací řetězec** a kliknutím na tlačítko **Kopírovat** zkopírujte připojovací řetězec.

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="Snímek obrazovky znázorňující zobrazení přístupových klíčů v Azure Portal":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete načíst přístupové klíče k účtu pomocí PowerShellu, zavolejte příkaz [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey) .

Následující příklad načte první klíč. Chcete-li načíst druhý klíč, použijte `Value[1]` místo `Value[0]` . Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete zobrazit seznam klíčů pro přístup k účtu pomocí Azure CLI, zavolejte příkaz [AZ Storage Account Keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list) , jak je znázorněno v následujícím příkladu. Nezapomeňte nahradit hodnoty zástupných symbolů v závorkách vlastními hodnotami. 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

Pro přístup k Azure Storage můžete použít kterýkoli ze dvou klíčů, ale obecně je dobrým zvykem použít první klíč a při střídání klíčů rezervovat použití druhého klíče.

Aby mohl uživatel zobrazit nebo číst přístupové klíče účtu, musí být buď správcem služby, nebo musí mít přiřazenou roli Azure, která zahrnuje **Microsoft. Storage/storageAccounts/klíče listkey/Action**. Některé předdefinované role Azure, které zahrnují tuto akci, jsou role **vlastníka**, **přispěvatele** a **role služby operátora klíče účtu úložiště** . Další informace o roli správce služby najdete v tématu [role správců pro klasický odběr, role Azure a role Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md). Podrobné informace o předdefinovaných rolích pro Azure Storage najdete v části **úložiště** v [předdefinovaných rolích Azure pro službu Azure RBAC](../../role-based-access-control/built-in-roles.md#storage).

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Správa přístupových klíčů pomocí Azure Key Vault

Microsoft doporučuje, abyste ke správě a střídání přístupových klíčů používali Azure Key Vault. Vaše aplikace může bezpečně přistupovat k vašim klíčům v Key Vault, abyste se mohli vyhnout jejich ukládání do kódu aplikace. Další informace o použití Key Vault ke správě klíčů najdete v následujících článcích:

- [Správa klíčů účtu úložiště pomocí Azure Key Vault a PowerShellu](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Správa klíčů účtu úložiště pomocí Azure Key Vault a Azure CLI](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Ruční otočení přístupových klíčů

Společnost Microsoft doporučuje, abyste své přístupové klíče pravidelně přeměnili, aby se zajistilo zabezpečení účtu úložiště. Pokud je to možné, použijte Azure Key Vault ke správě přístupových klíčů. Pokud Key Vault nepoužíváte, budete muset klíče otočit ručně.

Přiřadí se dva přístupové klíče, abyste mohli klíče otáčet. Máte-li dvě klíče, zajistíte tím, že vaše aplikace bude udržovat v průběhu procesu přístup k Azure Storage.

> [!WARNING]
> Opětovné generování přístupových klíčů může mít vliv na jakékoli aplikace nebo služby Azure, které jsou závislé na klíči účtu úložiště. Všichni klienti, kteří používají klíč účtu pro přístup k účtu úložiště, se musí aktualizovat tak, aby používali nový klíč, včetně Media Services, cloudu, desktopových a mobilních aplikací a aplikací grafického uživatelského rozhraní pro Azure Storage, jako je například [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

K otočení přístupových klíčů účtu úložiště v Azure Portal:

1. Aktualizujte připojovací řetězce v kódu aplikace tak, aby odkazovaly na sekundární přístupový klíč pro účet úložiště.
1. V [Azure Portal](https://portal.azure.com)přejděte na svůj účet úložiště.
1. V části **Nastavení** vyberte **Přístupové klíče**.
1. Pokud chcete znovu vygenerovat primární přístupový klíč pro účet úložiště, vyberte tlačítko **znovu vygenerovat** vedle primárního přístupového klíče.
1. Aktualizujte připojovací řetězce v kódu tak, aby odkazovaly na nový primární přístupový klíč.
1. Stejným způsobem pak znovu vygenerujte sekundární přístupový klíč.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Postup při otáčení přístupových klíčů k účtu úložiště pomocí PowerShellu:

1. Aktualizujte připojovací řetězce v kódu aplikace tak, aby odkazovaly na sekundární přístupový klíč pro účet úložiště.
1. Voláním příkazu [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) znovu vygenerujte primární přístupový klíč, jak je znázorněno v následujícím příkladu:

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

1. Aktualizujte připojovací řetězce v kódu tak, aby odkazovaly na nový primární přístupový klíč.
1. Stejným způsobem pak znovu vygenerujte sekundární přístupový klíč. Chcete-li znovu vygenerovat sekundární klíč, použijte `key2` jako název klíče místo `key1` .

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

K otočení přístupových klíčů účtu úložiště pomocí Azure CLI:

1. Aktualizujte připojovací řetězce v kódu aplikace tak, aby odkazovaly na sekundární přístupový klíč pro účet úložiště.
1. Zavolejte příkazem [AZ Storage Account Keys obnovit](/cli/azure/storage/account/keys#az_storage_account_keys_renew) a znovu vygenerujte primární přístupový klíč, jak je znázorněno v následujícím příkladu:

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. Aktualizujte připojovací řetězce v kódu tak, aby odkazovaly na nový primární přístupový klíč.
1. Stejným způsobem pak znovu vygenerujte sekundární přístupový klíč. Chcete-li znovu vygenerovat sekundární klíč, použijte `key2` jako název klíče místo `key1` .

---

> [!NOTE]
> Microsoft doporučuje používat ve všech aplikacích současně jenom jeden z klíčů. Pokud na některých místech a v dalších klíčích 2 použijete klíč 1, nebudete moct tyto klíče otočit, aniž by aplikace ztratila přístup.

Aby uživatel mohl otočit přístupové klíče účtu, musí být buď správce služby, nebo musí mít přiřazenou roli Azure, která zahrnuje **Microsoft. Storage/storageAccounts/RegenerateKey/Action**. Některé předdefinované role Azure, které zahrnují tuto akci, jsou role **vlastníka**, **přispěvatele** a **role služby operátora klíče účtu úložiště** . Další informace o roli správce služby najdete v tématu [role správců pro klasický odběr, role Azure a role Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md). Podrobné informace o předdefinovaných rolích Azure pro Azure Storage najdete v části **úložiště** v [předdefinovaných rolích Azure pro službu Azure RBAC](../../role-based-access-control/built-in-roles.md#storage).

## <a name="next-steps"></a>Další kroky

- [Přehled účtu Azure Storage](storage-account-overview.md)
- [Vytvoření účtu úložiště](storage-account-create.md)
