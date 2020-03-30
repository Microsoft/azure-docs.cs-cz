---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: b7d832ba375925d30a976dfde63a776b5d0742bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593705"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Stažení a instalace binárního souboru klienta Linkerd Linkerd

V prostředí založeném na prostředí `Invoke-WebRequest` PowerShell v systému Windows použijte ke stažení verze Linkerd následujícím způsobem:

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

Binární `linkerd` klient skáče v klientském počítači a umožňuje interakci s mřížkou služby Linkerd. Pomocí následujících příkazů nainstalujte binární `linkerd` soubor klienta Linkerd do prostředí založeného na prostředí PowerShell v systému Windows. Tyto příkazy `linkerd` zkopírují binární soubor klienta do složky Linkerd a poté jej zpřístupní okamžitě `PATH`(v aktuálním prostředí) a trvale (v rámci restartování prostředí) prostřednictvím aplikace . Ke spuštění těchto příkazů nepotřebujete zvýšená oprávnění (Správce) a nemusíte restartovat prostředí.

```powershell
# Copy linkerd.exe to C:\Linkerd
New-Item -ItemType Directory -Force -Path "C:\Linkerd"
Copy-Item -Path ".\linkerd2-cli-$LINKERD_VERSION-windows.exe" -Destination "C:\Linkerd\linkerd.exe"

# Add C:\Linkerd to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Linkerd\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```