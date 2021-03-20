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
ms.openlocfilehash: c590e0972de289a36890a75b220eddbded701ea8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "74815559"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Řešení potíže se sadou Speech Devices SDK

Tento článek poskytuje informace, které vám pomůžou při řešení problémů, se kterými se můžete setkat při používání sady Speech Devices SDK.

## <a name="certificate-failures"></a>Selhání certifikátů

Pokud při používání služby řeči dojde k chybě certifikátu, ujistěte se, že má vaše zařízení správné datum a čas:

1. Přejít na **Nastavení**. V části **systém** vyberte **Datum & čas**.

    ![V části nastavení vyberte datum & čas.](media/speech-devices-sdk/qsg-12.png)

1. Ponechte vybranou možnost **automatického data & času** . V části **Vybrat časové pásmo** vyberte své aktuální časové pásmo.

    ![Výběr možností data a času v časovém pásmu](media/speech-devices-sdk/qsg-13.png)

    Když zjistíte, že se čas nástroje dev Kit shoduje s časem v počítači, je sada dev Kit připojená k Internetu.

## <a name="next-steps"></a>Další kroky

* [Přečtěte si poznámky k verzi.](devices-sdk-release-notes.md)
