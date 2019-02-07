---
title: Transformace XML pomocí XSLT map – Azure Logic Apps | Dokumentace Microsoftu
description: Přidat že XSLT map pro transformaci XML v Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
manager: carmonm
ms.topic: article
ms.assetid: 90f5cfc4-46b2-4ef7-8ac4-486bb0e3f289
ms.date: 02/06/2019
ms.openlocfilehash: da5b099a5574d34c3676819c930f3e89610cf4ad
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767431"
---
# <a name="transform-xml-with-maps-in-azure-logic-apps-with-enterprise-integration-pack"></a>Transformace XML pomocí map ve službě Azure Logic Apps sadou Enterprise Integration Pack

Přenos dat XML mezi formáty pro podnikové scénáře integrace v Azure Logic Apps, můžete použít svou aplikaci logiky mapy nebo přesněji řečeno, mapuje šablony stylů transformace XSLT (Extensible Language). Dokument XML, který popisuje, jak převést data z dokumentu XML do jiného formátu je objekt map. 

Předpokládejme například, že pravidelně dostávat B2B objednávky nebo faktury od zákazníka, který používá formát data YYYMMDD. Ale vaše organizace používá MMDDYYY formát data. Můžete definovat a používat mapu, která transformuje YYYMMDD formát data do formátu MMDDYYY před uložením podrobnosti objednávky nebo faktury v databázi aktivity zákazníků.

Omezení související s účty pro integraci a součásti, jako jsou mapování najdete v tématu [omezení a konfigurační informace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

* [Účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) kam ukládat vaše mapy a další artefakty pro podnikovou integraci a řešení business-to-business (B2B).

* Pokud vaše mapa odkazuje na externí sestavení, budete muset nahrát *sestavení a mapy* ke svému účtu integrace. Ujistěte se, že jste *nejprve nahrát sestavení*a potom ho nahrajete mapa, který odkazuje na sestavení.

  Pokud vaše sestavení je 2 MB nebo menší, můžete přidat sestavení do účtu pro integraci *přímo* z portálu Azure portal. Nicméně pokud vaše sestavení nebo mapy je větší než 2 MB, ale ne větší než [omezení velikosti pro sestavení nebo aplikace mapy](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), máte tyto možnosti:

  * Sestavení je nutné kontejner objektů blob Azure, kde můžete nahrát sestavení a umístění tohoto kontejneru. Tímto způsobem můžete zadat toto umístění později při přidávání sestavení do účtu pro integraci. 
  Pro tuto úlohu budete potřebovat tyto položky:

    | Položka | Popis |
    |------|-------------|
    | [Účet služby Azure Storage](../storage/common/storage-account-overview.md) | V tomto účtu vytvoření kontejneru objektů blob v Azure pro vaše sestavení. Přečtěte si [způsob vytvoření účtu úložiště](../storage/common/storage-quickstart-create-account.md). |
    | Kontejner objektů BLOB | V tomto kontejneru můžete nahrát sestavení. Musíte také umístění tohoto kontejneru, při přidávání sestavení do účtu pro integraci. Zjistěte, jak [vytvořte kontejner objektů blob](../storage/blobs/storage-quickstart-blobs-portal.md). |
    | [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) | Tento nástroj umožňuje další snadno spravovat účty úložiště a kontejnery objektů blob. Použití Průzkumníka služby Storage, buď [stažení a instalace Průzkumníka služby Azure Storage](https://www.storageexplorer.com/). Potom propojení Průzkumníka služby Storage do účtu úložiště pomocí následujících kroků v [Začínáme se Storage Explorerem](../vs-azure-tools-storage-manage-with-storage-explorer.md). Další informace najdete v tématu [rychlý start: Vytvoření objektu blob v úložišti objektů pomocí Průzkumníka služby Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md). <p>Nebo na webu Azure Portal, vyhledejte a vyberte svůj účet úložiště. V nabídce účtu úložiště, vyberte **Průzkumníka služby Storage**. |
    |||

  * Pro mapy, můžete nyní přidat větší mapy pomocí [Azure Logic Apps REST API – mapuje](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

Není nutné aplikaci logiky po vytvoření a přidání mapy. Však použít mapu, aplikace logiky potřebuje připojení k účtu pro integraci kam se ukládají, která je namapována. Přečtěte si [postup propojení aplikace logiky s účty pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Pokud ještě nemáte aplikace logiky, přečtěte si [postup vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-referenced-assemblies"></a>Přidat odkazovaná sestavení

1. Přihlaste se k webu <a href="https://portal.azure.com" target="_blank">Azure Portal</a> pomocí přihlašovacích údajů svého účtu Azure.

1. K vyhledání a otevření účtu pro integraci, v hlavní nabídce Azure zvolte **všechny služby**. 
   Do vyhledávacího pole zadejte "účet integrace". 
   Vyberte **účty pro integraci**.

   ![Najít účet integrace](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Vyberte účet integrace, ve které chcete přidat sestavení, například:

   ![Vyberte účet pro integraci](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. V účtu integrace **přehled** stránce v části **součásti**, vyberte **sestavení** dlaždici.

   ![Vyberte "Sestavení"](./media/logic-apps-enterprise-integration-maps/select-assemblies.png)

1. Po **sestavení** otevře se stránka, zvolit **přidat**.

   ![Zvolte "Přidat"](./media/logic-apps-enterprise-integration-maps/add-assembly.png)

Podle velikosti souboru sestavení, postupujte podle kroků pro nahrávání sestavení, které je buď [až 2 MB](#smaller-assembly) nebo [více než 2 MB, ale pouze až 8 MB](#larger-assembly).
Omezení množství sestavení v integračních účtů najdete v tématu [omezení a konfigurace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits).

<a name="smaller-assembly"></a>

### <a name="add-assemblies-up-to-2-mb"></a>Přidat sestavení až 2 MB

1. V části **přidat sestavení**, zadejte název vašeho sestavení. Zachovat **malý soubor** vybrané. Vedle položky **sestavení** vyberte ikonu složky. Vyhledejte a vyberte sestavení, ve kterém jste při odesílání, například:

   ![Nahrát menší sestavení](./media/logic-apps-enterprise-integration-maps/upload-assembly-file.png)

   V **název sestavení** vlastnost název souboru sestavení se automaticky zobrazí po výběru sestavení.

1. Jakmile budete připraveni, zvolte **OK**.

   Po dokončení nahrávání souboru sestavení, zobrazí se v sestavení **sestavení** seznamu.

   ![Seznam nahrané sestavení](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies-list.png)

   V účtu integrace **přehled** stránce v části **součásti**, **sestavení** dlaždice nyní zobrazuje počet nahraných sestavení, například:

   ![Nahraný sestavení](./media/logic-apps-enterprise-integration-maps/uploaded-assemblies.png)

<a name="larger-assembly"></a>

### <a name="add-assemblies-more-than-2-mb"></a>Přidat sestavení více než 2 MB

K přidání větší sestavení, můžete nahrát sestavení do kontejneru objektů blob v Azure ve vašem účtu úložiště Azure. Vaše kroky pro přidání sestavení se liší na základě, zda má veřejné oprávnění ke čtení kontejneru objektů blob. Proto nejprve zkontrolujte, jestli má váš kontejner objektů blob veřejné oprávnění ke čtení pomocí následujících kroků: [Nastavte úroveň veřejného přístupu pro kontejner objektů blob](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Zkontrolujte úroveň přístupu kontejneru

1. Otevřete Průzkumníka služby Azure Storage. V okně Průzkumníka rozbalte vaše předplatné Azure, pokud ještě není rozbalen.

1. Rozbalte **účty úložiště** > {*svůj účet úložiště*} > **kontejnery objektů Blob**. Vyberte kontejner objektů blob.

1. V kontejneru objektů blob v místní nabídce vyberte **nastavte úroveň veřejného přístupu**.

   * Pokud alespoň veřejné oprávnění ke kontejneru objektů blob, zvolte **zrušit**a postupujte podle těchto kroků později na této stránce: [Odešlete do kontejneru s veřejného přístupu](#public-access-assemblies)

     ![Veřejný přístup](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Pokud váš kontejner objektů blob nemá veřejný přístup, zvolte **zrušit**a postupujte podle těchto kroků později na této stránce: [Odešlete do kontejneru bez veřejného přístupu](#no-public-access-assemblies)

     ![Žádný veřejný přístup](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access-assemblies"></a>

#### <a name="upload-to-containers-with-public-access"></a>Odešlete do kontejneru s veřejného přístupu

1. Sestavení nahrajte do účtu úložiště. 
   V pravém okně vyberte **nahrát**.

1. Po dokončení nahrávání, vyberte vaše nahrané sestavení. Na panelu nástrojů zvolte **kopírování adresy URL** tak, že zkopírujete adresu URL sestavení.

1. Vraťte se do portálu Azure portal kde **přidat sestavení** je otevřeno podokno. 
   Zadejte název vašeho sestavení. 
   Zvolte **velký soubor (větší než 2 MB)**.

   **Identifikátor URI obsahu** pole se teď zobrazí, spíše než **sestavení** pole.

1. V **identifikátor URI obsahu** pole, vložte adresu URL vašeho sestavení. 
   Dokončení přidání vašeho sestavení.

Po sestavení dokončí nahrávání, schéma se zobrazí v **sestavení** seznamu.
V účtu integrace **přehled** stránce v části **součásti**, **sestavení** dlaždice se teď zobrazuje počet nahraných sestavení.

<a name="no-public-access-assemblies"></a>

#### <a name="upload-to-containers-without-public-access"></a>Odešlete do kontejneru bez veřejného přístupu

1. Sestavení nahrajte do účtu úložiště. 
   V pravém okně vyberte **nahrát**.

1. Po dokončení nahrávání se vygenerujte sdílený přístupový podpis (SAS) pro vaše sestavení. 
   V místní nabídce vašeho sestavení, vyberte **získat sdílený přístupový podpis**.

1. V **sdílený přístupový podpis** vyberte **generovat úrovni kontejneru sdíleného přístupového podpisu URI** > **vytvořit**. 
   Po získá vygenerování adres URL SAS, vedle položky **URL** zvolte **kopírování**.

1. Vraťte se do portálu Azure portal kde **přidat sestavení** je otevřeno podokno. 
   Zadejte název vašeho sestavení. 
   Zvolte **velký soubor (větší než 2 MB)**.

   **Identifikátor URI obsahu** pole se teď zobrazí, spíše než **sestavení** pole.

1. V **identifikátor URI obsahu** vložte dříve vygenerovaný identifikátor URI SAS. Dokončení přidání vašeho sestavení.

Po dokončení nahrávání vaše sestavení, zobrazí se v sestavení **schémata** seznamu. V účtu integrace **přehled** stránce v části **součásti**, **sestavení** dlaždice se teď zobrazuje počet nahraných sestavení.

## <a name="create-maps"></a>Vytváření map

Vytvoření dokumentu XSLT můžete použít jako mapu, můžete použít Visual Studio 2015 pro vytvoření projektu BizTalk Integration pomocí [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md). V tomto projektu můžete vytvořit soubor mapy integrace, která umožňuje vizuálně mapovat položky mezi dvěma soubory schématu XML. Po vytvoření tohoto projektu získáte dokumentu XSLT.
Omezení množství mapy v integračních účtů najdete v tématu [omezení a konfigurace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#artifact-number-limits). 

## <a name="add-maps"></a>Přidání map

Po odeslání všech sestavení, na které odkazuje vaše mapa teď můžete nahrát mapy.

1. Pokud už se nejste přihlášení, přihlaste se k <a href="https://portal.azure.com" target="_blank">webu Azure portal</a> pomocí svých přihlašovacích údajů účtu Azure. 

1. Pokud ještě není otevřeno v hlavní nabídce Azure účtu pro integraci, vyberte **všechny služby**. 
   Do vyhledávacího pole zadejte "účet integrace". 
   Vyberte **účty pro integraci**.

   ![Najít účet integrace](./media/logic-apps-enterprise-integration-maps/find-integration-account.png)

1. Vyberte účet integrace, ve které chcete přidat mapu, například:

   ![Vyberte účet pro integraci](./media/logic-apps-enterprise-integration-maps/select-integration-account.png)

1. V účtu integrace **přehled** stránce v části **součásti**, vyberte **mapy** dlaždici.

   ![Vyberte "Mapy"](./media/logic-apps-enterprise-integration-maps/select-maps.png)

1. Po **mapy** otevře se stránka, zvolit **přidat**.

   ![Zvolte "Přidat"](./media/logic-apps-enterprise-integration-maps/add-map.png)  

<a name="smaller-map"></a>

### <a name="add-maps-up-to-2-mb"></a>Přidání map až 2 MB

1. V části **přidat mapu**, zadejte název mapy. 

1. V části **mapování typu**, vyberte typ, například: **Liquid**, **XSLT**, **XSLT 2.0**, nebo **XSLT 3.0**.

1. Zachovat **malý soubor** vybrané. Vedle položky **mapy** vyberte ikonu složky. Vyhledejte a vyberte mapu, kterou jste při odesílání, například:

   ![Nahrání mapy](./media/logic-apps-enterprise-integration-maps/upload-map-file.png)

   Pokud jste nechali **název** vlastnost prázdná, název souboru mapy automaticky zobrazí v této vlastnosti automaticky po výběru souboru mapy. 
   Můžete však použít libovolný jedinečný název.

1. Jakmile budete připraveni, zvolte **OK**. 
   Po dokončení nahrávání souboru mapy, zobrazí se v mapě **mapy** seznamu.

   ![Seznam nahrané mapy](./media/logic-apps-enterprise-integration-maps/uploaded-maps-list.png)

   V účtu integrace **přehled** stránce v části **součásti**, **mapuje** dlaždice nyní zobrazuje počet nahraných mapy, například:

   ![Nahrané mapy](./media/logic-apps-enterprise-integration-maps/uploaded-maps.png)

<a name="larger-map"></a>

### <a name="add-maps-more-than-2-mb"></a>Přidat mapování více než 2 MB

V současné době k přidání větší mapy, použijte [Azure Logic Apps REST API – mapuje](https://docs.microsoft.com/rest/api/logic/maps/createorupdate).

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

## <a name="edit-maps"></a>Upravit mapování

Pokud chcete aktualizovat existující mapování, budete muset nahrát nový soubor mapování, která obsahuje změny, které chcete. Je však nejprve stáhnout existující mapování pro úpravy.

1. V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a>, najít a otevřete svůj účet integrace, pokud není otevřen.

1. V hlavní nabídce Azure zvolte **všechny služby**. Do vyhledávacího pole zadejte "účet integrace". Vyberte **účty pro integraci**.

1. Vyberte, ve které chcete aktualizovat mapu účtu integrace.

1. V účtu integrace **přehled** stránce v části **součásti**, vyberte **mapy** dlaždici.

1. Po **mapy** otevře se stránka, vyberte mapu. 
   Chcete-li stáhnout a upravit mapu nejprve, zvolte **Stáhnout**a uložit na mapě.

1. Až budete připravení nahrát aktualizovanou mapování na **mapy** vyberte mapu, kterou chcete aktualizovat a zvolte **aktualizovat**.

1. Vyhledejte a vyberte aktualizovanou mapu, kterou chcete nahrát. 
   Po dokončení nahrávání souboru mapy, zobrazí se v aktualizované rozvržení **mapy** seznamu.

## <a name="delete-maps"></a>Odstranění mapování

1. V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a>, najít a otevřete svůj účet integrace, pokud není otevřen.

1. V hlavní nabídce Azure zvolte **všechny služby**. 
   Do vyhledávacího pole zadejte "účet integrace". 
   Vyberte **účty pro integraci**.

1. Vyberte, ve které chcete odstranit mapu účtu integrace.

1. V účtu integrace **přehled** stránce v části **součásti**, vyberte **mapy** dlaždici.

1. Po **mapy** otevře se stránka, vyberte mapu a zvolte **odstranit**.

1. Pokud chcete potvrdit, které chcete odstranit mapu, zvolte **Ano**.

## <a name="next-steps"></a>Další postup

* [Další informace o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)  
* [Další informace o schémat](../logic-apps/logic-apps-enterprise-integration-schemas.md)
* [Další informace o transformacích](../logic-apps/logic-apps-enterprise-integration-transform.md)