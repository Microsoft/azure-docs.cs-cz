---
title: 'Úvodní příručka: Nastavení platformy Platformy Pro Jazyk Java (Windows, Linux, macOS) – služba Řeč'
titleSuffix: Azure Cognitive Services
description: Tato příručka slouží k nastavení platformy pro používání Javy (Windows, Linux, macOS) se sadou SDK služby Řeč.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 7147f0d13c88c1d2e17e81a360a5aee55ee760ed
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "78924972"
---
Tato příručka ukazuje, jak nainstalovat [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro 64bitovou verzi java 8 JRE. Pokud chcete, aby název balíčku začal sám, java sdk není k dispozici v centrálním úložišti Maven. Ať už používáte Gradle `pom.xml` nebo soubor závislostí, musíte přidat vlastní `https://csspeechstorage.blob.core.windows.net/maven/` úložiště ukazující na (viz níže název balíčku).

> [!NOTE]
> Informace o sadě Speech Devices SDK a zařízení Roobo najdete v sadě [Speech Devices SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md).

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>Podporované operační systémy

- Balíček Java Speech SDK je k dispozici pro tyto operační systémy:
  - Windows: pouze 64bit
  - Mac: macOS X verze 10.13 nebo novější
  - Linux: 64bitový pouze na Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8

## <a name="prerequisites"></a>Požadavky

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) nebo [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/) (vyžaduje Java již nainstalován)
- Podporované platformy Linux budou vyžadovat`libssl` instalaci určitých knihoven (pro podporu sadezových vrstev a `libasound2` pro podporu zvuku). Níže naleznete níže uvedené informace o níže uvedených příkazech potřebných k instalaci správných verzí těchto knihoven.

  - V Ubuntu spusťte následující příkazy pro instalaci požadovaných balíčků:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - V Debianu 9 spusťte následující příkazy pro instalaci požadovaných balíčků:

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

  - Na RHEL/CentOS 8, spusťte následující příkazy k instalaci požadovaných balíčků:

        ```sh
        sudo yum update
        sudo yum install alsa-lib java-1.8.0-openjdk-devel openssl
        ```

> [!NOTE]
> Na RHEL/CentOS 8 postupujte podle [pokynů, jak nakonfigurovat OpenSSL pro Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

- V systému Windows potřebujete pro vaši platformu [redistribuovatelný microsoft visual c++ pro Visual Studio 2019.](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) Všimněte si, že instalace poprvé může vyžadovat restartování systému Windows před pokračováním v této příručce.

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Vytvoření projektu Eclipse a instalace sady Speech SDK

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>Další kroky

[!INCLUDE [windows](../quickstart-list.md)]
