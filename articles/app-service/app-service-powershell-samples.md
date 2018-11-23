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
ms.openlocfilehash: 211284f4eff112b9aebec7fa1a031f292a4a92f4
ms.sourcegitcommit: beb4fa5b36e1529408829603f3844e433bea46fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2018
ms.locfileid: "52290735"
---
# <a name="azure-powershell-samples"></a>Ukázky Azure PowerShellu

Následující tabulka obsahuje odkazy na skripty prostředí PowerShell vytvořené pomocí Azure Powershellu.

| | |
|-|-|
|**Vytvořit aplikaci**||
| [Vytvoření aplikace s nasazením z Githubu](./scripts/app-service-powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří webovou aplikaci Azure, která si přetáhne kód z Githubu. |
| [Vytvoření aplikace s průběžným nasazováním z Githubu](./scripts/app-service-powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří webovou aplikaci Azure, který se průběžně nasazuje kódu z Githubu. |
| [Vytvoření nové aplikace a nasazení kódu s využitím protokolu FTP](./scripts/app-service-powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří Azure webové aplikace a jejich nahrání souborů z místního adresáře pomocí FTP. |
| [Vytvoření nové aplikace a nasazení kódu z místního úložiště Git](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří webovou aplikaci Azure a nakonfiguruje kód push z místního úložiště Git. |
| [Vytvoření nové aplikace a nasazení kódu do přípravného prostředí](./scripts/app-service-powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří webovou aplikaci Azure se slot nasazení pro pracovní změny kódu. |
|**Konfigurace aplikace**||
| [Mapování vlastní domény na aplikaci](./scripts/app-service-powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří webovou aplikaci Azure a mapování vlastního názvu domény do něj. |
| [Vazba vlastního certifikátu SSL na aplikaci](./scripts/app-service-powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří webovou aplikaci Azure a k ní vytvoří vazbu certifikátu SSL vlastního názvu domény. |
|**Škálování aplikace**||
| [Ruční škálování aplikace](./scripts/app-service-powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří webovou aplikaci Azure a škálovat po celém 2 instance. |
| [Škálování aplikace po celém světě s využitím architektury vysokou dostupnost](./scripts/app-service-powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří dvě Azure web apps ve dvou různých geografických oblastech a zpřístupní je prostřednictvím jednoho koncového bodu pomocí Azure Traffic Manageru. |
|**Připojení aplikace k prostředkům**||
| [Připojení aplikace k SQL Database](./scripts/app-service-powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří webovou aplikaci Azure a SQL database a pak přidá do nastavení aplikace připojovací řetězec databáze. |
| [Připojení aplikace k účtu úložiště](./scripts/app-service-powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří webovou aplikaci Azure a účet úložiště a pak přidá do nastavení aplikace připojovací řetězec úložiště. |
|**Zálohování a obnovení aplikace**||
| [Zálohování aplikace](./scripts/app-service-powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří webovou aplikaci Azure a pro ni vytvoří jednorázovou zálohu. |
| [Vytvoření naplánovaného zálohování pro aplikace](./scripts/app-service-powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří webovou aplikaci Azure a pro ni vytvoří naplánované zálohování. |
| [Odstraňování zálohy pro aplikaci](./scripts/app-service-powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Odstraní existující zálohu aplikace. |
| [Obnovení aplikace ze zálohy](./scripts/app-service-powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obnoví aplikace ze zálohy předchozí dokončená. |
| [Obnovit zálohu napříč předplatnými](./scripts/app-service-powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obnoví webovou aplikaci ze zálohy v jiném předplatném. |
|**Monitorování aplikace**||
| [Monitorování aplikace pomocí protokolů webového serveru](./scripts/app-service-powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří webovou aplikaci Azure, umožňuje protokolování a stáhne protokoly do místního počítače. |
| | |
