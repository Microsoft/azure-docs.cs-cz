---
title: Výstrahy protokolu aktivit ve službě Azure Monitor
description: Být upozorněni prostřednictvím SMS, webhook, SMS, e-mail a další, když některé události se vyskytují v protokolu aktivit.
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 26ecfdb33b92c91010af63ec14089dd148d6bad0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77669009"
---
# <a name="alerts-on-activity-log"></a>Upozornění na protokol aktivit

## <a name="overview"></a>Přehled

Výstrahy protokolu aktivit jsou výstrahy, které se aktivují, když dojde k nové [události protokolu aktivit,](activity-log-schema.md) která odpovídá podmínkám zadaným ve výstraze. Na základě pořadí a objemu událostí zaznamenaných v [protokolu aktivit Azure](platform-logs-overview.md)se pravidlo výstrahy zapálí. Pravidla výstrah protokolu aktivit jsou prostředky Azure, takže je můžete vytvořit pomocí šablony Azure Resource Manager. Taky je dá vytvořit, aktualizovat nebo odstranit na webu Azure Portal. Tento článek představuje koncepty za výstrahy protokolu aktivit. Další informace o vytváření nebo používání pravidel výstrah protokolu aktivit naleznete v tématu [Vytváření a správa výstrah protokolu aktivit](alerts-activity-log.md).

> [!NOTE]
> Výstrahy **nelze** vytvořit pro události v kategorii výstrahy protokolu aktivit.

Obvykle vytváříte výstrahy protokolu aktivit pro příjem oznámení, když:

* Konkrétní operace se vyskytují u prostředků ve vašem předplatném Azure, často s rozsahem na konkrétní skupiny prostředků nebo prostředky. Můžete například chtít být upozorněni při odstranění libovolného virtuálního počítače v myProductionResourceGroup. Nebo můžete chtít být upozorněni, pokud jsou nějaké nové role přiřazeny uživateli ve vašem předplatném.
* Dojde k události stavu služby. Události stavu služby zahrnují oznámení o incidentech a událostech údržby, které se vztahují na prostředky ve vašem předplatném.

Jednoduchá analogie pro pochopení podmínek, na kterých se mohou vytvářet pravidla výstrah v protokolu aktivit, je prozkoumat nebo filtrovat události prostřednictvím [protokolu aktivit na webu Azure Portal](activity-log-view.md#azure-portal). V Azure Monitor – protokol aktivit, jeden můžete filtrovat nebo najít potřebnou událost a potom vytvořit výstrahu pomocí tlačítka **Přidat protokol aktivit.**

V obou případech výstraha protokolu aktivit monitoruje pouze události v předplatném, ve kterém je výstraha vytvořena.

Můžete nakonfigurovat výstrahu protokolu aktivit na základě libovolné vlastnosti nejvyšší úrovně v objektu JSON pro událost protokolu aktivit. Další informace naleznete [v tématu Kategorie v protokolu aktivit](activity-log-view.md#categories-in-the-activity-log). Další informace o událostech stavu služby najdete v [tématu Příjem upozornění protokolu aktivit na oznámení služby](alerts-activity-log-service-notifications.md). 

Výstrahy protokolu aktivit mají několik běžných možností:

- **Kategorie:** Správa, Stav služby, Automatické škálování, Zabezpečení, Zásady a Doporučení. 
- **Obor**: Jednotlivé prostředky nebo sady prostředků, pro které je definována výstraha v protokolu aktivit. Obor výstrahy protokolu aktivit lze definovat na různých úrovních:
    - Úroveň prostředků: Například pro konkrétní virtuální počítač
    - Úroveň skupiny prostředků: Například všechny virtuální počítače v určité skupině prostředků
    - Úroveň předplatného: Například všechny virtuální počítače v předplatném (nebo) všechny prostředky v předplatném
- **Skupina prostředků**: Ve výchozím nastavení je pravidlo výstrahy uloženo ve stejné skupině prostředků jako cíl definovaný v oboru. Uživatel může také definovat skupinu prostředků, kde by mělo být uloženo pravidlo výstrahy.
- **Typ prostředku**: Obor názvů definovaný správcem prostředků pro cíl výstrahy.
- **Název operace**: Název [operace Správce prostředků Azure](../../role-based-access-control/resource-provider-operations.md) používaný pro řízení přístupu na základě rolí . Operace, které nejsou registrované ve Správci prostředků Azure, nelze použít v pravidle výstrahy protokolu aktivit.
- **Úroveň**: Úroveň závažnosti události (Podrobné, Informační, Upozornění, Chyba nebo Kritická).
- **Stav**: Stav události, obvykle spuštěno, neúspěšné nebo úspěšné.
- **Událost iniciovaná**: Také známý jako "volající". E-mailová adresa nebo identifikátor služby Azure Active Directory uživatele, který operaci provedl.

> [!NOTE]
> V předplatném lze vytvořit až 100 pravidel výstrah pro aktivitu oboru buď na: jeden prostředek, všechny prostředky ve skupině prostředků (nebo) celou úroveň předplatného.

Když je aktivována výstraha protokolu aktivit, používá skupinu akcí ke generování akcí nebo oznámení. Skupina akcí je opakovaně použitelná sada příjemců oznámení, jako jsou e-mailové adresy, adresy URL webhooku nebo telefonní čísla SMS. Příjemci mohou odkazovat z více výstrah centralizovat a seskupit kanály oznámení. Když definujete výstrahu protokolu aktivit, máte dvě možnosti. Můžete:

* Ve výdižce protokolu aktivit použijte existující skupinu akcí.
* Vytvořte novou skupinu akcí.

Další informace o skupinách akcí najdete [v tématu Vytváření a správa skupin akcí na webu Azure Portal](action-groups.md).


## <a name="next-steps"></a>Další kroky

- Získejte [přehled o výstrahách](alerts-overview.md).
- Informace o [vytváření a úpravách výstrah protokolu aktivit](alerts-activity-log.md).
- Zkontrolujte [webhookové schéma výstrahy protokolu aktivit](activity-log-alerts-webhook.md).
- Další informace o [oznámeních o stavu služby](service-notifications.md).
