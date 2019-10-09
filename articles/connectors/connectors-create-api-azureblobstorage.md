---
title: Připojení k úložišti objektů BLOB v Azure – Azure Logic Apps
description: Vytváření a správa objektů BLOB v Azure Storage pomocí Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 06/20/2019
tags: connectors
ms.openlocfilehash: 98a811508d5fa65135c224536b668145ea0808d0
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176072"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Vytváření a správa objektů BLOB v úložišti objektů BLOB v Azure pomocí Azure Logic Apps

V tomto článku se dozvíte, jak můžete v rámci aplikace logiky pomocí konektoru služby Azure Blob Storage získat přístup k souborům uloženým jako objekty BLOB v účtu služby Azure Storage a spravovat je. Tímto způsobem můžete vytvářet aplikace logiky, které automatizují úlohy a pracovní postupy pro správu souborů. Můžete například vytvářet aplikace logiky, které vytvářejí, získávají, aktualizují a odstraňují soubory ve vašem účtu úložiště.

Předpokládejme, že máte nástroj, který se aktualizuje na webu Azure. který funguje jako Trigger vaší aplikace logiky. Když k této události dojde, můžete aplikaci logiky aktualizovat nějaký soubor v kontejneru úložiště objektů blob, což je akce v aplikaci logiky.

> [!IMPORTANT]
>
> Aplikace logiky nemají přímý přístup k účtům Azure Storage, které mají [pravidla brány firewall](../storage/common/storage-network-security.md) a existují ve stejné oblasti. Pokud ale povolíte [odchozí IP adresy pro spravované konektory ve vaší oblasti](../logic-apps/logic-apps-limits-and-config.md#outbound), můžou Logic Apps získat přístup k účtům úložiště v jiné oblasti kromě případů, kdy použijete konektor Azure Table Storage nebo Azure Queue Storage Connector. Pro přístup k Table Storage nebo Queue Storage můžete i nadále používat Trigger a akce HTTP. 
> V opačném případě můžete použít pokročilejší možnosti:
> 
> * Vytvořte [prostředí integrační služby](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), které se může připojit k prostředkům ve službě Azure Virtual Network.
>
> * Pokud pro API Management použijete vyhrazenou vrstvu, můžete rozhraní API úložiště před tím, že použijete API Management a povolíte jeho IP adresy prostřednictvím brány firewall. V podstatě přidejte virtuální síť Azure, kterou používá API Management, do nastavení brány firewall účtu úložiště. Pak můžete použít akci API Management nebo akci protokolu HTTP pro volání rozhraní API Azure Storage. Pokud však zvolíte tuto možnost, musíte proces ověřování zpracovat sami. Další informace najdete v tématu [Jednoduchá architektura podnikové integrace](https://aka.ms/aisarch).

Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Technické informace specifické pro konektor najdete v referenčních informacích k [Azure Blob Storage Connectoru](/connectors/azureblobconnector/).

## <a name="limits"></a>Omezení

* Ve výchozím nastavení můžou akce Azure Blob Storage číst nebo zapisovat soubory o *velikosti 50 MB nebo menší*. Aby bylo možné zpracovat soubory větší než 50 MB, ale až 1024 MB, akce Azure Blob Storage podporují vytváření [bloků zpráv](../logic-apps/logic-apps-handle-large-messages.md). Akce **získat obsah objektu BLOB** implicitně používá bloky dat.

* Aktivační události Azure Blob Storage nepodporují vytváření bloků dat. Při vyžádání obsahu souboru triggery vyberou pouze soubory, které jsou 50 MB nebo menší. Pokud chcete získat soubory větší než 50 MB, postupujte podle tohoto vzoru:

  * Použijte Trigger Blob Storage služby Azure, který vrátí vlastnosti souboru, například **při přidání nebo úpravě objektu BLOB (pouze vlastnosti)** .

  * Postupujte podle triggeru s akcí Azure Blob Storage **získat obsah objektu BLOB** , který načte kompletní soubor a implicitně použije bloky dat.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účet úložiště Azure a kontejner úložiště](../storage/blobs/storage-quickstart-blobs-portal.md)

* Aplikace logiky, kde potřebujete přístup k vašemu účtu služby Azure Blob Storage. Chcete-li spustit aplikaci logiky pomocí triggeru Blob Storage služby Azure, budete potřebovat [prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Přidat Trigger úložiště objektů BLOB

V Azure Logic Apps musí každá aplikace logiky začínat [triggerem](../logic-apps/logic-apps-overview.md#logic-app-concepts), který se aktivuje, když dojde ke konkrétní události nebo když dojde ke splnění určité podmínky. Pokaždé, když se Trigger aktivuje, modul Logic Apps vytvoří instanci aplikace logiky a začne spouštět pracovní postup vaší aplikace.

Tento příklad ukazuje, jak můžete spustit pracovní postup aplikace logiky s **při přidání nebo úpravě objektu BLOB (jen vlastnosti)** triggeru, když se do kontejneru úložiště přidají nebo aktualizují vlastnosti objektu BLOB.

1. V [Azure Portal](https://portal.azure.com) nebo Visual Studiu vytvořte prázdnou aplikaci logiky, která otevře návrháře aplikace logiky. V tomto příkladu se používá Azure Portal.

2. Do vyhledávacího pole zadejte jako filtr "Azure Blob". V seznamu triggery vyberte aktivační událost, kterou chcete.

   Tento příklad používá tuto aktivační událost: **při přidání nebo úpravě objektu BLOB (pouze vlastnosti)**

   ![Vybrat aktivační událost](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Pokud se zobrazí výzva k zadání podrobností o připojení, [vytvořte připojení k úložišti objektů BLOB hned teď](#create-connection). Nebo, pokud připojení již existuje, zadejte potřebné informace pro aktivační událost.

   V tomto příkladu vyberte kontejner a složku, které chcete monitorovat.

   1. V poli **kontejner** vyberte ikonu složky.

   2. V seznamu složka zvolte pravou ostrou závorku ( **>** ) a potom přejděte do složky, kterou chcete najít, a vyberte ji.

      ![Vyberte složku](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Vyberte interval a frekvenci, jak často chcete, aby Trigger kontroloval změny ve složce.

4. Až budete hotovi, na panelu nástrojů návrháře klikněte na **Uložit**.

5. Nyní pokračujte v přidávání jedné nebo více akcí do aplikace logiky pro úlohy, které chcete provést s výsledky triggeru.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Přidat akci úložiště objektů BLOB

V Azure Logic Apps [Akce](../logic-apps/logic-apps-overview.md#logic-app-concepts) je krok v pracovním postupu, který následuje po triggeru nebo jiné akci. V tomto příkladu se aplikace logiky spustí s [triggerem opakování](../connectors/connectors-native-recurrence.md).

1. V [Azure Portal](https://portal.azure.com) nebo Visual Studiu otevřete aplikaci logiky v návrháři aplikace logiky. V tomto příkladu se používá Azure Portal.

2. V návrháři aplikace logiky pod triggerem nebo akcí vyberte **Nový krok**.

   ![Přidání akce](./media/connectors-create-api-azureblobstorage/add-action.png) 

   Pokud chcete přidat akci mezi stávajícími kroky, přesuňte ukazatel myši na šipku připojení. Vyberte symbol plus ( **+** ), který se zobrazí, a vyberte **přidat akci**.

3. Do vyhledávacího pole zadejte jako filtr "Azure Blob". V seznamu akce vyberte akci, kterou chcete.

   Tento příklad používá tuto akci: **získat obsah objektu BLOB**

   ![Vybrat akci](./media/connectors-create-api-azureblobstorage/azure-blob-action.png)

4. Pokud se zobrazí výzva k zadání podrobností o připojení, [vytvořte připojení k Azure Blob Storage hned teď](#create-connection).
Nebo, pokud připojení již existuje, zadejte potřebné informace pro akci.

   V tomto příkladu vyberte soubor, který chcete.

   1. V poli **objekt BLOB** vyberte ikonu složky.
  
      ![Vyberte složku](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Vyhledejte a vyberte požadovaný soubor na základě čísla **ID** objektu BLOB. Toto **identifikační** číslo můžete najít v metadatech objektu blob, která jsou vrácená dříve popsanou triggerem služby Blob Storage.

5. Až budete hotovi, na panelu nástrojů návrháře klikněte na **Uložit**.
Pokud chcete otestovat aplikaci logiky, ujistěte se, že vybraná složka obsahuje objekt BLOB.

V tomto příkladu se načte jenom obsah objektu BLOB. Chcete-li zobrazit obsah, přidejte další akci, která vytvoří soubor s objektem BLOB pomocí jiného konektoru. Přidejte například akci OneDrivu, která vytvoří soubor na základě obsahu objektu BLOB.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Připojit k účtu úložiště

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Referenční informace ke konektorům

Podrobnosti o technických podrobnostech, jako jsou triggery, akce a omezení, jak je popsáno v souboru Open API konektoru (dříve Swagger), najdete na [referenční stránce konektoru](/connectors/azureblobconnector/).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
