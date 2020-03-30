---
title: Ověření xml pomocí schémat
description: Přidání schémat pro ověření dokumentů XML v logicových aplikacích Azure pomocí sady Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/06/2019
ms.openlocfilehash: ade29318fdd1510d03ddd208534258a30b256e98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75979377"
---
# <a name="validate-xml-with-schemas-in-azure-logic-apps-with-enterprise-integration-pack"></a>Ověřování XML s využitím schémat v Azure Logic Apps s rozšířením Enterprise Integration Pack

Chcete-li zkontrolovat, zda dokumenty používají platný XML a mají očekávaná data v předdefinovaném formátu pro scénáře podnikové integrace v Azure Logic Apps, vaše aplikace logiky můžete použít schémata. Schéma můžete také ověřit zprávy, které aplikace logiky výměnu ve scénářích business-to-business (B2B).

Omezení související s účty integrace a artefakty, jako jsou schémata, najdete v [tématu omezení a informace o konfiguraci pro Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md#integration-account-limits).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud předplatné nemáte, <a href="https://azure.microsoft.com/free/" target="_blank">zaregistrujte si bezplatný účet Azure</a>.

* [Účet integrace,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) ve kterém ukládáte schémata a další artefakty pro podnikovou integraci a řešení B2B (business-to-business). 

  Pokud je vaše schéma [2 MB nebo menší](#smaller-schema), můžete přidat schéma do účtu integrace přímo z portálu Azure. Pokud je však vaše schéma větší než 2 MB, ale ne větší než [limit velikosti schématu](../logic-apps/logic-apps-limits-and-config.md#artifact-capacity-limits), můžete schéma nahrát do účtu úložiště Azure. 
  Chcete-li přidat toto schéma do účtu integrace, můžete vytvořit odkaz na účet úložiště z účtu integrace. 
  Pro tento úkol, zde jsou položky, které potřebujete: 

  * [Účet úložiště Azure,](../storage/common/storage-account-overview.md) kde vytvoříte kontejner objektů blob pro vaše schéma. Přečtěte si, jak [vytvořit účet úložiště](../storage/common/storage-account-create.md). 

  * Kontejner s objekty blob pro ukládání schématu. Přečtěte si, jak [vytvořit kontejner objektů blob](../storage/blobs/storage-quickstart-blobs-portal.md). 
  Uri obsahu kontejneru budete potřebovat později, když přidáte schéma do účtu integrace.

  * [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md), který můžete použít pro správu účtů úložiště a kontejnerů objektů blob. 
  Pokud chcete použít Průzkumníka úložiště, zvolte jednu z možností:
  
    * Na webu Azure Portal najděte a vyberte svůj účet úložiště. 
    V nabídce účtu úložiště vyberte **Průzkumník a ukládání dat**.

    * Pro verzi pro stolní počítače [stáhněte a nainstalujte Azure Storage Explorer](https://www.storageexplorer.com/). 
    Potom připojte Průzkumníka úložiště ke svému účtu úložiště podle kroků v [části Začínáme s Průzkumníkem úložiště](../vs-azure-tools-storage-manage-with-storage-explorer.md). 
    Další informace najdete [v tématu Úvodní příručka: Vytvoření objektu blob v úložišti objektů pomocí Průzkumníka úložiště Azure](../storage/blobs/storage-quickstart-blobs-storage-explorer.md).

Při vytváření a přidávání schémat nepotřebujete aplikaci logiky. Chcete-li však použít schéma, aplikace logiky potřebuje propojení s účtem integrace, kde ukládáte toto schéma. Přečtěte [si, jak propojit aplikace logiky s účty integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account). Pokud ještě nemáte aplikaci logiky, přečtěte si, [jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-schemas"></a>Přidání schémat

1. Přihlaste se k webu <a href="https://portal.azure.com" target="_blank">Azure Portal</a> pomocí přihlašovacích údajů svého účtu Azure.

1. Pokud chcete najít a otevřít účet integrace, v hlavní nabídce Azure vyberte **Všechny služby**. Do vyhledávacího pole zadejte "účet integrace". Vyberte **integrační účty**.

   ![Najít účet integrace](./media/logic-apps-enterprise-integration-schemas/find-integration-account.png)

1. Vyberte účet integrace, do kterého chcete přidat schéma, například:

   ![Vybrat účet integrace](./media/logic-apps-enterprise-integration-schemas/select-integration-account.png)

1. Na stránce **Přehled** účtu integrace vyberte v části **Komponenty**dlaždici **Schémata.**

   ![Vyberte "Schémata"](./media/logic-apps-enterprise-integration-schemas/select-schemas.png)

1. Po otevření stránky **Schémata** zvolte **Přidat**.

   ![Zvolte "Přidat"](./media/logic-apps-enterprise-integration-schemas/add-schema.png)

Na základě velikosti souboru schématu (.xsd) postupujte podle pokynů pro nahrání schématu, které je až [2 MB](#smaller-schema) nebo [více než 2 MB, až 8 MB](#larger-schema).

<a name="smaller-schema"></a>

### <a name="add-schemas-up-to-2-mb"></a>Přidání schémat až do 2 MB

1. V části **Přidat schéma**zadejte název schématu. 
   Zachovat **vybraný malý soubor.** Vedle pole **Schéma** zvolte ikonu složky. Najděte a vyberte schéma, které nahráváte, například:

   ![Nahrání menšího schématu](./media/logic-apps-enterprise-integration-schemas/upload-smaller-schema-file.png)

1. Až budete připraveni, zvolte **OK**.

   Po dokončení nahrávání schématu se schéma zobrazí v seznamu **schémat.**

<a name="larger-schema"></a>

### <a name="add-schemas-more-than-2-mb"></a>Přidání schémat větší než 2 MB

Chcete-li přidat větší schémata, můžete nahrát schéma do kontejneru objektů blob Azure ve vašem účtu úložiště Azure. Postup přidání schémat se liší podle toho, jestli má váš kontejner objektů blob veřejný přístup pro čtení. Takže nejprve zkontrolujte, jestli váš kontejner objektů blob má veřejný přístup pro čtení podle následujících kroků: [Nastavení úrovně veřejného přístupu pro kontejner objektů blob](../vs-azure-tools-storage-explorer-blobs.md#set-the-public-access-level-for-a-blob-container)

#### <a name="check-container-access-level"></a>Zkontrolovat úroveň přístupu ke kontejneru

1. Otevřete Průzkumníka úložišť Azure. V okně Průzkumníka rozbalte předplatné Azure, pokud už není rozšířené.

1. Rozbalte **položku Účty úložiště** > {*váš účet úložiště*} > **kontejnery objektů blob**. Vyberte kontejner objektů blob.

1. V místní nabídce kontejneru objektů blob vyberte **Nastavit úroveň veřejného přístupu**.

   * Pokud má kontejner objektů blob alespoň veřejný přístup, zvolte **Zrušit**a na této stránce postupujte takto: [Nahrát do kontejnerů s veřejným přístupem](#public-access)

     ![Přístup veřejnosti](media/logic-apps-enterprise-integration-schemas/azure-blob-container-public-access.png)

   * Pokud váš kontejner objektů blob nemá veřejný přístup, zvolte **Zrušit**a na této stránce postupujte takto: [Nahrát do kontejnerů bez veřejného přístupu](#public-access)

     ![Žádný veřejný přístup](media/logic-apps-enterprise-integration-schemas/azure-blob-container-no-public-access.png)

<a name="public-access"></a>

#### <a name="upload-to-containers-with-public-access"></a>Nahrávání do kontejnerů s veřejným přístupem

1. Nahrajte schéma do svého účtu úložiště. 
   V pravém okně zvolte **Nahrát**.

1. Po dokončení nahrávání vyberte nahrané schéma. Na panelu nástrojů zvolte **Kopírovat adresu URL,** abyste zkopírovali adresu URL schématu.

1. Vraťte se na portál Azure, kde je otevřené podokno **Přidat schéma.** 
   Zadejte název sestavení. 
   Zvolte **Velký soubor (větší než 2 MB).** 

   Nyní se zobrazí pole **Identifikátor URI obsahu,** nikoli pole **Schéma.**

1. Do pole **Identifikátor URI obsahu** vložte adresu URL schématu. 
   Dokončete přidávání schématu.

Po dokončení nahrávání schématu se schéma zobrazí v seznamu **schémat.** Na stránce **Přehled** účtu integrace se v části **Komponenty**nyní zobrazuje dlaždice **Schémata** počet nahraných schémat.

<a name="no-public-access"></a>

#### <a name="upload-to-containers-without-public-access"></a>Nahrávání do kontejnerů bez přístupu veřejnosti

1. Nahrajte schéma do svého účtu úložiště. 
   V pravém okně zvolte **Nahrát**.

1. Po dokončení nahrávání vygenerujte sdílený přístupový podpis (SAS) pro schéma. 
   V místní nabídce schématu vyberte **Získat sdílený přístupový podpis**.

1. V podokně **Sdílený přístupový podpis** vyberte **Generovat vytvoření identifikátoru URI** > na úrovni kontejneru na úrovni kontejneru .**Create** 
   Po vygenerování adresy URL SAS zvolte vedle pole **ADRESA URL** **možnost Kopírovat**.

1. Vraťte se na portál Azure, kde je otevřené podokno **Přidat schéma.** Zvolte **Velký soubor**.

   Nyní se zobrazí pole **Identifikátor URI obsahu,** nikoli pole **Schéma.**

1. Do pole **Identifikátor URI obsahu** vložte identifikátor URI SAS, který jste dříve vygenerovali. Dokončete přidávání schématu.

Po dokončení nahrávání schématu se schéma zobrazí v seznamu **schémat.** Na stránce **Přehled** účtu integrace se v části **Komponenty**nyní zobrazuje dlaždice **Schémata** počet nahraných schémat.

## <a name="edit-schemas"></a>Upravit schémata

Chcete-li aktualizovat existující schéma, je třeba nahrát nový soubor schématu, který obsahuje požadované změny. Můžete však nejprve stáhnout existující schéma pro úpravy.

1. Na <a href="https://portal.azure.com" target="_blank">webu Azure Portal</a>najděte a otevřete svůj účet integrace, pokud už není otevřený.

1. V hlavní nabídce Azure vyberte **Všechny služby**. 
   Do vyhledávacího pole zadejte "účet integrace". 
   Vyberte **integrační účty**.

1. Vyberte účet integrace, ve kterém chcete aktualizovat schéma.

1. Na stránce **Přehled** účtu integrace vyberte v části **Komponenty**dlaždici **Schémata.**

1. Po otevření stránky **Schémata** vyberte schéma. 
   Chcete-li nejprve stáhnout a upravit schéma, zvolte **Stáhnout**a uložte schéma.

1. Až budete připraveni nahrát aktualizované schéma, vyberte na stránce **Schémata** schéma, které chcete aktualizovat, a zvolte **Aktualizovat**.

1. Najděte a vyberte aktualizované schéma, které chcete nahrát. 
   Po dokončení nahrávání souboru schématu se aktualizované schéma zobrazí v seznamu **schémat.**

## <a name="delete-schemas"></a>Odstranit schémata

1. Na <a href="https://portal.azure.com" target="_blank">webu Azure Portal</a>najděte a otevřete svůj účet integrace, pokud už není otevřený.

1. V hlavní nabídce Azure vyberte **Všechny služby**. 
   Do vyhledávacího pole zadejte "účet integrace". 
   Vyberte **integrační účty**.

1. Vyberte účet integrace, ve kterém chcete odstranit schéma.

1. Na stránce **Přehled** účtu integrace vyberte v části **Komponenty**dlaždici **Schémata.**

1. Po otevření stránky **Schémata** vyberte schéma a zvolte **Odstranit**.

1. Chcete-li potvrdit, že chcete schéma odstranit, zvolte **Ano**.

## <a name="next-steps"></a>Další kroky

* [Další informace o balíčku Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)
* [Další informace o mapách](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Další informace o transformacích](../logic-apps/logic-apps-enterprise-integration-transform.md)
