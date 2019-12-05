---
title: 'Rychlý Start: sada C++ Speech SDK (MacOS) platforma nastavení – služba pro rozpoznávání řeči'
titleSuffix: Azure Cognitive Services
description: Pomocí tohoto průvodce můžete nastavit platformu pro C++ MacOS pomocí sady SDK služby Speech.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: erhopf
ms.openlocfilehash: 1b01a7df286af65d3363956706ad2703a339a255
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74818539"
---
V této příručce se dozvíte, jak nainstalovat [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro C++ MacOS 10,13 a novější.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Požadavky na systém

macOS 10,13 a novější

## <a name="install-speech-sdk"></a>Instalace sady Speech SDK

1. Vyberte adresář, do kterého se mají soubory sady Speech SDK extrahovat, a proměnnou prostředí `SPEECHSDK_ROOT` nastavte tak, aby odkazovala na tento adresář. Tato proměnná umožňuje snadno odkazovat na adresář v budoucích příkazech. Pokud třeba chcete používat adresář `speechsdk` ve svém domovském adresáři, použijte příkaz podobný tomuto:

   ```sh
   export SPEECHSDK_ROOT="$HOME/speechsdk"
   ```

1. Pokud adresář neexistuje, vytvořte ho.

   ```sh
   mkdir -p "$SPEECHSDK_ROOT"
   ```

1. Stažení a extrakce `.zip`ho archivu obsahujícího rozhraní sady Speech SDK:

   ```sh
   wget -O SpeechSDK-macOS.zip https://aka.ms/csspeech/macosbinary
   unzip SpeechSDK-macOS.zip -d "$SPEECHSDK_ROOT"
   ```

1. Ověřte obsah adresáře nejvyšší úrovně extrahovaného balíčku:

   ```sh
   ls -l "$SPEECHSDK_ROOT"
   ```

   Výpis adresáře by měl obsahovat soubory oznámení a licencí třetích stran a také `MicrosoftCognitiveServicesSpeech.framework` adresář.

Nyní můžete přejít na [Další kroky](#next-steps) níže.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [windows](../quickstart-list.md)]
