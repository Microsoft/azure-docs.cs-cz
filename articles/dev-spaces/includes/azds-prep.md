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
ms.openlocfilehash: e9f97f804985f948e5442c64a31d95e7931b03cd
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
## <a name="preparing-code-for-docker-and-kubernetes-development"></a>Příprava Docker a Kubernetes vývoj kódu
Pokud máte základní webové aplikace, kterou můžete spustit místně. Budete je nyní containerize vytvořením prostředky, které definují kontejneru aplikace a jak ji nasadí na Kubernetes. Toto je snadné provést prostory Dev Azure: 

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
 
Soubor s názvem `./azds.yaml` je také vygenerované `prep` příkaz a je konfiguračního souboru pro Azure Dev prostory. Doplňuje artefakty Docker a Kubernetes další konfiguraci, která umožňuje iterativní vývojového prostředí v Azure. Například výchozí Helm graf nevystavuje žádné veřejné koncové body. V některých případech je však vhodné dočasně otevře veřejný koncový bod během vývoje, abyste je mohli otestovat váš kód Řekněme, z mobilního zařízení nebo adresa URL webhooku. Soubor azds.yaml vytvořený `azds prep --public` přepíše výchozí parametry Helm vystavit veřejný koncový bod vývoj pouze při spuštění.
