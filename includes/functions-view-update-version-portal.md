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
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52500045"
---
Pomocí následujícího postupu zobrazovat a aktualizovat verzi modulu runtime momentálně používáte aplikaci function app.

1. V [webu Azure portal](https://portal.azure.com), přejděte do aplikace function app.

1. V části **nakonfigurovat funkce**, zvolte **fungovat nastavení aplikace**.

    ![Vyberte nastavení aplikace function app](./media/functions-view-update-version-portal/add-update-app-setting.png)

1. V **fungovat nastavení aplikace** kartu, vyhledejte **verze modulu Runtime**. Poznamenejte si verzi modulu runtime specifické a požadovaný hlavní verze. V následujícím příkladu verze nastavena `~2`.

   ![Vyberte nastavení aplikace function app](./media/functions-view-update-version-portal/function-app-view-version.png)

1. Chcete-li připnout aplikace function app na modul runtime verze 1.x, zvolte **~ 1** pod **verze modulu Runtime**. Tento přepínač je zakázaný, pokud máte ve vaší aplikaci funkcí.

1. Při změně verze modulu runtime, vraťte se do **přehled** kartě a zvolte **restartovat** aplikaci restartovat.  Restartování aplikace function app běží na modul runtime verze 1.x a šablony verze 1.x se používají při vytváření funkce.