---
title: Připojení do Azure blob storage – Azure Logic Apps | Microsoft Docs
description: Vytvářet a spravovat objekty BLOB v úložišti Azure s Azure Logic Apps
author: ecfan
manager: cfowler
ms.author: estfan
ms.date: 05/21/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 15d737cd85f70717bfdf15dfb3d179f977b63c72
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "34723428"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Vytvářet a spravovat objekty BLOB v Azure blob storage službou Azure Logic Apps

Tento článek ukazuje, jak můžete používat a spravovat soubory uložené jako objekty BLOB v účtu úložiště Azure z uvnitř aplikaci logiky s konektorem Azure Blob Storage. Tímto způsobem můžete vytvořit aplikace logiky, které automatizují úlohy a pracovní postupy pro správu vašich souborů. Můžete například vytvořit logiku aplikace, které vytvořit, získat, aktualizovat a odstraňovat soubory ve vašem účtu úložiště.

Předpokládejme, že máte nástroj, který získá aktualizovat na webu Azure. který funguje jako aktivační událost pro svou aplikaci logiky. Pokud k této události dojde, může mít svou aplikaci logiky aktualizovat některé soubor v kontejnerech úložiště objektů blob, což je akce v aplikaci logiky. 

Pokud nemáte předplatné Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>. Pokud jste nové aplikace logiky, přečtěte si [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý start: vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Technické informace specifické pro konektor najdete v tématu <a href="https://docs.microsoft.com/connectors/azureblobconnector/" target="blank">odkaz konektor Azure Blob Storage</a>.

## <a name="prerequisites"></a>Požadavky

* [Účtu úložiště Azure a kontejneru úložiště](../storage/blobs/storage-quickstart-blobs-portal.md)

* Aplikace logiky, kde potřebujete přístup k vašemu účtu úložiště objektů blob v Azure. Chcete-li spustit aplikaci logiky s aktivační procedury Azure Blob Storage, musíte [aplikace logiky prázdné](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Přidat aktivační událost úložiště objektů blob

V Azure Logic Apps, musí začínat každou aplikaci logiky [aktivační událost](../logic-apps/logic-apps-overview.md#logic-app-concepts), která se stane, aktivuje se při určité události, nebo když je splněna určitá podmínka. Pokaždé, když aktivační událost je aktivována, modul Logic Apps vytvoří instanci aplikace logiky a spuštění pracovního postupu vaší aplikace.

Tento příklad ukazuje, jak můžete spustit aplikace logiky pracovního postupu s **Azure Blob Storage – když objekt blob je přidána nebo upravena (pouze vlastnosti)** aktivační událost, pokud objekt blob vlastnosti získá přidán nebo aktualizován v vašeho kontejneru úložiště. 

1. V portálu Azure nebo v sadě Visual Studio vytvořte prázdnou logiku aplikace, která otevře Návrhář aplikace logiky. Tento příklad používá portál Azure.

2. Do vyhledávacího pole zadejte "azure blob" jako filtr. Ze seznamu aktivační události vyberte aktivační události, které chcete.

   Tento příklad používá této aktivační události: **Azure Blob Storage – když objekt blob je přidána nebo upravena (pouze vlastnosti)**

   ![Vybrat trigger](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Pokud se zobrazí výzva k podrobnosti připojení [vytvořte připojení úložiště objektů blob teď](#create-connection). Nebo, pokud připojení již existuje, zadejte informace potřebné pro aktivační událost.

   V tomto příkladu vyberte kontejner a složku, kterou chcete monitorovat.

   1. V **kontejneru** vyberte ikonu složky.

   2. V seznamu složky, vyberte na pravý úhel – závorky ( **>** ) a potom vyhledejte dokud najděte a vyberte požadovanou složku. 

      ![Vyberte složku](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Vyberte interval a četnost pro požadovanou četnost aktivační událost zkontrolujte ve složce pro změny.

4. Vyberte, když jste hotovi, na panelu nástrojů návrháře **Uložit**.

5. Teď pokračujte v přidávání jednoho nebo více akcí do aplikace logiky pro úlohy, které chcete provést ve výsledcích aktivační události.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Přidat akce úložiště objektů blob

V Azure Logic Apps [akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok ve svém pracovním postupu, který následuje aktivační události nebo jiné akce. V tomto příkladu aplikace logiky začíná [aktivační událost opakování](../connectors/connectors-native-recurrence.md).

1. V portálu Azure nebo Visual Studio otevřete aplikaci logiky v návrháři aplikace logiky. Tento příklad používá portál Azure.

2. V návrháři aplikace logiky pod aktivační události nebo akce, zvolte **nový krok** > **přidat akci**.

   ![Přidání akce](./media/connectors-create-api-azureblobstorage/add-action.png) 

   A přidejte akci mezi existující kroky, přesuňte ukazatel myši nad připojování šipkou. 
   Vyberte znaménko plus (**+**), se zobrazí a potom vyberte **přidat akci**.

3. Do vyhledávacího pole zadejte "azure blob" jako filtr. Ze seznamu Akce vyberte akci, kterou chcete.

   Tento příklad používá tuto akci: **Azure Blob Storage - Get obsah objektu blob**

   ![Vyberte akci](./media/connectors-create-api-azureblobstorage/azure-blob-action.png) 

4. Pokud se zobrazí výzva k podrobnosti připojení [vytvořit připojení k Azure Blob Storage teď](#create-connection). Nebo, pokud připojení již existuje, zadejte informace potřebné pro akci. 

   V tomto příkladu vyberte požadovaný soubor.

   1. Z **Blob** vyberte ikonu složky.
  
      ![Vyberte složku](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Najděte a vyberte soubor, který chcete podle objektu blob **Id** číslo. Tento nástroj naleznete **Id** číslo v metadatech objektu blob, které se vrátí aktivační procedura úložiště objektů blob popsaných výše.

5. Vyberte, když jste hotovi, na panelu nástrojů návrháře **Uložit**.
K testování aplikace logiky, ujistěte se, že vybraná složka obsahuje objekt blob.

V tomto příkladu pouze získá obsah pro objekt blob. Chcete-li zobrazit obsah, přidejte akci, která se vytvoří soubor s objektem blob pomocí jiné konektoru. Například přidejte OneDrive akci, která vytvoří soubor podle obsahu objektu blob.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Připojit k účtu úložiště

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti, jako je například aktivačních událostí, akcí a omezení, jak je popsáno pomocí souboru Swagger konektoru, najdete v článku [stránka s referencemi k konektoru](/connectors/azureblobconnector/). 

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](http://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další postup

* Další informace o dalších [konektory Logic Apps](../connectors/apis-list.md)
