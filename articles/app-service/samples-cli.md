---
title: Ukázky Azure CLI – App Service | Dokumentace Microsoftu
description: Ukázky Azure CLI – App Service
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 12/12/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: fe5649951b1b19ce52c13648f897f4a83e1f761b
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53628370"
---
# <a name="cli-samples-for-azure-app-service"></a>Ukázky rozhraní příkazového řádku pro službu Azure App Service

Následující tabulka obsahuje odkazy na skripty Bash vytvořené pomocí Azure CLI.

| | |
|-|-|
|**Vytvořit aplikaci**||
| [Vytvoření nové aplikace a nasazení souborů s využitím protokolu FTP](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci služby App Service a nasadí soubor pomocí FTP. |
| [Vytvoření nové aplikace a nasazení kódu z Githubu](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci služby App Service a nasadí kód z veřejného úložiště GitHub. |
| [Vytvoření aplikace s průběžným nasazováním z Githubu](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci služby App Service s průběžné publikování z úložiště GitHub, které vlastníte. |
| [Vytvoření nové aplikace a nasazení kódu z místního úložiště Git](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci služby App Service a nakonfiguruje kód push z místního úložiště Git. |
| [Vytvoření nové aplikace a nasazení kódu do přípravného prostředí](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci služby App Service se slot nasazení pro pracovní změny kódu. |
| [Vytvoření aplikace ASP.NET Core v kontejneru Dockeru](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci služby App Service v Linuxu a načte image Dockeru z Docker Hubu. |
|**Konfigurace aplikace**||
| [Mapování vlastní domény na aplikaci](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci služby App Service a mapování vlastního názvu domény do něj. |
| [Vazba vlastního certifikátu SSL na aplikaci](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci služby App Service a k ní vytvoří vazbu certifikátu SSL vlastního názvu domény. |
|**Škálování aplikace**||
| [Ruční škálování aplikace](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci služby App Service a škálovat po celém 2 instance. |
| [Škálování aplikace po celém světě s využitím architektury vysokou dostupnost](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří dvě aplikace služby App Service ve dvou různých geografických oblastech a zpřístupní je prostřednictvím jednoho koncového bodu pomocí Azure Traffic Manageru. |
|**Připojení aplikace k prostředkům**||
| [Připojení aplikace k SQL Database](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci služby App Service a databázi SQL a pak přidá do nastavení aplikace připojovací řetězec databáze. |
| [Připojení aplikace k účtu úložiště](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci služby App Service a účet úložiště a pak přidá do nastavení aplikace připojovací řetězec úložiště. |
| [Připojení aplikace k Azure Cache pro Redis](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci služby App Service a Azure Cache pro Redis a pak přidá do nastavení aplikace podrobnosti připojení redis.) |
| [Připojení aplikace ke službě Cosmos DB](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci služby App Service a Cosmos DB a pak přidá do nastavení aplikace podrobnosti připojení Cosmos DB. |
|**Zálohování a obnovení aplikace**||
| [Zálohování aplikace](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci služby App Service a pro ni vytvoří jednorázovou zálohu. |
| [Vytvoření naplánovaného zálohování pro aplikace](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci služby App Service a pro ni vytvoří naplánované zálohování. |
| [Obnovení aplikace ze zálohy](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Obnoví aplikace služby App Service ze zálohy. |
|**Monitorování aplikace**||
| [Monitorování aplikace pomocí protokolů webového serveru](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci služby App Service, protokolování, povolí a stáhne protokoly do místního počítače. |
| | |