---
title: Pro přístup k Azure Key Vault použít spravovanou identitu přiřazenou systémem
description: Naučte se vytvořit spravovanou identitu pro App Service aplikací a jak ji použít pro přístup k Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 0aa9a9728c3a59b6f47ef1427cbcd368d4f1e77c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586116"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>Zajištění Key Vault ověřování pomocí spravované identity

Spravovaná identita z Azure Active Directory umožňuje vaší aplikaci snadný přístup k dalším prostředkům chráněným službou Azure AD. Identita je spravovaná platformou Azure a nevyžaduje zřízení ani otočení jakýchkoli tajných klíčů. Další informace najdete v tématu [spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md). 

V tomto článku se dozvíte, jak vytvořit spravovanou identitu pro aplikaci App Service a použít ji pro přístup k Azure Key Vault. Pro aplikace hostované ve virtuálních počítačích Azure najdete informace v tématu [použití spravované identity přiřazené systémem Windows VM pro přístup k Azure Key Vault](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md).


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky 

K dokončení této příručky musíte mít následující prostředky. 

- Trezor klíčů. Můžete použít existující Trezor klíčů nebo vytvořit nový pomocí následujících kroků v jednom z těchto rychlých startů:
   - [Vytvoření trezoru klíčů pomocí Azure CLI](../secrets/quick-create-cli.md)
   - [Vytvoření trezoru klíčů pomocí Azure PowerShell](../secrets/quick-create-powershell.md)
   - [Vytvořte Trezor klíčů pomocí Azure Portal](../secrets/quick-create-portal.md).
- Stávající aplikace App Service, pro kterou má být udělen přístup k trezoru klíčů. Můžete ho rychle vytvořit podle kroků v [dokumentaci App Service](../../app-service/overview.md).
- Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo [Azure PowerShell](/powershell/azure/). Alternativně můžete použít [Azure Portal](https://portal.azure.com).


## <a name="adding-a-system-assigned-identity"></a>Přidání identity přiřazené systémem 

Nejdřív musíte do aplikace přidat identitu přiřazenou systémem. 
 
### <a name="azure-portal"></a>portál Azure 

Pokud chcete na portálu nastavit spravovanou identitu, nejdřív vytvořte aplikaci jako normální a pak tuto funkci povolte. 

1. Pokud používáte aplikaci Function App, přejděte k **funkcím platformy**. U ostatních typů aplikací se posuňte dolů ke skupině **Nastavení** v levém navigačním panelu. 

1. Vyberte **spravovanou identitu**. 

1. V rámci karty **přiřazené systémem** přepněte **stav** na **zapnuto**. Klikněte na **Uložit**. 

   ![Snímek obrazovky, který ukazuje uložení identity přiřazené systémem.](../media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Azure CLI

Tento rychlý Start vyžaduje Azure CLI verze 2.0.4 nebo novější. Aktuální verzi zjistíte spuštěním `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

Pokud se chcete přihlásit pomocí Azure CLI, použijte příkaz [AZ Login](/cli/azure/reference-index?view=azure-cli-latest#az-login) :

```azurecli-interactive
az login
```

Další informace o možnostech přihlášení pomocí Azure CLI najdete v tématu [přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest). 

K vytvoření identity pro tuto aplikaci použijte příkaz Azure CLI [AZ WebApp identity Assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) nebo [AZ functionapp identity Assign](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign) :


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

Poznamenejte si `PrincipalId` , který bude potřeba v další části.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Udělit aplikaci přístup k Key Vault 

### <a name="azure-portal"></a>portál Azure

1.  Přejděte na prostředek Key Vault. 

1.  Vyberte **zásady přístupu** a klikněte na **Přidat zásady přístupu**. 

1.  V **oprávnění ke tajným klíčům**vyberte **získat, seznam**. 

1.  Zvolte **Vybrat objekt zabezpečení**a do vyhledávacího pole zadejte název aplikace.  V seznamu výsledků vyberte aplikaci a klikněte na **Vybrat**. 

1.  Kliknutím na **Přidat** dokončete přidávání nových zásad přístupu.

    ![Snímek obrazovky, který ukazuje přidání nové zásady přístupu v Azure Portal.](../media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Azure CLI

Pokud chcete vaší aplikaci udělit přístup k vašemu trezoru klíčů, použijte příkaz Azure CLI [AZ Key trezor set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) a zadejte parametr **objectID** s **principalId** , které jste si poznamenali výše.

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>Další kroky

- [Azure Key Vault zabezpečení: Správa identit a přístupu](overview-security.md#identity-and-access-management)
- [Zajištění Key Vault ověřování pomocí zásad řízení přístupu](group-permissions-for-apps.md)
- [Zabezpečte svůj Trezor klíčů](secure-your-key-vault.md).
- [Azure Key Vault příručka pro vývojáře](developers-guide.md)
- Kontrola [Azure Key Vault osvědčených postupů](best-practices.md)