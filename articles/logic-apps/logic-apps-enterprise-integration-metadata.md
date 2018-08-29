---
title: Správa integrace účtu artefaktu metadat – Azure Logic Apps | Dokumentace Microsoftu
description: Přidat nebo načíst metadata artefaktů z účtů pro integraci v Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 02/23/2018
ms.openlocfilehash: 537014c2780fe94cfb35806759f8bcbd974c4c95
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128799"
---
# <a name="manage-artifact-metadata-from-integration-accounts-in-azure-logic-apps-with-enterprise-integration-pack"></a>Správa artefaktů metadat z účtů pro integraci v Azure Logic Apps sadou Enterprise Integration Pack

Můžete definovat vlastních metadat pro artefakty v integračních účtů a načtení těchto metadat za běhu pro vaši aplikaci logiky. Můžete například zadat metadat pro artefakty, jako jsou partneři, smlouvy, schémat a mapy – všechna metadata úložiště pomocí páry klíč hodnota. 

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Přidat metadata do artefaktů v účty pro integraci

1. Na webu Azure Portal vytvořit [účtu pro integraci](logic-apps-enterprise-integration-create-integration-account.md).

2. Přidání artefaktu do účtu pro integraci, například, [partnera](logic-apps-enterprise-integration-partners.md), [smlouvy](logic-apps-enterprise-integration-agreements.md), nebo [schématu](logic-apps-enterprise-integration-schemas.md).

3. Vyberte prosím artefakt, zvolte **upravit**a zadejte podrobnosti o metadata.

   ![Zadejte metadat](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>Načíst metadata z artefaktů pro logic apps

1. Na portálu Azure vytvořit [aplikace logiky](quickstart-create-first-logic-app-workflow.md).

2. Vytvoření [odkaz z aplikace logiky do účtu pro integraci](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

3. V návrháři aplikace logiky přidávat aktivační události jako **žádosti** nebo **HTTP** do aplikace logiky.

4. Pod triggerem zvolte **nový krok** > **přidat akci**. Vyhledejte "účet pro integraci", aby mohla najít a pak vyberte tuto akci: **účet pro integraci – vyhledání artefaktu účtu pro integraci**

   ![Vyberte vyhledání artefaktu účtu pro integraci](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Vyberte **Typ artefaktu** a poskytnout **název artefaktu**. Příklad:

   ![Vyberte typ artefaktu a zadejte název artefaktu](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Příklad: Načtení partnera metadat

Předpokládejme, že tohoto partnera se tato metadata s `routingUrl` podrobnosti:

![Najít partnera "routingURL" metadata](media/logic-apps-enterprise-integration-metadata/image6.png)

1. Ve vaší aplikaci logiky přidáte trigger, **účet pro integraci – vyhledání artefaktu účtu pro integraci** akce pro vašeho partnera a **HTTP** akce, například:

   ![Přidání triggeru, vyhledání artefaktu a akce HTTP do aplikace logiky](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Chcete-li získat identifikátor URI, na panelu nástrojů návrháře aplikace logiky zvolte **zobrazení kódu** pro vaši aplikaci logiky. Definici aplikace logiky by měl vypadat jako v tomto příkladu:

   ![Hledání vyhledávání](media/logic-apps-enterprise-integration-metadata/image5.png)

## <a name="next-steps"></a>Další postup

* [Další informace o smlouvy](logic-apps-enterprise-integration-agreements.md)
