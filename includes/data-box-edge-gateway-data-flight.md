---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: bea14544949f90290bf3878295b49f1e08be9eea
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684409"
---
Pro data v let:

- Pro data, která přejdou mezi zařízením a Azure se používá standardní protokol TLS 1.2. Není záložní ověřování protokolu TLS 1.1 a dříve. Komunikace agenta se zablokuje, pokud není podporován protokol TLS 1.2. Také je vyžadována portál a operace správy SDK TLS 1.2.
- Pokud klienti přístup k vašemu zařízení prostřednictvím místního webového uživatelského rozhraní v prohlížeči, standardní TLS 1.2 slouží jako výchozí zabezpečený protokol.

    - Osvědčeným postupem je prohlížeč nakonfigurovat pro použití protokolu TLS 1.2.
    - Pokud prohlížeč nepodporuje protokol TLS 1.2, můžete použít protokol TLS 1.1 a TLS 1.0.
- K ochraně dat při kopírování z vašich dat serverů, doporučujeme použít se šifrování protokolu SMB 3.0.
