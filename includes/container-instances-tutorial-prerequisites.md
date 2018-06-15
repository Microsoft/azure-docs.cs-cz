---
title: zahrnout soubor
description: zahrnout soubor
services: container-instances
author: mmacy
ms.service: container-instances
ms.topic: include
ms.date: 03/20/2018
ms.author: marsma
ms.custom: include file
ms.openlocfilehash: 95a8cf4eca7969631d069a31c9d08ba52c8b02b4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30193478"
---
Abyste mohli absolvovat tento kurz, musíte splňovat následující požadavky:

**Azure CLI:** Na svém místním počítači musíte mít nainstalované Azure CLI verze 2.0.29 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0][azure-cli-install].

**Docker:** V tomto kurzu se předpokládá základní znalost klíčových konceptů Dockeru, jako jsou kontejnery, image kontejnerů a základní příkazy `docker`. Základní informace o Dockeru a kontejnerech najdete v článku [Docker Overview][docker-get-started] (Přehled Dockeru).

**Modul Docker:** K dokončení tohoto kurzu potřebuje místně nainstalovaný modul Docker. Docker poskytuje balíčky, které nakonfigurují prostředí Dockeru v systému [macOS][docker-mac], [Windows][docker-windows] a [Linux][docker-linux].

> [!IMPORTANT]
> Vzhledem k tomu, že Azure Cloud Shell neobsahuje démona Dockeru, k dokončení tohoto kurzu *musíte* nainstalovat Azure CLI i modul Docker na svůj *místní počítač*. Pro účely tohoto kurzu není možné použít Azure Cloud Shell.

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli