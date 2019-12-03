---
title: Ukázky rozhraní příkazového řádku
description: V některých běžných scénářích App Service najdete ukázky Azure CLI. Naučte se automatizovat nasazení App Service nebo úlohy správy.
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.topic: sample
ms.date: 12/12/2017
ms.custom: mvc
ms.openlocfilehash: 06f03e0005c4f98370bb1f6430fff506f9145d5a
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688513"
---
# <a name="cli-samples-for-azure-app-service"></a>Ukázky rozhraní příkazového řádku pro Azure App Service

Následující tabulka obsahuje odkazy na skripty Bash vytvořené pomocí Azure CLI.

| | |
|-|-|
|**Vytvořit aplikaci**||
| [Vytvoření aplikace a nasazení souborů s využitím protokolu FTP](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci App Service a nasadí do ní soubor pomocí protokolu FTP. |
| [Vytvoření aplikace a nasazení kódu z GitHubu](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci App Service a nasadí kód z veřejného úložiště GitHub. |
| [Vytvoření aplikace s průběžným nasazováním z GitHubu](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci App Service s průběžným publikováním z úložiště GitHub, které vlastníte. |
| [Vytvoření aplikace a nasazení kódu z místního úložiště Git](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci App Service a nakonfiguruje vložení kódu z místního úložiště Git. |
| [Vytvoření aplikace a nasazení kódu do přípravného prostředí](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci App Service s slotem nasazení pro změny kódu přípravy. |
| [Vytvoření aplikace ASP.NET Core v kontejneru Docker](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci App Service v systému Linux a načte image Docker z Docker Hub. |
|**Konfigurace aplikace**||
| [Mapování vlastní domény na aplikaci](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci App Service a namapuje na ni vlastní název domény. |
| [Vazba vlastního certifikátu SSL k aplikaci](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci App Service a váže certifikát SSL vlastního názvu domény. |
|**Škálování aplikace**||
| [Ruční škálování aplikace](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci App Service a škáluje ji napříč 2 instancemi. |
| [Škálování aplikace po celém světě s využitím architektury s vysokou dostupností](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří dvě aplikace App Service ve dvou různých geografických oblastech a zpřístupňuje je prostřednictvím jediného koncového bodu pomocí Azure Traffic Manager. |
|**Připojení aplikace k prostředkům**||
| [Připojení aplikace k SQL Database](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci App Service a databázi SQL a pak přidá připojovací řetězec databáze do nastavení aplikace. |
| [Připojení aplikace k účtu úložiště](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci App Service a účet úložiště a pak přidá připojovací řetězec úložiště do nastavení aplikace. |
| [Připojení aplikace k mezipaměti Azure pro Redis](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci App Service a mezipaměť Azure pro Redis a pak přidá podrobnosti připojení Redis do nastavení aplikace.) |
| [Připojení aplikace k Cosmos DB](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci App Service a Cosmos DB a potom do nastavení aplikace přidá podrobnosti o Cosmos DB připojení. |
|**Zálohování a obnovení aplikace**||
| [Zálohování aplikace](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci App Service a vytvoří pro ni jednorázovou zálohu. |
| [Vytvoření naplánovaného zálohování aplikace](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci App Service a vytvoří pro ni naplánované zálohování. |
| [Obnoví aplikaci ze zálohy.](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Obnoví aplikaci App Service ze zálohy. |
|**Monitorovat aplikaci**||
| [Monitorování aplikace pomocí protokolů webového serveru](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci App Service, povolí její protokolování a stáhne protokoly do místního počítače. |
| | |