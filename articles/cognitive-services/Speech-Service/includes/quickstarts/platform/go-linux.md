---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: cf765145cafa2eb06d77ea2e153e45c296281b71
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100552219"
---
V této příručce se dozvíte, jak nainstalovat [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro Linux.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Požadavky na systém

Linux Podívejte se na seznam [podporovaných distribucí a cílových architektur systému Linux](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu budete potřebovat:

* V systému Windows potřebujete [Microsoft Visual C++ distribuovatelné součásti pro Visual Studio 2019](https://support.microsoft.com/en-us/topic/the-latest-supported-visual-c-downloads-2647da03-1eea-4433-9aff-95f26a218cc0) pro vaši platformu. První instalace může vyžadovat restartování.
* RSZ
* [Přejít do binárního souboru (1,13 nebo novější)](https://golang.org/dl/)

* Podporované platformy Linux budou vyžadovat nainstalovaná konkrétní knihovny ( `libssl` pro podporu SSL (Secure Sockets Layer `libasound2` ) a pro podporu zvuku. Pro příkazy potřebné k instalaci správných verzí těchto knihoven použijte níže uvedenou distribuci.

   * V Ubuntu/Debian:

     ```sh
     sudo apt-get update
     sudo apt-get install build-essential libssl1.0.0 libasound2 wget
     ```

     Pokud libssl 1.0.0 není k dispozici, nainstalujte libssl 1.0. x (kde x je větší než 0) nebo místo toho libssl 1.1.

   * V RHEL/CentOS:

     ```sh
     sudo yum update
     sudo yum groupinstall "Development tools"
     sudo yum install alsa-lib openssl wget
     ```

> [!NOTE]
> - V RHEL/CentOS 7 postupujte podle pokynů, [jak nakonfigurovat RHEL/CentOS 7 pro sadu Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - V RHEL/CentOS 8 postupujte podle pokynů, [jak nakonfigurovat OpenSSL pro Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

[!INCLUDE [linux-install-sdk](linux-install-sdk.md)]


## <a name="configure-go-environment"></a>Konfigurace prostředí pro přechod

Pomocí následujících kroků nastavte prostředí pro rozpoznávání řeči a vyhledejte sadu Speech SDK. V obou krocích nahraďte `<architecture>` architekturou procesoru vašeho procesoru. To bude `x86` ,, `x64` `arm32` nebo `arm64` .

1. Vzhledem k tomu, že vazby spoléhají na `cgo` , je třeba nastavit proměnné prostředí tak, aby mohl najít sadu SDK:

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib/<architecture> -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. Aby bylo možné spouštět aplikace včetně sady SDK, musíme říct operačnímu systému, kde najít knihovny:

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<architecture>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [windows](../quickstart-list-go.md)]
