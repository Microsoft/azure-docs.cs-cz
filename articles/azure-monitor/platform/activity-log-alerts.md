---
title: Upozornění protokolu aktivit ve službě Azure Monitor
description: Oznámení prostřednictvím serveru SMS, webhooku, SMS, e-mail a další, při výskytu určitých událostí v protokolu aktivit.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: vinagara
ms.component: alerts
ms.openlocfilehash: a09dcfbffb6daa2ae2514c608e88c8dc475c9676
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53581432"
---
# <a name="alerts-on-activity-log"></a>Upozornění v protokolu aktivit 

## <a name="overview"></a>Přehled
Upozornění protokolu aktivit jsou výstrahy, které aktivují, když dojde k vytvoření nové události protokolu aktivit, která odpovídá podmínkám uvedeném ve výstraze. Jsou prostředky Azure, takže je lze vytvořit pomocí šablony Azure Resource Manageru. Jsou také může být vytvořen, aktualizoval nebo odstranil na webu Azure Portal. Tento článek představuje Principy upozornění protokolu aktivit. To potom ukazuje, jak pomocí webu Azure portal nastavit výstrahy na události protokolu aktivit. Další informace o použití najdete v tématu [vytvořit a spravovat upozornění protokolu aktivit](../../azure-monitor/platform/alerts-activity-log.md).

> [!NOTE]
> Výstrahy **nemůže** nelze pro události v kategorii výstrah protokolu aktivit

Obvykle je vytvoření upozornění protokolu aktivit k přijímání oznámení při:

* Určité operace, ke kterým došlo u prostředků v předplatném Azure, často obor skupiny konkrétní prostředek nebo prostředky. Například můžete být upozorněni na jakýkoli virtuální počítač v myProductionResourceGroup se odstraní. Nebo můžete chtít vás upozorní, když jsou přiřazeny žádné nové role pro uživatele ve vašem předplatném.
* Vyvolá se událost stavu služby. Události služby Service health zahrnout oznámení událostí a události údržby, které se vztahují k prostředkům ve vašem předplatném.

Jednoduché přirovnání pro vysvětlení podmínky, na kterých se dají vytvořit pravidla upozornění na protokol aktivit je prozkoumat nebo filtrovat události prostřednictvím [protokolu aktivit na webu Azure Portal](../../azure-monitor/platform/activity-logs-overview.md#query-the-activity-log-in-the-azure-portal). Ve službě Azure Monitor – protokol aktivit, jeden můžete filtrovat nebo najít potřebné události a pak vytvořte výstrahu pomocí **přidat upozornění protokolu aktivit** tlačítko.

V obou případech se upozornění protokolu aktivit monitoruje pouze pro události v rámci předplatného, ve kterém se vytvoří výstraha.

Můžete nakonfigurovat upozornění protokolu aktivit na základě jakékoli vlastnosti nejvyšší úrovně v objektu JSON pro událost protokolu aktivit. Další informace najdete v tématu [přehled protokolu aktivit Azure](./../../azure-monitor/platform/activity-logs-overview.md#categories-in-the-activity-log). Další informace o události služby service health najdete v tématu [dostávat upozornění protokolu aktivit pro oznámení služby](./../../azure-monitor/platform/alerts-activity-log-service-notifications.md). 

Upozornění protokolu aktivit mít několik běžných možností:

- **Kategorie**: Pro správu, služba stavu, automatické škálování, zabezpečení, zásady a doporučení. 
- **Obor**: Samostatný prostředek nebo sadu prostředků, pro který je definován upozornění v protokolu aktivit. Obor pro upozornění protokolu aktivit můžete definovat na různých úrovních:
    - Zdrojem pro úroveň: Například pro konkrétní virtuální počítač
    - Úrovni skupiny prostředků: Například všechny virtuální počítače v konkrétní skupině prostředků
    - Úroveň předplatného: Například všechny virtuální počítače v předplatném (nebo) všechny prostředky v předplatném
- **Skupina prostředků**: Ve výchozím nastavení je pravidlo upozornění uloženy ve stejné skupině prostředků jako cíle definované v oboru. Uživatele můžete také definovat skupiny prostředků, kde by měla být uložena pravidlo upozornění.
- **Typ prostředku**: Resource Manager definovaný obor názvů pro cílovou výstrahu.

- **Název operace**: Název operace řízení přístupu na základě Role Správce prostředků.
- **Úroveň**: Úroveň závažnosti události (Verbose, informační, upozornění, chyby nebo kritický).
- **Stav**: Stav události, obvykle spouštěny, selhaly nebo uspěly.
- **Zahájit událost**: Označované také jako "volající." E-mailovou adresu nebo Azure Active Directory identifikátor uživatele, který provedl operaci.

> [!NOTE]
> V rámci předplatného až 100 pravidla upozornění lze vytvořit pro aktivity z rozsahu na buď: jeden prostředek, všechny prostředky v prostředku skupiny (nebo) úroveň pro celé předplatné.

Když se aktivuje upozornění protokolu aktivit, používá skupiny akcí k vygenerování akce nebo oznámení. Skupiny akcí je opakovaně použitelné sadu příjemců oznámení, jako jsou e-mailové adresy, adresy URL webhooku, nebo SMS telefonních čísel. Příjemci můžou odkazovat z více výstrah centralizovat a členství kanálů oznámení. Při definování upozornění protokolu aktivit, máte dvě možnosti. Můžete:

* Použijte existující skupinu akcí v upozornění protokolu aktivit.
* Vytvořte novou skupinu akcí.

Další informace o skupinách akcí najdete v tématu [vytvořit a spravovat skupiny akcí na webu Azure Portal](../../azure-monitor/platform/action-groups.md).


## <a name="next-steps"></a>Další postup
- Získat [přehled výstrah](../../azure-monitor/platform/alerts-overview.md).
- Další informace o [vytvářet a upravovat upozornění protokolu aktivit](../../azure-monitor/platform/alerts-activity-log.md).
- Zkontrolujte [schéma webhooku v upozornění protokolu aktivit](activity-log-alerts-webhook.md).
- Další informace o [služby oznámení o stavu](../../azure-monitor/platform/service-notifications.md).

