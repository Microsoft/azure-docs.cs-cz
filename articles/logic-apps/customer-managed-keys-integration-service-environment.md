---
title: Nastavení klíčů spravovaných zákazníkem k šifrování dat v klidovém umístění v ISEs
description: Vytvářejte a spravujte vlastní šifrovací klíče k zabezpečení dat v klidovém prostředí pro prostředí ISEs (Integration Service Environment) v Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: ad5b4245cc445ecf8fae22c39db3365d71730a56
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89400139"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Nastavení klíčů spravovaných zákazníkem k šifrování dat v klidovém prostředí pro prostředí ISEs (Integration Service Environment) v Azure Logic Apps

Azure Logic Apps spoléhá na Azure Storage ukládání a automatické [šifrování dat v klidovém umístění](../storage/common/storage-service-encryption.md). Toto šifrování chrání vaše data a pomáhá splnit závazky zabezpečení vaší organizace a dodržování předpisů. Ve výchozím nastavení používá Azure Storage k šifrování vašich dat klíče spravované Microsoftem. Další informace o tom, jak funguje šifrování Azure Storage, najdete v tématu [Azure Storage šifrování pro](../storage/common/storage-service-encryption.md) neaktivní a neaktivní data v případě [šifrování dat v Azure](../security/fundamentals/encryption-atrest.md).

Když vytvoříte [prostředí ISE (Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) pro hostování aplikací logiky a potřebujete větší kontrolu nad šifrovacími klíči používanými Azure Storage, můžete nastavit, používat a spravovat vlastní klíč pomocí [Azure Key Vault](../key-vault/general/overview.md). Tato funkce se také označuje jako "Bring Your Own Key" (BYOK) a váš klíč se nazývá klíč spravovaný zákazníkem.

V tomto tématu se dozvíte, jak nastavit a zadat vlastní šifrovací klíč, který se použije při vytváření ISE pomocí REST API Logic Apps. Obecné kroky k vytvoření ISE prostřednictvím Logic Apps REST API najdete v tématu [vytvoření prostředí ISE (Integration Service Environment) pomocí REST API Logic Apps](../logic-apps/create-integration-service-environment-rest-api.md).

## <a name="considerations"></a>Důležité informace

* V tuto chvíli je podpora klíčových zákaznických služeb pro ISE dostupná jenom v těchto oblastech Azure: Západní USA 2, Východní USA a Střed USA – jih

* Klíč spravovaný zákazníkem můžete zadat *jenom při vytváření ISE*, ne následně. Po vytvoření ISE nemůžete tento klíč deaktivovat. V současné době není k dispozici žádná podpora pro otočení klíče spravovaného zákazníkem pro ISE.

* Pro podporu klíčů spravovaných zákazníkem vyžaduje vaše ISE vyžadovat, aby byla povolená [spravovaná identita přiřazená systémem](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) . Tato identita umožňuje ISE ověřovat přístup k prostředkům v jiných klientech Azure Active Directory (Azure AD), takže se nemusíte přihlašovat pomocí svých přihlašovacích údajů.

* V současné době můžete vytvořit ISE, který podporuje klíče spravované zákazníkem a má povolenou identitu přiřazenou systémem. je třeba volat REST API Logic Apps pomocí požadavku PUT protokolu HTTPS.

* Do *30 minut* poté, co odešlete požadavek https PUT, který vytvoří vaše ISE, musíte [dát trezoru klíčů přístup k identitě PŘIŘAZENÉ systému vaší ISE](#identity-access-to-key-vault). V opačném případě ISE vytvoření selže a vyvolá chybu oprávnění.

## <a name="prerequisites"></a>Požadavky

* Stejné [požadavky](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) a [požadavky pro povolení přístupu pro ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) jako při vytváření ISE v Azure Portal

* Trezor klíčů Azure s povolenými vlastnostmi **obnovitelného odstranění** a **nevyprázdnění**

  Další informace o tom, jak tyto vlastnosti povolit, najdete v tématu [Azure Key Vault obnovitelné odstranění – přehled](../key-vault/general/soft-delete-overview.md) a [konfigurace klíčů spravovaných zákazníkem pomocí Azure Key Vault](../storage/common/storage-encryption-keys-portal.md). Pokud s Azure Key Vault začínáte, přečtěte si, [jak vytvořit Trezor klíčů](../key-vault/secrets/quick-create-portal.md#create-a-vault) pomocí Azure Portal nebo pomocí příkazu Azure PowerShell [New-AzKeyVault](/powershell/module/az.keyvault/new-azkeyvault).

* V trezoru klíčů se vytvoří klíč, který je vytvořený pomocí těchto hodnot vlastností:

  | Vlastnost | Hodnota |
  |----------|-------|
  | **Typ klíče** | RSA |
  | **Velikost klíče RSA** | 2 048 |
  | **Povoleno** | Yes |
  |||

  ![Vytvoření šifrovacího klíče spravovaného zákazníkem](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Další informace najdete v tématu [konfigurace klíčů spravovaných zákazníkem pomocí Azure Key Vault](../storage/common/storage-encryption-keys-portal.md) nebo příkazu Azure PowerShell, [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey).

* Nástroj, který můžete použít k vytvoření ISE, voláním Logic Apps REST API s požadavkem PUT protokolu HTTPS. Můžete například použít [post](https://www.getpostman.com/downloads/)nebo můžete vytvořit aplikaci logiky, která provede tuto úlohu.

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>Vytvoření ISE s využitím trezoru klíčů a podpory spravovaných identit

Pokud chcete vytvořit ISE voláním REST API Logic Apps, udělejte tuto žádost o vložení HTTPS:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Verze Logic Apps REST API 2019-05-01 vyžaduje, abyste si pro konektory ISE napravili vlastní požadavek HTTP PUT.

Nasazení obvykle trvá do dvou hodin, než se dokončí. V některých případech může nasazení trvat až čtyři hodiny. Pokud chcete zjistit stav nasazení, v [Azure Portal](https://portal.azure.com)na panelu nástrojů Azure vyberte ikonu oznámení, která otevře podokno oznámení.

> [!NOTE]
> Pokud se nasazení nepovede nebo odstraníte ISE, může Azure trvat až hodinu, než se vaše podsítě uvolní. Tato prodleva znamená, že možná budete muset počkat, než tyto podsítě znovu použijete v jiné ISE.
>
> Pokud virtuální síť odstraníte, Azure obvykle trvá až dvě hodiny, než se uvolní vaše podsítě, ale tato operace může trvat delší dobu. 
> Při odstraňování virtuálních sítí se ujistěte, že nejsou připojené žádné prostředky. 
> Viz [odstranění virtuální sítě](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>Hlavička požadavku

V hlavičce požadavku zahrňte tyto vlastnosti:

* `Content-type`: Nastavte tuto vlastnost na hodnotu `application/json` .

* `Authorization`: Nastavte tuto hodnotu vlastnosti na nosný token pro zákazníka, který má přístup k předplatnému Azure nebo skupině prostředků, kterou chcete použít.

### <a name="request-body"></a>Text požadavku

V textu žádosti povolte podporu těchto dalších položek poskytnutím jejich informací v definici ISE:

* Spravovaná identita přiřazená systémem, kterou vaše ISE používá pro přístup k vašemu trezoru klíčů
* Váš Trezor klíčů a klíč spravovaný zákazníkem, který chcete použít

#### <a name="request-body-syntax"></a>Syntaxe textu žádosti

Tady je syntaxe textu žádosti, která popisuje vlastnosti, které se mají použít při vytváření ISE:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.KeyVault/vaults/{key-vault-name}",
            },
            "keyName": "{customer-managed-key-name}",
            "keyVersion": "{key-version-number}"
         }
      }
   }
}
```

#### <a name="request-body-example"></a>Příklad textu žádosti

Tento ukázkový text požadavku zobrazuje ukázkové hodnoty:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "identity": {
      "type": "SystemAssigned"
   },
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      },
      "encryptionConfiguration": {
         "encryptionKeyReference": {
            "keyVault": {
               "id": "subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.KeyVault/vaults/FabrikamKeyVault",
            },
            "keyName": "Fabrikam-Encryption-Key",
            "keyVersion": "********************"
         }
      }
   }
}
```

<a name="identity-access-to-key-vault"></a>

## <a name="grant-access-to-your-key-vault"></a>Udělení přístupu k trezoru klíčů

Do *30 minut* od odeslání žádosti HTTP PUT k vytvoření vašeho ISEu musíte do svého trezoru klíčů přidat zásadu přístupu pro identitu přiřazenou systémem vaší ISE. V opačném případě vytvoření pro ISE selže a zobrazí se chyba oprávnění. 

Pro tuto úlohu můžete použít buď příkaz Azure PowerShell [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) , nebo můžete postupovat podle těchto kroků v Azure Portal:

1. V [Azure Portal](https://portal.azure.com)otevřete svůj Trezor klíčů Azure.

1. V nabídce trezoru klíčů vyberte **zásady přístupu**  >  **Přidat zásady přístupu**, například:

   ![Přidání zásad přístupu pro spravovanou identitu přiřazenou systémem](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. Po otevření podokna **Přidat zásady přístupu** postupujte takto:

   1. Vyberte tyto možnosti:

      | Nastavení | Hodnoty |
      |---------|--------|
      | **Konfigurovat ze seznamu šablon (volitelné)** | Správa klíčů |
      | **Klíčová oprávnění** | - **Operace správy klíčů**: získat, seznam <p><p>- **Kryptografické operace**: rozbalení klíče, zalomit klíč |
      |||

      ![Vyberte možnost Správa klíčů > klíčová oprávnění.](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. V případě **Vyberte objekt zabezpečení**vyberte možnost **není vybráno**. Po otevření podokna **zabezpečení** vyhledejte v poli hledání a vyberte své ISE. Až budete hotovi, zvolte **Vybrat**  >  **Přidat**.

      ![Vyberte ISE, které chcete použít jako objekt zabezpečení.](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. Až skončíte s podoknem **zásady přístupu** , vyberte **Uložit**.

Další informace najdete v tématu [ověření Key Vault](/azure/key-vault/general/authentication) a [přiřazení zásad Key Vault přístupu](/azure/key-vault/general/assign-access-policy-portal).

## <a name="next-steps"></a>Další kroky

* Další informace o [Azure Key Vault](../key-vault/general/overview.md)
