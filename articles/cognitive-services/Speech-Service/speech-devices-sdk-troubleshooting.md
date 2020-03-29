---
title: Poradce při potížích s sadou SDK pro rozpoznávání řeči – služba Řeč
titleSuffix: Azure Cognitive Services
description: Tento článek obsahuje informace, které vám pomohou vyřešit problémy, se kterými se můžete setkat při použití sady SDK pro zařízení pro rozpoznávání řeči.
services: cognitive-services
author: mswellsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: wellsi
ms.openlocfilehash: c590e0972de289a36890a75b220eddbded701ea8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74815559"
---
# <a name="troubleshoot-the-speech-devices-sdk"></a>Řešení potíže se sadou Speech Devices SDK

Tento článek obsahuje informace, které vám pomohou vyřešit problémy, se kterými se můžete setkat při použití sady SDK pro zařízení pro rozpoznávání řeči.

## <a name="certificate-failures"></a>Selhání certifikátu

Pokud se při používání služby Řeč zobrazí selhání certifikátu, zkontrolujte, zda má vaše zařízení správné datum a čas:

1. Přejděte do **nastavení**. V části **Systém**vyberte **datum & času**.

    ![V části Nastavení vyberte Datum & čas.](media/speech-devices-sdk/qsg-12.png)

1. Ponechte vybranou možnost **Automatické datum & čas.** V části **Vybrat časové pásmo**vyberte aktuální časové pásmo.

    ![Vybrat možnosti data a časového pásma](media/speech-devices-sdk/qsg-13.png)

    Když zjistíte, že čas sady pro spuštění odpovídá času v počítači, je sada pro spuštění připojena k internetu.

## <a name="next-steps"></a>Další kroky

* [Kontrola poznámek k verzi](devices-sdk-release-notes.md)
