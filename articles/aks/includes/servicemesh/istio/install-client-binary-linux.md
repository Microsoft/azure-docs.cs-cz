---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: b310de560f9791e1fc49d54dfbf0789c38d37f57
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593937"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Stažení a instalace binárního souboru klienta Istio istioctl

V prostředí založeném na bash v systému Linux nebo v subsystému [Windows pro Linux][install-wsl]použijte `curl` ke stažení verze Istio a pak extrakci pomocí `tar` následujícím způsobem:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Binární soubor klienta `istioctl` běží na klientském počítači a umožňuje interakci s sítí služby Istio. Pomocí následujících příkazů nainstalujte binární soubor Istio `istioctl` klienta do prostředí založeného na bash na Linux nebo v subsystému [Windows pro Linux][install-wsl]. Tyto příkazy kopírují binární soubor `istioctl` klienta do umístění standardního uživatelského programu v `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Pokud byste chtěli doplňování příkazového řádku Istio `istioctl` binárním souboru klienta, nastavte ho následujícím způsobem:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10