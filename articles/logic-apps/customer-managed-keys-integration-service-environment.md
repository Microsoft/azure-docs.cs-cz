---
title: Nastavení klíčů spravovaných zákazníky k šifrování dat v klidovém stavu v ises
description: Vytvoření a správa vlastních šifrovacích klíčů pro zabezpečení dat v klidovém stavu pro prostředí integračních služeb (ISEs) v Aplikacích Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, rarayudu, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 7314559849f0b2019820ec3cb4fb10c684d330d6
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458433"
---
# <a name="set-up-customer-managed-keys-to-encrypt-data-at-rest-for-integration-service-environments-ises-in-azure-logic-apps"></a>Nastavení klíčů spravovaných zákazníky pro šifrování dat v klidovém stavu pro prostředí integračních služeb (ISEs) v Aplikacích Azure Logic Apps

Azure Logic Apps spoléhá na Azure Storage pro ukládání a automatické [šifrování dat v klidovém stavu](../storage/common/storage-service-encryption.md). Toto šifrování chrání vaše data a pomáhá vám plnit závazky organizace v oblasti zabezpečení a dodržování předpisů. Ve výchozím nastavení azure storage používá klíče spravované Microsoftem k šifrování dat. Další informace o tom, jak funguje šifrování Azure Storage, najdete [v tématu šifrování Azure Storage pro data v klidovém stavu](../storage/common/storage-service-encryption.md) a [Šifrování dat Azure v klidovém stavu](../security/fundamentals/encryption-atrest.md).

Když vytvoříte [prostředí integrační služby (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) pro hostování aplikací logiky a chcete mít větší kontrolu nad šifrovacími klíči používanými službou Azure Storage, můžete nastavit, používat a spravovat svůj vlastní klíč pomocí [azure key vaultu](../key-vault/general/overview.md). Tato funkce se také označuje jako "Přineste si vlastní klíč" (BYOK) a váš klíč se nazývá "klíč spravovaný zákazníkem".

Toto téma ukazuje, jak nastavit a zadat vlastní šifrovací klíč, který chcete použít při vytváření služby ISE pomocí rozhraní API REST logic apps. Obecné kroky k vytvoření rozhraní ISE prostřednictvím rozhraní REST API logic apps najdete v [tématu Vytvoření prostředí služby integrace (ISE) pomocí rozhraní API REST Logic Apps](../logic-apps/create-integration-service-environment-rest-api.md).

## <a name="considerations"></a>Požadavky

* V tuto chvíli je podpora klíčů spravovaná zákazníkem pro ise dostupná jenom v těchto oblastech Azure: Západní USA 2, Usa – východ a Usa – jih– střed USA.

* Klíč spravovaný zákazníkem můžete zadat *pouze v případě, že vytvoříte službu ISE*, nikoli později. Tento klíč nelze po vytvoření služby ISE zakázat. V současné době neexistuje žádná podpora pro otáčení klíče spravovaného zákazníkem pro ISE.

* Chcete-li podporovat klíče spravované zákazníkem, vyžaduje vaše ise povolení, aby byla [povolena spravovaná identita přiřazená systémem.](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) Tato identita umožňuje ISE ověřit přístup k prostředkům v jiných tenantech Služby Azure Active Directory (Azure AD), takže se nemusíte přihlašovat pomocí přihlašovacích údajů.

* V současné době chcete-li vytvořit ise, která podporuje klíče spravované zákazníkem a má povolenou identitu přiřazenou systému, musíte volat rozhraní API REST Logic Apps pomocí požadavku HTTPS PUT.

* Do *30 minut* po odeslání požadavku HTTPS PUT, který vytvoří vaši službu ISE, musíte [udělit přístup k systémově přiřazené identitě vaší služby ISE](#identity-access-to-key-vault). V opačném případě se vytvoření ise nezdaří a vyvolá chybu oprávnění.

## <a name="prerequisites"></a>Požadavky

* Stejné požadavky a [požadavky pro povolení přístupu pro ise](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) jako při vytváření [služby](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) ISE na webu Azure Portal

* Trezor klíčů Azure, který má povolené vlastnosti **Obnovitelné odstranění** a **Nevyčistit**

  Další informace o povolení těchto vlastností naleznete v [tématu Azure Key Vault software-delete přehled](../key-vault/general/overview-soft-delete.md) a [konfigurace klíčů spravovaných zákazníkem s Azure Key Vault](../storage/common/storage-encryption-keys-portal.md). Pokud s Azure Key Vault teče tezaury, přečtěte si, [jak vytvořit trezor klíčů](../key-vault/secrets/quick-create-portal.md#create-a-vault) pomocí portálu Azure nebo pomocí příkazu Azure PowerShell, [New-AzKeyVault](https://docs.microsoft.com/powershell/module/az.keyvault/new-azkeyvault).

* V trezoru klíčů klíč, který je vytvořen s těmito hodnotami vlastností:

  | Vlastnost | Hodnota |
  |----------|-------|
  | **Typ klíče** | RSA |
  | **Velikost klíče RSA** | 2 048 |
  | **Enabled** (Povoleno) | Ano |
  |||

  ![Vytvoření šifrovacího klíče spravovaného zákazníkem](./media/customer-managed-keys-integration-service-environment/create-customer-managed-key-for-encryption.png)

  Další informace najdete [v tématu Konfigurace klíčů spravovaných zákazníky pomocí azure key vaultu](../storage/common/storage-encryption-keys-portal.md) nebo příkazu Azure PowerShell [Add-AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/Add-AzKeyVaultKey).

* Nástroj, který můžete použít k vytvoření služby ISE voláním rozhraní API služby REST logic apps s požadavkem HTTPS PUT. Můžete například použít [Postman](https://www.getpostman.com/downloads/)nebo můžete vytvořit aplikaci logiky, která provádí tento úkol.

<a name="enable-support-key-system-identity"></a>

## <a name="create-ise-with-key-vault-and-managed-identity-support"></a>Vytvoření služby ISE s trezorem klíčů a podporou spravované identity

Chcete-li vytvořit službu ISE voláním rozhraní REST API logic apps, proveďte tento požadavek HTTPS PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Logic Apps REST API 2019-05-01 verze vyžaduje, abyste vytvořit vlastní HTTP PUT požadavek pro konektory ISE.

Nasazení obvykle trvá do dvou hodin. V některých případě může nasazení trvat až čtyři hodiny. Chcete-li zkontrolovat stav nasazení, vyberte na webu Azure na [panelu](https://portal.azure.com)nástrojů Azure ikonu oznámení, která otevře podokno oznámení.

> [!NOTE]
> Pokud nasazení selže nebo odstraníte ise, Azure může trvat až hodinu před uvolněním podsítí. Toto zpoždění znamená, že budete muset počkat před opětovnou použitím těchto podsítí v jiné ise.
>
> Pokud odstraníte virtuální síť, Azure obvykle trvá až dvě hodiny před uvolněním podsítí, ale tato operace může trvat déle. 
> Při mazání virtuálních sítí se ujistěte, že nejsou stále připojeny žádné prostředky. 
> Viz [Odstranění virtuální sítě](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

### <a name="request-header"></a>Hlavička požadavku

Do hlavičky požadavku uveďte tyto vlastnosti:

* `Content-type`: Nastavte tuto `application/json`hodnotu vlastnosti na .

* `Authorization`: Nastavte tuto hodnotu vlastnosti na token nosiče pro zákazníka, který má přístup k předplatnému Azure nebo skupině prostředků, které chcete použít.

### <a name="request-body"></a>Text požadavku

V textu žádosti povolte podporu pro tyto další položky tím, že poskytnejejich informace v definici ISE:

* Systémem přiřazená spravovaná identita, kterou vaše ise používá pro přístup k trezoru klíčů
* Trezor klíčů a klíč spravovaný zákazníkem, který chcete použít

#### <a name="request-body-syntax"></a>Syntaxe textu požadavku

Zde je syntaxe těla požadavku, která popisuje vlastnosti, které se mají použít při vytváření služby ISE:

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

#### <a name="request-body-example"></a>Příklad tělo požadavku

Toto tělo požadavku příklad zobrazuje ukázkové hodnoty:

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

## <a name="grant-access-to-your-key-vault"></a>Udělení přístupu do trezoru klíčů

Do *30 minut* po odeslání požadavku HTTP PUT k vytvoření služby ISE je nutné přidat zásady přístupu do trezoru klíčů pro systémově přiřazenou identitu vaší služby ISE. V opačném případě se nezdaří vytvoření pro ise a zobrazí se chyba oprávnění. 

Pro tuto úlohu můžete použít buď příkaz Azure PowerShell [Set-AzKeyVaultAccessPolicy,](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) nebo můžete postupovat takto na webu Azure Portal:

1. Na [webu Azure Portal](https://portal.azure.com)otevřete trezor klíčů Azure.

1. V nabídce trezoru klíčů vyberte **možnost Access Policies** > **Add Access Policy**, například:

   ![Přidání zásad přístupu pro spravovanou identitu přiřazenou systémem](./media/customer-managed-keys-integration-service-environment/add-ise-access-policy-key-vault.png)

1. Po otevření podokna **Přidat zásady přístupu** postupujte takto:

   1. Vyberte tyto možnosti:

      | Nastavení | Hodnoty |
      |---------|--------|
      | **Konfigurace ze seznamu šablon (nepovinné)** | Správa klíčů |
      | **Klíčová oprávnění** | - **Klíčové operace správy:** Získat, Seznam <p><p>- **Kryptografické operace:** Rozbalovací klíč, zalamovací klíč |
      |||

      ![Vyberte "Správa klíčů" > "Klíčová oprávnění"](./media/customer-managed-keys-integration-service-environment/select-key-permissions.png)

   1. V **popřípadě Vybrat objekt zabezpečení**vyberte Možnost Žádný **vybraný**. Po otevření podokna **Hlavní** ve vyhledávacím poli vyhledejte a vyberte ise. Až budete hotovi, zvolte **Vybrat** > **přidat**.

      ![Vyberte ise, které chcete použít jako hlavní](./media/customer-managed-keys-integration-service-environment/select-service-principal-ise.png)

   1. Až skončíte s podoknem **zásad aplikace Access,** vyberte **Uložit**.

Další informace naleznete [v tématu Provide Key Vault authentication with a managed identity](../key-vault/general/managed-identity.md#grant-your-app-access-to-key-vault).

## <a name="next-steps"></a>Další kroky

* Další informace o [Azure Key Vault](../key-vault/general/overview.md)