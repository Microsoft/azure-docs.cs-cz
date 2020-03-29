---
title: Řečová zařízení SDK Roobo Smart Audio Dev Kit v2 - Služba řeči
titleSuffix: Azure Cognitive Services
description: Požadavky a pokyny pro zahájení s řečovými zařízeními SDK, Roobo Smart Audio Dev Kit v2.
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 2f325c80877068c53d690bd7ff74f768dab3a174
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80371582"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>Zařízení: Roobo Smart Audio Dev Kit v2

Tento článek obsahuje informace specifické pro zařízení Roobo Smart Audio Dev Kit2.

## <a name="set-up-the-development-kit"></a>Nastavení vývojové sady

1. Vývojová sada má dva micro USB konektory. Levý konektor je pro napájení vývojové sady a je zvýrazněn jako Napájení na obrázku níže. Ten správný je ovládat, a je označen ladění v obraze. 
    ![Připojení sady pro dev](media/speech-devices-sdk/roobo-v2-connections.png)
1. Napájet vývojovou soupravu pomocí kabelu micro USB pro připojení napájecího portu k počítači nebo napájecímu adaptéru. Pod horní deskou se rozsvítí zelený indikátor napájení.
1. Chcete-li ovládat vývojovou sadu, připojte ladicí port k počítači pomocí druhého kabelu micro USB. Pro zajištění spolehlivé komunikace je nezbytné použít vysoce kvalitní kabel.
1. Orientujte svou vývojovou soupravu Kruhově - vzpřímeně, s mikrofony směřujícími ke stropu, jak je uvedeno výše


## <a name="development-information"></a>Informace o vývoji

Další informace o vývoji naleznete v [průvodci vývojem Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio-recordplay"></a>Zvukový záznam/přehrát

Zvukové operace DDK2 lze provádět následujícími způsoby:
* Používejte alsa open-source knihovny a jejich aplikace.
* Použijte appmainprog rozhraní k tomu vývoj aplikací. DDK2 audio - související softwarový framework používá standardní ALSA framework, můžete použít libasound. Takže vyvíjet software přímo. Takže můžete použít ALSA arecord a aplay přímo pro záznam a přehrávání zvuku.
