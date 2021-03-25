---
title: Příručka pro správce Azure IoT Central
description: Azure IoT Central je aplikační platforma IoT, která zjednodušuje vytváření řešení IoT. Tento článek poskytuje přehled role správce v IoT Central.
author: TheJasonAndrew
ms.author: v-anjaso
ms.date: 03/22/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: 95bf9693916c20533455c76f1e137779bb81754c
ms.sourcegitcommit: bb330af42e70e8419996d3cba4acff49d398b399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105039011"
---
# <a name="iot-central-administrator-guide"></a>Příručka pro správce IoT Central

Tento článek poskytuje přehled role správce v IoT Central. 

Pokud chcete získat přístup k části Správa a používat ji, musíte být v roli správce aplikace Azure IoT Central. Pokud vytvoříte aplikaci IoT Central pro Azure, automaticky se přiřadíte k roli správce této aplikace.

Jako _správce_ zodpovídáte za úlohy správy, jako například:

* Správa rolí
* Oprávnění ke způsobu přístupu
* Správa aplikace změnou názvu a adresy URL aplikace
* Nahrávání obrázku
* Odstranění aplikace v aplikaci Azure IoT Central.

## <a name="manage-application-settings"></a>Spravovat nastavení aplikace
Máte možnost [Spravovat nastavení aplikace](howto-administer.md).

## <a name="manage-billing"></a>Správa vyúčtování
Můžete [spravovat fakturaci služby Azure IoT Central](howto-view-bill.md). Svou aplikaci můžete přesunout od bezplatného cenového plánu ke standardnímu cenovému plánu a také upgradovat nebo downgradovat podle cenového plánu.

## <a name="export-applications"></a>Exportovat aplikace
[Aplikaci Azure IoT můžete exportovat](howto-use-app-templates.md) tak, abyste ji mohli znovu použít.

## <a name="manage-migration-between-versions"></a>Správa migrace mezi verzemi
Při vytváření nové aplikace IoT Central se jedná o aplikaci v3. Pokud jste dříve vytvořili aplikaci, může být v závislosti na době jejího vytvoření v2. Můžete [migrovat v2 do aplikace V3](howto-migrate.md).

## <a name="monitor-application-health"></a>Monitorování stavu aplikace
Můžete nastavit metriky poskytované IoT Central k [vyhodnocení stavu zařízení](howto-monitor-application-health.md) , která jsou připojena k vaší IoT Central aplikace a stavu spuštěných exportů dat.

## <a name="manage-security-x509-sas-keys-api-tokens"></a>Správa zabezpečení (X. 509, klíče SAS, tokeny API)
Jako _správce_ můžete provést následující akce:
* Správa [certifikátů X. 509](how-to-roll-x509-certificates.md)
* Obléčební [klíčů SAS](concepts-get-connected.md)
* Zkontrolovat [tokeny rozhraní API](https://docs.microsoft.com/rest/api/iotcentral/)

## <a name="configure-file-uploads"></a>Konfigurace nahrávání souborů
Můžete nakonfigurovat způsob [nahrávání souborů](howto-configure-file-uploads.md) .

## <a name="tools---azure-cli-azure-powershell-azure-portal"></a>Nástroje – Azure CLI, Azure PowerShell, Azure Portal

Tady jsou některé nástroje, ke kterým máte přístup jako _správce_.
* [Azure CLI](howto-manage-iot-central-from-cli.md)
* [Azure PowerShell](howto-manage-iot-central-from-powershell.md)
* [Azure Portal](howto-manage-iot-central-from-portal.md)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak spravovat vaši aplikaci Azure IoT Central, je doporučeným dalším krokem informace o [správě uživatelů a rolí](howto-manage-users-roles.md) v Azure IoT Central.
