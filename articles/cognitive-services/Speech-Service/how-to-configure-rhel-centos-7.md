---
title: Jak nakonfigurovat RHEL/CentOS 7 - Služba řeči
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak nakonfigurovat RHEL/CentOS 7 tak, aby bylo možné použít sadu Speech SDK.
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: dc09d517d95b5a3f2a88504a14f1451d1de5ffc9
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639160"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>Konfigurace funkce RHEL/CentOS 7 pro sadu Speech SDK

Red Hat Enterprise Linux (RHEL) 8 x64 a CentOS 8 x64 jsou oficiálně podporovány sadou Speech SDK verze 1.10.0 a novější. Je také možné použít řeč SDK na RHEL/CentOS 7 x64, ale to vyžaduje aktualizaci kompilátoru C++ (pro vývoj C++) a sdílené c++ runtime knihovny ve vašem systému.

Chcete-li zkontrolovat verzi kompilátoru jazyka C++, spusťte:

```bash
g++ --version
```

Pokud je nainstalován kompilátor, výstup by měl vypadat takto:

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

Tato zpráva vás dozví, že je nainstalována hlavní verze 4 GCC. Tato verze nemá plnou podporu pro standard C++ 11, který používá sada Speech SDK. Pokus o kompilaci programu Jazyka C++ s touto verzí GCC a záhlavísady Speech SDK bude mít za následek chyby kompilace.

Je také důležité zkontrolovat verzi sdílené knihovny runtime C++ (libstdc++). Většina sady Speech SDK je implementována jako nativní knihovny jazyka C++, což znamená, že závisí na libstdc++ bez ohledu na jazyk, který používáte k vývoji aplikací.

Chcete-li najít umístění libstdc++ v systému, spusťte:

```bash
ldconfig -p | grep libstdc++
```

Výstup na vanilkovém RHEL/CentOS 7 (x64) je:

```
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

Na základě této zprávy budete chtít zkontrolovat definice verzí pomocí tohoto příkazu:

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

Výstup by měl být:

```
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

Sada Speech SDK vyžaduje **CXXABI_1.3.9** a **GLIBCXX_3.4.21**. Tyto informace můžete najít `ldd libMicrosoft.CognitiveServices.Speech.core.so` spuštěním v knihovnách sady Speech SDK z balíčku Linux.

> [!NOTE]
> Doporučuje se, aby verze GCC nainstalovaná v systému byla alespoň **5.4.0**, s odpovídajícími runtime knihovnami.

## <a name="example"></a>Příklad

Toto je ukázkový příkaz, který ukazuje, jak nakonfigurovat RHEL/CentOS 7 x64 pro vývoj (C++, C#, Java, Python) s sadou Speech SDK 1.10.0 nebo novější:

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

# Build GCC 5.4.0 and runtimes and install them under /usr/local
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip

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
