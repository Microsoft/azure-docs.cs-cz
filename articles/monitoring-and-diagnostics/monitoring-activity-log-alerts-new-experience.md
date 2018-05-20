---
title: Vytvořit aktivitu protokolu výstrahy a spravovat je pomocí nové výstrahy prostředí v Azure monitorování | Microsoft Docs
description: Tento článek obsahuje informace o tom, jak vytvořit aktivitu protokolu výstrahy na kartě výstrahy v části monitorování Azure
author: JYOTHIRMAISURI
manager: vvithal
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: aabc0e57-78cd-44dd-a8d1-af5e1e567360
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/05/2018
ms.author: v-jysur
ms.custom: ''
ms.openlocfilehash: 070acc6fb6f6f744474edeef0e914dda9d24c558
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="create-activity-log-alerts-using-the-new-alerts-experience"></a>Vytvoření protokol aktivit, které prostředí výstrah pomocí nové výstrahy

Výstrahy protokolu aktivit jsou výstrahy, které získat aktivovat, pokud dojde k nové události protokolování aktivity, která odpovídá podmínkám uvedeném ve výstraze.

Tyto výstrahy jsou pro prostředky Azure, lze vytvořit pomocí šablony Azure Resource Manager. Také mohou být vytvořeny, aktualizovat nebo odstranit na portálu Azure. Tento článek představuje koncepty za aktivitu protokolu výstrahy. Potom ukazuje, jak pomocí portálu Azure nastavit upozornění na aktivitu protokolu události pomocí nové prostředí v [Azure výstrahy](monitoring-overview-unified-alerts.md).

Obvykle se vytvořit aktivitu protokolu výstrahy pro příjem oznámení, když dojde k určité změny na prostředky ve vašem předplatném Azure často obor k určitému prostředku skupiny nebo prostředku. Například můžete chtít být upozorněni, když některý virtuální počítač v (ukázka skupiny prostředků) **myProductionResourceGroup** odstraněna, nebo můžete chtít dostat upozornění, pokud žádné nové role přiřazené uživateli v rámci vašeho předplatného.

Můžete nakonfigurovat výstrahu protokolu aktivity podle libovolné vlastnosti nejvyšší úrovně v objektu JSON pro aktivitu protokolu události. Na portálu však zobrazuje nejběžnější možnosti podle popisu v následujících částech:

>[!NOTE]

> Pokud kategorie "správce", zadejte alespoň jeden z předchozí kritéria v upozornění. Nelze vytvořit výstrahu, která aktivuje pokaždé, když dojde k vytvoření události v protokolech aktivity.
>

Když se aktivuje výstrahu protokolu aktivit, používá skupinu akcí ke generování akcí nebo oznámení. Skupinu akcí je opakovaně použitelné sadu příjemců oznámení, jako je například e-mailové adresy, adresy URL webhooku nebo SMS telefonních čísel. Příjemci lze odkazovat z více výstrah a centralizovat skupiny vaší kanály oznámení. Když definujete výstrahu protokolu aktivit, máte dvě možnosti. Můžete:

* Použijte existující skupinu akce v protokolu upozornění aktivity.
* Vytvořte novou skupinu akce.

Další informace o skupinách akce najdete v tématu [vytvořit a spravovat skupiny akce na portálu Azure](monitoring-action-groups.md).

Další informace o oznámení o stavu služby najdete v tématu [výstrahy v protokolu aktivit na oznámení o stavu služby](monitoring-activity-log-alerts-on-service-notifications.md).


## <a name="whats-new-in-alerts-for-activity-logs"></a>Co je nového ve výstrahách protokoly aktivity?

[Azure výstrahy](monitoring-overview-unified-alerts.md) teď poskytuje lepší možnosti uživatele pro aktivitu protokolu výstrahy. Pomocí [rozšířené možnosti uživatelského prostředí pro výstrahy](monitoring-overview-unified-alerts.md), teď můžete:

- [Vytvoření](#create-an-alert-rule-for-an-activity-log) a [spravovat](#view-and-manage-activity-log-alert-rules) aktivity protokolu pravidla výstrah, z **monitorování** > **výstrahy** okno. Další informace o [protokoly aktivity](monitoring-overview-activity-logs.md).

- **Nové možnosti pro cíl výstrahy**: při vytváření nové pravidlo výstrahy protokolu aktivitu, můžete nyní vybrat cílový prostředek nebo skupinu prostředků nebo předplatného.


## <a name="create-an-alert-rule-for-an-activity-log"></a>Vytvořit pravidlo výstrahy pro protokol činnosti

> [!NOTE]

>  Při vytváření pravidla výstrah, ověřte následující:

> - Předplatné v oboru není liší od předplatné, kde se má vytvořit výstrahu.
- Kritéria musí být úroveň nebo stav nebo volající nebo skupinu prostředků nebo id prostředku / typ prostředku nebo kategorie události, na kterém je nakonfigurovaný výstrahy.
- Neexistuje žádné podmínky "anyOf" nebo vnořené podmínky v konfiguraci výstrahy JSON (v zásadě platí, pouze jeden allOf je povolen se žádné další allOf/anyOf).


Použijte následující postup:

1. Portál Azure, vyberte **monitorování** > **výstrahy**
2. Klikněte na tlačítko **nové pravidlo výstrahy** v horní části **výstrahy** okno.

     ![nové pravidlo výstrahy](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule.png)

     **Vytvořit pravidlo** se zobrazí v okně.

      ![nové možnosti pravidlo výstrahy](./media/monitoring-activity-log-alerts-new-experience/create-new-alert-rule-options.png)

3. **V části zadejte výstražný stav** zadejte následující informace a klikněte na **provádí**.

    - **Výstrahy cíl:** k zobrazení a vyberte cíl pro nové výstrahy, použijte **filtrovat podle předplatného** / **filtrovat podle typu prostředku** a vyberte prostředek nebo skupina prostředků z Zobrazí seznam.

    > [!NOTE]

    > můžete vybrat prostředek, skupinu prostředků nebo celé předplatné.

    **Ukázkové zobrazení výstrah cíl**

     ![Vyberte cíl](./media/monitoring-activity-log-alerts-new-experience/select-target.png)

    - V části **kritéria cílové**, klikněte na tlačítko **přidat kritéria** vyberte typ signál jako **protokol aktivit**.

    - Vyberte ze seznamu zobrazí signál.

    Můžete vybrat časové ose historie protokolu a odpovídající výstrahy logiku pro tento cíl signál:

    **Přidat kritéria obrazovky**

    ![Přidat kritéria](./media/monitoring-activity-log-alerts-new-experience/add-criteria.png)

    **Historie čas**: během posledních 6/12/24 hodin, přes minulého týdne.

    **Výstrahy logiku**:

     - **Úroveň události**-úroveň závažnosti události. **Podrobné, informační, upozornění, chyby**, nebo **kritické**.
     - **Stav**: stav události. **Spuštění, se nezdařilo**, nebo **úspěšné**.
     - **Událost iniciovaná**: známé taky jako volající; E-mailovou adresu nebo identifikátor Azure Active Directory uživatele, který provádí operaci.

        **Ukázka signál grafu se výstrahy logikou použít** :

        ![ Vybraná kritéria](./media/monitoring-activity-log-alerts-new-experience/criteria-selected.png)

4. V části **definujte pravidla výstrah podrobnosti**, zadejte následující podrobnosti:

    - **Název pravidla výstrahy** – název pro nové pravidlo výstrahy
    - **Popis** – popis nové pravidlo výstrahy
    - **Uložit výstrahy do skupiny prostředků** – vyberte skupinu prostředků, ve které chcete uložit toto nové pravidlo.

5. V části **akce skupiny**, z rozevírací nabídky, zadejte skupinu akce, kterou chcete přiřadit k této nové pravidlo výstrahy. Alternativně [vytvořit novou skupinu akce](monitoring-action-groups.md) a přiřadit nové pravidlo. Chcete-li vytvořit novou skupinu, klikněte na tlačítko **+ nové skupiny**.

6. Chcete-li povolit pravidla po jejím vytvoření, klikněte na tlačítko **Ano** pro **pravidlo povolit po vytvoření** možnost.
7. Klikněte na tlačítko **vytvořit pravidlo výstrahy**.

    Nové pravidlo výstrahy pro aktivitu protokolu se vytvoří a zobrazí potvrzovací zpráva v horní pravé části okna.

    ![ Vytvořit pravidlo výstrahy](./media/monitoring-activity-log-alerts-new-experience/alert-created.png)

    Můžete povolit, zakázat, upravit nebo odstranit pravidlo. [Další informace](#view-and-manage-activity-log-alert-rules) o správě pravidel protokolu aktivit.

## <a name="view-and-manage-activity-log-alert-rules"></a>Zobrazit a spravovat pravidla výstrah protokolu aktivit

1. Z portálu Azure, klikněte na tlačítko **monitorování** > **výstrahy** a klikněte na tlačítko **spravovat pravidla** v levém horním rohu okna.

    ![ Spravovat pravidla výstrah](./media/monitoring-activity-log-alerts-new-experience/manage-alert-rules.png)

    Zobrazí se seznam dostupná pravidla.

2. Vyhledejte pravidlo protokolu aktivity a změnit.

    ![ vyhledat pravidla výstrah protokolu aktivit](./media/monitoring-activity-log-alerts-new-experience/searth-activity-log-rule-to-edit.png)

    Můžete použít k dispozici tyto filtry - **předplatné**, **skupiny prostředků**, nebo **prostředků** vyhledání pravidla aktivity, který chcete upravit.

    > [!NOTE]

    > Můžete upravit pouze **popis** , **cíle kritéria** a **skupiny akcí**.

3.  Vyberte pravidlo a dvakrát klikněte na upravit pravidla možností. Proveďte požadované změny a pak klikněte na **Uložit**.

    ![ Spravovat pravidla výstrah](./media/monitoring-activity-log-alerts-new-experience/activity-log-rule-edit-page.png)

4.  Můžete zakázat, povolit nebo odstranění pravidla. Vyberte příslušnou možnost v horní části okna, po výběru pravidlo podle postupu v kroku 2.


## <a name="next-steps"></a>Další postup

- [Archivu aktivity protokolu výstrahy](monitoring-archive-activity-log.md)
- [Datový proud protokolů událostí služby Event hubs](monitoring-stream-activity-logs-event-hubs.md)
- [Migrace na protokoly aktivity](monitoring-migrate-management-alerts.md)
