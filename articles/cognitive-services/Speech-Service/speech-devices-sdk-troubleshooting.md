---
title: Řešení potíží s zařízení řeči SDK – hlasové služby
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje informace, které vám pomůžou při řešení problémů, že se můžete setkat při použití sady SDK zařízení řeči.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: wellsi
ms.openlocfilehash: 87fb35f06dcb1d1e3fb8c3ae3be64c7448162f14
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026153"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Řešení potíží s zařízení řeči SDK

Tento článek obsahuje informace, které vám pomůžou při řešení problémů, že se můžete setkat při použití sady SDK zařízení řeči.

## <a name="certificate-failures"></a>Selhání certifikátu

Pokud se zobrazí chyby certifikátů při používání hlasové služby, ujistěte se, že vaše zařízení má správné datum a čas:

1. Přejděte na **nastavení**. V části **systému**vyberte **datum a čas**.

    ![V části Nastavení vyberte datum a čas](media/speech-devices-sdk/qsg-12.png)

1. Zachovat **automatické datum a čas** zaškrtnutou možnost. V části **vyberte časové pásmo**, vyberte aktuální časové pásmo.

    ![Vyberte datum a časové pásmo možnosti](media/speech-devices-sdk/qsg-13.png)

    Když se zobrazí, že dev kit čas odpovídá času ve vašem počítači, dev kit je připojený k Internetu.

## <a name="next-steps"></a>Další postup

* [Přečtěte si poznámky k verzi](devices-sdk-release-notes.md)
