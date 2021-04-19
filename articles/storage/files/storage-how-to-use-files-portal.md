---
title: Rychlý start pro správu sdílených složek Azure pomocí webu Azure Portal
description: Přečtěte si téma jak vytvořit a spravovat sdílené složky Azure v Azure Portal. Vytvořte účet úložiště, vytvořte sdílenou složku Azure a použijte sdílenou složku Azure.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d7867a4bd60bb179c18fff49691072d683660f7e
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717827"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-the-azure-portal"></a>Rychlý start: Vytváření a správa sdílených složek Azure pomocí webu Azure Portal 
Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Sdílené složky Azure je možné připojit v systémech Windows, Linux a macOS. Tato příručka vás provede základy práce se sdílenými složkami Azure pomocí webu [Azure Portal](https://portal.azure.com/).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
Vytvoření sdílené složky Azure:

1. Vyberte účet úložiště na řídicím panelu.
1. Na stránce účtu úložiště v části **Služby** vyberte **Soubory**.
    
    ![Snímek obrazovky s oddílem úložiště dat účtu úložiště; Vyberte sdílení souborů.](media/storage-how-to-use-files-portal/create-file-share-1.png)

1. V nabídce v horní části stránky **souborové služby** klikněte na **sdílení souborů**. Rozbalí se stránka **Nová sdílená složka**.
1. Do pole **název** zadejte *myshare* zadejte quoate a nechte vybraní **transakce optimalizované** pro **vrstvy**.
1. Vyberte **vytvořit** a vytvořte sdílenou složku Azure.

Názvy sdílených složek můžou obsahovat pouze malá písmena, číslice a spojovníky a nesmí začínat na spojovník. Kompletní informace o zadávání názvů sdílených složek a souborů najdete v tématu [Pojmenování a odkazování na sdílené složky, soubory a metadata](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Použití sdílené složky Azure
Azure Files nabízí tři metody práce se soubory a složkami v rámci sdílené složky Azure: průmyslový standardní [protokol SMB (Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview), protokol NFS (Network File System) (Preview) a [protokol REST (File REST Protocol](/rest/api/storageservices/file-service-rest-api)). 

Informace o připojení sdílené složky s využitím protokolu SMB najdete v následujících dokumentech v závislosti na vašem operačním systému:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-from-the-azure-portal"></a>Použití sdílené složky Azure z webu Azure Portal
Všechny požadavky provedené přes Azure Portal se provedou pomocí souborového rozhraní REST API, které umožňuje vytvářet, upravovat a odstraňovat soubory a adresáře na klientech bez přístupu k protokolu SMB. Je možné pracovat přímo s protokolem REST protokolu (tj. handcrafting REST HTTP), ale nejběžnějším způsobem (mimo použití Azure Portal) k použití protokolu REST File Protocol je použití [modulu Azure PowerShell](storage-how-to-use-files-powershell.md), rozhraní příkazového [řádku Azure](storage-how-to-use-files-cli.md)nebo sady Azure Storage SDK, které nabízí skvělé obálky kolem protokolu REST v skriptovacím nebo programovacím jazyce podle vašeho výběru. 

Očekáváme, že většina uživatelů souborů Azure bude chtít pracovat se sdílenými složkami Azure přes protokol SMB, protože jim umožní používat stávající aplikace a nástroje, které očekávají, aby je bylo možné používat, ale existuje několik důvodů, proč je vhodné použít soubor REST API místo protokolu SMB, například:

- Potřebujete provést rychlou změnu sdílené složky Azure, když jste na cestách, například z přenosného počítače bez přístupu k protokolu SMB, tabletu nebo mobilního zařízení.
- Potřebujete spustit skript nebo aplikaci z klienta, který nedokáže připojit sdílenou složku SMB, jako jsou například místní klienti s blokovaným portem 445.
- Využíváte bezserverové prostředky, jako je služba [Azure Functions](../../azure-functions/functions-overview.md). 

Následující příklady ukazují, jak pomocí webu Azure Portal manipulovat se sdílenou složkou Azure s využitím souborového protokolu REST. 

Teď, když jste vytvořili sdílenou složku Azure, můžete sdílenou složku připojit pomocí protokolu SMB v systému [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) nebo [macOS](storage-how-to-use-files-mac.md). Další možností je se sdílenou složkou Azure pracovat pomocí webu Azure Portal. 

#### <a name="create-a-directory"></a>Vytvoření adresáře
Vytvoření nového adresáře *myDirectory* v kořenovém adresáři sdílené složky Azure:

1. Na stránce **Souborová služba** vyberte sdílenou složku **myshare**. Otevře se stránka vaší sdílené složky.
1. V nabídce v horní části stránky vyberte **+ Přidat adresář**. Rozbalí se stránka **Nový adresář**.
1. Zadejte *myDirectory* a pak klikněte na **OK**.

#### <a name="upload-a-file"></a>Nahrání souboru 
Abychom si předvedli nahrání souboru, nejprve musíte vytvořit nebo vybrat soubor k nahrání. Můžete to provést jakýmkoli způsobem, který vám vyhovuje. Po výběru souboru, který chcete nahrát:

1. Klikněte na adresář **myDirectory**. Otevře se panel **myDirectory**.
1. V nabídce v horní části vyberte **Nahrát**. Otevře se panel **Nahrání souborů**.  
    
    ![Snímek obrazovky s panelem Nahrání souborů](media/storage-how-to-use-files-portal/upload-file-1.png)

1. Kliknutím na ikonu složky otevřete okno, ve kterém můžete procházet místní soubory. 
1. Vyberte soubor a pak klikněte na **Otevřít**. 
1. Na stránce **Nahrání souborů** ověřte název souboru a pak klikněte na **Nahrát**.
1. Po dokončení by se soubor měl zobrazit v seznamu na stránce **myDirectory**.

#### <a name="download-a-file"></a>Stažení souboru
Kliknutím pravým tlačítkem na soubor, který jste nahráli, můžete stáhnout jeho kopii. Po kliknutí na tlačítko pro stažení bude konkrétní prostředí záviset na operačním systému a prohlížeči, které používáte.

## <a name="clean-up-resources"></a>Vyčištění prostředků
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Co je Azure Files?](storage-files-introduction.md)