---
title: Instalace prostorových kotev Azure pro Unity
description: Konfigurace projektu Unity pro použití prostorových kotev Azure
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 2/3/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: e058186d8848256bf97d99ee1b8b1ddae7d78383
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550619"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Konfigurace prostorových kotev Azure v projektu Unity

V této příručce se dozvíte, jak začít se sadou SDK prostorových kotev Azure v projektu Unity.

## <a name="requirements"></a>Požadavky

Prostorová ukotvení Azure aktuálně podporuje Unity 2019,4 (LTS) s následujícími konfiguracemi.

* V prostorových ukotveních Azure 2.4.0 + je podporovaná Unity 2019,4 s AR Foundation 3,1.

## <a name="configuring-a-project"></a>Konfigurace projektu

### <a name="download-packages"></a>Stáhnout balíčky
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-packages"></a>Import balíčků
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
