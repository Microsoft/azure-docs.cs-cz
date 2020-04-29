---
title: Šifrování dat IoT Hub Azure v klidovém umístění prostřednictvím klíčů spravovaných zákazníkem | Microsoft Docs
description: Šifrování neaktivních dat pomocí klíčů spravovaných zákazníkem pro IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/08/2020
ms.author: asrastog
ms.openlocfilehash: 609f68c79159c4ce17468fc1df13c0c9eae7f211
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79370572"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Šifrování neaktivních dat pomocí klíčů spravovaných zákazníkem pro IoT Hub

IoT Hub podporuje šifrování neaktivních dat pomocí klíčů spravovaných zákazníkem (CMK), označovaných také jako Přineste si vlastní klíč (BYOK), podporu pro Azure IoT Hub. Azure IoT Hub poskytuje šifrování neaktivních a přenosových dat. Ve výchozím nastavení IoT Hub používá k šifrování dat klíče spravované společností Microsoft. Díky podpoře CMK mají teď zákazníci možnost šifrování neaktivních dat pomocí klíčového šifrovacího klíče spravovaného zákazníky pomocí [Azure Key Vault](https://azure.microsoft.com/services/key-vault/).

Tato funkce vyžaduje vytvoření nové IoT Hub (úroveň Basic nebo Standard) v jedné z následujících oblastí: Východní USA, Západní USA 2, Střed USA – jih nebo US Gov. Pokud chcete tuto schopnost vyzkoušet, kontaktujte nás prostřednictvím [podpory Microsoftu](https://azure.microsoft.com/support/create-ticket/). Pokud se obrátíte na podporu Microsoftu, sdílejte název vaší společnosti a ID předplatného.

## <a name="next-steps"></a>Další kroky

* [Další informace o IoT Hub](https://docs.microsoft.com/azure/iot-hub/about-iot-hub)

* [Další informace o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
