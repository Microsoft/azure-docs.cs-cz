---
title: Azure Key Vault řešení v Azure Monitor | Microsoft Docs
description: K revizi protokolů Azure Key Vault můžete použít řešení Azure Key Vault v Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/27/2019
ms.openlocfilehash: f6d8929c8fd59836ff297f226851890892c10acc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91445134"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Řešení Azure Key Vault Analytics v Azure Monitor

> [!NOTE]
> Toto řešení je zastaralé. [Pro Key Vault teď doporučujeme použít Azure monitor](./key-vault-insights-overview.md).

![Symbol Key Vault](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

K revizi Azure Key Vault protokolů AuditEvent můžete použít řešení Azure Key Vault v Azure Monitor.

Abyste mohli řešení používat, musíte povolit protokolování diagnostiky Azure Key Vault a nasměrovat diagnostiku do pracovního prostoru Log Analytics. Není nutné zapisovat protokoly do úložiště objektů BLOB v Azure.

> [!NOTE]
> V lednu 2017 se podporuje možnost posílání protokolů z Key Vault do Log Analytics změnit. Pokud Key Vault řešení, které používáte, zobrazuje *(zastaralé)* v názvu, přečtěte si téma [migrace z původního Key Vault řešení](#migrating-from-the-old-key-vault-solution) kroků, které je třeba provést.
>
>

## <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
Pro instalaci a konfiguraci řešení Azure Key Vault použijte následující pokyny:

1. Pomocí postupu popsaného v tématu [přidání Azure monitor řešení z galerie řešení](./solutions.md) přidejte řešení Azure Key Vault do pracovního prostoru Log Analytics.
2. Povolení protokolování diagnostiky pro prostředky Key Vault ke sledování pomocí [portálu](#enable-key-vault-diagnostics-in-the-portal) nebo [PowerShellu](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Povolení diagnostiky Key Vault na portálu

1. V Azure Portal přejděte na prostředek Key Vault, který chcete monitorovat.
2. Výběrem *Možnosti nastavení diagnostiky* otevřete následující stránku.

   ![Snímek obrazovky se stránkou nastavení diagnostiky pro prostředek Key Vault ContosoKVSCUS. možnost zapnutí diagnostiky je zvýrazněna.](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Kliknutím na *zapnout diagnostiku* otevřete následující stránku.

   ![Snímek obrazovky stránky pro konfiguraci nastavení diagnostiky Jsou vybrány možnosti pro odeslání do Log Analytics, protokol AuditEvent a AllMetrics.](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Zadejte název nastavení diagnostiky.
5. Kliknutím na zaškrtávací políčko pro *odeslání Log Analytics*
6. Vyberte existující pracovní prostor Log Analytics nebo vytvořte pracovní prostor.
7. Pokud chcete povolit protokoly *AuditEvent* , klikněte na zaškrtávací políčko v části protokol.
8. Kliknutím na *Save (Uložit* ) Povolte protokolování diagnostiky do Log Analytics pracovního prostoru.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Povolení diagnostiky Key Vault pomocí prostředí PowerShell
Následující skript PowerShellu poskytuje příklad, jak použít `Set-AzDiagnosticSetting` k povolení protokolování prostředků pro Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Kontrola Azure Key Vault podrobností shromažďování dat
Azure Key Vault řešení shromažďuje protokoly diagnostiky přímo z Key Vault.
Není nutné zapisovat protokoly do úložiště objektů BLOB v Azure a pro shromažďování dat není nutné žádného agenta.

V následující tabulce jsou uvedeny metody shromažďování dat a další podrobnosti o tom, jak se data shromažďují pro Azure Key Vault.

| Platforma | Přímý Agent | Agent Operations Manager pro Systems Center | Azure | Operations Manager požadováno? | Data agenta Operations Manager odeslána prostřednictvím skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | při doručení |

## <a name="use-azure-key-vault"></a>Použití Azure Key Vault
Po [instalaci řešení](https://azuremarketplace.microsoft.com/en-usrketplace/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview)zobrazte data Key Vault kliknutím na dlaždici **Key Vault Analytics** na stránce **Přehled** Azure monitor. Tuto stránku otevřete z nabídky **Azure monitor** kliknutím na **Další** v části **přehledy** . 

![Snímek obrazovky Key Vault Analytics dlaždice na stránce Přehled Azure Monitor znázorňující graf objemu operací trezoru klíčů v průběhu času.](media/azure-key-vault/log-analytics-keyvault-tile.png)

Po kliknutí na dlaždici **Key Vault Analytics** můžete zobrazit souhrny protokolů a pak přejít k podrobnostem v následujících kategoriích:

* Objem všech operací trezoru klíčů v průběhu času
* Neúspěšné operace se svazky v průběhu času
* Průměrná provozní latence podle operace
* Kvalita služeb pro operace s počtem operací, které přebírají více než 1000 MS, a seznam operací, které mají více než 1000 MS

![Snímek obrazovky řídicího panelu Azure Key Vault znázorňující dlaždice s grafickými daty pro všechny operace, neúspěšné operace a průměrná provozní latence.](media/azure-key-vault/log-analytics-keyvault01.png)

![Snímek obrazovky řídicího panelu Azure Key Vault znázorňující dlaždice s daty pro průměrnou provozní latenci, kvalitu služby a doporučená hledání.](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Zobrazení podrobností o všech operacích
1. Na stránce **Přehled** klikněte na dlaždici **Key Vault Analytics** .
2. Na řídicím panelu **Azure Key Vault** zkontrolujte souhrnné informace v jednom z oken a potom kliknutím na jednu Zobrazte podrobné informace o této stránce na stránce prohledávání protokolu.

    Na kterékoli stránce pro prohledávání protokolu můžete zobrazit výsledky podle času, podrobných výsledků a historie prohledávání protokolu. K zúžení výsledků můžete také filtrovat podle omezujících vlastností.

## <a name="azure-monitor-log-records"></a>Záznamy protokolu Azure Monitor
Azure Key Vault řešení analyzuje záznamy, které mají typ **trezorů** klíčů, které jsou shromažďovány z [protokolů AuditEvent](../../key-vault/general/logging.md) v Azure Diagnostics.  Vlastnosti těchto záznamů jsou uvedené v následující tabulce:  

| Vlastnost | Popis |
|:--- |:--- |
| `Type` |*AzureDiagnostics* |
| `SourceSystem` |*Azure* |
| `CallerIpAddress` |IP adresa klienta, který odeslal požadavek |
| `Category` | *AuditEvent* |
| `CorrelationId` |Volitelný GUID, který může klient předat pro korelaci protokolů na straně klienta s protokoly na straně služby (Key Vault). |
| `DurationMs` |Doba trvání obsloužení požadavku REST API v milisekundách. Tato doba nezahrnuje latenci sítě, takže čas, který měříte na straně klienta, se nemusí shodovat s časem. |
| `httpStatusCode_d` |Stavový kód HTTP vrácený požadavkem (například *200*) |
| `id_s` |Jedinečné ID žádosti |
| `identity_claim_appid_g` | Identifikátor GUID pro ID aplikace |
| `OperationName` |Název operace, jak je popsáno v [Azure Key Vault protokolování](../../key-vault/general/logging.md) |
| `OperationVersion` |Verze REST API požadovaná klientem (například *2015-06-01*) |
| `requestUri_s` |Identifikátor URI žádosti |
| `Resource` |Název trezoru klíčů |
| `ResourceGroup` |Skupina prostředků trezoru klíčů |
| `ResourceId` |ID prostředku Azure Resource Manageru V případě protokolů Key Vault se jedná o Key Vault ID prostředku. |
| `ResourceProvider` |*Microsoft. KEYVAULT* |
| `ResourceType` | *TREZORY* |
| `ResultSignature` |Stav HTTP (například *OK*) |
| `ResultType` |Výsledek žádosti o REST API (například *úspěch*) |
| `SubscriptionId` |ID předplatného Azure s předplatným, které obsahuje Key Vault |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrace ze starého řešení Key Vault
V lednu 2017 se podporuje možnost posílání protokolů z Key Vault do Log Analytics změnit. Tyto změny poskytují následující výhody:
+ Protokoly se zapisují přímo do Log Analyticsho pracovního prostoru bez nutnosti používat účet úložiště.
+ Menší latence od času, kdy jsou do nich generovány protokoly, které jsou k dispozici v Log Analytics
+ Méně kroků konfigurace
+ Společný formát pro všechny typy diagnostiky Azure

Použití aktualizovaného řešení:

1. [Konfigurace diagnostiky, která se pošle přímo do Log Analytics pracovního prostoru z Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Povolte řešení Azure Key Vault pomocí procesu popsaného v tématu [přidání Azure monitor řešení z galerie řešení](./solutions.md)
3. Aktualizovat všechny uložené dotazy, řídicí panely nebo výstrahy, aby používaly nový datový typ
   + Typ se změní z: trezory klíčů na AzureDiagnostics. Pomocí prostředku ResourceType můžete filtrovat Key Vault protokolů.
   + Místo: `KeyVaults` , použijte `AzureDiagnostics | where ResourceType'=="VAULTS"`
   + Pole: (názvy polí rozlišují malá a velká písmena)
   + Pro každé pole, které má \_ v názvu příponu s, \_ d nebo \_ g, změňte první znak na malá písmena.
   + Pro každé pole, které má příponu \_ o v názvu, jsou data rozdělena do jednotlivých polí na základě názvů vnořených polí. Například hlavní název uživatele volajícího je uložen v poli. `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   + CallerIpAddress pole se změnila na CallerIPAddress.
   + Pole RemoteIPCountry už není k dispozici.
4. Odeberte řešení *Key Vault Analytics (nepoužívané)* . Pokud používáte PowerShell, použijte `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Data shromážděná před změnou nejsou v novém řešení viditelná. Můžete pokračovat v dotazování na tato data pomocí starého názvu typu a pole.

## <a name="troubleshooting"></a>Řešení potíží
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Další kroky
* Použijte [dotazy protokolu v Azure monitor](../log-query/log-query-overview.md) k zobrazení podrobných dat Azure Key Vault.

