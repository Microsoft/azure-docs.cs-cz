---
title: Začínáme s Azure Data Lake Storage Gen1 pomocí webu Azure portal | Dokumentace Microsoftu
description: Pomocí webu Azure portal vytvořit účet Azure Data Lake Storage Gen1 a provádění základních operací v účtu Data Lake Storage Gen1.
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: 91c069f845ef6291f867099e59e79fd059d8caf1
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2019
ms.locfileid: "54320221"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Začínáme s Azure Data Lake Storage Gen1 pomocí webu Azure portal

> [!div class="op_single_selector"]
> * [Azure Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
> 

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Zjistěte, jak pomocí webu Azure portal vytvořit účet Azure Data Lake Storage Gen1 a provádění základních operací, jako je vytváření složek, nahrávání a stahování datových souborů, odstranění účtu atd. Další informace najdete v tématu [přehled o Azure Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Požadavky
Před zahájením tohoto kurzu musíte mít tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Vytvoření účtu Data Lake Storage Gen1

1. Přihlaste se k novému webu [Azure Portal](https://portal.azure.com).
2. Klikněte na tlačítko **vytvořit prostředek > úložiště > Data Lake Storage Gen1**.
3. V **nová Data Lake Storage Gen1** okno, zadejte hodnoty, jak je znázorněno na následujícím snímku obrazovky:
   
    ![Vytvořit nový účet Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "vytvořit nový účet Data Lake Storage Gen1")
   
   * **Název**. Zadejte jedinečný název pro účet Data Lake Storage Gen1.
   * **Předplatné**. Vyberte předplatné, ve kterém chcete vytvořit nový účet Data Lake Storage Gen1.
   * **Skupina prostředků**. Vyberte existující skupinu prostředků nebo ji vytvořte pomocí možnosti **Vytvořit novou**. Skupina prostředků je kontejner, který obsahuje související prostředky pro aplikaci. Další informace najdete v tématu [Skupiny prostředků v Azure](../azure-resource-manager/resource-group-overview.md#resource-groups).
   * **Umístění**: Vyberte umístění, ve kterém chcete vytvořit účet Data Lake Storage Gen1.
   * **Nastavení šifrování**. Existují tři možnosti:
     
     * **Nepovolovat šifrování**.
     * **Používat klíče spravované službou Data Lake Storage Gen1**, pokud chcete Data Lake Storage Gen1 ke správě šifrovacích klíčů.
     * **Použít klíče z vašeho vlastního trezoru klíčů**. Můžete vybrat existující službu Azure Key Vault nebo vytvořit novou. Pokud chcete používat klíče ze služby Key Vault, je nutné přiřadit oprávnění pro účet Data Lake Storage Gen1 pro přístup k Azure Key Vault. Pokyny najdete v části [Přiřazení oprávnění pro Azure Key Vault](#assign-permissions-to-azure-key-vault).
       
        ![Šifrování data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-2.png "šifrování Data Lake Storage Gen1")
       
        Klikněte na **OK** v okně **Nastavení šifrování**.

        Další informace najdete v tématu [šifrování dat v Azure Data Lake Storage Gen1](./data-lake-store-encryption.md).

4. Klikněte na možnost **Vytvořit**. Pokud jste se rozhodli připnout účet na řídicí panel, budete přesměrováni zpět na řídicí panel a zobrazí se průběh zřizování účtu Data Lake Storage Gen1. Po zřízení účtu Data Lake Storage Gen1, se zobrazí okno účtu.

## <a name="assign-permissions-to-azure-key-vault"></a>Přiřazení oprávnění pro Azure Key Vault
Pokud jste použili klíče z Azure Key Vault ke konfiguraci šifrování pro účet Data Lake Storage Gen1, musíte nakonfigurovat přístup mezi účtem Data Lake Storage Gen1 a účet služby Azure Key Vault. To uděláte podle následujících kroků.

1. Pokud jste použili klíče z Azure Key Vault, zobrazí v okně účtu Data Lake Storage Gen1 upozornění v horní části. Kliknutím na upozornění otevřete **Šifrování**.
   
    ![Šifrování data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-3.png "šifrování Data Lake Storage Gen1")
2. V okně se zobrazují dvě možnosti pro konfiguraci přístupu.

    ![Šifrování data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-4.png "šifrování Data Lake Storage Gen1")
   
   * V první možnosti kliknutím na **Udělit oprávnění** nakonfigurujte přístup. První možností je povoleno pouze v případě, že uživatel, který vytvořil účet Data Lake Storage Gen1 je současně správcem pro Azure Key Vault.
   * Druhou možností je spustit rutinu PowerShellu zobrazenou v okně. Musíte být vlastníkem Azure Key Vaultu nebo mít možnost udělovat oprávnění pro Azure Key Vault. Po spuštění rutiny se vraťte do okna a kliknutím na **Povolit** nakonfigurujte přístup.

> [!NOTE]
> Můžete také vytvořit účet Data Lake Storage Gen1 pomocí šablon Azure Resource Manageru. Tyto šablony jsou dostupné na stránce [Šablony rychlého startu Azure](https://azure.microsoft.com/resources/templates/?term=data+lake+store):
    - Bez šifrování dat: [Nasazení účtu Azure Data Lake Storage Gen1 bez šifrování dat](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
    - S šifrováním dat pomocí služby Data Lake Storage Gen1: [Nasazení účtu Data Lake Storage Gen1 s šifrováním (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
    - S šifrováním dat pomocí služby Azure Key Vault: [Nasazení účtu Data Lake Storage Gen1 s šifrováním (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
> 
> 



## <a name="createfolder"></a>Vytváření složek v účtu Data Lake Storage Gen1
Vytváření složek v rámci účtu Data Lake Storage Gen1 ke správě a ukládání dat.

1. Otevřete účet Data Lake Storage Gen1, kterou jste vytvořili. V levém podokně klikněte na **Všechny prostředky** a pak v okně Všechny prostředky klikněte na název účtu, ve kterém chcete vytvořit složky. Pokud jste účet připnuli na úvodní panel, klikněte na dlaždici tohoto účtu.
2. V okně účtu Data Lake Storage Gen1, klikněte na tlačítko **Průzkumník dat**.
   
    ![Vytváření složek v účtu Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "vytváření složek v účtu Data Lake Storage Gen1")
3. V okně Průzkumník dat klikněte na **Nová složka**, zadejte název nové složky a pak klikněte na **OK**.
   
    ![Vytváření složek v účtu Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "vytváření složek v účtu Data Lake Storage Gen1")
   
    Nově vytvořená složka se zobrazí v okně **Průzkumník dat**. Můžete vytvářet vnořené složky libovolné úroveň pracovních stanic.
   
    ![Vytváření složek v účtu Data Lake](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "vytváření složek v účtu Data Lake")

## <a name="uploaddata"></a>Nahrání dat do účtu Data Lake Storage Gen1
Data můžete nahrát do účtu Data Lake Storage Gen1 přímo na úrovni kořenového adresáře nebo do složky, kterou jste vytvořili v rámci účtu. 

1. V okně **Průzkumník dat** klikněte na **Nahrát**. 
2. V okně **Nahrát soubory** přejděte k souborům, které chcete nahrát, a pak klikněte na **Přidat vybrané soubory**. Pro nahrání můžete také vybrat více než jeden soubor.

    ![Nahrání dat](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Nahrání dat")

Pokud hledáte ukázková data, která byste mohli nahrát, můžete použít složku **Ambulance Data** z [úložiště Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="properties"></a>Akce dostupné u uložených dat
Klikněte na ikonu se třemi tečkami naproti souboru a v místní nabídce klikněte na akci, kterou chcete s daty provést.

![Vlastnosti dat](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Vlastnosti dat") 

## <a name="secure-your-data"></a>Zabezpečení dat
Můžete zabezpečit data uložená v účtu Data Lake Storage Gen1 pomocí Azure Active Directory a řízení přístupu (ACL). Pokyny, jak to provést, najdete v části [zabezpečení dat v Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="delete-a-data-lake-storage-gen1-account"></a>Odstranění účtu Data Lake Storage Gen1
Pokud chcete odstranit účet Data Lake Storage Gen1 z Data Lake Storage Gen1 okno, klikněte na tlačítko **odstranit**. Tuto akci je nutné potvrdit, a proto se zobrazí výzva k zadání názvu účtu, který chcete odstranit. Zadejte název účtu a klikněte na možnost **Odstranit**.

![Odstranění účtu Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "odstranění účtu Data Lake")

## <a name="next-steps"></a>Další postup
* [Použití Azure Data Lake Storage Gen1 pro potřeby velkého objemu dat](data-lake-store-data-scenarios.md) 
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)

