---
title: Ukázky Azure CLI – služba Azure SignalR
description: Pomocí reálných ukázek se naučíte Azure CLI pro službu Azure Signal. Naučíte se, jak vytvořit službu signalizace s dalšími službami Azure.
author: sffamily
ms.service: signalr
ms.topic: reference
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 99ebe9c078ef4775e8f0df0545fb0af4b3d1057b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86515236"
---
# <a name="azure-cli-reference"></a>Referenční informace k Azure CLI

Následující tabulka obsahuje odkazy na skripty Bash pro službu Azure SignalR vytvořené pomocí Azure CLI.

| Skript | Označení |
|-|-|
|**Vytvořit**||
| [Vytvoření nové služby SignalR a skupiny prostředků](scripts/signalr-cli-create-service.md) | Vytvoří nový prostředek služby Azure SignalR v nové skupině prostředků s náhodným názvem.  |
|**Integrace**||
| [Vytvoření nové služby SignalR a webové aplikace nakonfigurované pro použití služby SignalR](scripts/signalr-cli-create-with-app-service.md) | Vytvoří nový prostředek služby Azure SignalR v nové skupině prostředků s náhodným názvem. Také přidá novou webovou aplikaci a App Service plán pro hostování ASP.NET Core webové aplikace, která používá službu Signal. Webová aplikace je nakonfigurovaná pomocí nastavení aplikace k připojení k novému prostředku služby SignalR. |
| [Vytvoření nové služby SignalR a webové aplikace nakonfigurované pro použití služby SignalR a standardu OAuth GitHubu](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Vytvoří nový prostředek služby Azure SignalR v nové skupině prostředků s náhodným názvem. Přidá taky novou webovou aplikaci Azure a plán hostování pro hostování ASP.NET Core webové aplikace, která používá službu Signal. Webová aplikace je nakonfigurovaná pomocí nastavení aplikace pro připojovací řetězec nového prostředku služby SignalR a tajných klíčů klienta pro podporu [ověřování GitHubu](https://developer.github.com/v3/guides/basics-of-authentication/), jak můžete vidět v [kurzu ověřování](signalr-concept-authenticate-oauth.md). Webová aplikace je dále nakonfigurovaná tak, aby jako zdroj nasazení používala místní úložiště Git. |
| | |
