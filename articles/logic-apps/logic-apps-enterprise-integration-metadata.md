---
title: Správa integrace účtu artefaktu metadat – Azure Logic Apps | Dokumentace Microsoftu
description: Přidání nebo získat metadata artefaktů z účtů pro integraci v Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 01/17/2019
ms.openlocfilehash: 5ebdf45bec4e7cfceb75354af40c7a21c22c6eef
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54446778"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Správa metadat artefakt v účty pro integraci s Azure Logic Apps a Enterprise Integration Pack

Můžete definovat vlastních metadat pro artefakty v integračních účtů a získání těchto metadat za běhu pro vaši aplikaci logiky použít. Například můžete zadat metadat pro artefakty, jako jsou partneři, smlouvy, schémat a mapy – všechna metadata úložiště pomocí páry klíč hodnota. 

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

* Základní [účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , který má artefakty, ve které chcete přidat metadata, například: 

  * [Partner](logic-apps-enterprise-integration-partners.md)
  * [Smlouva](logic-apps-enterprise-integration-agreements.md)
  * [schéma](logic-apps-enterprise-integration-schemas.md)
  * [Mapa](logic-apps-enterprise-integration-maps.md)

* Aplikace logiky, který je propojený s integrační účet a artefaktů metadata, kterou chcete použít. Pokud vaše aplikace logiky není již propojena, přečtěte si [postup propojení aplikace logiky s účty pro integraci](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

  Pokud ještě nemáte aplikace logiky, přečtěte si [postup vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Přidáte aktivační události a akce, které chcete použít pro správu metadata artefaktů. Nebo jenom vyzkoušet zkoušení různých věcí, přidání triggeru **žádosti** nebo **HTTP** do aplikace logiky.

## <a name="add-metadata-to-artifacts"></a>Přidání metadat pro artefakty

1. Přihlaste se k webu <a href="https://portal.azure.com" target="_blank">Azure Portal</a> pomocí přihlašovacích údajů svého účtu Azure. Vyhledání a otevření účtu pro integraci.

1. Vyberte prosím artefakt, ve které chcete přidat metadata a zvolte **upravit**. Zadejte podrobnosti metadat pro tento artefakt zobrazil, například:

   ![Zadejte metadat](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. Jakmile budete hotovi, zvolte **OK**.

1. Chcete-li zobrazit tato metadata v definici JavaScript Object Notation (JSON) pro účet pro integraci, zvolte **upravit jako JSON** tak, aby se otevře JSON editor: 

   ![JSON pro metadata partnera](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Získat metadata artefaktů

1. Na webu Azure Portal otevřete aplikaci logiky, který je propojený účet integrace, který chcete. 

1. V návrháři aplikace logiky, pokud přidáváte krok pro získávání metadat v aktivační události nebo poslední akce v pracovním postupu, vyberte **nový krok** > **přidat akci**. 

1. Do vyhledávacího pole zadejte "účet integrace". Pod vyhledávacím polem vyberte **všechny**. Ze seznamu akcí vyberte tuto akci: **Vyhledání artefaktu účtu pro integraci – účet pro integraci**

   ![Vyberte "Vyhledání artefaktu účtu pro integraci"](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Zadejte tyto informace pro artefakt, který chcete vyhledat:

   | Vlastnost | Požaduje se | Hodnota | Popis | 
   |----------|---------|-------|-------------| 
   | **Typ artefaktu** | Ano | **Schéma**, **mapy**, **partnera**, **smlouvy**, nebo vlastní typ | Typ artefaktu, který chcete | 
   | **Název artefaktu** | Ano | <*Název artefaktu*> | Název artefaktu, který chcete | 
   ||| 

   Například předpokládejme, že chcete získat metadata pro obchodní partner artefaktu:

   ![Vyberte typ artefaktu a zadejte název artefaktu](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Přidejte akci, která chcete použít pro zpracování těchto metadat, například:

   1. V části **vyhledání artefaktu účtu pro integraci** akce, zvolte **další krok**a vyberte **přidat akci**. 

   1. Do vyhledávacího pole zadejte "http". Pod vyhledávacím polem vyberte **předdefinované**a vyberte tuto akci: **HTTP - HTTP**

      ![Přidání akce HTTP](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Zadejte informace pro metadata artefaktů, které chcete spravovat. 

      Předpokládejme například, že chcete získat `routingUrl` metadata, která se přidá dříve v tomto tématu. Tady jsou hodnoty vlastností, které můžete například zadat: 

      | Vlastnost | Požaduje se | Hodnota | Popis | 
      |----------|----------|-------|-------------| 
      | **Metoda** | Ano | <*operation-to-run*> | Operace protokolu HTTP pro spuštění na artefakt. Například používá tuto akci HTTP **získat** metody. | 
      | **URI** | Ano | <*metadata-location*> | Pro přístup `routingUrl` metadat hodnotu v artefaktu načíst, můžete použít ve výrazu, například: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Hlavičky** | Ne | <*hodnoty hlavičky*> | Libovolné záhlaví výstupy z aktivační události, které chcete předat do akce HTTP. Pro příklad, a zajistěte tak předání triggeru `headers` hodnota vlastnosti: můžete použít ve výrazu, například: <p>`@triggeroutputs()['headers']` | 
      | **Text** | Ne | <*body-content*> | Další obsah, které chcete předat prostřednictvím akce HTTP `body` vlastnost. Tento příklad předává na artefakt `properties` hodnoty na akci HTTP: <p>1. Klikněte do **tělo** vlastnosti, takže se zobrazí v seznamu dynamického obsahu. Pokud se nezobrazí žádné vlastnosti, zvolte **zobrazit další**. <br>2. Ze seznamu dynamického obsahu v části **vyhledání artefaktu účtu pro integraci**vyberte **vlastnosti**. | 
      |||| 

      Příklad:

      ![Zadejte hodnoty a výrazy pro akce HTTP](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. Pokud chcete zkontrolovat informace, které jste zadali pro akce HTTP, zobrazení definice JSON vaší aplikace logiky. Na panelu nástrojů návrháře aplikace logiky zvolte **zobrazení kódu** tak objeví definice JSON aplikace, například:

      ![Definice JSON aplikace logiky](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      Po přepnutí zpět k návrháři aplikace logiky, všechny výrazy, které jste použili teď budou zobrazovat přeložit, například:

      ![Vyřešené výrazy v návrháři aplikace logiky](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>Další postup

* [Další informace o smlouvy](logic-apps-enterprise-integration-agreements.md)
