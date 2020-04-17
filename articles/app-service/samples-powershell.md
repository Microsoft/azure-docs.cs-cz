---
title: Ukázky pro PowerShell
description: Najděte ukázky Prostředí Azure PowerShell pro některé běžné scénáře služby App Service. Přečtěte si, jak automatizovat úlohy nasazení nebo správy služby App Service.
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.topic: sample
ms.date: 03/08/2017
ms.custom: mvc
ms.openlocfilehash: f289bd453f2387282402394c807fe9700151f221
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532421"
---
# <a name="powershell-samples-for-azure-app-service"></a>Ukázky Prostředí PowerShell pro službu Azure App Service

Následující tabulka obsahuje odkazy na skripty PowerShellu vytvořené pomocí Azure PowerShellu.

| | |
|-|-|
|**Vytvoření aplikace**||
| [Vytvoření aplikace s nasazením z GitHubu](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří aplikaci služby App Service, která stahuje kód z GitHubu. |
| [Vytvoření aplikace s nepřetržitým nasazením z GitHubu](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří aplikaci služby App Service, která nepřetržitě nasazuje kód z GitHubu. |
| [Vytvoření aplikace a nasazení kódu pomocí FTP](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci služby App Service a nahraje soubory z místního adresáře pomocí protokolu FTP. |
| [Vytvoření aplikace a nasazení kódu z místního úložiště Git](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci služby App Service a nakonfiguruje nabízenou nabídku kódu z místního úložiště Git. |
| [Vytvoření aplikace a nasazení kódu do pracovního prostředí](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci služby App Service s slotem pro nasazení pro změny pracovního kódu. |
|**Konfigurace aplikace**||
| [Mapování vlastní domény na aplikaci](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří aplikaci Služby aplikací a namapuje na ní vlastní název domény. |
| [Svázání vlastního certifikátu TLS/SSL s aplikací](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří aplikaci služby App Service a sváže s ní certifikát TLS/SSL vlastního názvu domény. |
|**Škálování aplikace**||
| [Ruční škálování aplikace](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci služby App Service a škáluje ji napříč 2 instancemi. |
| [Škálování aplikace po celém světě s architekturou s vysokou dostupností](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří dvě aplikace služby App Service ve dvou různých zeměpisných oblastech a zpřístupní je prostřednictvím jednoho koncového bodu pomocí Azure Traffic Manageru. |
|**Připojení aplikace k prostředku**||
| [Připojení aplikace k databázi SQL](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří aplikaci služby App Service a databázi SQL a pak přidá připojovací řetězec databáze do nastavení aplikace. |
| [Připojení aplikace k účtu úložiště](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří aplikaci služby App Service a účet úložiště a pak přidá připojovací řetězec úložiště do nastavení aplikace. |
|**Zálohování a obnovení aplikace**||
| [Zálohování aplikace](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci služby App Service a vytvoří pro něj jednorázovou zálohu. |
| [Vytvoření naplánovanézálohy pro aplikaci](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci služby App Service a vytvoří pro něj naplánovanou zálohu. |
| [Odstranění zálohy pro aplikaci](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Odstraní existující zálohu aplikace. |
| [Obnovení aplikace ze zálohy](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obnoví aplikaci z dříve dokončené zálohy. |
| [Obnovení zálohy mezi předplatnými](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obnoví webovou aplikaci ze zálohy v jiném předplatném. |
|**Monitorování aplikace**||
| [Sledování aplikace pomocí protokolů webového serveru](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci služby App Service, povolí protokolování a stáhne protokoly do místního počítače. |
| | |
