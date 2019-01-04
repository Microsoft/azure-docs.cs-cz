---
title: Protokolování v Azure Key Vault – Azure Key Vault | Dokumentace Microsoftu
description: Tento kurz vám pomůže začít s protokolováním v Azure Key Vault.
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 43f96a2b-3af8-4adc-9344-bc6041fface8
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: barclayn
ms.openlocfilehash: 8e3076f2176739f5b9df5776f27d7483c9fd2692
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "54000406"
---
# <a name="azure-key-vault-logging"></a>Protokolování v Azure Key Vault

Azure Key Vault je dostupný ve většině oblastí. Další informace najdete na [stránce s cenami Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Úvod

Poté, co jste vytvořili jeden nebo více trezorů klíčů, budete pravděpodobně chtít monitorovat kdo, jak a kdy k vašim trezorům klíčů přistupuje. Toho docílíte povolením protokolování pro Key Vault, které ukládá informace v zadaném účtu úložiště Azure. Nový kontejner s názvem **insights-logs-auditevent** je automaticky vytvořený pro zadaný účet úložiště a ten samý účet úložiště můžete použít pro shromažďování protokolů více trezorů klíčů.

Informace o protokolování jsou přístupné do 10 minut od proběhnutí operace trezoru klíčů. Ve většině případů to bude rychlejší.  Správa protokolů v účtu úložiště je pouze na vás:

* Zabezpečte protokoly pomocí standardních metod řízení přístupu Azure a určete, kdo k nim má přístup.
* Odstraňte protokoly, které už nechcete uchovávat v účtu úložiště.

Tento kurz vám pomůže začít s protokolováním v Azure Key Vault, vytvořením vlastního účtu úložiště, povolením protokolování a interpretací shromážděných informací.  

> [!NOTE]
> Tento kurz neobsahuje pokyny k vytvoření trezorů klíčů, klíčů nebo tajných klíčů. Další informace naleznete v tématu [Začínáme s Azure Key Vault](key-vault-get-started.md) Pokyny pro rozhraní příkazového řádku pro různé platformy naleznete v [tomto ekvivalentním kurzu](key-vault-manage-with-cli2.md).
>
> V současné době nelze Azure Key Vault konfigurovat na portálu Azure. Místo toho použijte tyto pokyny pro Azure PowerShell.
>
>

Souhrnné informace o Azure Key Vault naleznete v tématu [Co je Azure Key Vault?](key-vault-whatis.md).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete:

* Existující trezor klíčů, který již používáte.  
* Azure PowerShell v **minimální verzi 1.0.1**. Chcete-li nainstalovat Azure PowerShell a přidružit ho ke svému předplatnému Azure, prohlédněte si téma [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). Pokud jste již Azure PowerShell nainstalovali, ale neznáte jeho verzi, z konzoly Azure PowerShell zadejte `(Get-Module azure -ListAvailable).Version`.  
* Dostatečné úložiště v Azure pro vaše protokoly Key Vault.

## <a id="connect"></a>Připojení k předplatným

Spusťte relaci Azure PowerShellu a přihlaste se k účtu Azure pomocí následujícího příkazu:  

```PowerShell
Connect-AzureRmAccount
```

V automaticky otevřeném okně prohlížeče zadejte svoje uživatelské jméno a heslo k účtu Azure. Azure PowerShell získá všechna předplatná přidružená k tomuto účtu a ve výchozím nastavení použije první předplatné.

Máte-li více předplatných, možná budete muset zadat předplatné, které jste použili pro vytvoření Azure Key Vault. Chcete-li zobrazit předplatná vašeho účtu, zadejte následující:

```PowerShell
    Get-AzureRmSubscription
```

Chcete-li specifikovat předplatné přidružené k trezoru klíčů, který budete protokolovat, zadejte:

```PowerShell
Set-AzureRmContext -SubscriptionId <subscription ID>
```

> [!NOTE]
> Jedná se důležitý a obzvláště užitečný krok, pokud máte víc předplatných přidružených vašemu účtu. Pokud se tento krok přeskočí, můžete v registru Microsoft.Insights obdržet chybu.
>   
>

Další informace o konfiguraci Azure PowerShellu naleznete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

## <a id="storage"></a>Vytvoření nového účtu úložiště pro protokoly

Pro svoje protokoly můžete použít i stávající účet úložiště, my však vytvoříme nový účet úložiště, který bude vyhrazený pro protokoly Key Vault. Abychom si usnadnili práci při pozdějším zadávání, uložíme si podrobnosti do proměnné s názvem **sa**.

Pro další usnadnění správy použijeme také stejnou skupinu prostředků, jako ta, která obsahuje náš trezor klíčů. Z [kurzu Začínáme](key-vault-get-started.md) je název této skupiny prostředků **ContosoResourceGroup** a budeme pokračovat v používání umístění ve východní Asii. Tyto hodnoty nahraďte příslušnými vlastními hodnotami:

```PowerShell
 $sa = New-AzureRmStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Rozhodnete-li se použít stávající účet úložiště, pak musí používat stejné předplatné jako váš trezor klíčů, a musí místo klasického modelu nasazení používat model nasazení Resource Manageru.
>
>

## <a id="identify"></a>Určení trezoru klíčů pro protokoly

V našem kurzu Začínáme byl název trezoru klíčů **ContosoKeyVault**, budeme tedy pokračovat v používání tohoto názvu a podrobnosti uložíme do proměnné s názvem **kv**:

```PowerShell
$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Povolení protokolování

K povolení protokolování pro Key Vault použijeme rutinu Set-AzureRmDiagnosticSetting spolu s proměnnými, které jsme vytvořili pro nový účet úložiště a nový trezor zámků. Také nastavíme **-povoleno** příznak **$true** a kategorii nastavíme na AuditEvent (jediná kategorie pro protokolování v Key Vault):

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent
```

Výstup mimo jiné obsahuje:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

To potvrzuje, že je protokolování pro váš trezor klíčů nyní povolené a že ukládá informace do účtu úložiště.

Volitelně můžete pro své protokoly nastavit také zásady uchovávání informací tak, aby se starší protokoly automaticky odstraňovaly. Například po nastavení zásad uchovávání informací pomocí příznaku **-RetentionEnabled** nastaveného na **$true** a parametru **-RetentionInDays** nastaveného na **90** budou protokoly starší než 90 dnů automaticky odstraněny.

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Categories AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Co je protokolováno:

* Protokolují se všechny ověřené požadavky REST API, což zahrnuje i neúspěšné požadavky v důsledku neoprávněného přístupu, systémových chyb nebo chybných požadavků.
* Operace nad samotným trezorem klíčů – to zahrnuje vytváření, odstraňování, nastavování zásad přístupu k trezoru klíčů a aktualizaci atributů trezoru klíčů, jako třeba značek.
* Operace nad klíči a nd tajnými klíči v trezoru klíčů – to zahrnuje vytváření, upravování a odstraňování těchto klíčů nebo tajných klíčů, operace jako jsou podepsání, ověření, šifrování, dešifrování, zabalení a rozbalení klíčů, získání tajného klíče, výpis klíčů a tajných klíčů i s jejich verzemi.
* Neověřené požadavky, které skončí odpovědí 401 – Neoprávněno. Například požadavky, které nemají nosný token, jsou poškozené nebo jejichž platnost vypršela, nebo mají neplatný token.  

## <a id="access"></a>Přístup k protokolům

Protokoly trezoru klíčů se ukládají do kontejneru **insights-logs-auditevent** v poskytnutém účtu úložiště. Chcete-li vypsat všechny objekty blob v tomto kontejneru, zadejte:

Nejprve vytvořte proměnnou pro název kontejneru. Ten se použije po celý zbytek návodu.

```PowerShell
$container = 'insights-logs-auditevent'
```

Chcete-li vypsat všechny objekty blob v tomto kontejneru, zadejte:

```PowerShell
Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Výstup bude vypadat nějak takto:

**Identifikátor URI kontejneru: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent**

**Název**

- - -
**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json**

**resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json**\*\*

Jak je vidět na výpisu, objekty blob se řídí zásadou vytváření názvů: **resourceId =<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json**

Hodnoty data a času používají UTC.

Protože se dají používat stejný účet úložiště pro shromažďování protokolů u několika prostředků, je vhodné získat nebo stáhnout jenom objekty BLOB, které je třeba je úplné ID prostředku v názvu objektu blob. Ale předtím se podíváme na to, jak stáhnout všechny objekty blob.

Nejprve vytvořte složku, kam stáhnete objekty blob. Příklad:

```PowerShell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Poté získejte seznam všech objektů blob:  

```PowerShell
$blobs = Get-AzureStorageBlob -Container $container -Context $sa.Context
```

Předejte tento seznam prostřednictvím „GetAzureStorageBlobContent“ pro stažení objektů blob do naší cílové složky:

```PowerShell
$blobs | Get-AzureStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

Když spustíte tento druhý příkaz, oddělovač **/** v názvech objektů blob vytvoří v cílové složce úplnou strukturu složek, a tato struktura bude sloužit ke stažení a uložení objektů blob ve formě souborů.

Chcete-li stahovat objekty blob selektivně, použijte zástupné znaky. Příklad:

* Máte-li více trezorů klíčů a chcete stáhnout pouze protokoly pro jeden trezor klíčů s názvem CONTOSOKEYVAULT3:

```PowerShell
Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
```

* Máte-li více skupin prostředků a chcete stáhnout pouze protokoly pro jednu skupinu prostředků, použijte `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

```PowerShell
Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
```

* Chcete-li stáhnout všechny protokoly pro měsíc leden 2016, použijte `-Blob '*/year=2016/m=01/*'`:

```PowerShell
Get-AzureStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
```

Nyní jste připraveni podívat se, co je v protokolech. Ale ještě předtím si ukážeme další dva parametry Get-AzureRmDiagnosticSetting, které byste mohli potřebovat:

* Dotaz na stav nastavení diagnostiky pro prostředek trezoru klíčů provedete pomocí:`Get-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId`
* Zakázat protokolování pro prostředek trezoru klíčů můžete pomocí:`Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Categories AuditEvent`

## <a id="interpret"></a>Interpretace protokolů služby Key Vault

Jednotlivé objekty blob jsou uloženy jako text ve formátu JSON blob. Spuštěno

```PowerShell
Get-AzureRmKeyVault -VaultName 'contosokeyvault'`
```

Vrátí položku protokolu podobná následující:

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

Následující tabulka obsahuje seznam názvů polí a popisy.

| Název pole | Popis |
| --- | --- |
| time |Datum a čas (UTC). |
| resourceId |ID prostředku Azure Resource Manageru U protokolů v Key Vault je to vždy ID prostředku Key Vault. |
| operationName |Název operace, jak popisuje následující tabulka. |
| operationVersion |Toto je verze REST API požadovaná klientem. |
| category |Pro účely protokolů Key Vault je jedinou dostupnou hodnotou AuditEvent. |
| resultType |Výsledek požadavku REST API. |
| resultSignature |Stav HTTP. |
| resultDescription |Další popis výsledku, je-li k dispozici. |
| durationMs |Doba trvání obsloužení požadavku REST API v milisekundách. Nezahrnuje latenci sítě, takže čas naměřený na straně klienta se může lišit. |
| callerIpAddress |IP adresa klienta, který vytvořil požadavek. |
| correlationId |Volitelný GUID, který může klient předat pro korelaci protokolů na straně klienta s protokoly na straně služby (Key Vault). |
| identity |Identita z tokenu, který byl předložený při provádění požadavku REST API. Obvykle to je položka „user“, „service principal“ nebo kombinace „user+appId“ jako v případě požadavku pocházejícího z rutiny Azure PowerShellu. |
| properties |Toto pole bude obsahovat různé informace v závislosti na operaci (operationName). Ve většině případů obsahuje informace o klientovi (řetězec uživatelského agenta předaný klientem), přesný identifikátor URI požadavku REST API a stavový kód protokolu HTTP. Navíc, pokud je jako výsledek požadavku (například KeyCreate nebo VaultGet) vrácen objekt, bude obsahovat také identifikátor URI klíče (jako „id“), identifikátor URI trezoru nebo identifikátor URI tajného klíče. |

Hodnoty pole **operationName** jsou ve formátu ObjectVerb. Příklad:

* Všechny operace nad trezory klíčů jsou ve formátu „Vault`<action>`“, jako například `VaultGet` nebo `VaultCreate`.
* Všechny operace nad klíči jsou ve formátu „Key`<action>`“, jako například `KeySign` nebo `KeyList`.
* Všechny operace nad tajnými klíči jsou ve formátu „Secret`<action>`“, jako například `SecretGet` nebo `SecretListVersions`.

Následující tabulka obsahuje seznam operací (operationName) a odpovídajících příkazů REST API.

| operationName | Příkaz REST API |
| --- | --- |
| Authentication |Přes koncový bod služby Azure Active Directory |
| VaultGet |[Získání informací o trezoru klíčů](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| VaultPut |[Vytvoření nebo aktualizace trezoru klíčů](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| VaultDelete |[Odstranění trezoru klíčů](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| VaultPatch |[Aktualizace trezoru klíčů](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| VaultList |[Výpis všech trezorů klíčů ve skupině prostředků](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| KeyCreate |[Vytvoření klíče](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| KeyGet |[Získání informací o klíči](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| KeyImport |[Import klíče do trezoru](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| KeyBackup |[Zálohování klíče](https://msdn.microsoft.com/library/azure/dn878058.aspx). |
| KeyDelete |[Odstranění klíče](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| KeyRestore |[Obnovení klíče](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| KeySign |[Podpis klíčem](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| KeyVerify |[Ověření pomocí klíče](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| KeyWrap |[Zabalení klíče](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| KeyUnwrap |[Rozbalení klíče](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| KeyEncrypt |[Šifrování pomocí klíče](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| KeyDecrypt |[Dešifrování pomocí klíče](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| KeyUpdate |[Aktualizace klíče](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| KeyList |[Výpis klíčů v trezoru](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| KeyListVersions |[Výpis verzí klíče](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| SecretSet |[Vytvoření tajného kódu](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| SecretGet |[Získání tajného kódu](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| SecretUpdate |[Aktualizace tajného kódu](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| SecretDelete |[Odstranění tajného kódu](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| SecretList |[Výpis tajných kódů v trezoru](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| SecretListVersions |[Výpis verzí tajného kódu](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Použití Log Analytics

Řešení Azure Key Vault v Log Analytics můžete využít ke kontrole protokolů AuditEvent služby Azure Key Vault. Další informace včetně nastavení najdete v tématu [Řešení Azure Key Vault v Log Analytics](../azure-monitor/insights/azure-key-vault.md). Tento článek také obsahuje pokyny, pokud je potřeba migrovat z původního řešení Key Vault, které se nabízelo pro Log Analytics ve verzi Preview, kdy jste protokoly nejdřív směrovaly do účtu Azure Storage a potom nakonfigurovali službu Log Analytics, aby je odtud četla.

## <a id="next"></a>Další kroky

Chcete-li používat Azure Key Vault ve webové aplikaci, podívejte se na kurz [Použití Azure Key Vault z webové aplikace](key-vault-use-from-web-application.md).

Programátorské reference najdete v [příručce pro vývojáře Azure Key Vault](key-vault-developers-guide.md).

Seznam rutin Azure PowerShellu 1.0 pro Azure Key Vault naleznete v tématu [Rutiny Azure Key Vault](/powershell/module/azurerm.keyvault/#key_vault).

Kurz k rotaci klíčů a auditování protokolu pomocí služby Azure Key Vault najdete v článku [Jak nastavit Key Vault s komplexní rotací klíčů a auditováním](key-vault-key-rotation-log-monitoring.md).
