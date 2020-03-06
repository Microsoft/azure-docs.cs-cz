---
title: 'Kurz: Hostování statického webu ve službě Blob storage – Azure Storage'
description: Zjistěte, jak nakonfigurovat účet úložiště pro hostování statického webu a statického webu nasadit do služby Azure Storage.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 03850315a05f569d2c6ba9405b6ec38bb6b1305d
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330391"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Kurz: Hostování statického webu ve službě Blob Storage

V tomto kurzu se dozvíte, jak vytvořit a nasadit statický web pro Azure Storage. Jakmile budete hotovi, budete mít statický web, který mohou uživatelé veřejně. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace hostování statického webu
> * Nasazení webu Hello World

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

> [!NOTE] 
> Ujistěte se, že jste vytvořili účet úložiště úrovně Standard pro obecné účely v2. Statické weby nejsou k dispozici v žádném jiném typu účtu úložiště.

V tomto kurzu se používá [Visual Studio Code](https://code.visualstudio.com/download)bezplatný nástroj pro programátory k sestavení statického webu a jeho nasazení na účet Azure Storage.

Po dokončení instalace Visual Studio Code, nainstalujte rozšíření ve verzi preview služby Azure Storage. Toto rozšíření funkce správy úložiště Azure integruje Visual Studio Code. Rozšíření použijete k nasazení statického webu do služby Azure Storage. Chcete-li nainstalovat rozšíření:

1. Spusťte editor Visual Studio Code.
2. Na panelu nástrojů klikněte na tlačítko **rozšíření**. Vyhledejte *Azure Storage*a v seznamu vyberte rozšíření **Azure Storage** . Pak klikněte na tlačítko **nainstalovat** a nainstalujte rozšíření.

    ![Instalace rozšíření služby Azure Storage ve VS Code](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Začněte tím, že se přihlásíte k [Azure Portal](https://portal.azure.com/) .

## <a name="configure-static-website-hosting"></a>Konfigurace hostování statického webu

Prvním krokem je konfigurace vašeho účtu úložiště hostovat statický web na webu Azure Portal. Když nakonfigurujete účet pro hostování statického webu, Azure Storage automaticky vytvoří kontejner s názvem *$Web*. Kontejner *$Web* bude obsahovat soubory pro váš statický Web. 

1. Otevřete [Azure Portal](https://portal.azure.com/) ve webovém prohlížeči. 
1. Vyhledejte svůj účet úložiště a zobrazí přehled účtu.
1. Vyberte možnost **statický web** pro zobrazení konfigurační stránky pro statické weby.
1. Vyberte **povoleno** , pokud chcete povolit statické hostování webů pro účet úložiště.
1. V poli **název dokumentu indexu** zadejte výchozí indexovou stránku *index. html*. Když uživatel přejde do kořenového adresáře váš statický web, zobrazí se výchozí index stránky.  
1. V poli **cesta k chybovému dokumentu** zadejte výchozí chybovou stránku *404. html*. Když se uživatel pokusí přejít na stránku, která neexistuje v váš statický web, se zobrazí výchozí chybová stránka.
1. Klikněte na **Uložit**. Na webu Azure portal se teď zobrazují váš koncový bod statického webu. 

    ![Povolení hostování statického webu pro účet úložiště](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Nasazení webu Hello World

V dalším kroku vytvořit webovou stránku Hello World pomocí Visual Studio Code a nasaďte ji do statického webu hostované ve vašem účtu úložiště Azure.

1. V místním systému souborů vytvořte prázdnou složku s názvem *mywebsite* . 
1. Spusťte Visual Studio Code a otevřete složku, kterou jste právě vytvořili, z panelu **Průzkumník** .

    ![Otevřít složku v aplikaci Visual Studio Code](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. Ve složce *mywebsite* Vytvořte výchozí indexový soubor a pojmenujte ho *index. html*.

    ![Vytvořit výchozí soubor indexu ve Visual Studio Code](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. V editoru otevřete soubor *index. html* , do souboru vložte následující text a uložte ho:

    ```
    <h1>Hello World!</h1>
    ```

1. Vytvořte výchozí chybový soubor a pojmenujte ho *404. html*.
1. V editoru otevřete soubor *404. html* a vložte do něj následující text a uložte ho:

    ```
    <h1>404</h1>
    ```

1. Klikněte pravým tlačítkem na složku *mywebsite* na panelu **Průzkumníka** a vyberte **nasadit na statický Web...** a nasaďte svůj web. Zobrazí výzva k přihlášení do Azure a načíst seznam předplatných.

1. Vyberte předplatné obsahující účet úložiště, pro které jste povolili hostoval statický Web. Potom vyberte účet úložiště, po zobrazení výzvy.

Visual Studio Code se nyní odeslání souborů do vašeho koncového bodu webové a zobrazit stavový řádek úspěch. Spuštění webu můžete prohlížet v Azure.

Úspěšně jste dokončili kurz a nasadili jste statický web do Azure.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak nakonfigurovat účet Azure Storage pro hostování statických webů a jak vytvořit a nasadit statický web do koncového bodu Azure.

V dalším kroku se dozvíte, jak nakonfigurovat vlastní doménu s vaším statickým webem.

> [!div class="nextstepaction"]
> [Mapování vlastní domény na koncový bod Azure Blob Storage](storage-custom-domain-name.md)
