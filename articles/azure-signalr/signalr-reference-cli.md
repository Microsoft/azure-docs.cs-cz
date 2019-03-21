---
title: Ukázky Azure CLI – služba Azure SignalR
description: Ukázky Azure CLI – služba Azure SignalR
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 1fbe96d827bcf6bb39a6cf7a4e6811174b862d59
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57992209"
---
# <a name="azure-cli-reference"></a>Referenční informace k Azure CLI

Následující tabulka obsahuje odkazy na skripty Bash pro službu Azure SignalR vytvořené pomocí Azure CLI.

| | |
|-|-|
|**Vytvoření**||
| [Vytvoření nové služby SignalR a skupiny prostředků](scripts/signalr-cli-create-service.md) | Vytvoří nový prostředek služby Azure SignalR v nové skupině prostředků s náhodným názvem.  |
|**Integrace**||
| [Vytvoření nové služby SignalR a webové aplikace nakonfigurované pro použití služby SignalR](scripts/signalr-cli-create-with-app-service.md) | Vytvoří nový prostředek služby Azure SignalR v nové skupině prostředků s náhodným názvem. Také přidá nový plán webové aplikace a služby App Service pro hostování webové aplikace ASP.NET Core, která používá služby SignalR. Webová aplikace je nakonfigurovaná pomocí nastavení aplikace k připojení k novému prostředku služby SignalR. |
| [Vytvoření nové služby SignalR a webové aplikace nakonfigurované pro použití služby SignalR a standardu OAuth GitHubu](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Vytvoří nový prostředek služby Azure SignalR v nové skupině prostředků s náhodným názvem. Také přidá novou webovou aplikaci Azure a na hostitele webové aplikace ASP.NET Core, která používá službu SignalR plán hostování. Webová aplikace je nakonfigurovaná pomocí nastavení aplikace pro připojovací řetězec nového prostředku služby SignalR a tajných klíčů klienta pro podporu [ověřování GitHubu](https://developer.github.com/v3/guides/basics-of-authentication/), jak můžete vidět v [kurzu ověřování](signalr-concept-authenticate-oauth.md). Webová aplikace je dále nakonfigurovaná tak, aby jako zdroj nasazení používala místní úložiště Git. |
| | |
