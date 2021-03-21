---
title: Nastavení ověření Azure pomocí Azure CLI
description: Jak nastavit a nakonfigurovat poskytovatele ověření identity pomocí Azure CLI.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: ae283785b4d4dc80c6b9b6c3997aaf82c9ff0f2f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178707"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Rychlý Start: nastavení ověření Azure pomocí Azure CLI

Začínáme s [ověřováním Azure pomocí Azure CLI](/cli/azure/ext/attestation/attestation).

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="get-started"></a>Začínáme

1. Nainstalovat toto rozšíření pomocí příkazu CLI

   ```azurecli
   az extension add --name attestation
   ```
   
1. Ověřit verzi

   ```azurecli
   az extension show --name attestation --query version
   ```

1. K přihlášení do Azure použijte následující příkaz:

   ```azurecli
   az login
   ```

1. V případě potřeby přepněte na předplatné pro Azure Attestation:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. V předplatném Zaregistrujte poskytovatele prostředků Microsoft. Attestation pomocí příkazu [AZ Provider Register](/cli/azure/provider#az_provider_register) :

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   Další informace o poskytovatelích prostředků Azure a o tom, jak je nakonfigurovat a spravovat, najdete v tématu [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md).

   > [!NOTE]
   > U předplatného stačí zaregistrovat poskytovatele prostředků.

1. Vytvořte skupinu prostředků pro poskytovatele ověřování identity. Další prostředky Azure můžete umístit do stejné skupiny prostředků, včetně virtuálního počítače s instancí klientské aplikace. Spuštěním příkazu [AZ Group Create](/cli/azure/group#az_group_create) vytvořte skupinu prostředků nebo použijte existující skupinu prostředků:

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>Vytvoření a Správa poskytovatele ověřování identity

Tady jsou příkazy, které můžete použít k vytvoření a správě poskytovatele ověření identity:

1. Spuštěním příkazu [AZ Attestation Create Create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) vytvořte poskytovatele ověření identity bez požadavku na podepsání zásad:

   ```azurecli
   az attestation create --name "myattestationprovider" --resource-group "MyResourceGroup" --location westus
   ```
   
1. Spuštěním příkazu [AZ atesting show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) načtěte vlastnosti poskytovatele ověření identity, jako je stav a AttestURI:

   ```azurecli
   az attestation show --name "myattestationprovider" --resource-group "MyResourceGroup"
   ```

   Tento příkaz zobrazí hodnoty, jako je následující výstup:

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

Zprostředkovatele ověření identity můžete odstranit pomocí příkazu [AZ atesting Delete](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete) :

```azurecli
az attestation delete --name "myattestationprovider" --resource-group "sample-resource-group"
```

## <a name="policy-management"></a>Správa zásad

Pomocí příkazů popsaných tady můžete poskytovat správu zásad pro poskytovatele ověření identity – jeden typ ověření identity najednou.

Příkaz [AZ Attestation Policy show show](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) vrátí aktuální zásadu pro zadaný Tee:

```azurecli
az attestation policy show --name "myattestationprovider" --resource-group "MyResourceGroup" --attestation-type SGX-IntelSDK
```

> [!NOTE]
> Příkaz zobrazí zásady ve formátu text i JWT.

Podporovány jsou následující typy TEE:

- `SGX-IntelSDK`
- `SGX-OpenEnclaveSDK`
- `TPM`

Pomocí příkazu [AZ attestationing Policy set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) nastavte novou zásadu pro zadaný typ ověření identity.

Nastavení zásad v textovém formátu pro daný druh typu ověřování pomocí cesty k souboru:

```azurecli
az attestation policy set --name testatt1 --resource-group testrg --attestation-type SGX-IntelSDK --new-attestation-policy-file "{file_path}"
```

Nastavení zásad ve formátu JWT pro daný druh typu ověřování pomocí cesty k souboru:

```azurecli
az attestation policy set --name "myattestationprovider" --resource-group "MyResourceGroup" \
--attestation-type SGX-IntelSDK -f "{file_path}" --policy-format JWT
```

## <a name="next-steps"></a>Další kroky

- [Jak vytvářet a podepisovat zásady ověření identity](author-sign-policy.md)
- [Implementace ověření identity pomocí SGX enklávy s použitím ukázek kódu](/samples/browse/?expanded=azure&terms=attestation)
