---
title: Začínáme s Azure Data Lake Storage Gen1 – portál
description: Pomocí Azure Portal vytvořte účet Data Lake Storage Gen1 a v účtu proveďte základní operace.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 428ed96c3223e644b0c78712723231a5fabbdc77
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578576"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Začínáme s Azure Data Lake Storage Gen1 pomocí Azure Portal

> [!div class="op_single_selector"]
> * [Azure Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Naučte se, jak pomocí Azure Portal vytvořit účet Data Lake Storage Gen1 a provádět základní operace, jako je vytváření složek, nahrávání a stahování datových souborů, odstranění účtu atd. Další informace najdete v tématu [přehled Azure Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu musíte mít tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Vytvoření účtu Data Lake Storage Gen1

1. Přihlaste se k novému webu [Azure Portal](https://portal.azure.com).
2. Klikněte na **vytvořit prostředek > > úložiště data Lake Storage Gen1**.
3. V okně **nový Data Lake Storage Gen1** zadejte hodnoty, jak je znázorněno na následujícím snímku obrazovky:

    ![Vytvořit nový účet Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Vytvořit nový účet Data Lake Storage Gen1")

   * **Název:** Zadejte jedinečný název Data Lake Storage Gen1 účtu.
   * **Předplatné**. Vyberte předplatné, ve kterém chcete vytvořit nový účet Data Lake Storage Gen1.
   * **Skupina prostředků:** Vyberte existující skupinu prostředků nebo ji vytvořte pomocí možnosti **Vytvořit novou**. Skupina prostředků je kontejner, který obsahuje související prostředky pro aplikaci. Další informace najdete v tématu [Skupiny prostředků v Azure](../azure-resource-manager/management/overview.md#resource-groups).
   * **Umístění**: vyberte umístění, kde chcete vytvořit účet Data Lake Storage Gen1.
   * **Nastavení šifrování**. Existují tři možnosti:

     * **Nepovolovat šifrování**.
     * Pokud chcete Data Lake Storage Gen1 spravovat šifrovací klíče, **použijte klíče spravované pomocí Data Lake Storage Gen1**.
     * **Použít klíče z vašeho vlastního trezoru klíčů**. Můžete vybrat existující službu Azure Key Vault nebo vytvořit novou. Chcete-li používat klíče z Key Vault, je nutné přiřadit oprávnění pro účet Data Lake Storage Gen1 pro přístup k Azure Key Vault. Pokyny najdete v části [Přiřazení oprávnění pro Azure Key Vault](#assign-permissions-to-azure-key-vault).

        ![Snímek obrazovky s novým Data Lake Storage oknem pro obecné 1 a nastavení šifrování](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Data Lake Storage Gen1 šifrování")

        Klikněte na **OK** v okně **Nastavení šifrování**.

        Další informace najdete v tématu [šifrování dat v Azure Data Lake Storage Gen1](./data-lake-store-encryption.md).

4. Klikněte na **Vytvořit**. Pokud jste se rozhodli připnout účet na řídicí panel, přejdete zpátky na řídicí panel a uvidíte průběh zřizování účtu Data Lake Storage Gen1. Po zřízení účtu Data Lake Storage Gen1 se zobrazí okno účtu.

## <a name="assign-permissions-to-azure-key-vault"></a><a name="assign-permissions-to-azure-key-vault"></a>Přiřazení oprávnění pro Azure Key Vault

Pokud jste použili klíče z Azure Key Vault ke konfiguraci šifrování pro Data Lake Storage Gen1 účet, musíte nakonfigurovat přístup mezi Data Lake Storage Gen1m účtem a účtem Azure Key Vault. To uděláte podle následujících kroků.

1. Pokud jste použili klíče z Azure Key Vault, zobrazí okno pro Data Lake Storage Gen1 účet upozornění v horní části. Kliknutím na upozornění otevřete **Šifrování**.

    ![Snímek obrazovky okna Data Lake Storage Gen1 účtu s upozorněním, že je nutná konfigurace oprávnění trezoru klíčů Instalaci zobrazíte kliknutím sem.](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Data Lake Storage Gen1 šifrování")
2. V okně se zobrazují dvě možnosti pro konfiguraci přístupu.

    ![Snímek obrazovky okna šifrování](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Data Lake Storage Gen1 šifrování")

   * V první možnosti kliknutím na **Udělit oprávnění** nakonfigurujte přístup. První možnost je povolena, pouze pokud je uživatel, který vytvořil účet Data Lake Storage Gen1, také správcem pro Azure Key Vault.
   * Druhou možností je spustit rutinu PowerShellu zobrazenou v okně. Musíte být vlastníkem Azure Key Vaultu nebo mít možnost udělovat oprávnění pro Azure Key Vault. Po spuštění rutiny se vraťte do okna a kliknutím na **Povolit** nakonfigurujte přístup.

> [!NOTE]
> Data Lake Storage Gen1 účet můžete také vytvořit pomocí šablon Azure Resource Manager. Tyto šablony jsou dostupné na stránce [Šablony rychlého startu Azure](https://azure.microsoft.com/resources/templates/?term=data+lake+store):
> * Bez šifrování dat: [Nasaďte Azure Data Lake Storage Gen1 účet bez šifrování dat](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
> * S šifrováním dat pomocí Data Lake Storage Gen1: [Nasaďte Data Lake Storage Gen1 účet s šifrováním (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
> * S šifrováním dat pomocí Azure Key Vault: [Nasaďte Data Lake Storage Gen1 účet s šifrováním (Key Vault)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
>
>

## <a name="create-folders"></a><a name="createfolder"></a>Vytváření složek

Můžete vytvořit složky pod účtem Data Lake Storage Gen1, abyste mohli spravovat a ukládat data.

1. Otevřete účet Data Lake Storage Gen1, který jste vytvořili. V levém podokně klikněte na **všechny prostředky**a potom v okně **všechny prostředky** klikněte na název účtu, pod kterým chcete vytvořit složky. Pokud jste účet připnuli na úvodní panel, klikněte na dlaždici tohoto účtu.
2. V okně Data Lake Storage Gen1 účtu klikněte na **Průzkumník dat**.

    ![Snímek obrazovky okna Data Lake Storage účet 1. generace s možností Průzkumník dat s názvem.](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Vytváření složek v účtu Data Lake Storage Gen1")
3. V okně Průzkumník dat klikněte na **Nová složka**, zadejte název nové složky a pak klikněte na **OK**.

    ![Snímek obrazovky okna Průzkumník dat s možností nová složka a textové pole vytvořit novou složku s názvem.](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Vytváření složek v účtu Data Lake Storage Gen1")

    Nově vytvořená složka se zobrazí v okně **Průzkumník dat**. Můžete vytvářet vnořené složky až po libovolnou úroveň.

    ![Vytváření složek v účtu Data Lake](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Vytváření složek v účtu Data Lake")

## <a name="upload-data"></a><a name="uploaddata"></a>Nahrání dat

Data můžete na účet Data Lake Storage Gen1 nahrát přímo na úrovni kořenového adresáře nebo do složky, kterou jste v rámci účtu vytvořili.

1. V okně **Průzkumník dat** klikněte na **Nahrát**.
2. V okně **Nahrát soubory** přejděte k souborům, které chcete nahrát, a pak klikněte na **Přidat vybrané soubory**. Pro nahrání můžete také vybrat více než jeden soubor.

    ![Nahrání dat](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Nahrání dat")

Pokud hledáte ukázková data, která byste mohli nahrát, můžete použít složku **Ambulance Data** z [úložiště Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="actions-available-on-the-stored-data"></a><a name="properties"></a>Akce dostupné u uložených dat

Klikněte na ikonu se třemi tečkami naproti souboru a v místní nabídce klikněte na akci, kterou chcete s daty provést.

![Vlastnosti dat](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Vlastnosti dat")

## <a name="secure-your-data"></a>Zabezpečení dat

Data uložená ve vašem účtu Data Lake Storage Gen1 můžete zabezpečit pomocí Azure Active Directory a řízení přístupu (ACL). Pokyny k tomu, jak to provést, najdete v tématu [zabezpečení dat v Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="delete-your-account"></a>Odstranit účet

Pokud chcete účet Data Lake Storage Gen1 odstranit, v okně Data Lake Storage Gen1 klikněte na **Odstranit**. Tuto akci je nutné potvrdit, a proto se zobrazí výzva k zadání názvu účtu, který chcete odstranit. Zadejte název účtu a klikněte na možnost **Odstranit**.

![Odstranit účet Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Odstranění účtu Data Lake")

## <a name="next-steps"></a>Další kroky

* [Použití Azure Data Lake Storage Gen1 pro požadavky na velké objemy dat](data-lake-store-data-scenarios.md)
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
