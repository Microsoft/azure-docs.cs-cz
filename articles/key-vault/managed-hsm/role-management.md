---
title: Správa rolí spravovaného datového roviny HSM – Azure Key Vault | Microsoft Docs
description: Pomocí tohoto článku můžete spravovat přiřazení rolí pro spravovaný modul HSM.
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: a4cc898744109475bc119f37350d1b689c550f58
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102209556"
---
# <a name="managed-hsm-role-management"></a>Správa rolí pro Managed HSM

> [!NOTE]
> Key Vault podporuje dva typy prostředků: trezory a spravované HSM. Tento článek se týká **spravovaného modulu HSM**. Pokud se chcete dozvědět, jak spravovat trezor, přečtěte si téma [správa Key Vault pomocí rozhraní příkazového řádku Azure CLI](../general/manage-with-cli2.md).

Přehled spravovaného modulu HSM najdete v tématu [co je spravovaný modul HSM?](overview.md). Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Tento článek ukazuje, jak spravovat role pro spravovanou rovinu dat HSM. Další informace o spravovaném modelu řízení přístupu HSM najdete v tématu [spravované řízení přístupu HSM](access-control.md).

Aby objekt zabezpečení (například uživatel, instanční objekt, skupina nebo spravovaná identita) mohl provádět operace správy roviny dat HSM, musí jim být přiřazena role, která umožňuje provádět tyto operace. Například pokud chcete, aby aplikace mohla provádět operace podepsání pomocí klíče, musí být přiřazena role, která obsahuje "Microsoft. webkey trezor/managedHSM/Keys/Sign/Action" jako jednu z akcí dat. Roli je možné přiřadit k určitému oboru. Spravovaná místní RBAC nástroje HSM podporuje dva obory, HSM ( `/` nebo `/keys` ) a per Key ( `/keys/<keyname>` ).

Seznam všech spravovaných rolí modulu HSM a operací, které umožňují, najdete v tématu [vestavěné předdefinované role HSM](built-in-roles.md).

## <a name="prerequisites"></a>Předpoklady

Pokud chcete používat příkazy rozhraní příkazového řádku Azure CLI v tomto článku, musíte mít následující položky:

* Předplatné Microsoft Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial).
* Verze Azure CLI 2.12.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).
* Spravovaný modul HSM v rámci vašeho předplatného. Informace najdete v tématu [rychlý Start: zřízení a aktivace spravovaného modulu HSM pomocí Azure CLI](quick-create-cli.md) , který umožňuje zřídit a aktivovat spravovaný modul HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud se chcete přihlásit k Azure pomocí rozhraní příkazového řádku, můžete zadat:

```azurecli
az login
```

Další informace o možnostech přihlášení prostřednictvím rozhraní příkazového řádku najdete v tématu [přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli) .

## <a name="create-a-new-role-assignment"></a>Vytvořit nové přiřazení role

### <a name="assign-roles-for-all-keys"></a>Přiřadit role pro všechny klíče

Pomocí `az keyvault role assignment create` příkazu přiřaďte uživateli **spravovanou roli kryptografický pracovník HSM** , kterou identifikuje hlavní název uživatele (upn) **uživatel2 \@ contoso.com** pro všechny  **klíče** (obor `/keys` ) v ContosoHSM.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys
```

### <a name="assign-role-for-a-specific-key"></a>Přiřazení role pro určitý klíč

Pomocí `az keyvault role assignment create` příkazu přiřaďte uživateli **spravovanou roli kryptografický pracovník HSM** , kterou určí hlavní název uživatele (upn) **uživatel2 \@ contoso.com** pro konkrétní klíč s názvem **myrsakey**.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey
```

## <a name="list-existing-role-assignments"></a>Vypsat existující přiřazení rolí

Slouží `az keyvault role assignment list` k vypsání přiřazení rolí.

Všechna přiřazení rolí v oboru/(výchozí, pokud není zadaný žádný obor) pro všechny uživatele (výchozí, pokud se nezadá žádná--nabyvatel)

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM
```

Všechna přiřazení rolí na úrovni HSM pro určitého uživatele **user1@contoso.com** .

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user@contoso.com
```

> [!NOTE]
> Když je Scope/(nebo/Keys), příkaz list zobrazí jenom všechna přiřazení rolí na nejvyšší úrovni a nezobrazuje přiřazení rolí na úrovni jednotlivých klíčů.

Všechna přiřazení rolí pro konkrétního uživatele **user2@contoso.com** pro konkrétní klíč **myrsakey**.

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey
```

Konkrétní přiřazení role pro **kryptografického správce HSM spravované** role pro konkrétního uživatele **user2@contoso.com** pro konkrétního Key **myrsakey**


```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey --role "Managed HSM Crypto Officer"
```

## <a name="delete-a-role-assignment"></a>Odstranění přiřazení role

Pomocí `az keyvault role assignment delete` příkazu můžete odstranit **spravovanou roli kryptografického důstojníka HSM** přiřazenou uživateli **uživatel2 \@ contoso.com** pro Key **myrsakey2**.

```azurecli-interactive
az keyvault role assignment delete --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey2
```

## <a name="list-all-available-role-definitions"></a>Zobrazit seznam všech dostupných definic rolí

Pomocí `az keyvault role definition list` příkazu můžete vypsat všechny definice rolí.

```azurecli-interactive
az keyvault role definition list --hsm-name ContosoMHSM
```

## <a name="next-steps"></a>Další kroky

- Podívejte se na přehled [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/overview.md).
- Projděte si kurz [spravované správy rolí HSM](role-management.md) .
- Další informace o [spravovaném modelu řízení přístupu HSM](access-control.md)
- Zobrazit všechny [předdefinované role pro správu místních RBAC nástroje HSM](built-in-roles.md)
