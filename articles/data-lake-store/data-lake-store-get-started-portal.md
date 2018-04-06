---
title: Začínáme s Data Lake Storem pomocí webu Azure Portal | Dokumentace Microsoftu
description: Jak používat Azure Portal k vytvoření účtu Data Lake Store a provádění základních operací v Data Lake Storu
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: fea324d0-ad1a-4150-81f0-8682ddb4591c
ms.service: data-lake-store
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 91ed55e5f12c10e67653bdadfca80bd072779711
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="get-started-with-azure-data-lake-store-using-the-azure-portal"></a>Začínáme s Azure Data Lake Store pomocí webu Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
>
> 

Naučte se používat web Azure Portal k vytvoření účtu Azure Data Lake Store a provádění základních operací, jako je vytváření složek, nahrávání a stahování datových souborů, odstranění účtu atd. Další informace najdete v tématu [Přehled služby Azure Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Požadavky
Před zahájením tohoto kurzu musíte mít tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-an-azure-data-lake-store-account"></a>Vytvoření účtu Azure Data Lake Store

1. Přihlaste se k novému webu [Azure Portal](https://portal.azure.com).
2. Klikněte na **Vytvořit prostředek > Úložiště > Data Lake Store**.
3. V okně **Nová služba Data Lake Store** zadejte hodnoty tak, jak ukazuje následující snímek obrazovky:
   
    ![Vytvoření nového účtu Azure Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Vytvoření nového účtu Azure Data Lake Store")
   
   * **Název**. Zadejte jedinečný název účtu Data Lake Storu.
   * **Předplatné**. Vyberte předplatné, v rámci kterého chcete vytvořit nový účet Data Lake Store.
   * **Skupina prostředků**. Vyberte existující skupinu prostředků nebo ji vytvořte pomocí možnosti **Vytvořit novou**. Skupina prostředků je kontejner, který obsahuje související prostředky pro aplikaci. Další informace najdete v tématu [Skupiny prostředků v Azure](../azure-resource-manager/resource-group-overview.md#resource-groups).
   * **Umístění**: Vyberte umístění, kde chcete vytvořit účet Data Lake Store.
   * **Nastavení šifrování**. Existují tři možnosti:
     
     * **Nepovolovat šifrování**.
     * **Používat klíče spravované službou Data Lake Store**.  Vyberte tuto možnost, pokud chcete, aby služba Azure Data Lake Store spravovala vaše šifrovací klíče.
     * **Použít klíče z vašeho vlastního trezoru klíčů**. Můžete vybrat existující službu Azure Key Vault nebo vytvořit novou. Pokud chcete používat klíče ze služby Key Vault, musíte účtu Azure Data Lake Store přiřadit oprávnění pro přístup ke službě Azure Key Vault. Pokyny najdete v části [Přiřazení oprávnění pro Azure Key Vault](#assign-permissions-to-azure-key-vault).
       
        ![Šifrování Data Lake Storu](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Šifrování Data Lake Storu")
       
        Klikněte na **OK** v okně **Nastavení šifrování**.

        Další informace najdete v tématu [Šifrování dat v Azure Data Lake Store](./data-lake-store-encryption.md).

4. Klikněte na možnost **Vytvořit**. Pokud jste se rozhodli připnout účet na řídicí panel, vrátíte se na řídicí panel, kde uvidíte průběh zřizování účtu Data Lake Store. Po zřízení účtu Data Lake Store se zobrazí okno účtu.

## <a name="assign-permissions-to-azure-key-vault"></a>Přiřazení oprávnění pro Azure Key Vault
Pokud jste ke konfiguraci šifrování pro účet Data Lake Store použili klíče z Azure Key Vaultu, musíte nakonfigurovat přístup mezi účtem Data Lake Store a účtem Azure Key Vault. To uděláte podle následujících kroků.

1. Pokud jste použili klíče z Azure Key Vaultu, v okně účtu Data Lake Store se nahoře zobrazí upozornění. Kliknutím na upozornění otevřete **Šifrování**.
   
    ![Šifrování Data Lake Storu](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Šifrování Data Lake Storu")
2. V okně se zobrazují dvě možnosti pro konfiguraci přístupu.

    ![Šifrování Data Lake Storu](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Šifrování Data Lake Storu")
   
   * V první možnosti kliknutím na **Udělit oprávnění** nakonfigurujte přístup. První možnost je aktivní jenom v případě, že uživatel, který vytvořil účet Data Lake Store, je současně správcem pro Azure Key Vault.
   * Druhou možností je spustit rutinu PowerShellu zobrazenou v okně. Musíte být vlastníkem Azure Key Vaultu nebo mít možnost udělovat oprávnění pro Azure Key Vault. Po spuštění rutiny se vraťte do okna a kliknutím na **Povolit** nakonfigurujte přístup.

> [!NOTE]
> Účet Data Lake Store můžete vytvořit také pomocí šablon Azure Resource Manageru. Tyto šablony jsou dostupné na stránce [Šablony rychlého startu Azure](https://azure.microsoft.com/resources/templates/?term=data+lake+store):
    - Bez šifrování dat: [Nasazení účtu Azure Data Lake Store bez šifrování dat](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
    - S šifrováním dat pomocí služby Data Lake Store: [Nasazení účtu Data Lake Store s šifrováním (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
    - S šifrováním dat pomocí služby Azure Key Vault: [Nasazení účtu Data Lake Store s šifrováním (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
> 
> 



## <a name="createfolder"></a>Vytváření složek v účtu služby Azure Data Lake Store
V rámci účtu Data Lake Store můžete vytvářet složky, které slouží ke správě a ukládání dat.

1. Otevřete účet Data Lake Store, který jste vytvořili. V levém podokně klikněte na **Všechny prostředky** a pak v okně Všechny prostředky klikněte na název účtu, ve kterém chcete vytvořit složky. Pokud jste účet připnuli na úvodní panel, klikněte na dlaždici tohoto účtu.
2. V okně účtu Data Lake Store klikněte na možnost **Průzkumník dat**.
   
    ![Vytváření složek v účtu Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Vytváření složek v účtu Data Lake Store")
3. V okně Průzkumník dat klikněte na **Nová složka**, zadejte název nové složky a pak klikněte na **OK**.
   
    ![Vytváření složek v účtu Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Vytváření složek v účtu Data Lake Store")
   
    Nově vytvořená složka se zobrazí v okně **Průzkumník dat**. Můžete vytvářet vnořené složky do libovolné úrovně.
   
    ![Vytváření složek v účtu Data Lake Store](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Vytváření složek v účtu Data Lake Store")

## <a name="uploaddata"></a>Nahrání dat do účtu služby Azure Data Lake Store
Data můžete do účtu Azure Data Lake Store nahrát přímo na úrovni kořenového adresáře nebo do složky, kterou jste v rámci účtu vytvořili. 

1. V okně **Průzkumník dat** klikněte na **Nahrát**. 
2. V okně **Nahrát soubory** přejděte k souborům, které chcete nahrát, a pak klikněte na **Přidat vybrané soubory**. Pro nahrání můžete také vybrat více než jeden soubor.

    ![Nahrání dat](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Nahrání dat")

Pokud hledáte ukázková data, která byste mohli nahrát, můžete použít složku **Ambulance Data** z [úložiště Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="properties"></a>Akce dostupné u uložených dat
Klikněte na ikonu se třemi tečkami naproti souboru a v místní nabídce klikněte na akci, kterou chcete s daty provést.

![Vlastnosti dat](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Vlastnosti dat") 

## <a name="secure-your-data"></a>Zabezpečení dat
Data uložená v účtu Azure Data Lake Store můžete zabezpečit pomocí služby Azure Active Directory a řízení přístupu (ACL). Pokyny, jak to provést, najdete v tématu [Zabezpečení dat v Azure Data Lake Store](data-lake-store-secure-data.md).

## <a name="delete-azure-data-lake-store-account"></a>Odstranění účtu Azure Data Lake Store
Pokud chcete odstranit účet Azure Data Lake Store, v okně Data Lake Store klikněte na možnost **Odstranit**. Tuto akci je nutné potvrdit, a proto se zobrazí výzva k zadání názvu účtu, který chcete odstranit. Zadejte název účtu a klikněte na možnost **Odstranit**.

![Odstranění účtu Data Lake](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Odstranění účtu Data Lake")

## <a name="next-steps"></a>Další kroky
* [Použití Azure Data Lake Store pro potřeby velkého objemu dat](data-lake-store-data-scenarios.md) 
* [Zabezpečení dat ve službě Data Lake Store](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics se službou Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight se službou Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

