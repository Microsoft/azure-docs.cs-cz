---
title: Správa integrace účet artefaktu metadat - Azure Logic Apps | Microsoft Docs
description: Přidat nebo načíst metadata artefaktů z účtů integrace pro Azure Logic Apps
author: padmavc
manager: anneta
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 02/23/2018
ms.author: LADocs; padmavc
ms.openlocfilehash: 8ebca60c21366cddadccfd0456880696457d7777
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
---
# <a name="manage-artifact-metadata-in-integration-accounts-for-logic-apps"></a>Spravovat artefaktu metadat v účtech integrace pro logic apps

Můžete definovat vlastní metadata pro artefakty v účty pro integraci a načtení tohoto metadat při běhu pro svou aplikaci logiky. Můžete například zadat metadata pro artefaktů, jako jsou partnery, smlouvy, schémat a mapy - všechna metadata úložiště pomocí páry klíč hodnota. 

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Přidat metadata do artefakty na účty pro integraci

1. Na portálu Azure vytvořit [integrace účet](logic-apps-enterprise-integration-create-integration-account.md).

2. Například přidejte artefakt ke svému účtu integrace, [partnera](logic-apps-enterprise-integration-partners.md), [smlouvy](logic-apps-enterprise-integration-agreements.md), nebo [schématu](logic-apps-enterprise-integration-schemas.md).

3. Vyberte artefaktu, zvolte **upravit**a zadejte podrobnosti o metadata.

   ![Zadejte metadat](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>Načtení metadat z artefaktů pro logic apps

1. Na portálu Azure vytvořit [aplikace logiky](quickstart-create-first-logic-app-workflow.md).

2. Vytvoření [odkaz z aplikace logiky k vašemu účtu integrace](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

3. Návrhář aplikace na základě logiky, přidejte aktivační událost jako **požadavku** nebo **HTTP** do aplikace logiky.

4. V části aktivační událost, zvolte **nový krok** > **přidat akci**. Vyhledejte "integrace účet", můžete najít a pak vyberte tuto akci: **integrace účet – integrace vyhledávání artefaktů účtu**

   ![Vyberte integrační účet artefaktů vyhledávání](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Vyberte **typu artefaktu** a poskytují **artefaktů název**. Příklad:

   ![Vyberte typ artefaktů a zadejte název artefaktů](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Příklad: Načtení partnera metadat

Předpokládejme, že tento partner má tato metadata s `routingUrl` podrobnosti:

![Vyhledejte "routingURL" metadata partnera.](media/logic-apps-enterprise-integration-metadata/image6.png)

1. V aplikaci logiky přidat aktivační událost, **integrace účet – integrace vyhledávání artefaktů účtu** akce pro váš partner a **HTTP** akce, například:

   ![Přidání do aplikace logiky aktivační událost, artefaktů vyhledávání a akce HTTP](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Chcete-li získat identifikátor URI, na panelu nástrojů návrháře aplikace logiky, zvolte **zobrazení kódu** pro svou aplikaci logiky. Vaše definici aplikace logiky by měl vypadat jako tento ukázkový:

   ![Hledání vyhledávání](media/logic-apps-enterprise-integration-metadata/image5.png)

## <a name="next-steps"></a>Další postup

* [Další informace o smlouvy](logic-apps-enterprise-integration-agreements.md)
