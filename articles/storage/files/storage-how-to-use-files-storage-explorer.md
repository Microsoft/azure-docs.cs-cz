---
title: Správa sdílených složek Azure pomocí Průzkumníka služby Azure Storage
description: Zjistěte, jak pomocí Průzkumníka služby Azure Storage spravovat službu Soubory Azure.
services: storage
documentationcenter: ''
author: wmgries
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/27/2018
ms.author: wgries
ms.openlocfilehash: 0c16d3b0ef0b178f99eaafecd74eabb00cca5af9
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-with-azure-storage-explorer"></a>Správa sdílených složek Azure pomocí Průzkumníka služby Azure Storage 
Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Tato příručka vás provede základy práce se sdílenými složkami Azure pomocí [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/). Průzkumník služby Azure Storage je oblíbený klientský nástroj dostupný pro Windows, macOS a Linux určený ke správě sdílených složek Azure a dalších prostředků úložiště.

Tento postup vyžaduje instalovaného Průzkumníka služby Azure Storage. Pokud ho potřebujete nainstalovat, stáhněte si ho ze stránky [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

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
Průzkumník služby Azure Storage neposkytuje možnost vytvářet nové prostředky, proto pro účely této ukázky vytvořte účet úložiště pomocí webu [Azure Portal](https://portal.azure.com/). 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connecting-azure-storage-explorer-to-azure-resources"></a>Připojení Průzkumníka služby Azure Storage k prostředkům Azure
Při prvním spuštění se objeví okno **Průzkumník služby Microsoft Azure Storage – Připojení**. Průzkumník služby Azure Storage nabízí několik způsobů, jak se připojit k účtům úložiště: 

- **Přihlášení přes účet Azure:** Umožňuje přihlášení pomocí přihlašovacích údajů uživatele pro vaši organizaci nebo váš účet Microsoft. 
- **Připojení ke konkrétnímu účtu úložiště pomocí připojovacího řetězce nebo tokenu SAS:** Připojovací řetězec je speciální řetězec obsahující název účtu úložiště a klíč účtu úložiště nebo token SAS, který Průzkumníků služby Azure Storage umožňuje přímý přístup k účtu úložiště (místo toho, aby viděl všechny účty úložiště v rámci účtu Azure). Další informace o připojovacích řetězcích najdete v tématu [Konfigurace připojovacích řetězců úložiště Azure](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- **Připojení ke konkrétnímu účtu úložiště pomocí názvu a klíče účtu úložiště:** K připojení k úložišti Azure se používá název a klíč účtu úložiště.

Pro účely tohoto rychlého startu se přihlaste pomocí svého účtu Azure. Vyberte **Přidat účet Azure** a klikněte na **Přihlásit se**. Podle pokynů na obrazovce se přihlaste ke svému účtu Azure.

![Průzkumník služby Microsoft Azure Storage – okno Připojení](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Vytvoření sdílené složky
Vytvoření první sdílené složky Azure v rámci účtu úložiště *storageacct<random number>*:

1. Rozbalte účet úložiště, který jste vytvořili.
2. Klikněte pravým tlačítkem na **Sdílené složky** a vyberte **Vytvořit sdílenou složku**.  
    ![Snímek obrazovky se složkou sdílených složek a místní nabídkou v kontextu](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Jako název sdílené složky zadejte *myshare* a stiskněte **Enter**.

> [!Important]  
> Názvy sdílených složek můžou obsahovat pouze malá písmena, číslice a spojovníky a nesmí začínat na spojovník. Kompletní informace o zadávání názvů sdílených složek a souborů najdete v tématu [Pojmenování a odkazování na sdílené složky, soubory a metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Po vytvoření sdílené složky se v pravém podokně otevře karta vaší sdílené složky. 

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Práce s obsahem sdílené složky Azure
Teď, když jste vytvořili sdílenou složku Azure, můžete sdílenou složku připojit pomocí protokolu SMB v systému [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) nebo [macOS](storage-how-to-use-files-mac.md). Alternativně můžete se sdílenou složkou Azure pracovat pomocí webu Azure Portal. Všechny požadavky provedené přes Azure Portal se provedou pomocí souborového rozhraní REST API, které umožňuje vytvářet, upravovat a odstraňovat soubory a adresáře na klientech bez přístupu k protokolu SMB.

### <a name="create-a-directory"></a>Vytvoření adresáře
Přidání adresáře zajistí hierarchickou strukturu pro správu sdílené složky. Můžete vytvořit několik úrovní, musíte však zajistit, aby před vytvořením podadresáře existovaly všechny nadřazené adresáře. Například v případě cesty myDirectory/mySubDirectory musíte nejprve vytvořit adresář *myDirectory* a až pak můžete vytvořit adresář *mySubDirectory*. 

1. Na kartě sdílené složky v horní nabídce klikněte na tlačítko **+ Nová složka**. Otevře se stránka **Vytvoření nového adresáře**.
    ![Snímek obrazovky s tlačítkem Nová složka v kontextu](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. Jako název zadejte *myDirectory* a pak klikněte na **OK**. 

Adresář *myDirectory* se zobrazí v seznamu na kartě sdílené složky *myshare*.

### <a name="upload-a-file"></a>Nahrání souboru 
Nahrajte soubor z místního počítače do nového adresáře ve sdílené složce. Můžete nahrát celou složku nebo jenom jeden soubor.

1. V nabídce v horní části vyberte **Nahrát**. Tímto způsobem můžete nahrát složku nebo soubor.

2. Vyberte **Nahrát soubor** a z místního počítače zvolte soubor k nahrání.

3. V části **Nahrát do adresáře** zadejte *myDirectory* a pak klikněte na **Nahrát**. 

Po dokončení by se soubor měl zobrazit v seznamu na stránce **myDirectory**.

### <a name="download-a-file"></a>Stažení souboru
Kopii souboru ve sdílené složce můžete stáhnout tak, že na daný soubor kliknete pravým tlačítkem a vyberete **Stáhnout**. Zvolte, kam chcete soubor umístit na místním počítači, a pak klikněte na **Uložit**.

V podokně **Aktivity** v dolní části okna se zobrazí průběh stahování.

## <a name="create-and-modify-share-snapshots"></a>Vytváření a úpravy snímků sdílených složek
Snímek uchovává kopii sdílené složky Azure k určitému bodu v čase. Snímky sdílených složek jsou podobné dalším technologiím, které už možná znáte, jako například:
- [Služba Stínová kopie svazku (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) v případě systémů souborů Windows, jako jsou NTFS a ReFS.
- Snímky [Správce logických svazků (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) v případě systémů Linux.
- Snímky [systému souborů Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) v případě macOS. 

Vytvoření snímku sdílené složky:

1. Otevřete kartu sdílené složky *myshare*.
2. V nabídce v horní části karty klikněte na **Vytvořit snímek** (tato možnost může být skrytá pod možností **... Další** v závislosti na rozměrech okna Průzkumníka služby Azure Storage).  
    ![Snímek obrazovky s tlačítkem Vytvořit snímek v kontextu](media/storage-how-to-use-files-storage-explorer/create-share-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Výpis a procházení snímků sdílených složek
Po vytvoření snímku můžete kliknout na **Zobrazit snímky pro sdílenou složku** (tato možnost může být skrytá pod možností **... Další** v závislosti na rozměrech okna Průzkumníka služby Azure Storage) a zobrazit výpis snímků pro danou sdílenou složku. Dvojím kliknutím na snímek sdílené složky ho můžete procházet.

![Snímek obrazovky procházení snímků](media/storage-how-to-use-files-storage-explorer/list-browse-snapshots-1.png)

### <a name="restore-from-a-share-snapshot"></a>Obnovení ze snímku sdílené složky
Abychom si předvedli obnovení souboru ze snímku sdílené složky, nejprve musíme odstranit soubor z aktivní sdílené složky Azure. Přejděte do složky *myDirectory*, klikněte pravým tlačítkem na soubor, který jste nahráli, a pak klikněte na **Odstranit**. Následné obnovení daného souboru ze snímku sdílené složky:

1. Klikněte na **Zobrazit snímky pro sdílenou složku** (tato možnost může být skrytá pod možností **... Další** v závislosti na rozměrech okna Průzkumníka služby Azure Storage).
2. Vyberte ze seznamu snímek sdílené složky a dvojím kliknutím do něj přejděte.
3. Procházejte snímkem, dokud nenajdete soubor, který jste odstranili, vyberte ho a pak klikněte na **Obnovit snímek** (tato možnost může být skrytá pod možností **... Další** v závislosti na rozměrech okna Průzkumníka služby Azure Storage). Zobrazí se upozornění oznamující, že obnovení souboru přepíše obsah sdílené složky a že se jedná o nevratnou akci. Vyberte **OK**.
4. Soubor by teď měl být ve svém původním umístění v aktivní sdílené složce Azure.

### <a name="delete-a-share-snapshot"></a>Odstranění snímku sdílené složky
Pokud chcete odstranit snímek sdílené složky, [přejděte k seznamu snímků sdílené složky](#list-and-browse-share-snapshots). Klikněte pravým tlačítkem na snímek sdílené složky, který chcete odstranit, a vyberte Odstranit.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Průzkumník služby Azure Storage neposkytuje možnost odebrat prostředky. Vyčištění prostředků po tomto rychlém startu můžete provést pomocí webu [Azure Portal](https://portal.azure.com/). 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Další kroky
- [Správa sdílených složek pomocí webu Azure Portal](storage-how-to-use-files-portal.md)
- [Správa sdílených složek pomocí Azure PowerShellu](storage-how-to-use-files-powershell.md)
- [Správa sdílených složek pomocí Azure CLI](storage-how-to-use-files-cli.md)
- [Plánování nasazení služby Soubory Azure](storage-files-planning.md)