---
title: Řešení Azure Key Vault ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Řešení Azure Key Vault v Azure Monitoru můžete použít ke kontrole protokolů azure trezoru klíčů.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/27/2019
ms.openlocfilehash: 7a2becf8cb43568383c324bb9f4f5b2e7b844268
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77667139"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Řešení Azure Key Vault Analytics v Azure Monitoru

![Symbol trezoru klíčů](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pomocí řešení Azure Key Vault v Azure Monitoru můžete zkontrolovat protokoly AuditEvent protokolu Azure Key Vault.

Chcete-li použít řešení, musíte povolit protokolování diagnostiky Azure Key Vault a nasměrovat diagnostiku do pracovního prostoru Log Analytics. Není nutné zapisovat protokoly do úložiště objektů blob Azure.

> [!NOTE]
> V lednu 2017 se změnil podporovaný způsob odesílání protokolů z trezoru klíčů do služby Log Analytics. Pokud se v názvu zobrazuje řešení trezoru klíčů *(zastaralé),* podívejte se na [migraci ze starého řešení trezoru klíčů,](#migrating-from-the-old-key-vault-solution) kde najdete kroky, které je třeba provést.
>
>

## <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
K instalaci a konfiguraci řešení Azure Key Vault použijte následující pokyny:

1. Pomocí procesu popsaného v [tématu Přidání řešení Azure Monitor z Galerie řešení](../../azure-monitor/insights/solutions.md) přidejte řešení Azure Key Vault do pracovního prostoru Log Analytics.
2. Povolení protokolování diagnostiky prostředků trezoru klíčů ke sledování pomocí [portálu](#enable-key-vault-diagnostics-in-the-portal) nebo [prostředí PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Povolení diagnostiky trezoru klíčů na portálu

1. Na webu Azure Portal přejděte na prostředek trezoru klíčů a monitorujte
2. Výběrem *možnosti Nastavení diagnostiky* otevřete následující stránku.

   ![Obrázek dlaždice Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Kliknutím *na Zapnout diagnostiku* otevřete následující stránku.

   ![Obrázek dlaždice Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Pojmenujte diagnostické nastavení.
5. Klikněte na zaškrtávací políčko *Odeslat do analýzy protokolů.*
6. Vyberte existující pracovní prostor Log Analytics nebo vytvořte pracovní prostor
7. Chcete-li povolit protokoly *auditeventu,* zaškrtněte políčko Protokolovat.
8. Kliknutím na *Uložit* povolíte protokolování diagnostiky do pracovního prostoru Log Analytics.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Povolení diagnostiky trezoru klíčů pomocí Prostředí PowerShell
Následující skript prostředí PowerShell poskytuje příklad `Set-AzDiagnosticSetting` použití k povolení protokolování prostředků pro trezor klíčů:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Projděte si podrobnosti o shromažďování dat služby Azure Key Vault
Řešení Azure Key Vault shromažďuje protokoly diagnostiky přímo z trezoru klíčů.
Není nutné zapisovat protokoly do úložiště objektů blob Azure a pro shromažďování dat není vyžadován žádný agent.

V následující tabulce jsou uvedeny metody shromažďování dat a další podrobnosti o tom, jak se shromažďují data pro Azure Key Vault.

| Platforma | Přímý agent | Agent operations manageru systémového centra | Azure | Vyžaduje se provozní manažer? | Data agenta Operations Manager odeslaná prostřednictvím skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | při příjezdu |

## <a name="use-azure-key-vault"></a>Použití Azure Key Vault
Po [instalaci řešení](https://azuremarketplace.microsoft.com/en-usrketplace/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview)zobrazte data trezoru klíčů kliknutím na dlaždici **Key Vault Analytics** na stránce **Přehled** sledování Azure. Otevřete tuto stránku z nabídky **Azure Monitor** kliknutím na **Další** v části **Přehledy.** 

![Obrázek dlaždice Azure Key Vault](media/azure-key-vault/log-analytics-keyvault-tile.png)

Po kliknutí na dlaždici **Key Vault Analytics** můžete zobrazit souhrny protokolů a potom přejít k podrobnostem pro následující kategorie:

* Objem všech operací trezoru klíčů v průběhu času
* Svazky operací se nezdařilo v průběhu času
* Průměrná provozní latence podle provozu
* Kvalita služeb pro operace s počtem operací, které trvá déle než 1000 ms a seznam operací, které trvá déle než 1000 ms

![obrázek řídicího panelu Azure Key Vault](media/azure-key-vault/log-analytics-keyvault01.png)

![obrázek řídicího panelu Azure Key Vault](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Zobrazení podrobností pro jakoukoli operaci
1. Na stránce **Přehled** klikněte na dlaždici **Key Vault Analytics.**
2. Na řídicím panelu **Azure Key Vault** zkontrolujte souhrnné informace v jednom z listů a potom klikněte na jeden pro zobrazení podrobných informací o tom na stránce hledání protokolu.

    Na libovolné stránce hledání protokolu můžete zobrazit výsledky podle času, podrobných výsledků a historie hledání protokolu. Můžete také filtrovat podle omezujících vlastností a zúžit výsledky.

## <a name="azure-monitor-log-records"></a>Záznamy protokolu Azure Monitor
Řešení Azure Key Vault analyzuje záznamy, které mají typ **KeyVaults,** které jsou shromažďovány z [protokolů AuditEvent](../../key-vault/key-vault-logging.md) v Diagnostice Azure.  Vlastnosti těchto záznamů jsou uvedeny v následující tabulce:  

| Vlastnost | Popis |
|:--- |:--- |
| `Type` |*AzureDiagnostics* |
| `SourceSystem` |*Azure* |
| `CallerIpAddress` |IP adresa klienta, který podal žádost |
| `Category` | *Událost auditu* |
| `CorrelationId` |Volitelný GUID, který může klient předat pro korelaci protokolů na straně klienta s protokoly na straně služby (Key Vault). |
| `DurationMs` |Doba trvání obsloužení požadavku REST API v milisekundách. Tato doba nezahrnuje latenci sítě, takže čas, který měříte na straně klienta, nemusí odpovídat tentokrát. |
| `httpStatusCode_d` |Stavový kód HTTP vrácený požadavkem (například *200*) |
| `id_s` |Jedinečné ID žádosti |
| `identity_claim_appid_g` | IDENTIFIKÁTOR GUID pro ID aplikace |
| `OperationName` |Název operace, jak je zdokumentováno v [protokolování trezoru klíčů Azure](../../key-vault/key-vault-logging.md) |
| `OperationVersion` |Verze REST API požadovaná klientem (například *2015-06-01*) |
| `requestUri_s` |Uri žádosti |
| `Resource` |Název trezoru klíčů |
| `ResourceGroup` |Skupina prostředků trezoru klíčů |
| `ResourceId` |ID prostředku Azure Resource Manageru U protokolů trezoru klíčů se jedná o ID prostředku trezoru klíčů. |
| `ResourceProvider` |*Microsoft. TREZOR KLÍČŮ* |
| `ResourceType` | *Klenby* |
| `ResultSignature` |Stav HTTP (například *OK)* |
| `ResultType` |Výsledek požadavku REST API (například *Úspěch)* |
| `SubscriptionId` |ID předplatného Azure obsahující trezor klíčů |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrace ze starého řešení trezoru klíčů
V lednu 2017 se změnil podporovaný způsob odesílání protokolů z trezoru klíčů do služby Log Analytics. Tyto změny poskytují následující výhody:
+ Protokoly jsou zapsány přímo do pracovního prostoru Log Analytics bez nutnosti používat účet úložiště
+ Menší latence od okamžiku, kdy jsou generovány protokoly k nim jsou k dispozici v Log Analytics
+ Méně kroků konfigurace
+ Běžný formát pro všechny typy diagnostiky Azure

Použití aktualizovaného řešení:

1. [Konfigurace diagnostiky, která má být odeslána přímo do pracovního prostoru Analýzy protokolů z trezoru klíčů](#enable-key-vault-diagnostics-in-the-portal)  
2. Povolení řešení Azure Key Vault pomocí procesu popsaného v [tématu Přidání řešení Azure Monitor u Galerie řešení](../../azure-monitor/insights/solutions.md)
3. Aktualizace všech uložených dotazů, řídicích panelů nebo výstrah za účelem použití nového datového typu
   + Typ je změna z: KeyVaults na AzureDiagnostics. Prostředek ResourceType můžete použít k filtrování do protokolů trezoru klíčů.
   + Místo: `KeyVaults`, použijte`AzureDiagnostics | where ResourceType'=="VAULTS"`
   + Pole: (Názvy polí se ujímají velkých a malých písmen)
   + Pro každé pole, které má \_příponu \_ \_s, d nebo g v názvu, změňte první znak na malá písmena
   + Pro každé pole, které má \_příponu o v názvu, jsou data rozdělena do jednotlivých polí na základě názvů vnořených polí. Například hlavní upn volajícího je uložen v poli`identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   + Pole CallerIpAddress změněno na CallerIPAddress
   + Pole RemoteIPZemě již není k dispozici
4. Odeberte řešení *Key Vault Analytics (Zastaralé).* Pokud používáte PowerShell, použijte`Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Data shromážděná před změnou nejsou v novém řešení viditelná. Můžete pokračovat v dotazování na tato data pomocí starých názvů type a polí.

## <a name="troubleshooting"></a>Řešení potíží
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Další kroky
* Pomocí [dotazů protokolu v Azure Monitoru](../../azure-monitor/log-query/log-query-overview.md) zobrazte podrobná data služby Azure Key Vault.
