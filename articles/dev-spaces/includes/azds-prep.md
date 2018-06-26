---
title: zahrnout soubor
description: zahrnout soubor
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: d44f33b0e9f71c1d8d6e2c9878b08f9fa0e1f8a1
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938168"
---
## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Příprava Docker a Kubernetes vývoj kódu
Pokud máte základní webové aplikace, kterou můžete spustit místně. Budete je nyní containerize vytvořením prostředky, které definují kontejneru aplikace a jak ji nasadí na Kubernetes. Tato úloha je snadné provést prostory Dev Azure: 

1. Spusťte VS Code a otevřete `webfrontend` složky. (Zobrazování výzev výchozí přidat prostředky ladění nebo obnovení projektu můžete ignorovat.)
1. Otevřete integrované terminálu v produktu VS Code (pomocí **zobrazení > integrované Terminálové** nabídky).
1. Spusťte tento příkaz (ujistěte **webfrontend** je aktuální složku):

    ```cmd
    azds prep --public
    ```

Rozhraní příkazového řádku Azure `azds prep` příkaz generuje Docker a Kubernetes prostředky s výchozím nastavením:
* `./Dockerfile` Popisuje aplikace kontejneru bitové kopie, a jak zdrojový kód je vytvořen a běží v kontejneru.
* A [Helm grafu](https://docs.helm.sh) pod `./charts/webfrontend` popisuje, jak nasadit kontejner Kubernetes.

Zatím není nutné pochopit celý obsah těchto souborů. Je vhodné odkazující na, ale který **stejné Kubernetes a Docker prostředky jako kód konfigurace lze z vývojového prostřednictvím do produkčního prostředí, a zajišťuje tak lepší konzistence různých prostředích.**
 
Soubor s názvem `./azds.yaml` je také vygenerované `prep` příkaz a je konfiguračního souboru pro Azure Dev prostory. Doplňuje artefakty Docker a Kubernetes další konfiguraci, která umožňuje iterativní vývojového prostředí v Azure.
