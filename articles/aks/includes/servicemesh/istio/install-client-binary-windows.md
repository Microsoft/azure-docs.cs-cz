---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 562382cc1cfb6adb7e65d76e717df4c4e2962ba7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593935"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Stáhněte a nainstalujte binární soubor klienta Istio istioctl

V prostředí založeném na prostředí `Invoke-WebRequest` PowerShell v systému Windows použijte `Expand-Archive` ke stažení vydání Istio a pak extrahujte následujícím způsobem:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Binární `istioctl` klient skáče v klientském počítači a umožňuje interakci s sítí služby Istio. Pomocí následujících příkazů nainstalujte binární `istioctl` soubor klienta Istio do prostředí založeného na prostředí PowerShell v systému Windows. Tyto příkazy `istioctl` zkopírují binární soubor klienta do složky Istio a poté jej zpřístupní okamžitě `PATH`(v aktuálním prostředí) a trvale (napříč restartováním prostředí) prostřednictvím aplikace . Ke spuštění těchto příkazů nepotřebujete zvýšená oprávnění (Správce) a nemusíte restartovat prostředí.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```