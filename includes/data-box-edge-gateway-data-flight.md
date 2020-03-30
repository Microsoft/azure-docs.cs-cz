---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175644"
---
Pro data za letu:

- Standardní TLS 1.2 se používá pro data, která se pohybuje mezi zařízením a Azure. Neexistuje žádný záložní TLS 1.1 a starší. Komunikace agenta bude blokována, pokud není podporován a nebude podporován a nebude podporován systém TLS 1.2. TLS 1.2 je také vyžadován pro správu portálu a sady SDK.
- Když klienti přistupují k vašemu zařízení prostřednictvím místního webového uživatelského rozhraní prohlížeče, použije se jako výchozí zabezpečený protokol standardní protokol TLS 1.2.

    - Osvědčeným postupem je nakonfigurovat prohlížeč tak, aby používal protokol TLS 1.2.
    - Pokud prohlížeč nepodporuje TLS 1.2, můžete použít TLS 1.1 nebo TLS 1.0.
- Doporučujeme používat SMB 3.0 s šifrováním k ochraně dat při kopírování z datových serverů.
