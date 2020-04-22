---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: e26a2c214a03243d6507296c1e981706be8c56db
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81736806"
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
# Make the new PATH permanently available for the current User
$USER_PATH = [environment]::GetEnvironmentVariable("PATH", "User") + ";C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $USER_PATH, "User")
# Make the new PATH immediately available in the current shell
$env:PATH += ";C:\Istio\"
```