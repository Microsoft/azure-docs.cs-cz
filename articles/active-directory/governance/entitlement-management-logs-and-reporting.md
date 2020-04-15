---
title: Archivovat & sestavy pomocí Azure Monitoru – správa nároků Azure AD
description: Zjistěte, jak archivovat protokoly a vytvářet sestavy pomocí Azure Monitoru ve správě nároků Služby Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/14/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d59a508d03730a51e793a5e30e2c99a91af77ce8
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380201"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Archivovat protokoly a vytváření sestav na správu nároků Azure AD ve službě Azure Monitor

Azure AD ukládá události auditu po dobu až 30 dnů v protokolu auditu. Data auditu však můžete uchovávat déle, než je výchozí doba uchovávání, uvedená v [části Jak dlouho úložiště azure ad úložiště dat sestav?](../reports-monitoring/reference-reports-data-retention.md), směrováním do účtu Azure Storage nebo pomocí Azure Monitor. Potom můžete použít sešity a vlastní dotazy a sestavy na tato data.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Konfigurace Azure AD pro používání Azure Monitoru
Před použitím sešitů Azure Monitor, musíte nakonfigurovat Azure AD k odeslání kopie protokolů auditování do Azure Monitoru.

Archivace protokolů auditu Azure AD vyžaduje, abyste měli Azure Monitor v předplatném Azure. Další informace o požadavcích a odhadovaných nákladech na používání Azure Monitoru v [protokolech aktivit Azure AD najdete v Azure Monitoru.](../reports-monitoring/concept-activity-logs-azure-monitor.md)

**Předpokladová role**: Globální správce

1. Přihlaste se k portálu Azure jako uživatel, který je globální správce. Ujistěte se, že máte přístup ke skupině prostředků obsahující pracovní prostor Azure Monitor.
 
1. V levé navigační nabídce vyberte **Službu Azure Active Directory** a v části Monitorování klikněte na **Nastavení diagnostiky.** Zkontrolujte, zda již existuje nastavení pro odeslání protokolů auditu do tohoto pracovního prostoru.

1. Pokud nastavení ještě není k dispozici, klepněte na **tlačítko Přidat diagnostické nastavení**. Pomocí pokynů v článku [Integrace protokolů Azure AD s protokoly Azure Monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) u odesílání protokolu auditu Azure AD do pracovního prostoru Azure Monitor.

    ![Podokno nastavení diagnostiky](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Po odeslání protokolu do Azure Monitoru vyberte **pracovní prostory Analýzy protokolů**a vyberte pracovní prostor, který obsahuje protokoly auditu Azure AD.

1. Vyberte **Možnost Využití a odhadované náklady** a klepněte na **položku Uchovávání dat**. Změňte posuvník na počet dní, po které chcete data uchovávat tak, aby splňovala požadavky na auditování.

    ![Podokno pracovních prostorů Analýzy protokolů](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. Chcete-li později zobrazit rozsah dat uchovávaných v pracovním prostoru, můžete použít sešit *Archivovaný rozsah dat protokolu:*  
    
    1. Vyberte **Azure Active Directory** a klikněte na **Sešity**. 
    
    1. Rozbalte část **Řešení potíží služby Azure Active Directory**a klikněte na **položku Archivovaný rozsah dat protokolu**. 


## <a name="view-events-for-an-access-package"></a>Zobrazit události pro přístupový balíček  

Chcete-li zobrazit události pro přístupový balíček, musíte mít přístup k podkladovému pracovnímu prostoru Azure monitoru (informace najdete v tématu [Správa přístupu k datům protokolu a pracovním prostorům v Nástroji Azure)](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) a v jedné z následujících rolí: 

- Globální správce  
- Správce zabezpečení  
- Čtenář zabezpečení  
- Čtečka sestav  
- Správce aplikace  

K zobrazení událostí použijte následující postup: 

1. Na webu Azure Portal vyberte **Azure Active Directory a** klikněte na **Sešity**. Pokud máte jenom jedno předplatné, přejděte ke kroku 3. 

1. Pokud máte více předplatných, vyberte předplatné, které obsahuje pracovní prostor.  

1. Vyberte sešit s názvem *Access Package Activity*. 

1. V tomto sešitu vyberte časový rozsah (změnit na **Vše,** pokud si nejste jisti) a vyberte ID přístupového balíčku z rozevíracího seznamu všech přístupových balíčků, které měly aktivitu během tohoto časového období. Zobrazí se události související s balíčkem přístupu, ke kterým došlo během vybraného časového rozsahu.  

    ![Zobrazit události balíčku přístupu](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    Každý řádek obsahuje čas, ID přístupového balíčku, název operace, ID objektu, hlavní název uživatele a zobrazovaný název uživatele, který operaci spustil.  Další podrobnosti jsou uvedeny v JSON.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Vytváření vlastních dotazů Azure Monitoru pomocí portálu Azure
Můžete vytvořit vlastní dotazy na události auditu Azure AD, včetně událostí správy nároků.  

1. Ve službě Azure Active Directory na webu Azure Portal klikněte na **protokoly** v části Monitorování v levé navigační nabídce a vytvořte novou stránku dotazu.

1. Pracovní prostor by měl být zobrazen v levém horním rohu stránky dotazu. Pokud máte více pracovních prostorů Azure Monitor u a pracovní prostor, který používáte k ukládání událostí auditu Azure AD se nezobrazí, klikněte na **vybrat obor**. Potom vyberte správné předplatné a pracovní prostor.

1. Dále v textové oblasti dotazu odstraňte řetězec "hledat *" a nahraďte jej následujícím dotazem:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Potom klepněte na tlačítko **Spustit**. 

    ![Chcete-li spustit dotaz, klepněte na tlačítko Spustit.](./media/entitlement-management-logs-and-reporting/run-query.png)

V tabulce se zobrazí události protokolu auditu pro správu nároků za poslední hodinu ve výchozím nastavení. Chcete-li zobrazit starší události, můžete změnit nastavení "Časový rozsah". Změna tohoto nastavení se však zobrazí pouze události, ke kterým došlo po Azure AD byl nakonfigurován pro odesílání událostí do Azure Monitoru.

Pokud chcete znát nejstarší a nejnovější události auditu, které se konaly ve službě Azure Monitor, použijte následující dotaz:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Další informace o sloupcích, které jsou uložené pro události auditu v Azure Monitoru, najdete v [tématu interpretace schématu protokolů auditu Azure AD v Azure Monitoru](../reports-monitoring/reference-azure-monitor-audit-log-schema.md).

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Vytváření vlastních dotazů Azure Monitor pomocí Azure PowerShellu

K protokolům můžete přistupovat prostřednictvím Prostředí PowerShell po nakonfigurování Azure AD pro odesílání protokolů do Azure Monitoru. Potom odesílat dotazy ze skriptů nebo příkazového řádku Prostředí PowerShell, aniž byste museli být globálním správcem v tenantovi. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Ujistěte se, že uživatel nebo instanční objekt má správné přiřazení role

Ujistěte se, že uživatel nebo instancí, který se bude ověřovat ve službě Azure AD, jsou v příslušné roli Azure v pracovním prostoru Log Analytics. Možnosti role jsou buď Log Analytics Reader nebo přispěvatele analýzy protokolů. Pokud už jste v jedné z těchto rolí, pak přeskočte na [Načíst ID Analýzy protokolů s jedním předplatným Azure](#retrieve-log-analytics-id-with-one-azure-subscription).

Chcete-li nastavit přiřazení role a vytvořit dotaz, postupujte takto:

1. Na webu Azure Portal vyhledejte [pracovní prostor Log Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
).

1. Vyberte **řízení přístupu (IAM).**

1. Pak kliknutím na **Přidat** přidejte přiřazení role.

    ![Přidat přiřazení role](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Instalace modulu Azure PowerShell

Až budete mít odpovídající přiřazení rolí, spusťte PowerShell a [nainstalujte modul Azure PowerShell](/powershell/azure/install-az-ps?view=azps-3.3.0) (pokud jste tak ještě neučinili) zadáním:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Teď jste připraveni k ověření ve službě Azure AD a načíst ID pracovního prostoru Log Analytics, na který se dotazujete.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Načtení ID analýzy protokolů pomocí jednoho předplatného Azure
Pokud máte jenom jedno předplatné Azure a jeden pracovní prostor Log Analytics, zadejte následující, abyste se ověřili ve službě Azure AD, připojili se k tomuto předplatnému a načetli tento pracovní prostor:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Načtení ID analýzy protokolů s více předplatnými Azure

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) pracuje v jednom předplatném najednou. Takže pokud máte více předplatných Azure, budete chtít ujistěte se, že se připojíte k ten, který má pracovní prostor Log Analytics s protokoly Azure AD. 
 
 Následující rutiny zobrazí seznam předplatných a najdou ID předplatného, které má pracovní prostor Log Analytics:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
Relaci PowerShellu můžete k tomuto předplatnému znovu `Connect-AzAccount –Subscription $subs[0].id`ověřit a přidružit pomocí příkazu, jako je například . Další informace o tom, jak se ověřovat z PowerShellu do Azure, včetně neinterinterinterinterně, najdete [v tématu Přihlášení pomocí Azure PowerShellu](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
).

Pokud máte více pracovních prostorů Log Analytics v tomto předplatném, pak rutina [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) vrátí seznam pracovních prostorů. Pak můžete najít ten, který má protokoly Azure AD. Pole `CustomerId` vrácené touto rutinou je stejné jako hodnota "Id pracovního prostoru" zobrazené na portálu Azure v přehledu pracovního prostoru Analýzy protokolů.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Odeslání dotazu do pracovního prostoru Log Analytics
Nakonec, jakmile máte pracovní prostor identifikován, můžete použít [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
) k odeslání dotazu Kusto do tohoto pracovního prostoru. Tyto dotazy jsou napsány v [dotazovacím jazyce Kusto](https://docs.microsoft.com/azure/kusto/query/).
 
Můžete například načíst rozsah dat záznamů událostí auditu z pracovního prostoru Log Analytics pomocí rutin prostředí PowerShell pro odeslání dotazu, jako je:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

Můžete také načíst události správy nároků pomocí dotazu, jako je:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Další kroky:
- [Vytváření interaktivních sestav pomocí sešitů Azure Monitoru](../../azure-monitor/app/usage-workbooks.md) 

