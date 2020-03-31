---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 11/26/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: d06bda1826964b019edb156375885c7f389ca6ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175656"
---
Pomocí následujícího postupu můžete zobrazit a aktualizovat verzi runtime aktuálně používanou aplikací funkcí.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do aplikace funkce.

1. V části **Nakonfigurované funkce**zvolte **Nastavení aplikace Funkce**.

    ![Výběr nastavení aplikace funkcí](./media/functions-view-update-version-portal/add-update-app-setting.png)

1. Na kartě **Nastavení aplikace Funkce** vyhledejte verzi **runtime**. Všimněte si konkrétní verze runtime a požadované hlavní verze. V níže uvedeném příkladu `~2`je verze nastavena na .

   ![Výběr nastavení aplikace funkcí](./media/functions-view-update-version-portal/function-app-view-version.png)

1. Chcete-li připnout aplikaci funkce na runtime verze 1.x, zvolte **~1** v části **Runtime version**. Tento přepínač je zakázán, pokud máte v aplikaci funkce.

1. Když změníte verzi runtime, přejděte zpět na kartu **Přehled** a zvolte **Restartovat,** chcete-li aplikaci restartovat.  Aplikace funkce se restartuje spuštěna v době runtime verze 1.x a při vytváření funkcí se používají šablony verze 1.x.