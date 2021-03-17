---
title: Výstrahy protokolu aktivit v Azure Monitor
description: Informujte prostřednictvím SMS, Webhooku, SMS, e-mailu a dalších akcí, když dojde k určitým událostem v protokolu aktivit.
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 2762a9fbeef516d62067b670b14ea54f4363d7fc
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102045492"
---
# <a name="alerts-on-activity-log"></a>Upozornění na protokol aktivit

## <a name="overview"></a>Přehled

Výstrahy protokolu aktivit jsou výstrahy, které se aktivují, když dojde k nové [události protokolu aktivit](../essentials/activity-log-schema.md) , která odpovídá podmínkám zadaným ve výstraze. Na základě pořadí a objemu událostí zaznamenaných v [protokolu aktivit Azure](../essentials/platform-logs-overview.md)se pravidlo výstrahy aktivuje. Pravidla upozornění protokolu aktivit jsou prostředky Azure, aby je bylo možné vytvořit pomocí šablony Azure Resource Manager. Také je možné je vytvořit, aktualizovat nebo odstranit v Azure Portal. Tento článek představuje koncepty za výstrahami protokolu aktivit. Další informace o vytváření a používání pravidel upozornění protokolů aktivit najdete v tématu [vytváření a správa výstrah protokolu aktivit](alerts-activity-log.md).

> [!NOTE]
> * Pro události v kategorii výstrahy protokolu aktivit **nelze** vytvořit výstrahy.
> * Výstrahy protokolu aktivit s kategorií zabezpečení je možné definovat taky v [novém upgradovaném toku](../../security-center/continuous-export.md?tabs=azure-portal) na [ServiceNow](../../security-center/export-to-siem.md) .

Obvykle vytváříte výstrahy protokolu aktivit pro příjem oznámení v těchto případech:

* K určitým operacím dochází u prostředků ve vašem předplatném Azure, které jsou často vymezeny na konkrétní skupiny prostředků nebo prostředky. Například může být vhodné upozornit, když se odstraní libovolný virtuální počítač v myProductionResourceGroup. Nebo můžete chtít být upozorněni, pokud jsou nové role přiřazeny uživateli v rámci vašeho předplatného.
* Dojde k události služby Health Service. Události služby Service Health zahrnují oznámení o incidentech a událostech údržby, které se vztahují na prostředky v rámci vašeho předplatného.

Jednoduchá analogie pro porozumění podmínkám, na kterých je možné vytvořit pravidla výstrah v protokolu aktivit, je prozkoumat nebo filtrovat události prostřednictvím [protokolu aktivit v Azure Portal](../essentials/activity-log.md#view-the-activity-log). V protokolu Azure Monitor-Activity může jedna filtrovat nebo najít nezbytnou událost a pak vytvořit výstrahu pomocí tlačítka **Přidat upozornění protokolu aktivit** .

V obou případech se výstraha protokolu aktivit monitoruje jenom pro události v předplatném, ve kterém je tato výstraha vytvořená.

Výstrahu protokolu aktivit můžete nakonfigurovat na základě libovolné vlastnosti nejvyšší úrovně v objektu JSON pro událost protokolu aktivit. Další informace najdete v tématu [kategorie v protokolu aktivit](../essentials/activity-log.md#view-the-activity-log). Další informace o událostech stavu služby najdete v tématu [příjem výstrah protokolu aktivit v oznámeních o službě](../../service-health/alerts-activity-log-service-notifications-portal.md). 

Výstrahy protokolu aktivit mají několik běžných možností:

- **Kategorie**: administrativní, Service Health, automatické škálování, zabezpečení, zásady a doporučení. 
- **Scope (obor**): jednotlivý prostředek nebo sada prostředků, pro které je definovaná výstraha protokolu aktivit. Rozsah upozornění protokolu aktivit lze definovat na různých úrovních:
    - Úroveň prostředku: například pro konkrétní virtuální počítač
    - Úroveň skupiny prostředků: třeba všechny virtuální počítače v určité skupině prostředků
    - Úroveň předplatného: například všechny virtuální počítače v předplatném (nebo) všechny prostředky v rámci předplatného
- **Skupina prostředků**: ve výchozím nastavení se pravidlo výstrahy uloží do stejné skupiny prostředků, která je v cíli definovaná v oboru. Uživatel může také definovat skupinu prostředků, ve které se má pravidlo výstrahy ukládat.
- **Typ prostředku**: Správce prostředků definovaný obor názvů pro cíl výstrahy.
- **Název operace**: název [operace poskytovatele prostředků Azure](../../role-based-access-control/resource-provider-operations.md) , který se používá pro řízení přístupu na základě role Azure. Operace, které nejsou zaregistrované u Azure Resource Manager, se nedají použít v pravidle upozornění protokolu aktivit.
- **Level**: úroveň závažnosti události (informativní, varovná, chyba nebo kritická).
- **Stav**: stav události, obvykle spuštěno, selhalo nebo proběhlo úspěšně.
- **Událost iniciovaná**: taky se označuje jako volající. E-mailová adresa nebo Azure Active Directory identifikátor uživatele, který operaci provedl.

> [!NOTE]
> V rámci předplatného můžete vytvořit pravidla výstrah až 100, a to buď na jeden prostředek, všechny prostředky ve skupině prostředků (nebo) na celé úrovni předplatného.

Když se aktivuje výstraha protokolu aktivit, vygeneruje akce nebo oznámení pomocí skupiny akcí. Skupina akcí je opakovaně použitelná sada přijímačů oznámení, jako jsou e-mailové adresy, adresy URL Webhooku nebo telefonní čísla SMS. Na přijímače se dá odkazovat z několika výstrah, abyste mohli centralizovat a seskupovat kanály oznámení. Při definování upozornění protokolu aktivit máte dvě možnosti. Další možnosti:

* V upozornění protokolu aktivit použijte existující skupinu akcí.
* Vytvořte novou skupinu akcí.

Další informace o skupinách akcí naleznete v tématu [Create and Manage Action Groups in the Azure Portal](./action-groups.md).


## <a name="next-steps"></a>Další kroky

- Získejte [Přehled výstrah](./alerts-overview.md).
- Přečtěte si o [vytváření a úpravách výstrah protokolu aktivit](alerts-activity-log.md).
- Zkontrolujte [schéma Webhooku upozornění protokolu aktivit](../alerts/activity-log-alerts-webhook.md).
- Přečtěte si o [oznámeních o stavu služby](../../service-health/service-notifications.md).