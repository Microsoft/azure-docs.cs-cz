---
author: paulbouwer
ms.topic: include
ms.date: 11/15/2019
ms.author: pabouwer
ms.openlocfilehash: 74f5b22ccc822a188059b29d9c661a15cf8412bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593938"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>Stáhněte a nainstalujte binární soubor klienta Istio istioctl

V bash-založené shell na MacOS, použijte `curl` ke stažení vydání `tar` Istio a pak extrahovat takto:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.4.0

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

Binární `istioctl` klient skáče v klientském počítači a umožňuje interakci s sítí služby Istio. Pomocí následujících příkazů nainstalujte binární `istioctl` soubor klienta Istio do prostředí založeného na bashna MacOS. Tyto příkazy `istioctl` zkopírují binární soubor klienta `PATH`do standardního umístění uživatelského programu ve vašem .

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Pokud chcete dokončování příkazového řádku `istioctl` pro binární soubor klienta Istio, nastavte jej takto:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```