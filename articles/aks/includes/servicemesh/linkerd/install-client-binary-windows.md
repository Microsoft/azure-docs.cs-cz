---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: b7d832ba375925d30a976dfde63a776b5d0742bb
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593705"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Stažení a instalace binárního linkeru linkeru

V prostředí založeném na prostředí PowerShell ve Windows použijte `Invoke-WebRequest` ke stažení Linkerované verze následujícím způsobem:

```powershell
# Specify the Linkerd version that will be leveraged throughout these instructions
$LINKERD_VERSION="stable-2.6.0"

# Enforce TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-windows.exe" -OutFile "linkerd2-cli-$LINKERD_VERSION-windows.exe"
```

Binární soubor klienta `linkerd` běží na klientském počítači a umožňuje interakci s propojenou sítí služby. Použijte následující příkazy k instalaci linkeru `linkerd`ho binárního souboru klienta do prostředí založeného na prostředí PowerShell ve Windows. Tyto příkazy kopírují binární soubor `linkerd` klienta do Linkerované složky a pak ji ihned (v aktuálním prostředí) a trvale (v rámci restartování prostředí) zpřístupní přímo pomocí `PATH`. Ke spuštění těchto příkazů nepotřebujete oprávnění vyšší úrovně (správce) a nemusíte restartovat prostředí.

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