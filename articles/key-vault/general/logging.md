---
title: Azure Key Vault protokolování | Microsoft Docs
description: Naučte se sledovat přístup k vašim trezorům klíčů povolením protokolování pro Azure Key Vault, které šetří informace v účtu Azure Storage, který poskytnete.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 5423fc27ecc58bcd79b36a845e4b7569f342f712
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286706"
---
# <a name="azure-key-vault-logging"></a>Protokolování v Azure Key Vaultu

Po vytvoření jednoho nebo více trezorů klíčů budete pravděpodobně chtít sledovat, jak a kdy jsou k vašim trezorům klíčů přistupované a kým. Můžete to udělat tak, že povolíte protokolování pro Azure Key Vault, které ukládá informace v účtu úložiště Azure, který poskytnete. Podrobné pokyny k tomuto nastavení najdete v tématu [Jak povolit protokolování Key Vault](howto-logging.md).

Po operaci trezoru klíčů máte přístup k informacím o protokolování 10 minut (nejvíce). Ve většině případů to bude rychlejší.  Správa protokolů v účtu úložiště je pouze na vás:

* Zabezpečte protokoly pomocí standardních metod řízení přístupu Azure a určete, kdo k nim má přístup.
* Odstraňte protokoly, které už nechcete uchovávat v účtu úložiště.

Přehled informací o Key Vault najdete v tématu [co je Azure Key Vault?](overview.md). Informace o tom, kde je Key Vault k dispozici, najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/key-vault/). Informace o použití [Azure monitor pro Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="interpret-your-key-vault-logs"></a>Interpretujte svoje protokoly Key Vault

Když povolíte protokolování, pro zadaný účet úložiště se automaticky vytvoří nový kontejner s názvem **Insights-logs-auditevent** . Stejný účet úložiště můžete použít pro shromažďování protokolů pro více trezorů klíčů.

Jednotlivé objekty blob jsou uloženy jako text ve formátu JSON blob. Pojďme se podívat na příklad položky protokolu. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

V následující tabulce jsou uvedené názvy a popisy polí:

| Název pole | Popis |
| --- | --- |
| **interval** |Datum a čas ve standardu UTC. |
| **Prostředku** |ID prostředku Azure Resource Manager. U protokolů Key Vault se jedná vždy o Key Vault Resource ID. |
| **operationName** |Název operace, jak popisuje následující tabulka. |
| **operationVersion** |Verze REST API požadovaná klientem |
| **kategorií** |Typ výsledku. V případě protokolů Key Vault `AuditEvent` je jediná dostupná hodnota. |
| **Hodnotu** |Výsledek žádosti o REST API |
| **resultSignature** |Stav HTTP. |
| **resultDescription** |Další popis výsledku, je-li k dispozici. |
| **Trvání v MS** |Doba trvání obsloužení požadavku REST API v milisekundách. Nezahrnuje latenci sítě, takže čas naměřený na straně klienta se může lišit. |
| **callerIpAddress** |IP adresa klienta, který odeslal požadavek. |
| **ID** |Volitelný GUID, který může klient předat pro korelaci protokolů na straně klienta s protokoly na straně služby (Key Vault). |
| **odcizen** |Identita z tokenu, který byl předložen v žádosti REST API. Obvykle se jedná o "uživatel", "instanční objekt" nebo kombinaci "User + appId", jako v případě požadavku, který je výsledkem rutiny Azure PowerShell. |
| **vlastnosti** |Informace, které se liší v závislosti na operaci ( **OperationName** ). Ve většině případů toto pole obsahuje informace o klientovi (uživatelský agent, který předává klient), přesný REST API identifikátor URI žádosti a stavový kód HTTP. Kromě toho, když se vrátí objekt jako výsledek požadavku (například **Vytvoření** nebo **VaultGet** ), obsahuje taky identifikátor URI klíče (jako `id` ), identifikátor URI trezoru nebo tajný identifikátor URI. |

Hodnoty polí **OperationName** jsou ve formátu *ObjectVerb* . Například:

* Všechny operace trezoru klíčů mají `Vault<action>` formát, například `VaultGet` a `VaultCreate` .
* Všechny operace s klíči mají `Key<action>` formát, například `KeySign` a `KeyList` .
* Všechny operace tajného kódu mají `Secret<action>` formát, například `SecretGet` a `SecretListVersions` .

Následující tabulka uvádí hodnoty **OperationName** a odpovídající REST API příkazy:

### <a name="operation-names-table"></a>Tabulka názvů operací

| operationName | REST API – příkaz |
| --- | --- |
| **Authentication** |Ověřování prostřednictvím Azure Active Directoryho koncového bodu |
| **VaultGet** |[Získání informací o trezoru klíčů](/rest/api/keyvault/vaults) |
| **VaultPut** |[Vytvoření nebo aktualizace trezoru klíčů](/rest/api/keyvault/vaults) |
| **VaultDelete** |[Odstranění trezoru klíčů](/rest/api/keyvault/vaults) |
| **VaultPatch** |[Aktualizace trezoru klíčů](/rest/api/keyvault/vaults) |
| **VaultList** |[Výpis všech trezorů klíčů ve skupině prostředků](/rest/api/keyvault/vaults) |
| **KeyCreate** |[Vytvoření klíče](/rest/api/keyvault/createkey) |
| **KeyGet** |[Získání informací o klíči](/rest/api/keyvault/getkey) |
| **KeyImport** |[Import klíče do trezoru](/rest/api/keyvault/vaults) |
| **KeyBackup** |[Zálohování klíče](/rest/api/keyvault/backupkey) |
| **KeyDelete** |[Odstranění klíče](/rest/api/keyvault/deletekey) |
| **KeyRestore** |[Obnovení klíče](/rest/api/keyvault/restorekey) |
| **KeySign** |[Podpis klíčem](/rest/api/keyvault/sign) |
| **KeyVerify** |[Ověření pomocí klíče](/rest/api/keyvault/vaults) |
| **KeyWrap** |[Zabalení klíče](/rest/api/keyvault/wrapkey) |
| **KeyUnwrap** |[Rozbalení klíče](/rest/api/keyvault/unwrapkey) |
| **KeyEncrypt** |[Šifrování pomocí klíče](/rest/api/keyvault/encrypt) |
| **KeyDecrypt** |[Dešifrování pomocí klíče](/rest/api/keyvault/decrypt) |
| **KeyUpdate** |[Aktualizace klíče](/rest/api/keyvault/updatekey) |
| **KeyList** |[Výpis klíčů v trezoru](/rest/api/keyvault/vaults) |
| **KeyListVersions** |[Výpis verzí klíče](/rest/api/keyvault/getkeyversions) |
| **SecretSet** |[Vytvoření tajného kódu](/rest/api/keyvault/updatecertificate) |
| **SecretGet** |[Získat tajný kód](/rest/api/keyvault/getsecret) |
| **SecretUpdate** |[Aktualizace tajného kódu](/rest/api/keyvault/updatesecret) |
| **SecretDelete** |[Odstranění tajného kódu](/rest/api/keyvault/deletesecret) |
| **SecretList** |[Výpis tajných kódů v trezoru](/rest/api/keyvault/vaults) |
| **SecretListVersions** |[Výpis verzí tajného kódu](/rest/api/keyvault/getsecretversions) |
| **VaultAccessPolicyChangedEventGridNotification** | Publikovaná událost změny zásad přístupu k trezoru |
| **SecretNearExpiryEventGridNotification** |Publikovaná událost v blízkosti vypršení platnosti tajného kódu |
| **SecretExpiredEventGridNotification** |Publikovaná událost vypršela v tajnosti |
| **KeyNearExpiryEventGridNotification** |Vydaná událost v blízkosti vypršení platnosti klíče |
| **KeyExpiredEventGridNotification** |Vydaná událost vypršení platnosti klíče |
| **CertificateNearExpiryEventGridNotification** |Publikovaná událost poblíž vypršení platnosti certifikátu |
| **CertificateExpiredEventGridNotification** |Událost vypršení platnosti certifikátu byla publikována. |

## <a name="use-azure-monitor-logs"></a>Použití protokolů Azure Monitoru

K revizi protokolů Key Vault můžete použít řešení Key Vault v protokolech Azure Monitor `AuditEvent` . V protokolech Azure Monitor použijete dotazy protokolu k analýze dat a získání informací, které potřebujete. 

Další informace, včetně postupu nastavení, najdete [v tématu Azure Key Vault v Azure monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Další kroky

- [Postup povolení protokolování Key Vault](howto-logging.md)
- Kurz, který používá Azure Key Vault ve webové aplikaci .NET, najdete v tématu [použití Azure Key Vault z webové aplikace](tutorial-net-create-vault-azure-web-app.md).
- Programátorské reference najdete v [příručce pro vývojáře Azure Key Vault](developers-guide.md).
- Seznam rutin Azure PowerShell 1,0 pro Azure Key Vault najdete v tématu [rutiny Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).