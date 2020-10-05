---
title: Rychlý Start Azure – vytvoření spravovaného modulu HSM pomocí šablony Azure Resource Manager
description: Rychlý Start ukazující, jak vytvořit Azure Azure Key Vault spravovaný HSM pomocí šablony Správce prostředků
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.custom: mvc
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: 4d1488d6dd2e5d08ae774ca88b7ab41b2020efe5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91000929"
---
# <a name="quickstart-create-an-key-vault-managed-hsm-using-an-azure-resource-manager-template"></a>Rychlý Start: vytvoření spravovaného modulu HSM s Key Vault pomocí šablony Azure Resource Manager

Spravovaný modul HSM je plně spravovaná cloudová služba s vysokou dostupností, která vyhovuje standardům, která umožňuje chránit kryptografické klíče pro cloudové aplikace, a to pomocí ověřených HSM **úrovně 3 FIPS 140-2 Level 3** .  

Tento rychlý Start se zaměřuje na proces nasazení šablony Správce prostředků pro vytvoření spravovaného modulu HSM.  [Šablona Resource Manageru](../../azure-resource-manager/templates/overview.md) je soubor JSON (JavaScript Object Notation), který definuje infrastrukturu a konfiguraci projektu. Tato šablona využívá deklarativní syntaxi, která umožňuje prohlásit, co máte v úmyslu vytvořit, aniž by k tomu bylo nutné psát sekvence programových příkazů. Pokud chcete získat další informace o vývoji šablon Resource Manageru, přečtěte si [dokumentaci k Resource Manageru](../../azure-resource-manager/index.yml) a [referenční informace k šablonám](/azure/templates/microsoft.keyvault/allversions).

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Předpoklady

K dokončení kroků v tomto článku musíte mít následující položky:

- Předplatné Microsoft Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial).
- Verze Azure CLI 2.12.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI]( /cli/azure/install-azure-cli) .


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud se chcete přihlásit k Azure pomocí rozhraní příkazového řádku, můžete zadat:

```azurecli
az login
```

Další informace o možnostech přihlášení prostřednictvím rozhraní příkazového řádku najdete v tématu [přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true) .

## <a name="create-a-manage-hsm"></a>Vytvoření modulu pro správu HSM

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-managed-hsm-create/).

Prostředek Azure definovaný v šabloně:

* **Microsoft. ManagedHSMs trezor/**: vytvoří modul HSM spravovaný Azure Key Vault.

Další ukázky šablon Azure Key Vault najdete [tady](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault).

Šablona vyžaduje ID objektu přidruženého k vašemu účtu. Pokud ho chcete najít, použijte příkaz Azure CLI [AZ AD User show](/cli/azure/ad/user?view=azure-cli-latest&preserve-view=true#az_ad_user_show) a předejte e-mailovou adresu `--id` parametru. Výstup lze omezit pouze na ID objektu pouze s `--query` parametrem.

```azurecli-interactive
az ad user show --id <your-email-address> --query "objectId"
```

Možná budete potřebovat i ID tenanta. Pokud ho chcete najít, použijte příkaz Azure CLI [AZ AD User show](/cli/azure/account?view=azure-cli-latest&preserve-view=true#az_account_show) . Výstup můžete omezit pouze na ID tenanta s `--query` parametrem.

 ```azurecli-interactive
 az account show --query "tenantId"
 ```

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří Trezor klíčů a tajný klíč.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-managed-hsm-create%2Fazuredeploy.json"><img src="../media/deploy-to-azure.svg" alt="deploy to azure"/></a>

2. Vyberte nebo zadejte následující hodnoty.

    Pokud není zadaný, použijte k vytvoření trezoru klíčů a tajného klíče výchozí hodnotu.

    - **Předplatné**: vyberte předplatné Azure.
    - **Skupina prostředků**: vyberte **vytvořit novou**, zadejte jedinečný název skupiny prostředků a pak klikněte na **OK**.
    - **Umístění**: vyberte umístění. Například **střed USA – jih**.
    - **managedHSMName**: zadejte název spravovaného HSM.
    - **SKU**: zadejte název a rodinu spravovaného modulu HSM, který chcete vytvořit.  V tomto rychlém startu zadejte "Standard_B1" pro název a "B" pro rodinu.
    - **ID tenanta**: funkce šablony automaticky NAČTE vaše ID tenanta; neměňte výchozí hodnotu.  Pokud není žádná hodnota, zadejte ID tenanta, které jste získali v části [požadavky](#prerequisites).
    * **initialAdminObjectIds**: Zadejte ID objektu, který jste získali v části [požadavky](#prerequisites).

3. Vyberte **Koupit**. Po úspěšném nasazení trezoru klíčů obdržíte oznámení:

K nasazení šablony se použije Azure Portal. Kromě Azure Portal můžete použít také Azure PowerShell, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili spravovaný modul HSM. Tento spravovaný modul HSM nebude plně funkční, dokud nebude aktivovaný. Informace o tom, jak aktivovat modul HARDWAROVÉho zabezpečení, najdete v tématu [Aktivace spravovaného modulu HSM](quick-create-cli.md#activate-your-managed-hsm) .

- Přečtěte si [Přehled spravovaného HSM](overview.md)
- Další informace o [správě klíčů ve spravovaném modulu HSM](key-management.md)
- Projděte si spravované osvědčené [postupy modulu HSM](best-practices.md)