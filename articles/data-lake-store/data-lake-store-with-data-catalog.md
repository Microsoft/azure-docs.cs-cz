---
title: Registrace dat z Azure Data Lake Storage Gen1 v Katalogu dat Azure | Dokumenty společnosti Microsoft
description: Registrace dat z Azure Data Lake Storage Gen1 v Azure Data Catalog
services: data-lake-store,data-catalog
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 3294d91e-a723-41b5-9eca-ace0ee408a4b
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: fd887560c0011fb1ec2141e33f02f7e3d8a39c81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60196489"
---
# <a name="register-data-from-azure-data-lake-storage-gen1-in-azure-data-catalog"></a>Registrace dat z Azure Data Lake Storage Gen1 v Azure Data Catalog
V tomto článku se dozvíte, jak integrovat Azure Data Lake Storage Gen1 s Azure Data Catalog, aby vaše data zjistitelné v rámci organizace integrací s katalogu dat. Další informace o katalogizaci dat najdete v [tématu Azure Data Catalog](../data-catalog/data-catalog-what-is-data-catalog.md). Informace o scénářích, ve kterých můžete používat katalog dat, najdete v [tématu Běžných scénářů katalogu dat Azure](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Povolte předplatné Azure** pro Storage Data Lake Gen1. Viz [pokyny](data-lake-store-get-started-portal.md).
* **Účet Gen1 úložiště datového jezera**. Postupujte podle pokynů na [webu Začínáme s Azure Data Lake Storage Gen1 pomocí portálu Azure Portal](data-lake-store-get-started-portal.md). Pro účely tohoto kurzu vytvořte účet Data Lake Storage Gen1 s názvem **datacatalogstore**.

    Po vytvoření účtu do něj nahrajte ukázkovou sadu dat. Pro účely tohoto kurzu nám dejte nahrát všechny soubory .csv ve složce **AmbulanceData** v [úložišti Git Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). K nahrání dat do kontejneru objektů blob můžete použít různé klienty, například [Průzkumník a údržbu zařízení Azure](https://storageexplorer.com/)Storage Explorer .
* **Katalog dat Azure**. Vaše organizace už musí mít vytvořený katalog dat Azure pro vaši organizaci. Pro každou organizaci je povolen pouze jeden katalog.

## <a name="register-data-lake-storage-gen1-as-a-source-for-data-catalog"></a>Registrace data lake storage gen1 jako zdroj pro katalog dat

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Přejděte `https://azure.microsoft.com/services/data-catalog`na položku a klepněte na **tlačítko Začínáme**.
1. Přihlaste se do portálu Azure Data Catalog a klikněte na tlačítko **Publikovat data**.

    ![Registrace zdroje dat](./media/data-lake-store-with-data-catalog/register-data-source.png "Registrace zdroje dat")
1. Na další stránce klikněte na **spustit aplikaci**. Tím se stáhne soubor manifestu aplikace do počítače. Poklepáním na soubor manifestu spusťte aplikaci.
1. Na úvodní stránce klikněte na **Přihlásit**se a zadejte svoje přihlašovací údaje.

    ![Obrazovka Vítejte](./media/data-lake-store-with-data-catalog/welcome.screen.png "Obrazovka Vítejte")
1. Na stránce Vybrat zdroj dat vyberte **Azure Data Lake Store**a klikněte na **Další**.

    ![Výběr zdroje dat](./media/data-lake-store-with-data-catalog/select-source.png "Výběr zdroje dat")
1. Na další stránce zadejte název účtu Data Lake Storage Gen1, který chcete zaregistrovat v katalogu dat. Ostatní možnosti ponechejte jako výchozí a klepněte na tlačítko **Připojit**.

    ![Připojit ke zdroji dat](./media/data-lake-store-with-data-catalog/connect-to-source.png "Připojit ke zdroji dat")
1. Další stránku lze rozdělit do následujících segmentů.

    a. Pole **Hierarchie serveru** představuje strukturu složek účtu Data Lake Storage Gen1. **$Root** představuje kořen účtu Data Lake Storage Gen1 a **AmbulanceData** představuje složku vytvořenou v kořenovém adresáři účtu Data Lake Storage Gen1.

    b. V poli **Dostupné objekty** jsou uvedeny soubory a složky ve složce **AmbulanceData.**

    c. **Objekty, které mají být registrovány** pole uvádí soubory a složky, které chcete zaregistrovat v katalogu dat Azure.

    ![Zobrazit strukturu dat](./media/data-lake-store-with-data-catalog/view-data-structure.png "Zobrazit strukturu dat")
1. V tomto kurzu byste měli zaregistrovat všechny soubory v adresáři. Za tímto účelem klepněte na tlačítko (![přesunout objekty)](./media/data-lake-store-with-data-catalog/move-objects.png "Přesunutí objektů")a přesuňte všechny soubory do pole **Objekty, které mají být registrovány.**

    Vzhledem k tomu, že data budou registrována v katalogu dat pro celou organizaci, je doporučeným přístupem k přidání některých metadat, která můžete později použít k rychlému vyhledání dat. Můžete například přidat e-mailovou adresu vlastníka dat (například toho, kdo data nahrává) nebo přidat značku pro identifikaci dat. Při snímání obrazovky se zobrazí značka, kterou přidáte do dat.

    ![Zobrazit strukturu dat](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "Zobrazit strukturu dat")

    Klepněte na tlačítko **Registrovat**.
1. Následující zachycení obrazovky označuje, že data jsou úspěšně zaregistrována v katalogu dat.

    ![Registrace dokončena](./media/data-lake-store-with-data-catalog/registration-complete.png "Zobrazit strukturu dat")
1. Kliknutím na **Zobrazit portál** se vrátíte na portál Katalog dat a ověříte, zda máte nyní přístup k registrovaným datům z portálu. Chcete-li data prohledávat, můžete použít značku, kterou jste použili při registraci dat.

     ![Hledání dat v katalogu](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "Hledání dat v katalogu")
1. Nyní můžete provádět operace, jako je přidávání poznámk a dokumentace k datům. Další informace naleznete v následujících odkazech.

    * [Přidávání notových údajů v katalogu dat](../data-catalog/data-catalog-how-to-annotate.md)
    * [Zdroje dat dokumentu v katalogu dat](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Viz také
* [Přidávání notových údajů v katalogu dat](../data-catalog/data-catalog-how-to-annotate.md)
* [Zdroje dat dokumentu v katalogu dat](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrace Data Lake Storage Gen1 s dalšími službami Azure](data-lake-store-integrate-with-other-services.md)
