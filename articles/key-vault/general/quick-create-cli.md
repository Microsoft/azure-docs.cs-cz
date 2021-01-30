---
title: Rychlý Start – vytvoření Azure Key Vault pomocí Azure CLI
description: Rychlý Start ukazující, jak vytvořit Azure Key Vault pomocí Azure CLI
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: f7f6f5d82c5fda7101e80ddcb8b17dc6bdef6532
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070250"
---
# <a name="quickstart-create-a-key-vault-using-the-azure-cli"></a>Rychlý Start: vytvoření trezoru klíčů pomocí Azure CLI

Azure Key Vault je cloudová služba, která poskytuje zabezpečené úložiště klíčů, [tajných](../secrets/index.yml) [kódů](../keys/index.yml)a [certifikátů](../certificates/index.yml). Další informace o Key Vault najdete v tématu [o Azure Key Vault](overview.md). Další informace o tom, co je možné uložit v trezoru klíčů, najdete v tématu [informace o klíčích, tajných klíčích a certifikátech](about-keys-secrets-certificates.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - V tomto rychlém startu se vyžaduje verze Azure CLI 2.0.4 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[!INCLUDE [Create a resource group](../../../includes/key-vault-cli-rg-creation.md)]

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů.

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-kv-creation.md)]

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Create a key vault](../../../includes/key-vault-cli-delete-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Key Vault a odstranili jste ho. Další informace o Key Vault a o tom, jak je integrovat s vašimi aplikacemi, najdete dál v článcích níže.

- Přečtěte si [přehled Azure Key Vault](overview.md)
- Přečtěte si [Přehled zabezpečení Azure Key Vault](security-overview.md)
- Podívejte se na referenční informace pro [Azure CLI AZ klíčů trezor](/cli/azure/keyvault) .

