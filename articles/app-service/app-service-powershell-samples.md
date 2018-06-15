---
title: Ukázky Azure PowerShell - služby App Service | Microsoft Docs
description: Ukázky Azure PowerShell - služby App Service
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
ms.openlocfilehash: ba2bd2b185c395e54f2f085317a424a2aa1b4421
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2018
ms.locfileid: "27713412"
---
# <a name="azure-powershell-samples"></a>Ukázky Azure PowerShell

Následující tabulka obsahuje odkazy na skriptů prostředí PowerShell, které jsou vytvořené pomocí Azure PowerShell.

| | |
|-|-|
|**Vytvoření aplikace**||
| [Vytvoření webové aplikace s nasazením z Githubu](./scripts/app-service-powershell-deploy-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří Azure webové aplikace, který žádá o kód z Githubu. |
| [Vytvoření webové aplikace s průběžným nasazováním z Githubu](./scripts/app-service-powershell-continuous-deployment-github.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří Azure webové aplikace, které průběžně nasadí kód z Githubu. |
| [Vytvoření webové aplikace a nasazení kódu do serveru FTP](./scripts/app-service-powershell-deploy-ftp.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří službě Azure web app a nahrávání souborů z místního adresáře pomocí protokolu FTP. |
| [Vytvoření webové aplikace a nasazení kódu z místního úložiště Git](./scripts/app-service-powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří webové aplikace Azure a nakonfiguruje nabízené kód z místního úložiště Git. |
| [Vytvoření webové aplikace a nasazení kódu do přípravného prostředí](./scripts/app-service-powershell-deploy-staging-environment.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří se nasazovací slot pro přípravu změny kódu webové aplikace Azure. |
|**Konfigurace aplikace**||
| [Mapování vlastní domény na webovou aplikaci](./scripts/app-service-powershell-configure-custom-domain.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří webové aplikace Azure a mapuje vlastního názvu domény do ní. |
| [Vlastní certifikát SSL vazbu do webové aplikace](./scripts/app-service-powershell-configure-ssl-certificate.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří webové aplikace Azure a vytvoří vazbu certifikátu SSL vlastního názvu domény. |
|**Škálování aplikace**||
| [Ruční škálování webové aplikace](./scripts/app-service-powershell-scale-manual.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří webové aplikace Azure a škáluje napříč 2 instance. |
| [Škálování webové aplikace po celém světě s využitím architektury s vysokou dostupností](./scripts/app-service-powershell-scale-high-availability.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří dvě službě Azure web apps ve dvou různých zeměpisných oblastech a jsou pak dostupné přes jeden koncový bod pomocí Azure Traffic Manager. |
|**Připojení aplikace k prostředkům**||
| [Webovou aplikaci připojit k databázi SQL](./scripts/app-service-powershell-connect-to-sql.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří webové aplikace Azure a SQL database a potom přidá připojovací řetězec databáze nastavení aplikace. |
| [Připojení webové aplikace k účtu úložiště](./scripts/app-service-powershell-connect-to-storage.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Vytvoří webové aplikace Azure a účet úložiště a potom přidá připojovacího řetězce úložiště nastavení aplikace. |
|**Zálohování a obnovení aplikace**||
| [Zálohování webové aplikace](./scripts/app-service-powershell-backup-onetime.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří webové aplikace Azure a vytvoří pro ni jednorázové zálohy. |
| [Vytvoření naplánovaného zálohování pro webovou aplikaci](./scripts/app-service-powershell-backup-scheduled.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří webové aplikace Azure a vytvoří pro ni plánované zálohování. |
| [Odstranit zálohy pro webovou aplikaci](./scripts/app-service-powershell-backup-delete.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Odstraní existující zálohy pro webovou aplikaci. |
|**Monitorování aplikace**||
| [Monitorování webové aplikace pomocí protokolů webového serveru](./scripts/app-service-powershell-monitor.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Vytvoří webové aplikace Azure, povolí protokolování pro něj a stáhne protokoly do místního počítače. |
| | |
