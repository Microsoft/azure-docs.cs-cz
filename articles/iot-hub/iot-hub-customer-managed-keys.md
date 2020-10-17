---
title: Šifrování dat IoT Hub Azure v klidovém umístění prostřednictvím klíčů spravovaných zákazníkem | Microsoft Docs
description: Šifrování neaktivních dat pomocí klíčů spravovaných zákazníkem pro IoT Hub
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: asrastog
ms.openlocfilehash: 352da24b36124ff0446a81c1ecbc584da545bb16
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92142202"
---
# <a name="encryption-of-data-at-rest-with-customer-managed-keys-for-iot-hub"></a>Šifrování neaktivních dat pomocí klíčů spravovaných zákazníkem pro IoT Hub

IoT Hub podporuje šifrování neaktivních dat pomocí klíčů spravovaných zákazníkem (CMK), označovaných také jako Přineste si vlastní klíč (BYOK). Azure IoT Hub poskytuje šifrování neaktivních a přenosových dat při jejich zapsání v našich datacentrech a dešifruje je za vás, jak k nim přistupujete. Ve výchozím nastavení používá IoT Hub k šifrování neaktivních dat klíče spravované společností Microsoft. Pomocí CMK můžete získat další vrstvu šifrování nad výchozím šifrováním a můžete si zvolit Šifrování neaktivních dat pomocí klíčového šifrovacího klíče spravovaného prostřednictvím [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Díky tomu získáte flexibilitu při vytváření, střídání, zakázání a odvolávání řízení přístupu. Pokud je pro IoT Hub nakonfigurovaná BYOK, poskytujeme také dvojité šifrování, které nabízí druhou vrstvu ochrany, a přitom vám umožní řídit šifrovací klíč pomocí Azure Key Vault.

Tato funkce vyžaduje vytvoření nové IoT Hub (úroveň Basic nebo Standard). Pokud chcete tuto schopnost vyzkoušet, kontaktujte nás prostřednictvím [podpory Microsoftu](https://azure.microsoft.com/support/create-ticket/). Pokud se obrátíte na podporu Microsoftu, sdílejte název vaší společnosti a ID předplatného.


## <a name="next-steps"></a>Další kroky

* [Další informace o IoT Hub](./about-iot-hub.md)

* [Další informace o Azure Key Vault](../key-vault/general/overview.md)