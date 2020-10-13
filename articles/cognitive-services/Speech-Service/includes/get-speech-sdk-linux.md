---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: a93c478f0621bf62b710f58f3e6f06298bad9954
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83673046"
---
:::row:::
    :::column span="3":::
        Sada Speech SDK podporuje pouze **Ubuntu 16.04/18.04**, **Debian 9**, **Red Hat Enterprise Linux (RHEL) 7/8**a **CentOS 7/8** při použití se systémem Linux pro následující cílové architektury:
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

- x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu) a ARM64 (Debian/Ubuntu) pro vývoj v jazyce C++
- x64, ARM32 (Debian/Ubuntu) a ARM64 (Debian/Ubuntu) pro Java
- x64, ARM32 (Debian/Ubuntu) a ARM64 (Debian/Ubuntu) pro .NET Core
- x64 pro Python

> [!IMPORTANT]
> Pro C# v systému Linux ARM64 se vyžaduje balíček .NET Core 3. x (dotnet-SDK-3. x).

### <a name="system-requirements"></a>Požadavky na systém

Pro nativní aplikaci závisí sada Speech SDK na `libMicrosoft.CognitiveServices.Speech.core.so` . Ujistěte se, že cílová architektura (x86, x64) odpovídá aplikaci. V závislosti na verzi pro Linux můžou být potřeba další závislosti.

- Sdílené knihovny knihovny GNU C (včetně knihovny programování vláken POSIX `libpthreads` )
- Knihovna OpenSSL ( `libssl.so.1.0.0` nebo `libssl.so.1.0.2` )
- Sdílená knihovna pro aplikace ALSA ( `libasound.so.2` )

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
> - V RHEL/CentOS 7 postupujte podle pokynů, [jak nakonfigurovat RHEL/CentOS 7 pro sadu Speech SDK](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md).
> - V RHEL/CentOS 8 postupujte podle pokynů, [jak nakonfigurovat OpenSSL pro Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md).

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
