---
title: Šifrování dat služby Azure IoT Hub v klidovém stavu prostřednictvím klíčů spravovaných zákazníkem| Dokumenty společnosti Microsoft
description: Šifrování dat v klidovém stavu pomocí klíčů spravovaných zákazníkem pro Službu IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370572"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Šifrování dat v klidovém stavu pomocí klíčů spravovaných zákazníkem pro Službu IoT Hub

IoT Hub podporuje šifrování dat v klidovém stavu pomocí klíčů spravovaných zákazníky (CMK), označované také jako Bring your own key (BYOK), podpora azure iot hubu. Azure IoT Hub poskytuje šifrování dat v klidovém stavu a při přenosu. Ve výchozím nastavení používá služba IoT Hub k šifrování dat klíče spravované společností Microsoft. S podporou CMK mají zákazníci nyní možnost šifrování dat v klidovém stavu pomocí šifrovacího klíče klíče spravovaného zákazníky pomocí [trezoru klíčů Azure](https://azure.microsoft.com/services/key-vault/).

Tato funkce vyžaduje vytvoření nového iot hubu (základní nebo standardní úroveň) v jedné z následujících oblastí: Východní USA, Západní USA 2, Jižní střed USA nebo Us Gov. Chcete-li tuto možnost vyzkoušet, kontaktujte nás prostřednictvím [podpory společnosti Microsoft](https://azure.microsoft.com/support/create-ticket/). Při kontaktování podpory microsoftu sdílejte název společnosti a ID předplatného.

## <a name="next-steps"></a>Další kroky

* [Další informace o IoT Hubu](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
