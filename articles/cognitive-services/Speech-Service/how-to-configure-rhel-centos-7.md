---
title: Jak nakonfigurovat službu RHEL/CentOS 7-Speech Service
titleSuffix: Azure Cognitive Services
description: Naučte se konfigurovat RHEL/CentOS 7, aby bylo možné použít sadu Speech SDK.
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: ba531164e024f96d3bdd23912f3f6e90275edda4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83589733"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>Konfigurace RHEL/CentOS 7 pro sadu Speech SDK

Red Hat Enterprise Linux (RHEL) 8 x64 a CentOS 8 x64 jsou oficiálně podporovány sadou Speech SDK verze 1.10.0 a novější. Je také možné použít sadu Speech SDK na RHEL/CentOS 7 x64, ale to vyžaduje aktualizaci kompilátoru C++ (pro vývoj v jazyce C++) a Shared C++ Runtime Library ve vašem systému.

Chcete-li zjistit verzi kompilátoru jazyka C++, spusťte příkaz:

```bash
g++ --version
```

Pokud je kompilátor nainstalován, výstup by měl vypadat takto:

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

Tato zpráva vám informuje o tom, že je nainstalovaná hlavní verze RSZ 4. Tato verze nemá plnou podporu pro Standard C++ 11, který sada Speech SDK používá. Pokud se pokusíte zkompilovat program C++ s touto verzí RSZ a hlavičky sady Speech SDK budou mít za následek chyby při kompilaci.

Je také důležité ověřit verzi knihovny Shared C++ runtime (libstdc + +). Většina sady Speech SDK je implementována jako nativní knihovny C++, což znamená, že závisí na libstdc + + bez ohledu na jazyk, který používáte pro vývoj aplikací.

Chcete-li zjistit umístění libstdc + + v systému, spusťte příkaz:

```bash
ldconfig -p | grep libstdc++
```

Výstup na Vanilla RHEL/CentOS 7 (x64) je:

```bash
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

V závislosti na této zprávě budete chtít ověřit definice verzí pomocí tohoto příkazu:

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

Výstup by měl být:

```bash
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

Sada Speech SDK vyžaduje **CXXABI_1.3.9** a **GLIBCXX_3.4.21**. Tyto informace můžete najít spuštěním `ldd libMicrosoft.CognitiveServices.Speech.core.so` v knihovně sady Speech SDK z balíčku pro Linux.

> [!NOTE]
> Doporučuje se, aby byla verze RSZ nainstalovaná v systému minimálně **5.4.0**s odpovídajícími běhovými knihovnami.

## <a name="example"></a>Příklad

Toto je ukázková sada příkazů, která ukazuje, jak nakonfigurovat RHEL/CentOS 7 x64 pro vývoj (C++, C#, Java, Python) pomocí sady Speech SDK 1.10.0 nebo novější:

### <a name="1-general-setup"></a>1. Obecné nastavení

Nejdřív nainstalujte všechny obecné závislosti:

```bash
# Only run ONE of the following two commands
# - for CentOS 7:
sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
# - for RHEL 7:
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl python3
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free
```

### <a name="2-cc-compiler-and-runtime-libraries"></a>2. kompilátor C/C++ a běhové knihovny

Nainstalujte požadované balíčky pomocí tohoto příkazu:

```bash
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
```

> [!NOTE]
> Balíček libmpc-devel se v aktualizaci RHEL 7,8 už nepoužívá. Pokud výstup předchozího příkazu obsahuje zprávu
>
> ```bash
> No package libmpc-devel available.
> ```
>
> potřebné soubory musí být nainstalovány z původních zdrojů. Spusťte následující příkazy:
>
> ```bash
> curl https://ftp.gnu.org/gnu/mpc/mpc-1.1.0.tar.gz -O
> tar zxf mpc-1.1.0.tar.gz
> mkdir mpc-1.1.0-build && cd mpc-1.1.0-build
> ../mpc-1.1.0/configure --prefix=/usr/local --libdir=/usr/local/lib64
> make -j$(nproc)
> sudo make install-strip
> ```

Další aktualizace knihoven kompilátorů a modulů runtime:

```bash
# Build GCC 5.4.0 and runtimes and install them under /usr/local
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip
```

Pokud je potřeba, aby byl aktualizovaný kompilátor a knihovny nasazeny na několika počítačích, můžete je jednoduše kopírovat z části `/usr/local` do jiných počítačů. Pokud potřebujete pouze knihovny modulu runtime, soubory v nástroji `/usr/local/lib64` budou dostatečné.

### <a name="3-environment-settings"></a>3. nastavení prostředí

Pro dokončení konfigurace spusťte následující příkazy:

```bash
# Set SSL cert file location
# (this is required for any development/testing with Speech SDK)
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt

# Add updated C/C++ runtimes to the library path
# (this is required for any development/testing with Speech SDK)
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# For C++ development only:
# - add the updated compiler to PATH
#   (note, /usr/local/bin should be already first in PATH on vanilla systems)
# - add Speech SDK libraries from the Linux tar package to LD_LIBRARY_PATH
#   (note, use the actual path to extracted files!)
export PATH=/usr/local/bin:$PATH
hash -r # reset cached paths in the current shell session just in case
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-1.10.0/lib/x64:$LD_LIBRARY_PATH

# For Python: install the Speech SDK module
python3 -m pip install azure-cognitiveservices-speech --user
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o sadě Speech SDK](speech-sdk.md)
