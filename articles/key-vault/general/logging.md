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
ms.openlocfilehash: a51e9a628f67269357d42bd1d3af10c1d86f301a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91739778"
---
# <a name="azure-key-vault-logging"></a>Protokolování v Azure Key Vaultu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Po vytvoření jednoho nebo více trezorů klíčů budete pravděpodobně chtít sledovat, jak a kdy jsou k vašim trezorům klíčů přistupované a kým. Můžete to udělat tak, že povolíte protokolování pro Azure Key Vault, které ukládá informace v účtu úložiště Azure, který poskytnete. Pro zadaný účet úložiště se automaticky vytvoří nový kontejner s názvem **Insights-logs-auditevent** . Stejný účet úložiště můžete použít pro shromažďování protokolů pro více trezorů klíčů.

Po operaci trezoru klíčů máte přístup k informacím o protokolování 10 minut (nejvíce). Ve většině případů to bude rychlejší.  Správa protokolů v účtu úložiště je pouze na vás:

* Zabezpečte protokoly pomocí standardních metod řízení přístupu Azure a určete, kdo k nim má přístup.
* Odstraňte protokoly, které už nechcete uchovávat v účtu úložiště.

Přehled informací o Key Vault najdete v tématu [co je Azure Key Vault?](overview.md)). Informace o tom, kde je Key Vault k dispozici, najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/key-vault/). Informace o použití [Azure monitor pro Key Vault](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview).

## <a name="interpret-your-key-vault-logs"></a>Interpretujte svoje protokoly Key Vault

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
| **vlastnosti** |Informace, které se liší v závislosti na operaci (**OperationName**). Ve většině případů toto pole obsahuje informace o klientovi (uživatelský agent, který předává klient), přesný REST API identifikátor URI žádosti a stavový kód HTTP. Kromě toho, když se vrátí objekt jako výsledek požadavku (například **Vytvoření** nebo **VaultGet**), obsahuje taky identifikátor URI klíče (jako `id` ), identifikátor URI trezoru nebo tajný identifikátor URI. |

Hodnoty polí **OperationName** jsou ve formátu *ObjectVerb* . Například:

* Všechny operace trezoru klíčů mají `Vault<action>` formát, například `VaultGet` a `VaultCreate` .
* Všechny operace s klíči mají `Key<action>` formát, například `KeySign` a `KeyList` .
* Všechny operace tajného kódu mají `Secret<action>` formát, například `SecretGet` a `SecretListVersions` .

Následující tabulka uvádí hodnoty **OperationName** a odpovídající REST API příkazy:

### <a name="operation-names-table"></a>Tabulka názvů operací

| operationName | REST API – příkaz |
| --- | --- |
| **Authentication** |Ověřování prostřednictvím Azure Active Directoryho koncového bodu |
| **VaultGet** |[Získání informací o trezoru klíčů](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Vytvoření nebo aktualizace trezoru klíčů](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Odstranění trezoru klíčů](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Aktualizace trezoru klíčů](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Výpis všech trezorů klíčů ve skupině prostředků](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Vytvoření klíče](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Získání informací o klíči](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Import klíče do trezoru](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Zálohování klíče](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Odstranění klíče](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Obnovení klíče](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Podpis klíčem](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Ověření pomocí klíče](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Zabalení klíče](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Rozbalení klíče](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Šifrování pomocí klíče](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Dešifrování pomocí klíče](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Aktualizace klíče](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[Výpis klíčů v trezoru](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[Výpis verzí klíče](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Vytvoření tajného kódu](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Získat tajný kód](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Aktualizace tajného kódu](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Odstranění tajného kódu](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Výpis tajných kódů v trezoru](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Výpis verzí tajného kódu](https://msdn.microsoft.com/library/azure/dn986824.aspx) |
| **VaultAccessPolicyChangedEventGridNotification** | Publikovaná událost změny zásad přístupu k trezoru |
| **SecretNearExpiryEventGridNotification** |Publikovaná událost v blízkosti vypršení platnosti tajného kódu |
| **SecretExpiredEventGridNotification** |Publikovaná událost vypršela v tajnosti |
| **KeyNearExpiryEventGridNotification** |Vydaná událost v blízkosti vypršení platnosti klíče |
| **KeyExpiredEventGridNotification** |Vydaná událost vypršení platnosti klíče |
| **CertificateNearExpiryEventGridNotification** |Publikovaná událost poblíž vypršení platnosti certifikátu |
| **CertificateExpiredEventGridNotification** |Událost vypršení platnosti certifikátu byla publikována. |

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>Použití protokolů Azure Monitor

K revizi protokolů Key Vault můžete použít řešení Key Vault v protokolech Azure Monitor `AuditEvent` . V protokolech Azure Monitor použijete dotazy protokolu k analýze dat a získání informací, které potřebujete. 

Další informace, včetně postupu nastavení, najdete [v tématu Azure Key Vault v Azure monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a><a id="next"></a>Další kroky

Kurz, který používá Azure Key Vault ve webové aplikaci .NET, najdete v tématu [použití Azure Key Vault z webové aplikace](tutorial-net-create-vault-azure-web-app.md).

Programátorské reference najdete v [příručce pro vývojáře Azure Key Vault](developers-guide.md).

Seznam rutin Azure PowerShell 1,0 pro Azure Key Vault najdete v tématu [rutiny Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).
