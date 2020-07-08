---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "67175644"
---
Pro data v letu:

- Standard TLS 1,2 se používá pro data přenášená mezi zařízením a Azure. Neexistuje žádná záloha na TLS 1,1 a starší. Pokud se TLS 1,2 nepodporuje, komunikace agenta se zablokuje. Pro správu portálu a sady SDK je také vyžadován protokol TLS 1,2.
- Když klienti přistupují k zařízení prostřednictvím místního webového uživatelského rozhraní prohlížeče, použije se jako výchozí zabezpečený protokol Standard TLS 1,2.

    - Osvědčeným postupem je nakonfigurovat v prohlížeči používání protokolu TLS 1,2.
    - Pokud prohlížeč nepodporuje TLS 1,2, můžete použít TLS 1,1 nebo TLS 1,0.
- Pro ochranu dat při kopírování z datových serverů doporučujeme použít protokol SMB 3,0 se šifrováním.
