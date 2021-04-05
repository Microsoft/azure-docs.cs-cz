---
title: KeyVaultCertificateSelector – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. klíčů trezor. KeyVaultCertificateSelector pro Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1ac6fd5a3f1e581fc9a676fe7f8f27b9742e1581
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93101217"
---
# <a name="microsoftkeyvaultkeyvaultcertificateselector-ui-element"></a>Microsoft. klíčů trezor. KeyVaultCertificateSelector – element uživatelského rozhraní

Ovládací prvek pro výběr certifikátu trezoru klíčů.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Uživatel se zobrazí s možností vybrat dostupný certifikát.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-select.png" alt-text="Microsoft. klíčů trezor. KeyVaultCertificateSelector":::

Po výběru **Vybrat certifikát** může uživatel zadat Trezor klíčů a certifikát z trezoru klíčů.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-certificate.png" alt-text="Microsoft. klíčů trezor. KeyVaultCertificateSelector vybrat certifikát":::

Ovládací prvek se aktualizuje tak, aby zobrazoval vybraný Trezor klíčů a název certifikátu.

:::image type="content" source="./media/managed-application-elements/microsoft-keyvault-keyvaultcertificateselector-result.png" alt-text="Microsoft. klíčů trezor. KeyVaultCertificateSelector zobrazit vybraný certifikát":::

## <a name="schema"></a>Schéma

```json
{
  "name": "keyVaultCertificateSelection",
  "type": "Microsoft.KeyVault.KeyVaultCertificateSelector",
  "visible": true,
  "toolTip": "Select certificate",
  "label": "KeyVault certificates selection"
}
```

## <a name="sample-output"></a>Ukázkový výstup

```json
{
  "keyVaultName": "azuretestkeyvault1",
  "keyVaultId": "/subscriptions/{subscription-id}/resourceGroups/resourcegroup1/providers/Microsoft.KeyVault/vaults/azuretestkeyvault1",
  "certificateName": "certificate1",
  "certificateUrl": "https://azuretestkeyvault1.vault.azure.net/secrets/certificate1/{id}",
  "certificateThumbprint": "1B721E84DDDD1BB69282B4A54F18C6ADB1C174F2"
}
```

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).
