---
title: Transformace XML pomocí map XSLT
description: Přidání map XSLT pro transformaci XML v aplikacích Azure Logic Apps pomocí sady Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: e186b9713c8464f8f37e1e0bf112c4118621925c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979413"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Transformace XML s využitím map v Azure Logic Apps s rozšířením Enterprise Integration Pack

Chcete-li přenášet data XML mezi formáty pro scénáře podnikové integrace v Azure Logic Apps, vaše aplikace logiky můžete použít mapy nebo přesněji rozšiřitelné transformace šablony stylů (XSLT) mapy. Mapa je dokument XML, který popisuje, jak převést data z dokumentu XML do jiného formátu. 

Předpokládejme například, že pravidelně přijímáte objednávky B2B nebo faktury od zákazníka, který používá formát data YYYMMDDD. Vaše organizace však používá formát data MMDDYYY. Před uložením podrobností o objednávce nebo faktuře do databáze aktivit zákazníků můžete definovat a použít mapu, která transformuje formát data YYYMMDDD na formát MMDDYYY.

Omezení související s účty integrace a artefakty, jako jsou mapy, najdete [v tématu omezení a informace o konfiguraci pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účet integrace,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) ve kterém ukládáte mapy a další artefakty pro podnikovou integraci a řešení B2B (business-to-business).

* Pokud vaše mapa odkazuje na externí sestavení, musíte nahrát *sestavení i mapu* do účtu integrace. Ujistěte se, že [*nejprve nahrajete sestavení*](#add-assembly)a pak nahrajete mapu, která odkazuje na sestavení.

  Pokud je vaše sestavení 2 MB nebo menší, můžete přidat sestavení do účtu integrace *přímo* z portálu Azure. Pokud je však vaše sestava nebo mapa větší než 2 MB, ale není větší než [limit velikosti pro sestavení nebo mapy](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), máte tyto možnosti:

  * Pro sestavení potřebujete kontejner objektů blob Azure, kde můžete nahrát sestavení a umístění tohoto kontejneru. Tímto způsobem můžete zadat toto umístění později při přidání sestavení do účtu integrace. 
  Pro tento úkol potřebujete tyto položky:

    | Položka | Popis |
    |------|-------------|
    | [Účet služby Azure Storage](../storage/common/storage-account-overview.md) | V tomto účtu vytvořte kontejner objektů blob Azure pro vaše sestavení. Přečtěte [si, jak vytvořit účet úložiště](../storage/common/storage-account-create.md). |
    | Kontejner objektů blob | V tomto kontejneru můžete nahrát sestavení. Potřebujete také umístění tohoto kontejneru při přidání sestavení do účtu integrace. Přečtěte si, jak [vytvořit kontejner objektů blob](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Průzkumník úložišť Azure](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Tento nástroj vám pomůže snadněji spravovat účty úložiště a kontejnery objektů blob. Pokud chcete použít Průzkumníka úložiště, [stáhněte a nainstalujte Azure Storage Explorer](https://www.storageexplorer.com/). Potom připojte Průzkumníka úložiště ke svému účtu úložiště podle kroků v [části Začínáme s Průzkumníkem úložiště](../vs-azure-tools-storage-manage-with-storage-explorer.md). Další informace najdete [v tématu Úvodní příručka: Vytvoření objektu blob v úložišti objektů pomocí Průzkumníka úložiště Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>Nebo na webu Azure Portal najděte a vyberte svůj účet úložiště. V nabídce účtu úložiště vyberte **Průzkumník a ukládání dat**. |
    |||

  * Pro mapy můžete aktuálně přidat větší mapy pomocí [rozhraní AZURE Logic Apps REST API - Mapy](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

Při vytváření a přidávání map nepotřebujete aplikaci logiky. Chcete-li však použít mapu, aplikace logiky potřebuje propojení s účtem integrace, kde tuto mapu uložíte. Přečtěte [si, jak propojit aplikace logiky s účty integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Pokud ještě nemáte aplikaci logiky, přečtěte si, [jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-assembly"></a>

## <a name="add-referenced-assemblies"></a>Přidání odkazovaných sestav

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí přihlašovacích údajů svého účtu Azure.

1. Pokud chcete najít a otevřít účet integrace, v hlavní nabídce Azure vyberte **Všechny služby**. 
   Do vyhledávacího pole zadejte "účet integrace". 
   Vyberte **integrační účty**.

   ![Najít účet integrace](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Vyberte účet integrace, do kterého chcete přidat sestavení, například:

   ![Vybrat účet integrace](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Na stránce **Přehled** účtu integrace vyberte v části **Komponenty**dlaždici **Sestavení.**

   ![Vyberte možnost Sestavení.](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. Po otevření stránky **Sestavení** zvolte **Přidat**.

   ![Zvolte "Přidat"](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

Na základě velikosti souboru sestavení postupujte podle pokynů pro nahrání sestavení, které je až [2 MB](#smaller-assembly) nebo více než [2 MB, ale pouze do 8 MB](#larger-assembly).
Omezení množství sestavení v účtech integrace najdete v tématu [Limity a konfigurace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

> [!NOTE]
> Pokud změníte sestavení, musíte také aktualizovat mapu bez ohledu na to, zda mapa obsahuje změny.

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Přidání sestavení do 2 MB

1. V **části Přidat sestavení**zadejte název sestavení. Zachovat **vybraný malý soubor.** Vedle pole **Sestava** zvolte ikonu složky. Najděte a vyberte nahrané sestavení, například:

   ![Nahrání menší sestavy](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   Ve vlastnosti **Název sestavení** se název souboru sestavení zobrazí automaticky po výběru sestavení.

1. Až budete připraveni, zvolte **OK**.

   Po dokončení nahrávání souboru sestavení se sestavení zobrazí v seznamu **Sestavení.**

   ![Seznam nahraných sestavení](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   Na stránce **Přehled** účtu integrace v části **Komponenty**nyní dlaždice **Sestavení** zobrazuje počet nahraných sestavení, například:

   ![Nahraná sestavení](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Přidání sestavení větší než 2 MB

Chcete-li přidat větší sestavení, můžete nahrát sestavení do kontejneru objektů blob Azure ve vašem účtu úložiště Azure. Postup přidání sestavení se liší podle toho, zda má kontejner objektů blob veřejný přístup pro čtení. Takže nejprve zkontrolujte, jestli váš kontejner objektů blob má veřejný přístup pro čtení podle následujících kroků: [Nastavení úrovně veřejného přístupu pro kontejner objektů blob](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Zkontrolovat úroveň přístupu ke kontejneru

1. Otevřete Průzkumníka úložišť Azure. V okně Průzkumníka rozbalte předplatné Azure, pokud už není rozšířené.

1. Rozbalte **položku Účty úložiště** > {*váš účet úložiště*} > **kontejnery objektů blob**. Vyberte kontejner objektů blob.

1. V místní nabídce kontejneru objektů blob vyberte **Nastavit úroveň veřejného přístupu**.

   * Pokud má kontejner objektů blob alespoň veřejný přístup, zvolte **Zrušit**a na této stránce postupujte takto: [Nahrát do kontejnerů s veřejným přístupem](#public-access-assemblies)

     ![Přístup veřejnosti](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Pokud váš kontejner objektů blob nemá veřejný přístup, zvolte **Zrušit**a na této stránce postupujte takto: [Nahrát do kontejnerů bez veřejného přístupu](#no-public-access-assemblies)

     ![Žádný veřejný přístup](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Nahrávání do kontejnerů s veřejným přístupem

1. Nahrajte sestavení do svého účtu úložiště. 
   V pravém okně zvolte **Nahrát**.

1. Po dokončení nahrávání vyberte nahrané sestavení. Na panelu nástrojů zvolte **Kopírovat adresu URL,** abyste zkopírovali adresu URL sestavení.

1. Vraťte se na portál Azure, kde je otevřené podokno **Přidat sestavení.** 
   Zadejte název sestavení. 
   Zvolte **Velký soubor (větší než 2 MB).**

   Nyní se zobrazí pole **IDENTIFIKÁTOR URI obsahu,** nikoli pole **Sestavení.**

1. Do pole **Identifikátor URI obsahu** vložte adresu URL sestavení. 
   Dokončete přidávání sestavy.

Po dokončení nahrávání sestavení se schéma zobrazí v seznamu **sestavení.**
Na stránce **Přehled** účtu integrace v části **Komponenty**nyní dlaždice **Sestavení** zobrazuje počet nahraných sestavení.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Nahrávání do kontejnerů bez přístupu veřejnosti

1. Nahrajte sestavení do svého účtu úložiště. 
   V pravém okně zvolte **Nahrát**.

1. Po dokončení nahrávání vygenerujte sdílený přístupový podpis (SAS) pro vaše sestavení. 
   V místní nabídce sestavení vyberte **Získat sdílený přístupový podpis**.

1. V podokně **Sdílený přístupový podpis** vyberte **Generovat vytvoření identifikátoru URI** > na úrovni kontejneru na úrovni kontejneru .**Create** 
   Po vygenerování adresy URL SAS zvolte vedle pole **ADRESA URL** **možnost Kopírovat**.

1. Vraťte se na portál Azure, kde je otevřené podokno **Přidat sestavení.** 
   Zadejte název sestavení. 
   Zvolte **Velký soubor (větší než 2 MB).**

   Nyní se zobrazí pole **IDENTIFIKÁTOR URI obsahu,** nikoli pole **Sestavení.**

1. Do pole **Identifikátor URI obsahu** vložte identifikátor URI SAS, který jste dříve vygenerovali. Dokončete přidávání sestavy.

Po dokončení nahrávání sestavení se sestavení zobrazí v seznamu **schémat.** Na stránce **Přehled** účtu integrace v části **Komponenty**nyní dlaždice **Sestavení** zobrazuje počet nahraných sestavení.

## <a name="create-maps"></a>Vytváření map

Chcete-li vytvořit dokument XSLT, který můžete použít jako mapu, můžete použít Visual Studio 2015 pro vytvoření projektu BizTalk integrace pomocí [sady Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md). V tomto projektu můžete vytvořit soubor mapy integrace, který umožňuje vizuálně mapovat položky mezi dvěma soubory schématu XML. Po sestavení tohoto projektu získáte dokument XSLT.
Omezení mapových množství v účtech integrace najdete v tématu [Limity a konfigurace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). 

## <a name="add-maps"></a>Přidávání map

Po nahrání všech sestavení, na která vaše mapa odkazuje, můžete nyní nahrát mapu.

1. Pokud jste se ještě nepřihlásili, přihlaste se na [portál Azure](https://portal.azure.com) pomocí přihlašovacích údajů k účtu Azure. 

1. Pokud váš účet integrace ještě není otevřený, v hlavní nabídce Azure vyberte **Všechny služby**. 
   Do vyhledávacího pole zadejte "účet integrace". 
   Vyberte **integrační účty**.

   ![Najít účet integrace](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Vyberte účet integrace, do kterého chcete přidat mapu, například:

   ![Vybrat účet integrace](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. Na stránce **Přehled** účtu integrace vyberte v části **Komponenty**dlaždici **Mapy.**

   ![Vyberte "Mapy"](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. Po otevření stránky **Mapy** zvolte **Přidat**.

   ![Zvolte "Přidat"](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Přidání map až do 2 MB

1. V části **Přidat mapu**zadejte název mapy. 

1. V části **Map type**vyberte typ, například **Liquid**, **XSLT**, **XSLT 2.0**nebo **XSLT 3.0**.

1. Zachovat **vybraný malý soubor.** Vedle pole **Mapa** zvolte ikonu složky. Najděte a vyberte mapu, kterou nahráváte, například:

   ![Nahrát mapu](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Pokud jste ponechali vlastnost **Name** prázdnou, název souboru mapy se automaticky zobrazí v této vlastnosti po výběru souboru mapy. 
   Můžete však použít libovolný jedinečný název.

1. Až budete připraveni, zvolte **OK**. 
   Po dokončení nahrávání mapového souboru se mapa zobrazí v seznamu **Mapy.**

   ![Seznam nahraných map](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   Na stránce **Přehled** účtu integrace se **na** **dlaždici Komponenty**nyní zobrazuje počet nahraných map, například:

   ![Nahrané mapy](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Přidání map o více než 2 MB

V současné době chcete přidat větší mapy, použijte [Azure Logic Apps REST API - Mapy](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

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

Chcete-li aktualizovat existující mapu, musíte nahrát nový mapový soubor, který obsahuje požadované změny. Můžete si však nejprve stáhnout existující mapu pro úpravy.

1. Na [webu Azure Portal](https://portal.azure.com)najděte a otevřete svůj účet integrace, pokud už není otevřený.

1. V hlavní nabídce Azure vyberte **Všechny služby**. Do vyhledávacího pole zadejte "účet integrace". Vyberte **integrační účty**.

1. Vyberte účet integrace, ve kterém chcete mapu aktualizovat.

1. Na stránce **Přehled** účtu integrace vyberte v části **Komponenty**dlaždici **Mapy.**

1. Po otevření stránky **Mapy** vyberte mapu. 
   Chcete-li mapu nejprve stáhnout a upravit, zvolte **Stáhnout**a uložte mapu.

1. Až budete připraveni nahrát aktualizovanou mapu, vyberte na stránce **Mapy** mapu, kterou chcete aktualizovat, a zvolte **Aktualizovat**.

1. Najděte a vyberte aktualizovanou mapu, kterou chcete nahrát. 
   Po dokončení nahrávání souboru mapy se aktualizovaná mapa zobrazí v seznamu **Mapy.**

## <a name="delete-maps"></a>Odstraňování map

1. Na [webu Azure Portal](https://portal.azure.com)najděte a otevřete svůj účet integrace, pokud už není otevřený.

1. V hlavní nabídce Azure vyberte **Všechny služby**. 
   Do vyhledávacího pole zadejte "účet integrace". 
   Vyberte **integrační účty**.

1. Vyberte účet integrace, ve kterém chcete mapu odstranit.

1. Na stránce **Přehled** účtu integrace vyberte v části **Komponenty**dlaždici **Mapy.**

1. Po otevření stránky **Mapy** vyberte mapu a zvolte **Odstranit**.

1. Chcete-li potvrdit, že chcete mapu odstranit, zvolte **Ano**.

## <a name="next-steps"></a>Další kroky

* [Další informace o balíčku Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [Další informace o schématech](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [Další informace o transformacích](../logic-apps/logic-apps-enterprise-integration-transform.md)
