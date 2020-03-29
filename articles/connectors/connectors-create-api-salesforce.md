---
title: Připojení k Salesforce z aplikací Azure Logic Apps
description: Automatizace úloh a pracovních postupů, které monitorují, vytvářejí a spravují záznamy a úlohy Salesforce pomocí aplikací Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 000f4381ef2a7c0a2099a021b991087725ff2070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789286"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Sledování, vytváření a správa prostředků Salesforce pomocí aplikací Azure Logic Apps

Pomocí aplikací Azure Logic Apps a konektoru Salesforce můžete vytvářet automatizované úlohy a pracovní postupy pro prostředky Salesforce, jako jsou záznamy, úlohy a objekty, například:

* Sledujte, kdy jsou záznamy vytvořeny nebo změněny. 
* Vytvářejte, přijímejte a spravujte úlohy a záznamy, včetně akcí vkládání, aktualizace a odstraňování.

Můžete použít aktivační události Salesforce, které získají odpovědi ze služby Salesforce a zpřístupní výstup jiným akcím. Akce v aplikacích logiky můžete použít k provádění úkolů s prostředky Salesforce. Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* [Účet Salesforce](https://salesforce.com/)

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde chcete získat přístup k účtu Salesforce. Chcete-li začít s aktivační událostí Salesforce, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li použít akci Salesforce, spusťte aplikaci logiky s jinou aktivační událostí, například aktivační událostí **Opakování.**

## <a name="connect-to-salesforce"></a>Připojte se k Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. Zvolte cestu: 

   * U prázdných aplikací logiky zadejte do vyhledávacího pole jako filtr "salesforce". 
   V seznamu aktivačních událostí vyberte požadovanou aktivační událost. 

     -nebo-

   * U existujících aplikací logiky v yberte v kroku, kde chcete přidat akci, **nový krok**. Do vyhledávacího pole zadejte jako filtr "salesforce". V seznamu akcí vyberte požadovanou akci.

1. Pokud se zobrazí výzva k přihlášení ke společnosti Salesforce, přihlaste se a povolte přístup.

   Vaše přihlašovací údaje autorizují vaši aplikaci logiky k vytvoření připojení k Salesforce a přístupu k vašim datům.

1. Poskytněte potřebné podrobnosti pro vybranou aktivační událost nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a limitech, které jsou popsány v popisu openapi (dříve Swagger) konektoru, projděte referenční [stránku konektoru](/connectors/salesforce/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)