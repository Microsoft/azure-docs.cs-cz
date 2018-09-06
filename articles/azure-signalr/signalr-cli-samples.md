---
title: Ukázky Azure CLI – služba Azure SignalR | Microsoft Docs
description: Ukázky Azure CLI – služba Azure SignalR
services: signalr
documentationcenter: signalr
author: sffamily
manager: cfowler
editor: ''
tags: azure-service-management
ms.service: signalr
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: signalr
ms.date: 04/20/2018
ms.author: zhshang
ms.custom: mvc
ms.openlocfilehash: a1ca02a5e058816c133accf3027f0a951db42cf2
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43663999"
---
# <a name="azure-cli-samples"></a>Ukázky Azure CLI

Následující tabulka obsahuje odkazy na skripty Bash pro službu Azure SignalR vytvořené pomocí Azure CLI.

| | |
|-|-|
|**Vytvoření**||
| [Vytvoření nové služby SignalR a skupiny prostředků](scripts/signalr-cli-create-service.md) | Vytvoří nový prostředek služby Azure SignalR v nové skupině prostředků s náhodným názvem.  |
|**Integrace**||
| [Vytvoření nové služby SignalR a webové aplikace nakonfigurované pro použití služby SignalR](scripts/signalr-cli-create-with-app-service.md) | Vytvoří nový prostředek služby Azure SignalR v nové skupině prostředků s náhodným názvem. Dále přidá novou webovou aplikaci a plán služby App Service k hostování webové aplikace ASP.NET Core, která službu SignalR používá. Webová aplikace je nakonfigurovaná pomocí nastavení aplikace k připojení k novému prostředku služby SignalR. |
| [Vytvoření nové služby SignalR a webové aplikace nakonfigurované pro použití služby SignalR a standardu OAuth GitHubu](scripts/signalr-cli-create-with-app-service-github-oauth.md) | Vytvoří nový prostředek služby Azure SignalR v nové skupině prostředků s náhodným názvem. Dále přidá novou webovou aplikaci a plán hostování k hostování webové aplikace ASP.NET Core, která službu SignalR používá. Webová aplikace je nakonfigurovaná pomocí nastavení aplikace pro připojovací řetězec nového prostředku služby SignalR a tajných klíčů klienta pro podporu [ověřování GitHubu](https://developer.github.com/v3/guides/basics-of-authentication/), jak můžete vidět v [kurzu ověřování](signalr-authenticate-oauth.md). Webová aplikace je dále nakonfigurovaná tak, aby jako zdroj nasazení používala místní úložiště Git. |
| | |
