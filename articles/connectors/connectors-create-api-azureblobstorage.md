---
title: Připojení k úložišti objektů blob v Azure – Azure Logic Apps
description: Vytvoření a Správa objektů BLOB v Azure storage s Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 06/20/2019
tags: connectors
ms.openlocfilehash: d9c29837e99d327112e6a9d648a5c56cc35e8555
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296681"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Vytvoření a Správa objektů BLOB v Azure blob storage s využitím Azure Logic Apps

Tento článek popisuje, jak můžete používat a spravovat soubory uložené jako objekty BLOB v účtu služby Azure storage z uvnitř aplikace logiky pomocí konektoru služby Azure Blob Storage. Tímto způsobem můžete vytvořit aplikace logiky pro automatizaci úloh a pracovních postupů pro správu vašich souborů. Například můžete vytvářet aplikace logiky, které vytvořit, získat, aktualizovat a odstraňovat soubory v účtu úložiště.

Předpokládejme, že máte nástroj, který se aktualizuje na web Azure. který funguje jako trigger pro vaši aplikaci logiky. Pokud k této události dojde, můžete mít aplikace logiky aktualizovat některý soubor do kontejneru úložiště objektů blob, který je akce ve vaší aplikaci logiky.

> [!NOTE]
> Logic Apps nepodporuje přímého připojení k účtům úložiště Azure přes brány firewall. Pro přístup k tyto účty úložiště, použijte jednu z možností tady:
>
> * Vytvoření [prostředí integrační služby](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), který umožňuje připojení k prostředkům ve virtuální síti Azure.
>
> * Pokud už používáte API Management, můžete tuto službu pro tento scénář. Další informace najdete v tématu [jednoduché podnikové integrace architektury](https://aka.ms/aisarch).

Pokud se službou logic Apps teprve začínáte, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: Vytvořte svou první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Technické informace specifické pro konektor najdete v tématu [referenční informace ke konektorům Azure Blob Storage](/connectors/azureblobconnector/).

## <a name="limits"></a>Limits

* Ve výchozím nastavení, může číst nebo zapisovat soubory, které jsou akce Azure Blob Storage *50 MB nebo méně*. Zpracovat soubory větší než 50 MB, ale až 1 024 MB, akce Azure Blob Storage podporují [bloků zpráv](../logic-apps/logic-apps-handle-large-messages.md). **Získat obsah objektu blob** akce implicitně používá dělením dat do bloků.

* Triggery pro úložiště objektů Blob v Azure nepodporují dělením dat do bloků. Pokud se požaduje obsah souboru, aktivační události vyberte pouze soubory, které jsou 50 MB nebo méně. Pokud chcete získat soubory větší než 50 MB, postupujte podle tohoto vzoru:

  * Pomocí aktivační událost Azure Blob Storage, který vrací vlastnosti souboru, například **když objekt blob se přidá nebo upraví (jen vlastnosti)** .

  * Postupujte podle aktivační událost s Azure Blob Storage **získat obsah objektu blob** akce, která načte kompletní soubor a implicitně používá dělením dat do bloků.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účtu služby Azure storage a kontejner úložiště](../storage/blobs/storage-quickstart-blobs-portal.md)

* Aplikace logiky, které potřebují přístup k vašemu účtu úložiště objektů blob v Azure. Spuštění aplikace logiky se aktivační událost Azure Blob Storage, je nutné [prázdné aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Přidání triggeru pro úložiště objektů blob

V Azure Logic Apps, musí začínat každá aplikace logiky [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje při určité události nebo když je splněna konkrétní podmínka. Pokaždé, když se trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky a spustí pracovní postup vaší aplikace.

Tento příklad ukazuje, jak můžete začít pracovní postup aplikace logiky s **když objekt blob se přidá nebo upraví (jen vlastnosti)** aktivační událost při získá přidání nebo aktualizaci ve vašem kontejneru úložiště objekt blob vlastnosti.

1. V [webu Azure portal](https://portal.azure.com) nebo Visual Studio, vytvořit prázdné aplikace logiky, otevře se návrhář aplikace logiky. Tento příklad používá na webu Azure portal.

2. Do vyhledávacího pole zadejte jako filtr "objektů blob v azure". Ze seznamu triggerů vyberte trigger, který chcete.

   V tomto příkladu této aktivační události: **Když se objekt blob se přidá nebo upraví (jen vlastnosti)**

   ![Výběr triggeru](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Pokud se zobrazí výzva k připojení podrobnosti [vytvořte připojení úložiště objektů blob nyní](#create-connection). Nebo, pokud už připojení existuje, zadejte informace potřebné pro aktivační událost.

   V tomto příkladu vyberte kontejner a složku, kterou chcete monitorovat.

   1. V **kontejneru** , vyberte ikonu složky.

   2. V seznamu složek vyberte pravé ostré závorky ( **>** ) a potom vyhledejte dokud vyhledejte a vyberte složku, která chcete.

      ![Vyberte složku](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Vyberte interval a frekvenci jak často chcete, aby aktivační událost si zkontrolujte, jestli složka změny.

4. Jakmile budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**.

5. Teď pokračujte v přidávání jednu nebo více akcí do aplikace logiky pro úlohy, které chcete provést s výsledky aktivační události.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Přidání akce pro úložiště objektů blob

V Azure Logic Apps [akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok v pracovním postupu, který následuje aktivační události nebo jiné akce. V tomto příkladu aplikace logiky začíná [trigger opakování](../connectors/connectors-native-recurrence.md).

1. V [webu Azure portal](https://portal.azure.com) nebo Visual Studio, otevřete v návrháři aplikace logiky aplikace logiky. Tento příklad používá na webu Azure portal.

2. V návrháři aplikace logiky podle triggeru nebo akce, zvolte **nový krok**.

   ![Přidání akce](./media/connectors-create-api-azureblobstorage/add-action.png) 

   Přidání akce mezi stávající kroky, najeďte myší na připojení šipku. Vyberte znaménko plus ( **+** ), který se zobrazí a vyberte **přidat akci**.

3. Do vyhledávacího pole zadejte jako filtr "objektů blob v azure". Ze seznamu akcí vyberte požadovanou akci.

   Tento příklad používá tuto akci: **Získat obsah objektu blob**

   ![Vyberte akci](./media/connectors-create-api-azureblobstorage/azure-blob-action.png)

4. Pokud se zobrazí výzva k připojení podrobnosti [vytvořit připojení k Azure Blob Storage teď](#create-connection).
Nebo pokud už připojení existuje, uveďte potřebné informace pro akci.

   V tomto příkladu vyberte požadovaný soubor.

   1. Z **Blob** , vyberte ikonu složky.
  
      ![Vyberte složku](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Vyhledejte a vyberte soubor, který má na jeho základě **Id** číslo. Tuto možnost najdete **Id** v objektu blob metadat, který je vrácen aktivační událost výše popsaným blob storage.

5. Jakmile budete hotovi, na panelu nástrojů návrháře zvolte **Uložit**.
Otestovat svou aplikaci logiky, ujistěte se, že do vybrané složky obsahuje objekt blob.

V tomto příkladu pouze získá obsah objektu blob. Chcete-li zobrazit obsah, přidejte akci, která se vytvoří soubor s použitím jiný konektor s objektem blob. Například přidejte OneDrive akci, která vytvoří soubor na základě obsahu objektu blob.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Připojení k účtu úložiště

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako jsou triggery, akce a omezení, jak je popsáno v konektoru Openapi (dříve Swagger) souboru, najdete v článku [konektoru referenční stránce](/connectors/azureblobconnector/).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
