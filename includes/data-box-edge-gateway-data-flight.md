---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: acf1195616d45b155445604ef0204528e5f7ca03
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60754255"
---
Pro data na cestě:

- Standardní protokol TLS 1.2 je používána pro data přenášená mezi zařízením a Azure. Není záložní ověřování protokolu TLS 1.1 a dříve. Komunikace agenta se zablokuje, pokud není podporován protokol TLS 1.2. Také je vyžadována pro portál a správa sady SDK TLS 1.2.
- Když klienti přístup k vašemu zařízení prostřednictvím místního webového uživatelského rozhraní v prohlížeči, standardní TLS 1.2 slouží jako výchozí zabezpečený protokol.

    - Osvědčeným postupem je prohlížeč nakonfigurovat pro použití protokolu TLS 1.2.
    - Pokud prohlížeč nepodporuje protokol TLS 1.2, můžete použít protokol TLS 1.1 a TLS 1.0.
- Doporučujeme použít protokol SMB 3.0 se šifrováním k ochraně dat při kopírování ze serverů se data.
