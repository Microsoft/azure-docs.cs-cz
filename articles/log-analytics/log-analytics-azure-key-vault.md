---
title: Azure Key Vault řešení v Log Analytics | Microsoft Docs
description: Řešení Azure Key Vault v analýzy protokolů můžete použít ke kontrole protokoly Azure Key Vault.
services: log-analytics
documentationcenter: ''
author: richrundmsft
manager: jochan
editor: ''
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: richrund
ms.openlocfilehash: 9c4b16ec11d1990de687014c5385314f0e0c602a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
ms.locfileid: "30238388"
---
# <a name="azure-key-vault-analytics-solution-in-log-analytics"></a>Azure Key Vault Analytics řešení v analýzy protokolů

![Symbol Key Vault](./media/log-analytics-azure-keyvault/key-vault-analytics-symbol.png)

Řešení Azure Key Vault v Log Analytics můžete využít ke kontrole protokolů AuditEvent služby Azure Key Vault.

Pokud chcete používat řešení, musíte povolit protokolování diagnostiky Azure Key Vault a přímé diagnostiku do pracovního prostoru analýzy protokolů. Není nutné zapsat protokoly do úložiště objektů Blob v Azure.

> [!NOTE]
> V ledna 2017 podporované způsob odesílání protokolů z trezoru klíč k Log Analytics změnil. Je-li řešení Key Vault, kterou používáte ukazuje *(zastaralé)* v názvu se označují [migrace z původního řešení Key Vault](#migrating-from-the-old-key-vault-solution) muset postupovat podle kroků.
>
>

## <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
Použijte následující pokyny k instalaci a konfiguraci řešení Azure Key Vault:

1. Povolit řešení Azure Key Vault z [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview) nebo pomocí procesu popsaného v tématu [řešení přidat analýzy protokolů z Galerie řešení](log-analytics-add-solutions.md).
2. Povolit protokolování diagnostiky pro Key Vault prostředky. Pokud chcete monitorovat, buď pomocí [portál](#enable-key-vault-diagnostics-in-the-portal) nebo [prostředí PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Povolte diagnostiku Key Vault na portálu

1. Na portálu Azure přejděte k prostředku Key Vault k monitorování
2. Vyberte *protokolů diagnostiky* otevřete na následující stránce

   ![Obrázek dlaždice Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault-enable-diagnostics01.png)
3. Klikněte na tlačítko *zapněte diagnostiku* otevřete na následující stránce

   ![Obrázek dlaždice Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault-enable-diagnostics02.png)
4. Chcete-li zapněte diagnostiku, klikněte na tlačítko *na* pod *stav*
5. Klikněte na zaškrtávací políčko pro *poslat analýzy protokolů*
6. Vyberte existující pracovní prostor analýzy protokolů, nebo vytvořit pracovní prostor
7. Chcete-li povolit *AuditEvent* protokoly, klikněte na zaškrtávací políčko v protokolu
8. Klikněte na tlačítko *Uložit* povolení protokolování diagnostiky k analýze protokolů

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Povolit Key Vault diagnostiku pomocí prostředí PowerShell
Následující skript prostředí PowerShell představuje příklad, jak pomocí `Set-AzureRmDiagnosticSetting` povolení diagnostických protokolování pro Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Zkontrolujte podrobnosti kolekce dat Azure Key Vault
Řešení Azure Key Vault shromažďuje diagnostické protokoly přímo z trezoru klíčů.
Není nutné zapsat protokoly do úložiště objektů Blob v Azure a žádný agent je vyžadována pro shromažďování dat.

Následující tabulka uvádí metody shromažďování dat a další podrobnosti o tom, jak se údaje pro Azure Key Vault.

| Platforma | Přímé agenta | Agent systémy Center Operations Manager | Azure | Nástroj Operations Manager vyžaduje? | Dat agenta nástroje Operations Manager odeslána prostřednictvím skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | v případě přijetí |

## <a name="use-azure-key-vault"></a>Použití Azure Key Vault
Po můžete [nainstalovat řešení](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview), zobrazit data Key Vault kliknutím **Azure Key Vault** dlaždice z **přehled** stránky analýzy protokolů.

![Obrázek dlaždice Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault-tile.png)

Po kliknutí **přehled** dlaždici, můžete zobrazit souhrny souborů protokolu a pak přejdete podrobnostech v těchto kategoriích:

* Objem všechny operace trezoru klíčů v čase
* Se nezdařila operace svazky v čase
* Průměrná latence provozní operací
* Kvality služeb pro operace s počet operací, které trvat víc než 1000 ms a seznam operací, které trvat víc než 1000 ms

![Obrázek řídicí panel Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault01.png)

![Obrázek řídicí panel Azure Key Vault](./media/log-analytics-azure-keyvault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Chcete-li zobrazit podrobnosti pro všechny operace
1. Na **přehled** klikněte na tlačítko **Azure Key Vault** dlaždici.
2. Na **Azure Key Vault** řídicí panel, zkontrolujte souhrnné informace v jednom z okna a pak klikněte na jednu k zobrazení podrobných informací o něm na stránce vyhledávání protokolu.

    Na všech stránkách vyhledávání protokolu můžete zobrazit výsledky čas, podrobné výsledky a historii hledání protokolu. Můžete také filtrovat podle omezující vlastnosti výsledky upřesněte.

## <a name="log-analytics-records"></a>Záznamy služby Log Analytics
Řešení Azure Key Vault analyzuje záznamy, které mají typ **KeyVaults** shromážděná z [AuditEvent protokoly](../key-vault/key-vault-logging.md) v Azure Diagnostics.  Vlastnosti pro tyto záznamy jsou v následující tabulce:  

| Vlastnost | Popis |
|:--- |:--- |
| Typ |*AzureDiagnostics* |
| SourceSystem |*Azure* |
| CallerIpAddress |IP adresa klienta, který vytvořil požadavek |
| Kategorie | *AuditEvent* |
| CorrelationId |Volitelný GUID, který může klient předat pro korelaci protokolů na straně klienta s protokoly na straně služby (Key Vault). |
| DurationMs |Doba trvání obsloužení požadavku REST API v milisekundách. Tentokrát nezahrnuje latenci sítě, takže čas, který budete vyhodnocovat na straně klienta se může lišit. |
| httpStatusCode_d |Stavový kód HTTP vrácených požadavkem (například *200*) |
| id_s |Jedinečné ID požadavku |
| identity_claim_appid_g | Identifikátor GUID pro id aplikace |
| OperationName |Název operace, jak je uvedeno v [protokolování Azure Key Vault](../key-vault/key-vault-logging.md) |
| OperationVersion |Verze rozhraní REST API požadovaná klientem (například *2015-06-01*) |
| requestUri_s |Identifikátor URI požadavku |
| Prostředek |Název trezoru klíčů |
| ResourceGroup |Skupina prostředků služby key vault |
| ID prostředku |ID prostředku Azure Resource Manageru Pro protokoly Key Vault to je ID prostředku Key Vault. |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResourceType | *VAULTS* |
| ResultSignature |Stav protokolu HTTP (například *OK*) |
| ResultType |Výsledek požadavku REST API (například *úspěch*) |
| SubscriptionId |ID předplatného Azure předplatného obsahující Key Vault |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrace z původního řešení Key Vault
V ledna 2017 podporované způsob odesílání protokolů z trezoru klíč k Log Analytics změnil. Tyto změny poskytovat následující výhody:
+ Protokoly zapisují přímo k Log Analytics, aniž by bylo nutné použít účet úložiště
+ Menší latenci od času po vygenerování protokoly jim je k dispozici v analýzy protokolů
+ Méně kroků konfigurace
+ Běžný formát pro všechny typy Azure diagnostics

Chcete-li použít aktualizované řešení:

1. [Konfiguraci diagnostiky k odeslání přímo k Log Analytics z Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Povolení Azure Key Vault řešení pomocí procesu popsaného v tématu [řešení přidat analýzy protokolů z Galerie řešení](log-analytics-add-solutions.md)
3. Aktualizovat žádné uložené dotazy, řídicí panely nebo výstrahy používat nový datový typ.
  + Typ je změna z: KeyVaults k AzureDiagnostics. Příkaz ResourceType můžete filtrovat, aby protokoly Key Vault.
  - Místo: `KeyVaults`, použijte `AzureDiagnostics | where ResourceType'=="VAULTS"`
  + Pole: (názvy polí jsou malá a velká písmena)
  - Pro každé pole, které má příponu \_s, \_d, nebo \_g v názvu, změna po prvním znaku na malá písmena
  - Pro každé pole, které má příponu \_o název, data je rozdělená do jednotlivých polí na základě názvů vnořená pole. Například hlavní název uživatele volajícího je uložený v poli `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   - Pole CallerIpAddress změnit tak, aby CallerIPAddress
   - Pole RemoteIPCountry je již k dispozici
4. Odeberte *klíč trezoru Analytics (nepoužívané)* řešení. Pokud používáte prostředí PowerShell, použijte `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Data jsou shromažďována předtím, než tato změna není zobrazená v nové řešení. Můžete pokračovat se dotázat na tato data pomocí starého typu a názvy polí.

## <a name="troubleshooting"></a>Řešení potíží
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Další postup
* Použití [hledání přihlásit analýzy protokolů](log-analytics-log-searches.md) na podrobnější data Azure Key Vault.
