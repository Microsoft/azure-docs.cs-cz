---
title: Správa prostředků Azure Data Lake Store v Průzkumníku služby Azure Storage
description: Zjistěte, jak přistupovat k datům a prostředkům Azure Data Lake Store a spravovat je v Průzkumníku služby Azure Storage.
Keywords: Azure Data Lake Store, Azure Storage Explorer
services: Data Lake Store
documentationcenter: ''
author: jejiang
manager: DJ
editor: Jenny Jiang
ms.assetid: ''
ms.service: Data Lake Store
ms.custom: Azure Data Lake Store
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 02/05/2018
ms.author: jejiang
ms.openlocfilehash: a02844c678c08d8aefbceb16d3908faeffd755fb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="manage-azure-data-lake-store-resources-by-using-storage-explorer"></a>Správa prostředků Azure Data Lake Store pomocí Průzkumníka služby Storage

[Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview) je služba pro ukládání velkých objemů nestrukturovaných dat, jako jsou textová nebo binární data. K datům můžete získat přístup odkudkoli přes protokol HTTP nebo HTTPS. Data Lake Store v Průzkumníku služby Azure Storage umožňuje přistupovat k datům a prostředkům Data Lake Store a spravovat je spolu s dalšími entitami Azure, jako jsou objekty blob a fronty. Nyní můžete pomocí jednoho nástroje spravovat různé entity Azure na jednom místě.

Další výhodou je, že ke správě dat Data Lake Store nepotřebujete mít oprávnění k předplatnému. V Průzkumníku služby Storage můžete připojit cestu Data Lake Store k uzlu **Místní a připojené**, pokud vám k tomu někdo udělí oprávnění.

## <a name="prerequisites"></a>Požadavky
K dokončení kroků v tomto článku budete potřebovat následující:

*   Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial).
*   Účet Azure Data Lake Store. Pokyny k jeho vytvoření najdete v tématu [Začínáme s Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-get-started-portal).

## <a name="install-storage-explorer"></a>Instalace Průzkumníka služby Storage

Nainstalujte nejnovější části Průzkumníka služby Azure Storage z [webu produktu](https://azure.microsoft.com/features/storage-explorer/). Instalace podporuje verze pro Windows, Linux a Mac.

## <a name="connect-to-an-azure-subscription"></a>Připojení k předplatnému Azure

1. V Průzkumníku služby Storage vyberte ikonu modulu plug-in na levé straně.
       
   ![Ikona modulu plug-in](./media/data-lake-store-in-storage-explorer/plug-in-icon.png)
 
2. Vyberte **Přidat účet Azure** a pak vyberte **Přihlásit**.

   ![Dialogové okno Připojit ke službě Azure Storage](./media/data-lake-store-in-storage-explorer/connect-to-azure-subscription.png)

2. V dialogovém okně **Přihlášení k účtu** zadejte své přihlašovací údaje Azure.

    ![Dialogové okno pro přihlášení do Azure](./media/data-lake-store-in-storage-explorer/sign-in.png)

3. Vyberte ze seznamu své předplatné a pak vyberte **Použít**.

    ![Informace o předplatném a tlačítko Použít](./media/data-lake-store-in-storage-explorer/apply-subscription.png)

    Podokno **PRŮZKUMNÍK** se aktualizuje a zobrazí účty ve vybraném předplatném.

    ![Seznam účtů](./media/data-lake-store-in-storage-explorer/account-list.png)

Právě jste úspěšně propojili službu Azure Data Lake Store se svým předplatným Azure.

## <a name="connect-to-data-lake-store"></a>Připojení ke službě Data Lake Store
Můžete přistupovat i k prostředkům, které neexistují ve vašem předplatném, pokud vám někdo poskytne identifikátor URI těchto prostředků. Po přihlášení se pak můžete připojit ke službě Data Lake Store s použitím tohoto identifikátoru URI.
1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte **Místní a připojené**.
3. Klikněte pravým tlačítkem na **Data Lake Store** a pak vyberte **Připojit ke službě Data Lake Store**.

      ![Možnost Připojit ke službě Data Lake Store v místní nabídce](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Zadejte identifikátor URI. Nástroj přejde do umístění adresy URL, kterou jste právě zadali.

      ![Dialogové okno Připojit ke službě Data Lake Store s textovým polem pro zadání identifikátoru URI](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

      ![Výsledek připojení ke službě Data Lake Store](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-attach-finish.png)

## <a name="view-an-azure-data-lake-store-accounts-contents"></a>Zobrazení obsahu účtu Azure Data Lake Store
Prostředky účtu Azure Data Lake Store obsahují složky a soubory.

Následující postup ukazuje, jak zobrazit obsah účtu Data Lake Store v Průzkumníku služby Storage:

1. Otevřete Průzkumníka služby Storage.
2. V levém podokně rozbalte předplatné obsahující účet Azure Data Lake Store, který chcete zobrazit.
3. Rozbalte **Data Lake Store**.
4. Klikněte pravým tlačítkem na uzel účtu Azure Data Lake Store, který chcete zobrazit, a pak vyberte **Otevřít**. Účet Data Lake Store můžete otevřít také tak, že na něj dvakrát kliknete. 
   
   V hlavním podokně se zobrazí obsah účtu Data Lake Store.

   ![Hlavní podokno se seznamem složek](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-toolbar-mainpane.png) 

## <a name="manage-resources-in-azure-data-lake-store"></a>Správa prostředků v Azure Data Lake Store

Prostředky Azure Data Lake Store můžete spravovat prováděním následujících operací:
*   Procházení prostředků Data Lake Store napříč několika účty Azure Data Lake  
*   Použití připojovacího řetězce k připojení ke službě Data Lake Store a její přímé správě 
*   Zobrazení prostředků Data Lake Store sdílených ostatními prostřednictvím seznamu ACL v části **Místní a připojené**
*   Provádění operací CRUD se soubory a složkami: podpora rekurzivních složek a vícenásobně vybraných souborů 
*   Přetahování a přidávání složek pro rychlý přístup k nedávným umístěním. Tato operace odpovídá desktopovému prostředí Průzkumníka souborů. 
*   Kopírování a otevírání hypertextových odkazů Azure Data Lake jedním kliknutím v Průzkumníku služby Storage 
*   Zobrazení protokolu aktivit se stavem aktivit v pravém dolním podokně
*   Zobrazení statistik složek a vlastností souborů

## <a name="manage-resources-in-azure-storage-explorer"></a>Správa prostředků v Průzkumníku služby Azure Storage
Po vytvoření účtu Azure Data Lake Store můžete provádět následující operace:

* Nahrávání složek a souborů, stahování složek a souborů a otevírání prostředků na místním počítači
* Připnutí na **Rychlý přístup**, vytvoření nové složky, kopírování adresy URL a výběr všech
* Kopírování a vložení, přejmenování, odstranění, aktualizace a získání statistik složek

Následující položky ukazují, jak spravovat prostředky v účtu Azure Data Lake Store. Postupujte podle kroků pro úlohu, kterou chcete provést.

### <a name="upload-files"></a>Nahrání souborů

1. Na panelu nástrojů v hlavním podokně vyberte **Nahrát** a pak v rozevírací nabídce vyberte **Nahrát soubory**.

   ![Položka nabídky Nahrát soubory](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-menu.png) 

2. V dialogovém okně **Vyberte soubory k nahrání** vyberte soubory, které chcete nahrát.

   ![Dialogové okno pro nahrání souborů](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-files-dialog.png)

3. Vyberte **Otevřít** a zahajte nahrávání.

### <a name="upload-a-folder"></a>Nahrání složky

1. Na panelu nástrojů v hlavním podokně vyberte **Nahrát** a pak v rozevírací nabídce vyberte **Nahrát složku**.

   ![Položka nabídky Nahrát složku](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-menu.png) 
     
2. V dialogovém okně **Vyberte složku k nahrání** vyberte složku, kterou chcete nahrát. Pak klikněte na **Vybrat složku**.

   ![Dialogové okno pro nahrání složek](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-dialog.png)      

   Spustí se nahrávání.

   ![Dialogové okno s probíhajícím nahráváním](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-upload-folder-drag.png) 

> [!NOTE] 
> Nahrávání můžete spustit přímým přetažením složek a souborů na místním počítači. 
       
### <a name="download-folders-or-files-to-your-local-computer"></a>Stažení složek nebo souborů na místní počítač

1. Vyberte složky nebo soubory, které chcete stáhnout.
2. Na panelu nástrojů v hlavním podokně vyberte **Stáhnout**.
3. V dialogovém okně **Vyberte složku, do které se mají stažené soubory uložit** zadejte umístění a název.
4. Vyberte **Uložit**.

### <a name="open-a-folder-or-file-from-your-local-computer"></a>Otevření složky nebo souboru z místního počítače

1. Vyberte složku nebo soubor, který chcete otevřít.
2. Na panelu nástrojů v hlavním podokně vyberte **Otevřít**. Nebo klikněte pravým tlačítkem na vybranou složku nebo soubor a pak v místní nabídce vyberte **Otevřít**.

Soubor se stáhne a otevře v aplikaci, která je přidružená k základnímu typu tohoto souboru. Nebo se v hlavním podokně otevře složka.

![Otevřený soubor](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-open.png) 

### <a name="copy-folders-or-files-to-the-clipboard"></a>Kopírování složek nebo souborů do schránky

1. Vyberte složky nebo soubory, které chcete kopírovat.
2. Na panelu nástrojů v hlavním podokně vyberte **Kopírovat**. Nebo klikněte pravým tlačítkem na vybrané složky nebo soubory a pak v místní nabídce vyberte **Kopírovat**.
3. V levém podokně přejděte do jiného účtu Data Lake Store a dvojím kliknutím ho zobrazte v hlavním podokně.
4. Na panelu nástrojů v hlavním podokně vyberte **Vložit** a vytvořte kopii. Nebo vyberte **Vložit** v místní nabídce cíle.

![Výběry pro kopírování složky](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-copy-paste.png)

> [!NOTE] 
> Operace kopírování a vložení mezi různými typy úložiště se nepodporují. Můžete kopírovat složky nebo soubory Data Lake Store a vkládat je do jiného účtu Data Lake Store. *Nemůžete* však kopírovat složky nebo soubory Data Lake Store a vkládat je do úložiště objektů blob v Azure ani naopak.
> 
> Operace kopírování a vkládání fungují tak, že se složky nebo soubory stáhnou do místního počítače a pak se nahrají do cíle. Nástroj *neprovádí* tuto akci na pozadí. Operace kopírování a vkládání velkých souborů jsou pomalé. V současné době probíhá optimalizace vysoce výkonného kopírování a přesouvání souborů.

### <a name="delete-folders-or-files"></a>Odstranění složek nebo souborů

1. Vyberte složky nebo soubory, které chcete odstranit.
2. Na panelu nástrojů v hlavním podokně vyberte **Odstranit**. Nebo klikněte pravým tlačítkem na vybrané složky nebo soubory a pak v místní nabídce vyberte **Odstranit**.
3. V potvrzovacím dialogovém okně vyberte **Ano**.

![Výběry pro odstranění složky](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-delete.png)

### <a name="pin-to-quick-access"></a>Připnutí na Rychlý přístup

1. Vyberte složku, kterou chcete připnout.
2. Na panelu nástrojů v hlavním podokně vyberte **Připnout na Rychlý přístup**.

   V levém podokně se vybraná složka přidá do uzlu **Rychlý přístup**.

   ![Výběry pro připnutí složky na Rychlý přístup](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-quick-access.png)

Po připnutí složky k uzlu **Rychlý přístup** můžete snadno přistupovat k prostředkům.

### <a name="use-deep-links"></a>Použití přímých odkazů
Pokud máte adresu URL, můžete ji zadat do adresního řádku v Průzkumníku souborů nebo v prohlížeči. Pak se automaticky spustí Storage Explorer.exe (Průzkumník služby Storage) a přejde do umístění adresy URL.

![Přímý odkaz v Průzkumníku souborů](./media/data-lake-store-in-storage-explorer/storageexplorer-adls-deep-link.png)


## <a name="next-steps"></a>Další kroky
* Podívejte se na [nejnovější poznámky k verzi a videa pro Průzkumníka služby Storage](http://www.storageexplorer.com).
* Zjistěte, jak [spravovat službu Azure Cosmos DB v Průzkumníku služby Azure Storage](https://docs.microsoft.com/en-us/azure/cosmos-db/storage-explorer).
* [Začínáme s Průzkumníkem služby Storage](https://docs.microsoft.com/en-us/azure/vs-azure-tools-storage-manage-with-storage-explorer).
* [Začínáme s Azure Data Lake Store](https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-overview).
* Podívejte se na [video na YouTube věnované používání služby Azure Cosmos DB v Průzkumníku služby Azure Storage](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
