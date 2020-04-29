---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 876e05d7b18ac193edbc9cf842ea2c1bf0555d54
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77593706"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Stažení a instalace binárního linkeru linkeru

V prostředí založeném na bash na MacOS použijte `curl` ke stažení linkerované verze následujícím způsobem:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-darwin"
```

Binární `linkerd` soubor klienta běží na klientském počítači a umožňuje interakci s propojenou sítí služby. Použijte následující příkazy pro instalaci linkeru binárního `linkerd` klienta v prostředí založeném na bash na MacOS. Tyto příkazy kopírují `linkerd` binární soubor klienta do umístění standardního uživatelského programu v `PATH`.

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-darwin /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Pokud chcete dokončit příkazový řádek pro Linkerový binární soubor `linkerd` klienta, nastavte ho následujícím způsobem:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```
