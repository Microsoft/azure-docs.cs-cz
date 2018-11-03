---
title: Řešení Azure Key Vault v Log Analytics | Dokumentace Microsoftu
description: Řešení Azure Key Vault v Log Analytics můžete použít ke kontrole protokolů služby Azure Key Vault.
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
ms.topic: conceptual
ms.date: 02/09/2017
ms.author: richrund
ms.component: ''
ms.openlocfilehash: caccd70e17d814fb58d5801ed12192e56f0e16ad
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959963"
---
# <a name="azure-key-vault-analytics-solution-in-log-analytics"></a>Řešení Azure Key Vault Analytics ve službě Log Analytics

![Symbol služby Key Vault](media/log-analytics-azure-key-vault/key-vault-analytics-symbol.png)

Řešení Azure Key Vault v Log Analytics můžete využít ke kontrole protokolů AuditEvent služby Azure Key Vault.

Pokud chcete používat řešení, budete muset povolit protokolování diagnostiky služby Azure Key Vault a přímé diagnostiky k pracovnímu prostoru Log Analytics. Není nutné pro zápis protokolů do úložiště objektů Blob v Azure.

> [!NOTE]
> V lednu 2017 změnit podporovaným způsobem odesílání protokolů ze služby Key Vault ke službě Log Analytics. Pokud používáte řešení Key Vault se zobrazí *(zastaralé)* v názvu odkazovat na [migrace z původního řešení Key Vault](#migrating-from-the-old-key-vault-solution) pro je potřeba postupovat podle kroků.
>
>

## <a name="install-and-configure-the-solution"></a>Instalace a konfigurace řešení
Pomocí následujících pokynů k instalaci a konfiguraci řešení Azure Key Vault:

1. Povolení řešení Azure Key Vault z [Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview) nebo pomocí procesu popsaného v [přidání řešení Log Analytics z Galerie řešení](../monitoring/monitoring-solutions.md).
2. Povolení diagnostického protokolování pro Key Vault prostředky pro monitorování, buď pomocí [portál](#enable-key-vault-diagnostics-in-the-portal) nebo [prostředí PowerShell](#enable-key-vault-diagnostics-using-powershell)

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Povolení diagnostiky na portálu služby Key Vault

1. Na webu Azure Portal přejděte k prostředku služby Key Vault k monitorování
2. Vyberte *diagnostické protokoly* otevřete následující stránku

   ![snímek dlaždice Azure Key Vault](media/log-analytics-azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Klikněte na tlačítko *zapnout diagnostiku* otevřete následující stránku

   ![snímek dlaždice Azure Key Vault](media/log-analytics-azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Chcete-li zapnout diagnostiku, klikněte na tlačítko *na* pod *stav*
5. Klikněte na zaškrtávací políčko pro *odesílat do Log Analytics*
6. Vyberte existující pracovní prostor Log Analytics nebo vytvořit pracovní prostor
7. Chcete-li povolit *AuditEvent* protokolů, klikněte na zaškrtávací políčko v protokolu
8. Klikněte na tlačítko *Uložit* povolení protokolování diagnostiky ke službě Log Analytics

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Povolení diagnostiky služby Key Vault pomocí Powershellu
Následující skript prostředí PowerShell poskytuje příklad, jak používat `Set-AzureRmDiagnosticSetting` povolit diagnostické protokolování pro Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzureRmKeyVault -VaultName 'ContosoKeyVault'

Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Přečtěte si podrobné informace o shromažďování dat pro Azure Key Vault
Řešení Azure Key Vault shromažďuje protokoly diagnostiky přímo ze služby Key Vault.
Není nutné pro zápis protokolů do úložiště objektů Blob v Azure a vyžaduje pro shromažďování dat se žádný agent.

V následující tabulce jsou uvedeny metody shromažďování dat a další podrobnosti o tom, jak data shromažďována pro Azure Key Vault.

| Platforma | Přímý agent | Systémy agenta System Center Operations Manager | Azure | Nástroj Operations Manager vyžaduje? | Dat agenta nástroje Operations Manager odeslaná pomocí skupiny pro správu | Četnost shromažďování dat |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | Při doručení |

## <a name="use-azure-key-vault"></a>Použití Azure Key Vault
Poté co [řešení nainstalovat](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview), zobrazení dat služby Key Vault kliknutím **Azure Key Vault** dlaždici z **přehled** stránka služby Log Analytics.

![snímek dlaždice Azure Key Vault](media/log-analytics-azure-key-vault/log-analytics-keyvault-tile.png)

Po klepnutí **přehled** dlaždici, můžete zobrazení souhrnných informací o protokolů a pak přejdete k podrobnostem v těchto kategoriích:

* Objem všechny operace služby key vault v čase
* Se nezdařila operace svazky v čase
* Průměrné operační latence operací
* Kvalitu služby pro operace se počet operací, neměl zabrat víc než 1000 ms seznam operací, neměl zabrat víc než 1000 ms

![Obrázek řídicího panelu služby Azure Key Vault](media/log-analytics-azure-key-vault/log-analytics-keyvault01.png)

![Obrázek řídicího panelu služby Azure Key Vault](media/log-analytics-azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Chcete-li zobrazit podrobnosti pro všechny operace
1. Na **přehled** stránky, klikněte na tlačítko **Azure Key Vault** dlaždici.
2. Na **Azure Key Vault** řídicí panel, zkontrolujte souhrnné informace u některého okna a pak klikněte na jednu Chcete-li zobrazit podrobné informace na stránce vyhledávání protokolu.

    Na žádném z vyhledávací stránky protokolů můžete zobrazit výsledky podle času, podrobné výsledky a historii hledání protokolu. Můžete také filtrovat podle omezujících vlastností můžete zúžit výsledky.

## <a name="log-analytics-records"></a>Záznamy služby Log Analytics
Řešení Azure Key Vault analyzuje záznamy, které mají typ **KeyVaults** , která se shromažďují z [AuditEvent protokoly](../key-vault/key-vault-logging.md) v Azure Diagnostics.  Vlastnosti pro tyto záznamy jsou v následující tabulce:  

| Vlastnost | Popis |
|:--- |:--- |
| Typ |*AzureDiagnostics* |
| SourceSystem |*Azure* |
| callerIpAddress |IP adresa klienta, který vytvořil požadavek |
| Kategorie | *AuditEvent* |
| CorrelationId |Volitelný GUID, který může klient předat pro korelaci protokolů na straně klienta s protokoly na straně služby (Key Vault). |
| doby trvání v MS |Doba trvání obsloužení požadavku REST API v milisekundách. Tentokrát nezahrnuje latenci sítě, takže čas, který budete vyhodnocovat na straně klienta se nemusí shodovat této doby. |
| httpStatusCode_d |Stavový kód HTTP vrácený z požadavku (například *200*) |
| id_s |Jedinečné ID požadavku |
| identity_claim_appid_g | Identifikátor GUID pro id aplikace |
| OperationName |Název operace, jak je uvedeno v [protokolování Azure Key Vault](../key-vault/key-vault-logging.md) |
| OperationVersion |Verze rozhraní REST API požadovaná klientem (například *2015-06-01*) |
| requestUri_s |Identifikátor URI žádosti |
| Prostředek |Název trezoru klíčů. |
| ResourceGroup |Skupina prostředků trezoru klíčů. |
| ID prostředku |ID prostředku Azure Resource Manageru Pro protokoly Key Vault. to je ID prostředku Key Vault. |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResourceType | *TREZORY SLUŽBY* |
| resultSignature |Stav protokolu HTTP (například *OK*) |
| Hodnota resultType |Výsledek požadavku REST API (například *úspěch*) |
| SubscriptionId |ID předplatného Azure obsahující trezor klíčů předplatného |

## <a name="migrating-from-the-old-key-vault-solution"></a>Migrace z původního řešení Key Vault
V lednu 2017 změnit podporovaným způsobem odesílání protokolů ze služby Key Vault ke službě Log Analytics. Tyto změny poskytují následující výhody:
+ Protokoly se zapisují přímo do Log Analytics bez nutnosti používat účet úložiště
+ Nižší latence od okamžiku, kdy se generují protokoly na ně k dispozici ve službě Log Analytics
+ Méně kroků konfigurace
+ Běžný formát pro všechny typy diagnostiky Azure

Použití aktualizované řešení:

1. [Konfigurovat diagnostiku, která se pošle přímo ke službě Log Analytics ze služby Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Povolení řešení Azure Key Vault pomocí procesu popsaného v [přidání řešení Log Analytics z Galerie řešení](../monitoring/monitoring-solutions.md)
3. Aktualizovat všechny uložené dotazy, řídicí panely nebo výstrahy k použití nového datového typu
  + Typ se liší od: KeyVaults k AzureDiagnostics. Elementu ResourceType můžete použít k filtrování pro protokoly Key Vault.
  - Místo: `KeyVaults`, použijte `AzureDiagnostics | where ResourceType'=="VAULTS"`
  + Pole: (názvy polí jsou malá a velká písmena)
  - Pro všechna pole, který má příponu \_s, \_d, nebo \_g v názvu, změňte první znak na malá písmena
  - Pro všechna pole, který má příponu \_o název, data se dělí do jednotlivých polí na základě názvů vnořeného pole. Například hlavní název uživatele volajícího uložená v poli `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`
   - Změnit na CallerIPAddress CallerIpAddress pole
   - Pole RemoteIPCountry již není k dispozici
4. Odeberte *Key Vault Analytics (zastaralé)* řešení. Pokud používáte PowerShell, použijte `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Data shromážděná před změna není viditelný v novém řešení. Můžete pokračovat k dotazování na tato data pomocí starého typu a názvy polí.

## <a name="troubleshooting"></a>Řešení potíží
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Další postup
* Použití [prohledávání protokolů v Log Analytics](log-analytics-log-search.md) zobrazíte podrobné údaje služby Azure Key Vault.
