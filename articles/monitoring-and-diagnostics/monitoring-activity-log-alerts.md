---
title: Vytvoření upozornění protokolu aktivit classic
description: Upozorněni prostřednictvím SMS, webhooků a e-mailu při výskytu určitých událostí v protokolu aktivit.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/18/2017
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 120fd3552ad36b3d19179f39ca95ce2b3ee2c2e6
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39426614"
---
# <a name="create-activity-log-alerts-classic"></a>Vytvořit aktivitu protokolu upozornění (klasická)

## <a name="overview"></a>Přehled
Upozornění protokolu aktivit jsou výstrahy, které aktivují, když dojde k vytvoření nové události protokolu aktivit, která odpovídá podmínkám uvedeném ve výstraze. Jsou prostředky Azure, takže je lze vytvořit pomocí šablony Azure Resource Manageru. Jsou také může být vytvořen, aktualizoval nebo odstranil na webu Azure Portal. Tento článek představuje Principy upozornění protokolu aktivit. To potom ukazuje, jak pomocí webu Azure portal nastavit výstrahy na události protokolu aktivit.

> [!NOTE]

>  Nové [výstrahy](monitoring-overview-unified-alerts.md) prostředí nahradil tento postup. Tento článek je uveden jako odkaz na předchozí zkušenosti. [Další informace](monitoring-activity-log-alerts-new-experience.md).

Obvykle je vytvoření upozornění protokolu aktivit k přijímání oznámení při:

* Konkrétní změnách na prostředky ve vašem předplatném Azure, často obor skupiny konkrétní prostředek nebo prostředky. Například můžete být upozorněni na jakýkoli virtuální počítač v myProductionResourceGroup se odstraní. Nebo můžete chtít vás upozorní, když jsou přiřazeny žádné nové role pro uživatele ve vašem předplatném.
* Vyvolá se událost stavu služby. Události služby Service health zahrnout oznámení událostí a události údržby, které se vztahují k prostředkům ve vašem předplatném.

V obou případech se upozornění protokolu aktivit monitoruje pouze pro události v rámci předplatného, ve kterém se vytvoří výstraha.

Můžete nakonfigurovat upozornění protokolu aktivit na základě jakékoli vlastnosti nejvyšší úrovně v objektu JSON pro událost protokolu aktivit. Na portálu se ale zobrazí nejběžnější možnosti:

- **Kategorie**: pro správu, služba stavu, automatické škálování a doporučení. Další informace najdete v tématu [přehled protokolu aktivit Azure](./monitoring-overview-activity-logs.md#categories-in-the-activity-log). Další informace o události služby service health najdete v tématu [dostávat upozornění protokolu aktivit pro oznámení služby](./monitoring-activity-log-alerts-on-service-notifications.md).
- **Skupina prostředků**
- **Prostředek**
- **Typ prostředku**
- **Název operace**: název operace The Resource Manager Role-Based řízení přístupu.
- **Úroveň**: úroveň závažnosti události (Verbose, informační, upozornění, chyby nebo kritický).
- **Stav**: stav události, obvykle spouštěny, selhaly nebo uspěly.
- **Zahájit událost**: označované také jako "volající." E-mailovou adresu nebo Azure Active Directory identifikátor uživatele, který provedl operaci.

> [!NOTE]
> Pokud kategorie "správce", musíte zadat alespoň jeden z předchozích kritéria v upozornění. Nelze vytvořit výstrahu, která se aktivuje vždy, když se vytvoří událost v protokolech aktivit.

Když se aktivuje upozornění protokolu aktivit, používá skupiny akcí k vygenerování akce nebo oznámení. Skupiny akcí je opakovaně použitelné sadu příjemců oznámení, jako jsou e-mailové adresy, adresy URL webhooku, nebo SMS telefonních čísel. Příjemci můžou odkazovat z více výstrah centralizovat a členství kanálů oznámení. Při definování upozornění protokolu aktivit, máte dvě možnosti. Můžete:

* Použijte existující skupinu akcí v upozornění protokolu aktivit.
* Vytvořte novou skupinu akcí.

Další informace o skupinách akcí najdete v tématu [vytvořit a spravovat skupiny akcí na webu Azure Portal](monitoring-action-groups.md).

Další informace o oznámení o stavu služby, najdete v článku [dostávat upozornění protokolu aktivit na oznámení o stavu služby](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="create-an-alert-classic-on-an-activity-log-event-with-a-new-action-group-by-using-the-azure-portal"></a>Vytvořit (classic) výstrahu pro událost protokolu aktivit se nová skupina akcí s využitím webu Azure portal
1. V [portál](https://portal.azure.com)vyberte **monitorování**.

    !["Monitor" service](./media/monitoring-activity-log-alerts/home-monitor.png)
1. V **protokolu aktivit** vyberte **upozornění (klasická)**.

    ![Na kartě "Upozornění"](./media/monitoring-activity-log-alerts/alerts-blades.png)
1. Vyberte **přidat upozornění protokolu aktivit**a přejít k vyplnění polí.

1. Zadejte název do pole **název upozornění protokolu aktivit** a vyberte **popis**.

    ![Příkaz "Přidat upozornění protokolu aktivit"](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

1. **Předplatné** pole autofills s vaším aktuálním předplatným. Toto předplatné je ten, ve kterém se uloží skupiny akcí. Upozornění prostředek nasazuje se do tohoto předplatného a sleduje události protokolu aktivit z něj.

    ![Dialogové okno "Přidat upozornění protokolu aktivit."](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

1. Vyberte **skupiny prostředků** ve kterém výstrah prostředků vytvoří. Nejedná se o skupinu prostředků, který je monitorován pomocí upozornění. Místo toho je skupina prostředků, kde je umístěný prostředek výstrahy.

1. Volitelně můžete vybrat **kategorii událostí** upravit další filtry, které jsou zobrazeny. Pro správu událostí, filtry zahrnují **skupiny prostředků**, **prostředků**, **typ prostředku**, **název operace**, **Úroveň**, **stav**, a **události iniciovaných**. Tyto hodnoty identifikovat události, které toto upozornění by měl monitorovat.

    >[!NOTE]
    >Musíte zadat alespoň jeden z předchozích kritéria v upozornění. Nelze vytvořit výstrahu, která se aktivuje vždy, když se vytvoří událost v protokolech aktivit.
    >
    >

1. Zadejte název do pole **název skupiny akcí** pole a zadejte název do pole **krátký název** pole. Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.

1.  Definujte seznam akcí tím, že poskytuje akce:

    a. **Název**: Zadejte název akce, alias nebo identifikátor.

    b. **Typ akce**: Vyberte zprávu SMS, e-mailu nebo webhooku.

    c. **Podrobnosti o**: založená na typu akce, zadejte telefonní číslo, e-mailovou adresu nebo webhook identifikátoru URI.

1.  Vyberte **OK** k vytvoření upozornění.

Upozornění trvá několik minut plně rozšířily a potom aktivuje. To spustí, když nové položky kritériím výstrah.

Další informace najdete v tématu [pochopení schématu webhooku v upozornění protokolu aktivit](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Skupina akcí, které jsou definované v následujícím postupu je opakovaně použitelný jako existující skupiny akcí pro všechny budoucí upozornění definice.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-by-using-the-azure-portal"></a>Vytvořit výstrahu pro událost protokolu aktivit pro existující skupiny akcí s využitím webu Azure portal
1. Postupujte podle kroků 1 až 7 v předchozí části k vytvoření upozornění protokolu aktivit.

1. V části **upozorňovat prostřednictvím**, vyberte **existující** skupiny tlačítko akce. Ze seznamu vyberte existující skupinu akcí.

1. Vyberte **OK** k vytvoření upozornění.

Upozornění trvá několik minut plně rozšířily a potom aktivuje. To spustí, když nové položky kritériím výstrah.

## <a name="manage-your-alerts"></a>Spravovat upozornění

Po vytvoření upozornění je vidět v okně Monitor část pojednávající o výstrahách. Vyberte výstrahu, kterou chcete spravovat:

* Upravte.
* Odstraňte ho.
* Zakázat nebo povolit, pokud chcete dočasně zastavit nebo obnovit příjem oznámení pro výstrahy.

## <a name="next-steps"></a>Další postup
- Získat [přehled výstrah](monitoring-overview-alerts.md).
- Další informace o [rychlosti oznámení](monitoring-alerts-rate-limiting.md).
- Zkontrolujte [schéma webhooku v upozornění protokolu aktivit](monitoring-activity-log-alerts-webhook.md).
- Další informace o [skupiny akcí](monitoring-action-groups.md).  
- Další informace o [služby oznámení o stavu](monitoring-service-notifications.md).
- Vytvoření [upozornění protokolu aktivit ke sledování všech operací modul automatického škálování v rámci předplatného](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Vytvoření [upozornění protokolu aktivit ke sledování všech operací škálování/Škálováním neúspěšné automatické škálování v rámci předplatného](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).
