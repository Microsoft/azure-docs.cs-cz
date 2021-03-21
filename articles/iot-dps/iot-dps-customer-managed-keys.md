---
title: Šifrování dat služby Azure Device Provisioning v klidovém provozu prostřednictvím klíčů spravovaných zákazníkem | Microsoft Docs
description: Šifrování neaktivních dat pomocí klíčů spravovaných zákazníkem pro službu Device Provisioning
author: chrissie926
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: menchi
ms.openlocfilehash: d22a01bab81fc330484e7715a65c89a1cfd7802c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94967172"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Šifrování neaktivních dat pomocí klíčů spravovaných zákazníkem pro službu Device Provisioning

## <a name="overview"></a>Přehled

Služba Device Provisioning Service (DPS) podporuje šifrování neaktivních dat pomocí klíčů spravovaných zákazníkem (CMK), označovaných také jako funkce Přineste si vlastní klíč (BYOK). DPS zajišťuje Šifrování neaktivních dat a jejich přenosů, když je napíšete v našich datacentrech, a při přístupu k nim dešifrujeme. Ve výchozím nastavení DPS používá pro šifrování neaktivních dat klíče spravované společností Microsoft. Pomocí CMK můžete získat další vrstvu šifrování na základě výchozího šifrování platformy tím, že vyberete možnost šifrování neaktivních dat pomocí klíčového šifrovacího klíče spravovaného prostřednictvím [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Získáte tak flexibilitu při vytváření, střídání, zakázání a odvolávání klíčů. Pokud je pro svůj DPS nakonfigurovaný CMK, znamená to, že je povolené dvojité šifrování se dvěma vrstvami ochrany, které aktivně chrání vaše data. 

Tato funkce vyžaduje vytvoření nového DPS. Pokud chcete tuto schopnost vyzkoušet, kontaktujte nás prostřednictvím [podpory Microsoftu](https://azure.microsoft.com/support/create-ticket/). Pokud se obrátíte na podporu Microsoftu, sdílejte název vaší společnosti a ID předplatného.


## <a name="next-steps"></a>Další kroky

* [Další informace o službě Device Provisioning Service](./index.yml)

* [Další informace o Azure Key Vault](../key-vault/general/overview.md)