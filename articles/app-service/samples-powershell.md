---
title: Ukázky Azure Powershellu – App Service | Dokumentace Microsoftu
description: Ukázky Azure Powershellu – App Service
services: app-service
documentationcenter: app-service
author: syntaxc4
manager: erikre
editor: ggailey777
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.service: app-service
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: app-service
ms.date: 03/08/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 69fd27785e5fc16a79fc23728b6d1e50a0a7b834
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53632853"
---
# <a name="powershell-samples-for-azure-app-service"></a>Ukázky Powershellu pro službu Azure App Service

Následující tabulka obsahuje odkazy na skripty prostředí PowerShell vytvořené pomocí Azure Powershellu.

| | |
|-|-|
|**Vytvořit aplikaci**||
| [Vytvoření aplikace s nasazením z Githubu](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří aplikaci služby App Service, která si přetáhne kód z Githubu. |
| [Vytvoření aplikace s průběžným nasazováním z Githubu](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří aplikaci služby App Service, který se průběžně nasazuje kódu z Githubu. |
| [Vytvoření nové aplikace a nasazení kódu s využitím protokolu FTP](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří App Service aplikaci a nahrajte soubory z místního adresáře pomocí FTP. |
| [Vytvoření nové aplikace a nasazení kódu z místního úložiště Git](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci služby App Service a nakonfiguruje kód push z místního úložiště Git. |
| [Vytvoření nové aplikace a nasazení kódu do přípravného prostředí](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci služby App Service se slot nasazení pro pracovní změny kódu. |
|**Konfigurace aplikace**||
| [Mapování vlastní domény na aplikaci](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří aplikaci služby App Service a mapování vlastního názvu domény do něj. |
| [Vazba vlastního certifikátu SSL na aplikaci](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří aplikaci služby App Service a k ní vytvoří vazbu certifikátu SSL vlastního názvu domény. |
|**Škálování aplikace**||
| [Ruční škálování aplikace](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci služby App Service a škálovat po celém 2 instance. |
| [Škálování aplikace po celém světě s využitím architektury vysokou dostupnost](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří dvě aplikace služby App Service ve dvou různých geografických oblastech a zpřístupní je prostřednictvím jednoho koncového bodu pomocí Azure Traffic Manageru. |
|**Připojení aplikace k prostředkům**||
| [Připojení aplikace k SQL Database](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří aplikaci služby App Service a databázi SQL a pak přidá do nastavení aplikace připojovací řetězec databáze. |
| [Připojení aplikace k účtu úložiště](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří aplikaci služby App Service a účet úložiště a pak přidá do nastavení aplikace připojovací řetězec úložiště. |
|**Zálohování a obnovení aplikace**||
| [Zálohování aplikace](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci služby App Service a pro ni vytvoří jednorázovou zálohu. |
| [Vytvoření naplánovaného zálohování pro aplikace](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci služby App Service a pro ni vytvoří naplánované zálohování. |
| [Odstraňování zálohy pro aplikaci](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Odstraní existující zálohu aplikace. |
| [Obnovení aplikace ze zálohy](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obnoví aplikace ze zálohy předchozí dokončená. |
| [Obnovit zálohu napříč předplatnými](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obnoví webovou aplikaci ze zálohy v jiném předplatném. |
|**Monitorování aplikace**||
| [Monitorování aplikace pomocí protokolů webového serveru](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci služby App Service, protokolování, povolí a stáhne protokoly do místního počítače. |
| | |
