---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 562382cc1cfb6adb7e65d76e717df4c4e2962ba7
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593935"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Stažení a instalace binárního souboru klienta Istio istioctl

V prostředí založeném na prostředí PowerShell ve Windows použijte `Invoke-WebRequest` ke stažení verze Istio a pak extrakci pomocí `Expand-Archive` následujícím způsobem:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.4.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Binární soubor klienta `istioctl` běží na klientském počítači a umožňuje interakci s sítí služby Istio. Pomocí následujících příkazů nainstalujte binární soubor Istio `istioctl` klienta do prostředí založeného na prostředí PowerShell ve Windows. Tyto příkazy zkopírujte binární soubor klienta `istioctl` do složky Istio a pak ho ihned (v současném prostředí) zajistěte, aby byl hned (v aktuálním prostředí) a trvale (po restartování prostředí) prostřednictvím `PATH`. Ke spuštění těchto příkazů nepotřebujete oprávnění vyšší úrovně (správce) a nemusíte restartovat prostředí.

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