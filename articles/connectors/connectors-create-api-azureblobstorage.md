---
title: Připojení k úložišti objektů blob v Azure – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvoření a Správa objektů BLOB v Azure storage s Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/21/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 49d08135dee4568d1a9d65ec2d22d17ee3bda2ea
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2018
ms.locfileid: "35294675"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Vytvoření a Správa objektů BLOB v Azure blob storage s využitím Azure Logic Apps

Tento článek popisuje, jak můžete používat a spravovat soubory uložené jako objekty BLOB v účtu služby Azure storage z uvnitř aplikace logiky pomocí konektoru služby Azure Blob Storage. Tímto způsobem můžete vytvořit aplikace logiky pro automatizaci úloh a pracovních postupů pro správu vašich souborů. Například můžete vytvářet aplikace logiky, které vytvořit, získat, aktualizovat a odstraňovat soubory v účtu úložiště.

Předpokládejme, že máte nástroj, který se aktualizuje na webu Azure. který funguje jako trigger pro vaši aplikaci logiky. Pokud k této události dojde, můžete mít aplikace logiky aktualizovat některý soubor do kontejneru úložiště objektů blob, který je akce ve vaší aplikaci logiky. 

Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Technické informace specifické pro konektor najdete v tématu <a href="https://docs.microsoft.com/connectors/azureblobconnector/" target="blank">referenční informace ke konektorům Azure Blob Storage</a>.

## <a name="prerequisites"></a>Požadavky

* [Účtu služby Azure storage a kontejner úložiště](../storage/blobs/storage-quickstart-blobs-portal.md)

* Aplikace logiky, které potřebují přístup k vašemu účtu úložiště objektů blob v Azure. Spuštění aplikace logiky se aktivační událost Azure Blob Storage, je nutné [prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Přidání triggeru pro úložiště objektů blob

V Azure Logic Apps, musí začínat každá aplikace logiky [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje při určité události nebo když je splněna konkrétní podmínka. Pokaždé, když se trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky a spustí pracovní postup vaší aplikace.

Tento příklad ukazuje, jak můžete začít pracovní postup aplikace logiky s **Azure Blob Storage – když se objekt blob se přidá nebo upraví (jen vlastnosti)** aktivační událost při získá přidání nebo aktualizaci ve vašem kontejneru úložiště objekt blob vlastnosti. 

1. Webu Azure portal nebo Visual Studio vytvořte prázdné aplikace logiky, otevře se návrhář aplikace logiky. Tento příklad používá na webu Azure portal.

2. Do vyhledávacího pole zadejte jako filtr "objektů blob v azure". Ze seznamu triggerů vyberte trigger, který chcete.

   Tento příklad používá tento trigger: **Azure Blob Storage – když se objekt blob se přidá nebo upraví (jen vlastnosti)**

   ![Vybrat trigger](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Pokud se zobrazí výzva k připojení podrobnosti [vytvořte připojení úložiště objektů blob nyní](#create-connection). Nebo, pokud už připojení existuje, zadejte informace potřebné pro aktivační událost.

   V tomto příkladu vyberte kontejner a složku, kterou chcete monitorovat.

   1. V **kontejneru** , vyberte ikonu složky.

   2. V seznamu složek vyberte pravé ostré závorky ( **>** ) a potom vyhledejte dokud vyhledejte a vyberte složku, která chcete. 

      ![Vybrat složku](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Vyberte interval a frekvenci jak často chcete, aby aktivační událost si zkontrolujte, jestli složka změny.

4. Jakmile budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**.

5. Teď pokračujte v přidávání jednu nebo více akcí do aplikace logiky pro úlohy, které chcete provést s výsledky aktivační události.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Přidání akce pro úložiště objektů blob

V Azure Logic Apps [akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok v pracovním postupu, který následuje aktivační události nebo jiné akce. V tomto příkladu aplikace logiky začíná [trigger opakování](../connectors/connectors-native-recurrence.md).

1. Webu Azure portal nebo Visual Studio otevřete aplikaci logiky v návrháři aplikace logiky. Tento příklad používá na webu Azure portal.

2. V návrháři aplikace logiky podle triggeru nebo akce, zvolte **nový krok** > **přidat akci**.

   ![Přidání akce](./media/connectors-create-api-azureblobstorage/add-action.png) 

   Přidání akce mezi stávající kroky, najeďte myší na připojení šipku. 
   Vyberte znaménko plus (**+**), který se zobrazí a klikněte na tlačítko **přidat akci**.

3. Do vyhledávacího pole zadejte jako filtr "objektů blob v azure". Ze seznamu akcí vyberte požadovanou akci.

   Tento příklad používá tuto akci: **Azure Blob Storage – získat obsah objektu blob**

   ![Vyberte akci](./media/connectors-create-api-azureblobstorage/azure-blob-action.png) 

4. Pokud se zobrazí výzva k připojení podrobnosti [vytvořit připojení k Azure Blob Storage teď](#create-connection). Nebo pokud už připojení existuje, uveďte potřebné informace pro akci. 

   V tomto příkladu vyberte požadovaný soubor.

   1. Z **Blob** , vyberte ikonu složky.
  
      ![Vybrat složku](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Vyhledejte a vyberte soubor, který má na jeho základě **Id** číslo. Tuto možnost najdete **Id** v objektu blob metadat, který je vrácen aktivační událost výše popsaný blob storage.

5. Jakmile budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**.
Otestovat svou aplikaci logiky, ujistěte se, že do vybrané složky obsahuje objekt blob.

V tomto příkladu pouze získá obsah objektu blob. Chcete-li zobrazit obsah, přidejte akci, která se vytvoří soubor s použitím jiný konektor s objektem blob. Například přidejte OneDrive akci, která vytvoří soubor na základě obsahu objektu blob.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Připojení k účtu úložiště

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou triggery, akce a omezení, jak je popsáno v souboru Swagger konektoru, najdete v článku [konektoru referenční stránce](/connectors/azureblobconnector/). 

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
