---
title: 'Rychlý Start: sada Speech SDK C++ (Linux) nastavení platformy – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto průvodce můžete nastavit platformu pro C++ v systému Linux se sadou Speech Service SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 4f211e4b90dcc8bffa2fbba6fa4783caf846f50c
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78383874"
---
V této příručce se dozvíte, jak nainstalovat [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro Linux.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Požadavky na systém

Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8)

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu budete potřebovat:

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)

* Podporované platformy Linux budou vyžadovat nainstalovaná konkrétní knihovny`libssl` (pro podporu SSL (Secure `libasound2` Sockets Layer) a pro podporu zvuku. Pro příkazy potřebné k instalaci správných verzí těchto knihoven použijte níže uvedenou distribuci.

   * V Ubuntu:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

   * V Debian 9:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.2 libasound2 wget
     ```

   * V RHEL/CentOS 8:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> V RHEL/CentOS 8 postupujte podle pokynů, [jak nakonfigurovat OpenSSL pro Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

## <a name="install-speech-sdk"></a>Instalace sady Speech SDK

Sadu Speech SDK pro Linux můžete použít k sestavení 64bitových i 32bitových aplikací. Požadované knihovny a hlavičkové soubory lze stáhnout jako soubor tar z https://aka.ms/csspeech/linuxbinary.

Sadu SDK si stáhněte a nainstalujte následujícím způsobem:

1. Vyberte adresář, do kterého se mají soubory sady Speech SDK extrahovat, a proměnnou prostředí `SPEECHSDK_ROOT` nastavte tak, aby odkazovala na tento adresář. Tato proměnná umožňuje snadno odkazovat na adresář v budoucích příkazech. Pokud třeba chcete používat adresář `speechsdk` ve svém domovském adresáři, použijte příkaz podobný tomuto:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Pokud adresář neexistuje, vytvořte ho.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Stáhněte a extrahujte archiv `.tar.gz` obsahující binární soubory sady Speech SDK:

   ```sh
   wget -O SpeechSDK-Linux.tar.gz https://aka.ms/csspeech/linuxbinary
   tar --strip 1 -xzf SpeechSDK-Linux.tar.gz -C "$SPEECHSDK_ROOT"
   ```

1. Ověřte obsah adresáře nejvyšší úrovně extrahovaného balíčku:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Výpis adresáře by měl obsahovat oznámení třetích stran a soubory s licencí a také adresář `include` obsahující soubory hlaviček (`.h`) a adresář `lib` obsahující knihovny.

   [!INCLUDE [Linux Binary Archive Content](~/includes/cognitive-services-speech-service-linuxbinary-content.md)]

Nyní můžete přejít na [Další kroky](#next-steps) níže.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [windows](../quickstart-list.md)]
