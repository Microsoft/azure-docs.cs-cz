---
title: Ověření XML pomocí schémat
description: Přidání schémat pro ověřování dokumentů XML v Azure Logic Apps pomocí Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: ade29318fdd1510d03ddd208534258a30b256e98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75979377"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Ověřování XML s využitím schémat v Azure Logic Apps s rozšířením Enterprise Integration Pack

Pokud chcete ověřit, že dokumenty používají platný kód XML a mají očekávaná data v předdefinovaném formátu pro scénáře podnikové integrace v Azure Logic Apps, vaše aplikace logiky může používat schémata. Schéma může také ověřit zprávy, které aplikace Logic Apps vyměňuje ve scénářích B2B (Business-to-Business).

Omezení související s integračními účty a artefakty, jako jsou schémata, najdete v tématu [omezení a informace o konfiguraci pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

* [Účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , kam uložíte schémata a další artefakty pro podnikovou integraci a řešení B2B (Business-to-Business). 

  Pokud je vaše schéma [2 MB nebo menší](#smaller-schema), můžete do svého účtu pro integraci přidat své schéma přímo z Azure Portal. Pokud je ale vaše schéma větší než 2 MB, ale není větší než [limit velikosti schématu](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), můžete své schéma nahrát do účtu úložiště Azure. 
  Pokud chcete toto schéma přidat do účtu pro integraci, můžete ho propojit s účtem úložiště z účtu pro integraci. 
  Pro tuto úlohu se tady zobrazí položky, které potřebujete: 

  * [Účet služby Azure Storage](../storage/common/storage-account-overview.md) , kde vytvoříte kontejner objektů BLOB pro vaše schéma. Naučte se [vytvořit účet úložiště](../storage/common/storage-account-create.md). 

  * Kontejner objektů BLOB pro ukládání schématu Přečtěte si, jak [vytvořit kontejner objektů BLOB](../storage/blobs/storage-quickstart-blobs-portal.md). 
  Identifikátor URI obsahu kontejneru budete potřebovat později při přidávání schématu do účtu pro integraci.

  * [Průzkumník služby Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md), které můžete použít ke správě účtů úložiště a kontejnerů objektů BLOB. 
  Pokud chcete použít Průzkumník služby Storage, vyberte jednu z možností:
  
    * V Azure Portal vyhledejte a vyberte svůj účet úložiště. 
    V nabídce účtu úložiště vyberte **Průzkumník služby Storage**.

    * Pro verzi desktopu [si stáhněte a nainstalujte Průzkumník služby Azure Storage](https://www.storageexplorer.com/). 
    Potom připojte Průzkumník služby Storage k účtu úložiště podle kroků uvedených v části [Začínáme s Průzkumník služby Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md). 
    Další informace najdete v tématu [rychlý Start: vytvoření objektu BLOB v úložišti objektů pomocí Průzkumník služby Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

Při vytváření a přidávání schémat nepotřebujete aplikaci logiky. Pokud ale chcete použít schéma, vaše aplikace logiky potřebuje propojit s integračním účtem, kam toto schéma ukládáte. Přečtěte si, [Jak propojit Logic Apps s účty pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Pokud ještě nemáte aplikaci logiky, přečtěte si, [jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-schemas"></a>Přidání schémat

1. Přihlaste se k webu <a href="https://portal.azure.com" target="_blank">Azure Portal</a> pomocí přihlašovacích údajů svého účtu Azure.

1. Pokud chcete najít a otevřít účet pro integraci, v hlavní nabídce Azure vyberte **všechny služby**. Do vyhledávacího pole zadejte "účet pro integraci". Vyberte **účty pro integraci**.

   ![Najít účet pro integraci](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Vyberte účet pro integraci, do kterého chcete přidat schéma, například:

   ![Vybrat účet pro integraci](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. Na stránce **Přehled** účtu pro integraci vyberte v části **komponenty**dlaždici **schémata** .

   ![Vybrat schémata](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. Po otevření stránky **schémata** klikněte na tlačítko **Přidat**.

   ![Zvolit přidat](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

V závislosti na velikosti souboru schématu (. XSD) postupujte podle kroků pro nahrání schématu, které je buď až [2 MB](#smaller-schema) [, nebo více než 2 MB, až 8 MB](#larger-schema).

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Přidat schémata až do 2 MB

1. V části **Přidat schéma**zadejte název schématu. 
   Ponechat vybraný **malý soubor** . Vedle pole **schéma** vyberte ikonu složky. Vyhledejte a vyberte schéma, které nahráváte, například:

   ![Nahrání menšího schématu](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. Až budete připraveni, klikněte na **tlačítko OK**.

   Po dokončení nahrávání schématu se schéma zobrazí v seznamu **schémata** .

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Přidat schémata více než 2 MB

Chcete-li přidat větší schémata, můžete své schéma nahrát do kontejneru objektů blob Azure v účtu úložiště Azure. Postup přidání schémat se liší v závislosti na tom, jestli má váš kontejner objektů BLOB veřejný přístup pro čtení. Nejdřív ověřte, jestli má váš kontejner objektů BLOB veřejný přístup pro čtení, a to pomocí následujících kroků: [Nastavení úrovně veřejného přístupu pro kontejner objektů BLOB](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Kontrolovat úroveň přístupu kontejneru

1. Otevřete Průzkumník služby Azure Storage. V okně Průzkumníka rozbalte své předplatné Azure, pokud ještě není rozbalené.

1. Rozbalte položku **účty úložiště** > {*Your-Storage-Account*} > **kontejnerů objektů BLOB**. Vyberte kontejner objektů BLOB.

1. V místní nabídce kontejneru objektů BLOB vyberte **nastavit úroveň veřejného přístupu**.

   * Pokud má váš kontejner objektů BLOB aspoň veřejný přístup, klikněte na **Zrušit**a pak na této stránce použijte následující postup: [nahrání do kontejnerů s veřejným přístupem](#public-access)

     ![Veřejný přístup](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Pokud Váš kontejner objektů BLOB nemá veřejný přístup, klikněte na **Zrušit**a pak na této stránce proveďte následující kroky: [nahrání do kontejnerů bez veřejného přístupu](#public-access) .

     ![Bez veřejného přístupu](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Odeslat do kontejnerů s veřejným přístupem

1. Nahrajte schéma do svého účtu úložiště. 
   V pravém okně vyberte **nahrát**.

1. Po dokončení nahrávání vyberte nahrané schéma. Na panelu nástrojů vyberte možnost **Kopírovat adresu URL** , abyste ZKOPÍROVALI adresu URL schématu.

1. Vraťte se do Azure Portal, kde je otevřeno podokno **Přidat schéma** . 
   Zadejte název pro sestavení. 
   Vyberte **velký soubor (větší než 2 MB)**. 

   Nyní se zobrazí okno **identifikátor URI obsahu** , nikoli pole **schématu** .

1. Do pole **identifikátor URI obsahu** vložte adresu URL vašeho schématu. 
   Dokončete přidání vašeho schématu.

Po dokončení nahrávání schématu se schéma zobrazí v seznamu **schémata** . Na stránce **Přehled** v účtu pro integraci se teď v části **komponenty**na dlaždici **schémata** zobrazuje počet nahraných schémat.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Odeslat do kontejnerů bez přístupu veřejnosti

1. Nahrajte schéma do svého účtu úložiště. 
   V pravém okně vyberte **nahrát**.

1. Po dokončení nahrávání vygenerujte sdílený přístupový podpis (SAS) pro vaše schéma. 
   V místní nabídce schématu vyberte **získat sdílený přístupový podpis**.

1. V podokně **sdílený přístupový podpis** vyberte možnost **generovat identifikátor URI sdíleného přístupového podpisu na úrovni kontejneru**  >  **vytvořit**. 
   Po vygenerování adresy URL SAS klikněte vedle pole **Adresa URL** na možnost **Kopírovat**.

1. Vraťte se do Azure Portal, kde je otevřeno podokno **Přidat schéma** . Vyberte možnost **velký soubor**.

   Nyní se zobrazí okno **identifikátor URI obsahu** , nikoli pole **schématu** .

1. Do pole **identifikátor URI obsahu** vložte identifikátor URI SAS, který jste předtím vygenerovali. Dokončete přidání vašeho schématu.

Po dokončení nahrávání schématu se schéma zobrazí v seznamu **schémata** . Na stránce **Přehled** v účtu pro integraci se teď v části **komponenty**na dlaždici **schémata** zobrazuje počet nahraných schémat.

## <a name="edit-schemas"></a>Upravit schémata

Chcete-li aktualizovat existující schéma, je nutné nahrát nový soubor schématu, který obsahuje požadované změny. Můžete si ale nejdřív stáhnout existující schéma pro úpravy.

1. V <a href="https://portal.azure.com" target="_blank">Azure Portal</a>vyhledejte a otevřete účet pro integraci, pokud ještě není otevřený.

1. V hlavní nabídce Azure vyberte **všechny služby**. 
   Do vyhledávacího pole zadejte "účet pro integraci". 
   Vyberte **účty pro integraci**.

1. Vyberte účet pro integraci, ve kterém chcete schéma aktualizovat.

1. Na stránce **Přehled** účtu pro integraci vyberte v části **komponenty**dlaždici **schémata** .

1. Po otevření stránky **schémata** vyberte své schéma. 
   Chcete-li nejprve stáhnout a upravit schéma, klikněte na tlačítko **Stáhnout**a uložte schéma.

1. Až budete připraveni na nahrání aktualizovaného schématu, vyberte na stránce **schémata** schéma, které chcete aktualizovat, a zvolte **aktualizovat**.

1. Vyhledejte a vyberte aktualizované schéma, které chcete nahrát. 
   Po nahrání souboru schématu se v seznamu **schémat** zobrazí aktualizované schéma.

## <a name="delete-schemas"></a>Odstranit schémata

1. V <a href="https://portal.azure.com" target="_blank">Azure Portal</a>vyhledejte a otevřete účet pro integraci, pokud ještě není otevřený.

1. V hlavní nabídce Azure vyberte **všechny služby**. 
   Do vyhledávacího pole zadejte "účet pro integraci". 
   Vyberte **účty pro integraci**.

1. Vyberte účet pro integraci, ve kterém chcete schéma odstranit.

1. Na stránce **Přehled** účtu pro integraci vyberte v části **komponenty**dlaždici **schémata** .

1. Po otevření stránky **schémata** vyberte schéma a zvolte **Odstranit**.

1. Chcete-li potvrdit, že chcete schéma odstranit, klikněte na tlačítko **Ano**.

## <a name="next-steps"></a>Další kroky

* [Další informace o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
* [Další informace o mapách](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Další informace o transformacích](../logic-apps/logic-apps-enterprise-integration-transform.md)
