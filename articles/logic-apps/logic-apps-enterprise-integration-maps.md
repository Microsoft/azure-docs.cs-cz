---
title: Transformace XML s mapami XSLT
description: Přidat mapy XSLT pro transformaci XML v Azure Logic Apps pomocí Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: 62c3d4533dd04dbb5a2ce0c73afa52b81d433913
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91570782"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Transformace XML s využitím map v Azure Logic Apps s rozšířením Enterprise Integration Pack

K převádění dat XML mezi formáty ve scénářích podnikové integrace v Azure Logic Apps může vaše aplikace logiky používat mapy, konkrétně mapy XSLT (Extensible Stylesheet Language Transformation). Mapa je dokument XML, který popisuje, jak převádět data z dokumentu XML do jiného formátu. 

Předpokládejme například, že pravidelně dostanete objednávky B2B nebo faktury od zákazníka, který používá formát data YYYMMDD. Vaše organizace ale používá formát data MMDDYYY. Můžete definovat a použít mapu, která transformuje formát data YYYMMDD na formát MMDDYYY před uložením podrobností o objednávce nebo faktuře do vaší databáze aktivity zákazníka.

Omezení související s integračními účty a artefakty, jako jsou mapy, najdete v tématu [omezení a informace o konfiguraci pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , kam ukládáte své mapy a další artefakty pro podnikovou integraci a řešení B2B (Business-to-Business).

* Pokud vaše mapa odkazuje na externí sestavení, je nutné nahrát *sestavení i mapu* do svého účtu pro integraci. [*Nejprve nahrajte sestavení*](#add-assembly)a poté nahrajte mapu, která na sestavení odkazuje.

  Pokud je vaše sestavení 2 MB nebo menší, můžete přidat sestavení k účtu integrace *přímo* z Azure Portal. Pokud je však sestavení nebo mapa větší než 2 MB, ale není větší než [omezení velikosti pro sestavení nebo mapy](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), máte tyto možnosti:

  * Pro sestavení potřebujete kontejner objektů blob Azure, kde můžete nahrát sestavení a umístění tohoto kontejneru. Tímto způsobem můžete toto umístění poskytnout později po přidání sestavení do účtu pro integraci. 
  Pro tento úkol budete potřebovat tyto položky:

    | Položka | Popis |
    |------|-------------|
    | [Účet úložiště Azure](../storage/common/storage-account-overview.md) | V tomto účtu vytvořte kontejner objektů BLOB v Azure pro vaše sestavení. Naučte [se vytvořit účet úložiště](../storage/common/storage-account-create.md). |
    | Kontejner objektů blob | V tomto kontejneru můžete nahrát sestavení. Toto umístění kontejneru budete potřebovat také při přidávání sestavení do účtu pro integraci. Přečtěte si, jak [vytvořit kontejner objektů BLOB](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Tento nástroj vám pomůže snadněji spravovat účty úložiště a kontejnery objektů BLOB. Chcete-li použít Průzkumník služby Storage, buď [Stáhněte a nainstalujte Průzkumník služby Azure Storage](https://www.storageexplorer.com/). Potom připojte Průzkumník služby Storage k účtu úložiště podle kroků uvedených v části [Začínáme s Průzkumník služby Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md). Další informace najdete v tématu [rychlý Start: vytvoření objektu BLOB v úložišti objektů pomocí Průzkumník služby Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>Nebo v Azure Portal vyhledejte a vyberte svůj účet úložiště. V nabídce účtu úložiště vyberte **Průzkumník služby Storage**. |
    |||

  * U map můžete v současnosti přidat větší mapy pomocí [Azure Logic Apps REST API Maps](/rest/api/logic/maps/createorupdate).

Při vytváření a přidávání map nepotřebujete aplikaci logiky. Pokud ale chcete použít mapu, vaše aplikace logiky potřebuje propojit s integračním účtem, kam tuto mapu ukládáte. Přečtěte si, [Jak propojit Logic Apps s účty pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Pokud ještě nemáte aplikaci logiky, přečtěte si, [jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>Přidat odkazovaná sestavení

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. Pokud chcete najít a otevřít účet pro integraci, v hlavní nabídce Azure vyberte **všechny služby**. 
   Do vyhledávacího pole zadejte "účet pro integraci". 
   Vyberte **účty pro integraci**.

   ![Najít účet pro integraci](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Vyberte účet pro integraci, do kterého chcete přidat sestavení, například:

   ![Vybrat účet pro integraci](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Na stránce **Přehled** účtu pro integraci vyberte v části **komponenty** dlaždici **sestavení** .

   ![Vybrat sestavení](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. Po otevření stránky **sestavení** klikněte na tlačítko **Přidat**.

   ![Snímek obrazovky, který zvýrazní tlačítko Přidat na stránce sestavení.](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

V závislosti na velikosti souboru sestavení, postupujte podle kroků pro nahrání sestavení, které je buď [na 2 MB](#smaller-assembly) , nebo [více než 2 MB, ale pouze až 8 MB](#larger-assembly).
Omezení množství sestavení v integračních účtech najdete v tématu [omezení a konfigurace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

> [!NOTE]
> Pokud změníte sestavení, je nutné také aktualizovat mapu bez ohledu na to, zda mapa obsahuje změny.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Přidat sestavení až do 2 MB

1. V části **Přidat sestavení** zadejte název sestavení. Ponechat vybraný **malý soubor** . Vedle pole **sestavení** vyberte ikonu složky. Vyhledejte a vyberte sestavení, které nahráváte, například:

   ![Nahrát menší sestavení](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   Ve vlastnosti **název sestavení** se název souboru sestavení zobrazuje automaticky po výběru sestavení.

1. Až budete připraveni, klikněte na **tlačítko OK**.

   Po nahrání souboru sestavení se sestavení zobrazí v seznamu **sestavení** .

   ![Seznam odeslaných sestavení](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   Na stránce **Přehled** v účtu pro integraci se teď v části **komponenty** na dlaždici **sestavení** zobrazuje počet nahraných sestavení, například:

   ![Odeslaná sestavení](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Přidat sestavení více než 2 MB

Chcete-li přidat větší sestavení, můžete nahrát sestavení do kontejneru objektů blob Azure v účtu úložiště Azure. Postup pro přidání sestavení se liší v závislosti na tom, jestli má váš kontejner objektů BLOB veřejný přístup pro čtení. Nejdřív ověřte, jestli má váš kontejner objektů BLOB veřejný přístup pro čtení, a to pomocí následujících kroků: [Nastavení úrovně veřejného přístupu pro kontejner objektů BLOB](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Kontrolovat úroveň přístupu kontejneru

1. Otevřete Průzkumník služby Azure Storage. V okně Průzkumníka rozbalte své předplatné Azure, pokud ještě není rozbalené.

1. Rozbalte položku **účty úložiště** > {*Your-Storage-Account*} > **kontejnerů objektů BLOB**. Vyberte kontejner objektů BLOB.

1. V místní nabídce kontejneru objektů BLOB vyberte **nastavit úroveň veřejného přístupu**.

   * Pokud má váš kontejner objektů BLOB aspoň veřejný přístup, klikněte na **Zrušit** a pak na této stránce použijte následující postup: [nahrání do kontejnerů s veřejným přístupem](#public-access-assemblies)

     ![Veřejný přístup](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Pokud Váš kontejner objektů BLOB nemá veřejný přístup, klikněte na **Zrušit** a pak na této stránce proveďte následující kroky: [nahrání do kontejnerů bez veřejného přístupu](#no-public-access-assemblies) .

     ![Bez veřejného přístupu](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Odeslat do kontejnerů s veřejným přístupem

1. Nahrajte sestavení do svého účtu úložiště. 
   V pravém okně vyberte **nahrát**.

1. Po dokončení nahrávání vyberte vaše nahrané sestavení. Na panelu nástrojů vyberte možnost **Kopírovat adresu URL** , abyste ZKOPÍROVALI adresu URL sestavení.

1. Vraťte se do Azure Portal, kde je otevřeno podokno **Přidat sestavení** . 
   Zadejte název pro sestavení. 
   Vyberte **velký soubor (větší než 2 MB)**.

   Nyní se zobrazí pole **identifikátor URI obsahu** , nikoli pole **sestavení** .

1. Do pole **identifikátor URI obsahu** vložte adresu URL vašeho sestavení. 
   Dokončete přidání sestavení.

Po dokončení nahrávání sestavení se schéma zobrazí v seznamu **sestavení** .
Na stránce **Přehled** v účtu pro integraci teď v části **komponenty** dlaždice **sestavení** zobrazuje počet nahraných sestavení.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Odeslat do kontejnerů bez přístupu veřejnosti

1. Nahrajte sestavení do svého účtu úložiště. 
   V pravém okně vyberte **nahrát**.

1. Po dokončení nahrávání vygenerujte sdílený přístupový podpis (SAS) pro vaše sestavení. 
   V místní nabídce vašeho sestavení vyberte **získat sdílený přístupový podpis**.

1. V podokně **sdílený přístupový podpis** vyberte možnost **generovat identifikátor URI sdíleného přístupového podpisu na úrovni kontejneru**  >  **vytvořit**. 
   Po vygenerování adresy URL SAS klikněte vedle pole **Adresa URL** na možnost **Kopírovat**.

1. Vraťte se do Azure Portal, kde je otevřeno podokno **Přidat sestavení** . 
   Zadejte název pro sestavení. 
   Vyberte **velký soubor (větší než 2 MB)**.

   Nyní se zobrazí pole **identifikátor URI obsahu** , nikoli pole **sestavení** .

1. Do pole **identifikátor URI obsahu** vložte identifikátor URI SAS, který jste předtím vygenerovali. Dokončete přidání sestavení.

Po dokončení nahrávání sestavení se sestavení zobrazí v seznamu **schémata** . Na stránce **Přehled** v účtu pro integraci teď v části **komponenty** dlaždice **sestavení** zobrazuje počet nahraných sestavení.

## <a name="create-maps"></a>Vytváření map

Chcete-li vytvořit dokument XSLT, který můžete použít jako mapu, můžete použít Visual Studio 2015 pro vytvoření projektu integrace BizTalk pomocí [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md). V tomto projektu můžete sestavit soubor integrační mapy, který umožňuje vizuálně mapovat položky mezi dvěma soubory schématu XML. Po sestavení tohoto projektu získáte dokument XSLT.
Omezení množství na mapě v integračních účtech najdete v tématu [omezení a konfigurace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). 

## <a name="add-maps"></a>Přidávání map

Po nahrání všech sestavení, na která mapa odkazuje, teď můžete nahrát mapu.

1. Pokud jste to ještě neudělali, přihlaste se k [Azure Portal](https://portal.azure.com) pomocí svých přihlašovacích údajů k účtu Azure. 

1. Pokud váš účet pro integraci ještě není otevřený, vyberte v hlavní nabídce Azure možnost **všechny služby**. 
   Do vyhledávacího pole zadejte "účet pro integraci". 
   Vyberte **účty pro integraci**.

   ![Najít účet pro integraci](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Vyberte účet pro integraci, do kterého chcete přidat mapu, například:

   ![Vybrat účet pro integraci](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Na stránce **Přehled** účtu Integration klikněte v části **komponenty** na dlaždici **mapy** .

   ![Vyberte Maps](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. Po otevření stránky **mapy** klikněte na tlačítko **Přidat**.

   ![Zvolit přidat](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Přidat mapy až do 2 MB

1. V části **Přidat mapu** zadejte název mapy. 

1. V části **typ mapy** vyberte typ, například: **Liquid**, **XSLT**, **XSLT 2,0** nebo **XSLT 3,0**.

1. Ponechat vybraný **malý soubor** . Vedle pole **Mapa** vyberte ikonu složky. Najděte a vyberte mapu, kterou nahráváte, například:

   ![Nahrát mapu](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Pokud jste ponechali vlastnost **Name** prázdnou, název souboru mapy se automaticky zobrazí v této vlastnosti automaticky po výběru souboru mapování. 
   Můžete ale použít libovolný jedinečný název.

1. Až budete připraveni, klikněte na **tlačítko OK**. 
   Po dokončení nahrávání souboru mapy se mapa zobrazí v seznamu **mapy** .

   ![Seznam odeslaných map](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   Na stránce **Přehled** účtu pro integraci teď v části **komponenty** zobrazuje dlaždice **mapy** počet nahraných map, například:

   ![Nahrané mapy](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Přidat mapy více než 2 MB

V současné době přidejte větší mapy pomocí [Azure Logic Apps mapování REST API](/rest/api/logic/maps/createorupdate).

<!--

To add larger maps, you can upload your map to 
an Azure blob container in your Azure storage account. 
Your steps for adding maps differ based whether your 
blob container has public read access. So first, check 
whether or not your blob container has public read 
access by following these steps: 
[Set public access level for blob container](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### Check container access level

1. Open Azure Storage Explorer. In the Explorer window, 
   expand your Azure subscription if not already expanded.

1. Expand **Storage Accounts** > {*your-storage-account*} > 
   **Blob Containers**. Select your blob container.

1. From your blob container's shortcut menu, 
   select **Set Public Access Level**.

   * If your blob container has at least public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers with public access](#public-access)

     ![Public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * If your blob container doesn't have public access, choose **Cancel**, 
   and follow these steps later on this page: 
   [Upload to containers without public access](#public-access)

     ![No public access](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-maps"></a>

### Add maps to containers with public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**. 

1. After you finish uploading, select your 
   uploaded map. On the toolbar, choose **Copy URL** 
   so that you copy the map's URL.

1. Return to the Azure portal where the 
   **Add Map** pane is open. Choose **Large file**. 

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste your map's URL. 
   Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

<a name="no-public-access-maps"></a>

### Add maps to containers with no public access

1. Upload the map to your storage account. 
   In the right-hand window, choose **Upload**.

1. After you finish uploading, generate a 
   shared access signature (SAS) for your schema. 
   From your map's shortcut menu, 
   select **Get Shared Access Signature**.

1. In the **Shared Access Signature** pane, select 
   **Generate container-level shared access signature URI** > **Create**. 
   After the SAS URL gets generated, next to the **URL** box, choose **Copy**.

1. Return to the Azure portal where the 
   **Add Maps** pane is open. Choose **Large file**.

   The **Content URI** box now appears, 
   rather than the **Map** box.

1. In the **Content URI** box, paste the SAS URI 
   you previously generated. Finish adding your map.

After your map finishes uploading, 
the map appears in the **Maps** list.

-->

## <a name="edit-maps"></a>Úpravy map

Chcete-li aktualizovat existující mapu, je nutné nahrát nový soubor mapy, který obsahuje požadované změny. Můžete ale nejdřív stáhnout stávající mapu pro úpravy.

1. V [Azure Portal](https://portal.azure.com)vyhledejte a otevřete účet pro integraci, pokud ještě není otevřený.

1. V hlavní nabídce Azure vyberte **všechny služby**. Do vyhledávacího pole zadejte "účet pro integraci". Vyberte **účty pro integraci**.

1. Vyberte účet pro integraci, ve kterém chcete aktualizovat mapu.

1. Na stránce **Přehled** účtu Integration klikněte v části **komponenty** na dlaždici **mapy** .

1. Po otevření stránky **mapy** vyberte mapu. 
   Chcete-li nejprve stáhnout a upravit mapu, vyberte možnost **Stáhnout** a mapa uložte.

1. Až budete připraveni nahrát aktualizovanou mapu, vyberte na stránce **mapy** mapu, kterou chcete aktualizovat, a zvolte **aktualizovat**.

1. Vyhledejte a vyberte aktualizovanou mapu, kterou chcete nahrát. 
   Po nahrání souboru mapy se v seznamu **mapy** zobrazí aktualizované mapování.

## <a name="delete-maps"></a>Odstraňování map

1. V [Azure Portal](https://portal.azure.com)vyhledejte a otevřete účet pro integraci, pokud ještě není otevřený.

1. V hlavní nabídce Azure vyberte **všechny služby**. 
   Do vyhledávacího pole zadejte "účet pro integraci". 
   Vyberte **účty pro integraci**.

1. Vyberte účet pro integraci, ve kterém chcete mapu odstranit.

1. Na stránce **Přehled** účtu Integration klikněte v části **komponenty** na dlaždici **mapy** .

1. Po otevření stránky **mapy** vyberte mapu a zvolte **Odstranit**.

1. Pokud chcete potvrdit, že chcete mapu odstranit, klikněte na **Ano**.

## <a name="next-steps"></a>Další kroky

* [Další informace o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [Další informace o schématech](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [Další informace o transformacích](../logic-apps/logic-apps-enterprise-integration-transform.md)
