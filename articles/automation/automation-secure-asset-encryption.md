---
title: Šifrování zabezpečených prostředků ve službě Azure Automation
description: Tento článek poskytuje koncepty pro konfiguraci účtu Automation pro použití šifrování.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 7d59ca60c7f90c227885927086511bd1f8ac7ca1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185836"
---
# <a name="encryption-of-secure-assets-in-azure-automation"></a>Šifrování zabezpečených prostředků ve službě Azure Automation

Zabezpečené prostředky v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou chráněné v Azure Automation s využitím více úrovní šifrování. Na základě klíče nejvyšší úrovně používaného pro šifrování jsou k dispozici dva modely šifrování:

- Používání klíčů spravovaných Microsoftem
- Používání klíčů, které spravujete

## <a name="microsoft-managed-keys"></a>Klíče spravované společností Microsoft

Ve výchozím nastavení používá váš účet Azure Automation klíče spravované společností Microsoft.

Každý zabezpečený prostředek je zašifrovaný a uložený v Azure Automation pomocí jedinečného klíče (datového šifrovacího klíče), který se generuje pro každý účet Automation. Tyto klíče se samy šifrují a ukládají v Azure Automation pomocí jiného jedinečného klíče, který se generuje pro každý účet s názvem šifrovací klíč účtu (AEK). Tyto šifrovací klíče účtu se šifrují a ukládají v Azure Automation pomocí klíčů spravovaných Microsoftem. 

## <a name="keys-that-you-manage-with-key-vault-preview"></a>Klíče, které spravujete pomocí Key Vault (Preview)

Můžete spravovat šifrování zabezpečených prostředků pro svůj účet Automation pomocí vlastních klíčů. Když na úrovni účtu Automation zadáte klíč spravovaný zákazníkem, použije se tento klíč k ochraně a řízení přístupu k šifrovacímu klíči účtu pro účet Automation. To zase slouží k šifrování a dešifrování všech zabezpečených prostředků. Klíče spravované zákazníkem nabízejí větší flexibilitu při vytváření, střídání, zakázání a odvolávání řízení přístupu. Můžete také auditovat šifrovací klíče používané k ochraně vašich zabezpečených prostředků.

K ukládání klíčů spravovaných zákazníkem použijte Azure Key Vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů.  Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../key-vault/general/overview.md)

## <a name="use-of-customer-managed-keys-for-an-automation-account"></a>Použití klíčů spravovaných zákazníkem pro účet Automation

Když použijete šifrování s klíči spravovanými zákazníkem pro účet Automation, Azure Automation zabalí šifrovací klíč účtu s klíčem spravovaným zákazníkem v přidruženém trezoru klíčů. Povolení klíčů spravovaných zákazníkem nemá vliv na výkon a účet je zašifrovaný pomocí nového klíče hned bez zpoždění.

Nový účet Automation je vždycky zašifrovaný pomocí klíčů spravovaných Microsoftem. V okamžiku vytvoření účtu není možné povolit klíče spravované zákazníkem. Klíče spravované zákazníkem jsou uložené v Azure Key Vault a trezor klíčů musí být zřízený pomocí zásad přístupu, které udělí klíčová oprávnění ke spravované identitě, která je přidružená k účtu Automation. Spravovaná identita je k dispozici až po vytvoření účtu úložiště.

Když upravíte klíč, který se používá pro Azure Automation zabezpečené šifrování assetů, povolením nebo zakázáním klíčů spravovaných zákazníkem, aktualizací verze klíče nebo zadáním jiného klíče, šifrování šifrovacího klíče účtu se změní, ale zabezpečené prostředky v účtu Azure Automation není nutné znovu šifrovat.

> [!NOTE] 
> Pokud chcete povolit klíče spravované zákazníkem, musíte provést Azure Automation REST API volání pomocí rozhraní API verze 2020-01-13-Preview.

### <a name="prerequisites-for-using-customer-managed-keys-in-azure-automation"></a>Předpoklady použití klíčů spravovaných zákazníkem v Azure Automation

Než povolíte klíče spravované zákazníkem pro účet Automation, musíte zajistit splnění následujících požadavků:

 - Klíč zákazníka-spravovaných je uložený v Azure Key Vault. 
 - V trezoru klíčů povolte jak **obnovitelné odstranění** , tak i **nemazatelné** vlastnosti. Tyto funkce jsou nutné k povolení obnovení klíčů v případě náhodného odstranění.
 - Šifrování Azure Automation podporuje jenom klíče RSA. Další informace o klíčích najdete v tématu [informace o Azure Key Vaultch klíčích, tajných klíčích a certifikátech](../key-vault/general/about-keys-secrets-certificates.md).
- Účet Automation a trezor klíčů můžou být v různých předplatných, ale musí být ve stejném Azure Active Directory tenantovi.

### <a name="assignment-of-an-identity-to-the-automation-account"></a>Přiřazení identity k účtu Automation

Pokud chcete používat klíče spravované zákazníkem s účtem Automation, váš účet Automation musí ověřovat proti trezoru klíčů, který ukládá klíče spravované zákazníkem. Azure Automation používá spravované identity přiřazené systémem k ověření účtu s Azure Key Vault. Další informace o spravovaných identitách najdete v tématu [co jsou spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md)

Nakonfigurujte spravovanou identitu přiřazenou systémem na účet Automation pomocí následujícího REST API volání:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Text požadavku:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

Identita přiřazená systémem pro účet Automation se vrátí v odpovědi podobné následujícímu:

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

### <a name="configuration-of-the-key-vault-access-policy"></a>Konfigurace zásad přístupu Key Vault

Jakmile se ke účtu Automation přiřadí spravovaná identita, nakonfigurujete přístup k trezoru klíčů, který ukládá klíče spravované zákazníkem. Azure Automation pro klíče spravované zákazníkem vyžaduje **Get**, **Recovery**, **wrapKey**a **UnwrapKey** .

Tyto zásady přístupu se dají nastavit pomocí následujícího REST API volání:

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

Text požadavku:

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> Pole **tenantId** a **objectID** musí být k dispozici s hodnotami **identity. tenantId** a **identity. PrincipalId** z odpovědi spravované identity pro účet Automation.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Změna konfigurace účtu Automation na použití klíče spravovaného zákazníkem

Nakonec můžete přepnout účet Automation z klíčů spravovaných Microsoftem na klíče spravované zákazníky pomocí následujícího REST API volání:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Text požadavku:

```json
 {
    "properties": {
      "encryption": {
        "keySource": "Microsoft.Keyvault",
        "keyvaultProperties": {
          "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
        }
      }
    }
  }
```

Ukázková odpověď

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="rotation-of-a-customer-managed-key"></a>Rotace klíče spravovaného zákazníkem

Klíč spravovaný zákazníkem můžete v Azure Key Vault otočit podle vašich zásad dodržování předpisů. Při otočení klíče musíte aktualizovat účet Automation, aby používal nový identifikátor URI klíče.

Otáčení klíče neaktivuje opětovné šifrování zabezpečených prostředků v účtu Automation. Není vyžadována žádná další akce.

## <a name="revocation-of-access-to-a-customer-managed-key"></a>Odvolání přístupu k klíči spravovanému zákazníkem

K odvolání přístupu k klíčům spravovaným zákazníkem použijte PowerShell nebo Azure CLI. Další informace najdete v tématu [Azure Key Vault PowerShellu](/powershell/module/az.keyvault/) nebo rozhraní příkazového [řádku Azure Key Vault](/cli/azure/keyvault). Odvolání přístupu efektivně blokuje přístup ke všem zabezpečeným prostředkům v účtu Automation, protože šifrovací klíč je nepřístupný Azure Automation.

## <a name="next-steps"></a>Další kroky

- Informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../key-vault/general/overview.md).
- Informace o práci s certifikáty najdete v tématu [Správa certifikátů v Azure Automation](shared-resources/certificates.md).
- Informace o zpracování přihlašovacích údajů najdete v tématu [Správa přihlašovacích údajů v Azure Automation](shared-resources/credentials.md).
- Chcete-li použít proměnné automatizace, [spravujte proměnné v Azure Automation](shared-resources/variables.md).
- Nápovědu k práci s připojeními najdete v tématu [Správa připojení v Azure Automation](automation-connections.md).
