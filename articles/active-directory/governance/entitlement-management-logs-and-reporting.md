---
title: Archivace & sestav pomocí Azure Monitor – Správa nároků Azure AD
description: Naučte se archivovat protokoly a vytvářet sestavy pomocí Azure Monitor v Azure Active Directory správě nároků.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c5ab92fcc1d70d12e37ae351e768514b4e7522f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102501698"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Archivace protokolů a vytváření sestav o správě nároků Azure AD v Azure Monitor

Azure AD ukládá události auditu po dobu až 30 dnů v protokolu auditu. Můžete ale data auditu uchovávat déle než výchozí dobu uchování, která je podrobnější, [Jak dlouho služba Azure AD Store ukládá data](../reports-monitoring/reference-reports-data-retention.md), a to tak, že se směruje na účet Azure Storage nebo pomocí Azure monitor. K těmto datům pak můžete použít sešity a vlastní dotazy a sestavy.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Konfigurace služby Azure AD pro použití Azure Monitor
Předtím, než použijete sešity Azure Monitor, je nutné nakonfigurovat službu Azure AD tak, aby odesílala kopii svých protokolů auditu do Azure Monitor.

Archivace protokolů auditu Azure AD vyžaduje, abyste měli Azure Monitor v předplatném Azure. Můžete si přečíst další informace o požadavcích a odhadované náklady na používání Azure Monitor v [protokolech aktivit Azure AD v Azure monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md).

**Požadovaná role**: globální správce

1. Přihlaste se k Azure Portal jako uživatel, který je globálním správcem. Ujistěte se, že máte přístup ke skupině prostředků obsahující pracovní prostor Azure Monitor.
 
1. Vyberte **Azure Active Directory** pak v nabídce monitorování v levém navigačním panelu klikněte na **nastavení diagnostiky** . Zkontrolujte, jestli už není nastavené nastavení pro odesílání protokolů auditu do tohoto pracovního prostoru.

1. Pokud není nastavení ještě nastaveno, klikněte na **Přidat nastavení diagnostiky**. Podle pokynů v článku [Integrujte protokoly služby Azure AD s protokoly Azure monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) k odeslání protokolu auditu Azure AD do pracovního prostoru Azure monitor.

    ![Podokno nastavení diagnostiky](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. Po odeslání protokolu do Azure Monitor vyberte **Log Analytics pracovní prostory** a vyberte pracovní prostor, který obsahuje protokoly auditu Azure AD.

1. Vyberte **využití a odhadované náklady** a klikněte na **uchovávání dat**. Změňte posuvník na počet dní, po které chcete zachovat data pro splnění požadavků auditování.

    ![Podokno pracovních prostorů Log Analytics](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

1. Chcete-li zobrazit rozsah dat uložených ve vašem pracovním prostoru, můžete použít sešit s *rozsahem data archivovaného protokolu* :  
    
    1. Vyberte **Azure Active Directory** klikněte na **sešity**. 
    
    1. Rozbalte část **Azure Active Directory řešení potíží** a klikněte na **Archivovaný rozsah dat protokolu**. 


## <a name="view-events-for-an-access-package"></a>Zobrazit události pro balíček pro přístup  

Pokud chcete zobrazit události pro balíček pro přístup, musíte mít přístup k základnímu pracovnímu prostoru Azure monitor (informace najdete v tématu [Správa přístupu k datům protokolů a pracovním prostorům v Azure monitor](../../azure-monitor/logs/manage-access.md#manage-access-using-azure-permissions) pro informace) a v jedné z následujících rolí: 

- Globální správce  
- Správce zabezpečení  
- Čtenář zabezpečení  
- Čtečka sestav  
- Správce aplikace  

K zobrazení událostí použijte následující postup: 

1. V Azure Portal vyberte **Azure Active Directory** a pak klikněte na **sešity**. Pokud máte jenom jedno předplatné, přejděte ke kroku 3. 

1. Pokud máte více předplatných, vyberte předplatné, které obsahuje pracovní prostor.  

1. Vyberte sešit s názvem *aktivita přístupu balíčku*. 

1. V tomto sešitu vyberte časový rozsah (změnit na **vše** , pokud to není nutné), a v rozevíracím seznamu všech balíčků pro přístup, které měly aktivity během tohoto časového rozsahu, vyberte ID balíčku přístupu. Zobrazí se události související s balíčkem přístupu, ke kterému došlo během vybraného časového rozsahu.  

    ![Zobrazit události balíčku přístupu](./media/entitlement-management-logs-and-reporting/view-events-access-package.png) 

    Každý řádek obsahuje čas, ID balíčku pro přístup, název operace, ID objektu, hlavní název uživatele (UPN) a zobrazované jméno uživatele, který operaci zahájil.  Další podrobnosti jsou obsaženy ve formátu JSON.   


## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Vytváření vlastních Azure Monitor dotazů pomocí Azure Portal
Můžete vytvářet vlastní dotazy na události auditu Azure AD, včetně událostí správy nároků.  

1. V Azure Active Directory Azure Portal klikněte v navigační nabídce vlevo na **protokoly** v části monitorování a vytvořte novou stránku dotazu.

1. Váš pracovní prostor by se měl zobrazit v levém horním rohu stránky dotazu. Pokud máte několik pracovních prostorů Azure Monitor a pracovní prostor, který používáte k uložení událostí auditu Azure AD, není zobrazený, klikněte na **Vybrat obor**. Pak vyberte správné předplatné a pracovní prostor.

1. Dále v oblasti text dotazu odstraňte řetězec "Search *" a nahraďte ho následujícím dotazem:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Pak klikněte na **Spustit**. 

    ![Kliknutím na Spustit spustíte dotaz.](./media/entitlement-management-logs-and-reporting/run-query.png)

V tabulce se ve výchozím nastavení zobrazí události protokolu auditu pro správu nároků za poslední hodinu. Můžete změnit nastavení časový rozsah a zobrazit starší události. Změnou tohoto nastavení se ale zobrazí jenom události, ke kterým došlo po nakonfigurování Azure AD, aby odesílaly události do Azure Monitor.

Pokud byste chtěli znát nejstarší a nejnovější události auditu, které jsou v Azure Monitor, použijte následující dotaz:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Další informace o sloupcích uložených pro události auditu v Azure Monitor najdete v tématu [Interpretace schématu protokolu auditu Azure AD v Azure monitor](../reports-monitoring/reference-azure-monitor-audit-log-schema.md).

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Vytváření vlastních Azure Monitor dotazů pomocí Azure PowerShell

Po nakonfigurování služby Azure AD k odesílání protokolů na Azure Monitor můžete přes PowerShell získat přístup k protokolům. Pak odešlete dotazy ze skriptů nebo příkazového řádku PowerShellu, aniž by bylo nutné být globálním správcem v tenantovi. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Zajistěte, aby měl uživatel nebo instanční objekt správné přiřazení role.

Ujistěte se, že jste vy, uživatel nebo instanční objekt, který se ověří v Azure AD, v příslušné roli Azure v pracovním prostoru Log Analytics. Možnosti role jsou buď čtecí modul Log Analytics, nebo přispěvatel Log Analytics. Pokud už jste v jedné z těchto rolí, přeskočte, abyste [načetli ID Log Analytics s jedním předplatným Azure](#retrieve-log-analytics-id-with-one-azure-subscription).

Chcete-li nastavit přiřazení role a vytvořit dotaz, proveďte následující kroky:

1. V Azure Portal vyhledejte [pracovní prostor Log Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
).

1. Vyberte **Access Control (IAM)**.

1. Pak kliknutím na **Přidat** přidejte přiřazení role.

    ![Přidat přiřazení role](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Nainstalovat modul Azure PowerShell

Jakmile budete mít příslušné přiřazení role, spusťte PowerShell a [nainstalujte Azure PowerShell modul](/powershell/azure/install-az-ps) (Pokud jste to ještě neudělali), a to tak, že zadáte:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Nyní jste připraveni ověřit službu Azure AD a načíst ID Log Analytics pracovního prostoru, který se dotazuje.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Načtení ID Log Analytics s jedním předplatným Azure
Pokud máte pouze jedno předplatné Azure a jeden Log Analytics pracovní prostor, zadejte následující příkaz pro ověření ve službě Azure AD, připojte se k tomuto předplatnému a načtěte tento pracovní prostor:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Načtení ID Log Analytics s několika předplatnými Azure

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) funguje v jednom předplatném najednou. Pokud máte více předplatných Azure, budete chtít mít jistotu, že se připojujete k tomuto pracovnímu prostoru Log Analytics s protokoly Azure AD. 
 
 Následující rutiny zobrazují seznam předplatných a vyhledají ID předplatného, které má Log Analytics pracovní prostor:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
Můžete znovu ověřit a přidružit relaci PowerShellu k tomuto předplatnému pomocí příkazu, jako je `Connect-AzAccount –Subscription $subs[0].id` . Další informace o tom, jak ověřit službu Azure z PowerShellu, včetně neinteraktivního, najdete v tématu věnovaném [přihlášení pomocí Azure PowerShell](/powershell/azure/authenticate-azureps).

Pokud máte v tomto předplatném více Log Analytics pracovních prostorů, rutina [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) vrátí seznam pracovních prostorů. Pak můžete najít tu, která má protokoly služby Azure AD. `CustomerId`Pole vrácené touto rutinou je stejné jako hodnota "ID pracovního prostoru", která se zobrazuje v Azure Portal v tématu Přehled pracovního prostoru Log Analytics.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Odeslat dotaz do pracovního prostoru Log Analytics
Nakonec, když máte identifikovaný pracovní prostor, můžete k odeslání dotazu Kusto do tohoto pracovního prostoru použít [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery) . Tyto dotazy jsou napsané v [jazyce dotazů Kusto](/azure/kusto/query/).
 
Můžete například načíst rozsah dat záznamů událostí auditu z pracovního prostoru Log Analytics a pomocí rutin prostředí PowerShell odeslat dotaz jako:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

Události správy oprávnění můžete také načíst pomocí dotazu, jako je:

```azurepowershell
$bQuery = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Další kroky:
- [Vytváření interaktivních sestav pomocí Azure Monitorch sešitů](../../azure-monitor/visualize/workbooks-overview.md)