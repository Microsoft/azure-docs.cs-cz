---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 47cb20f3a23caf586777523e56902af20b747ea1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400030"
---
:::row:::
    :::column span="3":::
        Java SDK pro Android je zabalen jako <a href="https://developer.android.com/studio/projects/android-library" target="_blank">AAR <span class="docon docon-navigate-external x-hidden-focus"> </span>(Android Library) </a>, který obsahuje potřebné knihovny a požadovaná oprávnění Android. Je hostován v úložišti Maven `https://csspeechstorage.blob.core.windows.net/maven/` `com.microsoft.cognitiveservices.speech:client-sdk:1.11.0`jako balíček .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Chcete-li balíček využívat z projektu Android Studio, proveďte následující změny:

1. V souboru *build.gradle* na úrovni projektu `repository` přidejte do oddílu následující:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. V souboru *build.gradle* na úrovni modulu `dependencies` přidejte do oddílu následující:
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.11.0'
  ```

Sada Java SDK je také součástí [sady SDK pro řečová zařízení](../speech-devices-sdk.md).

#### <a name="additional-resources"></a>Další zdroje

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Zdrojový kód rychlého startu java specifické pro Android<span class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Zdrojový kód rychlého startu java runtime (JRE)<span class="docon docon-navigate-external x-hidden-focus"></span></a>