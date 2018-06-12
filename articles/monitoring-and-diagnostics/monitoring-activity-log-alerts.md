---
title: Vytvoření klasického aktivity protokolu výstrahy
description: Informování prostřednictvím serveru SMS, webhooku a e-mailu při určité události v protokolu aktivit.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/18/2017
ms.author: johnkem
ms.component: alerts
ms.openlocfilehash: 84bd82f479ce516152f50d5753e8d91940724c93
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263520"
---
# <a name="create-activity-log-alerts-classic"></a>Vytvořit aktivitu protokolu výstrahy (klasické)

## <a name="overview"></a>Přehled
Výstrahy protokolu aktivit jsou výstrahy, které aktivovat při výskytu nové aktivity protokolu události, která odpovídá podmínkám uvedeném ve výstraze. Jsou prostředky Azure, takže je lze vytvořit pomocí šablony Azure Resource Manager. Také mohou být vytvořeny, aktualizovat nebo odstranit na portálu Azure. Tento článek představuje koncepty za aktivitu protokolu výstrahy. Ji pak ukazuje, jak nastavit výstrahy na aktivity protokolu události pomocí portálu Azure.

> [!NOTE]

>  Nové [výstrahy](monitoring-overview-unified-alerts.md) prostředí nahradili tento postup. Tento článek slouží jako odkaz pro předchozí zkušenosti. [Další informace](monitoring-activity-log-alerts-new-experience.md).

Obvykle můžete vytvořit aktivitu protokolu výstrahy pro příjem oznámení při:

* Určité změny, ke kterým došlo u prostředků ve vašem předplatném Azure často obor pro určitý prostředek skupiny nebo prostředky. Například můžete chtít být upozorněni, když se odstraní všechny virtuální počítače v myProductionResourceGroup. Nebo můžete chtít upozornění, pokud žádné nové role přiřazené uživateli v rámci vašeho předplatného.
* Nastane událost stavu služby. Události stavu služby zahrnují upozornění incidenty a události údržby, které se vztahují k prostředkům ve vašem předplatném.

V obou případech výstrahu protokolu aktivit monitoruje pouze pro události v rámci předplatného, ve kterém se vytvoří výstraha.

Můžete nakonfigurovat výstrahu protokolu aktivity podle libovolné vlastnosti nejvyšší úrovně v objektu JSON pro aktivitu protokolu události. Na portálu však uvádí nejběžnější možnosti:

- **Kategorie**: pro správu, služba stavu, škálování a doporučení. Další informace najdete v tématu [přehled protokolu Azure činnosti](./monitoring-overview-activity-logs.md#categories-in-the-activity-log). Další informace o události stavu služby najdete v tématu [výstrahy v protokolu aktivit na oznámení o službách](./monitoring-activity-log-alerts-on-service-notifications.md).
- **Skupina prostředků**
- **Prostředek**
- **Typ prostředku**
- **Název operace**: název operace The Resource Manager Role-Based řízení přístupu.
- **Úroveň**: úroveň závažnosti události (Verbose, informační, upozornění, chyby nebo kritický).
- **Stav**: stav události, obvykle spuštění, se nezdařilo nebo bylo úspěšné.
- **Událost iniciovaná**: také známé jako "volající." E-mailovou adresu nebo identifikátor Azure Active Directory uživatele, který provádí operaci.

> [!NOTE]
> Pokud kategorie "správce", zadejte alespoň jeden z předchozí kritéria v upozornění. Nelze vytvořit výstrahu, která aktivuje pokaždé, když dojde k vytvoření události v protokolech aktivity.

Když se aktivuje výstrahu protokolu aktivit, používá skupinu akcí ke generování akcí nebo oznámení. Skupinu akcí je opakovaně použitelné sadu příjemců oznámení, jako je například e-mailové adresy, adresy URL webhooku nebo SMS telefonních čísel. Příjemci lze odkazovat z více výstrah a centralizovat skupiny vaší kanály oznámení. Když definujete výstrahu protokolu aktivit, máte dvě možnosti. Můžete:

* Použijte existující skupinu akce v protokolu upozornění aktivity.
* Vytvořte novou skupinu akce.

Další informace o skupinách akce najdete v tématu [vytvořit a spravovat skupiny akce na portálu Azure](monitoring-action-groups.md).

Další informace o oznámení o stavu služby najdete v tématu [výstrahy v protokolu aktivit na oznámení o stavu služby](monitoring-activity-log-alerts-on-service-notifications.md).

## <a name="create-an-alert-classic-on-an-activity-log-event-with-a-new-action-group-by-using-the-azure-portal"></a>Vytvoření (klasické) výstrahu na aktivity protokolu události s novou skupinu akce pomocí portálu Azure
1. V [portál](https://portal.azure.com), vyberte **monitorování**.

    ![Službu "Sledování"](./media/monitoring-activity-log-alerts/home-monitor.png)
2. V **protokol aktivit** vyberte **výstrahy (klasické)**.

    ![Na kartě "Výstrahy"](./media/monitoring-activity-log-alerts/alerts-blades.png)
3. Vyberte **přidat aktivitu protokolu upozornění**a vyplňte příslušná pole.

4. Zadejte název do pole **výstrahy název aktivity protokolu** a vyberte **popis**.

    ![Příkaz "Přidat aktivitu protokolu upozornění"](./media/monitoring-activity-log-alerts/add-activity-log-alert.png)

5. **Předplatné** pole autofills s vaším aktuálním předplatným. Toto předplatné je ten, ve kterém je akce skupinu uložit. Výstrahy prostředek je nasazen na toto předplatné a monitoruje aktivity protokolu události z něj.

    ![Dialogové okno "Přidat aktivitu protokolu upozornění"](./media/monitoring-activity-log-alerts/activity-log-alert-new-action-group.png)

6. Vyberte **skupiny prostředků** v výstrahy prostředek je vytvořen. Nejedná se o skupině prostředků, který je monitorován výstraha. Místo toho je skupina prostředků, kde je umístěný prostředek výstrahy.

7. Volitelně vyberte **kategorie události** upravit další filtry, které jsou zobrazeny. Pro správu události filtry zahrnují **skupiny prostředků**, **prostředků**, **typ prostředku**, **název operace**, **Úroveň**, **stav**, a **iniciovaná událostí**. Tyto hodnoty identifikovat události, které by měly monitorovat této výstrahy.

    >[!NOTE]
    >Musíte zadat alespoň jeden z předchozí kritéria v upozornění. Nelze vytvořit výstrahu, která aktivuje pokaždé, když dojde k vytvoření události v protokolech aktivity.
    >
    >

8. Zadejte název do pole **název skupiny akce** pole a zadejte název do pole **krátký název** pole. Krátký název se použije místo úplného názvu skupiny akcí při odesílání oznámení pomocí této skupiny.

9.  Definujte seznam akcí, tím, že poskytuje akce:

    a. **Název**: Zadejte název akce, alias nebo identifikátor.

    b. **Typ akce**: Vyberte SMS, e-mailu nebo webhooku.

    c. **Podrobnosti o**: v závislosti na typu akce, zadejte telefonní číslo, e-mailovou adresu nebo webhooku identifikátor URI.

10. Vyberte **OK** vytvořit výstrahu.

Výstraha trvá několik minut plně šíří a pak stane aktivní. Aktivuje při nové události kritériím výstrah.

Další informace najdete v tématu [pochopit schéma webhooku použít ve výstrahách aktivity protokolu](monitoring-activity-log-alerts-webhook.md).

>[!NOTE]
>Skupina akce definované v těchto kroků je opakovaně použitelné jako stávající skupina akce pro všechny budoucí výstrahy definice.
>
>

## <a name="create-an-alert-on-an-activity-log-event-for-an-existing-action-group-by-using-the-azure-portal"></a>Vytvořit výstrahu na aktivity protokolu události pro stávající skupinu akce pomocí portálu Azure
1. Postupujte podle kroků 1 až 7 v předchozí části Vytvořit výstrahu protokolu aktivit.

2. V části **oznámit prostřednictvím**, vyberte **existující** tlačítko akce skupiny. Vyberte existující skupinu akce ze seznamu.

3. Vyberte **OK** vytvořit výstrahu.

Výstraha trvá několik minut plně šíří a pak stane aktivní. Aktivuje při nové události kritériím výstrah.

## <a name="manage-your-alerts"></a>Spravovat oznámení

Po vytvoření výstrahy, je viditelný v části výstrahy monitorování okna. Vyberte výstrahy, kterou chcete spravovat:

* Upravte.
* Odstraňte ji.
* Zakázat nebo povolit, pokud chcete dočasně zastavit nebo obnovit příjem oznámení pro výstrahy.

## <a name="next-steps"></a>Další postup
- Získat [přehled výstrah](monitoring-overview-alerts.md).
- Další informace o [omezení rychlosti oznámení](monitoring-alerts-rate-limiting.md).
- Zkontrolujte [schéma výstrahy webhooku protokolu činnosti](monitoring-activity-log-alerts-webhook.md).
- Další informace o [skupiny akcí](monitoring-action-groups.md).  
- Další informace o [oznámení o stavu služby](monitoring-service-notifications.md).
- Vytvoření [výstraha aktivity protokolu monitorovat všechny operace škálování modul vaše předplatné](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert).
- Vytvoření [výstraha aktivity protokolu monitorovat všechny operace škálování nebo škálovatelnou neúspěšné automatické škálování na vaše předplatné](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert).
