---
title: Ukázky PowerShellu
description: V některých běžných scénářích App Service najdete ukázky Azure PowerShell. Naučte se automatizovat nasazení App Service nebo úlohy správy.
tags: azure-service-management
ms.assetid: b48d1137-8c04-46e0-b430-101e07d7e470
ms.topic: sample
ms.date: 03/08/2017
ms.custom: mvc
ms.openlocfilehash: f32db2a4dda0ceb69644b64a5c76155b448c5a28
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687034"
---
# <a name="powershell-samples-for-azure-app-service"></a>Ukázky PowerShellu pro Azure App Service

Následující tabulka obsahuje odkazy na skripty PowerShellu sestavené pomocí Azure PowerShell.

| | |
|-|-|
|**Vytvořit aplikaci**||
| [Vytvoření aplikace s nasazením z GitHubu](./scripts/powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří aplikaci App Service, která načte kód z GitHubu. |
| [Vytvoření aplikace s průběžným nasazováním z GitHubu](./scripts/powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří aplikaci App Service, která průběžně nasazuje kód z GitHubu. |
| [Vytvoření aplikace a nasazení kódu pomocí FTP](./scripts/powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci App Service a nahrajte soubory z místního adresáře pomocí FTP. |
| [Vytvoření aplikace a nasazení kódu z místního úložiště Git](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci App Service a nakonfiguruje vložení kódu z místního úložiště Git. |
| [Vytvoření aplikace a nasazení kódu do přípravného prostředí](./scripts/powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci App Service s slotem nasazení pro změny kódu přípravy. |
|**Konfigurace aplikace**||
| [Mapování vlastní domény na aplikaci](./scripts/powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří aplikaci App Service a namapuje na ni vlastní název domény. |
| [Vazba vlastního certifikátu SSL k aplikaci](./scripts/powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří aplikaci App Service a váže certifikát SSL vlastního názvu domény. |
|**Škálování aplikace**||
| [Ruční škálování aplikace](./scripts/powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci App Service a škáluje ji napříč 2 instancemi. |
| [Škálování aplikace po celém světě s využitím architektury s vysokou dostupností](./scripts/powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří dvě aplikace App Service ve dvou různých geografických oblastech a zpřístupňuje je prostřednictvím jediného koncového bodu pomocí Azure Traffic Manager. |
|**Připojení aplikace k prostředkům**||
| [Připojení aplikace k SQL Database](./scripts/powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří aplikaci App Service a databázi SQL a pak přidá připojovací řetězec databáze do nastavení aplikace. |
| [Připojení aplikace k účtu úložiště](./scripts/powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří aplikaci App Service a účet úložiště a pak přidá připojovací řetězec úložiště do nastavení aplikace. |
|**Zálohování a obnovení aplikace**||
| [Zálohování aplikace](./scripts/powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci App Service a vytvoří pro ni jednorázovou zálohu. |
| [Vytvoření naplánovaného zálohování aplikace](./scripts/powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci App Service a vytvoří pro ni naplánované zálohování. |
| [Odstranění zálohy aplikace](./scripts/powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Odstraní existující zálohu aplikace. |
| [Obnovení aplikace ze zálohy](./scripts/powershell-backup-restore.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obnoví aplikaci z předchozího dokončeného zálohování. |
| [Obnovení zálohy mezi předplatnými](./scripts/powershell-backup-restore-diff-sub.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Obnoví webovou aplikaci ze zálohy v jiném předplatném. |
|**Monitorovat aplikaci**||
| [Monitorování aplikace pomocí protokolů webového serveru](./scripts/powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří aplikaci App Service, povolí její protokolování a stáhne protokoly do místního počítače. |
| | |
