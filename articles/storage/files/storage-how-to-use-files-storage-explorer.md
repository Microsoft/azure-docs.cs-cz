---
title: Správa sdílených složek Azure pomocí Průzkumníka služby Azure Storage
description: Zjistěte, jak pomocí Průzkumníka služby Azure Storage spravovat službu Soubory Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: get-started-article
ms.date: 02/27/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: e8989cadd331021a3e35fc559421bbdd08f0b4fb
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39521190"
---
# <a name="manage-azure-file-shares-with-azure-storage-explorer"></a>Správa sdílených složek Azure pomocí Průzkumníka služby Azure Storage 
Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Tento článek vás provede základy práce se sdílenými složkami Azure pomocí [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/). Průzkumník služby Storage je oblíbený klientský nástroj, který je k dispozici pro Windows, macOS a Linux. Můžete ho použít ke správě sdílených složek Azure a dalších prostředků úložiště.

Tento postup vyžaduje nainstalovaného Průzkumníka služby Storage. Stáhnout ho a následně ho nainstalovat můžete ze stránky [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření skupiny prostředků a účtu úložiště
> * Vytvoření sdílené složky Azure 
> * Vytvoření adresáře
> * Nahrání souboru
> * Stažení souboru
> * Vytvoření a použití snímku sdílené složky

Pokud ještě nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-storage-account"></a>vytvořit účet úložiště
Průzkumníka služby Storage není možné použít k vytvoření nových prostředků. Pro účely této ukázky si vytvořte účet úložiště na webu [Azure Portal](https://portal.azure.com/). 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connect-storage-explorer-to-azure-resources"></a>Připojení Průzkumníka služby Storage k prostředkům Azure
Když poprvé spustíte Průzkumníka služby Storage, objeví se okno **Průzkumník služby Microsoft Azure Storage – Připojení**. Průzkumník služby Storage nabízí několik způsobů, jak se k účtům úložiště připojit: 

- **Přihlášení pomocí účtu Azure**: Můžete se přihlásit pomocí přihlašovacích údajů uživatele své organizace nebo účtu Microsoft. 
- **Připojení ke konkrétnímu účtu úložiště pomocí připojovacího řetězce nebo tokenu SAS**: Připojovací řetězec je speciální řetězec, který obsahuje název účtu úložiště a klíč účtu úložiště nebo token SAS. Díky tomuto tokenu získá Průzkumník služby Storage přístup přímo k účtu úložiště (místo zobrazení všech účtů úložiště obsažených na účtu Azure). Další informace o připojovacích řetězcích najdete v tématu [Konfigurace připojovacích řetězců úložiště Azure](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- **Připojení ke konkrétnímu účtu úložiště pomocí názvu a klíče účtu úložiště:** Připojte se k úložišti Azure použitím názvu a klíče účtu úložiště.

Pro účely tohoto rychlého startu se přihlaste pomocí svého účtu Azure. Vyberte **Přidat účet Azure** a pak vyberte **Přihlásit**. Podle pokynů se přihlaste ke svému účtu Azure.

![Snímek obrazovky okna Průzkumníka služby Microsoft Azure Storage – Připojení](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Vytvoření sdílené složky
Vytvoření první sdílené složky Azure v účtu úložiště *storageacct<random number>*:

1. Rozbalte účet úložiště, který jste vytvořili.
2. Klikněte pravým tlačítkem na **Sdílené složky** a potom vyberte **Vytvořit sdílenou složku**.  
    ![Snímek obrazovky se složkou sdílených složek a místní nabídkou v kontextu](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Zadejte *myshare* jako název sdílené složky a potom stiskněte Enter.

> [!IMPORTANT]  
> Názvy sdílených složek mohou obsahovat pouze malá písmena, číslice a spojovníky (nesmí ale spojovníkem začínat). Kompletní informace o zadávání názvů sdílených složek a souborů najdete v tématu věnovaném [pojmenovávání sdílených složek, souborů a metadat a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Po vytvoření sdílené složky se v pravém podokně otevře karta. 

## <a name="work-with-the-contents-of-an-azure-file-share"></a>Práce s obsahem sdílené složky Azure
Teď, když jste vytvořili sdílenou složku Azure, můžete sdílenou složku připojit pomocí protokolu SMB v systému [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) nebo [macOS](storage-how-to-use-files-mac.md). Alternativně můžete se sdílenou složkou Azure pracovat pomocí Azure CLI. Výhodou použití Azure CLI místo připojování sdílené složky pomocí protokolu SMB je, že všechny požadavky vytvořené pomocí Azure CLI se vytváří použitím souborového rozhraní REST API. Souborové rozhraní REST API můžete použít k vytvoření, úpravě a odstranění souborů a adresářů na klientech bez přístupu k protokolu SMB.

### <a name="create-a-directory"></a>Vytvoření adresáře
Přidání adresáře zajistí hierarchickou strukturu pro správu sdílené složky. Ve svém adresáři můžete vytvořit více úrovní. Před vytvořením podadresářů se ale musíte přesvědčit, že existují nadřazené adresáře. Například v případě cesty myDirectory/mySubDirectory musíte nejprve vytvořit adresář *myDirectory*. a teprve potom můžete vytvořit adresář *mySubDirectory*. 

1. Na kartě sdílené složky v horní nabídce vyberte tlačítko **Nová složka**. Potom se otevře podokno **Vytvoření nového adresáře**.
    ![Snímek obrazovky s tlačítkem Nová složka v kontextu](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. Jako název adresáře zadejte *myDirectory* a potom vyberte **OK**. 

Adresář *myDirectory* bude uvedený v seznamu na kartě sdílené složky *myshare*.

### <a name="upload-a-file"></a>Nahrání souboru 
Z místního počítače můžete do nového adresáře ve sdílené složce nahrát soubor. Můžete nahrát jeden soubor nebo celou složku.

1. V horní nabídce vyberte **Nahrát**. Tímto způsobem můžete nahrát složku nebo soubor.
2. Vyberte **Nahrát soubor** a potom na místním počítači vyberte soubor k nahrání.
3. V části **Nahrát do adresáře** zadejte *myDirectory* a potom vyberte **Nahrát**. 

Po dokončení se tento soubor objeví na seznamu v podokně *myDirectory*.

### <a name="download-a-file"></a>Stažení souboru
Kopii souboru ze sdílené složky stáhnete pravým kliknutím na daný soubor a následným výběrem možnosti **Stáhnout**. Zvolte, kam chcete soubor na místním počítači uložit, a potom vyberte **Uložit**.

V podokně **Aktivity** v dolní části okna se zobrazí průběh stahování.

## <a name="create-and-modify-share-snapshots"></a>Vytváření a úpravy snímků sdílených složek
Snímek uchovává kopii sdílené složky Azure k určitému bodu v čase. Snímky sdílených složek jsou podobné jiným technologiím, které už možná znáte, jako například:
- [Služba Stínová kopie svazku (VSS)](https://docs.microsoft.com/en-us/windows/desktop/VSS/volume-shadow-copy-service-portal) v případě systémů souborů Windows, jako jsou NTFS a ReFS
- Snímky [Správce logických svazků (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) v případě systémů Linux.
- Snímky [systému souborů Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) v případě macOS

Vytvoření snímku sdílené složky:

1. Vyberte kartu sdílené složky *myshare*.
2. V horní nabídce vyberte **Vytvořit snímek**. (Možná budete muset nejdříve kliknout na **Další**, aby se vám tato možnost zobrazila – záleží na velikosti okna Průzkumníka služby Storage.)  
    ![Snímek obrazovky s tlačítkem Vytvořit snímek v kontextu](media/storage-how-to-use-files-storage-explorer/create-share-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Výpis a procházení snímků sdílených složek
Po vytvoření snímku si můžete zobrazit seznam snímků sdílené složky kliknutím na **Zobrazit snímky pro sdílenou složku**. (Možná budete muset nejdříve kliknout na **Další**, aby se vám tato možnost zobrazila – záleží na velikosti okna Průzkumníka služby Storage.) Dvojím kliknutím na snímek sdílené složky ho můžete procházet.

![Snímek obrazovky okna procházení snímků](media/storage-how-to-use-files-storage-explorer/list-browse-snapshots-1.png)

### <a name="restore-from-a-share-snapshot"></a>Obnovení ze snímku sdílené složky
Abychom si mohli předvést způsob obnovení souboru ze snímku sdílené složky, musíme nejprve odstranit soubor z aktivní sdílené složky Azure. Přejděte do složky *myDirectory*, klikněte pravým tlačítkem na nahraný soubor a potom vyberte **Odstranit**. Daný soubor ze snímku sdílené složky obnovíte následovně:

1. Vyberte **Zobrazit snímky pro sdílenou složku**. (Možná budete muset nejdříve kliknout na **Další**, aby se vám tato možnost zobrazila – záleží na velikosti okna Průzkumníka služby Storage.)
2. V seznamu snímků sdílené složky dvakrát klikněte na potřebný snímek sdílené složky.
3. Vyhledejte ve snímku odstraněný soubor. Vyberte sdílenou složku a potom vyberte **Obnovit snímek**. (Možná budete muset nejdříve kliknout na **Další**, aby se vám tato možnost zobrazila – záleží na velikosti okna Průzkumníka služby Storage.) Otevře se okno s upozorněním oznamujícím, že obnovení souboru přepíše obsah sdílené složky a že se jedná o nevratnou akci. Vyberte **OK**.
4. Soubor by nyní měl být ve svém původním umístění v aktivní sdílené složce Azure.

### <a name="delete-a-share-snapshot"></a>Odstranění snímku sdílené složky
Pokud chcete snímek sdílené složky odstranit, přejděte na [seznam snímků sdílené složky](#list-and-browse-share-snapshots). Klikněte pravým tlačítkem na snímek sdílené složky, který chcete odstranit, a vyberte **Odstranit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Průzkumníka služby Storage není možné použít k odebrání prostředků. Pokud chcete vyčistit prostředky z tohoto rychlého startu, můžete použít web [Azure Portal](https://portal.azure.com/). 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Další kroky
- [Správa sdílených složek pomocí webu Azure Portal](storage-how-to-use-files-portal.md)
- [Správa sdílených složek pomocí Azure PowerShellu](storage-how-to-use-files-powershell.md)
- [Správa sdílených složek pomocí Azure CLI](storage-how-to-use-files-cli.md)
- [Plánování nasazení služby Soubory Azure](storage-files-planning.md)