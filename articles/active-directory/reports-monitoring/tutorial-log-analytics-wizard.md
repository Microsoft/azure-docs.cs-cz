---
title: Konfigurace Průvodce Log Analytics v Azure AD | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat Log Analytics.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 08/05/2020
ms.author: markvi
author: MarkusVi
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 643010ef1f6e941a57673a711e1871aafd1e341d
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361904"
---
# <a name="tutorial-configure-the-log-analytics-wizard"></a>Kurz: Konfigurace Průvodce Log Analytics


V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace pracovního prostoru Log Analytics pro protokoly auditu a přihlašování
> * Spouštění dotazů pomocí dotazovacího jazyka Kusto (KQL)
> * Vytvoření pravidla upozornění, které odesílá výstrahy při použití konkrétního účtu
> * Vytvoření vlastního sešitu pomocí šablony pro rychlý Start
> * Přidat dotaz do existující šablony sešitu

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure s alespoň jedním správcem licencované licence P1. Pokud nemáte předplatné Azure, můžete si [zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/free/).

- Tenanta Azure AD.

- Uživatele, který je globálním správcem nebo správcem zabezpečení pro tohoto tenanta Azure AD.


Seznamte se s těmito články:

- [Kurz: shromáždění a analýza protokolů prostředků z prostředku Azure](../../azure-monitor/learn/tutorial-resource-logs.md)

- [Jak integrovat protokoly aktivit pomocí Log Analytics](./howto-integrate-activity-logs-with-log-analytics.md)

- [Správa účtu pro nouzový přístup v Azure AD](../users-groups-roles/directory-emergency-access.md)

- [Stručná referenční příručka ke KQL](/azure/data-explorer/kql-quick-reference)

- [Azure Monitor sešity](../../azure-monitor/platform/workbooks-overview.md)



## <a name="configure-a-workspace"></a>Konfigurace pracovního prostoru 

Tento postup popisuje, jak nakonfigurovat pracovní prostor Log Analytics pro protokoly auditu a přihlašování.
Konfigurace pracovního prostoru Log Analytics se skládá ze dvou hlavních kroků:
 
1. Vytváření pracovního prostoru Log Analytics
2. Nastavení diagnostiky

**Konfigurace pracovního prostoru:** 


1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce.

2. Vyhledejte **pracovní prostory Log Analytics**.

    ![Hledání prostředků služby a dokumentů](./media/tutorial-log-analytics-wizard/search-services.png)

3. Na stránce pracovní prostory Log Analytics klikněte na **Přidat**.

    ![Snímek obrazovky se zobrazí na stránce pracovní prostory Log Analytics tlačítko Přidat.](./media/tutorial-log-analytics-wizard/add.png)

4.  Na stránce **vytvořit Log Analytics pracovní prostor** proveďte následující kroky:

    ![Vytvoření pracovního prostoru Log Analytics](./media/tutorial-log-analytics-wizard/create-log-analytics-workspace.png)

    1. Vyberte předplatné.

    2. Vyberte skupinu prostředků.
 
    3. Do textového pole **název** zadejte název (např.: MytestWorkspace1).

    4. Vyberte oblast.

5. Klikněte na **Zkontrolovat a vytvořit**.

    ![Podokno Zkontrolovat a vytvořit](./media/tutorial-log-analytics-wizard/review-create.png)

6. Klikněte na **vytvořit** a počkejte na úspěšné nasazení. Je možné, že budete muset aktualizovat stránku, aby se zobrazil nový pracovní prostor.

    ![Vytvořit](./media/tutorial-log-analytics-wizard/create-workspace.png)

7. Vyhledejte **Azure Active Directory**.

    ![Azure Active Firectory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

8. V části **monitorování** klikněte na **nastavení diagnostiky**.

    ![Azure Active Firectory](./media/tutorial-log-analytics-wizard/diagnostic-settings.png)

9. Na stránce **nastavení diagnostiky** klikněte na **Přidat nastavení diagnostiky**.

    ![Přidat nastavení diagnostiky](./media/tutorial-log-analytics-wizard/add-diagnostic-setting.png)

10. Na stránce **nastavení diagnostiky** proveďte následující kroky:

    ![Vybrat nastavení diagnostiky](./media/tutorial-log-analytics-wizard/select-diagnostics-settings.png)

    1. V části **Podrobnosti kategorie**vyberte **AuditLogs** a **SigninLogs**.

    2. V části **Podrobnosti o cíli**vyberte **Odeslat do Log Analytics**a pak vyberte svůj nový pracovní prostor Log Analytics. 
   
    3. Klikněte na **Uložit**. 

## <a name="run-queries"></a>Spouštění dotazů  

Tento postup ukazuje, jak spustit dotazy pomocí **dotazovacího jazyka Kusto (KQL)**.


**Spuštění dotazu:**


1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce.

2. Vyhledejte **Azure Active Directory**.

    ![Azure Active Firectory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. V části **monitorování** klikněte na **protokoly**.

4. Na stránce **protokoly** **klikněte na Začínáme.**

5. Do textového pole **hledání* zadejte dotaz.

6. Klikněte na **Spustit**.  


### <a name="kql-query-examples"></a>Příklady dotazů KQL

Z vstupních dat vybereme 10 náhodných položek:

`SigninLogs | take 10`

Podívejte se na přihlášení, kdy byl podmíněný přístup úspěšný.

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus` 


Spočítat, kolik úspěchů bylo

`SigninLogs | where ConditionalAccessStatus == "success" | project UserDisplayName, ConditionalAccessStatus | count`


Agregovaný počet úspěšných přihlášení uživatelem podle dne:

`SigninLogs | where ConditionalAccessStatus == "success" | summarize SuccessfulSign-ins = count() by UserDisplayName, bin(TimeGenerated, 1d)` 


Zobrazit, kolikrát uživatel provede určitou operaci v konkrétním časovém období:

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | summarize count() by OperationName, Identity`


Překlopit výsledky na název operace

`AuditLogs | where TimeGenerated > ago(30d) | where OperationName contains "Add member to role" | project OperationName, Identity | evaluate pivot(OperationName)`


Sloučení společně auditu a přihlášení pomocí vnitřního spojení:

`AuditLogs |where OperationName contains "Add User" |extend UserPrincipalName = tostring(TargetResources[0].userPrincipalName) | |project TimeGenerated , UserPrincipalName |join kind = inner (SigninLogs) on UserPrincipalName |summarize arg_min(TimeGenerated, *) by UserPrincipalName |extend SigninDate = TimeGenerated` 


Zobrazit počet přihlášení podle typu klientské aplikace:

`SigninLogs | summarize count() by ClientAppUsed`

Počítat přihlášení za den:

`SigninLogs | summarize NumberOfEntries=count() by bin(TimeGenerated, 1d)`

Vezměte 5 náhodných položek a Prohlédněte si sloupce, které chcete zobrazit ve výsledcích:

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `


Vezměte horní 5 v sestupném pořadí a Prohlédněte si sloupce, které chcete zobrazit.

`SigninLogs | take 5 | project ClientAppUsed, Identity, ConditionalAccessStatus, Status, TimeGenerated `

Vytvoří nový sloupec kombinováním hodnot do dvou dalších sloupců:

`SigninLogs | limit 10 | extend RiskUser = strcat(RiskDetail, "-", Identity) | project RiskUser, ClientAppUsed`




## <a name="create-an-alert-rule"></a>Vytvoření pravidla upozornění  

Tento postup ukazuje, jak odeslat výstrahy při použití účtu breakglass.

**Vytvoření pravidla výstrahy:**


1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce.

2. Vyhledejte **Azure Active Directory**.

    ![Azure Active Firectory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. V části **monitorování** klikněte na **protokoly**.

4. Na stránce **protokoly** **klikněte na Začínáme.**

5. Do textového pole **hledání** zadejte: `SigninLogs |where UserDisplayName contains "BreakGlass" | project UserDisplayName`

6. Klikněte na **Spustit**.  

7. Na panelu nástrojů klikněte na **nové pravidlo výstrahy**.

    ![Nové pravidlo výstrahy](./media/tutorial-log-analytics-wizard/new-alert-rule.png)

8. Na stránce **vytvořit pravidlo výstrahy** ověřte, zda je obor správný.

9. V části **Podmínka**klikněte na: **vždy, když je průměrné hledání vlastního protokolu větší než <logic undefined> počet** .

    ![Výchozí podmínka](./media/tutorial-log-analytics-wizard/default-condition.png)

10. Na stránce **Konfigurovat logiku signálu** v části **logika výstrahy** proveďte následující kroky:

    ![Logika výstrahy](./media/tutorial-log-analytics-wizard/alert-logic.png)

    1. V **závislosti na**vyberte **počet výsledků**.

    2. Jako **operátor**vyberte **větší než**.

    3. Jako **prahová hodnota**vyberte **0**. 

11. Na stránce **Konfigurovat logiku signálu** v části **vyhodnocováno** podle proveďte následující kroky:

    ![Vyhodnoceno na základě](./media/tutorial-log-analytics-wizard/evaluated-based-on.png)

    1. Jako **perioda (v minutách)** vyberte **5**.

    2. Jako **frekvence (v minutách)** vyberte **5**.

    3. Klikněte na **Hotovo**. 

12. V části **Skupina akcí**klikněte na **Vybrat skupinu akcí**. 

    ![Skupina akcí](./media/tutorial-log-analytics-wizard/action-group.png)

13. V poli **Vybrat skupinu akcí, která se má připojit k tomuto pravidlu výstrahy**, klikněte na **vytvořit skupinu akcí**. 

    ![Vytvoření skupiny akcí](./media/tutorial-log-analytics-wizard/create-action-group.png)

14. Na stránce **vytvořit skupinu akcí** proveďte následující kroky:

    ![Podrobnosti o instancích](./media/tutorial-log-analytics-wizard/instance-details.png)

    1. Do textového pole **název skupiny akcí** zadejte **moje skupina akcí**.

    2. Do textového pole **Zobrazovaný název** zadejte **Moje akce**.

    3. Klikněte na **Zkontrolovat a vytvořit**. 

    4. Klikněte na **Vytvořit**.


15. V části **přizpůsobit akci**proveďte následující kroky:

    ![Přizpůsobení akcí](./media/tutorial-log-analytics-wizard/customize-actions.png)

    1. Vyberte **Předmět e-mailu**.

    2. Do textového pole **řádek předmětu** zadejte: `Breakglass account has been used`

16. V části **Podrobnosti pravidla výstrahy**proveďte následující kroky:

    ![Podrobnosti pravidla výstrahy](./media/tutorial-log-analytics-wizard/alert-rule-details.png)

    1. Do textového pole **název pravidla výstrahy** zadejte: `Breakglass account`

    2. Do textového pole **Popis** zadejte: `Your emergency access account has been used`

17. Klikněte na **Vytvořit pravidlo upozornění**.   


## <a name="create-a-custom-workbook"></a>Vytvoření vlastního sešitu

Tento postup ukazuje, jak vytvořit nový sešit pomocí šablony pro rychlý Start.




1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce.

2. Vyhledejte **Azure Active Directory**.

    ![Azure Active Firectory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. V části **monitorování** klikněte na možnost **sešity**.

    ![Snímek obrazovky ukazuje monitorování v nabídce Azure Portal s vybranými sešity.](./media/tutorial-log-analytics-wizard/workbooks.png)

4. V části **rychlý Start** klikněte na **prázdné**.

    ![Rychlý start](./media/tutorial-log-analytics-wizard/quick-start.png)

5. Klikněte na **Přidat**.

    ![Přidat sešit](./media/tutorial-log-analytics-wizard/add-workbook.png)

6. Klikněte na tlačítko **Přidat text**.

    ![Přidat text](./media/tutorial-log-analytics-wizard/add-text.png)


7. Do textového pole zadejte: `# Client apps used in the past week` a pak klikněte na **hotové úpravy**.

    ![Text v sešitě](./media/tutorial-log-analytics-wizard/workbook-text.png)

8. V novém sešitu klikněte na tlačítko **Přidat**a potom klikněte na tlačítko **Přidat dotaz**.

    ![Přidat dotaz](./media/tutorial-log-analytics-wizard/add-query.png)

9. Do textového pole dotazu zadejte: `SigninLogs | where TimeGenerated > ago(7d) | project TimeGenerated, UserDisplayName, ClientAppUsed | summarize count() by ClientAppUsed`

10. Klikněte na **Spustit dotaz**.

    ![Spuštění dotazu](./media/tutorial-log-analytics-wizard/run-workbook-query.png)

11. Na panelu nástrojů v části **vizualizace**klikněte na **výsečový graf**.

    ![Výsečový graf](./media/tutorial-log-analytics-wizard/pie-chart.png)

12. Klikněte na **Hotovo úpravy**.

    ![Hotové úpravy](./media/tutorial-log-analytics-wizard/done-workbook-editing.png)



## <a name="add-a-query-to-a-workbook-template"></a>Přidání dotazu do šablony sešitu  

Tento postup ukazuje, jak přidat dotaz do existující šablony sešitu. Tento příklad je založený na dotazu, který znázorňuje distribuci úspěchu podmíněného přístupu na selhání.


1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce.

2. Vyhledejte **Azure Active Directory**.

    ![Azure Active Firectory](./media/tutorial-log-analytics-wizard/search-azure-ad.png)

3. V části **monitorování** klikněte na možnost **sešity**.

    ![Snímek obrazovky s vybranými sešity zobrazuje v nabídce monitorování.](./media/tutorial-log-analytics-wizard/workbooks.png)

4. V části **podmíněný přístup** klikněte na **přehledy podmíněného přístupu a vytváření sestav**.

    ![Šablona podmíněného přístupu](./media/tutorial-log-analytics-wizard/conditional-access-template.png)

5. Na panelu nástrojů klikněte na tlačítko **Upravit**.

    ![Šablona podmíněného přístupu](./media/tutorial-log-analytics-wizard/edit-workbook-template.png)

6. Na panelu nástrojů klikněte na tři tečky a pak **Přidat**a **Přidat dotaz**.

    ![Přidat dotaz na sešit](./media/tutorial-log-analytics-wizard/add-custom-workbook-query.png)

7. Do textového pole dotazu zadejte: `SigninLogs | where TimeGenerated > ago(20d) | where ConditionalAccessPolicies != "[]" | summarize dcount(UserDisplayName) by bin(TimeGenerated, 1d), ConditionalAccessStatus`

8. Klikněte na **Spustit dotaz**.

    ![Spuštění dotazu](./media/tutorial-log-analytics-wizard/run-workbook-insights-query.png)

9. Klikněte na **časový rozsah**a pak vyberte **nastavit v dotazu**.

10. Klikněte na **vizualizace**a pak vyberte **pruhový graf**. 

11. Klikněte na **Upřesnit nastavení**, jako název grafu, zadejte `Conditional Access status over the last 20 days` a potom klikněte na **hotové úpravy**. 

    ![Nastavit název grafu](./media/tutorial-log-analytics-wizard/set-chart-title.png)








## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku a Naučte se spravovat identity zařízení pomocí Azure Portal.
> [!div class="nextstepaction"]
> [Monitorování](overview-monitoring.md)