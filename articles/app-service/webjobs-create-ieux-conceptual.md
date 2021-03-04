---
title: Webová úloha, úlohy na pozadí v Azure
description: Přečtěte si o webových úlohách.
author: ggailey777
ms.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1cb5e99558d6bf1a5baa21d05d45415855c61cb5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744872"
---
# <a name="webjobs-run-background-tasks-in-azure-app-service"></a>Webové úlohy spouštějí úlohy na pozadí v Azure App Service

Tento článek ukazuje, jak nasadit webové úlohy pomocí [Azure Portal](https://portal.azure.com) k nahrání spustitelného souboru nebo skriptu. Informace o tom, jak vyvíjet a nasazovat WebJobs pomocí sady Visual Studio, najdete v tématu [Nasazení WebJobs pomocí sady Visual Studio](webjobs-dotnet-deploy-vs.md).

## <a name="overview"></a>Přehled
WebJobs je funkce [Azure App Service](index.yml) , která umožňuje spustit program nebo skript ve stejné instanci jako webová aplikace, aplikace API nebo mobilní aplikace. Pro použití WebJobs se neúčtují žádné další náklady.

> [!IMPORTANT]
> Webové úlohy se zatím nepodporují pro App Service v systému Linux.

Sadu Azure WebJobs SDK lze použít s WebJobs k zjednodušení mnoha programovacích úloh. Další informace najdete v tématu [co je sada WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki).

Azure Functions poskytuje další způsob, jak spouštět programy a skripty. Porovnání mezi službami WebJobs a Functions najdete v tématu [Výběr mezi tokem, Logic Apps, funkcemi a WebJobs](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md).

## <a name="webjob-types"></a>Typy webové úlohy

Následující tabulka popisuje rozdíly mezi *průběžnými* a *aktivovanými* WebJobs.


|Průběžný  |Aktivuje  |
|---------|---------|
| Spustí se hned po vytvoření webové úlohy. Aby bylo možné úlohu ukončit, program nebo skript obvykle provede svou práci v nekonečné smyčce. Pokud úloha skončí, můžete ji restartovat. | Spustí se jenom v případě, že se aktivuje ručně nebo podle plánu. |
| Spustí se ve všech instancích, na kterých běží webová aplikace. Můžete volitelně omezit webovou úlohu na jednu instanci. |Spustí se v jediné instanci, kterou Azure vybere pro vyrovnávání zatížení.|
| Podporuje vzdálené ladění. | Nepodporuje vzdálené ladění.|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="add-webjob-to-source-control"></a>Přidat webovou úlohu do správy zdrojových kódů

Pokud máte s vaší aplikací nakonfigurovaný zdrojový ovládací prvek, webové úlohy by se měly nasadit jako součást integrace správy zdrojového kódu. Po konfiguraci správy zdrojového kódu s vaší aplikací nejde webovou úlohu přidat z webu Azure Portal.

## <a name="supported-file-types-for-scripts-or-programs"></a><a name="acceptablefiles"></a>Podporované typy souborů pro skripty nebo programy

Podporované jsou následující typy souborů:

* . cmd,. bat,. exe (použití Windows CMD)
* . ps1 (použití PowerShellu)
* . sh (použití bash)
* . php (použití PHP)
* . py (použití Pythonu)
* . js (použití Node.js)
* . jar (použití jazyka Java)

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak [vytvořit webovou úlohu](./webjobs-create-ieux.md)
* Zobrazení historie protokolu WebJobs] (./WebJobs-Create-ieux-View-log.MD)