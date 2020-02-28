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
ms.openlocfilehash: b065135715868094b7e248aebfca35f3b55a4c48
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77675140"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-device-provisioning-service"></a>Šifrování neaktivních dat pomocí klíčů spravovaných zákazníkem pro službu Device Provisioning

## <a name="overview"></a>Přehled

Služba Device Provisioning Service (DPS) podporuje šifrování neaktivních dat pomocí klíčů spravovaných zákazníkem (CMK), označovaných také jako funkce Přineste si vlastní klíč (BYOK). DPS zajišťuje Šifrování neaktivních dat a přenosů. Ve výchozím nastavení DPS používá k šifrování dat klíče spravované společností Microsoft. Díky podpoře CMK mají teď zákazníci možnost šifrování neaktivních dat pomocí klíčového šifrovacího klíče spravovaného zákazníky pomocí [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Tato funkce vyžaduje vytvoření nového DPS v jedné z následujících oblastí: Východní USA, Západní USA 2 nebo Střed USA – jih. Pokud chcete tuto schopnost vyzkoušet, kontaktujte nás prostřednictvím [podpory Microsoftu](https://azure.microsoft.com/support/create-ticket/). Pokud se obrátíte na podporu Microsoftu, sdílejte název vaší společnosti a ID předplatného.

## <a name="next-steps"></a>Další kroky

* [Další informace o službě Device Provisioning Service](https://docs.microsoft.com/azure/iot-dps/)

* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)