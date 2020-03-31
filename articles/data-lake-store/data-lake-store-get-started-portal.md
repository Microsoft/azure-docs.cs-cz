---
title: Začínáme s portálem Azure Data Lake Storage Gen1 –
description: Pomocí portálu Azure vytvořte účet Data Lake Storage Gen1 a proveďte základní operace v účtu.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 1bfb9362aa0e893a8bf1a758e5a0069e85ab227b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265581"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-the-azure-portal"></a>Začínáme s Azure Data Lake Storage Gen1 pomocí portálu Azure

> [!div class="op_single_selector"]
> * [Portál](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Naučte se, jak pomocí portálu Azure vytvořit účet Data Lake Storage Gen1 a provádět základní operace, jako je vytváření složek, nahrávání a stahování datových souborů, odstranění účtu atd. Další informace najdete [v tématu Přehled Azure Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu musíte mít tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-a-data-lake-storage-gen1-account"></a>Vytvoření účtu Data Lake Storage Gen1

1. Přihlaste se k novému webu [Azure Portal](https://portal.azure.com).
2. Klikněte **na Vytvořit prostředek > úložiště > úložiště datového jezera Gen1**.
3. V okně **New Data Lake Storage Gen1** poskytněte hodnoty, jak je znázorněno na následujícím snímku obrazovky:

    ![Vytvoření nového účtu Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Vytvoření nového účtu Data Lake Storage Gen1")

   * **Název**. Zadejte jedinečný název účtu Data Lake Storage Gen1.
   * **Předplatné**. Vyberte předplatné, pod kterým chcete vytvořit nový účet Data Lake Storage Gen1.
   * **Skupina prostředků**. Vyberte existující skupinu prostředků nebo ji vytvořte pomocí možnosti **Vytvořit novou**. Skupina prostředků je kontejner, který obsahuje související prostředky pro aplikaci. Další informace najdete v tématu [Skupiny prostředků v Azure](../azure-resource-manager/management/overview.md#resource-groups).
   * **Umístění**: Vyberte umístění, kde chcete vytvořit účet Data Lake Storage Gen1.
   * **Nastavení šifrování**. Existují tři možnosti:

     * **Nepovolovat šifrování**.
     * Pokud chcete, aby vaše šifrovací klíče spravovaly vaše šifrovací klíče, **použijte klíče spravované společností Data Lake Storage Gen1**.
     * **Použít klíče z vašeho vlastního trezoru klíčů**. Můžete vybrat existující službu Azure Key Vault nebo vytvořit novou. Chcete-li použít klíče z trezoru klíčů, musíte přiřadit oprávnění pro účet Data Lake Storage Gen1 pro přístup k trezoru klíčů Azure. Pokyny najdete v části [Přiřazení oprávnění pro Azure Key Vault](#assign-permissions-to-azure-key-vault).

        ![Šifrování Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Šifrování Data Lake Storage Gen1")

        Klikněte na **OK** v okně **Nastavení šifrování**.

        Další informace najdete [v tématu Šifrování dat v Azure Data Lake Storage Gen1](./data-lake-store-encryption.md).

4. Klikněte na **Vytvořit**. Pokud jste se rozhodli připnout účet na řídicí panel, budete převedena zpět na řídicí panel a uvidíte průběh zřizování účtu Data Lake Storage Gen1. Po zřízení účtu Data Lake Storage Gen1 se zobrazí okno účtu.

## <a name="assign-permissions-to-azure-key-vault"></a><a name="assign-permissions-to-azure-key-vault"></a>Přiřazení oprávnění pro Azure Key Vault

Pokud jste ke konfiguraci šifrování v účtu Data Lake Storage Gen1 použili klíče z trezoru klíčů Azure, je nutné nakonfigurovat přístup mezi účtem Data Lake Storage Gen1 a účtem Azure Key Vault. To uděláte podle následujících kroků.

1. Pokud jste použili klíče z trezoru klíčů Azure, okno pro účet Data Lake Storage Gen1 zobrazí upozornění v horní části. Kliknutím na upozornění otevřete **Šifrování**.

    ![Šifrování Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Šifrování Data Lake Storage Gen1")
2. V okně se zobrazují dvě možnosti pro konfiguraci přístupu.

    ![Šifrování Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/adls-encryption-4.png "Šifrování Data Lake Storage Gen1")

   * V první možnosti kliknutím na **Udělit oprávnění** nakonfigurujte přístup. První možnost je povolena pouze v případě, že uživatel, který vytvořil účet Data Lake Storage Gen1 je také správce pro Azure Key Vault.
   * Druhou možností je spustit rutinu PowerShellu zobrazenou v okně. Musíte být vlastníkem Azure Key Vaultu nebo mít možnost udělovat oprávnění pro Azure Key Vault. Po spuštění rutiny se vraťte do okna a kliknutím na **Povolit** nakonfigurujte přístup.

> [!NOTE]
> Účet Gen1 úložiště datového jezera můžete také vytvořit pomocí šablon Azure Resource Manager. Tyto šablony jsou dostupné na stránce [Šablony rychlého startu Azure](https://azure.microsoft.com/resources/templates/?term=data+lake+store):
> * Bez šifrování dat: [Nasazení účtu Azure Data Lake Storage Gen1 bez šifrování dat](https://azure.microsoft.com/resources/templates/101-data-lake-store-no-encryption/).
> * S šifrováním dat pomocí Data Lake Storage Gen1: [Nasazení účtu Data Lake Storage Gen1 s šifrováním (Data Lake)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-adls/).
> * S šifrováním dat pomocí Azure Key Vault: [Nasazení účtu Data Lake Storage Gen1 s šifrováním (trezor klíčů)](https://azure.microsoft.com/resources/templates/101-data-lake-store-encryption-key-vault/).
>
>

## <a name="create-folders"></a><a name="createfolder"></a>Vytváření složek

Můžete vytvořit složky pod účtem Data Lake Storage Gen1 pro správu a ukládání dat.

1. Otevřete účet Data Lake Storage Gen1, který jste vytvořili. V levém podokně klikněte na **Všechny prostředky**a potom v okně **Všechny prostředky** klikněte na název účtu, pod kterým chcete vytvářet složky. Pokud jste účet připnuli na úvodní panel, klikněte na dlaždici tohoto účtu.
2. V okně účtu Data Lake Storage Gen1 klikněte na **Průzkumník dat**.

    ![Vytvoření složek v účtu Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Vytvoření složek v účtu Data Lake Storage Gen1")
3. V okně Průzkumník dat klikněte na **Nová složka**, zadejte název nové složky a pak klikněte na **OK**.

    ![Vytvoření složek v účtu Data Lake Storage Gen1](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Vytvoření složek v účtu Data Lake Storage Gen1")

    Nově vytvořená složka se zobrazí v okně **Průzkumník dat**. Můžete vytvořit vnořené složky až na libovolnou úroveň.

    ![Vytvoření složek v účtu Datového jezera](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Vytvoření složek v účtu Datového jezera")

## <a name="upload-data"></a><a name="uploaddata"></a>Nahrání dat

Data můžete nahrát do účtu Data Lake Storage Gen1 přímo na kořenové úrovni nebo do složky, kterou jste v rámci účtu vytvořili.

1. V okně **Průzkumník dat** klikněte na **Nahrát**.
2. V okně **Nahrát soubory** přejděte k souborům, které chcete nahrát, a pak klikněte na **Přidat vybrané soubory**. Pro nahrání můžete také vybrat více než jeden soubor.

    ![Nahrání dat](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Nahrání dat")

Pokud hledáte ukázková data, která byste mohli nahrát, můžete použít složku **Ambulance Data** z [úložiště Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="actions-available-on-the-stored-data"></a><a name="properties"></a>Akce dostupné u uložených dat

Klikněte na ikonu se třemi tečkami naproti souboru a v místní nabídce klikněte na akci, kterou chcete s daty provést.

![Vlastnosti dat](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Vlastnosti dat")

## <a name="secure-your-data"></a>Zabezpečení dat

Data uložená v účtu Gene1 úložiště datového jezera můžete zabezpečit pomocí Azure Active Directory a řízení přístupu (ACLs). Pokyny, jak to udělat, najdete [v tématu Zabezpečení dat v Azure Data Lake Storage Gen1](data-lake-store-secure-data.md).

## <a name="delete-your-account"></a>Odstranit účet

Pokud chcete odstranit účet Data Lake Storage Gen1, klikněte z okna Data Lake Storage Gen1 na **Odstranit**. Tuto akci je nutné potvrdit, a proto se zobrazí výzva k zadání názvu účtu, který chcete odstranit. Zadejte název účtu a klikněte na možnost **Odstranit**.

![Odstranit účet Gen1 úložiště datového jezera](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Odstranění účtu Data Lake")

## <a name="next-steps"></a>Další kroky

* [Použití Azure Data Lake Storage Gen1 pro požadavky na velké objemy dat](data-lake-store-data-scenarios.md)
* [Zabezpečení dat ve službě Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Použití Azure Data Lake Analytics s datelake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Použití Azure HDInsight s úložištěm datových jezer Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
