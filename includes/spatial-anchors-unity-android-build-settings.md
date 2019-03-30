---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 33c932c36cd6de730d3768d596a214c442d74ae1
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2019
ms.locfileid: "58632886"
---
Otevřít **nastavení sestavení** tak, že vyberete **souboru** > **nastavení sestavení**.

V **platformy** vyberte **Android**. Změnit **sestavovací systém** k **Gradle** a vyberte **Export projektů**.

Vyberte **přepínač platformy** platformy s cílem změnit **Android**. Unity může výzvu k instalaci součásti pro Android, podporují, pokud v nich chybí.

![Okno nastavení sestavení Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Zavřít **nastavení sestavení** okna.

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Stáhnout a naimportovat sady SDK ARCore pro Unity

Stáhněte si `unitypackage` soubor [uvolní ARCore SDK pro Unity 1.5](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). Zpět v Unity projektu vyberte **prostředky** > **importovat balíček** > **vlastní balíček** a pak vyberte `unitypackage` souboru stáhli. V **Import balíčku Unity** dialogové okno, zkontrolujte, zda jsou vybrány všechny soubory a pak vyberte **Import**.
