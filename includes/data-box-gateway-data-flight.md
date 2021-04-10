---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: e177fabdad7a1517a8371fe1d3483cb5c9310d2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96582340"
---
Pro data v letu:

- Standard TLS 1,2 se používá pro data přenášená mezi zařízením a Azure. Neexistuje žádná záloha na TLS 1,1 a starší. Pokud se TLS 1,2 nepodporuje, komunikace agenta se zablokuje. Pro správu portálu a sady SDK je také vyžadován protokol TLS 1,2.
- Když klienti přistupují k zařízení prostřednictvím místního webového uživatelského rozhraní prohlížeče, použije se jako výchozí zabezpečený protokol Standard TLS 1,2.

    - Osvědčeným postupem je nakonfigurovat v prohlížeči používání protokolu TLS 1,2.
    - Pokud prohlížeč nepodporuje TLS 1,2, můžete použít TLS 1,1 nebo TLS 1,0.
- Pro ochranu dat při kopírování z datových serverů doporučujeme použít protokol SMB 3,0 se šifrováním.
