---
title: Data z Data Lake Store v registru ve službě Azure Data Catalog | Dokumentace Microsoftu
description: Data z Data Lake Store v registru ve službě Azure Data Catalog
services: data-lake-store,data-catalog
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 3294d91e-a723-41b5-9eca-ace0ee408a4b
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 8da9f0f8aeb36d9ff2f87511c902dd719bc755b9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441594"
---
# <a name="register-data-from-data-lake-store-in-azure-data-catalog"></a>Data z Data Lake Store v registru ve službě Azure Data Catalog
V tomto článku se dozvíte, jak integrovat Azure Data Lake Store pomocí služby Azure Data Catalog zjistitelnost vašich dat v organizaci pomocí integrace s katalogem Data Catalog. Další informace o vytváření katalogu dat, naleznete v tématu [Azure Data Catalog](../data-catalog/data-catalog-what-is-data-catalog.md). Scénáře, ve které můžete použít katalog dat najdete v tématu [běžné scénáře Azure Data Catalog](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Povolení předplatného Azure** pro veřejné verzi Preview služby Data Lake Store. Viz [pokyny](data-lake-store-get-started-portal.md).
* **Účet Azure Data Lake Store**. Postupujte podle pokynů v tématu [Začínáme s Azure Data Lake Store s použitím webu Azure Portal](data-lake-store-get-started-portal.md). Pro účely tohoto kurzu, vytvořit účet Data Lake Store s názvem **datacatalogstore**.

    Jakmile vytvoříte účet, do něj nahrát ukázkovou datovou sadu. Pro účely tohoto kurzu, dejte nám nahrát soubor CSV v části **AmbulanceData** složky [úložiště Git Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Můžete použít různé klienty, například [Průzkumníka služby Azure Storage](http://storageexplorer.com/), jak nahrát data do kontejneru objektů blob.
* **Azure Data Catalog**. Vaše organizace musí už mít službu Azure Data Catalog pro vaši organizaci vytvořený. Pro každou organizaci je povolen pouze jeden katalog.

## <a name="register-data-lake-store-as-a-source-for-data-catalog"></a>Registr Data Lake Store jako zdroj pro službu Data Catalog

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Přejděte na `https://azure.microsoft.com/services/data-catalog`a klikněte na tlačítko **Začínáme**.
1. Přihlaste se do portálu Azure Data Catalog a klikněte na tlačítko **publikovat data**.

    ![Registrace zdroje dat](./media/data-lake-store-with-data-catalog/register-data-source.png "registraci zdroje dat")
1. Na další stránce klikněte na tlačítko **spustit aplikaci**. Tento příkaz stáhne soubor manifestu aplikace ve vašem počítači. Poklikejte na soubor manifestu a spusťte tak aplikaci.
1. Na úvodní stránce klikněte na tlačítko **přihlášení**a zadejte svoje přihlašovací údaje.

    ![Úvodní obrazovka](./media/data-lake-store-with-data-catalog/welcome.screen.png "úvodní obrazovka")
1. Na stránce vyberte zdroj dat vyberte **Azure Data Lake**a potom klikněte na tlačítko **Další**.

    ![Vyberte zdroj dat](./media/data-lake-store-with-data-catalog/select-source.png "vyberte zdroj dat")
1. Na další stránce zadejte název účtu Data Lake Store, kterou chcete zaregistrovat ve službě Data Catalog. U ostatních možností ponechte jako výchozí a potom klikněte na tlačítko **připojit**.

    ![Připojit ke zdroji dat](./media/data-lake-store-with-data-catalog/connect-to-source.png "připojit ke zdroji dat")
1. Na další stránku je možné rozdělit do následující segmenty.

    a. **Hierarchie serverů** pole představuje strukturu složek účtu Data Lake Store. **$Root** představuje kořenový adresář účtu Data Lake Store a **AmbulanceData** představuje složky vytvořené v kořenovém adresáři účtu Data Lake Store.

    b. **Dostupné objekty** pole obsahuje seznam souborů a složek **AmbulanceData** složky.

    c. **Objekty k registraci** pole zobrazí soubory a složky, které chcete zaregistrovat ve službě Azure Data Catalog.

    ![Zobrazit datovou strukturu](./media/data-lake-store-with-data-catalog/view-data-structure.png "zobrazit datové struktury")
1. Pro účely tohoto kurzu byste měli zaregistrovat všechny soubory v adresáři. K tomu, klikněte na tlačítko (![přesunout objekty](./media/data-lake-store-with-data-catalog/move-objects.png "přesunout objekty")) přesuňte všechny soubory a **objekty k registraci** pole.

    Protože data se zaregistruje v katalog dat pro celou organizaci, je doporučený postup pro přidání některých metadat, který můžete později použít k rychlému vyhledání dat. Můžete například přidat e-mailovou adresu vlastníka dat (například jeden, který nahrává data) nebo přidat značku, kterou chcete identifikovat data. Snímek obrazovky níže ukazuje značku, že přidáte k datům.

    ![Zobrazit datovou strukturu](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "zobrazit datové struktury")

    Klikněte na tlačítko **zaregistrovat**.
1. Následující snímek obrazovky označuje, že data se úspěšně zaregistrovala ve službě Data Catalog.

    ![Registrace je dokončena](./media/data-lake-store-with-data-catalog/registration-complete.png "zobrazit datové struktury")
1. Klikněte na tlačítko **zobrazit portál** přejít zpět na portál služby Data Catalog a ověřte, že je nyní přístup k registrované datové z portálu. K vyhledání dat, můžete použít značku, kterou jste použili při registraci data.

     ![Vyhledávání dat v katalogu](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "vyhledávání v katalogu dat")
1. Teď můžete provádět operace, jako je přidání poznámky a dokumentaci k datům. Další informace najdete v následujících tématech.

    * [Přidání poznámek ke zdrojům dat ve službě Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
    * [Dokumentování zdrojů dat ve službě Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Další informace najdete v tématech
* [Přidání poznámek ke zdrojům dat ve službě Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
* [Dokumentování zdrojů dat ve službě Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrace Data Lake Store s ostatními službami Azure](data-lake-store-integrate-with-other-services.md)
