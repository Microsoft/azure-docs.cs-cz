---
author: ramonarguelles
ms.service: azure-spatial-anchors
ms.topic: include
ms.date: 1/29/2019
ms.author: rgarcia
ms.openlocfilehash: 662aced6df27febdf29f2645725962763e89cfa2
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2019
ms.locfileid: "56752182"
---
Otevřete Unity a otevřete projekt v `Unity` složky.

Otevřít **nastavení sestavení** tak, že vyberete **souboru** -> **nastavení sestavení**.

V **platformy** vyberte **Android**. Změňte **sestavovací systém** k **Gradle** a zkontrolujte **Export projektů** možnost.

Vyberte **přepínač platformy** platformy s cílem změnit **Android**. Unity vás může požádat o podporu Androidu součásti nainstalovat, pokud se v nich chybí.

![Nastavení buildu Unity](./media/spatial-anchors-unity/unity-android-build-settings.png)

Zavřít **nastavení sestavení** okna.

### <a name="download-and-import-the-arcore-sdk-for-unity"></a>Stáhnout a naimportovat sady SDK ARCore pro Unity

Stáhněte si `unitypackage` soubor [uvolní ARCore SDK pro Unity](https://github.com/google-ar/arcore-unity-sdk/releases/tag/v1.5.0). Zpět v Unity projektu vyberte **prostředky** -> **importovat balíček** -> **vlastní balíček...**  a pak vyberte `unitypackage` jste dříve stáhli. V **Import balíčku Unity** dialogové okno, zkontrolujte, zda jsou vybrány všechny soubory a pak vyberte **Import**.
