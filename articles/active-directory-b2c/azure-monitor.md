---
title: Monitorování Azure AD B2C s využitím Azure Monitor
titleSuffix: Azure AD B2C
description: Naučte se protokolovat události Azure AD B2C pomocí Azure Monitor pomocí delegované správy prostředků.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.author: mimart
ms.subservice: B2C
ms.date: 11/12/2020
ms.openlocfilehash: b41f5e9a3bd4d3cbe52cf2e1c567d24de8a661f4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94949951"
---
# <a name="monitor-azure-ad-b2c-with-azure-monitor"></a>Monitorování Azure AD B2C s využitím Azure Monitor

Pomocí Azure Monitor můžete směrovat přihlašování Azure Active Directory B2C (Azure AD B2C) a protokoly [auditování](view-audit-logs.md) do různých řešení monitorování. Protokoly můžete uchovávat pro dlouhodobé použití nebo integraci s nástroji SIEM (Security Information and Event Management) třetích stran, abyste získali přehled o vašem prostředí.

Události protokolu můžete směrovat do:

* Účet služby Azure [Storage](../storage/blobs/storage-blobs-introduction.md).
* [Pracovní prostor Log Analytics](../azure-monitor/platform/resource-logs.md#send-to-log-analytics-workspace) (pro analýzu dat, vytváření řídicích panelů a upozornění na konkrétní události).
* [Centrum událostí](../event-hubs/event-hubs-about.md) Azure (a integrujte je s vašimi logickými instancemi Splunk a sumo).

![Azure Monitor](./media/azure-monitor/azure-monitor-flow.png)

V tomto článku se dozvíte, jak přenést protokoly do pracovního prostoru Azure Log Analytics. Pak můžete vytvořit řídicí panel nebo vytvořit výstrahy založené na aktivitách Azure AD B2C uživatelů.

## <a name="deployment-overview"></a>Přehled nasazení

Azure AD B2C využívá [Azure Active Directory monitorování](../active-directory/reports-monitoring/overview-monitoring.md). Pokud chcete povolit *nastavení diagnostiky* v Azure Active Directory v rámci vašeho tenanta Azure AD B2C, použijte [Azure Lighthouse](../lighthouse/concepts/azure-delegated-resource-management.md) k [delegování prostředku](../lighthouse/concepts/azure-delegated-resource-management.md), který umožňuje vašemu Azure AD B2C ( **poskytovateli služeb**) spravovat prostředek služby Azure AD ( **zákazníka**). Po dokončení kroků v tomto článku budete mít přístup ke skupině prostředků *Azure-AD-B2C-monitor* , která obsahuje [pracovní prostor Log Analytics](../azure-monitor/learn/quick-create-workspace.md) na portálu **Azure AD B2C** . Protokoly budete moct přenést z Azure AD B2C do pracovního prostoru Log Analytics.

Během tohoto nasazení autorizujete uživatele nebo skupinu v adresáři Azure AD B2C ke konfiguraci instance pracovního prostoru Log Analytics v rámci tenanta, který obsahuje vaše předplatné Azure. Pokud chcete vytvořit autorizaci, nasadíte šablonu [Azure Resource Manager](../azure-resource-manager/index.yml) do svého tenanta služby Azure AD obsahujícího předplatné.

Následující diagram znázorňuje komponenty, které nakonfigurujete ve svých klientech Azure AD a Azure AD B2C.

![Projekce skupiny prostředků](./media/azure-monitor/resource-group-projection.png)

Během tohoto nasazení nakonfigurujete svého klienta Azure AD B2C i klienta služby Azure AD, kde bude hostovaný pracovní prostor Log Analytics. Účet, který se používá ke spuštění nasazení, musí mít v obou těchto klientech přiřazenou roli [globálního správce](../active-directory/roles/permissions-reference.md#limit-use-of-global-administrator) . Je také důležité se ujistit, že jste přihlášeni ke správnému adresáři, jak jste dokončili každý krok, jak je popsáno v tématu.

## <a name="1-create-or-choose-resource-group"></a>1. Vytvořte nebo vyberte skupinu prostředků.

Nejprve vytvořte nebo vyberte skupinu prostředků, která obsahuje cílový Log Analytics pracovní prostor, který bude přijímat data z Azure AD B2C. Název skupiny prostředků určíte při nasazení šablony Azure Resource Manager.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho **tenanta Azure AD**.
1. [Vytvořte skupinu prostředků](../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) nebo vyberte některou z existujících. V tomto příkladu se používá skupina prostředků s názvem *Azure-AD-B2C-monitor*.

## <a name="2-create-a-log-analytics-workspace"></a>2. vytvoření pracovního prostoru Log Analytics

**Log Analytics pracovní prostor** je jedinečné prostředí pro Azure monitor data protokolu. Pomocí tohoto Log Analytics pracovního prostoru můžete shromažďovat data z [protokolů auditu](view-audit-logs.md)Azure AD B2C a pak je vizualizovat pomocí dotazů a sešitů nebo vytvářet upozornění.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho **tenanta Azure AD**.
1. [Vytvořte pracovní prostor Log Analytics](../azure-monitor/learn/quick-create-workspace.md). V tomto příkladu se používá pracovní prostor Log Analytics s názvem *AzureAdB2C* ve skupině prostředků s názvem *Azure-AD-B2C-monitor*.

## <a name="3-delegate-resource-management"></a>3. delegování správy prostředků

V tomto kroku zvolíte svého tenanta Azure AD B2C jako **poskytovatele služeb**. Také definujete autorizaci, které potřebujete k přiřazení odpovídajících integrovaných rolí Azure skupinám v tenantovi Azure AD.

### <a name="31-get-your-azure-ad-b2c-tenant-id"></a>3,1 Získejte ID tenanta Azure AD B2C.

Nejprve získejte **ID tenanta** vašeho adresáře Azure AD B2C (označuje se také jako ID adresáře).

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta **Azure AD B2C** .
1. Vyberte **Azure Active Directory** vyberte **Přehled**.
1. Poznamenejte si **ID tenanta**.

### <a name="32-select-a-security-group"></a>3,2 vyberte skupinu zabezpečení.

Teď vyberte skupinu Azure AD B2C nebo uživatele, kterému chcete udělit oprávnění ke skupině prostředků, kterou jste vytvořili dříve v adresáři, který obsahuje vaše předplatné.  

Pro usnadnění správy doporučujeme použít pro každou roli *skupiny* uživatelů Azure AD, což umožňuje přidat nebo odebrat jednotlivé uživatele do skupiny místo přiřazování oprávnění přímo tomuto uživateli. V tomto návodu přidáme skupinu zabezpečení.

> [!IMPORTANT]
> Aby bylo možné přidat oprávnění pro skupinu Azure AD, musí být **typ skupiny** nastavený na **zabezpečení**. Tato možnost je vybrána při vytváření skupiny. Další informace najdete v tématu [Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

1. Pokud jste v adresáři **Azure AD B2C** pořád vybrali **Azure Active Directory** , vyberte **skupiny** a pak vyberte skupinu. Pokud nemáte existující skupinu, vytvořte skupinu **zabezpečení** a pak přidejte členy. Pro další informace použijte postup [Vytvoření základní skupiny a přidání členů pomocí Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). 
1. Vyberte **Přehled** a zaznamenejte **ID objektu** skupiny.

### <a name="33-create-an-azure-resource-manager-template"></a>3,3 Vytvoření šablony Azure Resource Manager

V dalším kroku vytvoříte šablonu Azure Resource Manager, která uděluje Azure AD B2C přístup k této skupině prostředků Azure AD, kterou jste vytvořili dříve (například *Azure-AD-B2C-monitor*). Nasaďte šablonu z ukázky na GitHubu pomocí tlačítka **nasadit do Azure** , které otevře Azure Portal a umožňuje konfiguraci a nasazení šablony přímo na portálu. V případě těchto kroků se ujistěte, že jste přihlášeni ke svému tenantovi služby Azure AD (nikoli k tenantovi Azure AD B2C).

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho TENANTA **Azure AD** .
3. Pomocí tlačítka **nasadit do Azure** otevřete Azure Portal a šablonu nasaďte přímo na portálu. Další informace najdete v tématu [Vytvoření šablony Azure Resource Manager](../lighthouse/how-to/onboard-customer.md#create-an-azure-resource-manager-template).

   [![Nasazení do Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Lighthouse-samples%2Fmaster%2Ftemplates%2Frg-delegated-resource-management%2FrgDelegatedResourceManagement.json)

5. Na stránce **vlastní nasazení** zadejte následující informace:

   | Pole   | Definice |
   |---------|------------|
   | Předplatné |  Vyberte adresář, který obsahuje předplatné Azure, ve kterém se vytvořila skupina prostředků *Azure-AD-B2C-monitor* . |
   | Oblast| Vyberte oblast, do které se prostředek nasadí.  | 
   | Název nabídky MSP| Název popisující tuto definici. Například *Azure AD B2C monitoring*.  |
   | Popis nabídky MSP| Stručný popis vaší nabídky Například *povolí Azure monitor v Azure AD B2C*.|
   | Spravované podle ID tenanta| **ID tenanta** vašeho tenanta Azure AD B2C (označuje se také jako ID adresáře). |
   |Autorizace|Zadejte pole JSON objektů, které zahrnují Azure AD `principalId` , `principalIdDisplayName` a Azure `roleDefinitionId` . `principalId`Je **ID objektu** skupiny B2C nebo uživatele, který bude mít přístup k prostředkům v tomto předplatném Azure. Pro tento návod zadejte ID objektu skupiny, které jste si poznamenali dříve. Pro můžete `roleDefinitionId` použít [předdefinovanou hodnotu role](../role-based-access-control/built-in-roles.md) *přispěvatele* `b24988ac-6180-42a0-ab88-20f7382dd24c` .|
   | Název RG | Název skupiny prostředků, kterou jste v tenantovi Azure AD vytvořili dříve. Například *Azure-AD-B2C-monitor*. |

   Následující příklad ukazuje pole autorizace s jednou skupinou zabezpečení.

   ```json
   [
       {
           "principalId": "<Replace with group's OBJECT ID>",
           "principalIdDisplayName": "Azure AD B2C tenant administrators",
           "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
       }
   ]
   ```

Až šablonu nasadíte, může trvat několik minut (obvykle ne více než pět), než se projekce prostředku dokončí. Můžete ověřit nasazení v tenantovi Azure AD a získat podrobné informace o projekci prostředků. Další informace najdete v tématu [zobrazení a Správa poskytovatelů služeb](../lighthouse/how-to/view-manage-service-providers.md).

## <a name="4-select-your-subscription"></a>4. Vyberte své předplatné.

Po nasazení šablony a dokončení projekce prostředků počkejte několik minut. k přidružení předplatného k adresáři Azure AD B2C použijte následující postup.

1. Pokud jste přihlášeni, odhlaste se z Azure Portal, pokud jste právě přihlášení (Tím umožníte aktualizovat přihlašovací údaje relace v dalším kroku).
2. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu správce **Azure AD B2C** . Tento účet musí být členem skupiny zabezpečení, kterou jste zadali v kroku [Správa prostředků delegáta](#3-delegate-resource-management) .
3. Na panelu nástrojů portálu vyberte ikonu **adresář + předplatné** .
4. Vyberte adresář služby Azure AD, který obsahuje předplatné Azure a skupinu prostředků *Azure-AD-B2C-monitor* , kterou jste vytvořili.

    ![Přepnout adresář](./media/azure-monitor/azure-monitor-portal-03-select-subscription.png)

1. Ověřte, že jste vybrali správný adresář a předplatné. V tomto příkladu jsou vybrané všechny adresáře a všechny odběry.

    ![Všechny adresáře vybrané ve filtru předplatných adresářových &](./media/azure-monitor/azure-monitor-portal-04-subscriptions-selected.png)

## <a name="5-configure-diagnostic-settings"></a>5. Konfigurace nastavení diagnostiky

Nastavení diagnostiky definují, kam se mají odesílat protokoly a metriky prostředku. Možné cíle:

- [Účet úložiště Azure](../azure-monitor/platform/resource-logs-collect-storage.md)
- Řešení [centra událostí](../azure-monitor/platform/resource-logs-stream-event-hubs.md)
- [Pracovní prostor Log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md)

V tomto příkladu používáme pracovní prostor Log Analytics k vytvoření řídicího panelu.

### <a name="51-create-diagnostic-settings"></a>5,1 Vytvoření nastavení diagnostiky

Jste připraveni [vytvořit nastavení diagnostiky](../active-directory/reports-monitoring/overview-monitoring.md) v Azure Portal.

Postup konfigurace nastavení monitorování pro Azure AD B2C protokoly aktivit:

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu správce Azure AD B2C. Tento účet musí být členem skupiny zabezpečení, kterou jste zadali v kroku [Vyberte skupinu zabezpečení](#32-select-a-security-group) .
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. Vyberte **Azure Active Directory**
1. V části **Monitorování** vyberte **Nastavení diagnostiky**.
1. Pokud pro prostředek existují nastavení, zobrazí se seznam už nakonfigurovaných nastavení. Buď vyberte **Přidat nastavení diagnostiky** a přidejte nové nastavení, nebo vyberte **Upravit** pro úpravu existujícího nastavení. Každé nastavení nemůže mít více než jeden z cílových typů.

    ![Podokno nastavení diagnostiky v Azure Portal](./media/azure-monitor/azure-monitor-portal-05-diagnostic-settings-pane-enabled.png)

1. Dejte nastavení název, pokud ho ještě nikdo nemá.
1. Zaškrtněte políčko pro všechny cílové umístění pro odeslání protokolů. Vyberte **Konfigurovat** a určete jejich nastavení **, jak je popsáno v následující tabulce**.
1. Vyberte **Odeslat do Log Analytics** a potom vyberte **název pracovního prostoru** , který jste vytvořili dříve ( `AzureAdB2C` ).
1. Vyberte **AuditLogs** a **SignInLogs**.
1. Vyberte **Uložit**.

> [!NOTE]
> Může trvat až 15 minut, než se událost vygeneruje, aby se [zobrazila v pracovním prostoru Log Analytics](../azure-monitor/platform/data-ingestion-time.md). Přečtěte si taky další informace o [latencích generování sestav služby Active Directory](../active-directory/reports-monitoring/reference-reports-latencies.md), které mohou mít vliv na zastaralost dat a hrají důležitou roli při vytváření sestav.

Pokud se zobrazí chybová zpráva "Pokud chcete nastavit nastavení diagnostiky pro použití Azure Monitor pro adresář Azure AD B2C, budete muset nastavit delegovanou správu prostředků," Ujistěte se, že jste se přihlásili s uživatelem, který je členem [skupiny zabezpečení](#32-select-a-security-group) , a [vyberete své předplatné](#4-select-your-subscription).

## <a name="6-visualize-your-data"></a>6. vizualizace dat

Teď můžete nakonfigurovat svůj pracovní prostor Log Analytics k vizualizaci dat a konfiguraci výstrah. Tyto konfigurace je možné provést jak v tenantovi Azure AD, tak i v tenantovi Azure AD B2C.

### <a name="61-create-a-query"></a>6,1 vytvořit dotaz

Dotazy protokolu umožňují plně využít hodnoty dat shromažďovaných v protokolech Azure Monitor. Výkonný dotazovací jazyk umožňuje spojit data z více tabulek, agregovat velké sady dat a provádět komplexní operace s minimálním kódem. Prakticky všechny dotazy mohou být zodpovězeny a analýzou provedeny, pokud jsou shromážděna podpůrná data a rozumíte tomu, jak vytvořit správný dotaz. Další informace najdete v tématu [Začínáme s dotazy protokolu v Azure monitor](../azure-monitor/log-query/get-started-queries.md).

1. V **Log Analytics pracovním prostoru** vyberte **protokoly** .
1. V editoru dotazů vložte následující dotaz dotazovacího [jazyka Kusto](https://docs.microsoft.com/azure/data-explorer/kusto/query/) . Tento dotaz zobrazí použití zásad podle operace za posledních x dní. Výchozí doba trvání je nastavená na 90 dní (90d). Všimněte si, že dotaz se zaměřuje pouze na operaci, kde je token nebo kód vydán zásadami.

    ```kusto
    AuditLogs
    | where TimeGenerated  > ago(90d)
    | where OperationName contains "issue"
    | extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
    | extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
    | summarize SignInCount = count() by Policy, OperationName
    | order by SignInCount desc  nulls last
    ```

1. Vyberte **Run** (Spustit). Výsledky dotazu se zobrazí v dolní části obrazovky.
1. Pokud chcete dotaz uložit pro pozdější použití, vyberte **Uložit**.

   ![Editor protokolu Log Analytics](./media/azure-monitor/query-policy-usage.png)

1. Vyplňte následující podrobnosti:

    - **Název** – zadejte název dotazu.
    - Možnost **Uložit jako** `query` .
    - Vyberte **kategorii** `Log` .

1. Vyberte **Uložit**.

Můžete také změnit dotaz pro vizualizaci dat pomocí operátoru [vykreslení](https://docs.microsoft.com/azure/data-explorer/kusto/query/renderoperator?pivots=azuremonitor) .

```kusto
AuditLogs
| where TimeGenerated  > ago(90d)
| where OperationName contains "issue"
| extend  UserId=extractjson("$.[0].id",tostring(TargetResources))
| extend Policy=extractjson("$.[1].value",tostring(AdditionalDetails))
| summarize SignInCount = count() by Policy
| order by SignInCount desc  nulls last
| render  piechart
```

![Log Analytics výsečového editoru protokolů](./media/azure-monitor/query-policy-usage-pie.png)

Další ukázky najdete v [úložišti GitHub Azure AD B2C Siem](https://aka.ms/b2csiem).

### <a name="62-create-a-workbook"></a>6,2 Vytvoření sešitu

Sešity poskytují flexibilní plátno pro analýzu dat a vytváření bohatých vizuálních sestav v rámci webu Azure Portal. Umožňují využívat více zdrojů dat v rámci Azure a kombinovat je do sjednocených interaktivních prostředí. Další informace najdete v tématu [Azure monitor sešity](../azure-monitor/platform/workbooks-overview.md).

Podle pokynů níže vytvořte nový sešit pomocí šablony Galerie JSON. Tento sešit poskytuje řídicí panel pro **uživatele** a **ověřování** pro Azure AD B2C tenanta.

1. V **pracovním prostoru Log Analytics** vyberte **sešity**.
1. Na panelu nástrojů vyberte **+ Nová** možnost a vytvořte nový sešit.
1. Na stránce **nový sešit** vyberte **Rozšířený editor** pomocí **</>** Možnosti na panelu nástrojů.

     ![Šablona galerie](./media/azure-monitor/wrkb-adv-editor.png)

1. Vyberte **šablonu Galerie**.
1. Nahraďte JSON v **šabloně Galerie**  obsahem z [Azure AD B2C základní sešit](https://raw.githubusercontent.com/azure-ad-b2c/siem/master/workbooks/dashboard.json):
1. Použijte šablonu pomocí tlačítka **použít** .
1. Kliknutím na tlačítko **Úpravy Hotovo** na panelu nástrojů dokončíte úpravy sešitu.
1. Nakonec sešit uložte pomocí tlačítka **Uložit** na panelu nástrojů.
1. Zadejte **název**, například *Azure AD B2C řídicí panel*.
1. Vyberte **Uložit**.

    ![Uložit sešit](./media/azure-monitor/wrkb-title.png)

Sešit zobrazí sestavy ve formě řídicího panelu.

![První řídicí panel sešitu](./media/azure-monitor/wkrb-dashboard-1.png)

![Druhý řídicí panel sešitu](./media/azure-monitor/wrkb-dashboard-2.png)

![Třetí řídicí panel sešitu](./media/azure-monitor/wrkb-dashboard-3.png)


## <a name="create-alerts"></a>Vytváření upozornění

Upozornění vytvářejí pravidla upozornění služby Azure Monitor. Pravidla mohou v pravidelných intervalech automaticky spouštět uložené dotazy nebo vlastní prohledávání protokolů. Můžete vytvářet upozornění na základě konkrétních metrik výkonu, vytvoření určitých událostí, chybějící události nebo počtu událostí vytvořených v konkrétním časovém intervalu. Výstrahy můžete například použít k upozornění, když průměrný počet přihlášení překročí určitou prahovou hodnotu. Další informace najdete v tématu o [vytváření výstrah](../azure-monitor/learn/tutorial-response.md).


Pomocí následujících pokynů můžete vytvořit nové upozornění Azure, které pošle [e-mailové oznámení](../azure-monitor/platform/action-groups.md#configure-notifications) pokaždé, když dojde k 25% poklesu **celkového počtu požadavků** na předchozí období. Výstraha se spustí každých 5 minut a vyhledá se v oknech za posledních 24 hodin. Výstrahy se vytvářejí pomocí dotazovacího jazyka Kusto.


1. Z **Log Analytics pracovního prostoru** vyberte **protokoly**. 
1. Vytvořte nový **dotaz Kusto** pomocí následujícího dotazu.

    ```kusto
    let start = ago(24h);
    let end = now();
    let threshold = -25; //25% decrease in total requests.
    AuditLogs
    | serialize TimeGenerated, CorrelationId, Result
    | make-series TotalRequests=dcount(CorrelationId) on TimeGenerated in range(start, end, 1h)
    | mvexpand TimeGenerated, TotalRequests
    | where TotalRequests > 0
    | serialize TotalRequests, TimeGenerated, TimeGeneratedFormatted=format_datetime(todatetime(TimeGenerated), 'yyyy-M-dd [hh:mm:ss tt]')
    | project   TimeGeneratedFormatted, TotalRequests, PercentageChange= ((toreal(TotalRequests) - toreal(prev(TotalRequests,1)))/toreal(prev(TotalRequests,1)))*100
    | order by TimeGeneratedFormatted
    | where PercentageChange <= threshold   //Trigger's alert rule if matched.
    ```

1. Kliknutím na tlačítko **Spustit** otestujete dotaz. Výsledky by se měly zobrazit v případě, že celkový počet požadavků za posledních 24 hodin je minimálně 25%.
1. Chcete-li vytvořit pravidlo výstrahy na základě dotazu výše, použijte možnost **+ nové pravidlo výstrahy** dostupné na panelu nástrojů.
1. Na stránce **vytvořit pravidlo výstrahy** vyberte **název podmínky** . 
1. Na stránce **Konfigurovat logiku signálu** nastavte následující hodnoty a pak použijte tlačítko **Hotovo** a změny uložte.
    * Logika výstrahy: nastavte **počet výsledků** **větší než** **0**.
    * Vyhodnocení na základě: vyberte **1440** pro periodu (v minutách) a **5** pro frekvenci (v minutách). 

    ![Vytvoření podmínky pravidla výstrahy](./media/azure-monitor/alert-create-rule-condition.png)

Po vytvoření výstrahy přejdete do **Log Analytics pracovního prostoru** a vyberte **výstrahy**. Tato stránka zobrazuje všechny výstrahy, které byly aktivovány v rámci možnosti trvání nastavené podle **časového rozsahu** .  

### <a name="configure-action-groups"></a>Konfigurace skupin akcí

Výstrahy Azure Monitor a Service Health pomocí skupin akcí upozorní uživatele na aktivaci výstrahy. Můžete zahrnout odesílání hlasových hovorů, SMS, e-mailu. nebo spouštějí různé typy automatizovaných akcí. Postupujte podle pokynů [v části Vytvoření a Správa skupin akcí v Azure Portal](../azure-monitor/platform/action-groups.md)

Tady je příklad e-mailu s oznámením o výstraze. 

   ![E-mailové oznámení](./media/azure-monitor/alert-email-notification.png)

## <a name="multiple-tenants"></a>Více tenantů

Pokud chcete připojit více Azure AD B2Cch protokolů klientů ke stejnému pracovnímu prostoru Log Analytics (nebo účtu úložiště Azure nebo centra událostí), budete potřebovat samostatná nasazení s různými hodnotami **názvu nabídky MSP** . Ujistěte se, že je váš pracovní prostor Log Analytics ve stejné skupině prostředků jako ten, který jste nakonfigurovali v části [vytvořit nebo zvolit skupinu prostředků](#1-create-or-choose-resource-group).

Pokud pracujete s více Log Analyticsmi pracovními prostory, použijte [dotaz napříč pracovními](../azure-monitor/log-query/cross-workspace-query.md) prostory k vytváření dotazů, které fungují v několika pracovních prostorech. Následující dotaz například provede spojení dvou protokolů auditu z různých tenantů na základě stejné kategorie (například ověřování):

```kusto
workspace("AD-B2C-TENANT1").AuditLogs
| join  workspace("AD-B2C-TENANT2").AuditLogs
  on $left.Category== $right.Category
```

## <a name="change-the-data-retention-period"></a>Změna doby uchovávání dat

Protokoly Azure Monitor jsou navržené tak, aby bylo možné škálovat a podporovat shromažďování, indexování a ukládání velkých objemů dat za den z libovolného zdroje ve vaší firmě nebo v Azure, které jsou nasazené v Azure. Ve výchozím nastavení se protokoly uchovávají po dobu 30 dnů, ale doba uchování se dá prodloužit na až dva roky. Naučte se [spravovat využití a náklady pomocí protokolů Azure monitor](../azure-monitor/platform/manage-cost-storage.md). Po výběru cenové úrovně můžete [změnit dobu uchovávání dat](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period).

## <a name="next-steps"></a>Další kroky

* Další ukázky najdete v galerii Azure AD B2C [Siem](https://aka.ms/b2csiem). 

* Další informace o přidání a konfiguraci nastavení diagnostiky v Azure Monitor najdete v tématu [kurz: shromáždění a analýza protokolů prostředků z prostředku Azure](../azure-monitor/insights/monitor-azure-resource.md).

* Informace o streamování protokolů služby Azure AD do centra událostí najdete v tématu [kurz: streamování Azure Active Directory protokoly do centra událostí Azure](../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md).
