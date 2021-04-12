---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: f99db628b08084ca750816c00a6892e877e90efc
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080155"
---
## <a name="download-and-install-the-osm-client-binary"></a>Stažení a instalace binárního souboru klienta OSM

V prostředí založeném na bash v systému Linux nebo v subsystému [Windows pro Linux][install-wsl]použijte `curl` ke stažení verze osm a pak extrakci pomocí `tar` následujícího postupu:

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.8.2

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-linux-amd64.tar.gz" | tar -vxzf -
```

`osm`Binární soubor klienta se spouští na klientském počítači a umožňuje správu osm v clusteru AKS. Pomocí následujících příkazů nainstalujte `osm` binární soubor klienta osm do prostředí založeného na bash na Linux nebo v subsystému [Windows pro Linux][install-wsl]. Tyto příkazy kopírují `osm` binární soubor klienta do umístění standardního uživatelského programu v `PATH` .

```bash
sudo mv ./linux-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

Pomocí následujícího příkazu můžete ověřit, zda je `osm` Knihovna klienta správně přidána do vaší cesty a čísla verze.

```
osm version
```

<!-- LINKS - external -->

[install-wsl]: /windows/wsl/install-win10
