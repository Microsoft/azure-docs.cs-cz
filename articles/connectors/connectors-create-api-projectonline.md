---
title: Připojení k Projectu Online z aplikací Azure Logic Apps
description: Automatizace pracovních postupů, které monitorují, vytvářejí a spravují projekty, úkoly a prostředky Project Online pomocí aplikací Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 116922e018410f260b33f4dfd76e5983fbbcc0a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789320"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Správa projektů, úkolů a prostředků Projectu Online pomocí aplikací Azure Logic Apps

S Aplikacemi logiky Azure a konektorem Projectu Online můžete vytvářet automatizované úkoly a pracovní postupy pro své projekty, úkoly a zdroje v Projectu Online prostřednictvím Office 365. Vaše pracovní postupy mohou provádět tyto a jiné akce, například:

* Sledujte, kdy jsou vytvořeny nové projekty, úkoly nebo zdroje. Nebo sledovat, kdy jsou publikovány nové projekty.
* Vytvořte nové projekty, úkoly nebo zdroje.
* Seznam existujících projektů nebo úkolů.
* Rezervovat, vrácení se změnami nebo publikovat projekty.

Project Online vám pomůže plánovat, určovat priority a spravovat projekty a investice do portfolia projektů téměř odkudkoli na téměř jakémkoli zařízení tím, že poskytuje výkonné možnosti řízení projektů. Můžete použít aktivační události Projectu Online, které získají odpovědi z Projectu Online a zpřístupní výstup dalším akcím. Akce v aplikacích logiky můžete použít k provádění různých úkolů v Projectu Online. Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Project Online dostupný prostřednictvím [účtu Office 365](https://www.office.com/), 

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete získat přístup k datům Projectu Online. Chcete-li začít aktivační událostí Projectu Online, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akce Projectu Online, spusťte aplikaci logiky s jinou aktivační událostí, například aktivační událostí **Opakování.**

## <a name="connect-to-project-online"></a>Připojení k Projectu Online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. Zvolte cestu: 

   * U prázdných aplikací logiky zadejte do vyhledávacího pole jako filtr "Project Online". 
   V seznamu aktivačních událostí vyberte požadovanou aktivační událost. 

     -nebo-

   * U existujících aplikací logiky v yberte v kroku, kde chcete přidat akci, **nový krok**. Do vyhledávacího pole zadejte jako filtr "Project Online". V seznamu akcí vyberte požadovanou akci.

1. Pokud se zobrazí výzva k přihlášení k Projectu Online, přihlaste se hned.

   Vaše přihlašovací údaje autorizují vaši aplikaci logiky k vytvoření připojení k Projectu Online a přístupu k vašim datům.

1. Poskytněte potřebné podrobnosti pro vybranou aktivační událost nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a limitech, které jsou popsány v popisu openapi (dříve Swagger) konektoru, projděte referenční [stránku konektoru](/connectors/projectonline/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)