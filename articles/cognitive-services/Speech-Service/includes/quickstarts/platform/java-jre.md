---
title: 'Rychlý Start: sada Speech SDK pro Java (Windows, Linux, macOS) nastavení platformy – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto průvodce můžete nastavit platformu pro používání Java (Windows, Linux, macOS) se sadou Speech Service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 195d43739ff27a493b2f2923eac43df03313963b
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74816285"
---
V této příručce se dozvíte, jak nainstalovat [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro 64 JRE Java 8.

> [!NOTE]
> Informace o sadě Speech Devices SDK a zařízení Roobo najdete v sadě [Speech Devices SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Podporované operační systémy

- Balíček Java Speech SDK je k dispozici pro tyto operační systémy:
  - Windows: jenom 64 bitů
  - Mac: macOS X verze 10,13 nebo novější
  - Linux: 64-bit pouze v Ubuntu 16,04, Ubuntu 18,04 nebo Debian 9

## <a name="prerequisites"></a>Předpoklady

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) nebo [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Java IDE zatmění](https://www.eclipse.org/downloads/) (vyžaduje už nainstalovaný Java)
- Podporované platformy Linux budou vyžadovat, aby byly nainstalovány určité knihovny (`libssl` pro podporu technologie Secure Sockets Layer a `libasound2` pro podporu zvuku). Pro příkazy potřebné k instalaci správných verzí těchto knihoven použijte níže uvedenou distribuci.

  - V Ubuntu spusťte následující příkazy, abyste nainstalovali požadované balíčky:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - V Debian 9 spusťte následující příkazy, abyste nainstalovali požadované balíčky:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

- V systému Windows budete potřebovat [Microsoft Visual C++ Redistributable pro Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) pro vaši platformu. Mějte na paměti, že pokud tuto instalaci nainstalujete poprvé, může před pokračováním v této příručce vyžadovat restartování systému Windows.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Vytvořit projekt zatmění a nainstalovat sadu Speech SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Další kroky

[!INCLUDE [windows](../quickstart-list.md)]
