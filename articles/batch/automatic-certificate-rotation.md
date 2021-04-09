---
title: Povolení automatického otočení certifikátu ve fondu Batch
description: Fond Batch můžete vytvořit se spravovanou identitou a certifikátem, který se bude automaticky obnovovat.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: references_regions
ms.openlocfilehash: e8bea49b2980deb8f20258ab7ea5619ece8cd2bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962476"
---
# <a name="enable-automatic-certificate-rotation-in-a-batch-pool"></a>Povolení automatického otočení certifikátu ve fondu Batch

 Můžete vytvořit fond dávek s certifikátem, který se bude automaticky obnovovat. K tomu je třeba vytvořit fond pomocí [spravované identity přiřazené uživatelem](managed-identity-pools.md) , která bude mít přístup k certifikátu v [Azure Key Vault](../key-vault/general/overview.md).

> [!IMPORTANT]
> Podpora fondů Azure Batch s uživatelsky přiřazenými spravovanými identitami je v současnosti ve verzi Public Preview pro tyto oblasti: Západní USA 2, Střed USA – jih, Východní USA, US Gov – Arizona a US Gov – Virginie.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-user-assigned-identity"></a>Vytvoření identity přiřazené uživatelem

Nejdřív [vytvořte uživatelem přiřazenou spravovanou identitu](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) ve stejném tenantovi jako účet Batch. Tato spravovaná identita nemusí být ve stejné skupině prostředků nebo dokonce ani ve stejném předplatném.

Nezapomeňte si poznamenat **ID klienta** spravované identity přiřazené uživatelem. Tuto hodnotu budete potřebovat později.

:::image type="content" source="media/automatic-certificate-rotation/client-id.png" alt-text="Snímek obrazovky s ID klienta spravované identity přiřazené uživatelem v Azure Portal.":::

## <a name="create-your-certificate"></a>Vytvoření certifikátu

V dalším kroku budete muset vytvořit certifikát a přidat ho do Azure Key Vault. Pokud jste ještě nevytvořili Trezor klíčů, budete ho muset udělat nejdřív. Pokyny najdete v tématu [rychlý Start: nastavení a načtení certifikátu z Azure Key Vault pomocí Azure Portal](../key-vault/certificates/quick-create-portal.md).

Při vytváření certifikátu nezapomeňte nastavit **typ akce životního cyklu** na automatické obnovení a zadejte počet dní, po jejichž uplynutí se certifikát má prodloužit.

:::image type="content" source="media/automatic-certificate-rotation/certificate.png" alt-text="Snímek obrazovky s vytvořením certifikátu v Azure Portal.":::

Po vytvoření certifikátu si poznamenejte jeho **tajný identifikátor**. Tuto hodnotu budete potřebovat později.

:::image type="content" source="media/automatic-certificate-rotation/secret-identifier.png" alt-text="Snímek obrazovky zobrazující tajný identifikátor certifikátu":::

## <a name="add-an-access-policy-in-azure-key-vault"></a>Přidat zásadu přístupu v Azure Key Vault

V trezoru klíčů přiřaďte zásadu Key Vault přístupu, která umožňuje vaší uživatelsky přiřazené spravované identitě přístup k tajným klíčům a certifikátům. Podrobné pokyny najdete v tématu [přiřazení zásad Key Vault přístupu pomocí Azure Portal](../key-vault/general/assign-access-policy-portal.md).

## <a name="create-a-batch-pool-with-a-user-assigned-managed-identity"></a>Vytvoření fondu Batch pomocí spravované identity přiřazené uživatelem

Vytvořte fond služby Batch pomocí spravované identity pomocí [knihovny pro správu služby Batch .NET](/dotnet/api/overview/azure/batch#management-library). Další informace najdete v tématu [Konfigurace spravovaných identit ve fondech služby Batch](managed-identity-pools.md).

Následující příklad používá REST API pro správu dávek k vytvoření fondu. Nezapomeňte použít **tajný identifikátor** vašeho certifikátu `observedCertificates` a **ID klienta** spravované identity pro a `msiClientId` nahradit níže uvedená ukázková data.

Identifikátor URI v REST API

```http
PUT https://management.azure.com/subscriptions/<subscriptionid>/resourceGroups/<resourcegroupName>/providers/Microsoft.Batch/batchAccounts/<batchaccountname>/pools/<poolname>?api-version=2021-01-01
```

Text požadavku

```json
{
    "name": "test2",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "KVExtensions",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2s.vault.azure.net/secrets/authcertforumatesting/8f5f3f491afd48cb99286ba2aacd39af"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "b9f6dd56-d2d6-4967-99d7-8062d56fd84c"
                            }  }, "protectedSettings":{}
                    }                ]            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "resizeTimeout": "PT15M"
            }
        },
    },
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/042998e4-36dc-4b7d-8ce3-a7a2c4877d33/resourceGroups/ACR/providers/Microsoft.ManagedIdentity/userAssignedIdentities/testumaforpools": {}
        }
    }
}

```

## <a name="validate-the-certificate"></a>Ověřit certifikát

Pokud chcete ověřit, že se certifikát úspěšně nasadil, přihlaste se k výpočetnímu uzlu. Zobrazený výstup by měl vypadat přibližně takto:

```
root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status# cat 1.status
[{"status":{"code":0,"formattedMessage":{"lang":"en","message":"Successfully started Key Vault extension service. 2021-03-03T23:12:23Z"},"operation":"Service start.","status":"success"},"timestampUTC":"2021-03-03T23:12:23Z","version":"1.0"}]root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status#
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [spravovaných identitách pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).
- Naučte se používat [klíče spravované zákazníky s identitami spravovanými uživatelem](batch-customer-managed-key.md).
