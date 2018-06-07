---
title: Zaregistrovat v Azure Data Catalog dat z Data Lake Store | Microsoft Docs
description: Zaregistrovat v Azure Data Catalog dat z Data Lake Store
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
ms.openlocfilehash: 3f1bc925b772265a9f72c34f5ac661088123bb1a
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626133"
---
# <a name="register-data-from-data-lake-store-in-azure-data-catalog"></a>Zaregistrovat v Azure Data Catalog dat z Data Lake Store
V tomto článku se dozvíte, jak integrovat Azure Data Lake Store s Azure Data Catalog zjistitelnost vaše data v rámci organizace integrací s katalogem Data Catalog. Další informace o vytváření katalogu dat najdete v tématu [Azure Data Catalog](../data-catalog/data-catalog-what-is-data-catalog.md). Chcete-li pochopit scénáře, ve které můžete použít Data Catalog, přečtěte si téma [běžné scénáře Azure Data Catalog](../data-catalog/data-catalog-common-scenarios.md).

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Aktivujte předplatné Azure** pro verzi Public Preview Data Lake Store. Viz [pokyny](data-lake-store-get-started-portal.md).
* **Účet Azure Data Lake Store**. Postupujte podle pokynů v tématu [Začínáme s Azure Data Lake Store s použitím webu Azure Portal](data-lake-store-get-started-portal.md). V tomto kurzu Vytvoření účtu Data Lake Store názvem **datacatalogstore**.

    Po vytvoření účtu tím, že nahrajete ukázková datové sady do ní. V tomto kurzu, dejte nám odeslat všechny soubory .csv v **AmbulanceData** složku [úložiště Git Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/). Můžete používat různé klienty, jako třeba [Azure Storage Explorer](http://storageexplorer.com/), odesílat data do kontejneru objektů blob.
* **Azure Data Catalog**. Vaše organizace již musí mít Azure Data Catalog pro vaši organizaci vytvořený. Pro každou organizaci, je povolen pouze jeden katalog.

## <a name="register-data-lake-store-as-a-source-for-data-catalog"></a>Registrace Data Lake Store jako zdroj pro katalog Data Catalog

> [!VIDEO https://channel9.msdn.com/Series/AzureDataLake/ADCwithADL/player]

1. Přejděte na `https://azure.microsoft.com/services/data-catalog`a klikněte na tlačítko **Začínáme**.
2. Přihlaste se k portálu Azure Data Catalog a klikněte na tlačítko **publikovat data**.

    ![Registrace zdroje dat](./media/data-lake-store-with-data-catalog/register-data-source.png "registrace zdroje dat")
3. Na další stránce klikněte na tlačítko **spustit aplikaci**. To se stažení souboru manifestu aplikace ve vašem počítači. Poklikejte na soubor manifestu a spusťte aplikaci.
4. Na úvodní stránce klikněte na tlačítko **přihlášení**a zadejte svá pověření.

    ![Úvodní obrazovka](./media/data-lake-store-with-data-catalog/welcome.screen.png "úvodní obrazovce")
5. Na stránce vyberte zdroj dat vyberte **Azure Data Lake**a potom klikněte na **Další**.

    ![Vyberte zdroj dat](./media/data-lake-store-with-data-catalog/select-source.png "vyberte zdroj dat")
6. Na další stránce zadejte název účtu Data Lake Store, který chcete zaregistrovat v katalogu Data Catalog. Nechte ostatní možnosti jako výchozí a pak klikněte na tlačítko **Connect**.

    ![Připojení ke zdroji dat](./media/data-lake-store-with-data-catalog/connect-to-source.png "připojit ke zdroji dat")
7. Na další stránku, je možné rozdělit do následující segmenty.

    a. **Hierarchii serverů** pole představuje struktura složek účet Data Lake Store. **$Root** reprezentuje kořen účet Data Lake Store, a **AmbulanceData** představuje složku vytvořit v kořenové složce účtu Data Lake Store.

    b. **Dostupné objekty** pole obsahuje seznam souborů a složek **AmbulanceData** složky.

    c. **Objekty k registraci** pole jsou uvedené soubory a složky, které chcete zaregistrovat v Azure Data Catalog.

    ![Zobrazit datové struktury](./media/data-lake-store-with-data-catalog/view-data-structure.png "zobrazit datové struktury")
8. V tomto kurzu byste měli zaregistrovat všechny soubory v adresáři. Kliknutím (![přesun objektů](./media/data-lake-store-with-data-catalog/move-objects.png "přesun objektů")) přesuňte všechny soubory pro **objekty k registraci** pole.

    Data budou zaregistrovány ve katalog dat pro celou organizaci, proto je doporučený postup pro přidání některé metadata, která později můžete rychle vyhledat potřebná data. Například můžete přidat e-mailovou adresu pro vlastník dat (například jeden, který odesílá data) nebo přidat značku identifikovat data. Snímek obrazovky níže ukazuje značku přidat do data.

    ![Zobrazit datové struktury](./media/data-lake-store-with-data-catalog/view-selected-data-structure.png "zobrazit datové struktury")

    Klikněte na tlačítko **zaregistrovat**.
9. Následující snímek obrazovky označuje, že data se úspěšně zaregistrován v katalogu Data Catalog.

    ![Registrace je dokončena](./media/data-lake-store-with-data-catalog/registration-complete.png "zobrazit datové struktury")
10. Klikněte na tlačítko **zobrazit portál** přejít zpět na portál pro katalog Data Catalog a ověřte, že jste registrované datové teď přístup z portálu. Pokud chcete hledat data, můžete použít značky, které jste použili při registraci data.

     ![Vyhledávání dat v katalogu](./media/data-lake-store-with-data-catalog/search-data-in-catalog.png "vyhledávání dat v katalogu")
11. Teď můžete dělat operace, jako je přidání poznámky a dokumentaci k datům. Další informace najdete v následujících tématech.

    * [Přidání poznámek ke zdrojům dat v katalogu Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
    * [Zdroje dat dokumentu v katalogu Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)

## <a name="see-also"></a>Další informace najdete v tématech
* [Přidání poznámek ke zdrojům dat v katalogu Data Catalog](../data-catalog/data-catalog-how-to-annotate.md)
* [Zdroje dat dokumentu v katalogu Data Catalog](../data-catalog/data-catalog-how-to-documentation.md)
* [Integrace s jinými službami Azure Data Lake Store](data-lake-store-integrate-with-other-services.md)
