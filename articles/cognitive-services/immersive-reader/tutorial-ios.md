---
title: 'Kurz: spuštění moderního čtečky pomocí ukázky kódu SWIFT pro iOS'
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
ms.openlocfilehash: a9259026747102dd65be3bb8da853735098667db
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050164"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-swift-ios-code-sample"></a>Kurz: spuštění moderního čtečky pomocí ukázky kódu SWIFT pro iOS

V tomto [přehledu](./overview.md)jste se dozvěděli o tom, co je moderní čtečka a jak implementuje osvědčené techniky pro zlepšení porozumění čtení pro jazykové učení, vznikající čtenáři a studenty s rozdíly v učení. V tomto kurzu se dozvíte, jak vytvořit aplikaci pro iOS, která spustí moderní čtečku. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Konfigurace a spuštění aplikace SWIFT pro iOS pomocí ukázkového projektu
> * Získání přístupového tokenu
> * Spuštění moderního čtecího zařízení s ukázkovým obsahem

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), ještě než začnete.

## <a name="prerequisites"></a>Požadavky

* Prostředek moderního čtecího zařízení nakonfigurovaný pro ověřování Azure Active Directory. Pomocí [těchto pokynů](./how-to-create-immersive-reader.md) si můžete nastavit. Při konfiguraci vlastností prostředí budete potřebovat některé z hodnot, které jsou zde vytvořeny. Uložte výstup vaší relace do textového souboru pro budoucí referenci.
* [macOS](https://www.apple.com/macos)
* [Git](https://git-scm.com/)
* [SADA moderní čtečky](https://github.com/microsoft/immersive-reader-sdk)
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)

## <a name="configure-authentication-credentials"></a>Konfigurace ověřovacích přihlašovacích údajů

1. Otevřete Xcode a otevřete **Immersive-Reader-SDK/js/Samples/iOS/Quickstart-SWIFT/Quickstart-SWIFT. xcodeproj**.
2. V horní nabídce klikněte na **schéma > produktu > upravit schéma...**
3. V zobrazení **Spustit** klikněte na kartu **argumenty** .
4. V části **proměnné prostředí** přidejte následující názvy a hodnoty a zadejte hodnoty, které jste zadali při vytváření prostředku pro moderní čtečku.

```text
TENANT_ID=<YOUR_TENANT_ID>
CLIENT_ID=<YOUR_CLIENT_ID>
CLIENT_SECRET<YOUR_CLIENT_SECRET>
SUBDOMAIN=<YOUR_SUBDOMAIN>
```

Ujistěte se, že nechcete potvrdit výše uvedenou změnu do správy zdrojových kódů, protože obsahuje tajné kódy, které by neměly být zveřejněny.

## <a name="launch-the-immersive-reader-with-sample-content"></a>Spuštění moderního čtecího zařízení s ukázkovým obsahem

1. V Xcode spusťte projekt stisknutím **kombinace kláves CTRL + R** .

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [sadu moderních čtenářů](https://github.com/microsoft/immersive-reader-sdk) a [referenční materiály k sadě pro moderní čtečku](./reference.md)
* Zobrazit ukázky kódu na [GitHubu](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
