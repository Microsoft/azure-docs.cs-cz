---
title: Offline nasazení
description: Offline nasazení umožňuje načítat image kontejneru z privátního registru kontejnerů, nemusíte je stahovat z Microsoft Container Registry.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 5fa0b6ca41349d20614a64006536e78d8ee71844
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97955361"
---
# <a name="offline-deployment-overview"></a>Přehled offline nasazení

Image kontejnerů používané při vytváření řadiče dat ARC Azure, spravovaných instancí SQL a PostgreSQLch skupin serverů s ochrannou škálou jsou obvykle přímo načítány ze služby Microsoft Container Registry (MCR). V některých případech nebude prostředí, které nasazujete, připojení k Container Registry Microsoft.  V takových situacích můžete načíst image kontejneru pomocí počítače _, který má_ přístup k Microsoft Container registry a pak je označit a vložit do soukromého registru kontejnerů, který _je_ možné připojit z prostředí, ve kterém chcete nasadit datové služby s podporou ARC Azure.

Vzhledem k tomu, že jsou k dispozici měsíční aktualizace pro datové služby s podporou ARC Azure a existuje velký počet imagí kontejneru, je nejlepší provést tento proces nastavování, označování a vkládání imagí kontejneru do soukromého registru kontejnerů pomocí skriptu.  Skript můžete buď automatizovat, nebo spustit ručně.

[Vzorový skript](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/scripts/pull-and-push-arc-data-services-images-to-private-registry.py) najdete v úložišti GitHub Azure ARC.

> [!NOTE]
> Tento skript vyžaduje instalaci Pythonu a [Docker CLI](https://docs.docker.com/install/).

Skript bude interaktivně vyzvat k zadání následujících informací.  Případně, pokud chcete, aby se skript spouštěl bez interaktivních výzev, můžete před spuštěním skriptu nastavit odpovídající proměnné prostředí.

|Výzva|Proměnná prostředí|Poznámky|
|---|---|---|
|Zadejte registr zdrojového kontejneru – stisknutím klávesy ENTER můžete použít `mcr.microsoft.com`|SOURCE_DOCKER_REGISTRY|Obvykle byste tyto image vyčetli z Microsoft Container Registry, ale pokud se účastníte privátní verze Preview s jiným registrem, můžete použít informace, které jste získali jako součást programu Preview.|
|Poskytněte úložiště registru zdrojového kontejneru – stiskněte klávesu ENTER pro použití `arcdata` :|SOURCE_DOCKER_REPOSITORY|Pokud se stahujete z Microsoft Container Registry, úložiště bude `arcdata` .|
|Zadejte uživatelské jméno pro kontejner zdrojového kontejneru – stiskněte klávesu ENTER pro použití None:|SOURCE_DOCKER_USERNAME|Zadejte hodnotu jenom v případě, že nasazujete image kontejneru ze zdroje, který vyžaduje přihlášení.  Microsoft Container Registry nevyžaduje přihlášení.|
|Zadejte heslo ke zdrojovému registru kontejneru – stiskněte klávesu ENTER pro použití None:|SOURCE_DOCKER_PASSWORD|Zadejte hodnotu jenom v případě, že nasazujete image kontejneru ze zdroje, který vyžaduje přihlášení.  Microsoft Container Registry nevyžaduje přihlášení. Toto je okno s maskováním hesla.  Heslo se nezobrazí, pokud ho zadáte nebo vložíte do.|
|Zadejte značku image kontejneru pro obrázky na zdrojovém stisku klávesy ENTER pro použití `<current monthly release tag>` :|SOURCE_DOCKER_TAG|Výchozí název značky se aktualizuje měsíčně, aby odrážel měsíc a rok aktuální verze v Microsoft Container Registry.|
|Zadejte název DNS nebo IP adresu cílového registru kontejneru:|TARGET_DOCKER_REGISTRY|Název DNS cílového registru nebo IP adresa.  Jedná se o registr, _do_ kterého se budou obrázky přidávají.|
|Zadejte cílové úložiště registru kontejneru:|TARGET_DOCKER_REPOSITORY|Úložiště v cílovém registru, do kterého se mají vložit image|
|Zadejte uživatelské jméno pro cílový registr kontejneru – stiskněte klávesu ENTER pro použití None:|TARGET_DOCKER_USERNAME|Uživatelské jméno, které se používá k přihlášení do cílového registru kontejneru.|
|Zadejte heslo pro cílový registr kontejneru – stiskněte klávesu ENTER pro použití None:|TARGET_DOCKER_PASSWORD|Heslo, které se používá k přihlášení do cílového registru kontejneru. Toto je okno s maskováním hesla.  Heslo se nezobrazí, pokud ho zadáte nebo vložíte do.|
|Zadejte značku image kontejneru pro obrázky v cíli:|TARGET_DOCKER_TAG|Obvykle byste použili stejnou značku jako zdroj, aby nedošlo k nejasnostem.|