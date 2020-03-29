---
title: Použití webu Azure IoT Solutions – Azure | Dokumenty společnosti Microsoft
description: Popisuje, jak používat web AzureIoTSolutions.com k nasazení akcelerátoru řešení.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 87f6b9cef50e4b8c388be835b2aa7bed8177ac4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61447424"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>Nasazení akcelerátoru řešení pomocí webu azureiotsolutions.com

Akcelerátory řešení Azure IoT můžete nasadit do předplatného Azure z [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators). AzureIoTSolutions.com hostuje akcelerátory řešení s otevřeným zdrojovým kódem i partnerské řešení společnosti Microsoft. Tyto akcelerátory řešení zarovnávají s [referenční architekturou Azure IoT](https://aka.ms/iotrefarchitecture). Lokalitu můžete použít k rychlému nasazení akcelerátoru řešení jako ukázkového nebo produkčního prostředí.

![AzureIoTSolutions.com](media/iot-accelerators-permissions/iotsolutionscom.png)

> [!TIP]
> Pokud potřebujete větší kontrolu nad procesem nasazení, můžete použít [cli k nasazení akcelerátoru řešení](iot-accelerators-remote-monitoring-deploy-cli.md).

Akcelerátory řešení můžete nasadit v následujících konfiguracích:

* **Standard**: Rozšířené nasazení infrastruktury pro vývoj produkčního prostředí. Služba Azure Container Service nasazuje mikroslužby do několika virtuálních počítačů Azure. Kubernetes orchestruje kontejnery Dockeru, které jsou hostiteli jednotlivých mikroslužeb.
* **Základní**: Verze se sníženými náklady pro ukázku nebo testování nasazení. Všechny mikroslužby se nasazují do jednoho virtuálního počítače Azure.
* **Místní**: Nasazení místního počítače pro testování a vývoj. Tento přístup nasazuje mikroslužby do místního kontejneru Dockeru a připojuje se k iot hubu, Azure Cosmos DB a službám úložiště Azure v cloudu.

Každý akcelerátory řešení používá jinou kombinaci služeb Azure, jako je IoT Hub, Azure Stream Analytics a Cosmos DB. Další informace naleznete [na AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) a vyberte akcelerátor řešení.

## <a name="sign-in-at-azureiotsolutionscom"></a>Přihlaste se na azureiotsolutions.com

Než nasadíte akcelerátor řešení, musíte se přihlásit AzureIoTSolutions.com pomocí přihlašovacích údajů přidružených k předplatnému Azure. Pokud je váš účet přidružený k více než jednomu tenantovi služby Microsoft Azure Active Directory (AD), můžete pomocí **rozevíracího seznamu Výběr účtu** zvolit adresář, který chcete použít.

Vaše oprávnění k nasazení akcelerátorů řešení, správě uživatelů a správě služeb Azure závisí na vaší roli ve vybraném adresáři. Mezi běžné role Azure AD přidružené k akcelerátorům řešení patří:

* **Globální správce**: Na klienta Azure AD může existovat mnoho [globálních správců:](../active-directory/users-groups-roles/directory-assign-admin-roles.md)

  * Když vytvoříte klienta Azure AD, jste ve výchozím nastavení globálním správcem tohoto klienta.
  * Globální správce může nasadit základní a standardní akcelerátory řešení.

* **Uživatel domény**: Může existovat mnoho uživatelů domény na klienta Azure AD. Uživatel domény může nasadit základní akcelerátor řešení.

* **Uživatel typu Host:** Může existovat mnoho uživatelů typu Host na klienta Azure AD. Uživatelé typu Host nemohou nasadit akcelerátor řešení v tenantovi Azure AD.

Další informace o uživatelích a rolích ve službě Azure AD najdete v následujících zdrojích informací:

* [Vytváření uživatelů ve službě Azure Active Directory](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Přiřazení uživatelů k aplikacím](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Vyberte si zařízení

Web AzureIoTSolutions.com odkazuje na [katalog zařízení Azure Certified for IoT](https://catalog.azureiotsolutions.com/).

Katalog obsahuje stovky certifikovaných hardwarových zařízení IoT, které můžete připojit k akcelerátorům řešení a začít vytvářet řešení IoT.

![Katalog zařízení](media/iot-accelerators-permissions/devicecatalog.png)

Pokud jste výrobce hardwaru, klikněte na **Stát se partnerem** a získejte informace o partnerství se společností Microsoft v programu Certified for IoT.

## <a name="next-steps"></a>Další kroky

Pokud si chcete vyzkoušet některý z akcelerátorů řešení IoT, projděte si následující rychlé starty:

* [Vyzkoušení řešení pro vzdálené monitorování](quickstart-remote-monitoring-deploy.md)
* [Vyzkoušení řešení propojené továrny](quickstart-connected-factory-deploy.md)
* [Vyzkoušení řešení prediktivní údržby](quickstart-predictive-maintenance-deploy.md)
* [Vyzkoušení řešení simulace zařízení](quickstart-device-simulation-deploy.md)
