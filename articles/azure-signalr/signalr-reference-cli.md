---
title: Ukázky Azure CLI – služba Azure SignalR
description: Ukázky Azure CLI – služba Azure SignalR
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: dedd9d14bafb342b0b34e3685ef3b7a46a54f9c8
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554943"
---
# <a name="azure-cli-reference"></a>Referenční informace k Azure CLI

Následující tabulka obsahuje odkazy na skripty Bash pro službu Azure SignalR vytvořené pomocí Azure CLI.

| | |
|-|-|
|**Vytvoření**||
| [Vytvoření nové služby SignalR a skupiny prostředků](scripts/signalr-cli-create-service.md) | Vytvoří nový prostředek služby Azure SignalR v nové skupině prostředků s náhodným názvem.  |
|**Integrace**||
| [Vytvoření nové služby SignalR a webové aplikace nakonfigurované pro použití služby SignalR](scripts/signalr-cli-create-with-app-service.md) | Vytvoří nový prostředek služby Azure SignalR v nové skupině prostředků s náhodným názvem. Dále přidá novou webovou aplikaci a plán služby App Service k hostování webové aplikace ASP.NET Core, která službu SignalR používá. Webová aplikace je nakonfigurovaná pomocí nastavení aplikace k připojení k novému prostředku služby SignalR. |
| [Vytvoření nové služby SignalR a webové aplikace nakonfigurované pro použití služby SignalR a standardu OAuth GitHubu](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Vytvoří nový prostředek služby Azure SignalR v nové skupině prostředků s náhodným názvem. Dále přidá novou webovou aplikaci a plán hostování k hostování webové aplikace ASP.NET Core, která službu SignalR používá. Webová aplikace je nakonfigurovaná pomocí nastavení aplikace pro připojovací řetězec nového prostředku služby SignalR a tajných klíčů klienta pro podporu [ověřování GitHubu](https://developer.github.com/v3/guides/basics-of-authentication/), jak můžete vidět v [kurzu ověřování](signalr-concept-authenticate-oauth.md). Webová aplikace je dále nakonfigurovaná tak, aby jako zdroj nasazení používala místní úložiště Git. |
| | |