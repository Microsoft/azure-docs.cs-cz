---
title: Spravovat metadata artefaktů účtu pro integraci
description: Přidání nebo získání metadat artefaktů z integračních účtů v Azure Logic Apps pomocí Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/17/2019
ms.openlocfilehash: bc119f1ce8efb821781dabfb9dd259cc5c8d9c23
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74792468"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Správa metadat artefaktů v integračních účtech pomocí Azure Logic Apps a Enterprise Integration Pack

Můžete definovat vlastní metadata pro artefakty v integračních účtech a získat tato metadata za běhu, která má vaše aplikace logiky použít. Můžete například zadat metadata pro artefakty, jako jsou partneři, smlouvy, schémata a mapy – všechna metadata úložiště se používají páry klíč-hodnota. 

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud předplatné nemáte, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

* Základní [účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , který obsahuje artefakty, do kterých chcete přidat metadata, například: 

  * [Instituc](logic-apps-enterprise-integration-partners.md)
  * [Ujednání](logic-apps-enterprise-integration-agreements.md)
  * [XSD](logic-apps-enterprise-integration-schemas.md)
  * [Mapy](logic-apps-enterprise-integration-maps.md)

* Aplikace logiky, která je propojená s integračním účtem a metadaty artefaktů, které chcete použít. Pokud vaše aplikace logiky ještě není propojená, podívejte se, [Jak propojit aplikace logiky s účty pro integraci](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

  Pokud ještě nemáte aplikaci logiky, přečtěte si, [jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Přidejte aktivační událost a akce, které chcete použít pro správu metadat artefaktů. Nebo pokud chcete něco vyzkoušet, přidejte do aplikace logiky Trigger, jako je třeba **Request** nebo **http** .

## <a name="add-metadata-to-artifacts"></a>Přidat metadata k artefaktům

1. Přihlaste se k webu <a href="https://portal.azure.com" target="_blank">Azure Portal</a> pomocí přihlašovacích údajů svého účtu Azure. Vyhledejte a otevřete účet pro integraci.

1. Vyberte artefakt, do kterého chcete přidat metadata, a zvolte **Upravit**. Zadejte podrobnosti metadat pro tento artefakt, například:

   ![Zadat metadata](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. Až budete hotovi, klikněte na **OK**.

1. Chcete-li zobrazit tato metadata v definici JavaScript Object Notation (JSON) pro účet pro integraci, vyberte možnost **Upravit jako JSON** , aby se otevřel Editor JSON: 

   ![JSON pro metadata partnerů](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Získat metadata artefaktů

1. V Azure Portal otevřete aplikaci logiky, která je propojená s účtem pro integraci, který chcete. 

1. Pokud přidáváte krok pro získávání metadat v rámci triggeru nebo poslední akce v pracovním postupu v návrháři aplikace logiky, vyberte **Nový krok** > **přidat akci**. 

1. Do vyhledávacího pole zadejte "účet pro integraci". V poli Hledat vyberte možnost **vše**. V seznamu akce vyberte tuto akci: **vyhledávání artefaktů účtu integrace – účet pro integraci**

   ![Vyberte "vyhledávání artefaktů účtu integrace"](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Zadejte tyto informace pro artefakt, který chcete najít:

   | Vlastnost | Požaduje se | Hodnota | Popis | 
   |----------|---------|-------|-------------| 
   | **Typ artefaktu** | Ano | **Schéma**, **Mapa**, **partner**, **smlouva**nebo vlastní typ | Typ artefaktu, který chcete | 
   | **Název artefaktu** | Ano | *název artefaktu* <> | Název artefaktu, který chcete | 
   ||| 

   Předpokládejme například, že chcete získat metadata pro artefakt obchodního partnera:

   ![Vyberte typ artefaktu a zadejte název artefaktu.](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Přidejte akci, kterou chcete použít pro zpracování metadat, například:

   1. V části akce **vyhledávání artefaktů účtu integrace** zvolte **Další krok**a vyberte **přidat akci**. 

   1. Do vyhledávacího pole zadejte "http". V poli hledání zvolte **předdefinované**a vyberte tuto akci: **http-http**

      ![Přidat akci HTTP](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Zadejte informace o metadatech artefaktů, které chcete spravovat. 

      Předpokládejme například, že chcete získat `routingUrl` metadata, která byla přidána dříve v tomto tématu. Tady jsou hodnoty vlastností, které můžete zadat: 

      | Vlastnost | Požaduje se | Hodnota | Popis | 
      |----------|----------|-------|-------------| 
      | **Metoda** | Ano | <*operací na spuštění*> | Operace HTTP, která se má spustit na artefaktu Tato akce HTTP například používá metodu **Get** . | 
      | **IDENTIFIKÁTOR URI** | Ano | <*metadata – umístění*> | Pro přístup k hodnotě metadat `routingUrl` z artefaktu, který jste načetli, můžete použít výraz, například: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Hlavičky** | Ne | <*hodnoty hlaviček*> | Všechny výstupy hlaviček z triggeru, které chcete předat akci HTTP. Například pro předání hodnoty vlastnosti `headers` triggeru: můžete použít výraz, například: <p>`@triggeroutputs()['headers']` | 
      | **Text** | Ne | *text <– obsah*> | Jakýkoli další obsah, který chcete předat pomocí vlastnosti `body` akce HTTP. Tento příklad předává `properties` hodnoty artefaktu do akce HTTP: <p>1. klikněte do vlastnosti **body** , aby se zobrazil seznam dynamického obsahu. Pokud se nezobrazí žádné vlastnosti, klikněte na **Zobrazit další**. <br>2. v seznamu dynamického obsahu v části **vyhledávání artefaktů účtu integrace**vyberte **vlastnosti**. | 
      |||| 

      Například:

      ![Zadat hodnoty a výrazy pro akci HTTP](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. Pokud chcete zjistit informace, které jste zadali pro akci HTTP, podívejte se na definici JSON vaší aplikace logiky. Na panelu nástrojů návrháře aplikace logiky vyberte **zobrazení kód** , aby se zobrazila definice JSON aplikace, například:

      ![Definice JSON aplikace logiky](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      Po přepnutí zpátky do návrháře aplikace logiky se všechny použité výrazy, které jste teď využívali, vyřešily, například:

      ![Vyřešené výrazy v návrháři aplikace logiky](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>Další kroky

* [Další informace o smlouvách](logic-apps-enterprise-integration-agreements.md)
