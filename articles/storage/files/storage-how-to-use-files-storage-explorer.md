---
title: Rychlý start pro správu sdílených složek Azure pomocí Průzkumníka služby Azure Storage
description: V tomto rychlém startu zjistíte, jak pomocí Průzkumníka služby Azure Storage spravovat službu Soubory Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: f86a86fd9858fcc6f0b78256da1e96effbcbe68c
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49944214"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-azure-storage-explorer"></a>Rychlý start: Vytváření a správa sdílených složek Azure pomocí Průzkumníka služby Azure Storage
Tato příručka vás provede základy práce se [sdílenými složkami Azure](storage-files-introduction.md) pomocí Průzkumníka služby Azure Storage. Sdílené složky Azure jsou stejné jako ostatní sdílené složky, ale jsou uložené v cloudu a využívají platformu Azure. Sdílené složky Azure podporují standardní průmyslový protokol SMB a umožňují sdílení souborů mezi různými počítači, aplikacemi a instancemi. 

Průzkumník služby Azure Storage je oblíbený klientský nástroj, který je k dispozici pro Windows, macOS a Linux. Můžete ho použít ke správě sdílených složek Azure a dalších prostředků úložiště.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Tento postup vyžaduje nainstalovaného Průzkumníka služby Storage. Stáhnout ho a následně ho nainstalovat můžete ze stránky [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

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
Vytvoření první sdílené složky Azure v účtu úložiště `storageacct<random number>`:

1. Rozbalte účet úložiště, který jste vytvořili.
2. Klikněte pravým tlačítkem na **Sdílené složky** a potom vyberte **Vytvořit sdílenou složku**.  
    ![Snímek obrazovky se složkou sdílených složek a místní nabídkou v kontextu](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Zadejte *myshare* jako název sdílené složky a potom stiskněte Enter.

Názvy sdílených složek mohou obsahovat pouze malá písmena, číslice a spojovníky (nesmí ale spojovníkem začínat). Kompletní informace o zadávání názvů sdílených složek a souborů najdete v tématu věnovaném [pojmenovávání sdílených složek, souborů a metadat a odkazování na ně](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Po vytvoření sdílené složky se v pravém podokně otevře karta. 

## <a name="use-your-azure-file-share"></a>Použití sdílené složky Azure
Teď, když jste vytvořili sdílenou složku Azure, můžete sdílenou složku připojit pomocí protokolu SMB v systému [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) nebo [macOS](storage-how-to-use-files-mac.md). Alternativně můžete se sdílenou složkou Azure pracovat pomocí Průzkumníka služby Azure Storage. Výhodou použití Průzkumníka služby Azure Storage místo připojování sdílené složky pomocí protokolu SMB je, že všechny požadavky vytvořené pomocí Průzkumníka služby Azure Storage se vytváří použitím souborového rozhraní REST API. Souborové rozhraní REST API můžete použít k vytvoření, úpravě a odstranění souborů a adresářů na klientech bez přístupu k protokolu SMB.

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

## <a name="clean-up-resources"></a>Vyčištění prostředků
Průzkumníka služby Storage není možné použít k odebrání prostředků. Pokud chcete vyčistit prostředky z tohoto rychlého startu, můžete použít web [Azure Portal](https://portal.azure.com/). 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Co je služba Soubory Azure?](storage-files-introduction.md)