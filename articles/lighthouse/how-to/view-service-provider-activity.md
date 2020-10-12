---
title: Zobrazení aktivity poskytovatele služeb
description: Zákazníci si můžou zobrazit protokolované aktivity a zobrazit akce provedené poskytovateli služeb prostřednictvím delegované správy prostředků Azure.
ms.date: 07/07/2020
ms.topic: how-to
ms.openlocfilehash: 0c92fc9b45d17e37fb3721d9cf087c5e7a62f6d7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86131468"
---
# <a name="view-service-provider-activity"></a>Zobrazení aktivity poskytovatele služeb

Zákazníci, kteří mají delegované předplatné pro [Azure Lighthouse](../overview.md) , mohou [Zobrazit data protokolu aktivit Azure](../../azure-monitor/platform/platform-logs-overview.md) , aby viděli všechny akce, které provedli. Díky tomu mohou zákazníci plně zobrazit operace, které poskytovatelé služeb provádějí prostřednictvím [správy delegovaných prostředků Azure](../concepts/azure-delegated-resource-management.md), spolu s operacemi provedenými uživateli v rámci vlastního Azure Active Directoryho tenanta zákazníka (Azure AD).

> [!TIP]
> Poskytujeme také Azure Policy vestavěnou definici zásad pro auditování delegování oborů do spravovaného tenanta. Další informace najdete v tématu [auditování delegování ve vašem prostředí](view-manage-service-providers.md#audit-delegations-in-your-environment).

## <a name="view-activity-log-data"></a>Zobrazení dat protokolu aktivit

[Protokol aktivit můžete zobrazit](../../azure-monitor/platform/activity-log.md#view-the-activity-log) v nabídce **monitorování** v Azure Portal. Pokud chcete výsledky omezit na konkrétní předplatné, použijte filtry k výběru konkrétního předplatného. [Události protokolu aktivit můžete také zobrazit a načíst](../../azure-monitor/platform/activity-log.md#view-the-activity-log) programově.

> [!NOTE]
> Uživatelé v tenantovi poskytovatele služeb můžou zobrazit výsledky protokolu aktivit delegovaného předplatného v tenantovi zákazníka, pokud jim byla udělena role [Čtenář](../../role-based-access-control/built-in-roles.md#reader) (nebo jiná předdefinovaná role, která zahrnuje přístup čtenářů) při připojení tohoto předplatného do Azure Lighthouse.

V protokolu aktivit uvidíte název operace a její stav spolu s datem a časem, kdy byla provedena. **Událost iniciovaná ve** sloupci zobrazuje, který uživatel provedl operaci, ať už se jedná o uživatele v tenantovi poskytovatele služeb, který působí prostřednictvím Azure Lighthouse nebo uživatel ve vlastním tenantovi zákazníka. Všimněte si, že se zobrazuje jméno uživatele, nikoli tenant nebo role, které uživatel přiřadil k tomuto předplatnému.

Protokolovaná aktivita je v Azure Portal k dispozici po dobu posledních 90 dnů. Informace o tom, jak ukládat tato data déle než 90 dní, najdete [v tématu shromáždění a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics](../../azure-monitor/platform/activity-log.md).

> [!NOTE]
> Uživatelé od poskytovatele služeb se zobrazí v protokolu aktivit, ale tito uživatelé a jejich přiřazení rolí se v **Access Control (IAM)** nezobrazuje ani při načítání informací o přiřazení rolí přes rozhraní API.

## <a name="set-alerts-for-critical-operations"></a>Nastavení výstrah pro kritické operace

Abyste měli přehled o důležitých operacích, které poskytovatelé služeb (nebo uživatelé ve vašem tenantovi) provádějí, doporučujeme vytvořit [výstrahy protokolu aktivit](../../azure-monitor/platform/activity-log-alerts.md). Můžete například chtít sledovat všechny akce správy pro předplatné nebo upozornit, když dojde k odstranění libovolného virtuálního počítače v určité skupině prostředků. Když vytváříte výstrahy, budou zahrnovat akce provedené uživateli ve vlastním tenantovi zákazníka i na všech spravovaných klientech.

Další informace najdete v tématu [vytváření a správa výstrah protokolu aktivit](../../azure-monitor/platform/alerts-activity-log.md).

## <a name="create-log-queries"></a>Vytváření dotazů protokolu

Můžete vytvářet dotazy k analýze protokolované aktivity nebo zaměření na konkrétní položky. Například audit vyžaduje, abyste nahlásili všechny akce na úrovni správy provedené v rámci předplatného. Můžete vytvořit dotaz pro filtrování pouze těchto akcí a seřadit výsledky podle uživatele, datum nebo jiné hodnoty.

Další informace najdete v tématu [Přehled dotazů protokolu v Azure monitor](../../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure monitor](../../azure-monitor/index.yml).
- Naučte se, jak [Zobrazit a spravovat nabídky poskytovatele služeb](view-manage-service-providers.md) v Azure Portal.
