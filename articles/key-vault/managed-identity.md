---
title: Pro přístup k Azure Key Vault použijte spravovanou identitu App Service aplikace přiřazenou systémem.
description: Naučte se vytvořit spravovanou identitu pro App Service aplikací a jak ji použít pro přístup k Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 8ac6f9be80d31804089ae2589998079dc7df66b3
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004303"
---
# <a name="use-an-app-service-managed-identity-to-access-azure-key-vault"></a>Použití spravované identity App Service pro přístup k Azure Key Vault 

V tomto článku se dozvíte, jak vytvořit spravovanou identitu pro aplikace App Service a použít ji pro přístup k Azure Key Vault. Pro aplikace hostované ve virtuálních počítačích Azure najdete informace v tématu [použití spravované identity přiřazené systémem Windows VM pro přístup k Azure Key Vault](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md). 

Spravovaná identita z Azure Active Directory umožňuje vaší aplikaci snadný přístup k dalším prostředkům chráněným službou Azure AD. Identita je spravovaná platformou Azure a nevyžaduje zřízení ani otočení jakýchkoli tajných klíčů. Další informace o spravovaných identitách v Azure AD najdete v tématu [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky 

K dokončení této příručky musíte mít následující prostředky. 

- Trezor klíčů. Můžete použít existující Trezor klíčů nebo vytvořit nový pomocí následujících kroků v jednom z těchto rychlých startů:
   - [Vytvoření trezoru klíčů pomocí Azure CLI](quick-create-cli.md)
   - [Vytvoření trezoru klíčů pomocí Azure PowerShell](quick-create-powershell.md)
   - [Vytvořte Trezor klíčů pomocí Azure Portal](quick-create-portal.md).
- Stávající aplikace App Service, pro kterou má být udělen přístup k trezoru klíčů. Můžete ho rychle vytvořit podle kroků v [dokumentaci k App Service](../app-service/overview.md) ./


## <a name="adding-a-system-assigned-identity"></a>Přidání identity přiřazené systémem 

Nejdřív musíte do aplikace přidat identitu přiřazenou systémem. 
 
### <a name="azure-portal"></a>portál Azure 

Pokud chcete na portálu nastavit spravovanou identitu, nejdřív vytvořte aplikaci jako normální a pak tuto funkci povolte. 

1. Pokud používáte aplikaci Function App, přejděte k **funkcím platformy**. U ostatních typů aplikací se posuňte dolů ke skupině **Nastavení** v levém navigačním panelu. 

1. Vyberte **spravovanou identitu**. 

1. V rámci karty **přiřazené systémem** přepněte **stav** na **zapnuto**. Klikněte na **Uložit**. 

    ![](./media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Azure CLI

Tento rychlý Start vyžaduje Azure CLI verze 2.0.4 nebo novější. Aktuální verzi zjistíte spuštěním `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

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

Poznamenejte `PrincipalId`si, který bude potřeba v další části.

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

    ![](./media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Azure CLI

Pokud chcete vaší aplikaci udělit přístup k vašemu trezoru klíčů, použijte příkaz Azure CLI [AZ Key trezor set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) a zadejte parametr **objectID** s **principalId* , které jste si poznamenali výše.

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>Další postup

- Přečtěte si [přehled Azure Key Vault](key-vault-overview.md)
- Další informace najdete v [příručce pro vývojáře Azure Key Vault](key-vault-developers-guide.md) .
- Další informace o [klíčích, tajných klíčích a certifikátech](about-keys-secrets-and-certificates.md)
- Kontrola [Azure Key Vault osvědčených postupů](key-vault-best-practices.md)
