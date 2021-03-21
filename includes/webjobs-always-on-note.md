---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c255be53a1809bf5dd3fc6b184852767dfec9c66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009669"
---
> [!NOTE]
> Po 20 minutách nečinnosti může webová aplikace vytrvat déle. a pouze požadavky na skutečnou webovou aplikaci mohou resetovat časovač. Zobrazení konfigurace aplikace ve Azure Portal nebo provedení požadavků na web rozšířených nástrojů ( `https://<app_name>.scm.azurewebsites.net` ) neresetuje časovač. Pokud nastavíte webovou aplikaci tak, aby běžela průběžné nebo plánované webové úlohy časovače (časovač-Trigger), povolte nastavení **Always On** na stránce **Konfigurace** Azure vaší webové aplikace, aby bylo zajištěno, že se webové úlohy budou spolehlivě spouštět. Tato funkce je dostupná jenom pro [cenové úrovně](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)Basic, Standard a Premium.
