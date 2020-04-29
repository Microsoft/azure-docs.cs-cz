---
title: Azure Key Vault protokolování | Microsoft Docs
description: Tento kurz vám pomůže začít s protokolováním v Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: e9198892f95635add27bcfe9e479d0dd6fe3f08d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422589"
---
# <a name="azure-key-vault-logging"></a>Protokolování v Azure Key Vaultu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Po vytvoření jednoho nebo více trezorů klíčů budete pravděpodobně chtít sledovat, jak a kdy jsou k vašim trezorům klíčů přistupované a kým. Můžete to udělat tak, že povolíte protokolování pro Azure Key Vault, které ukládá informace v účtu úložiště Azure, který poskytnete. Pro zadaný účet úložiště se automaticky vytvoří nový kontejner s názvem **Insights-logs-auditevent** . Stejný účet úložiště můžete použít pro shromažďování protokolů pro více trezorů klíčů.

Po operaci trezoru klíčů máte přístup k informacím o protokolování 10 minut (nejvíce). Ve většině případů to bude rychlejší.  Správa protokolů v účtu úložiště je pouze na vás:

* Zabezpečte protokoly pomocí standardních metod řízení přístupu Azure a určete, kdo k nim má přístup.
* Odstraňte protokoly, které už nechcete uchovávat v účtu úložiště.

Tento kurz vám pomůže začít s protokolováním v Azure Key Vault. Vytvoříte účet úložiště, povolíte protokolování a interpretují shromážděné informace protokolu.  

> [!NOTE]
> Tento kurz neobsahuje pokyny k vytvoření trezorů klíčů, klíčů nebo tajných klíčů. Tyto informace najdete v tématu [co je Azure Key Vault?](overview.md)). Další pokyny pro Azure CLI pro různé platformy najdete v [tomto ekvivalentním kurzu](manage-with-cli2.md).
>
> Tento článek poskytuje Azure PowerShell pokyny pro aktualizaci protokolování diagnostiky. Můžete také aktualizovat diagnostické protokolování pomocí Azure Monitor v části **diagnostické protokoly** v Azure Portal. 
>

Přehled informací o Key Vault najdete v tématu [co je Azure Key Vault?](overview.md)). Informace o tom, kde je Key Vault k dispozici, najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete:

* Existující trezor klíčů, který již používáte.  
* Azure PowerShell minimální verze 1.0.0. Chcete-li nainstalovat Azure PowerShell a přidružit ho ke svému předplatnému Azure, prohlédněte si téma [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). Pokud jste již nainstalovali Azure PowerShell a neznáte verzi, v konzole Azure PowerShell zadejte `$PSVersionTable.PSVersion`.  
* Dostatečné úložiště v Azure pro vaše protokoly Key Vault.

## <a name="connect-to-your-key-vault-subscription"></a><a id="connect"></a>Připojení k předplatnému trezoru klíčů

Prvním krokem při nastavování protokolování klíčů je Ukázat Azure PowerShell k trezoru klíčů, který chcete protokolovat.

Spusťte relaci Azure PowerShell a přihlaste se k účtu Azure pomocí následujícího příkazu:  

```powershell
Connect-AzAccount
```

V automaticky otevřeném okně prohlížeče zadejte svoje uživatelské jméno a heslo k účtu Azure. Azure PowerShell získá všechna předplatná, která jsou přidružená k tomuto účtu. Ve výchozím nastavení používá prostředí PowerShell první z nich.

Možná budete muset zadat předplatné, které jste použili k vytvoření trezoru klíčů. Zadáním následujícího příkazu zobrazíte odběry pro váš účet:

```powershell
Get-AzSubscription
```

Pak můžete zadat předplatné, které je přidružené k trezoru klíčů, který budete přihlašovat:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

Ukazatel PowerShellu na pravé předplatné je důležitý krok, zejména pokud máte k vašemu účtu přidruženo několik předplatných. Další informace o konfiguraci prostředí Azure PowerShell najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

## <a name="create-a-storage-account-for-your-logs"></a><a id="storage"></a>Vytvoření účtu úložiště pro vaše protokoly

I když pro svoje protokoly můžete použít existující účet úložiště, vytvoříme účet úložiště, který bude vyhrazený pro protokoly Key Vault. V případě, že je to potřeba později zadat, uložíme podrobnosti do proměnné s názvem **SA**.

Pro další snadné řízení používáme také stejnou skupinu prostředků jako ta, která obsahuje Trezor klíčů. V [úvodním kurzu](../secrets/quick-create-cli.md)se tato skupina prostředků jmenuje **ContosoResourceGroup**a budeme dál používat východní Asie umístění. Tyto hodnoty nahraďte vlastními, a to podle potřeby:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Pokud se rozhodnete použít existující účet úložiště, musí používat stejné předplatné jako váš Trezor klíčů. A musí používat model nasazení Azure Resource Manager, nikoli model nasazení Classic.
>
>

## <a name="identify-the-key-vault-for-your-logs"></a><a id="identify"></a>Určení trezoru klíčů pro protokoly

V [úvodním kurzu](../secrets/quick-create-cli.md)se název trezoru klíčů **ContosoKeyVault**. Budeme dál používat tento název a uložíme podrobnosti do proměnné s názvem **KV**:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a name="enable-logging"></a><a id="enable"></a>Povolit protokolování

Pokud chcete povolit protokolování pro Key Vault, použijeme rutinu **set-AzDiagnosticSetting** spolu s proměnnými, které jsme vytvořili pro nový účet úložiště a trezoru klíčů. Také nastavíme příznak **-Enabled** na **$true** a nastavíte kategorii na **AuditEvent** (jediná kategorie pro protokolování Key Vault):

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Výstup bude vypadat nějak takto:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

Tento výstup potvrdí, že je pro váš Trezor klíčů povolené protokolování, a uloží informace do svého účtu úložiště.

Volitelně můžete pro své protokoly nastavit zásady uchovávání informací tak, aby se starší protokoly automaticky odstranily. Nastavte například zásady uchovávání informací nastavením příznaku **-RetentionEnabled** na **$true**a nastavte parametr **-RetentionInDays** na **90** tak, aby se automaticky odstranily protokoly starší než 90 dní.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Co je protokolováno:

* Všechny požadavky ověřených REST API, včetně neúspěšných žádostí v důsledku oprávnění přístupu, systémových chyb nebo chybných požadavků.
* Operace samotného trezoru klíčů, včetně vytvoření, odstranění, nastavení zásad přístupu trezoru klíčů a aktualizace atributů trezoru klíčů, například značek.
* Operace s klíči a tajnými klíči v trezoru klíčů, včetně:
  * Vytváření, úpravy nebo odstraňování těchto klíčů nebo tajných kódů.
  * Podepisování, ověřování, šifrování, dešifrování, zabalení a rozbalení klíčů, získávání tajných klíčů a zobrazování klíčů a tajných kódů (a jejich verzí).
* Neověřené požadavky, které skončí odpovědí 401 – Neoprávněno. Příklady jsou požadavky, které nemají nosný token, které jsou poškozené nebo jejichž platnost vypršela, nebo které mají neplatný token.  

## <a name="access-your-logs"></a><a id="access"></a>Přístup k protokolům

Protokoly Key Vault se ukládají do kontejneru **Insights-logs-auditevent** v účtu úložiště, který jste zadali. Chcete-li zobrazit protokoly, je nutné stáhnout objekty blob.

Nejprve vytvořte proměnnou pro název kontejneru. Tuto proměnnou použijete v celé zbývající části tohoto návodu.

```powershell
$container = 'insights-logs-auditevent'
```

Pokud chcete zobrazit seznam všech objektů BLOB v tomto kontejneru, zadejte:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Výstup vypadá podobně jako tento:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

---
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Jak vidíte z tohoto výstupu, objekty blob dodržují konvenci pojmenování:`resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Hodnoty data a času používají UTC.

Vzhledem k tomu, že ke shromažďování protokolů pro více prostředků můžete použít stejný účet úložiště, úplné ID prostředku v názvu objektu BLOB je užitečné pro přístup k objektům blob, které potřebujete, nebo ke stažení jenom. Ale předtím se podíváme na to, jak stáhnout všechny objekty blob.

Vytvořte složku pro stažení objektů BLOB. Příklad:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Poté získejte seznam všech objektů blob:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Přesměrujte tento seznam prostřednictvím **Get-AzStorageBlobContent** , aby se objekty blob stáhly do cílové složky:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Když spustíte tento druhý příkaz, **/** oddělovač v názvech objektů BLOB vytvoří v cílové složce úplnou strukturu složek. Pomocí této struktury budete stahovat a ukládat objekty BLOB jako soubory.

Chcete-li stahovat objekty blob selektivně, použijte zástupné znaky. Příklad:

* Máte-li více trezorů klíčů a chcete stáhnout pouze protokoly pro jeden trezor klíčů s názvem CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Máte-li více skupin prostředků a chcete stáhnout pouze protokoly pro jednu skupinu prostředků, použijte `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Pokud chcete stáhnout všechny protokoly pro měsíc v lednu 2019, použijte `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Nyní jste připraveni podívat se, co je v protokolech. Ale předtím, než se k němu připravujeme, byste měli znát dva další příkazy:

* Dotaz na stav nastavení diagnostiky pro prostředek trezoru klíčů provedete pomocí:`Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Zakázat protokolování pro prostředek trezoru klíčů můžete pomocí:`Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a name="interpret-your-key-vault-logs"></a><a id="interpret"></a>Interpretace protokolů služby Key Vault

Jednotlivé objekty blob jsou uloženy jako text ve formátu JSON blob. Pojďme se podívat na příklad položky protokolu. Spusťte tento příkaz:

```powershell
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

V následující tabulce jsou uvedené názvy a popisy polí:

| Název pole | Popis |
| --- | --- |
| **interval** |Datum a čas ve standardu UTC. |
| **Prostředku** |ID prostředku Azure Resource Manager. U protokolů Key Vault se jedná vždy o Key Vault Resource ID. |
| **operationName** |Název operace, jak popisuje následující tabulka. |
| **operationVersion** |Verze REST API požadovaná klientem |
| **kategorií** |Typ výsledku. V případě protokolů Key Vault je **AuditEvent** jedinou dostupnou hodnotou. |
| **resultType** |Výsledek žádosti o REST API |
| **resultSignature** |Stav HTTP. |
| **resultDescription** |Další popis výsledku, je-li k dispozici. |
| **durationMs** |Doba trvání obsloužení požadavku REST API v milisekundách. Nezahrnuje latenci sítě, takže čas naměřený na straně klienta se může lišit. |
| **callerIpAddress** |IP adresa klienta, který odeslal požadavek. |
| **correlationId** |Volitelný GUID, který může klient předat pro korelaci protokolů na straně klienta s protokoly na straně služby (Key Vault). |
| **odcizen** |Identita z tokenu, který byl předložen v žádosti REST API. Obvykle se jedná o "uživatel", "instanční objekt" nebo kombinaci "User + appId", jako v případě požadavku, který je výsledkem rutiny Azure PowerShell. |
| **vlastnosti** |Informace, které se liší v závislosti na operaci (**OperationName**). Ve většině případů toto pole obsahuje informace o klientovi (uživatelský agent, který předává klient), přesný REST API identifikátor URI žádosti a stavový kód HTTP. Kromě toho, když se vrátí objekt jako výsledek požadavku (například **Vytvoření** nebo **VaultGet**), obsahuje taky identifikátor URI klíče (as "ID"), identifikátor URI trezoru nebo tajný identifikátor URI. |

Hodnoty polí **OperationName** jsou ve formátu *ObjectVerb* . Příklad:

* Všechny operace trezoru klíčů mají `Vault<action>` formát, například `VaultGet` a `VaultCreate`.
* Všechny operace s klíči mají `Key<action>` formát, například `KeySign` a `KeyList`.
* Všechny operace tajného kódu `Secret<action>` mají formát, například `SecretGet` a `SecretListVersions`.

Následující tabulka uvádí hodnoty **OperationName** a odpovídající REST API příkazy:

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

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>Použití protokolů Azure Monitoru

Můžete použít řešení Key Vault v protokolech Azure Monitor ke kontrole Key Vault protokolů **AuditEvent** . V protokolech Azure Monitor použijete dotazy protokolu k analýze dat a získání informací, které potřebujete. 

Další informace, včetně postupu nastavení, najdete [v tématu Azure Key Vault řešení v protokolech Azure monitor](../../azure-monitor/insights/azure-key-vault.md). Tento článek obsahuje také pokyny, pokud potřebujete migrovat ze starého řešení Key Vault, které bylo nabídnuto během verze Preview Azure Monitorch protokolů, kde jste nakonfigurovali protokoly do účtu služby Azure Storage a nakonfigurovali Azure Monitor protokoly pro čtení.

## <a name="next-steps"></a><a id="next"></a>Další kroky

Kurz, který používá Azure Key Vault ve webové aplikaci .NET, najdete v tématu [použití Azure Key Vault z webové aplikace](tutorial-net-create-vault-azure-web-app.md).

Programátorské reference najdete v [příručce pro vývojáře Azure Key Vault](developers-guide.md).

Seznam rutin Azure PowerShell 1,0 pro Azure Key Vault najdete v tématu [rutiny Azure Key Vault](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

Kurz týkající se střídání klíčů a auditování protokolů pomocí Azure Key Vault najdete v tématu [nastavení Key Vault s koncovým otočením a auditováním klíčů](../secrets/key-rotation-log-monitoring.md).
