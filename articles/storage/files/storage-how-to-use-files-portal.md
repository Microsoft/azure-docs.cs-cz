---
title: Správa sdílených složek Azure pomocí webu Azure Portal
description: Zjistěte, jak pomocí webu Azure Portal spravovat službu Soubory Azure.
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
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 9b8a7988be2c3a2622dd45a264abcb674b7d169e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="managing-azure-file-shares-with-the-azure-portal"></a>Správa sdílených složek Azure pomocí webu Azure Portal 
Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Sdílené složky Azure je možné připojit v systémech Windows, Linux a macOS. Tato příručka vás provede základy práce se sdílenými složkami Azure pomocí webu [Azure Portal](https://portal.azure.com/). Naučte se:

> [!div class="checklist"]
> * Vytvoření skupiny prostředků a účtu úložiště
> * Vytvoření sdílené složky Azure 
> * Vytvoření adresáře
> * Nahrání souboru 
> * Stažení souboru
> * Vytvoření a použití snímku sdílené složky

Pokud ještě nemáte předplatné Azure, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-a-file-share"></a>Vytvoření sdílené složky
Vytvoření sdílené složky:

1. Vyberte účet úložiště na řídicím panelu.
2. Na stránce účtu úložiště v části **Služby** vyberte **Soubory**.
    ![Snímek obrazovky účtu úložiště s částí Služby; výběr služby Soubory](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. V nabídce v horní části stránky **Souborová služba** klikněte na **+ Sdílená složka**. Rozbalí se stránka **Nová sdílená složka**.
4. Do pole **Název** zadejte *myshare*.
5. Kliknutím na **OK** vytvořte sdílenou složku Azure.

## <a name="work-with-the-contents-of-the-azure-file-share"></a>Práce s obsahem sdílené složky Azure
Teď, když jste vytvořili sdílenou složku Azure, můžete sdílenou složku připojit pomocí protokolu SMB v systému [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) nebo [macOS](storage-how-to-use-files-mac.md). Další možností je se sdílenou složkou Azure pracovat pomocí webu Azure Portal. Všechny požadavky provedené přes Azure Portal se provedou pomocí souborového rozhraní REST API, které umožňuje vytvářet, upravovat a odstraňovat soubory a adresáře na klientech bez přístupu k protokolu SMB.

### <a name="create-a-directory"></a>Vytvoření adresáře
Vytvoření nového adresáře *myDirectory* v kořenovém adresáři sdílené složky Azure:

1. Na stránce **Souborová služba** vyberte sdílenou složku **myshare**. Otevře se stránka vaší sdílené složky.
2. V nabídce v horní části stránky vyberte **+ Přidat adresář**. Rozbalí se stránka **Nový adresář**.
3. Zadejte *myDirectory* a pak klikněte na **OK**.

### <a name="upload-a-file"></a>Nahrání souboru 
Abychom si předvedli nahrání souboru, nejprve musíte vytvořit nebo vybrat soubor k nahrání. Můžete to provést jakýmkoli způsobem, který vám vyhovuje. Po výběru souboru, který chcete nahrát:

1. Klikněte na adresář **myDirectory**. Otevře se panel **myDirectory**.
2. V nabídce v horní části klikněte na **Nahrát**. Otevře se panel **Nahrání souborů**.  
    ![Snímek obrazovky s panelem Nahrání souborů](media/storage-how-to-use-files-portal/upload-file-1.png)

3. Kliknutím na ikonu složky otevřete okno, ve kterém můžete procházet místní soubory. 
4. Vyberte soubor a pak klikněte na **Otevřít**. 
5. Na stránce **Nahrání souborů** ověřte název souboru a pak klikněte na **Nahrát**.
6. Po dokončení by se soubor měl zobrazit v seznamu na stránce **myDirectory**.

### <a name="download-a-file"></a>Stažení souboru
Kliknutím pravým tlačítkem na soubor, který jste nahráli, můžete stáhnout jeho kopii. Po kliknutí na tlačítko pro stažení bude konkrétní prostředí záviset na operačním systému a prohlížeči, které používáte.

## <a name="create-and-modify-share-snapshots"></a>Vytváření a úpravy snímků sdílených složek
Další užitečnou úlohou, kterou se sdílenými složkami Azure můžete provádět, je vytváření snímků sdílených složek. Snímek uchovává sdílenou složku Azure k určitému bodu v čase. Snímky sdílených složek jsou podobné technologiím operačního systému, které už možná znáte, jako například:
- [Služba Stínová kopie svazku (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) v případě systémů souborů Windows, jako jsou NTFS a ReFS.
- Snímky [Správce logických svazků (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) v případě systémů Linux.
- Snímky [systému souborů Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) v případě macOS. 

Vytvoření snímku sdílené složky:

1. Otevřete stránku sdílené složky otevřením účtu úložiště v části řídicí panel > **Soubory** > **myshare**. 
2. V nabídce v horní části stránky sdílené složky klikněte na tlačítko **Snímek** a pak vyberte **Vytvořit snímek**.  
    ![Snímek obrazovky ukazující umístění tlačítka pro vytvoření snímku](media/storage-how-to-use-files-portal/create-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Výpis a procházení snímků sdílených složek
Po vytvoření snímku můžete znovu kliknout na **Snímek** a pak výběrem možnosti **Zobrazit snímky** vypsat snímky dané sdílené složky. Ve výsledném podokně se zobrazí snímky dané sdílené složky. Kliknutím na snímek sdílené složky ho můžete procházet.

### <a name="restore-from-a-share-snapshot"></a>Obnovení ze snímku sdílené složky
Abychom si předvedli obnovení souboru ze snímku sdílené složky, nejprve musíme odstranit soubor z aktivní sdílené složky Azure. Přejděte do složky *myDirectory*, klikněte pravým tlačítkem na soubor, který jste nahráli, a pak klikněte na **Odstranit**. Následné obnovení daného souboru ze snímku sdílené složky:

1. Klikněte na **Snímky** v horní nabídce a vyberte **Zobrazit snímky**. 
2. Klikněte na snímek, který jste vytvořili dříve. Otevře se nová stránka s jeho obsahem. 
3. Ve snímku klikněte na složku **myDirectory** a měl by se zobrazit soubor, který jste odstranili. 
4. Klikněte na odstraněný soubor pravým tlačítkem a vyberte **Obnovit**.
5. Zobrazí se automaticky otevírané okno s možností výběru mezi obnovením souboru jako kopie a přepsáním původního souboru. Vzhledem k tomu, že jsme odstranili původní soubor, můžeme vybrat možnost **Přepsat původní soubor** a obnovit soubor do stavu, v jakém byl před odstraněním. Kliknutím na **OK** obnovte soubor do sdílené složky Azure.  
    ![Snímek obrazovky s dialogovým oknem pro obnovení souboru](media/storage-how-to-use-files-portal/restore-snapshot-1.png)

6. Po obnovení souboru zavřete stránku snímku a vraťte se do části **myshare** > **myDirectory**. Soubor by měl být zpět v původním umístění.

### <a name="delete-a-share-snapshot"></a>Odstranění snímku sdílené složky
Pokud chcete odstranit snímek sdílené složky, [přejděte k seznamu snímků sdílené složky](#list-and-browse-a-share-snapshot). Klikněte na zaškrtávací políčko vedle názvu snímku sdílené složky a vyberte tlačítko **Odstranit**.

![Snímek obrazovky s odstraněním snímku sdílené složky](media/storage-how-to-use-files-portal/delete-snapshot-1.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Další kroky
- [Správa sdílených složek pomocí Azure PowerShellu](storage-how-to-use-files-powershell.md)
- [Správa sdílených složek pomocí Azure CLI](storage-how-to-use-files-cli.md)
- [Správa sdílených složek pomocí Průzkumníka služby Azure Storage](storage-how-to-use-files-storage-explorer.md)
- [Plánování nasazení služby Soubory Azure](storage-files-planning.md)