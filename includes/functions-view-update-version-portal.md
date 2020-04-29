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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "67175656"
---
Pomocí následujícího postupu můžete zobrazit a aktualizovat verzi modulu runtime, kterou aktuálně používá aplikace Function App.

1. V [Azure Portal](https://portal.azure.com)přejděte do aplikace Function App.

1. V části **nakonfigurované funkce**vyberte možnost **nastavení aplikace Function App**.

    ![Vybrat nastavení aplikace Function App](./media/functions-view-update-version-portal/add-update-app-setting.png)

1. Na kartě **nastavení aplikace funkcí** vyhledejte **verzi modulu runtime**. Všimněte si konkrétní verze modulu runtime a požadované hlavní verze. V následujícím příkladu je verze nastavena na `~2`.

   ![Vybrat nastavení aplikace Function App](./media/functions-view-update-version-portal/function-app-view-version.png)

1. Pokud chcete aplikaci Function App připnout na modul runtime verze 1. x, v části **verze modulu runtime**vyberte **~ 1** . Tento přepínač je zakázaný, když máte funkce ve vaší aplikaci.

1. Když změníte verzi modulu runtime, vraťte se na kartu **Přehled** a kliknutím na tlačítko **restartovat** restartujte aplikaci.  Aplikace Function App se restartuje v modulu runtime verze 1. x a při vytváření funkcí se používají šablony verze 1. x.