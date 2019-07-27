---
title: Řešení potíží se službou Speech SDK – Speech Service
titleSuffix: Azure Cognitive Services
description: Tento článek poskytuje informace, které vám pomůžou při řešení problémů, se kterými se můžete setkat při používání sady Speech Devices SDK.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: 9a757402360b4b69e760315a809482be9b4e04d4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558906"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Řešení potíže se sadou Speech Devices SDK

Tento článek poskytuje informace, které vám pomůžou při řešení problémů, se kterými se můžete setkat při používání sady Speech Devices SDK.

## <a name="certificate-failures"></a>Selhání certifikátu

Pokud při používání služeb řeči dojde k selhání certifikátu, ujistěte se, že má vaše zařízení správné datum a čas:

1. Přejděte na **nastavení**. V části **systému**vyberte **datum a čas**.

    ![V části Nastavení vyberte datum a čas](media/speech-devices-sdk/qsg-12.png)

1. Zachovat **automatické datum a čas** zaškrtnutou možnost. V části **vyberte časové pásmo**, vyberte aktuální časové pásmo.

    ![Vyberte datum a časové pásmo možnosti](media/speech-devices-sdk/qsg-13.png)

    Když se zobrazí, že dev kit čas odpovídá času ve vašem počítači, dev kit je připojený k Internetu.

## <a name="next-steps"></a>Další kroky

* [Přečtěte si poznámky k verzi](devices-sdk-release-notes.md)
