---
title: Instalace prostorových kotev Azure pro Unity
description: Konfigurace projektu Unity pro použití prostorových kotev Azure
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 03/30/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: fe9160f22754c62888b2a61ce9751f596842604e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106076686"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Konfigurace prostorových kotev Azure v projektu Unity

V této příručce se dozvíte, jak začít se sadou SDK prostorových kotev Azure v projektu Unity.

## <a name="project-requirements"></a>Požadavky projektu

[!INCLUDE [Unity Project Requirements](../../../includes/spatial-anchors-unity-project-requirements.md)]

## <a name="configuring-a-project"></a>Konfigurace projektu

Před zahrnutím sady Azure prostorové kotvy do vašeho projektu Unity nezapomeňte nainstalovat [požadované](#project-requirements) balíčky pomocí Správce balíčků Unity.

### <a name="download-asa-packages"></a>Stáhnout balíčky ASA
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-asa-packages"></a>Importovat balíčky ASA
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="android-only-configure-the-maintemplategradle-file"></a>Jenom Android: konfigurace souboru mainTemplate. Gradle

1. Přejít na **Upravit**  >  **nastavení projektu**  >  **Player**.
2. Na **panelu Inspektor** pro **nastavení přehrávače** vyberte ikonu **Androidu** .
3. V části **sestavení** zaškrtněte políčko **vlastní hlavní Gradle šablona** , čímž vygenerujete vlastní šablonu Gradle na adrese `Assets\Plugins\Android\mainTemplate.gradle` .
4. Otevřete `mainTemplate.gradle` soubor v textovém editoru.
5. V `dependencies` části vložte následující závislosti:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

Až to bude hotové, vaše `dependencies` část by měla vypadat přibližně takto:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postupy: vytváření a hledání kotev v Unity](./create-locate-anchors-unity.md)
