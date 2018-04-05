---
title: Vytvoření funkce aktivované službou Blob Storage v Azure | Dokumentace Microsoftu
description: Pomocí služby Azure Functions vytvoříte funkci bez serveru, kterou volají položky přidané do úložiště služby Azure Blob Storage.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/27/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 928ea1dbb68206e128f0593ba15cb48935ab1ccf
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="create-a-function-triggered-by-azure-blob-storage"></a>Vytvoření funkce aktivované službou Azure Blob Storage

Dozvíte se, jak vytvořit funkci, která se aktivuje při odesílání souborů do služby Azure Blob Storage nebo při jejich aktualizaci v této službě.

![Zobrazte si zprávy v protokolech.](./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>Požadavky

+ Stáhnout a nainstalovat [Microsoft Azure Storage Explorer](http://storageexplorer.com/).
+ Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-function-app"></a>Vytvoření aplikace Azure Function App

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Aplikace Function App byla úspěšně vytvořena.](./media/functions-create-first-azure-function/function-app-create-success.png)

Dál vytvoříte v nové aplikaci Function App funkci.

<a name="create-function"></a>

## <a name="create-a-blob-storage-triggered-function"></a>Vytvoření funkce aktivované službou Blob Storage

1. Rozbalte aplikaci Function App a klikněte na tlačítko **+** vedle položky **Funkce**. Pokud jde o první funkci ve vaší aplikaci Function App, vyberte možnost **Vlastní funkce**. Zobrazí se kompletní sada šablon funkcí.

    ![Stručný úvod do služby Functions na webu Azure Portal](./media/functions-create-storage-blob-triggered-function/add-first-function.png)

2. Do vyhledávacího pole zadejte `blob` a zvolte jazyk požadovaný pro šablonu funkce aktivované úložištěm objektů blob.

    ![Vyberte šablonu funkce aktivované úložištěm objektů blob.](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal.png)
 
3. Použijte nastavení uvedená v tabulce pod obrázkem.

    ![Vytvoření funkce aktivované službou Blob Storage](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png)

    | Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|
    | **Název** | Jedinečný název v rámci aplikace Function App | Název této funkce aktivované objektem blob. |
    | **Cesta**   | samples-workitems/{name}    | Monitorované umístění ve službě Blob Storage. Název souboru objektu blob se předává v rámci vazby jako parametr _název_.  |
    | **Připojení k účtu úložiště** | AzureWebJobsStorage | Můžete použít připojení k účtu úložiště, které už používá vaše aplikace Function App, nebo můžete vytvořit nové.  |

3. Funkci vytvoříte kliknutím na **Vytvořit**.

Teď se připojíte ke svému účtu služby Azure Storage a vytvoříte kontejner **samples-workitems**.

## <a name="create-the-container"></a>Vytvoření kontejneru

1. Ve funkci klikněte na **Integrace**, rozbalte položku **Dokumentace**a zkopírujte údaje **Název účtu** a **Klíč účtu**. Tyto přihlašovací údaje použijte k připojení k účtu úložiště. Pokud jste se už ke svému účtu úložiště připojili, přejděte ke kroku 4.

    ![Získejte přihlašovací údaje účtu úložiště.](./media/functions-create-storage-blob-triggered-function/functions-storage-account-connection.png)

1. Spusťte nástroj [Microsoft Azure Storage Explorer](http://storageexplorer.com/), vlevo klikněte na ikonu připojení, zvolte **Use a storage account name and key** (Použít název a klíč účtu úložiště) a klikněte na **Next** (Další).

    ![Spusťte nástroj Průzkumník účtu úložiště.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-1.png)

1. Zadejte **Název účtu** a **Klíč účtu** z kroku 1, klikněte na **Další** a potom klikněte na **Připojit**. 

    ![Zadejte přihlašovací údaje úložiště a připojte se.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-2.png)

1. Rozbalte připojený účet úložiště, klikněte pravým tlačítkem na **Kontejnery objektů blob**, klikněte na **Vytvořit kontejner objektů blob**, zadejte `samples-workitems` a potom stiskněte Enter.

    ![Vytvořte frontu úložiště.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-create-blob-container.png)

Teď když máte kontejner objektů blob a můžete funkci otestovat tím, že do kontejneru odešlete soubor.

## <a name="test-the-function"></a>Testování funkce

1. Zpátky na webu Azure Portal přejděte na svoji funkci, ve spodní části stránky rozbalte **Protokoly** a ujistěte se, že není pozastavené streamování protokolů.

1. V Průzkumníku služby Storage rozbalte účet úložiště, položku **Kontejnery objektů blob** a potom položku **samples-workitems**. Klikněte na **Odeslat** a potom na **Nahrát soubory…**.

    ![Nahrání souboru do kontejneru objektů blob.](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png)

1. V dialogovém okně **Nahrát soubory** klikněte na pole **Soubory**. Přejděte na soubor v místním počítači, třeba na soubor obrázku, vyberte ho, klikněte na **Otevřít** a potom na **Odeslat**.

1. Vraťte se k protokolům funkce a zkontrolujte, jestli proběhlo čtení objektu blob.

   ![Zobrazte si zprávy v protokolech.](./media/functions-create-storage-blob-triggered-function/functions-blob-storage-trigger-view-logs.png)

    >[!NOTE]
    > Pokud vaše Function App běží ve výchozím plánu Consumption, může mezi přidáním nebo aktualizací objektu blob a aktivací funkce dojít až k několikaminutové prodlevě. Pokud u funkcí aktivovaných objekty blob potřebujete nízkou latenci, zvažte spuštění aplikace Function App v rámci plánu služby App Service.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Vytvořili jste funkci, která se spustí při přidání nebo aktualizaci objektu blob ve službě Blob Storage. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Další informace o aktivačních událostech služby Blob Storage najdete v tématu [Vazby služby Azure Functions Blob Storage](functions-bindings-storage-blob.md).
