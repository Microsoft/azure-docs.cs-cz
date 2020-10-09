---
title: 'Kurz: spuštění moderního čtečky pomocí ukázek kódu pro Android'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu nakonfigurujete a spustíte ukázkovou aplikaci pro Android, která spustí moderní čtečku.
services: cognitive-services
author: dylankil
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: dylankil
ms.custom: devx-track-java
ms.openlocfilehash: 637b4d988a4845369a441dce55f0043d873879f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88516431"
---
# <a name="tutorial-start-the-immersive-reader-using-the-android-java-code-sample"></a>Kurz: spuštění moderního čtečky pomocí ukázky kódu pro Android Java

V tomto [přehledu](./overview.md)jste se dozvěděli o tom, co je moderní čtečka a jak implementuje osvědčené techniky pro zlepšení porozumění čtení pro jazykové učení, vznikající čtenáři a studenty s rozdíly v učení. V tomto kurzu se dozvíte, jak vytvořit aplikaci pro Android, která spustí moderní čtečku. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Nakonfigurujte a spusťte aplikaci pro Android pomocí ukázkového projektu.
> * Získání přístupového tokenu
> * Spusťte moderní čtečku s ukázkovým obsahem.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).

## <a name="prerequisites"></a>Požadavky

* Prostředek moderního čtecího zařízení nakonfigurovaný pro ověřování Azure Active Directory. Pomocí [těchto pokynů](./how-to-create-immersive-reader.md) si můžete nastavit. Když nakonfigurujete vlastnosti prostředí, budete potřebovat některé z hodnot, které jsou tady vytvořené. Uložte výstup vaší relace do textového souboru pro budoucí referenci.
* [Git](https://git-scm.com/).
* [Sada moderního čtecího zařízení](https://github.com/microsoft/immersive-reader-sdk).
* [Android Studio](https://developer.android.com/studio).

## <a name="configure-authentication-credentials"></a>Konfigurace ověřovacích přihlašovacích údajů

1. Spusťte Android Studio a otevřete projekt z aplikace **moderního čtenář-SDK/js/Samples/Starter-Java-Android** (Java) nebo s adresářem **moderního čtenář-SDK/js/Samples/Start-Kotlin** (Kotlin).

1. Ve složce **/assets** vytvořte soubor s názvem **ENV** . Přidejte následující názvy a hodnoty a podle potřeby zadejte hodnoty. Tento soubor Nepotvrzujte do správy zdrojových kódů, protože obsahuje tajné klíče, které by neměly být zveřejněny.
    
    ```text
    TENANT_ID=<YOUR_TENANT_ID>
    CLIENT_ID=<YOUR_CLIENT_ID>
    CLIENT_SECRET=<YOUR_CLIENT_SECRET>
    SUBDOMAIN=<YOUR_SUBDOMAIN>
    ```

## <a name="start-the-immersive-reader-with-sample-content"></a>Spuštění moderního čtecího zařízení s ukázkovým obsahem

Vyberte emulátor zařízení z AVD Manageru a spusťte projekt.

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [sadu moderních čtenářů](https://github.com/microsoft/immersive-reader-sdk) a [referenční materiály k sadě pro moderní čtečku](./reference.md).
* Zobrazení ukázek kódu na [GitHubu](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).