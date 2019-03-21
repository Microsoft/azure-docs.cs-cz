---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 53f480b8858e2bbe7d4699d8637ecaa5ab0c08a3
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58305128"
---
V Unity, otevřete projekt v `Unity` složky.

Otevřít **nastavení sestavení** tak, že vyberete **souboru** > **nastavení sestavení**.

V **platformy** vyberte **Android**. Změnit **sestavovací systém** k **Gradle** a vyberte **Export projektů**.

Vyberte **přepínač platformy** platformy s cílem změnit **Android**. Unity může výzvu k instalaci součásti pro Android, podporují, pokud v nich chybí.

![Okno nastavení sestavení Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Zavřít **nastavení sestavení** okna.

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Stáhnout a naimportovat sady SDK ARCore pro Unity

Stáhněte si `unitypackage` soubor [uvolní ARCore SDK pro Unity](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). Zpět v Unity projektu vyberte **prostředky** > **importovat balíček** > **vlastní balíček** a pak vyberte `unitypackage` souboru stáhli. V **Import balíčku Unity** dialogové okno, zkontrolujte, zda jsou vybrány všechny soubory a pak vyberte **Import**.
