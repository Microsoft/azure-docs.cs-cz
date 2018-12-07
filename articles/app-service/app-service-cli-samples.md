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
ms.openlocfilehash: b57ec449c597984f8f4a53035be32047ec755274
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53015343"
---
# <a name="azure-cli-samples"></a>Ukázky Azure CLI

Následující tabulka obsahuje odkazy na skripty Bash vytvořené pomocí Azure CLI.

| | |
|-|-|
|**Vytvořit aplikaci**||
| [Vytvoření webové aplikace a nasazení souborů s využitím protokolu FTP](./scripts/app-service-cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří webovou aplikaci Azure a nasadí soubor pomocí FTP. |
| [Vytvoření webové aplikace a nasazení kódu z Githubu](./scripts/app-service-cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří webovou aplikaci Azure a nasadí kód z veřejného úložiště GitHub. |
| [Vytvoření webové aplikace s průběžným nasazováním z Githubu](./scripts/app-service-cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří webovou aplikaci Azure s průběžné publikování z úložiště GitHub, které vlastníte. |
| [Vytvoření webové aplikace a nasazení kódu z místního úložiště Git](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří webovou aplikaci Azure a nakonfiguruje kód push z místního úložiště Git. |
| [Vytvoření webové aplikace a nasazení kódu do přípravného prostředí](./scripts/app-service-cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří webovou aplikaci Azure se slot nasazení pro pracovní změny kódu. |
| [Vytvoření webové aplikace ASP.NET Core v kontejneru Docker](./scripts/app-service-cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří webovou aplikaci Azure v Linuxu a načte image Dockeru z Docker Hubu. |
|**Konfigurace aplikace**||
| [Mapování vlastní domény na webovou aplikaci](./scripts/app-service-cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří webovou aplikaci Azure a mapování vlastního názvu domény do něj. |
| [Vazba vlastního certifikátu SSL do webové aplikace](./scripts/app-service-cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří webovou aplikaci Azure a k ní vytvoří vazbu certifikátu SSL vlastního názvu domény. |
|**Škálování aplikace**||
| [Ruční škálování webové aplikace](./scripts/app-service-cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří webovou aplikaci Azure a škálovat po celém 2 instance. |
| [Škálování webové aplikace po celém světě s využitím architektury s vysokou dostupností](./scripts/app-service-cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří dvě Azure web apps ve dvou různých geografických oblastech a zpřístupní je prostřednictvím jednoho koncového bodu pomocí Azure Traffic Manageru. |
|**Připojení aplikace k prostředkům**||
| [Připojení webové aplikace do služby SQL Database](./scripts/app-service-cli-app-service-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří webovou aplikaci Azure a SQL database a pak přidá do nastavení aplikace připojovací řetězec databáze. |
| [Připojení webové aplikace k účtu úložiště](./scripts/app-service-cli-app-service-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří webovou aplikaci Azure a účet úložiště a pak přidá do nastavení aplikace připojovací řetězec úložiště. |
| [Připojení webové aplikace do služby Azure Cache pro Redis](./scripts/app-service-cli-app-service-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří webovou aplikaci Azure a Azure Cache pro Redis a pak přidá do nastavení aplikace podrobnosti připojení redis.) |
| [Připojení webové aplikace ke službě Cosmos DB](./scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří webovou aplikaci Azure a Cosmos DB a pak přidá do nastavení aplikace podrobnosti připojení Cosmos DB. |
|**Zálohování a obnovení aplikace**||
| [Zálohování webové aplikace](./scripts/app-service-cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří webovou aplikaci Azure a pro ni vytvoří jednorázovou zálohu. |
| [Vytvoření naplánovaného zálohování webové aplikace](./scripts/app-service-cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří webovou aplikaci Azure a pro ni vytvoří naplánované zálohování. |
| [Obnoví webovou aplikaci ze zálohy](./scripts/app-service-cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Obnoví webovou aplikaci Azure ze zálohy. |
|**Monitorování aplikace**||
| [Monitorování webové aplikace pomocí protokolů webového serveru](./scripts/app-service-cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří webovou aplikaci Azure, umožňuje protokolování a stáhne protokoly do místního počítače. |
| | |