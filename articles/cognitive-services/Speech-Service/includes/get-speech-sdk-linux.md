---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: e47c8bc4dc814f1d4c5cb115a2da911544dd55f8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81399977"
---
:::row:::
    :::column span="3":::
        Sada Speech SDK podporuje pouze **Ubuntu 16.04/18.04**, **Debian 9**, **Red Hat Enterprise Linux (RHEL) 7/8**a **CentOS 7/8** při použití se systémem Linux pro následující cílové architektury:
        - x64
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!IMPORTANT]
> Při cílení na Linux ARM64 a použití jazyka C# je vyžadován balíček .NET Core 3. x (dotnet-SDK-3. x). Pokud cílíte na ARM32 nebo ARM64, Python se nepodporuje.

> [!NOTE]
> Architektury x86 rozhraní Ubuntu 16,04, Ubuntu 18,04 a Debian 9 podporují pouze vývoj v jazyce C++ se sadou Speech SDK.

### <a name="system-requirements"></a>Požadavky na systém

Pro nativní aplikaci závisí sada Speech SDK na `libMicrosoft.CognitiveServices.Speech.core.so`. Ujistěte se, že cílová architektura (x86, x64) odpovídá aplikaci. V závislosti na verzi pro Linux můžou být potřeba další závislosti.

- Sdílené knihovny knihovny GNU C (včetně knihovny programování vláken POSIX `libpthreads`)
- Knihovna OpenSSL (`libssl.so.1.0.0` nebo `libssl.so.1.0.2`)
- Sdílená knihovna pro aplikace ALSA (`libasound.so.2`)

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

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 a CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!IMPORTANT]
> Postupujte podle pokynů, [jak nakonfigurovat RHEL/CentOS 7 pro sadu Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).

> [!TIP]
> V RHEL/CentOS 8 postupujte podle pokynů, [jak nakonfigurovat OpenSSL pro Linux](../how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
