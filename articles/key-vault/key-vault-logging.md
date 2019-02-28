---
title: Protokolování v Azure Key Vault | Dokumentace Microsoftu
description: Tento kurz vám pomůže začít s protokolováním v Azure Key Vault.
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.assetid: 43f96a2b-3af8-4adc-9344-bc6041fface8
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: c9d8dd366ecabe9eb508998d526ddfe7b1da300d
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960500"
---
# <a name="azure-key-vault-logging"></a>Protokolování v Azure Key Vaultu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Po vytvoření jednoho nebo více trezorů klíčů budete pravděpodobně chtít monitorovat jak a kdy jsou k němu přistupovat a kdo vašim trezorům klíčů. Můžete to provést povolením protokolování pro Azure Key Vault, které ukládá informace v účtu služby Azure storage, který zadáte. Nový kontejner s názvem **insights-logs-auditevent** se automaticky vytvoří pro zadaný účet úložiště. Můžete použít ten samý účet úložiště pro shromažďování protokolů více trezorů klíčů.

Dostanete informace o protokolování 10 minut (maximálně) po provedení této operace služby key vault. Ve většině případů to bude rychlejší.  Správa protokolů v účtu úložiště je pouze na vás:

* Zabezpečte protokoly pomocí standardních metod řízení přístupu Azure a určete, kdo k nim má přístup.
* Odstraňte protokoly, které už nechcete uchovávat v účtu úložiště.

Tento kurz vám pomůže začít s protokolováním v Azure Key Vault. Vytvoříte účet úložiště, povolit protokolování a interpretovat informace shromážděné protokolu.  

> [!NOTE]
> Tento kurz neobsahuje pokyny k vytvoření trezorů klíčů, klíčů nebo tajných klíčů. Tyto informace v tématu [co je Azure Key Vault?](key-vault-overview.md). Nebo – multiplatformního rozhraní příkazového řádku Azure najdete v tématu [tomto ekvivalentním kurzu](key-vault-manage-with-cli2.md).
>
> Tento článek obsahuje pokyny pro Azure PowerShell pro aktualizaci protokolování diagnostiky. Protokolování diagnostiky můžete také aktualizovat pomocí Azure monitoru v **diagnostické protokoly** části webu Azure portal. 
>

Další informace o službě Key Vault najdete v článku [co je Azure Key Vault?](key-vault-whatis.md). Informace o tom, kde je k dispozici služby Key Vault najdete v tématu [stránce s cenami](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete:

* Existující trezor klíčů, který již používáte.  
* Azure PowerShell, minimální verze 1.0.0. Chcete-li nainstalovat Azure PowerShell a přidružit ho ke svému předplatnému Azure, prohlédněte si téma [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). Pokud jste již nainstalovali Azure PowerShell a neznáte verzi, z konzoly Azure PowerShell, zadejte `$PSVersionTable.PSVersion`.  
* Dostatečné úložiště v Azure pro vaše protokoly Key Vault.

## <a id="connect"></a>Připojení k předplatnému služby key vault

Prvním krokem při nastavení klíče protokolování je bod Azure PowerShell k trezoru klíčů, který chcete zaznamenat.

Spusťte relaci Azure Powershellu a přihlaste se ke svému účtu Azure pomocí následujícího příkazu:  

```PowerShell
Connect-AzAccount
```

V automaticky otevřeném okně prohlížeče zadejte svoje uživatelské jméno a heslo k účtu Azure. Prostředí Azure PowerShell získá všechna předplatná, které jsou spojeny s tímto účtem. PowerShell ve výchozím nastavení použije první předplatné.

Budete muset zadat předplatné, které jste použili k vytvoření trezoru klíčů. Zadejte následující příkaz k zobrazení předplatných pro váš účet:

```PowerShell
Get-AzSubscription
```

Potom určete předplatné, který je spojen s využitím key vaultu, který budete protokolovat, zadejte:

```PowerShell
Set-AzContext -SubscriptionId <subscription ID>
```

Správné předplatné přejdete na příkaz Powershellu je důležitý krok, zejména v případě, že máte více předplatných, které jsou spojené s vaším účtem. Další informace o konfiguraci prostředí Azure PowerShell najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

## <a id="storage"></a>Vytvoření účtu úložiště pro protokoly

I když používáte existující účet úložiště pro protokoly vytvoříme účet úložiště, který bude vyhrazený pro protokoly Key Vault. Pro usnadnění máme při pozdějším zadávání, uložíme podrobnosti do proměnné s názvem **sa**.

Pro další usnadnění správy použijeme také stejnou skupinu prostředků jako ten, který obsahuje služby key vault. Z [úvodním kurzu](key-vault-get-started.md), tato skupina prostředků se jmenuje **ContosoResourceGroup**, a budeme nadále používat umístění ve východní Asii. Tyto hodnoty nahraďte svými vlastními, jak použít:

```PowerShell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Pokud se rozhodnete použít existující účet úložiště, musí používat stejné předplatné jako váš trezor klíčů. A musí používat model nasazení Azure Resource Manageru, nikoli modelu nasazení classic.
>
>

## <a id="identify"></a>Určení trezoru klíčů pro protokoly

V [úvodním kurzu](key-vault-get-started.md), byl název trezoru klíčů **ContosoKeyVault**. Budeme nadále používat tento název a podrobnosti uložíme do proměnné s názvem **kv**:

```PowerShell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Povolení protokolování

Povolení protokolování pro Key Vault, použijeme **Set-AzDiagnosticSetting** rutiny, spolu s proměnnými, které jsme vytvořili pro nový účet úložiště a trezoru klíčů. Také nastavíme **-povoleno** příznak **$true** a kategorii nastavíme na **AuditEvent** (jediná kategorie pro protokolování v Key Vault):

```PowerShell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Výstup vypadá takto:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

Tento výstup potvrzuje, že teď je povoleno protokolování pro key vault, a informace se uloží do vašeho účtu úložiště.

Volitelně můžete nastavit zásady uchovávání informací pro svoje protokoly tak, aby se starší protokoly automaticky odstraní. Například nastavit zásady uchovávání informací tím, že nastavíte **- RetentionEnabled** příznak **$true**a nastavte **- RetentionInDays** parametr **90**tak, aby protokoly starší než 90 dnů automaticky odstraněny.

```PowerShell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Co je protokolováno:

* Všechny ověřené požadavky REST API, včetně neúspěšné požadavky v důsledku oprávnění k přístupu, systémových chyb nebo chybných požadavků.
* Operace s klíči trezoru samostatně, včetně vytváření, odstraňování, nastavení zásad přístupu trezoru klíčů a aktualizaci atributů trezoru klíčů, jako jsou například značky.
* Operace nad klíči a tajnými kódy ve službě key vault, včetně:
  * Vytváření, úprava nebo odstraňování těchto klíčů nebo tajných kódů.
  * Podepsání, ověření, šifrování, dešifrování, zabalení a rozbalení klíčů, získání tajné klíče, výpis klíčů a tajných kódů (a jejich verze).
* Neověřené požadavky, které skončí odpovědí 401 – Neoprávněno. Příklady jsou požadavky, které nemají nosný token, které jsou poškozené nebo jejichž platnost vypršela, nebo mají neplatný token.  

## <a id="access"></a>Přístup k protokolům

Protokoly Key Vault se ukládají v **insights-logs-auditevent** kontejneru v účtu úložiště, které jste zadali. Pokud chcete zobrazit protokoly, budete muset stahovat objekty BLOB.

Nejprve vytvořte proměnnou pro název kontejneru. Tato proměnná po celý zbytek návodu budete používat.

```PowerShell
$container = 'insights-logs-auditevent'
```

Chcete-li vypsat všechny objekty BLOB v tomto kontejneru, zadejte:

```PowerShell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Výstup vypadá podobně jako tento:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

- - -
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Jak vidíte z tohoto výstupu, objekty BLOB dodržovat zásadu vytváření názvů: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Hodnoty data a času používají UTC.

Protože shromažďování protokolů u několika prostředků můžete použít stejný účet úložiště, je vhodné získat nebo stáhnout jenom objekty BLOB, které je třeba je úplné ID prostředku v názvu objektu blob. Ale předtím se podíváme na to, jak stáhnout všechny objekty blob.

Vytvořte složku, kam stáhnete objekty BLOB. Příklad:

```PowerShell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Poté získejte seznam všech objektů blob:  

```PowerShell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Předejte tento seznam prostřednictvím **Get-AzStorageBlobContent** stahovat objekty BLOB do cílové složky:

```PowerShell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Když spustíte tento druhý příkaz **/** oddělovač v názvech objektů blob vytvoří úplnou strukturu složek v cílové složce. Tato struktura budete používat ke stažení a uložení objektů BLOB jako soubory.

Chcete-li stahovat objekty blob selektivně, použijte zástupné znaky. Příklad:

* Máte-li více trezorů klíčů a chcete stáhnout pouze protokoly pro jeden trezor klíčů s názvem CONTOSOKEYVAULT3:

  ```PowerShell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Máte-li více skupin prostředků a chcete stáhnout pouze protokoly pro jednu skupinu prostředků, použijte `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```PowerShell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Pokud budete chtít stáhnout všechny protokoly pro měsíc leden 2019, použijte `-Blob '*/year=2019/m=01/*'`:

  ```PowerShell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Nyní jste připraveni podívat se, co je v protokolech. Ale než se pustíme do, který, které byste měli mít dva další příkazy:

* Dotaz na stav nastavení diagnostiky pro prostředek trezoru klíčů provedete pomocí:`Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Zakázat protokolování pro prostředek trezoru klíčů můžete pomocí:`Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>Interpretace protokolů služby Key Vault

Jednotlivé objekty blob jsou uloženy jako text ve formátu JSON blob. Podívejme se na příklad položky protokolu. Spusťte tento příkaz:

```PowerShell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Vrátí položku protokolu podobnou této:

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

V následující tabulce jsou uvedeny popisy a názvy polí:

| Název pole | Popis |
| --- | --- |
| **čas** |Datum a čas ve standardu UTC. |
| **ID prostředku** |ID prostředku Azure Resource Manageru Pro protokoly Key Vault je to vždy ID prostředku Key Vault. |
| **OperationName** |Název operace, jak popisuje následující tabulka. |
| **operationVersion** |Verze rozhraní REST API požadovaná klientem. |
| **Kategorie** |Typ výsledku. Pro protokoly Key Vault **AuditEvent** je jedinou dostupnou hodnotou. |
| **resultType** |Výsledek požadavku REST API. |
| **resultSignature** |Stav HTTP. |
| **resultDescription** |Další popis výsledku, je-li k dispozici. |
| **durationMs** |Doba trvání obsloužení požadavku REST API v milisekundách. Nezahrnuje latenci sítě, takže čas naměřený na straně klienta se může lišit. |
| **callerIpAddress** |IP adresa klienta, ze kterého přišel požadavek. |
| **correlationId** |Volitelný GUID, který může klient předat pro korelaci protokolů na straně klienta s protokoly na straně služby (Key Vault). |
| **Identita** |Identita z tokenu, který byl předložený v požadavku REST API. Toto je obvykle "user", "service principal" nebo kombinace "user + appId," jako v případě požadavku, která je výsledkem rutiny Azure Powershellu. |
| **Vlastnosti** |Informace, které se liší podle operace (**operationName**). Ve většině případů toto pole obsahuje informace o klientovi (řetězec agenta uživatele předaný klientem), přesný identifikátor URI požadavku REST API a stavový kód HTTP. Kromě toho, pokud je vrácen objekt jako výsledek požadavku (například **KeyCreate** nebo **VaultGet**), obsahuje také klíč trezoru identifikátoru URI (jako "id"), identifikátor URI nebo identifikátor URI tajného kódu. |

**OperationName** hodnoty polí jsou v *ObjectVerb* formátu. Příklad:

* Všechny operace služby key vault mít `Vault<action>` formátování, jako například `VaultGet` a `VaultCreate`.
* Všechny operace nad klíči jsou `Key<action>` formátování, jako například `KeySign` a `KeyList`.
* Všechny operace nad tajnými klíči `Secret<action>` formátování, jako například `SecretGet` a `SecretListVersions`.

Následující tabulce jsou uvedeny **operationName** hodnoty a odpovídajících příkazů REST API:

| operationName | Příkaz REST API |
| --- | --- |
| **Ověřování** |Ověřování prostřednictvím koncového bodu Azure Active Directory |
| **VaultGet** |[Získání informací o trezoru klíčů](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Vytvoření nebo aktualizace trezoru klíčů](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Odstranění trezoru klíčů](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Aktualizace trezoru klíčů](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Výpis všech trezorů klíčů ve skupině prostředků](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Vytvoření klíče](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Získání informací o klíči](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Import klíče do trezoru](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Zálohovat šifrovací klíč](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
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
| **SecretGet** |[Získání tajného kódu](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Aktualizace tajného kódu](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Odstranění tajného kódu](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Výpis tajných kódů v trezoru](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Výpis verzí tajného kódu](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Použití Log Analytics

Řešení Key Vault ve službě Azure Log Analytics můžete použít ke kontrole služby Key Vault **AuditEvent** protokoly. Ve službě Log Analytics pomocí dotazů na protokoly k analýze dat a získat informace, které potřebujete. 

Další informace včetně nastavení najdete v tématu [Řešení Azure Key Vault v Log Analytics](../azure-monitor/insights/azure-key-vault.md). Tento článek také obsahuje pokyny, pokud je potřeba migrovat z původního řešení Key Vault, které se nabízelo pro Log Analytics verze preview, kam jste nejprve směrovat vaše protokoly do účtu služby Azure storage a nakonfigurovali službu Log Analytics ke čtení z něj.

## <a id="next"></a>Další kroky

Kurz, který používá Azure Key Vault ve webové aplikaci .NET, najdete v tématu [použití Azure Key Vault z webové aplikace](tutorial-net-create-vault-azure-web-app.md).

Programátorské reference najdete v [příručce pro vývojáře Azure Key Vault](key-vault-developers-guide.md).

Seznam rutin Azure Powershellu 1.0 pro Azure Key Vault najdete v tématu [rutiny Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

Kurz k rotaci klíčů a auditování protokolu pomocí služby Azure Key Vault najdete v tématu [nastavení služby Key Vault s začátku do konce obměny klíčů a auditování](key-vault-key-rotation-log-monitoring.md).
