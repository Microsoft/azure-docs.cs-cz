---
title: Zobrazení aktivity poskytovatele služeb
description: Zákazníci si můžou zobrazit protokolované aktivity a zobrazit akce provedené poskytovateli služeb prostřednictvím delegované správy prostředků Azure.
ms.date: 12/11/2020
ms.topic: how-to
ms.openlocfilehash: 40deca310eea2fc9618cfc83d34caadcf2b2b14d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571747"
---
# <a name="view-service-provider-activity"></a>Zobrazení aktivity poskytovatele služeb

Zákazníci, kteří mají delegované předplatné pro [Azure Lighthouse](../overview.md) , mohou [Zobrazit data protokolu aktivit Azure](../../azure-monitor/essentials/platform-logs-overview.md) , aby viděli všechny akce, které provedli. Díky tomu mohou zákazníci plně zobrazit operace, které poskytovatelé služeb provádějí prostřednictvím [správy delegovaných prostředků Azure](../concepts/azure-delegated-resource-management.md), spolu s operacemi provedenými uživateli v rámci vlastního Azure Active Directoryho tenanta zákazníka (Azure AD).

> [!TIP]
> Poskytujeme také Azure Policy předdefinované definice zásad, které [omezují delegování na konkrétní správu tenantů](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json) a [auditují delegování oborů na spravovaného tenanta](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json). Další informace najdete v tématu [auditování delegování ve vašem prostředí](view-manage-service-providers.md#audit-delegations-in-your-environment).

## <a name="view-activity-log-data"></a>Zobrazení dat protokolu aktivit

[Protokol aktivit můžete zobrazit](../../azure-monitor/essentials/activity-log.md#view-the-activity-log) v nabídce **monitorování** v Azure Portal. Pokud chcete výsledky omezit na konkrétní předplatné, použijte filtry k výběru konkrétního předplatného. [Události protokolu aktivit můžete také zobrazit a načíst](../../azure-monitor/essentials/activity-log.md#view-the-activity-log) programově.

> [!NOTE]
> Uživatelé v tenantovi poskytovatele služeb můžou zobrazit výsledky protokolu aktivit delegovaného předplatného v tenantovi zákazníka, pokud jim byla udělena role [Čtenář](../../role-based-access-control/built-in-roles.md#reader) (nebo jiná předdefinovaná role, která zahrnuje přístup čtenářů) při připojení tohoto předplatného do Azure Lighthouse.

V protokolu aktivit uvidíte název operace a její stav spolu s datem a časem, kdy byla provedena. **Událost iniciovaná ve** sloupci zobrazuje, který uživatel provedl operaci, ať už se jedná o uživatele v tenantovi poskytovatele služeb, který působí prostřednictvím Azure Lighthouse nebo uživatel ve vlastním tenantovi zákazníka. Všimněte si, že se zobrazuje jméno uživatele, nikoli tenant nebo role, které uživatel přiřadil k tomuto předplatnému.

Protokolovaná aktivita je v Azure Portal k dispozici po dobu posledních 90 dnů. Informace o tom, jak ukládat tato data déle než 90 dní, najdete [v tématu shromáždění a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics](../../azure-monitor/essentials/activity-log.md).

> [!NOTE]
> Uživatelé od poskytovatele služeb se zobrazí v protokolu aktivit, ale tito uživatelé a jejich přiřazení rolí se v **Access Control (IAM)** nezobrazuje ani při načítání informací o přiřazení rolí přes rozhraní API.

## <a name="set-alerts-for-critical-operations"></a>Nastavení výstrah pro kritické operace

Abyste měli přehled o důležitých operacích, které poskytovatelé služeb (nebo uživatelé ve vašem tenantovi) provádějí, doporučujeme vytvořit [výstrahy protokolu aktivit](../../azure-monitor/alerts/activity-log-alerts.md). Můžete například chtít sledovat všechny akce správy pro předplatné nebo upozornit, když dojde k odstranění libovolného virtuálního počítače v určité skupině prostředků. Když vytváříte výstrahy, budou zahrnovat akce provedené uživateli ve vlastním tenantovi zákazníka i na všech spravovaných klientech.

Další informace najdete v tématu [vytváření a správa výstrah protokolu aktivit](../../azure-monitor/alerts/alerts-activity-log.md).

## <a name="create-log-queries"></a>Vytváření dotazů protokolu

Můžete vytvářet dotazy k analýze protokolované aktivity nebo zaměření na konkrétní položky. Například audit vyžaduje, abyste nahlásili všechny akce na úrovni správy provedené v rámci předplatného. Můžete vytvořit dotaz pro filtrování pouze těchto akcí a seřadit výsledky podle uživatele, datum nebo jiné hodnoty.

Další informace najdete v tématu [Přehled dotazů protokolu v Azure monitor](../../azure-monitor/logs/log-query-overview.md).

## <a name="view-user-activity-across-domains"></a>Zobrazení aktivity uživatelů napříč doménami

Aktivitu můžete zobrazit z jednotlivých uživatelů napříč více doménami pomocí ukázkového sešitu [protokolů aktivit podle domény](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/workbook-activitylogs-by-domain) .

Výsledky je možné filtrovat podle názvu domény. Můžete také použít další filtry, například kategorii, úroveň nebo skupinu prostředků.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure monitor](../../azure-monitor/index.yml).
- Naučte se, jak [Zobrazit a spravovat nabídky poskytovatele služeb](view-manage-service-providers.md) v Azure Portal.
