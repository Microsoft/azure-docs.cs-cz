---
title: Speech Devices SDK Roobo Smart audio dev Kit v2 – Speech Service
titleSuffix: Azure Cognitive Services
description: Požadavky a pokyny pro zahájení práce se sadou Speech Devices SDK a Roobo Smart audio dev Kit v2
services: cognitive-services
author: anushapatnala
manager: wellsi
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/20/2020
ms.author: v-anusp
ms.openlocfilehash: 2f325c80877068c53d690bd7ff74f768dab3a174
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "80371582"
---
# <a name="device-roobo-smart-audio-dev-kit-v2"></a>Zařízení: Roobo Smart audio dev Kit v2

Tento článek poskytuje informace specifické pro konkrétní zařízení pro vývoj Roobo Smart audio Kit2.

## <a name="set-up-the-development-kit"></a>Nastavení vývojové sady

1. Vývojová sada má dva konektory USB. Levý konektor je Power Kit a na obrázku níže se zvýrazní jako napájení. Napravo je jeho řízení a je označeno jako ladění v obrázku. 
    ![Připojení k sadě dev SDK](media/speech-devices-sdk/roobo-v2-connections.png)
1. Pokud chcete připojit port napájení k počítači nebo adaptéru napájení, zapněte vývojovou sadu pomocí kabelu mikroUSB. Zelený indikátor napájení se na horní desce rozsvítí.
1. K řízení vývojové sady připojte port ladění k počítači pomocí druhého kabelu USB. Je nutné používat vysoce kvalitní kabel k zajištění spolehlivé komunikace.
1. Naorientujte svou vývojovou sadu cyklicky – vzhůru s mikrofony, které čelí hornímu limitu, jak je uvedeno výše.


## <a name="development-information"></a>Informace o vývoji

Další informace o vývoji najdete v [příručce pro vývoj Roobo](http://dwn.roo.bo/server_upload/ddk/ROOBO%20Dev%20Kit-User%20Guide.pdf).

## <a name="audio-recordplay"></a>Záznam zvuku/přehrávání

DDK2 zvukové operace lze provést následujícími způsoby:
* Používejte ALSA open source knihovny a jejich aplikace.
* K vývoji aplikací použijte rozhraní appmainprog. DDK2 audio software v cloudu používá standardní architekturu ALSA, můžete použít libasound. Proto je potřeba vyvíjet software přímo. Takže můžete k záznamu a přehrání zvuku použít přímo arecord a aplay ALSA.
