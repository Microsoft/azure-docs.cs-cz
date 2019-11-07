---
title: Shromažďovat protokol aktivit Azure s nastavením diagnostiky (Preview) – Azure Monitor | Microsoft Docs
description: Pomocí nastavení diagnostiky předáte protokoly aktivit Azure, abyste Azure Monitor protokoly, úložiště Azure nebo Azure Event Hubs.
author: bwren
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: bwren
ms.date: 10/31/2019
ms.openlocfilehash: 9f8783dc6d3c14b086364639b60273dbae626cee
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587976"
---
# <a name="collect-azure-activity-log-with-diagnostic-settings-preview"></a>Shromažďování protokolů aktivit Azure s nastavením diagnostiky (Preview)
[Protokol aktivit Azure](activity-logs-overview.md) je [protokol platformy](platform-logs-overview.md) , který poskytuje přehled o událostech na úrovni předplatného, ke kterým došlo v Azure. Dříve jste vytvořili profil protokolu pro posílání položek protokolu aktivit do [centra událostí nebo účtu úložiště](activity-log-export.md) a pomocí konektoru je shromáždíte do [pracovního prostoru Log Analytics](activity-log-collect.md).

Teď můžete konfigurovat shromažďování protokolů aktivit Azure pomocí stejných [nastavení diagnostiky](diagnostic-settings.md) , která se používají ke shromažďování [protokolů prostředků](resource-logs-overview.md). Použití nastavení diagnostiky má oproti aktuálním metodám následující výhody:

- Konzistentní způsob shromažďování všech protokolů platforem.
- Shromažďování protokolů aktivit napříč několika předplatnými a klienty.
- Vyfiltruje shromažďování, aby se shromáždily jenom protokoly pro konkrétní kategorie.

## <a name="considerations"></a>Požadavky
Než tuto funkci povolíte, zvažte následující podrobnosti o shromažďování protokolů aktivit pomocí nastavení diagnostiky.

- Existující kolekci aktivity byste měli zakázat předtím, než ji povolíte pomocí nastavení diagnostiky. U obou povolených může být výsledkem duplicitní data.
- Nastavení uchování pro shromažďování protokolu aktivit do služby Azure Storage bylo odebráno, což znamená, že data budou ukládána po neomezenou dobu, dokud je neodeberete.
- Protokol aktivit můžete odeslat z více předplatných do jednoho Log Analytics pracovního prostoru. Protokol aktivit můžete odeslat z libovolného jednoho předplatného do pěti Log Analyticsch pracovních prostorů.

## <a name="configure-diagnostic-settings"></a>Konfigurace nastavení diagnostiky
Následující postup použijte k vytvoření nastavení diagnostiky v Azure Portal ke shromáždění protokolu aktivit Azure. V současné době nemůžete vytvořit nastavení diagnostiky na úrovni předplatného pomocí jiných metod.

1. Zakázat existující kolekci Log Analyticsho pracovního prostoru pro protokol aktivit:
   1. Otevřete nabídku **Log Analytics pracovní prostory** v Azure Portal a vyberte pracovní prostor pro shromáždění protokolu aktivit.
   2. V části **zdroje dat pracovního prostoru** v nabídce pracovního prostoru vyberte **Protokol aktivit Azure**.
   3. Klikněte na předplatné, které chcete odpojit.
   4. Po zobrazení výzvy k potvrzení výběru klikněte na **Odpojit** a pak na **Ano** .
2. V nabídce **Azure monitor** v Azure Portal vyberte **Protokol aktivit**.
3. Klikněte na **Nastavení diagnostiky**.
   
   ![Nastavení diagnostiky](media/diagnostic-settings-subscription/diagnostic-settings.png)
   
4. Klikněte na fialový banner pro starší verzi a zakažte všechny aktuální kolekce do úložiště nebo Center událostí. 

    ![Starší verze prostředí](media/diagnostic-settings-subscription/legacy-experience.png)

5. Pokud chcete vytvořit nastavení diagnostiky, postupujte podle pokynů v části [Vytvoření nastavení diagnostiky v Azure Portal](diagnostic-settings.md#create-diagnostic-settings-in-azure-portal) . Vysvětlení kategorií, které můžete použít k filtrování událostí z protokolu aktivit, najdete [v tématu kategorie v protokolu aktivit](activity-logs-overview.md#categories-in-the-activity-log) . 


## <a name="differences-in-data"></a>Rozdíly v datech
Nastavení diagnostiky shromažďuje stejná data jako předchozí metody použité ke shromáždění protokolu aktivit s následujícími rozdíly:

Byly odebrány následující vlastnosti:

- ActivityStatus
- ActivitySubstatus
- OperationName
- ResourceProvider 

Byly přidány následující vlastnosti:

- Authorization_d
- Claims_d
- Properties_d

## <a name="activity-log-monitoring-solution"></a>Řešení monitorování protokolu aktivit
Řešení Azure Log Analytics monitoring zahrnuje několik dotazů a zobrazení protokolů pro analýzu záznamů protokolu aktivit ve vašem pracovním prostoru Log Analytics. Toto řešení využívá data protokolu shromážděná v pracovním prostoru Log Analytics a bude i nadále fungovat bez jakýchkoli změn, pokud shromáždíte protokol aktivit pomocí nastavení diagnostiky. Podrobnosti o tomto řešení najdete v tématu [řešení monitorování analýzy protokolů aktivit](activity-log-collect.md#activity-logs-analytics-monitoring-solution) .

## <a name="next-steps"></a>Další kroky

* [Další informace o protokolu aktivit](../../azure-resource-manager/resource-group-audit.md)
* [Další informace o nastavení diagnostiky](diagnostic-settings.md)
