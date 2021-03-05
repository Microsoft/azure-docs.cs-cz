---
title: Postup povolení protokolování Azure Key Vault
description: Jak povolit protokolování pro Azure Key Vault, které ukládá informace v účtu úložiště Azure, který poskytnete.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/01/2020
ms.author: mbaldwin
ms.openlocfilehash: 9ec1e59a5599ca2e95578eacc1484932956ebf16
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204010"
---
# <a name="how-to-enable-key-vault-logging"></a>Postup povolení protokolování Key Vault

Po vytvoření jednoho nebo více trezorů klíčů budete pravděpodobně chtít sledovat, jak a kdy jsou k vašim trezorům klíčů přistupované a kým. Úplné informace o této funkci najdete v tématu [protokolování Key Vault](logging.md).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete:

* Existující trezor klíčů, který již používáte.  
* Prostředí [Azure Cloud Shell](https://shell.azure.com) – bash
* Dostatečné úložiště v Azure pro vaše protokoly Key Vault.

Tyto příkazy průvodce jsou formátované pro [Cloud Shell](https://shell.azure.com) s bash jako prostředí.

## <a name="connect-to-your-key-vault-subscription"></a>Připojení k předplatnému Key Vault

Prvním krokem při nastavení protokolování klíčů je připojení k předplatnému, které obsahuje váš Trezor klíčů. To je obzvláště důležité, pokud máte k vašemu účtu k dispozici několik předplatných.

Pomocí Azure CLI můžete zobrazit všechna Vaše předplatná pomocí příkazu [AZ Account list](/cli/azure/account#az_account_list) a pak se k němu připojit pomocí příkazu [AZ Account set](/cli/azure/account#az_account_set):

```azurecli-interactive
az account list

az account set --subscription "<subscriptionID>"
```

Pomocí Azure PowerShell můžete nejdřív vypsat své odběry pomocí rutiny [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?view=azps-4.7.0) a pak se k ní připojit pomocí rutiny [set-AzContext](/powershell/module/az.accounts/set-azcontext?view=azps-4.7.0) : 

```powershell-interactive
Get-AzSubscription

Set-AzContext -SubscriptionId "<subscriptionID>"
```

## <a name="create-a-storage-account-for-your-logs"></a>Vytvoření účtu úložiště pro vaše protokoly

I když pro vaše protokoly můžete použít existující účet úložiště, vytvoříme nový účet úložiště vyhrazený pro Key Vault protokoly. 

Pro další snadné řízení používáme také stejnou skupinu prostředků jako ta, která obsahuje Trezor klíčů. V rychlém startu [Azure PowerShell](quick-create-powershell.md)a v rychlém startu [Azure CLI](quick-create-cli.md) se tato skupina prostředků jmenuje **myResourceGroup** a umístění je *eastus*. Tyto hodnoty nahraďte vlastními, pokud je to možné. 

Bude také potřeba zadat název účtu úložiště. Názvy účtů úložiště musí být jedinečné, musí být dlouhé 3 až 24 znaků a obsahovat jenom číslice a malá písmena.  Nakonec vytvoříme účet úložiště pro SKU "Standard_LRS".

Pomocí Azure CLI použijte příkaz [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) .

```azurecli-interactive
az storage account create --name "<your-unique-storage-account-name>" -g "myResourceGroup" --sku "Standard_LRS"
```

Pomocí Azure PowerShell použijte rutinu [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount?view=azps-4.7.0) . Budete muset zadat umístění, které odpovídá skupině prostředků.

```powershell
 New-AzStorageAccount -ResourceGroupName myResourceGroup -Name "<your-unique-storage-account-name>" -Type "Standard_LRS" -Location "eastus"
```

V obou případech si poznamenejte "ID" účtu úložiště. Operace Azure CLI vrátí "ID" ve výstupu. Chcete-li získat "ID" s Azure PowerShell, použijte [příkaz Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount?view=azps-4.7.0) a přiřadíte výstup k proměnné $SA. Pak uvidíte účet úložiště s $sa. ID. ($Sa. Dále v tomto článku se bude používat i kontextová vlastnost.)

```powershell-interactive
$sa = Get-AzStorageAccount -Name "<your-unique-storage-account-name>" -ResourceGroup "myResourceGroup"
$sa.id
```

ID účtu úložiště bude ve formátu "/Subscriptions/<identifikátorem-ID předplatného>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/<název-účtu-úložiště>".

> [!NOTE]
> Pokud se rozhodnete použít existující účet úložiště, musí používat stejné předplatné jako váš Trezor klíčů a musí používat model nasazení Azure Resource Manager, nikoli model nasazení Classic.

## <a name="obtain-your-key-vault-resource-id"></a>Získání ID prostředku trezoru klíčů

V [rychlém startu CLI](quick-create-cli.md) a [rychlém startu PowerShellu](quick-create-powershell.md)jste vytvořili klíč s jedinečným názvem.  Znovu použijte tento název v následujících krocích.  Pokud si název vašeho trezoru klíčů nepamatujete, můžete k jejich vypsání použít příkaz Azure CLI [AZ Key trezor list](/cli/azure/keyvault#az_keyvault_list) nebo rutina Azure PowerShell [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0) .

K vyhledání ID prostředku použijte název trezoru klíčů.  Pomocí Azure CLI použijte příkaz [AZ klíčů trezor show](/cli/azure/keyvault#az_keyvault_show) .

```azurecli-interactive
az keyvault show --name "<your-unique-keyvault-name>"
```

Pomocí Azure PowerShell použijte rutinu [Get-AzKeyVault](/powershell/module/az.keyvault/get-azkeyvault?view=azps-4.7.0) .

```powershell-interactive
Get-AzKeyVault -VaultName "<your-unique-keyvault-name>"
```

ID prostředku vašeho trezoru klíčů bude ve formátu "/Subscriptions/<your-subscription-ID>/resourceGroups/myResourceGroup/providers/Microsoft.KeyVault/vaults/<je to jedinečné úložiště klíčů – název>". Poznamenejte si ho pro další krok.

## <a name="enable-logging-using-azure-powershell"></a>Povolit protokolování pomocí Azure PowerShell

Pokud chcete povolit protokolování pro Key Vault, použijeme příkaz Azure CLI [AZ monitor Diagnostic-Settings Create](/cli/azure/monitor/diagnostic-settings) nebo rutinu [set-AZDIAGNOSTICSETTING](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) spolu s ID účtu úložiště a ID prostředku trezoru klíčů.

```azurecli-interactive
az monitor diagnostic-settings create --storage-account "<storage-account-id>" --resource "<key-vault-resource-id>" --name "Key vault logs" --logs '[{"category": "AuditEvent","enabled": true}]' --metrics '[{"category": "AllMetrics","enabled": true}]'
```

V Azure PowerShell použijeme rutinu [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) s příznakem **-Enabled** nastaveným na **$true** a kategorií nastavenou na `AuditEvent` (jediná kategorie pro Key Vault protokolování):

```powershell-interactive
Set-AzDiagnosticSetting -ResourceId "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category "AuditEvent"
```

Volitelně můžete nastavit zásady uchovávání informací pro vaše protokoly, aby se starší protokoly automaticky odstranily po uplynutí určité doby. Například můžete nastavit zásady uchovávání informací, které automaticky odstraní protokoly starší než 90 dní.

<!-- With the Azure CLI, use the [az monitor diagnostic-settings update](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_update) command. 

```azurecli-interactive
az monitor diagnostic-settings update 
```
-->

Pomocí Azure PowerShell použijte rutinu [set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=azps-4.7.0) . 

```powershell-interactive
Set-AzDiagnosticSetting "<key-vault-resource-id>" -StorageAccountId $sa.id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Co je protokolováno:

* Všechny požadavky ověřených REST API, včetně neúspěšných žádostí v důsledku oprávnění přístupu, systémových chyb nebo chybných požadavků.
* Operace samotného trezoru klíčů, včetně vytvoření, odstranění, nastavení zásad přístupu trezoru klíčů a aktualizace atributů trezoru klíčů, například značek.
* Operace s klíči a tajnými klíči v trezoru klíčů, včetně:
  * Vytváření, úpravy nebo odstraňování těchto klíčů nebo tajných kódů.
  * Podepisování, ověřování, šifrování, dešifrování, zabalení a rozbalení klíčů, získávání tajných klíčů a zobrazování klíčů a tajných kódů (a jejich verzí).
* Neověřené požadavky, které skončí odpovědí 401 – Neoprávněno. Příklady jsou požadavky, které nemají nosný token, které jsou poškozené nebo jejichž platnost vypršela, nebo které mají neplatný token.  
* Události oznámení Event Grid pro blížící se vypršení platnosti a zásady přístupu do trezoru se změnily (událost nové verze se neprotokoluje). Události jsou protokolovány bez ohledu na to, zda je v trezoru klíčů vytvořeno předplatné událostí. Další informace najdete v tématu [Event Grid schéma událostí pro Key Vault](../../event-grid/event-schema-key-vault.md)

## <a name="access-your-logs"></a>Přístup k vašim protokolům

Protokoly Key Vault se ukládají do kontejneru "Insights-logs-auditevent" v účtu úložiště, který jste zadali. Chcete-li zobrazit protokoly, je nutné stáhnout objekty blob.

Nejprve vypíšete seznam všech objektů BLOB v kontejneru.  Pomocí Azure CLI použijte příkaz [AZ Storage BLOB list](/cli/azure/storage/blob#az_storage_blob_list) .

```azurecli-interactive
az storage blob list --account-name "<your-unique-storage-account-name>" --container-name "insights-logs-auditevent"
```

V Azure PowerShell použijte příkaz [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0) , který bude obsahovat všechny objekty BLOB v tomto kontejneru, a zadejte:

```powershell
Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context
```

Jak uvidíte z výstupu příkazu rozhraní příkazového řádku Azure CLI nebo rutiny Azure PowerShell, názvy objektů BLOB jsou ve formátu `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json` . Hodnoty data a času používají UTC.

Vzhledem k tomu, že ke shromažďování protokolů pro více prostředků můžete použít stejný účet úložiště, úplné ID prostředku v názvu objektu BLOB je užitečné pro přístup k objektům blob, které potřebujete, nebo ke stažení jenom. Ale předtím se podíváme na to, jak stáhnout všechny objekty blob.

Pomocí Azure CLI použijte příkaz [AZ Storage BLOB Download](/cli/azure/storage/blob#az_storage_blob_download) , předejte mu názvy objektů BLOB a cestu k souboru, do kterého chcete výsledky uložit.

```azurecli-interactive
az storage blob download --container-name "insights-logs-auditevent" --file <path-to-file> --name "<blob-name>" --account-name "<your-unique-storage-account-name>"
```

Pomocí Azure PowerShell použijte k získání seznamu objektů BLOB rutinu [gt-AzStorageBlobs](/powershell/module/az.storage/get-azstorageblob?view=azps-4.7.0) a potom pomocí kanálu rutinu [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent?view=azps-4.7.0) Stáhněte protokoly do zvolené cesty.

```powershell-interactive
$blobs = Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context | Get-AzStorageBlobContent -Destination "<path-to-file>"
```

Při spuštění této druhé rutiny v prostředí PowerShell **/** vytvoří oddělovač v názvech objektů BLOB úplnou strukturu složek v cílové složce. Pomocí této struktury budete stahovat a ukládat objekty BLOB jako soubory.

Chcete-li stahovat objekty blob selektivně, použijte zástupné znaky. Například:

* Máte-li více trezorů klíčů a chcete stáhnout pouze protokoly pro jeden trezor klíčů s názvem CONTOSOKEYVAULT3:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Máte-li více skupin prostředků a chcete stáhnout pouze protokoly pro jednu skupinu prostředků, použijte `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Pokud chcete stáhnout všechny protokoly pro měsíc v lednu 2019, použijte `-Blob '*/year=2019/m=01/*'` :

  ```powershell
  Get-AzStorageBlob -Container "insights-logs-auditevent" -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Nyní jste připraveni podívat se, co je v protokolech. Ale předtím, než se k němu připravujeme, byste měli znát dva další příkazy:

Podrobnosti o tom, jak číst protokoly, najdete v tématu [Key Vault Logging: interpretace protokolů Key Vault](logging.md#interpret-your-key-vault-logs)

## <a name="use-azure-monitor-logs"></a>Použití protokolů Azure Monitoru

K revizi protokolů Key Vault můžete použít řešení Key Vault v protokolech Azure Monitor `AuditEvent` . V protokolech Azure Monitor použijete dotazy protokolu k analýze dat a získání informací, které potřebujete.

Další informace, včetně postupu nastavení, najdete [v tématu Azure Key Vault v Azure monitor](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Další kroky

- Koncepční informace, včetně toho, jak interpretovat protokoly Key Vault, najdete v tématu [Key Vault Logging](logging.md) .
- Kurz, který používá Azure Key Vault ve webové aplikaci .NET, najdete v tématu [použití Azure Key Vault z webové aplikace](tutorial-net-create-vault-azure-web-app.md).
- Programátorské reference najdete v [příručce pro vývojáře Azure Key Vault](developers-guide.md).
