---
title: Zobrazení aktivity poskytovatele služeb
description: Zákazníci mohou zobrazit protokolované aktivity vidět akce prováděné poskytovateli služeb prostřednictvím Azure delegované správy prostředků.
ms.date: 01/15/2020
ms.topic: conceptual
ms.openlocfilehash: a923a57ecc94ac15af207c2b8dc8998708b708d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77649632"
---
# <a name="view-service-provider-activity"></a>Zobrazení aktivity poskytovatele služeb

Zákazníci, kteří delegovali předplatná pro správu delegovaných prostředků Azure, můžou zobrazit data [protokolu aktivit Azure,](../../azure-monitor/platform/platform-logs-overview.md) aby viděli všechny provedené akce. To poskytuje zákazníkům úplný přehled o operacích, které poskytovatelé služeb provádějí prostřednictvím správy delegovaných prostředků Azure, spolu s operacemi provedenými uživateli v rámci vlastního klienta Azure Active Directory (Azure AD).

> [!TIP]
> Poskytujeme také azure zásady integrované definice zásad pro audit delegování oborů na správě klienta. Další informace naleznete [v tématu Auditování delegací ve vašem prostředí](view-manage-service-providers.md#audit-delegations-in-your-environment).

## <a name="view-activity-log-data"></a>Zobrazit data protokolu aktivit

Protokol [aktivit](../../azure-monitor/platform/activity-log-view.md) můžete zobrazit z nabídky **Monitorování** na webu Azure Portal. Chcete-li omezit výsledky na konkrétní předplatné, použijte filtry k výběru konkrétního předplatného. Můžete také [zobrazit a načíst události protokolu aktivit](../../azure-monitor/platform/activity-log-view.md) programově.

> [!NOTE]
> Uživatelé v tenantovi poskytovatele služeb mohou zobrazit výsledky protokolu aktivit pro delegované předplatné v tenantovi zákazníka, pokud jim byla udělena role [Čtečka](../../role-based-access-control/built-in-roles.md#reader) (nebo jiná předdefinovaná role, která zahrnuje přístup ke čtenáři), když bylo toto předplatné integrované pro správu delegovaných prostředků Azure.

V protokolu aktivit uvidíte název operace a její stav spolu s datem a časem, kdy byla provedena. **Událost iniciovaná sloupcem** ukazuje, který uživatel provedl operaci, zda se jednalo o uživatele v tenantovi poskytovatele služeb, který jednal prostřednictvím správy delegovaných prostředků Azure, nebo uživatele ve vlastním tenantovi zákazníka. Všimněte si, že je zobrazen název uživatele, nikoli tenanta nebo role, která byla uživateli přiřazena pro toto předplatné.

Protokolovaná aktivita je dostupná na webu Azure Portal za posledních 90 dní. Informace o tom, jak ukládat tato data déle než 90 dní, najdete v [tématu Shromažďování a analýza protokolů aktivit Azure v pracovním prostoru Log Analytics](../../azure-monitor/platform/activity-log-collect.md).

> [!NOTE]
> Uživatelé od poskytovatele služeb se zobrazí v protokolu aktivit, ale tito uživatelé a jejich přiřazení rolí se nezobrazují v **řízení přístupu (IAM)** nebo při načítání informací o přiřazení rolí prostřednictvím souborů API.

## <a name="set-alerts-for-critical-operations"></a>Nastavení výstrah pro kritické operace

Chcete-li si být vědomi důležitých operací, které provádějí poskytovatelé služeb (nebo uživatelé ve vašem vlastním tenantovi), doporučujeme vytvořit [výstrahy protokolu aktivit](../../azure-monitor/platform/activity-log-alerts.md). Můžete například chtít sledovat všechny akce správy pro předplatné nebo být upozorněni, když je odstraněn jakýkoli virtuální počítač v určité skupině prostředků. Při vytváření výstrah budou zahrnovat akce prováděné uživateli ve vlastním tenantovi zákazníka i ve všech klientech správy.

Další informace naleznete v [tématu Vytvoření a správa výstrah protokolu aktivit](../../azure-monitor/platform/alerts-activity-log.md).

## <a name="create-log-queries"></a>Vytvořit dotazy protokolu

Můžete vytvořit dotazy k analýze zaznamenané aktivity nebo se zaměřit na konkrétní položky. Audit například vyžaduje, abyste ohlásili všechny akce na úrovni správy provedené v rámci předplatného. Můžete vytvořit dotaz pro filtrování pouze těchto akcí a seřadit výsledky podle uživatele, data nebo jiné hodnoty.

Další informace najdete [v tématu Přehled dotazů protokolu v Azure Monitoru](../../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Další kroky

- Další informace o [Azure Monitoru](../../azure-monitor/index.yml).
- Přečtěte si, jak [zobrazit a spravovat nabídky poskytovatelů služeb](view-manage-service-providers.md) na webu Azure Portal.