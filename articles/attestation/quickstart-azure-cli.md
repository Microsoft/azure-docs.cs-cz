---
title: Nastavení ověření Azure pomocí Azure CLI
description: Jak nastavit a nakonfigurovat poskytovatele ověření identity pomocí Azure CLI.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: e5ffcd56be1ce7427697cd3a75bd7c4232cc3b8a
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94667615"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Rychlý Start: nastavení ověření Azure pomocí Azure CLI

Začínáme s ověřováním Azure pomocí Azure CLI k nastavení ověření identity.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="get-started"></a>Začínáme

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

Pomocí tohoto postupu můžete vytvořit a spravovat poskytovatele ověření identity.

1. Spuštěním příkazu [AZ Attestation Create Create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) vytvořte poskytovatele ověření identity:

   ```azurecli
   az attestation create --resource-group attestationrg --name attestationProvider --location uksouth \
      --attestation-policy SgxDisableDebugMode --certs-input-path C:\test\policySignersCertificates.pem
   ```

   Parametr **--certs-Input-Path** určuje sadu důvěryhodných podpisových klíčů. Pokud pro tento parametr zadáte název souboru, poskytovatel ověření identity musí být nakonfigurovaný jenom se zásadami v podepsaném formátu JWT. V opačném případě může být zásada nakonfigurovaná v textovém nebo nepodepsaném formátu JWT. Informace o tokenu JWT najdete v tématu [základní koncepty](basic-concepts.md). Ukázky certifikátů najdete v tématu [příklady certifikátu podepsaného zásad ověření identity](policy-signer-examples.md).

1. Spuštěním příkazu [AZ atesting show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) načtěte vlastnosti poskytovatele ověření identity, jako je stav a AttestURI:

   ```azurecli
   az attestation show --resource-group attestationrg --name attestationProvider
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
az attestation delete --resource-group attestationrg --name attestationProvider
```

## <a name="policy-management"></a>Správa zásad

Pro správu zásad vyžaduje uživatel Azure AD následující oprávnění pro `Actions` :

- `Microsoft.Attestation/attestationProviders/attestation/read`
- `Microsoft.Attestation/attestationProviders/attestation/write`
- `Microsoft.Attestation/attestationProviders/attestation/delete`

Tato oprávnění je možné přiřadit uživateli Azure AD prostřednictvím role, jako je například `Owner` (oprávnění zástupných znaků), `Contributor` (oprávnění zástupných znaků), nebo `Attestation Contributor` (specifická oprávnění pouze pro Azure Attestation).  

Pro čtení zásad vyžaduje uživatel Azure AD následující oprávnění pro `Actions` :

- `Microsoft.Attestation/attestationProviders/attestation/read`

Toto oprávnění je možné přiřadit uživateli Azure AD prostřednictvím role, jako je například `Reader` (oprávnění zástupných znaků) nebo `Attestation Reader` (specifická oprávnění pouze pro Azure Attestation).

Pomocí příkazů popsaných tady můžete poskytovat správu zásad pro poskytovatele ověření identity, jednu TEE v jednom okamžiku.

Příkaz [AZ Attestation Policy show show](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) vrátí aktuální zásadu pro zadaný Tee:

```azurecli
az attestation policy show --resource-group attestationrg --name attestationProvider --tee SgxEnclave
```

> [!NOTE]
> Příkaz zobrazí zásady ve formátu text i JWT.

Podporovány jsou následující typy TEE:

- `CyResComponent`
- `OpenEnclave`
- `SgxEnclave`
- `VSMEnclave`

Pomocí příkazu [AZ attestationing Policy set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) nastavte novou zásadu pro zadaný Tee.

```azurecli
az attestation policy set --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --new-attestation-policy newAttestationPolicyname
```

Zásady ověřování ve formátu JWT musí obsahovat deklaraci s názvem `AttestationPolicy` . Podepsané zásady musí být podepsané klíčem, který odpovídá jakémukoli existujícímu certifikátu podepsanému zásadami.

Ukázky zásad najdete v tématu [Příklady zásad ověřování identity](policy-examples.md).

Příkaz [AZ Attestation Policy Reset](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_reset) nastaví novou zásadu pro zadaný Tee.

```azurecli
az attestation policy reset --resource-group attestationrg --name attestationProvider --tee SgxEnclave \
   --policy-jws "eyJhbGciOiJub25lIn0.."
```

## <a name="policy-signer-certificates-management"></a>Správa certifikátů podepsaných zásadou

Pomocí následujících příkazů spravujte certifikáty podepsané zásady pro poskytovatele ověření identity:

```azurecli
az attestation signer list --resource-group attestationrg --name attestationProvider

az attestation signer add --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."

az attestation signer remove --resource-group attestationrg --name attestationProvider \
   --signer "eyAiYWxnIjoiUlMyNTYiLCAie..."
```

Certifikát podepsané zásady je podepsaný token JWT s deklarací s názvem `maa-policyCertificate` . Hodnota deklarace identity je JWK, který obsahuje důvěryhodný podpisový klíč, který se má přidat. Token JWT musí být podepsán pomocí privátního klíče, který odpovídá libovolnému ze stávajících certifikátů podepsaných zásadou. Informace o tokenech JWT a JWK najdete v tématu [základní koncepty](basic-concepts.md).

Veškerá sémantická manipulace s certifikátem podepsané zásady se musí provádět mimo rozhraní příkazového řádku Azure CLI. Pokud jde o rozhraní příkazového řádku Azure CLI, jedná se o jednoduchý řetězec.

Ukázky certifikátů najdete v tématu [příklady certifikátu podepsaného zásad ověření identity](policy-signer-examples.md).

## <a name="next-steps"></a>Další kroky

- [Jak vytvářet a podepisovat zásady ověření identity](author-sign-policy.md)
- [Implementace ověření identity pomocí SGX enklávy s použitím ukázek kódu](/samples/browse/?expanded=azure&terms=attestation)
