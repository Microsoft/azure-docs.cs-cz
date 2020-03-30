---
title: Ukázky Azure CLI – služba Azure SignalR
description: Postupujte podle skutečných ukázek a naučte se azure cli pro službu Azure SignalR. Dozvíte se, jak vytvořit službu SignalR s dalšími službami Azure.
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 3c28781362e60d22b0d9a4e3754daaa24e5b61ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158122"
---
# <a name="azure-cli-reference"></a>Referenční informace k Azure CLI

Následující tabulka obsahuje odkazy na skripty Bash pro službu Azure SignalR vytvořené pomocí Azure CLI.

| | |
|-|-|
|**Vytvořit**||
| [Vytvoření nové služby SignalR a skupiny prostředků](scripts/signalr-cli-create-service.md) | Vytvoří nový prostředek služby Azure SignalR v nové skupině prostředků s náhodným názvem.  |
|**Integrace**||
| [Vytvoření nové služby SignalR a webové aplikace nakonfigurované pro použití služby SignalR](scripts/signalr-cli-create-with-app-service.md) | Vytvoří nový prostředek služby Azure SignalR v nové skupině prostředků s náhodným názvem. Také přidá nový plán Webové aplikace a služby App Service pro hostování ASP.NET Základní webová aplikace, která používá službu SignalR. Webová aplikace je nakonfigurovaná pomocí nastavení aplikace k připojení k novému prostředku služby SignalR. |
| [Vytvoření nové služby SignalR a webové aplikace nakonfigurované pro použití služby SignalR a standardu OAuth GitHubu](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Vytvoří nový prostředek služby Azure SignalR v nové skupině prostředků s náhodným názvem. Přidá také nový Azure Web App a hostování plán pro hostování ASP.NET Core Web App, který používá službu SignalR. Webová aplikace je nakonfigurovaná pomocí nastavení aplikace pro připojovací řetězec nového prostředku služby SignalR a tajných klíčů klienta pro podporu [ověřování GitHubu](https://developer.github.com/v3/guides/basics-of-authentication/), jak můžete vidět v [kurzu ověřování](signalr-concept-authenticate-oauth.md). Webová aplikace je dále nakonfigurovaná tak, aby jako zdroj nasazení používala místní úložiště Git. |
| | |
