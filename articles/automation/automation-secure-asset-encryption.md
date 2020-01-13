---
title: Šifrování zabezpečených prostředků v automatizaci
description: Azure Automation chrání zabezpečené prostředky pomocí více úrovní šifrování. Ve výchozím nastavení se šifrování provádí pomocí klíčů spravovaných Microsoftem. Zákazníci můžou nakonfigurovat své účty Automation, aby k šifrování používali spravované klíče zákazníka. Tento článek popisuje podrobné informace o obou režimech šifrování a o tom, jak můžete mezi nimi přepínat.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: fa8ea40d827807565e71d1e790c8c52986b85ec8
ms.sourcegitcommit: d48afd9a09f850b230709826d4a5cd46e57d19fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75904951"
---
# <a name="secure-assets-in-azure-automation"></a>Zabezpečení prostředků v Azure Automation

Zabezpečené prostředky v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou chráněné v Azure Automation s využitím více úrovní šifrování. Na základě klíče nejvyšší úrovně používaného pro šifrování jsou k dispozici dva modely šifrování:
-   Používání klíčů spravovaných Microsoftem
-   Použití klíčů spravovaných zákazníkem

### <a name="microsoft-managed-keys"></a>Klíče spravované společností Microsoft

Ve výchozím nastavení používá váš účet Azure Automation klíče spravované společností Microsoft.

Každý zabezpečený prostředek je zašifrovaný a uložený v Azure Automation pomocí jedinečného klíče (datového šifrovacího klíče), který se generuje pro každý účet Automation. Tyto klíče se samy šifrují a ukládají v Azure Automation pomocí jiného jedinečného klíče, který se generuje pro každý účet s názvem šifrovací klíč účtu (AEK). Tyto šifrovací klíče účtu se šifrují a ukládají v Azure Automation pomocí spravovaných klíčů společnosti Microsoft. 

### <a name="customer-managed-keys-with-key-vault-preview"></a>Klíče spravované zákazníkem s Key Vault (Preview)

Šifrování zabezpečených prostředků můžete spravovat v Azure Automation na úrovni účtu Automation pomocí vlastních klíčů. Když na úrovni účtu Automation zadáte klíč spravovaný zákazníkem, použije se tento klíč k ochraně a řízení přístupu k šifrovacímu klíči účtu pro účet Automation, který se zase používá k šifrování a dešifrování všech zabezpečených prostředků. Klíče spravované zákazníkem nabízejí větší flexibilitu při vytváření, střídání, zakázání a odvolávání řízení přístupu. Můžete také auditovat šifrovací klíče používané k ochraně vašich zabezpečených prostředků. 

K ukládání klíčů spravovaných zákazníkem je nutné použít Azure Key Vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů.  Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../key-vault/key-vault-overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>Povolení klíčů spravovaných zákazníkem pro účet Automation

Když zapnete šifrování pomocí klíčů spravovaných zákazníkem pro účet Automation, Azure Automation zabalí šifrovací klíč účtu s klíčem spravovaným zákazníkem v přidruženém trezoru klíčů. Povolení klíčů spravovaných zákazníkem nemá vliv na výkon a účet je zašifrovaný pomocí nového klíče hned bez jakékoliv prodlevy.

Nový účet Automation je vždycky zašifrovaný pomocí klíčů spravovaných Microsoftem. V okamžiku vytvoření účtu není možné povolit klíče spravované zákazníkem. Klíče spravované zákazníkem jsou uložené v Azure Key Vault a trezor klíčů musí být zřízený pomocí zásad přístupu, které udělí klíčová oprávnění ke spravované identitě, která je přidružená k účtu Automation. Spravovaná identita je k dispozici až po vytvoření účtu úložiště.

Když upravíte klíč, který se používá pro Azure Automation zabezpečeného šifrování prostředků povolením nebo zakázáním klíčů spravovaných zákazníkem, aktualizací verze klíče nebo zadáním jiného klíče, pak se změní šifrování kořenového klíče, ale zabezpečené prostředky v Azure Účet Automation není nutné znovu šifrovat.

Následující tři oddíly popisují mechanismy povolování klíčů spravovaných zákazníkem pro účet Automation. 

> [!NOTE] 
> Pokud chcete povolit klíče spravované zákazníkem, budete teď muset Azure Automation rozhraní API REST pomocí rozhraní API verze 2020-01-13-Preview.

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>Předpoklady pro použití klíčů spravovaných zákazníkem v Azure Automation

Než povolíte klíče spravované zákazníkem pro účet Automation, musíte zajistit, aby byly splněné následující předpoklady.

 - Klíč zákazníka-spravovaných je uložený v Azure Key Vault. 
 - V trezoru klíčů musíte povolit jak **obnovitelné odstranění** , tak i **Nemazat** vlastnosti. Tyto funkce jsou nutné k povolení obnovení klíčů v případě náhodného odstranění.
 - Šifrování Azure Automation podporuje jenom klíče RSA. Další informace o klíčích najdete v tématu [informace o Azure Key Vaultch klíčích, tajných klíčích a certifikátech](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).
- Účet Automation a trezor klíčů můžou být v různých předplatných, ale musí být ve stejném Azure Active Directory tenantovi.

### <a name="assign-an-identity-to-the-automation-account"></a>Přiřazení identity k účtu Automation

Pokud chcete používat klíče spravované zákazníkem s účtem Automation, váš účet Automation musí ověřovat proti trezoru klíčů, který ukládá klíče spravované zákazníkem. Azure Automation používá spravované identity přiřazené systémem k ověření účtu s Key Vault. Další informace o spravovaných identitách najdete v tématu [co jsou spravované identity pro prostředky Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Nakonfigurujte spravovanou identitu přiřazenou systémem na účet Automation pomocí následujícího REST API volání.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```
Text požadavku
```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```    

Identita přiřazená systémem pro účet Automation se vrátí v odpovědi.

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

### <a name="configure-the-key-vault-access-policy"></a>Konfigurace zásad přístupu Key Vault

Jakmile se ke účtu Automation přiřadí spravovaná identita, nakonfigurujete přístup k Key Vault ukládání spravovaných klíčů zákazníka. Azure Automation vyžaduje, aby na spravovaných klíčích zákazníka byly **UnwrapKeyy** **Get**, **Recovery**, **wrapKey**a.

Tyto zásady přístupu se dají nastavit pomocí následujícího REST API volání.

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```
Text požadavku

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
> Pole tenantId a objectId musí být k dispozici s hodnotami identity. tenantId a identity. principalId z odpovědi spravované identity pro účet Automation.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Změna konfigurace účtu Automation na použití spravovaného klíče zákazníka

Nakonec můžete přepnout účet Automation z klíčů spravovaných Microsoft na klíče spravované zákazníkem pomocí následujícího REST API volání.

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```
Text požadavku

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

## <a name="manage-customer-managed-keys-lifecycle"></a>Spravovat životní cyklus klíčů spravovaných zákazníkem

### <a name="rotate-customer-managed-keys"></a>Otočit klíče spravované zákazníkem

Klíč spravovaný zákazníkem můžete v Azure Key Vault otočit podle vašich zásad dodržování předpisů. Při otočení klíče musíte aktualizovat účet Automation, aby používal nový identifikátor URI klíče. 

Otáčení klíče neaktivuje opětovné šifrování dat v účtu úložiště. Od uživatele není vyžadována žádná další akce.

## <a name="revoke-access-to-customer-managed-keys"></a>Odvolat přístup k klíčům spravovaným zákazníkem

K odvolání přístupu ke klíčům spravovaným zákazníkem použijte PowerShell nebo Azure CLI. Další informace najdete v tématu [Azure Key Vault PowerShellu](https://docs.microsoft.com/powershell/module/az.keyvault/) nebo rozhraní příkazového [řádku Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). Odvolání přístupu efektivně zablokuje přístup ke všem datům v účtu úložiště, protože šifrovací klíč je nepřístupný Azure Storage.

## <a name="next-steps"></a>Další kroky

- [Co je Azure Key Vault?](../key-vault/key-vault-overview.md) 
- [Prostředky certifikátů ve službě Azure Automation](shared-resources/certificates.md)
- [Prostředky přihlašovacích údajů ve službě Azure Automation](shared-resources/credentials.md)
- [Prostředky proměnných ve službě Azure Automation](shared-resources/variables.md)
