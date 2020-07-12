---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 34a393e06d91e5f60e622667a47e833e910e7228
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2020
ms.locfileid: "86244156"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Stažení a instalace binárního souboru klienta Istio istioctl

V prostředí založeném na bash v systému Linux nebo v subsystému [Windows pro Linux][install-wsl]použijte `curl` ke stažení verze Istio a pak extrakci pomocí `tar` následujícího postupu:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

`istioctl`Binární soubor klienta běží na klientském počítači a umožňuje interakci s sítí služby Istio. Pomocí následujících příkazů nainstalujte `istioctl` binární soubor klienta Istio do prostředí založeného na bash na Linux nebo v subsystému [Windows pro Linux][install-wsl]. Tyto příkazy kopírují `istioctl` binární soubor klienta do umístění standardního uživatelského programu v `PATH` .

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
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

<!-- LINKS - external -->
[install-wsl]: /windows/wsl/install-win10
