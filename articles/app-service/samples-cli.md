---
title: Ukázky rozhraní příkazového řádku
description: Najděte ukázky azure cli pro některé běžné scénáře služby App Service. Přečtěte si, jak automatizovat úlohy nasazení nebo správy služby App Service.
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.topic: sample
ms.date: 12/12/2017
ms.custom: mvc
ms.openlocfilehash: 0b3acb1b421962cde7d90398f42bdfeefda578e3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74971498"
---
# <a name="cli-samples-for-azure-app-service"></a>Ukázky příkazového příkazového příkazu pro službu Azure App Service

Následující tabulka obsahuje odkazy na skripty Bash vytvořené pomocí Azure CLI.

| | |
|-|-|
|**Vytvoření aplikace**||
| [Vytvoření aplikace a nasazení souborů pomocí protokolu FTP](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci služby App Service a nasadí do ní soubor pomocí FTP. |
| [Vytvoření aplikace a nasazení kódu z GitHubu](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci služby App Service a nasadí kód z veřejného úložiště GitHub. |
| [Vytvoření aplikace s nepřetržitým nasazením z GitHubu](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci služby App Service s nepřetržitým publikováním z úložiště GitHub, které vlastníte. |
| [Vytvoření aplikace a nasazení kódu z místního úložiště Git](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci služby App Service a nakonfiguruje nabízenou nabídku kódu z místního úložiště Git. |
| [Vytvoření aplikace a nasazení kódu do pracovního prostředí](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci služby App Service s slotem pro nasazení pro změny pracovního kódu. |
| [Vytvoření aplikace ASP.NET Core v kontejneru Dockeru](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci služby App Service na Linuxu a načte image Dockeru z Docker Hubu. |
|**Konfigurace aplikace**||
| [Mapování vlastní domény na aplikaci](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci Služby aplikací a namapuje na ní vlastní název domény. |
| [Vytvoření vytvoření vlastního certifikátu SSL s aplikací](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci služby App Service a sváže s ní certifikát SSL vlastního názvu domény. |
|**Škálování aplikace**||
| [Ruční škálování aplikace](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci služby App Service a škáluje ji napříč 2 instancemi. |
| [Škálování aplikace po celém světě s architekturou s vysokou dostupností](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří dvě aplikace služby App Service ve dvou různých zeměpisných oblastech a zpřístupní je prostřednictvím jednoho koncového bodu pomocí Azure Traffic Manageru. |
|**Ochrana aplikace**||
| [Integrace s aplikační bránou Azure](./scripts/cli-integrate-app-service-with-application-gateway.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci služby App Service a integruje ji s aplikační bránou pomocí koncového bodu služby a omezení přístupu. |
|**Připojení aplikace k prostředku**||
| [Připojení aplikace k databázi SQL](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci služby App Service a databázi SQL a pak přidá připojovací řetězec databáze do nastavení aplikace. |
| [Připojení aplikace k účtu úložiště](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| Vytvoří aplikaci služby App Service a účet úložiště a pak přidá připojovací řetězec úložiště do nastavení aplikace. |
| [Připojení aplikace k Azure Cache pro Redis](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci služby App Service a azure cache pro Redis a pak do nastavení aplikace přidá podrobnosti o připojení redis.) |
| [Připojení aplikace ke službě Cosmos DB](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci služby App Service a Cosmos DB a pak do nastavení aplikace přidá podrobnosti o připojení Cosmos DB. |
|**Zálohování a obnovení aplikace**||
| [Zálohování aplikace](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci služby App Service a vytvoří pro něj jednorázovou zálohu. |
| [Vytvoření naplánovanézálohy pro aplikaci](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci služby App Service a vytvoří pro něj naplánovanou zálohu. |
| [Obnoví aplikaci ze zálohy.](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | Obnoví aplikaci služby App Service ze zálohy. |
|**Monitorování aplikace**||
| [Sledování aplikace pomocí protokolů webového serveru](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | Vytvoří aplikaci služby App Service, povolí protokolování a stáhne protokoly do místního počítače. |
| | |