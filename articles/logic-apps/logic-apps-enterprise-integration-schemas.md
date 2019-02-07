---
title: Ověření XML se schématy – Azure Logic Apps | Dokumentace Microsoftu
description: Přidání schémat pro ověření XML dokumenty v Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 56c5846c-5d8c-4ad4-9652-60b07aa8fc3b
ms.date: 02/06/2019
ms.openlocfilehash: 03ac2e0f42ff05165aa2313d823710a71c7dffec
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768299"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Ověření XML se schématy v Azure Logic Apps sadou Enterprise Integration Pack

Pokud chcete zkontrolovat, že dokumenty používají platný kód XML a mají očekávaná data v předdefinované formát pro podnikové scénáře integrace v Azure Logic Apps, můžete použít svou aplikaci logiky schémata. Schéma můžete také ověřit zprávy, které aplikacím logiky systému exchange ve scénářích business-to-business (B2B).

Omezení související s účty pro integraci a součásti, jako jsou schémata najdete v tématu [omezení a konfigurační informace pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

* [Účtu pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) kam ukládat vaše schémata a další artefakty pro podnikovou integraci a řešení business-to-business (B2B). 

  Pokud je schéma [2 MB nebo méně](#smaller-schema), schéma můžete přidat k účtu pro integraci přímo z portálu Azure portal. Nicméně pokud schéma je větší než 2 MB, ale ne větší než [limit velikosti schématu](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), schéma můžete nahrát do účtu služby Azure storage. 
  Pokud chcete přidat toto schéma účtu integrace, si můžete propojit k vašemu účtu úložiště z vašeho účtu integrace. 
  Pro tuto úlohu Zde jsou položky, které budete potřebovat: 

  * [Účet úložiště Azure](../storage/common/storage-account-overview.md) kde vytvořit kontejner objektů blob pro schéma. Zjistěte, jak [vytvořit účet úložiště](../storage/common/storage-quickstart-create-account.md). 

  * Kontejner objektů blob pro ukládání vašeho schématu. Zjistěte, jak [vytvořte kontejner objektů blob](../storage/blobs/storage-quickstart-blobs-portal.md). 
  Budete potřebovat identifikátor URI obsahu vašeho kontejneru později přidáte schéma účtu integrace.

  * [Průzkumník služby Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md), který můžete použít ke správě účtů úložiště a kontejnery objektů blob. 
  Pokud chcete použít Průzkumníka služby Storage, zvolte jednu z možností tady:
  
    * Na webu Azure Portal vyhledejte a vyberte svůj účet úložiště. 
    V nabídce účtu úložiště, vyberte **Průzkumníka služby Storage**.

    * Pro desktopová verze [stažení a instalace Průzkumníka služby Azure Storage](https://www.storageexplorer.com/). 
    Potom propojení Průzkumníka služby Storage do účtu úložiště pomocí následujících kroků v [Začínáme se Storage Explorerem](../vs-azure-tools-storage-manage-with-storage-explorer.md). 
    Další informace najdete v tématu [rychlý start: Vytvoření objektu blob v úložišti objektů pomocí Průzkumníka služby Azure Storage](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

Není nutné aplikaci logiky po vytvoření a přidání schémat. Ale chcete-li využívají schéma, aplikace logiky potřebuje propojení účtu pro integraci kam se ukládají tohoto schématu. Přečtěte si [postup propojení aplikace logiky s účty pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Pokud ještě nemáte aplikace logiky, přečtěte si [postup vytvoření aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-schemas"></a>Přidání schémat

1. Přihlaste se k webu <a href="https://portal.azure.com" target="_blank">Azure Portal</a> pomocí přihlašovacích údajů svého účtu Azure.

1. K vyhledání a otevření účtu pro integraci, v hlavní nabídce Azure zvolte **všechny služby**. Do vyhledávacího pole zadejte "účet integrace". Vyberte **účty pro integraci**.

   ![Najít účet integrace](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Vyberte účet integrace, ve které chcete přidat schéma, například:

   ![Vyberte účet pro integraci](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. V účtu integrace **přehled** stránce v části **součásti**, vyberte **schémata** dlaždici.

   ![Vyberte "Schémata"](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. Po **schémata** otevře se stránka, zvolit **přidat**.

   ![Zvolte "Přidat"](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

Podle velikosti souboru schématu (XSD), postupujte podle kroků pro nahrávání schématu, který je buď [až 2 MB](#smaller-schema) nebo [více než 2 MB, 8 MB](#larger-schema).

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Přidání schémat až 2 MB

1. V části **přidat schéma**, zadejte název vašeho schématu. 
   Zachovat **malý soubor** vybrané. Vedle položky **schématu** vyberte ikonu složky. Vyhledejte a vyberte schéma, které jste při odesílání, například:

   ![Nahrát menší schématu](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. Jakmile budete připraveni, zvolte **OK**.

   Po dokončení nahrávání schématu schématu se zobrazí v **schémata** seznamu.

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Přidat schémata více než 2 MB

K přidání větší schémat, můžete nahrát schéma do kontejneru objektů blob v Azure ve vašem účtu úložiště Azure. Vaše kroky pro přidání mapy se liší v závislosti, zda má veřejné oprávnění ke čtení kontejneru objektů blob. Proto nejprve zkontrolujte, jestli má váš kontejner objektů blob veřejné oprávnění ke čtení pomocí následujících kroků: [Nastavte úroveň veřejného přístupu pro kontejner objektů blob](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Zkontrolujte úroveň přístupu kontejneru

1. Otevřete Průzkumníka služby Azure Storage. V okně Průzkumníka rozbalte vaše předplatné Azure, pokud ještě není rozbalen.

1. Rozbalte **účty úložiště** > {*svůj účet úložiště*} > **kontejnery objektů Blob**. Vyberte kontejner objektů blob.

1. V kontejneru objektů blob v místní nabídce vyberte **nastavte úroveň veřejného přístupu**.

   * Pokud alespoň veřejné oprávnění ke kontejneru objektů blob, zvolte **zrušit**a postupujte podle těchto kroků později na této stránce: [Odešlete do kontejneru s veřejného přístupu](#public-access)

     ![Veřejný přístup](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Pokud váš kontejner objektů blob nemá veřejný přístup, zvolte **zrušit**a postupujte podle těchto kroků později na této stránce: [Odešlete do kontejneru bez veřejného přístupu](#public-access)

     ![Žádný veřejný přístup](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Odešlete do kontejneru s veřejného přístupu

1. Nahrajte schéma do účtu úložiště. 
   V pravém okně vyberte **nahrát**.

1. Po dokončení nahrávání, vyberte nahrané schématu. Na panelu nástrojů zvolte **kopírování adresy URL** tak, že zkopírujete adresu URL schématu.

1. Vraťte se do portálu Azure portal kde **přidat schéma** je otevřeno podokno. 
   Zadejte název vašeho sestavení. 
   Zvolte **velký soubor (větší než 2 MB)**. 

   **Identifikátor URI obsahu** pole se teď zobrazí, spíše než **schématu** pole.

1. V **identifikátor URI obsahu** pole, vložte adresu URL vašeho schématu. 
   Dokončit přidávání vašeho schématu.

Po dokončení nahrávání schématu schématu se zobrazí v **schémata** seznamu. V účtu integrace **přehled** stránce v části **součásti**, **schémata** dlaždice nyní zobrazuje počet nahraných schémata.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Odešlete do kontejneru bez veřejného přístupu

1. Nahrajte schéma do účtu úložiště. 
   V pravém okně vyberte **nahrát**.

1. Po dokončení nahrávání se vygenerujte sdílený přístupový podpis (SAS) pro schéma. 
   V místní nabídce vašeho schématu, vyberte **získat sdílený přístupový podpis**.

1. V **sdílený přístupový podpis** vyberte **generovat úrovni kontejneru sdíleného přístupového podpisu URI** > **vytvořit**. 
   Po získá vygenerování adres URL SAS, vedle položky **URL** zvolte **kopírování**.

1. Vraťte se do portálu Azure portal kde **přidat schéma** je otevřeno podokno. Zvolte **velkých souborů**.

   **Identifikátor URI obsahu** pole se teď zobrazí, spíše než **schématu** pole.

1. V **identifikátor URI obsahu** vložte dříve vygenerovaný identifikátor URI SAS. Dokončit přidávání vašeho schématu.

Po dokončení nahrávání schématu schématu se zobrazí v **schémata** seznamu. V účtu integrace **přehled** stránce v části **součásti**, **schémata** dlaždice nyní zobrazuje počet nahraných schémata.

## <a name="edit-schemas"></a>Upravit schémata

Aktualizovat stávající schéma, budete muset nahrát nový soubor schématu, který má požadované změny. Je však nejprve stáhnout existující schéma pro úpravy.

1. V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a>, najít a otevřete svůj účet integrace, pokud není otevřen.

1. V hlavní nabídce Azure zvolte **všechny služby**. 
   Do vyhledávacího pole zadejte "účet integrace". 
   Vyberte **účty pro integraci**.

1. Vyberte, ve které chcete aktualizovat schéma účtu integrace.

1. V účtu integrace **přehled** stránce v části **součásti**, vyberte **schémata** dlaždici.

1. Po **schémata** otevře se stránka, vyberte schéma. 
   Chcete-li stáhnout a upravit schéma nejprve, zvolte **Stáhnout**a uložit schéma.

1. Až budete připravení nahrát aktualizace schématu na **schémata** stránky, vyberte schéma, kterou chcete aktualizovat a zvolte **aktualizovat**.

1. Vyhledejte a vyberte aktualizované schéma, které chcete nahrát. 
   Po dokončení nahrávání souboru schématu, zobrazí se v aktualizované schéma **schémata** seznamu.

## <a name="delete-schemas"></a>Odstranit schémata

1. V <a href="https://portal.azure.com" target="_blank">webu Azure portal</a>, najít a otevřete svůj účet integrace, pokud není otevřen.

1. V hlavní nabídce Azure zvolte **všechny služby**. 
   Do vyhledávacího pole zadejte "účet integrace". 
   Vyberte **účty pro integraci**.

1. Vyberte, ve které chcete odstranit schéma účtu integrace.

1. V účtu integrace **přehled** stránce v části **součásti**, vyberte **schémata** dlaždici.

1. Po **schémata** otevře se stránka, vyberte schéma a zvolte **odstranit**.

1. Pokud chcete potvrdit, že chcete odstranit schéma, zvolte **Ano**.

## <a name="next-steps"></a>Další postup

* [Další informace o Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
* [Další informace o mapování](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Další informace o transformacích](../logic-apps/logic-apps-enterprise-integration-transform.md)