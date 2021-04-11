---
author: phillipgibson
ms.topic: include
ms.date: 03/15/2021
ms.author: phillipgibson
ms.openlocfilehash: 1c7dbe9bb6cf28f4dee2c4e9aa036410fadbbddf
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080159"
---
## <a name="download-and-install-the-osm-client-binary"></a>Stažení a instalace binárního souboru klienta OSM

V prostředí založeném na bash použijte `curl` ke stažení verze osm a pak extrakci pomocí `tar` následujícího postupu:

```bash
# Specify the OSM version that will be leveraged throughout these instructions
OSM_VERSION=v0.8.2

curl -sL "https://github.com/openservicemesh/osm/releases/download/$OSM_VERSION/osm-$OSM_VERSION-darwin-amd64.tar.gz" | tar -vxzf -
```

`osm`Binární soubor klienta se spouští na klientském počítači a umožňuje správu osm v clusteru AKS. Pomocí následujících příkazů nainstalujte `osm` binární soubor klienta osm do prostředí založeného na bash. Tyto příkazy kopírují `osm` binární soubor klienta do umístění standardního uživatelského programu v `PATH` .

```bash
sudo mv ./darwin-amd64/osm /usr/local/bin/osm
sudo chmod +x /usr/local/bin/osm
```

Pomocí následujícího příkazu můžete ověřit, zda je `osm` Knihovna klienta správně přidána do vaší cesty a čísla verze.

```
osm version
```
