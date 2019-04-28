---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 228f445dda2724985154723a292adb8215a5ad68
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60342334"
---
Otevřít **nastavení sestavení** tak, že vyberete **souboru** > **nastavení sestavení**.

V **platformy** vyberte **Android**. Změnit **sestavovací systém** k **Gradle** a vyberte **Export projektů**.

Vyberte **přepínač platformy** platformy s cílem změnit **Android**. Unity může výzvu k instalaci součásti pro Android, podporují, pokud v nich chybí.

![Okno nastavení sestavení Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Zavřít **nastavení sestavení** okna.

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Stáhnout a naimportovat sady SDK ARCore pro Unity

Stáhněte si `unitypackage` soubor [uvolní ARCore SDK Unity 1.7](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.7.0). Zpět v Unity projektu vyberte **prostředky** > **importovat balíček** > **vlastní balíček** a pak vyberte `unitypackage` souboru stáhli. V **Import balíčku Unity** dialogové okno, zkontrolujte, zda jsou vybrány všechny soubory a pak vyberte **Import**.
