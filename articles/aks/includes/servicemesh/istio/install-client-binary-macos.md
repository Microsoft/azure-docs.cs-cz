---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: 5dc9686e4a9994a085cc9f4a4631e66b05d7949d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91666695"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Stažení a instalace binárního souboru klienta Istio istioctl

V prostředí založeném na bash na macOS použijte `curl` ke stažení verze Istio a pak ji rozbalte následujícím `tar` způsobem:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.7.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

`istioctl`Binární soubor klienta se spouští na klientském počítači a umožňuje v clusteru AKS instalovat a spravovat Istio. Pomocí následujících příkazů nainstalujte `istioctl` binární soubor klienta Istio do prostředí založeného na bash na MacOS. Tyto příkazy kopírují `istioctl` binární soubor klienta do umístění standardního uživatelského programu v `PATH` .

```bash
sudo mv ./istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Pokud chcete dokončit příkazový řádek pro `istioctl` binární soubor klienta Istio, nastavte ho následujícím způsobem:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```