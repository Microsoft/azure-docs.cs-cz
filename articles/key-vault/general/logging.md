---
title: Protokolování úložiště klíčů Azure | Dokumenty společnosti Microsoft
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422589"
---
# <a name="azure-key-vault-logging"></a>Protokolování v Azure Key Vaultu

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Po vytvoření jednoho nebo více trezorů klíčů budete pravděpodobně chtít sledovat, jak a kdy jsou k trezorům klíčů přistupovány a kým. Můžete to provést povolením protokolování pro Azure Key Vault, který ukládá informace v účtu úložiště Azure, které poskytujete. Pro zadaný účet úložiště se automaticky vytvoří nový kontejner s názvem **insights-logs-auditevent.** Stejný účet úložiště můžete použít pro shromažďování protokolů pro více trezorů klíčů.

Přístup k informacím o protokolování můžete získat 10 minut (maximálně) po operaci trezoru klíčů. Ve většině případů to bude rychlejší.  Správa protokolů v účtu úložiště je pouze na vás:

* Zabezpečte protokoly pomocí standardních metod řízení přístupu Azure a určete, kdo k nim má přístup.
* Odstraňte protokoly, které už nechcete uchovávat v účtu úložiště.

Tento kurz vám pomůže začít s protokolováním v Azure Key Vault. Vytvoříte účet úložiště, povolíte protokolování a interpretujete shromážděné informace protokolu.  

> [!NOTE]
> Tento kurz neobsahuje pokyny k vytvoření trezorů klíčů, klíčů nebo tajných klíčů. Tyto informace naleznete v tématu [Co je Azure Key Vault?](overview.md)). Nebo pokyny pro různé platformy Azure CLI, naleznete [v tomto ekvivalentním kurzu](manage-with-cli2.md)).
>
> Tento článek obsahuje pokyny k Azure PowerShellu pro aktualizaci protokolování diagnostiky. Protokolování diagnostiky můžete také aktualizovat pomocí nástroje Azure Monitor v části **Diagnostické protokoly** na webu Azure Portal. 
>

Informace o trezoru klíčů najdete v tématu [Co je Azure Key Vault?](overview.md)). Informace o tom, kde je trezor klíčů k dispozici, naleznete na [stránce s cenami](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete:

* Existující trezor klíčů, který již používáte.  
* Azure PowerShell, minimální verze 1.0.0. Chcete-li nainstalovat Azure PowerShell a přidružit ho ke svému předplatnému Azure, prohlédněte si téma [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). Pokud jste už nainstalovali Azure PowerShell a neznáte verzi, z `$PSVersionTable.PSVersion`konzoly Azure PowerShell, zadejte .  
* Dostatečné úložiště v Azure pro vaše protokoly Key Vault.

## <a name="connect-to-your-key-vault-subscription"></a><a id="connect"></a>Připojení k předplatnému trezoru klíčů

Prvním krokem při nastavování protokolování klíčů je nasměrovat Azure PowerShell do trezoru klíčů, který chcete protokolovat.

Spusťte relaci Azure PowerShellu a přihlaste se ke svému účtu Azure pomocí následujícího příkazu:  

```powershell
Connect-AzAccount
```

V automaticky otevřeném okně prohlížeče zadejte svoje uživatelské jméno a heslo k účtu Azure. Azure PowerShell získá všechna předplatná, která jsou přidružená k tomuto účtu. Ve výchozím nastavení používá Prostředí PowerShell první.

Možná budete muset zadat předplatné, které jste použili k vytvoření trezoru klíčů. Chcete-li zobrazit předplatná svého účtu, zadejte následující příkaz:

```powershell
Get-AzSubscription
```

Chcete-li zadat předplatné přidružené k trezoru klíčů, který budete protokolovat, zadejte:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

Ukazuje PowerShell na pravé předplatné je důležitý krok, zejména pokud máte více předplatných spojených s vaším účtem. Další informace o konfiguraci prostředí Azure PowerShell najdete v tématu [Instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

## <a name="create-a-storage-account-for-your-logs"></a><a id="storage"></a>Vytvoření účtu úložiště pro protokoly

I když pro vaše protokoly můžete použít existující účet úložiště, vytvoříme účet úložiště, který bude vyhrazen pro protokoly trezoru klíčů. Pro pohodlí, když budeme muset zadat později, uložíme podrobnosti v proměnné s názvem **sa**.

Pro další snadnou správu použijeme také stejnou skupinu prostředků, jako je ta, která obsahuje trezor klíčů. Z [kurzu začínáme](../secrets/quick-create-cli.md), tato skupina prostředků s názvem **ContosoResourceGroup**, a budeme i nadále používat umístění východní Asie. Nahraďte tyto hodnoty vlastními hodnotami:

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Pokud se rozhodnete použít existující účet úložiště, musí používat stejné předplatné jako trezor klíčů. A musí používat model nasazení Azure Resource Manager, nikoli klasický model nasazení.
>
>

## <a name="identify-the-key-vault-for-your-logs"></a><a id="identify"></a>Určení trezoru klíčů pro protokoly

V [kurzu začínáme](../secrets/quick-create-cli.md)název trezoru klíčů **contosoKeyVault**. Budeme i nadále používat tento název a uložit podrobnosti v proměnné s názvem **kv**:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a name="enable-logging"></a><a id="enable"></a>Povolit protokolování

Chcete-li povolit protokolování pro trezor klíčů, použijeme rutinu **Set-AzDiagnosticSetting** spolu s proměnnými, které jsme vytvořili pro nový účet úložiště a trezor klíčů. Také nastavíme příznak **-Enabled** na **$true** a nastavíme kategorii na **AuditEvent** (jediná kategorie pro protokolování trezoru klíčů):

```powershell
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

Tento výstup potvrzuje, že protokolování je nyní povoleno pro trezor klíčů a uloží informace do vašeho účtu úložiště.

Volitelně můžete nastavit zásady uchovávání informací pro protokoly tak, aby starší protokoly byly automaticky odstraněny. Například nastavte zásady uchovávání informací nastavením příznaku **RetentionEnabled** na **$true**a nastavte parametr **-RetentionInDays** na **90** tak, aby protokoly starší než 90 dní byly automaticky odstraněny.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Co je protokolováno:

* Všechny ověřené požadavky rozhraní REST API, včetně neúspěšných požadavků v důsledku přístupových oprávnění, systémových chyb nebo chybných požadavků.
* Operace samotného trezoru klíčů, včetně vytvoření, odstranění, nastavení zásad přístupu k trezoru klíčů a aktualizace atributů trezoru klíčů, jako jsou značky.
* Operace s klíči a tajnými klíči v trezoru klíčů, včetně:
  * Vytváření, úpravy nebo odstranění těchto klíčů nebo tajných klíčů.
  * Podepisování, ověřování, šifrování, dešifrování, balení a rozbalení klíčů, získávání tajných kódů a výpis klíčů a tajných kódů (a jejich verzí).
* Neověřené požadavky, které skončí odpovědí 401 – Neoprávněno. Příklady jsou požadavky, které nemají nosný token, které jsou poškozené nebo vypršela nebo které mají neplatný token.  

## <a name="access-your-logs"></a><a id="access"></a>Přístup k protokolům

Protokoly trezoru klíčů jsou uloženy v kontejneru **insights-logs-auditevent** v účtu úložiště, který jste poskytli. Chcete-li zobrazit protokoly, musíte stáhnout objekty BLOB.

Nejprve vytvořte proměnnou pro název kontejneru. Tuto proměnnou budete používat po zbytek návodu.

```powershell
$container = 'insights-logs-auditevent'
```

Chcete-li vypsat všechny objekty BLOB v tomto kontejneru, zadejte:

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

Jak můžete vidět z tohoto výstupu, objekty BLOB postupujte podle konvence pojmenování:`resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Hodnoty data a času používají UTC.

Vzhledem k tomu, že ke shromažďování protokolů pro více prostředků můžete použít stejný účet úložiště, úplné ID prostředků v názvu objektu blob je užitečné pro přístup nebo stažení pouze objektů BLOB, které potřebujete. Ale předtím se podíváme na to, jak stáhnout všechny objekty blob.

Vytvořte složku pro stažení objektů BLOB. Příklad:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Poté získejte seznam všech objektů blob:  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Kanál tento seznam prostřednictvím **Get-AzStorageBlobContent** stáhnout objekty BLOB do cílové složky:

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Při spuštění tohoto druhého **/** příkazu vytvoří oddělovač v názvech objektů blob úplnou strukturu složek pod cílovou složkou. Tuto strukturu použijete ke stažení a uložení objektů BLOB jako souborů.

Chcete-li stahovat objekty blob selektivně, použijte zástupné znaky. Příklad:

* Máte-li více trezorů klíčů a chcete stáhnout pouze protokoly pro jeden trezor klíčů s názvem CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Máte-li více skupin prostředků a chcete stáhnout pouze protokoly pro jednu skupinu prostředků, použijte `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Pokud si chcete stáhnout všechny protokoly za měsíc leden 2019, použijte `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Nyní jste připraveni podívat se, co je v protokolech. Ale než se k tomu přeneseme, měl bys znát další dva příkazy:

* Dotaz na stav nastavení diagnostiky pro prostředek trezoru klíčů provedete pomocí:`Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Zakázat protokolování pro prostředek trezoru klíčů můžete pomocí:`Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a name="interpret-your-key-vault-logs"></a><a id="interpret"></a>Interpretace protokolů služby Key Vault

Jednotlivé objekty blob jsou uloženy jako text ve formátu JSON blob. Podívejme se na ukázkovou položku protokolu. Spusťte tento příkaz:

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

V následující tabulce jsou uvedeny názvy a popisy polí:

| Název pole | Popis |
| --- | --- |
| **Čas** |Datum a čas v čase UTC. |
| **Resourceid** |ID prostředku Správce prostředků Azure. U protokolů trezoru klíčů se vždy jedná o ID prostředku trezoru klíčů. |
| **název_operace** |Název operace, jak popisuje následující tabulka. |
| **operationVersion** |VERZE ROZHRANÍ REST API požadovaná klientem. |
| **Kategorie** |Typ výsledku. Pro protokoly trezoru klíčů **je AuditEvent** je jedinou dostupnou hodnotou. |
| **resultType** |Výsledek požadavku rozhraní REST API. |
| **resultSignature** |Stav HTTP. |
| **resultDescription** |Další popis výsledku, je-li k dispozici. |
| **durationMs** |Doba trvání obsloužení požadavku REST API v milisekundách. Nezahrnuje latenci sítě, takže čas naměřený na straně klienta se může lišit. |
| **callerIpAddress** |IP adresa klienta, který podal požadavek. |
| **correlationId** |Volitelný GUID, který může klient předat pro korelaci protokolů na straně klienta s protokoly na straně služby (Key Vault). |
| **Identity** |Identita z tokenu, který byl uveden v požadavku rozhraní REST API. Obvykle se jedná o "uživatele", "instanční objekt" nebo kombinaci "user+appId", jako v případě požadavku, který je výsledkem rutiny prostředí Azure PowerShell. |
| **Vlastnosti** |Informace, které se liší v závislosti na operaci (**operationName**). Ve většině případů toto pole obsahuje informace o klientovi (řetězec uživatelského agenta předaný klientem), přesný identifikátor URI požadavku rozhraní REST API a stavový kód HTTP. Kromě toho při vrácení objektu jako výsledek požadavku (například **KeyCreate** nebo **VaultGet)** obsahuje také identifikátor URI klíče (jako "id"), identifikátor URI úložiště nebo tajný identifikátor URI. |

Hodnoty pole **operationName** jsou ve formátu *ObjectVerb.* Příklad:

* Všechny operace trezoru `Vault<action>` klíčů mají `VaultGet` `VaultCreate`formát, například a .
* Všechny klíčové operace `Key<action>` mají formát, například `KeySign` a `KeyList`.
* Všechny tajné operace `Secret<action>` mají formát, například `SecretGet` a `SecretListVersions`.

V následující tabulce jsou uvedeny hodnoty **operationName** a odpovídající příkazy rozhraní REST API:

| operationName | Rozhraní REST API, příkaz |
| --- | --- |
| **Authentication** |Ověření prostřednictvím koncového bodu Služby Azure Active Directory |
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
| **SecretGet** |[Získejte tajemství](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Aktualizace tajného kódu](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Odstranění tajného kódu](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Výpis tajných kódů v trezoru](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Výpis verzí tajného kódu](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a name="use-azure-monitor-logs"></a><a id="loganalytics"></a>Použití protokolů Azure Monitoru

Řešení trezoru klíčů v protokolech Azure Monitor umítnete ke kontrole protokolů AuditEvent protokolu **trezoru** klíčů. V protokolech Azure Monitor pomocí dotazů protokolu můžete analyzovat data a získat informace, které potřebujete. 

Další informace, včetně toho, jak to nastavit, najdete [v tématu Azure Key Vault řešení v protokolech Azure Monitor](../../azure-monitor/insights/azure-key-vault.md). Tento článek také obsahuje pokyny, pokud potřebujete migrovat ze starého řešení Trezor klíčů, který byl nabízen během náhledu protokolů Azure Monitor, kde jste nejprve směrovány protokoly na účet úložiště Azure a nakonfigurované protokoly Azure Monitor pro čtení odtud.

## <a name="next-steps"></a><a id="next"></a>Další kroky

Kurz, který používá Azure Key Vault ve webové aplikaci .NET, najdete [v tématu použití azure key vault z webové aplikace](tutorial-net-create-vault-azure-web-app.md).

Programátorské reference najdete v [příručce pro vývojáře Azure Key Vault](developers-guide.md).

Seznam rutin Azure PowerShellu 1.0 pro Azure Key Vault najdete v [tématu Rutiny azure trezoru klíčů](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

Kurz o střídání klíčů a auditování protokolů pomocí trezoru klíčů Azure, najdete v tématu [Nastavení trezoru klíčů s end-to-end střídání klíčů a auditování](../secrets/key-rotation-log-monitoring.md).
