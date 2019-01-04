---
title: 'Kurz: Hostování statického webu ve službě Blob storage – Azure Storage'
description: Zjistěte, jak nakonfigurovat účet úložiště pro hostování statického webu a statického webu nasadit do služby Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: tamram
ms.custom: seodec18
ms.openlocfilehash: eb472465c0d35150f2a13563058905751219411d
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976456"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Kurz: Hostování statického webu ve službě Blob Storage

Tento kurz je první částí série. V ní se dozvíte, jak k vytvoření a nasazení statického webu do služby Azure Storage. Jakmile budete hotovi, budete mít statický web, který mohou uživatelé veřejně. 

V první části této série se naučíte:

> [!div class="checklist"]
> * Konfigurace hostování statického webu
> * Nasazení webu Hello World

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Tento kurz používá [Visual Studio Code](https://code.visualstudio.com/download), bezplatný nástroj pro programátory statického webu sestavit a nasadit ho do účtu služby Azure Storage.

Po dokončení instalace Visual Studio Code, nainstalujte rozšíření ve verzi preview služby Azure Storage. Toto rozšíření funkce správy úložiště Azure integruje Visual Studio Code. Rozšíření použijete k nasazení statického webu do služby Azure Storage. Chcete-li nainstalovat rozšíření:

1. Spusťte editor Visual Studio Code.
2. Na panelu nástrojů klikněte na tlačítko **rozšíření**. Vyhledejte *služby Azure Storage*a vyberte **služby Azure Storage** rozšíření ze seznamu. Klikněte **nainstalovat** tlačítko nainstalovat rozšíření.

    ![Instalace rozšíření služby Azure Storage ve VS Code](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k [webu Azure portal](https://portal.azure.com/) začít.

## <a name="configure-static-website-hosting"></a>Konfigurace hostování statického webu

Prvním krokem je konfigurace vašeho účtu úložiště hostovat statický web na webu Azure Portal. Při konfiguraci pro hostování statického webu vašeho účtu úložiště Azure automaticky vytvoří kontejner s názvem *$web*. *$Web* kontejner bude obsahovat soubory pro váš statický Web. 

1. Otevřít [webu Azure portal](https://portal.azure.com/) ve webovém prohlížeči. 
1. Vyhledejte svůj účet úložiště a zobrazí přehled účtu.
1. Vyberte **statického webu** zobrazíte na stránce konfigurace pro statických webů.
1. Vyberte **povoleno** aby hostoval statický web pro účet úložiště.
1. V **název dokumentu indexu** zadejte výchozí stránku index *index.html*. Když uživatel přejde do kořenového adresáře váš statický web, zobrazí se výchozí index stránky.  
1. V **cesta dokumentu chyby** zadejte výchozí chybovou stránku z *404. html*. Když se uživatel pokusí přejít na stránku, která neexistuje v váš statický web, se zobrazí výchozí chybová stránka.
1. Klikněte na **Uložit**. Na webu Azure portal se teď zobrazují váš koncový bod statického webu. 

    ![Povolení hostování statického webu pro účet úložiště](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Nasazení webu Hello World

V dalším kroku vytvořit webovou stránku Hello World pomocí Visual Studio Code a nasaďte ji do statického webu hostované ve vašem účtu úložiště Azure.

1. Vytvořte prázdnou složku s názvem *mywebsite* na vašem místním systému souborů. 
1. Spusťte Visual Studio Code a otevřete složku, která právě vytvořené **Explorer** panelu.

    ![Otevřít složku v aplikaci Visual Studio Code](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. Vytvořit výchozí soubor indexu v *mywebsite* složku a pojmenujte ho *index.html*.

    ![Vytvořit výchozí soubor indexu ve Visual Studio Code](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. Otevřít *index.html* v editoru vložte následující text do souboru a uložte ho:

    ```
    <h1>Hello World!</h1>
    ```

1. Vytvořit výchozí chybový soubor a pojmenujte ho *404. html*.
1. Otevřít *404. html* v editoru vložte následující text do souboru a uložte ho:

    ```
    <h1>404</h1>
    ```

1. Klikněte pravým tlačítkem myši v části *mywebsite* složky **Explorer** panelu a vyberte **nasadit do statického webu...**  pro nasazení webu. Zobrazí výzva k přihlášení do Azure a načíst seznam předplatných.

1. Vyberte předplatné obsahující účet úložiště, pro které jste povolili hostoval statický Web. Potom vyberte účet úložiště, po zobrazení výzvy.

Visual Studio Code se nyní odeslání souborů do vašeho koncového bodu webové a zobrazit stavový řádek úspěch. Spuštění webu můžete prohlížet v Azure.

![Zobrazení nasazení statického webu v Azure](media/storage-blob-static-website-host/view-static-website-endpoint.png)

Úspěšně jste dokončili kurz a statického webu nasadit do Azure.

## <a name="next-steps"></a>Další postup

V první části tohoto kurzu jste zjistili, jak nakonfigurovat účet úložiště Azure pro hostování statického webu a jak vytvořit a nasadit statického webu pro koncový bod Azure.

Teď přejděte k druhé, které nakonfigurujete pro vlastní doménu s protokolem SSL pro váš statický web s Azure CDN.

> [!div class="nextstepaction"]
> [Použití Azure CDN umožňuje vlastní doménu s protokolem SSL pro statický Web](storage-blob-static-website-custom-domain.md)
