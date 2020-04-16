---
title: Použití spravované identity přiřazené systémem pro přístup k úložišti klíčů Azure
description: Zjistěte, jak vytvořit spravovanou identitu pro aplikace služby App Service a jak ji použít pro přístup k azure key vaultu
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: bb5288d043ab5638bb33c357cea55c64b03fcf1d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432122"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>Poskytnutí ověřování trezoru klíčů se spravovanou identitou

Spravovaná identita z Azure Active Directory umožňuje vaší aplikaci snadný přístup k dalším prostředkům chráněným službou Azure AD. Identita je spravována platformou Azure a nevyžaduje, abyste zrozovávat nebo otáčet žádné tajné klíče. Další informace najdete v tématu [Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md). 

Tento článek ukazuje, jak vytvořit spravovanou identitu pro aplikaci služby App Service a použít ji pro přístup k azure key vault. U aplikací hostovaných ve virtuálních počítačích Azure najdete [v tématu Použití spravované identity přiřazené systému Windows pro přístup k azure key vaultu](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md).


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky 

Chcete-li dokončit tuto příručku, musíte mít následující zdroje. 

- Trezor klíčů. Můžete použít existující trezor klíčů nebo vytvořit nový podle kroků v jednom z těchto rychlých startů:
   - [Vytvoření trezoru klíčů pomocí příkazového příkazového příkazu Azure](../secrets/quick-create-cli.md)
   - [Vytvoření trezoru klíčů pomocí Azure PowerShellu](../secrets/quick-create-powershell.md)
   - [Vytvořte trezor klíčů pomocí portálu Azure](../secrets/quick-create-portal.md).
- Existující aplikace služby App Service, ke kterému udělit přístup k trezoru klíčů. Můžete rychle vytvořit pomocí pokynů v [dokumentaci služby App Service](../../app-service/overview.md).
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) nebo [Azure PowerShell](/powershell/azure/overview). Případně můžete použít [portál Azure](https://portal.azure.com).


## <a name="adding-a-system-assigned-identity"></a>Přidání systémově přiřazené identity 

Nejprve je nutné přidat do aplikace identitu přiřazenou systémem. 
 
### <a name="azure-portal"></a>portál Azure 

Chcete-li nastavit spravovanou identitu na portálu, nejprve vytvoříte aplikaci jako normální a poté tuto funkci povolíte. 

1. Pokud používáte aplikaci funkcí, přejděte na **funkce platformy**. U ostatních typů aplikací přejděte v levém navigačním panelu dolů do skupiny **Nastavení.** 

1. Vyberte **spravovanou identitu**. 

1. Na kartě **Systém přiřazený** přepněte **stav** **na Zapnuto**. Klikněte na **Uložit**. 

    ![](../media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Azure CLI

Tento rychlý start vyžaduje Azure CLI verze 2.0.4 nebo novější. Aktuální verzi zjistíte spuštěním `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

Pokud se chcete přihlásit pomocí azure cli, použijte příkaz [az login:](/cli/azure/reference-index?view=azure-cli-latest#az-login)

```azurecli-interactive
az login
```

Další informace o možnostech přihlášení pomocí příkazového příkazu k řešení Azure najdete v tématu [Přihlášení pomocí azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest). 

Chcete-li vytvořit identitu pro tuto aplikaci, použijte příkaz přiřazení identity Azure CLI [az webapp](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) nebo příkaz [přiřazení identity az functionapp:](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign)


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

Poznamenejte `PrincipalId`si , které budou potřebné v další části.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Udělení přístupu aplikace ke službě Key Vault 

### <a name="azure-portal"></a>portál Azure

1.  Přejděte na prostředek trezoru klíčů. 

1.  Vyberte **zásady aplikace Access** a klepněte na tlačítko **Přidat zásady přístupu**. 

1.  V **seznamu Tajná oprávnění**vyberte možnost **Získat, Seznam**. 

1.  Zvolte **Vybrat hlavní**a do vyhledávacího pole zadejte název aplikace.  Vyberte aplikaci v seznamu výsledků a klepněte na **vybrat**. 

1.  Chcete-li dokončit přidání nové zásady přístupu, klepněte na tlačítko **Přidat.**

    ![](../media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Azure CLI

Chcete-li aplikaci udělit přístup k trezoru klíčů, použijte příkaz Azure CLI [az keyvault set-policy,](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) který poskytuje parametr **ObjectId** s **principalId,** které jste si poznamenali výše.

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>Další kroky

- [Zabezpečení azure trezoru klíčů: Správa identit a přístupu](overview-security.md#identity-and-access-management)
- [Poskytnutí ověřování trezoru klíčů pomocí zásad řízení přístupu](group-permissions-for-apps.md)
- [Zajistěte si trezor klíčů](secure-your-key-vault.md)).
- [Průvodce vývojářem azure key vaultu](developers-guide.md)
- Kontrola [doporučených postupů azure key vaultu](best-practices.md)