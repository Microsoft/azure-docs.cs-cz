---
title: Instalace rozhraní příkazového řádku DC/OS | Dokumentace Microsoftu
description: Nainstalujte rozhraní příkazového řádku DC/OS.
services: container-service
documentationcenter: ''
author: rgardler
manager: timlt
editor: ''
tags: acs, azure-container-service
keywords: Kontejnery, mikroslužby, DC/OS, Azure
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/10/2016
ms.author: rogardle
ms.openlocfilehash: e2601d5200f0b8ebcfb856bffb871f01b3acb215
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60557522"
---
> [!NOTE]
> To slouží k práci s clustery ACS založenými na DC/OS. Není nutné pro clustery ACS založené na metodě Swarm.
> 
> 

Nejdřív [se připojte ke svému clusteru ACS založenému na DC/OS](../articles/container-service/container-service-connect.md). Až to uděláte, můžete rozhraní příkazového řádku DC/OS pomocí níže uvedených příkazů nainstalovat na klientský počítač:

```bash
sudo pip install virtualenv
mkdir dcos && cd dcos
wget https://raw.githubusercontent.com/mesosphere/dcos-cli/master/bin/install/install-optout-dcos-cli.sh
chmod +x install-optout-dcos-cli.sh
./install-optout-dcos-cli.sh . http://localhost --add-path yes
```

Pokud používáte starší verzi Pythonu, můžou se zobrazovat upozornění „InsecurePlatformWarning“. Ta můžete bezpečně ignorovat.

Pokud chcete začít, aniž byste museli restartovat své prostředí, spusťte tento příkaz:

```bash
source ~/.bashrc
```

Pokud spustíte nové prostředí, nebude tento krok nutný.

Teď si můžete ověřit, jestli se rozhraní příkazového řádku nainstalovalo:

```bash
dcos --help
```