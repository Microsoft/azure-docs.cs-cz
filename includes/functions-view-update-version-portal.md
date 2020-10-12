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
ms.openlocfilehash: aa9a715fdafc143a116458691965087b016dec1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83343350"
---
Pomocí následujícího postupu můžete zobrazit a aktualizovat verzi modulu runtime, kterou aktuálně používá aplikace Function App.

1. V [Azure Portal](https://portal.azure.com)přejděte do aplikace Function App.

1. V části **Nastavení**vyberte **Konfigurace**. Na kartě **nastavení běhu funkce** Najděte **verzi modulu runtime**. Všimněte si konkrétní verze modulu runtime. V následujícím příkladu je verze nastavena na `~3` .

    :::image type="content" source="./media/functions-view-update-version-portal/functions-view-runtime-version.png" alt-text="Zobrazit verzi modulu runtime." border="true":::

1. Pokud chcete aplikaci Function App připnout na modul runtime verze 1. x, v části **verze modulu runtime**vyberte **~ 1** . Tento přepínač je zakázaný, když máte funkce ve vaší aplikaci.

1. Když změníte verzi modulu runtime, vraťte se na kartu **Přehled** a kliknutím na tlačítko **restartovat** restartujte aplikaci.  Aplikace Function App se restartuje v modulu runtime verze 1. x a při vytváření funkcí se používají šablony verze 1. x.

    :::image type="content" source="./media/functions-view-update-version-portal/functions-restart-function-app.png" alt-text="Zobrazit verzi modulu runtime." border="true":::
