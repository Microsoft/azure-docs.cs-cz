---
title: Ukázky rozhraní příkazového řádku Azure - služby App Service | Microsoft Docs
description: Ukázky rozhraní příkazového řádku Azure - služby App Service
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
ms.openlocfilehash: fdc5e03350783fb8c3e30b6c9a40af45a5925ba8
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2017
ms.locfileid: "26658360"
---
# <a name="azure-cli-samples"></a>Ukázky rozhraní příkazového řádku Azure

Následující tabulka obsahuje odkazy na bash skripty, které jsou vytvořené pomocí rozhraní příkazového řádku Azure.

| | |
|-|-|
|**Vytvoření aplikace**||
| [Vytvoření webové aplikace a nasazení soubory s FTP](./scripts/app-service-cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Webové aplikace Azure vytvoří a nasadí soubor se pomocí protokolu FTP. |
| [Vytvoření webové aplikace a nasazení kódu z Githubu](./scripts/app-service-cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Webové aplikace Azure vytvoří a nasadí kód z veřejného úložiště GitHub. |
| [Vytvoření webové aplikace s průběžným nasazováním z Githubu](./scripts/app-service-cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří webové aplikace Azure s nepřetržitou publikování z úložiště Githubu, které vlastníte. |
| [Vytvoření webové aplikace a nasazení kódu z místního úložiště Git](./scripts/app-service-cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří webové aplikace Azure a nakonfiguruje nabízené kód z místního úložiště Git. |
| [Vytvoření webové aplikace a nasazení kódu do přípravného prostředí](./scripts/app-service-cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří se nasazovací slot pro přípravu změny kódu webové aplikace Azure. |
| [Vytvoření webové aplikace ASP.NET Core v kontejneru Docker](./scripts/app-service-cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří webové aplikace Azure v systému Linux a načte bitovou kopii Docker z úložiště Docker Hub. |
|**Konfigurace aplikace**||
| [Mapování vlastní domény na webovou aplikaci](./scripts/app-service-cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří webové aplikace Azure a mapuje vlastního názvu domény do ní. |
| [Vlastní certifikát SSL vazbu do webové aplikace](./scripts/app-service-cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří webové aplikace Azure a vytvoří vazbu certifikátu SSL vlastního názvu domény. |
|**Škálování aplikace**||
| [Ruční škálování webové aplikace](./scripts/app-service-cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří webové aplikace Azure a škáluje napříč 2 instance. |
| [Škálování webové aplikace po celém světě s využitím architektury s vysokou dostupností](./scripts/app-service-cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří dvě službě Azure web apps ve dvou různých zeměpisných oblastech a jsou pak dostupné přes jeden koncový bod pomocí Azure Traffic Manager. |
|**Připojení aplikace k prostředkům**||
| [Webovou aplikaci připojit k databázi SQL](./scripts/app-service-cli-app-service-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří webové aplikace Azure a SQL database a potom přidá připojovací řetězec databáze nastavení aplikace. |
| [Připojení webové aplikace k účtu úložiště](./scripts/app-service-cli-app-service-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří webové aplikace Azure a účet úložiště a potom přidá připojovacího řetězce úložiště nastavení aplikace. |
| [Připojení webové aplikace do mezipaměti redis](./scripts/app-service-cli-app-service-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří webové aplikace Azure a mezipaměti redis a potom přidá podrobnosti připojení redis nastavení aplikace.) |
| [Webovou aplikaci připojit k databázi systému Cosmos](./scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří webové aplikace Azure a Cosmos DB a potom přidá podrobnosti připojení Cosmos DB nastavení aplikace. |
|**Zálohování a obnovení aplikace**||
| [Zálohování webové aplikace](./scripts/app-service-cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří webové aplikace Azure a vytvoří pro ni jednorázové zálohy. |
| [Vytvoření naplánovaného zálohování pro webovou aplikaci](./scripts/app-service-cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří webové aplikace Azure a vytvoří pro ni plánované zálohování. |
| [Obnoví ze zálohy, webové aplikace](./scripts/app-service-cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Webové aplikace Azure obnoví ze zálohy. |
|**Monitorování aplikace**||
| [Monitorování webové aplikace pomocí protokolů webového serveru](./scripts/app-service-cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří webové aplikace Azure, povolí protokolování pro něj a stáhne protokoly do místního počítače. |
| | |