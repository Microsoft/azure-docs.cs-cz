---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: dapine
ms.openlocfilehash: a298e78c32464680dab9feccb3cfc84f686ff81b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656630"
---
:::row:::
    :::column span="3":::
        Sada Speech SDK podporuje pouze **Ubuntu 16.04/18.04**, **Debian 9**, Red Hat Enterprise **Linux (RHEL) 8**a **CentOS 7/8** na následujících cílových architekturách při použití s Linuxem:
        - x86
        - x64
        - ARM32
        - ARM64
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Při cílení linuxarm64 a pomocí Jazyka C# - je vyžadován balíček .NET Core 3.x (dotnet-sdk-3.x). Pokud cílíte na ARM32 nebo ARM64, Python není podporován.

> [!NOTE]
> Architektury x86 Ubuntu 16.04, Ubuntu 18.04 a Debian 9 podporují vývoj Jazyka C++ pouze s sadou Speech SDK.

### <a name="system-requirements"></a>Požadavky na systém

Pro nativní aplikace sada Speech SDK spoléhá na `libMicrosoft.CognitiveServices.Speech.core.so`. Ujistěte se, že cílová architektura (x86, x64) odpovídá aplikaci. V závislosti na verzi Linuxu může být vyžadováno další závislosti.

- Sdílené knihovny knihovny GNU C (včetně knihovny POSIX Threads Programming library), `libpthreads`
- Knihovna OpenSSL`libssl.so.1.0.0` `libssl.so.1.0.2`( nebo )
- Sdílená knihovna pro aplikace`libasound.so.2`ALSA ( )

# <a name="ubuntu-16041804"></a>[Ubuntu 16.04/18.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# <a name="debian-9"></a>[Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# <a name="rhel-8-and-centos-78"></a>[RHEL 8 a CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> Postupujte podle [pokynů, jak nakonfigurovat RHEL/CentOS 7 pro řeč SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).

> [!TIP]
> Na RHEL/CentOS 8 postupujte podle [pokynů, jak nakonfigurovat OpenSSL pro Linux](../how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
