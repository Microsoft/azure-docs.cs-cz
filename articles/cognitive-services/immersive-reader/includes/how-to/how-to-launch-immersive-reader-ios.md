---
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 03/04/2021
ms.author: erhopf
ms.openlocfilehash: a3345ff9a6cc1d5f8e2fbc78c2a76ba6f183aeb6
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102620026"
---
## <a name="prerequisites"></a>Požadavky

* Předplatné Azure – [Vytvořte si ho zdarma](https://azure.microsoft.com/free/cognitive-services) .
* Prostředek moderního čtecího zařízení nakonfigurovaný pro ověřování Azure Active Directory. Pomocí [těchto pokynů](../../how-to-create-immersive-reader.md) si můžete nastavit.  Při konfiguraci vlastností prostředí budete potřebovat některé z hodnot, které jsou zde vytvořeny. Uložte výstup vaší relace do textového souboru pro budoucí referenci.
* [MacOS](https://www.apple.com/macos).
* [Git](https://git-scm.com/).
* [Sada moderního čtecího zařízení](https://github.com/microsoft/immersive-reader-sdk).
* [Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12).

## <a name="configure-authentication-credentials"></a>Konfigurace ověřovacích přihlašovacích údajů

1. Otevřete Xcode a otevřete **Immersive-Reader-SDK/js/Samples/iOS/Quickstart-SWIFT/Quickstart-SWIFT. xcodeproj**.
1. V horní nabídce vyberte možnost   >    >  **schéma úprav** schématu produktu.
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

* Prozkoumejte [sadu moderních čtenářů](https://github.com/microsoft/immersive-reader-sdk) a [referenční materiály k sadě pro moderní čtečku](../../reference.md).
* Zobrazení ukázek kódu na [GitHubu](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/).
