---
author: paulbouwer
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 7a22256c88f9cee3ce62c68a2de4a5974d76f026
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593704"
---
## <a name="download-and-install-the-linkerd-linkerd-client-binary"></a>Stažení a instalace binárního souboru klienta Linkerd Linkerd

V bash-založené shell na Linux u Linuxu nebo [Windows Subsystem pro Linux][install-wsl], použijte `curl` ke stažení linkerd verze takto:

```bash
# Specify the Linkerd version that will be leveraged throughout these instructions
LINKERD_VERSION=stable-2.6.0

curl -sLO "https://github.com/linkerd/linkerd2/releases/download/$LINKERD_VERSION/linkerd2-cli-$LINKERD_VERSION-linux"
```

Binární `linkerd` klient skáče v klientském počítači a umožňuje interakci s mřížkou služby Linkerd. Pomocí následujících příkazů nainstalujte binární `linkerd` soubor klienta Linkerd do prostředí založeného na bashna Linuxu nebo [Windows Subsystem pro Linux][install-wsl]. Tyto příkazy `linkerd` zkopírují binární soubor klienta `PATH`do standardního umístění uživatelského programu ve vašem .

```bash
sudo cp ./linkerd2-cli-$LINKERD_VERSION-linux /usr/local/bin/linkerd
sudo chmod +x /usr/local/bin/linkerd
```

Pokud chcete dokončení příkazového řádku pro binární `linkerd` soubor klienta Linkerd, nastavte jej následujícím způsobem:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && linkerd completion bash > ~/completions/linkerd.bash
source ~/completions/linkerd.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/linkerd.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10