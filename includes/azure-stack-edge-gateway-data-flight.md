---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 04/16/2019
ms.author: alkohli
ms.openlocfilehash: f79081d506db6225b9ebef25674aad136e342ecf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96466845"
---
Pro data v letu:

- Protokol TLS (Standard Transport Layer Security) 1,2 se používá pro data přenášená mezi zařízením a Azure. Neexistuje žádná záloha na TLS 1,1 a starší. Pokud se TLS 1,2 nepodporuje, komunikace agenta se zablokuje. Pro správu portálu a sady SDK je také vyžadován protokol TLS 1,2.
- Když klienti přistupují k zařízení prostřednictvím místního webového uživatelského rozhraní prohlížeče, použije se jako výchozí zabezpečený protokol Standard TLS 1,2.

  - Osvědčeným postupem je nakonfigurovat v prohlížeči používání protokolu TLS 1,2.
  - Vaše zařízení podporuje jenom TLS 1,2 a nepodporuje starší verze TLS 1,1 a TLS 1,0.
- Pro ochranu dat při kopírování z datových serverů doporučujeme použít protokol SMB 3,0 se šifrováním.
