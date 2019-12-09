---
title: Zobrazit aktivitu poskytovatele služeb
description: Zákazníci si můžou zobrazit protokolované aktivity a zobrazit akce provedené poskytovateli služeb prostřednictvím delegované správy prostředků Azure.
ms.date: 12/6/2019
ms.topic: conceptual
ms.openlocfilehash: 69517e942aa9f82be16fa3d0e7d6f9252de44d4c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932681"
---
# <a name="view-service-provider-activity"></a>Zobrazit aktivitu poskytovatele služeb

Zákazníci, kteří mají delegované předplatné pro správu delegovaných prostředků Azure, můžou [Zobrazit data protokolu aktivit Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview) , aby viděli všechny akce, které provedli. To zákazníkům poskytuje kompletní přehled o operacích, které poskytovatelé služeb provádějí prostřednictvím správy delegovaných prostředků Azure, spolu s činnostmi prováděnými uživateli v rámci vlastního klienta Azure Active Directory (Azure AD) zákazníka.

## <a name="view-activity-log-data"></a>Zobrazit data protokolu aktivit

[Protokol aktivit můžete zobrazit](https://docs.microsoft.com/azure/azure-monitor/platform/activity-logs-overview#view-the-activity-log) v nabídce **monitorování** v Azure Portal. Pokud chcete výsledky omezit na konkrétní předplatné, můžete použít filtry k výběru konkrétního předplatného. [Události protokolu aktivit můžete také zobrazit a načíst](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view) programově.

> [!NOTE]
> Uživatelé v tenantovi poskytovatele služeb můžou zobrazit výsledky protokolu aktivit delegovaného předplatného v tenantovi zákazníka, pokud jim byla udělena role [Čtenář](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) (nebo jiná předdefinovaná role, která zahrnuje přístup čtenářů) při registraci tohoto předplatného pro správu delegovaných prostředků Azure.

V protokolu aktivit uvidíte název operace a její stav spolu s datem a časem, kdy byla provedena. **Událost iniciovaná ve** sloupci zobrazuje, který uživatel provedl operaci, ať už se jedná o uživatele v tenantovi poskytovatele služeb, který působí prostřednictvím správy delegovaných prostředků Azure, nebo uživatel ve vlastním tenantovi zákazníka. Všimněte si, že se zobrazuje jméno uživatele, nikoli tenant nebo role, které uživatel přiřadil k tomuto předplatnému.

Protokolovaná aktivita je v Azure Portal k dispozici po dobu posledních 90 dnů. Informace o tom, jak ukládat tato data déle než 90 dní, najdete v tématu [shromáždění a analýza protokolů aktivit Azure v Log Analytics pracovním prostoru v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect)

## <a name="set-alerts-for-critical-operations"></a>Nastavení výstrah pro kritické operace

Abyste měli přehled o důležitých operacích, které poskytovatelé služeb (nebo uživatelé ve vašem tenantovi) provádějí, doporučujeme vytvořit [výstrahy protokolu aktivit](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-alerts). Můžete například chtít sledovat všechny akce správy pro předplatné nebo upozornit, když dojde k odstranění libovolného virtuálního počítače v určité skupině prostředků. Když vytváříte výstrahy, budou zahrnovat akce provedené uživateli ve vlastním tenantovi zákazníka i na všech spravovaných klientech.

Další informace najdete v tématu [vytváření a správa výstrah protokolu aktivit](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log).

## <a name="create-log-queries"></a>Vytváření dotazů protokolu

Můžete vytvářet dotazy k analýze protokolované aktivity nebo zaměření na konkrétní položky. Například audit vyžaduje, abyste nahlásili všechny akce na úrovni správy provedené v rámci předplatného. Můžete vytvořit dotaz pro filtrování pouze těchto akcí a seřadit výsledky podle uživatele, datum nebo jiné hodnoty.

Další informace najdete v tématu [Přehled dotazů protokolu v Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview).

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/).
- Naučte se, jak [Zobrazit a spravovat nabídky poskytovatele služeb](view-manage-service-providers.md) v Azure Portal.