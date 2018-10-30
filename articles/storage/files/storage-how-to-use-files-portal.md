---
title: Rychlý start pro správu sdílených složek Azure pomocí webu Azure Portal
description: V tomto rychlém startu zjistíte, jak pomocí webu Azure Portal spravovat službu Soubory Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: quickstart
ms.date: 10/18/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 12e6b3a3fe790183a35c71fbb87243890ad22236
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2018
ms.locfileid: "49944834"
---
# <a name="quickstart-create-and-manage-azure-file-shares-with-the-azure-portal"></a>Rychlý start: Vytváření a správa sdílených složek Azure pomocí webu Azure Portal 
Služba [Soubory Azure](storage-files-introduction.md) je snadno použitelný cloudový systém souborů od Microsoftu. Sdílené složky Azure je možné připojit v systémech Windows, Linux a macOS. Tato příručka vás provede základy práce se sdílenými složkami Azure pomocí webu [Azure Portal](https://portal.azure.com/).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-storage-account"></a>vytvořit účet úložiště
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-an-azure-file-share"></a>Vytvoření sdílené složky Azure
Vytvoření sdílené složky Azure:

1. Vyberte účet úložiště na řídicím panelu.
2. Na stránce účtu úložiště v části **Služby** vyberte **Soubory**.
    ![Snímek obrazovky účtu úložiště s částí Služby; výběr služby Soubory](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. V nabídce v horní části stránky **Souborová služba** klikněte na **+ Sdílená složka**. Rozbalí se stránka **Nová sdílená složka**.
4. Do pole **Název** zadejte *myshare*.
5. Kliknutím na **OK** vytvořte sdílenou složku Azure.

Názvy sdílených složek můžou obsahovat pouze malá písmena, číslice a spojovníky a nesmí začínat na spojovník. Kompletní informace o zadávání názvů sdílených složek a souborů najdete v tématu [Pojmenování a odkazování na sdílené složky, soubory a metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Použití sdílené složky Azure
Služba Soubory Azure nabízí dva způsoby práce se soubory a složkami ve sdílené složce Azure: standardní průmyslový [protokol SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) a [souborový protokol REST](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Informace o připojení sdílené složky s využitím protokolu SMB najdete v následujících dokumentech v závislosti na vašem operačním systému:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-from-the-azure-portal"></a>Použití sdílené složky Azure z webu Azure Portal
Všechny požadavky provedené přes Azure Portal se provedou pomocí souborového rozhraní REST API, které umožňuje vytvářet, upravovat a odstraňovat soubory a adresáře na klientech bez přístupu k protokolu SMB. Se souborovým protokolem REST můžete pracovat přímo (tedy zpracovávat volání REST HTTP ručně), ale nejběžnějším způsobem (mimo použití webu Azure Portal), jak používat souborový protokol REST, je použít [modul Azure RM PowerShellu](storage-how-to-use-files-powershell.md), [Azure CLI](storage-how-to-use-files-cli.md) nebo sadu SDK služby Azure Storage. Všechny tyto metody poskytují praktickou obálku souborového protokolu REST v libovolném skriptovacím nebo programovacím jazyce. 

Předpokládáme, že ve většině případů použití služby Soubory Azure budete chtít se sdílenými složkami Azure pracovat přes protokol SMB, protože vám to umožní používat stávající aplikace a nástroje, které očekáváte, že budete moct použít. Existuje však několik důvodů, proč může být výhodnější místo protokolu SMB použít souborové rozhraní REST API, jako například:

- Potřebujete provést rychlou změnu sdílené složky Azure, když jste na cestách, například z přenosného počítače bez přístupu k protokolu SMB, tabletu nebo mobilního zařízení.
- Potřebujete spustit skript nebo aplikaci z klienta, který nedokáže připojit sdílenou složku SMB, jako jsou například místní klienti s blokovaným portem 445.
- Využíváte bezserverové prostředky, jako je služba [Azure Functions](../../azure-functions/functions-overview.md). 

Následující příklady ukazují, jak pomocí webu Azure Portal manipulovat se sdílenou složkou Azure s využitím souborového protokolu REST. 

Teď, když jste vytvořili sdílenou složku Azure, můžete sdílenou složku připojit pomocí protokolu SMB v systému [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) nebo [macOS](storage-how-to-use-files-mac.md). Další možností je se sdílenou složkou Azure pracovat pomocí webu Azure Portal. 

#### <a name="create-a-directory"></a>Vytvoření adresáře
Vytvoření nového adresáře *myDirectory* v kořenovém adresáři sdílené složky Azure:

1. Na stránce **Souborová služba** vyberte sdílenou složku **myshare**. Otevře se stránka vaší sdílené složky.
2. V nabídce v horní části stránky vyberte **+ Přidat adresář**. Rozbalí se stránka **Nový adresář**.
3. Zadejte *myDirectory* a pak klikněte na **OK**.

#### <a name="upload-a-file"></a>Nahrání souboru 
Abychom si předvedli nahrání souboru, nejprve musíte vytvořit nebo vybrat soubor k nahrání. Můžete to provést jakýmkoli způsobem, který vám vyhovuje. Po výběru souboru, který chcete nahrát:

1. Klikněte na adresář **myDirectory**. Otevře se panel **myDirectory**.
2. V nabídce v horní části klikněte na **Nahrát**. Otevře se panel **Nahrání souborů**.  
    ![Snímek obrazovky s panelem Nahrání souborů](media/storage-how-to-use-files-portal/upload-file-1.png)

3. Kliknutím na ikonu složky otevřete okno, ve kterém můžete procházet místní soubory. 
4. Vyberte soubor a pak klikněte na **Otevřít**. 
5. Na stránce **Nahrání souborů** ověřte název souboru a pak klikněte na **Nahrát**.
6. Po dokončení by se soubor měl zobrazit v seznamu na stránce **myDirectory**.

#### <a name="download-a-file"></a>Stažení souboru
Kliknutím pravým tlačítkem na soubor, který jste nahráli, můžete stáhnout jeho kopii. Po kliknutí na tlačítko pro stažení bude konkrétní prostředí záviset na operačním systému a prohlížeči, které používáte.

## <a name="clean-up-resources"></a>Vyčištění prostředků
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Co je služba Soubory Azure?](storage-files-introduction.md)