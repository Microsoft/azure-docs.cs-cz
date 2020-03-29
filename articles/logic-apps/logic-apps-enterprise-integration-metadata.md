---
title: Správa metadat artefaktu účtu integrace
description: Přidání nebo získání metadat artefaktů z účtů integrace v Azure Logic Apps s podnikovou integrací Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/17/2019
ms.openlocfilehash: bc119f1ce8efb821781dabfb9dd259cc5c8d9c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792468"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Správa metadat artefaktů v integračních účtech pomocí Azure Logic Apps a Enterprise Integration Pack

Můžete definovat vlastní metadata pro artefakty v účtech integrace a získat tato metadata za běhu pro aplikaci logiky k použití. Můžete například poskytnout metadata pro artefakty, jako jsou partneři, smlouvy, schémata a mapy – všechna metadata úložiště pomocí párů klíč-hodnota. 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

* Základní [účet integrace,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) který má artefakty, kde chcete přidat metadata, například: 

  * [Partner](logic-apps-enterprise-integration-partners.md)
  * [Smlouva](logic-apps-enterprise-integration-agreements.md)
  * [Schéma](logic-apps-enterprise-integration-schemas.md)
  * [Mapa](logic-apps-enterprise-integration-maps.md)

* Aplikace logiky, která je propojená s účtem integrace a metadaty artefaktů, které chcete použít. Pokud vaše aplikace logiky ještě není propojená, přečtěte si, [jak propojit aplikace logiky s účty integrace](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

  Pokud ještě nemáte aplikaci logiky, přečtěte si, [jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Přidejte aktivační událost a akce, které chcete použít pro správu metadat artefaktu. Nebo chcete-li jen vyzkoušet věci, přidejte aktivační událost, jako je **požadavek** nebo **HTTP** do aplikace logiky.

## <a name="add-metadata-to-artifacts"></a>Přidání metadat k artefaktům

1. Přihlaste se k webu <a href="https://portal.azure.com" target="_blank">Azure Portal</a> pomocí přihlašovacích údajů svého účtu Azure. Najděte a otevřete si účet pro integraci.

1. Vyberte artefakt, do kterého chcete přidat metadata, a zvolte **Upravit**. Zadejte podrobnosti metadat pro tento artefakt, například:

   ![Zadat metadata](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. Po dokončení vyberte **OK**.

1. Chcete-li tato metadata zobrazit v definici javascriptového zápisu objektu (JSON) pro integrační účet, zvolte **Upravit jako JSON,** aby se otevřel editor JSON: 

   ![JSON pro partnerská metadata](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Získání metadat artefaktů

1. Na webu Azure Portal otevřete aplikaci logiky, která je propojená s účtem integrace, který chcete. 

1. Pokud v Návrháři aplikace logiky přidáváte krok pro získání metadat pod aktivační událostí nebo poslední akcí v pracovním postupu, zvolte **Nový krok** > **Přidat akci**. 

1. Do vyhledávacího pole zadejte "účet integrace". Pod vyhledávacím polem zvolte **Vše**. Ze seznamu akcí vyberte tuto akci: **Vyhledávání artefaktů účtu integrace – účet integrace**

   ![Vyberte možnost "Vyhledávání artefaktů účtu integrace"](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Zadejte tyto informace pro artefakt, který chcete najít:

   | Vlastnost | Požaduje se | Hodnota | Popis | 
   |----------|---------|-------|-------------| 
   | **Typ artefaktu** | Ano | **Schéma**, **Mapa**, **Partner**, **Smlouva**nebo vlastní typ | Typ artefaktu, který chcete | 
   | **Název artefaktu** | Ano | <*název artefaktu*> | Název artefaktu, který chcete | 
   ||| 

   Předpokládejme například, že chcete získat metadata pro artefakt obchodního partnera:

   ![Vyberte typ artefaktu a zadejte název artefaktu](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Přidejte požadovanou akci pro zpracování metadat, například:

   1. V části Akce **Vyhledávání artefaktů účtu integrace** zvolte Další **krok**a vyberte **Přidat akci**. 

   1. Do vyhledávacího pole zadejte "http". Pod vyhledávacím polem zvolte **Vestavěné položky**a vyberte tuto akci: **HTTP - HTTP**

      ![Přidat akci HTTP](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Zadejte informace pro metadata artefaktu, které chcete spravovat. 

      Předpokládejme například, že `routingUrl` chcete získat metadata, která byla přidána dříve v tomto tématu. Zde jsou hodnoty vlastností, které můžete zadat: 

      | Vlastnost | Požaduje se | Hodnota | Popis | 
      |----------|----------|-------|-------------| 
      | **Metoda** | Ano | <*operace ke spuštění*> | Operace HTTP spustit na artefakt. Například tato akce HTTP používá metodu **GET.** | 
      | **Identifikátor URI** | Ano | <*umístění metadat*> | Chcete-li `routingUrl` získat přístup k hodnotě metadat z artefaktu, který jste načetli, můžete použít výraz, například: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Hlavičky** | Ne | <*hodnoty záhlaví*> | Všechny výstupy záhlaví z aktivační události, kterou chcete předat do akce HTTP. Chcete-li například předat hodnotu `headers` vlastnosti aktivační události: můžete použít výraz, například: <p>`@triggeroutputs()['headers']` | 
      | **Text** | Ne | <*obsah těla*> | Jakýkoli jiný obsah, který chcete předat `body` vlastnosti akce HTTP. Tento příklad předá `properties` hodnoty artefaktu do akce HTTP: <p>1. Klepněte do vlastnosti **Tělo,** aby se objevil seznam dynamického obsahu. Pokud se nezobrazí žádné vlastnosti, zvolte **Zobrazit další**. <br>2. V seznamu dynamického obsahu včásti **Vyhledávání artefaktů účtu integrace**vyberte **vlastnosti**. | 
      |||| 

      Například:

      ![Určení hodnot a výrazů pro akci HTTP](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. Chcete-li zkontrolovat informace, které jste zadali pro akci HTTP, zobrazte definici JSON aplikace logiky. Na panelu nástrojů Návrhář epoje zvolte **Zobrazení kódu,** aby se zobrazila definice JSON aplikace, například:

      ![Definice aplikace Logika JSON](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      Po přepnutí zpět do Návrháře aplikací logiky se všechny výrazy, které jste použili, zobrazí vyřešené, například:

      ![Vyřešené výrazy v Návrháři aplikací logiky](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>Další kroky

* [Další informace o smlouvách](logic-apps-enterprise-integration-agreements.md)
