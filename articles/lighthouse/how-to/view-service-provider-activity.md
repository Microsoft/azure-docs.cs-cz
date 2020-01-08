---
title: Zobrazení aktivity poskytovatele služeb
description: Zákazníci si můžou zobrazit protokolované aktivity a zobrazit akce provedené poskytovateli služeb prostřednictvím delegované správy prostředků Azure.
ms.date: 12/6/2019
ms.topic: conceptual
ms.openlocfilehash: a6ae5668601e3587b7306ba1652f6b107800fcb3
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75527820"
---
# <a name="view-service-provider-activity"></a>Zobrazení aktivity poskytovatele služeb

Zákazníci, kteří mají delegované předplatné pro správu delegovaných prostředků Azure, můžou [Zobrazit data protokolu aktivit Azure](../../azure-monitor/platform/activity-logs-overview.md) , aby viděli všechny akce, které provedli. To zákazníkům poskytuje kompletní přehled o operacích, které poskytovatelé služeb provádějí prostřednictvím správy delegovaných prostředků Azure, spolu s činnostmi prováděnými uživateli v rámci vlastního klienta Azure Active Directory (Azure AD) zákazníka.

## <a name="view-activity-log-data"></a>Zobrazit data protokolu aktivit

[Protokol aktivit můžete zobrazit](../../azure-monitor/platform/activity-log-view.md) v nabídce **monitorování** v Azure Portal. Pokud chcete výsledky omezit na konkrétní předplatné, můžete použít filtry k výběru konkrétního předplatného. [Události protokolu aktivit můžete také zobrazit a načíst](../../azure-monitor/platform/activity-log-view.md) programově.

> [!NOTE]
> Uživatelé v tenantovi poskytovatele služeb můžou zobrazit výsledky protokolu aktivit delegovaného předplatného v tenantovi zákazníka, pokud jim byla udělena role [Čtenář](../../role-based-access-control/built-in-roles.md#reader) (nebo jiná předdefinovaná role, která zahrnuje přístup čtenářů) při registraci tohoto předplatného pro správu delegovaných prostředků Azure.

V protokolu aktivit uvidíte název operace a její stav spolu s datem a časem, kdy byla provedena. **Událost iniciovaná ve** sloupci zobrazuje, který uživatel provedl operaci, ať už se jedná o uživatele v tenantovi poskytovatele služeb, který působí prostřednictvím správy delegovaných prostředků Azure, nebo uživatel ve vlastním tenantovi zákazníka. Všimněte si, že se zobrazuje jméno uživatele, nikoli tenant nebo role, které uživatel přiřadil k tomuto předplatnému.

Protokolovaná aktivita je v Azure Portal k dispozici po dobu posledních 90 dnů. Informace o tom, jak ukládat tato data déle než 90 dní, najdete v tématu [shromáždění a analýza protokolů aktivit Azure v Log Analytics pracovním prostoru v Azure monitor](../../azure-monitor/platform/activity-log-collect.md)

## <a name="set-alerts-for-critical-operations"></a>Nastavení výstrah pro kritické operace

Abyste měli přehled o důležitých operacích, které poskytovatelé služeb (nebo uživatelé ve vašem tenantovi) provádějí, doporučujeme vytvořit [výstrahy protokolu aktivit](../../azure-monitor/platform/activity-log-alerts.md). Můžete například chtít sledovat všechny akce správy pro předplatné nebo upozornit, když dojde k odstranění libovolného virtuálního počítače v určité skupině prostředků. Když vytváříte výstrahy, budou zahrnovat akce provedené uživateli ve vlastním tenantovi zákazníka i na všech spravovaných klientech.

Další informace najdete v tématu [vytváření a správa výstrah protokolu aktivit](../../azure-monitor/platform/alerts-activity-log.md).

## <a name="create-log-queries"></a>Vytváření dotazů protokolu

Můžete vytvářet dotazy k analýze protokolované aktivity nebo zaměření na konkrétní položky. Například audit vyžaduje, abyste nahlásili všechny akce na úrovni správy provedené v rámci předplatného. Můžete vytvořit dotaz pro filtrování pouze těchto akcí a seřadit výsledky podle uživatele, datum nebo jiné hodnoty.

Další informace najdete v tématu [Přehled dotazů protokolu v Azure monitor](../../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure monitor](../../azure-monitor/index.yml).
- Naučte se, jak [Zobrazit a spravovat nabídky poskytovatele služeb](view-manage-service-providers.md) v Azure Portal.