---
title: 'Kurz: Hostování statického webu v úložišti objektů Blob – Azure Storage'
description: Zjistěte, jak nakonfigurovat účet úložiště pro statický web hosting a nasadit statický web do Azure Storage.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: tutorial
ms.date: 1/22/2020
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 03850315a05f569d2c6ba9405b6ec38bb6b1305d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78330391"
---
<!---Customer intent: I want to host files for a static website in Blob storage and access the website from an Azure endpoint.--->

# <a name="tutorial-host-a-static-website-on-blob-storage"></a>Kurz: Hostování statického webu v úložišti objektů Blob

V tomto kurzu se dozvíte, jak vytvořit a nasadit statický web do Služby Azure Storage. Po dokončení budete mít statický web, ke kterému budou mít uživatelé přístup veřejně. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace statického hostingu webových stránek
> * Nasazení webu Hello World

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

> [!NOTE] 
> Ujistěte se, že vytvořit účet úložiště pro obecné účely v2 Standard . Statické weby nejsou k dispozici v žádném jiném typu účtu úložiště.

Tento kurz používá [Visual Studio Code](https://code.visualstudio.com/download), bezplatný nástroj pro programátory, k vytvoření statického webu a jeho nasazení do účtu Azure Storage.

Po instalaci kódu Visual Studia nainstalujte rozšíření Azure Storage preview. Toto rozšíření integruje funkce správy úložiště Azure s Visual Studio Code. Rozšíření použijete k nasazení statického webu do Azure Storage. Instalace rozšíření:

1. Spusťte editor Visual Studio Code.
2. Na panelu nástrojů klepněte na **položku Rozšíření**. Vyhledejte *Azure Storage*a vyberte rozšíření Úložiště **Azure** ze seznamu. Potom kliknutím na tlačítko **Instalovat** nainstalujte rozšíření.

    ![Instalace rozšíření úložiště Azure v kódu VS](media/storage-blob-static-website-host/install-extension-vs-code.png)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Chcete-li začít, přihlaste se na [portál Azure.](https://portal.azure.com/)

## <a name="configure-static-website-hosting"></a>Konfigurace statického hostingu webových stránek

Prvním krokem je konfigurace účtu úložiště pro hostování statického webu na webu Azure Portal. Když nakonfigurujete svůj účet pro statický hosting webů, Azure Storage automaticky vytvoří kontejner s názvem *$web*. Kontejner *$web* bude obsahovat soubory pro statický web. 

1. Otevřete [portál Azure](https://portal.azure.com/) ve webovém prohlížeči. 
1. Vyhledejte svůj účet úložiště a zobrazte přehled účtu.
1. Vyberte **Statický web,** chcete-li zobrazit konfigurační stránku pro statické weby.
1. Výběrem **možnosti Povoleno** povolíte statický hosting webu pro účet úložiště.
1. V poli **Název dokumentu Rejstříku** zadejte výchozí stránku indexu *index.html*. Výchozí stránka indexu se zobrazí, když uživatel přejde do kořenového adresáře statického webu.  
1. V poli **Cesta dokumentu Chyba** zadejte výchozí chybovou stránku *404.html*. Výchozí chybová stránka se zobrazí, když se uživatel pokusí přejít na stránku, která na statickém webu neexistuje.
1. Klikněte na **Uložit**. Portál Azure teď zobrazuje koncový bod statického webu. 

    ![Povolení statického hostování webových stránek pro účet úložiště](media/storage-blob-static-website-host/enable-static-website-hosting.png)

## <a name="deploy-a-hello-world-website"></a>Nasazení webu Hello World

Dále vytvořte webovou stránku Hello World s visual studio kód a nasadit ji na statické webové stránky hostované ve vašem účtu Azure Storage.

1. Vytvořte prázdnou složku s názvem *mywebsite* v místním systému souborů. 
1. Spusťte kód Visual Studia a otevřete složku, kterou jste právě vytvořili z panelu **Průzkumníka.**

    ![Otevřít složku v kódu Visual Studia](media/storage-blob-static-website-host/open-folder-vs-code.png)

1. Vytvořte výchozí indexový soubor ve složce *mywebsite* a pojmenujte jej *index.html*.

    ![Vytvoření výchozího souboru indexu v kódu sady Visual Studio](media/storage-blob-static-website-host/create-index-file-vs-code.png)

1. Otevřete *soubor index.html* v editoru, vložte do souboru následující text a uložte jej:

    ```
    <h1>Hello World!</h1>
    ```

1. Vytvořte výchozí soubor chyb a pojmenujte jej *404.html*.
1. Otevřete v editoru *soubor 404.html,* vložte do souboru následující text a uložte jej:

    ```
    <h1>404</h1>
    ```

1. Klikněte pravým tlačítkem myši pod složku *myweb* v panelu **Průzkumník** a vyberte **nasadit na statický web...** a nasadit svůj web. Budete vyzváni k přihlášení do Azure k načtení seznamu předplatných.

1. Vyberte předplatné obsahující účet úložiště, pro který jste povolili statický web hosting. Dále vyberte účet úložiště po zobrazení výzvy.

Visual Studio Code teď nahraje vaše soubory do koncového bodu webu a zobrazí stavový řádek úspěchu. Spusťte web a zobrazte ho v Azure.

Úspěšně jste dokončili kurz a nasadili statický web do Azure.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak nakonfigurovat účet úložiště Azure pro statické hostování webů a jak vytvořit a nasadit statický web do koncového bodu Azure.

Dále se dozvíte, jak nakonfigurovat vlastní doménu pomocí statického webu.

> [!div class="nextstepaction"]
> [Mapování vlastní domény na koncový bod úložiště objektů blob Azure](storage-custom-domain-name.md)
