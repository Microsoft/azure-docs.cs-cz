---
title: Integrace Data Lake Storage Gen1 s Azure Data Catalog
description: Naučte se, jak registrovat data z Azure Data Lake Storage Gen1 v Azure Data Catalog, aby bylo možné zjistit data ve vaší organizaci.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 02544489816f5711ca6e599c2bce03737c747934
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92106616"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Zaregistrujte data z Azure Data Lake Storage Gen1 Azure Data Catalog
V tomto článku se naučíte, jak integrovat Azure Data Lake Storage Gen1 s Azure Data Catalog k tomu, aby byla data zjistitelná v rámci organizace tím, že je integruje s Data Catalog. Další informace o katalogu dat najdete v tématu [Azure Data Catalog](../data-catalog/overview.md). Pro pochopení scénářů, ve kterých můžete použít Data Catalog, přečtěte si téma [Azure Data Catalog běžných scénářů](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Předpoklady
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Povolte pro data Lake Storage Gen1 předplatné Azure** . Viz [pokyny](data-lake-store-get-started-portal.md).
* **Účet Data Lake Storage Gen1**. Postupujte podle pokynů v tématu [Začínáme s Azure Data Lake Storage Gen1 pomocí Azure Portal](data-lake-store-get-started-portal.md). Pro tento kurz vytvořte účet Data Lake Storage Gen1 s názvem **datacatalogstore**.

    Po vytvoření účtu nahrajte do něj ukázkovou datovou sadu. V tomto kurzu nám Nahrajte všechny soubory. csv ve složce **AmbulanceData** v [úložišti Git Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Pro nahrání dat do kontejneru objektů blob můžete použít různé klienty, například [Průzkumník služby Azure Storage](https://storageexplorer.com/).
* **Azure Data Catalog**. Vaše organizace už musí mít vytvořený Azure Data Catalog pro vaši organizaci. Pro každou organizaci je povolený jenom jeden katalog.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Registrovat Data Lake Storage Gen1 jako zdroj pro Data Catalog

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Přejděte na `https://azure.microsoft.com/services/data-catalog` a klikněte na **Začínáme**.
1. Přihlaste se do portálu Azure Data Catalog a klikněte na tlačítko **Publikovat data**.

    ![Registrace zdroje dat](./media/data-lake-store-with-data-catalog/register-data-source.png "Registrace zdroje dat")
1. Na další stránce klikněte na **Spustit aplikaci**. Tím se stáhne soubor manifestu aplikace na vašem počítači. Dvakrát klikněte na soubor manifestu a spusťte tak aplikaci.
1. Na úvodní stránce klikněte na **Přihlásit** se a zadejte svoje přihlašovací údaje.

    ![Obrazovka Vítejte](./media/data-lake-store-with-data-catalog/welcome.screen.png "Obrazovka Vítejte")
1. Na stránce vyberte zdroj dat vyberte možnost **Azure Data Lake Store** a potom klikněte na tlačítko **Další**.

    ![Výběr zdroje dat](./media/data-lake-store-with-data-catalog/select-source.png "Výběr zdroje dat")
1. Na další stránce zadejte název Data Lake Storage Gen1 účtu, který chcete zaregistrovat v Data Catalog. U ostatních možností ponechte výchozí nastavení a klikněte na **připojit**.

    ![Připojit ke zdroji dat](./media/data-lake-store-with-data-catalog/connect-to-source.png "Připojit ke zdroji dat")
1. Další stránku lze rozdělit do následujících segmentů.

    a. Okno **hierarchie serveru** představuje strukturu složek Data Lake Storage Gen1 účtu. **$Root** představuje kořen Data Lake Storage Gen1 účtu a **AmbulanceData** představuje složku vytvořenou v kořenovém adresáři Data Lake Storage Gen1 účtu.

    b. Pole **dostupné objekty** obsahuje seznam souborů a složek ve složce **AmbulanceData** .

    c. Pole **objekty k registraci** obsahují seznam souborů a složek, které chcete zaregistrovat v Azure Data Catalog.

    ![Snímek obrazovky dialogového okna Microsoft Azure Data Catalog-Store Account](./media/data-lake-store-with-data-catalog/view-data-structure.png "Zobrazit datovou strukturu")
1. V tomto kurzu byste měli zaregistrovat všechny soubory v adresáři. V takovém případě kliknutím na tlačítko (![přesunout objekty](./media/data-lake-store-with-data-catalog/move-objects.png "Přesunout objekty")) přesuňte všechny soubory do pole **objekty, které mají být registrovány** .

    Vzhledem k tomu, že se data budou registrovat v katalogu dat pro celé organizace, je doporučený postup pro přidání některých metadat, která můžete později použít k rychlému vyhledání dat. Můžete například přidat e-mailovou adresu vlastníka dat (například jeden, kdo odesílá data), nebo přidat značku k identifikaci dat. Následující snímek obrazovky ukazuje značku, kterou přidáte k datům.

    ![Snímek obrazovky dialogového okna Microsoft Azure Data Catalog-Store Account se značkou, která byla přidána do dat s názvem.](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Zobrazit datovou strukturu")

    Klikněte na **Zaregistrovat**.
1. Následující snímek obrazovky označuje, že data jsou úspěšně registrována v Data Catalog.

    ![Registrace dokončena](./media/data-lake-store-with-data-catalog/registration-complete.png "Zobrazit datovou strukturu")
1. Kliknutím na **Zobrazit portál** se vraťte na portál Data Catalog a ověřte, že teď máte přístup k registrovaným datům z portálu. Chcete-li vyhledávat data, můžete použít značku, kterou jste použili při registraci dat.

     ![Vyhledat data v katalogu](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Vyhledat data v katalogu")
1. Nyní můžete provádět operace, jako je přidání poznámek a dokumentace k datům. Další informace najdete na následujících odkazech.

    * [Přidání poznámek ke zdrojům dat v Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
    * [Dokumentování zdrojů dat v Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Viz také
* [Přidání poznámek ke zdrojům dat v Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
* [Dokumentování zdrojů dat v Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrace Data Lake Storage Gen1 s dalšími službami Azure](data-lake-store-integrate-with-other-services.md)