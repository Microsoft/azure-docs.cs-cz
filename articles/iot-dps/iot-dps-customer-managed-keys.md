---
title: Šifrování dat služby Azure Device Provisioning Service v klidovém stavu prostřednictvím klíčů spravovaných zákazníkem| Dokumenty společnosti Microsoft
description: Šifrování dat v klidovém stavu pomocí klíčů spravovaných zákazníkem pro službu Device Provisioning Service
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77675140"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Šifrování dat v klidovém stavu pomocí klíčů spravovaných zákazníkem pro službu Device Provisioning Service

## <a name="overview"></a>Přehled

Služba Device Provisioning Service (DPS) podporuje šifrování dat v klidovém stavu pomocí klíčů spravovaných zákazníkem (CMK), označovaných také jako Bring your own key (BYOK). DPS poskytuje šifrování dat v klidovém stavu a při přenosu. Ve výchozím nastavení používá DPS k šifrování dat klíče spravované společností Microsoft. S podporou CMK mají zákazníci nyní možnost šifrování dat v klidovém stavu pomocí šifrovacího klíče klíče spravovaného zákazníky pomocí [trezoru klíčů Azure](https://azure.microsoft.com/services/key-vault/).

Tato funkce vyžaduje vytvoření nového DPS v jedné z následujících oblastí: Východní USA, Západní USA 2 nebo Střední usa v jižní části USA. Chcete-li tuto možnost vyzkoušet, kontaktujte nás prostřednictvím [podpory společnosti Microsoft](https://azure.microsoft.com/support/create-ticket/). Při kontaktování podpory microsoftu sdílejte název společnosti a ID předplatného.

## <a name="next-steps"></a>Další kroky

* [Další informace o službě Zřizování zařízení](https://docs.microsoft.com/azure/iot-dps/)

* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)