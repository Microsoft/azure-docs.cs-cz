---
author: glecaros
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: gelecaro
ms.openlocfilehash: 06e4eea32aefcb400c144be98c274e3e4bb4b121
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188176"
---
V této příručce se dozvíte, jak nainstalovat [sadu Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) pro Linux.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="system-requirements"></a>Požadavky na systém

Linux Podívejte se na seznam [podporovaných distribucí a cílových architektur systému Linux](~/articles/cognitive-services/speech-service/speech-sdk.md).

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto rychlého startu budete potřebovat:

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

1. Vzhledem k tomu, že vazby spoléhají na `cgo` , je třeba nastavit proměnné prostředí tak, aby mohl najít sadu SDK:

   ```sh
   export CGO_CFLAGS="-I$SPEECHSDK_ROOT/include/c_api"
   export CGO_LDFLAGS="-L$SPEECHSDK_ROOT/lib -lMicrosoft.CognitiveServices.Speech.core"
   ```

1. Pro spouštění aplikací, včetně sady SDK, je navíc potřeba sdělit operačnímu systému, kde najít knihovny:

   ```sh
   export LD_LIBRARY_PATH="$SPEECHSDK_ROOT/lib/<arch>:$LD_LIBRARY_PATH"
   ```

## <a name="next-steps"></a>Další kroky

[!INCLUDE [windows](../quickstart-list-go.md)]