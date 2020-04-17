---
title: Šifrování zabezpečených prostředků ve službě Azure Automation
description: Azure Automation chrání zabezpečené prostředky pomocí několika úrovní šifrování. Ve výchozím nastavení se šifrování provádí pomocí klíčů spravovaných společností Microsoft. Zákazníci mohou nakonfigurovat své účty automatizace tak, aby k šifrování používaly klíče spravované zákazníkem. Tento článek popisuje podrobnosti o obou režimech šifrování a jak můžete přepínat mezi těmito dvěma.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 01/11/2020
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: a82d2b6d9521ba7dd5e7b194c26ff8fe5a100871
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457480"
---
# <a name="encrypt-secure-assets-in-azure-automation"></a>Šifrování zabezpečených prostředků ve službě Azure Automation

Zabezpečené datové zdroje v Azure Automation zahrnují přihlašovací údaje, certifikáty, připojení a šifrované proměnné. Tyto prostředky jsou chráněné v Azure Automation pomocí několika úrovní šifrování. Na základě klíče nejvyšší úrovně použitého pro šifrování existují dva modely pro šifrování:
-    Použití klíčů spravovaných společností Microsoft
-    Použití klíčů spravovaných zákazníkem

## <a name="microsoft-managed-keys"></a>Klíče spravované společností Microsoft

Ve výchozím nastavení používá váš účet Azure Automation klíče spravované společností Microsoft.

Každý zabezpečený prostředek je šifrovaný a uložený v Azure Automation pomocí jedinečného klíče (klíč šifrování dat), který se generuje pro každý účet automatizace. Tyto klíče samy o sobě jsou šifrované a uložené v Azure Automation pomocí dalšího jedinečného klíče, který se generuje pro každý účet nazývaný šifrovací klíč účtu (AEK). Tyto šifrovací klíče účtu šifrované a uložené v Azure Automation pomocí klíčů spravovaných Microsoftem. 

## <a name="customer-managed-keys-with-key-vault-preview"></a>Klíče spravované zákazníkem s trezorem klíčů (náhled)

Šifrování zabezpečených datových zdrojů pro svůj účet Automation můžete spravovat pomocí vlastních klíčů. Když zadáte klíč spravovaný zákazníkem na úrovni účtu Automation, tento klíč se používá k ochraně a řízení přístupu k šifrovacímu klíči účtu pro účet Automation. To se zase používá k šifrování a dešifrování všech zabezpečených datových zdrojů. Klíče spravované zákazníkem nabízejí větší flexibilitu při vytváření, otáčení, zakázání a odvolání ovládacích prvků přístupu. Můžete také auditovat šifrovací klíče používané k ochraně zabezpečených datových zdrojů.

K ukládání klíčů spravovaných zákazníky použijte Azure Key Vault. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít Azure Key Vault API ke generování klíčů.  Další informace o Azure Key Vault najdete v tématu [Co je Azure Key Vault?](../key-vault/general/overview.md)

## <a name="enable-customer-managed-keys-for-an-automation-account"></a>Povolení klíčů spravovaných zákazníkem pro účet automatizace

Když povolíte šifrování pomocí klíčů spravovaných zákazníkem pro účet Automation, Azure Automation zabalí šifrovací klíč účtu s klíčem spravovaným zákazníkem v přidruženém trezoru klíčů. Povolení klíčů spravovaných zákazníkem nemá vliv na výkon a účet je okamžitě zašifrován pomocí nového klíče, bez zpoždění.

Nový účet Automation je vždy šifrován pomocí klíčů spravovaných společností Microsoft. V době vytvoření účtu není možné povolit klíče spravované zákazníkem. Klíče spravované zákazníkem jsou uloženy v trezoru klíčů Azure a trezor klíčů musí být zřízen zásadami přístupu, které udělují oprávnění klíče ke spravované identitě přidružené k účtu Automation. Spravovaná identita je k dispozici pouze po vytvoření účtu úložiště.

Když upravíte klíč používaný pro zabezpečené šifrování prostředků Azure Automation, povolením nebo zakázáním klíčů spravovaných zákazníkem, aktualizací verze klíče nebo určením jiného klíče se změní šifrování šifrovacího klíče účtu, ale zabezpečené prostředky ve vašem účtu Azure Automation nemusí být znovu šifrovány.

Následující tři části popisují mechanismy povolení klíčů spravovaných zákazníkem pro účet automatizace. 

> [!NOTE] 
> Chcete-li povolit klíče spravované zákazníkem, je třeba provést volání rozhraní REST API Azure Automation pomocí rozhraní API verze 2020-01-13-preview

### <a name="pre-requisites-for-using-customer-managed-keys-in-azure-automation"></a>Předpoklady pro použití klíčů spravovaných zákazníky v Azure Automation

Před povolením klíčů spravovaných zákazníkem pro účet Automation je nutné zajistit splnění následujících předpokladů:

 - Klíč vytvořený zákazníkem je uložen v trezoru klíčů Azure. 
 - Povolte vlastnosti **Obnovitelné odstranění** i **Nečistit** v trezoru klíčů. Tyto funkce jsou nutné, aby bylo možné obnovit klíče v případě náhodného odstranění.
 - Šifrování azure automation podporuje jenom klíče RSA. Další informace o klíčích naleznete v [tématu O klíčích, tajných klíčích a certifikátech trezoru klíčů Azure](../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).
- Účet Automatizace a trezor klíčů může být v různých předplatných, ale musí být ve stejném tenantovi služby Azure Active Directory.

### <a name="assign-an-identity-to-the-automation-account"></a>Přiřazení identity k účtu automatizace

Chcete-li používat klíče spravované zákazníkem s účtem Automation, musí se váš účet Automation ověřit v trezoru klíčů, který ukládá klíče spravované zákazníkem. Azure Automation používá k ověření účtu pomocí Azure Key Vault u systému přiřazené spravované identity. Další informace o spravovaných identitách najdete v tématu [Co je spravované identity pro prostředky Azure?](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Nakonfigurujte systém přiřazenou spravovanou identitu k účtu automatizace pomocí následujícího volání rozhraní REST API:

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

Systém přiřazená identita pro účet Automatizace je vrácena v odpovědi podobné následující:

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

### <a name="configure-the-key-vault-access-policy"></a>Konfigurace zásad přístupu trezoru klíčů

Jakmile je spravovaná identita přiřazena k účtu Automation, nakonfigurujete přístup k trezoru klíčů, který ukládá klíče spravované zákazníkem. Azure Automation vyžaduje **get**, **recover**, **wrapKey**, **UnwrapKey** na klíčích spravovaných zákazníkem.

Tyto zásady přístupu lze nastavit pomocí následujícího volání rozhraní REST API:

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
> Pole **tenantId** a **objectId** musí být poskytnuta s hodnotami **identity.tenantId** a **identity.principalId** z odpovědi spravované identity pro účet Automation.

### <a name="change-the-configuration-of-automation-account-to-use-customer-managed-key"></a>Změna konfigurace účtu Automation tak, aby používal klíč spravovaný zákazníkem

Nakonec můžete přepnout svůj účet Automation z klíčů spravovaných microsft na klíče spravované zákazníky pomocí následujícího volání rozhraní REST API:

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

## <a name="manage-customer-managed-keys-lifecycle"></a>Správa životního cyklu klíčů spravovaných zákazníkem

### <a name="rotate-customer-managed-keys"></a>Otočení klíčů spravovaných zákazníkem

Klíč spravovaný zákazníkem můžete v trezoru klíčů Azure v souladu s vašimi zásadami dodržování předpisů otočit. Při otočení klíče je nutné aktualizovat účet automatizace, aby se použil nový identifikátor URI klíče.

Otočení klíče neaktivuje opětovné šifrování zabezpečených prostředků v účtu automatizace. Není nutná žádná další akce.

### <a name="revoke-access-to-customer-managed-keys"></a>Odvolání přístupu ke klíčům spravovaným zákazníkem

Pokud chcete odvolat přístup ke klíčům spravovaným zákazníkem, použijte PowerShell nebo Azure CLI. Další informace najdete [v tématu Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) nebo [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Zrušení přístupu efektivně blokuje přístup ke všem zabezpečeným prostředkům v účtu Automation, protože šifrovací klíč není pro Azure Automation přístupný.

## <a name="next-steps"></a>Další kroky

- [Co je Azure Key Vault?](../key-vault/general/overview.md)

- [Prostředky certifikátů ve službě Azure Automation](shared-resources/certificates.md)

- [Prostředky přihlašovacích údajů ve službě Azure Automation](shared-resources/credentials.md)

- [Prostředky proměnných ve službě Azure Automation](shared-resources/variables.md)
