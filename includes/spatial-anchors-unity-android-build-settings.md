---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 9798e5f76881be38fb27e1f428565caba6e50bf2
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "67135092"
---
Otevřít **nastavení sestavení** tak, že vyberete **souboru** > **nastavení sestavení**.

V **platformy** vyberte **Android**. Změnit **sestavovací systém** k **Gradle** a zajistit **Export projektů** zaškrtávacího políčka nemá značku zaškrtnutí.

Vyberte **přepínač platformy** platformy s cílem změnit **Android**. Unity může výzvu k instalaci součásti pro Android, podporují, pokud v nich chybí.

![Okno nastavení sestavení Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Zavřít **nastavení sestavení** okna.

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Stáhnout a naimportovat sady SDK ARCore pro Unity

Stáhněte si `unitypackage` soubor [uvolní ARCore SDK Unity 1.7](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.7.0). Zpět v Unity projektu vyberte **prostředky** > **importovat balíček** > **vlastní balíček** a pak vyberte `unitypackage` souboru stáhli. V **Import balíčku Unity** dialogové okno, zkontrolujte, zda jsou vybrány všechny soubory a pak vyberte **Import**.
