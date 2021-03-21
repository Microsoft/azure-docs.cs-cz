---
title: 'Kurz: hostování statického webu v úložišti objektů blob – Azure Storage'
description: Naučte se konfigurovat účet úložiště pro hostování statických webů a nasadit statický web pro Azure Storage.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 36cdaa813e0eccb23563301052aee268ab61533a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94888500"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Kurz: hostování statického webu na Blob Storage

V tomto kurzu se dozvíte, jak vytvořit a nasadit statický web pro Azure Storage. Až budete hotovi, budete mít statický web, ke kterému uživatelé budou mít přístup veřejně. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace hostování statického webu
> * Nasazení webu Hello World

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

> [!NOTE] 
> Ujistěte se, že jste vytvořili účet úložiště úrovně Standard pro obecné účely v2. Statické weby nejsou k dispozici v žádném jiném typu účtu úložiště.

V tomto kurzu se používá [Visual Studio Code](https://code.visualstudio.com/download)bezplatný nástroj pro programátory k sestavení statického webu a jeho nasazení na účet Azure Storage.

Po instalaci Visual Studio Code nainstalujte rozšíření Azure Storage Preview. Toto rozšíření integruje Azure Storage funkce správy s Visual Studio Code. Pomocí tohoto rozšíření nasadíte svůj statický web na Azure Storage. Instalace rozšíření:

1. Spusťte editor Visual Studio Code.
2. Na panelu nástrojů klikněte na tlačítko **rozšíření**. Vyhledejte *Azure Storage* a v seznamu vyberte rozšíření **Azure Storage** . Pak klikněte na tlačítko **nainstalovat** a nainstalujte rozšíření.

    ![Instalace rozšíření Azure Storage v VS Code](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Začněte tím, že se přihlásíte k [Azure Portal](https://portal.azure.com/) .

## <a name="configure-static-website-hosting"></a>Konfigurace hostování statického webu

Prvním krokem je konfigurace účtu úložiště pro hostování statického webu v Azure Portal. Když nakonfigurujete účet pro hostování statického webu, Azure Storage automaticky vytvoří kontejner s názvem *$Web*. Kontejner *$Web* bude obsahovat soubory pro váš statický Web. 

1. Otevřete [Azure Portal](https://portal.azure.com/) ve webovém prohlížeči. 
1. Vyhledejte svůj účet úložiště a zobrazte přehled účtu.
1. Vyberte možnost **statický web** pro zobrazení konfigurační stránky pro statické weby.
1. Vyberte **povoleno** , pokud chcete povolit statické hostování webů pro účet úložiště.
1. V poli **název dokumentu indexu** zadejte výchozí stránku indexu *index.html*. Výchozí stránka indexu se zobrazí, když uživatel přejde do kořenového adresáře vašeho statického webu.  
1. V poli **cesta k chybovému dokumentu** zadejte výchozí chybovou stránku *404.html*. Výchozí chybová stránka se zobrazí, když se uživatel pokusí přejít na stránku, která na vašem statickém webu neexistuje.
1. Klikněte na **Uložit**. Azure Portal teď zobrazuje váš koncový bod statického webu. 

    ![Povolení hostování statických webů pro účet úložiště](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Nasazení webu Hello World

Dále vytvořte webovou stránku Hello World s Visual Studio Code a nasaďte ji na statický web hostovaný ve vašem účtu Azure Storage.

1. V místním systému souborů vytvořte prázdnou složku s názvem *mywebsite* . 
1. Spusťte Visual Studio Code a otevřete složku, kterou jste právě vytvořili, z panelu **Průzkumník** .

    ![Otevřít složku v Visual Studio Code](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. Ve složce *mywebsite* Vytvořte výchozí indexový soubor a pojmenujte ho *index.html*.

    ![Vytvořte výchozí indexový soubor v Visual Studio Code](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. V editoru otevřete *index.html* , do souboru vložte následující text a uložte ho:

    ```
    <!DOCTYPE html>
    <html>
      <body>
        <h1>Hello World!</h1>
      </body>
    </html>
    ```

1. Vytvořte výchozí chybový soubor a pojmenujte ho *404.html*.
1. V editoru otevřete *404.html* , do souboru vložte následující text a uložte ho:

    ```
    <!DOCTYPE html>
    <html>
      <body>
        <h1>404</h1>
      </body>
    </html>
    ```

1. Klikněte pravým tlačítkem na složku *mywebsite* na panelu **Průzkumníka** a vyberte **nasadit na statický Web...** a nasaďte svůj web. Zobrazí se výzva, abyste se přihlásili k Azure a načetli seznam předplatných.

1. Vyberte předplatné obsahující účet úložiště, pro který jste povolili hostování statických webů. Potom po zobrazení výzvy vyberte účet úložiště.

Visual Studio Code nyní nahrajte soubory do vašeho webového koncového bodu a zobrazí se stavový řádek úspěch. Otevřete web a zobrazte ho v Azure.

Úspěšně jste dokončili kurz a nasadili jste statický web do Azure.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak nakonfigurovat účet Azure Storage pro hostování statických webů a jak vytvořit a nasadit statický web do koncového bodu Azure.

V dalším kroku se dozvíte, jak nakonfigurovat vlastní doménu s vaším statickým webem.

> [!div class="nextstepaction"]
> [Mapování vlastní domény na koncový bod Azure Blob Storage](storage-custom-domain-name.md)
