---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: 6897bf9b4ccce71048af88a3108e3d87d17a375d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102434441"
---
:::row:::
    :::column span="3":::
        Java SDK pro Android je zabalená jako <a href="https://developer.android.com/studio/projects/android-library" target="_blank">Knihovna AAR (Android Library) </a>, která zahrnuje nezbytné knihovny a požadovaná oprávnění pro Android. Hostuje se v úložišti Maven v `https://csspeechstorage.blob.core.windows.net/maven/` balíčku `com.microsoft.cognitiveservices.speech:client-sdk:1.15.0` .
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Java" src="https://docs.microsoft.com/media/logos/logo_java.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

Chcete-li balíček využívat z Android Studio projektu, proveďte následující změny:

1. V souboru *Build. Gradle* na úrovni projektu přidejte do `repositories` oddílu následující:
  ```gradle
  maven { url 'https://csspeechstorage.blob.core.windows.net/maven/' }
  ```

2. V souboru *Build. Gradle* na úrovni modulu přidejte do `dependencies` oddílu následující:
  ```gradle
  implementation 'com.microsoft.cognitiveservices.speech:client-sdk:1.15.0'
  ```

Sada Java SDK je také součástí [sady Speech SDK pro zařízení](../speech-devices-sdk.md).

#### <a name="additional-resources"></a>Další zdroje informací

- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android" target="_blank">Zdrojový kód pro rychlý Start Java specifický pro Android </a>
- <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre" target="_blank">Zdrojový kód pro rychlé zprovoznění Java Runtime (JRE) </a>
