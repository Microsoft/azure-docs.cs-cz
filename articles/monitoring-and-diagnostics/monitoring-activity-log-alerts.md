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
ms.openlocfilehash: 5ddf510d50f38ed9aaf742bd06c330e53ffe1391
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992792"
---
# <a name="alerts-on-activity-log"></a>Upozornění v protokolu aktivit 

## <a name="overview"></a>Přehled
Upozornění protokolu aktivit jsou výstrahy, které aktivují, když dojde k vytvoření nové události protokolu aktivit, která odpovídá podmínkám uvedeném ve výstraze. Jsou prostředky Azure, takže je lze vytvořit pomocí šablony Azure Resource Manageru. Jsou také může být vytvořen, aktualizoval nebo odstranil na webu Azure Portal. Tento článek představuje Principy upozornění protokolu aktivit. To potom ukazuje, jak pomocí webu Azure portal nastavit výstrahy na události protokolu aktivit. Další informace o použití najdete v tématu [vytvořit a spravovat upozornění protokolu aktivit](alert-activity-log.md).

> [!NOTE]
> Výstrahy **nemůže** nelze pro události v kategorii výstrah protokolu aktivit

Obvykle je vytvoření upozornění protokolu aktivit k přijímání oznámení při:

* Určité operace, ke kterým došlo u prostředků v předplatném Azure, často obor skupiny konkrétní prostředek nebo prostředky. Například můžete být upozorněni na jakýkoli virtuální počítač v myProductionResourceGroup se odstraní. Nebo můžete chtít vás upozorní, když jsou přiřazeny žádné nové role pro uživatele ve vašem předplatném.
* Vyvolá se událost stavu služby. Události služby Service health zahrnout oznámení událostí a události údržby, které se vztahují k prostředkům ve vašem předplatném.

Jednoduché přirovnání pro vysvětlení podmínky, na kterých se dají vytvořit pravidla upozornění na protokol aktivit je prozkoumat nebo filtrovat události prostřednictvím [protokolu aktivit na webu Azure Portal](monitoring-overview-activity-logs.md#query-the-activity-log-in-the-azure-portal). Ve službě Azure Monitor – protokol aktivit, jeden můžete filtrovat nebo najít potřebné události a pak vytvořte výstrahu pomocí **přidat upozornění protokolu aktivit** tlačítko.

V obou případech se upozornění protokolu aktivit monitoruje pouze pro události v rámci předplatného, ve kterém se vytvoří výstraha.

Můžete nakonfigurovat upozornění protokolu aktivit na základě jakékoli vlastnosti nejvyšší úrovně v objektu JSON pro událost protokolu aktivit. Další informace najdete v tématu [přehled protokolu aktivit Azure](./monitoring-overview-activity-logs.md#categories-in-the-activity-log). Další informace o události služby service health najdete v tématu [dostávat upozornění protokolu aktivit pro oznámení služby](./monitoring-activity-log-alerts-on-service-notifications.md). 

Upozornění protokolu aktivit mít několik běžných možností:

- **Kategorie**: pro správu, služba stavu, automatické škálování, zabezpečení, zásady a doporučení. 
- **Obor**: samostatný prostředek nebo sadu prostředků, pro který je definován upozornění v protokolu aktivit. Obor pro upozornění protokolu aktivit můžete definovat na různých úrovních:
    - Zdrojem pro úroveň: třeba pro konkrétní virtuální počítač
    - Úroveň skupiny prostředků: například všechny virtuální počítače v konkrétní skupině prostředků
    - Úroveň předplatného: například všechny virtuální počítače v předplatném (nebo) všechny prostředky v předplatném
- **Skupina prostředků**: ve výchozím nastavení, pravidlo upozornění se uloží ve stejné skupině prostředků jako cíle definované v oboru. Uživatele můžete také definovat skupiny prostředků, kde by měla být uložena pravidlo upozornění.
- **Typ prostředku**: Resource Manager definovaný obor názvů pro cílovou výstrahu.

- **Název operace**: název operace The Resource Manager Role-Based řízení přístupu.
- **Úroveň**: úroveň závažnosti události (Verbose, informační, upozornění, chyby nebo kritický).
- **Stav**: stav události, obvykle spouštěny, selhaly nebo uspěly.
- **Zahájit událost**: označované také jako "volající." E-mailovou adresu nebo Azure Active Directory identifikátor uživatele, který provedl operaci.

> [!NOTE]
> V rámci předplatného až 100 pravidla upozornění pro aktivitu rozsahu na buď: jeden prostředek, všechny prostředky v prostředku skupiny (nebo) úroveň pro celé předplatné.

Když se aktivuje upozornění protokolu aktivit, používá skupiny akcí k vygenerování akce nebo oznámení. Skupiny akcí je opakovaně použitelné sadu příjemců oznámení, jako jsou e-mailové adresy, adresy URL webhooku, nebo SMS telefonních čísel. Příjemci můžou odkazovat z více výstrah centralizovat a členství kanálů oznámení. Při definování upozornění protokolu aktivit, máte dvě možnosti. Můžete:

* Použijte existující skupinu akcí v upozornění protokolu aktivit.
* Vytvořte novou skupinu akcí.

Další informace o skupinách akcí najdete v tématu [vytvořit a spravovat skupiny akcí na webu Azure Portal](monitoring-action-groups.md).


## <a name="next-steps"></a>Další postup
- Získat [přehled výstrah](monitoring-overview-alerts.md).
- Další informace o [vytvářet a upravovat upozornění protokolu aktivit](alert-activity-log.md).
- Zkontrolujte [schéma webhooku v upozornění protokolu aktivit](monitoring-activity-log-alerts-webhook.md).
- Další informace o [služby oznámení o stavu](monitoring-service-notifications.md).

