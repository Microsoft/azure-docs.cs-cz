---
title: Doporučení pro zabezpečení a konfiguraci brány firewall Azure Percept
description: Další informace o konfiguraci brány firewall Azure Percept a doporučeních pro zabezpečení
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: cfc20a30104e24a3950c71bdd8377544803d2f25
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105604409"
---
# <a name="azure-percept-firewall-configuration-and-security-recommendations"></a>Doporučení pro zabezpečení a konfiguraci brány firewall Azure Percept

V níže uvedených pokynech najdete informace o konfiguraci bran firewall a obecných osvědčených postupech zabezpečení pomocí Azure Percept.

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Konfigurace bran firewall pro Azure Percept DK

Pokud instalace sítě vyžaduje, abyste výslovně povolili připojení vytvořená ze zařízení Azure Percept DK, Projděte si následující seznam součástí.

Tento kontrolní seznam je výchozím bodem pro pravidla brány firewall:

|Adresa URL (* = zástupný znak)|Odchozí porty TCP|Využití|
|-------------------|------------------|---------|
|*. auth.azureperceptdk.azure.net|443|Ověřování a autorizace ve službě Azure DK SOM|
|*. auth.projectsantacruz.azure.net|443|Ověřování a autorizace ve službě Azure DK SOM|

Dále si Projděte seznam [připojení používaných Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/production-checklist#allow-connections-from-iot-edge-devices).

## <a name="additional-recommendations-for-deployment-to-production"></a>Další doporučení pro nasazení do produkčního prostředí

Azure Percept DK nabízí v krabici spoustu možností zabezpečení. Kromě těchto výkonných funkcí zabezpečení, které jsou součástí aktuální verze, společnost Microsoft také navrhuje při zvažování produkčního nasazení následující pokyny:

- Silná fyzická ochrana samotného zařízení
- Zajistěte, aby bylo zapnuté šifrování na základě dat.
- Nepřetržitě monitorujte zařízení stav a rychle reagují na výstrahy.
- Omezte počet správců, kteří mají přístup k zařízení.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o zabezpečení Azure Percept](./overview-percept-security.md)