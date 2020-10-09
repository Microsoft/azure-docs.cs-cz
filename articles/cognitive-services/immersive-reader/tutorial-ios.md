---
title: 'Kurz: spuštění moderního čtečky s využitím ukázky kódu SWIFT pro iOS'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu nakonfigurujete a spustíte ukázkovou aplikaci SWIFT, která spustí moderní čtečku.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.openlocfilehash: ce9d3af8f7517e2acae5264197b842d47085279c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88516363"
---
# <a name="tutorial-start-the-immersive-reader-using-the-swift-ios-code-sample"></a>Kurz: spuštění moderního čtečky s využitím ukázky kódu SWIFT pro iOS

V tomto [přehledu](./overview.md)jste se dozvěděli o tom, co je moderní čtečka a jak implementuje osvědčené techniky pro zlepšení porozumění čtení pro jazykové učení, vznikající čtenáři a studenty s rozdíly v učení. V tomto kurzu se dozvíte, jak vytvořit aplikaci pro iOS, která spustí moderní čtečku. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nakonfigurujte a spusťte aplikaci SWIFT pro iOS pomocí ukázkového projektu.
> * Získání přístupového tokenu
> * Spusťte moderní čtečku s ukázkovým obsahem.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

* Prostředek moderního čtecího zařízení nakonfigurovaný pro ověřování Azure Active Directory. Pomocí [těchto pokynů](./how-to-create-immersive-reader.md) si můžete nastavit. Když nakonfigurujete vlastnosti prostředí, budete potřebovat některé z hodnot, které jsou tady vytvořené. Uložte výstup vaší relace do textového souboru pro budoucí referenci.
* [MacOS](https://www.apple.com/macos).
* [Git](https://git-scm.com/).
* [Sada moderního čtecího zařízení](https://github.com/microsoft/immersive-reader-sdk).
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="configure-authentication-credentials"></a>Konfigurace ověřovacích přihlašovacích údajů

1. Otevřete Xcode a otevřete **Immersive-Reader-SDK/js/Samples/iOS/Quickstart-SWIFT/Quickstart-SWIFT. xcodeproj**.
1. V horní nabídce vyberte možnost **Product**  >  **Scheme**  >  **schéma úprav**schématu produktu.
1. V zobrazení **spuštění** vyberte kartu **argumenty** .
1. V části **proměnné prostředí** přidejte následující názvy a hodnoty. Zadejte hodnoty, které jste zadali při vytváření prostředku pro moderní čtečku.

    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

Tuto změnu nepotvrďte správou zdrojových kódů, protože obsahuje tajné klíče, které by se neměly zveřejnit.

## <a name="start-the-immersive-reader-with-sample-content"></a>Spuštění moderního čtecího zařízení s ukázkovým obsahem

V Xcode klikněte na **CTRL + R** a spusťte projekt.

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [sadu moderních čtenářů](https://github.com/microsoft/immersive-reader-sdk) a [referenční materiály k sadě pro moderní čtečku](./reference.md).
* Zobrazení ukázek kódu na [GitHubu](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
