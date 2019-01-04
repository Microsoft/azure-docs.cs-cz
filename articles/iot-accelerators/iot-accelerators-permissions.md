---
title: Použijte řešení IoT v Azure – Azure | Dokumentace Microsoftu
description: Popisuje způsob použití AzureIoTSolutions.com webu nasadit akcelerátor řešení.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 87f6b9cef50e4b8c388be835b2aa7bed8177ac4b
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53601079"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>Použití azureiotsolutions.com webu nasadit akcelerátor řešení

Akcelerátory řešení Azure IoT můžete nasadit ke svému předplatnému Azure z [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators). AzureIoTSolutions.com hostuje Microsoft open source a akcelerátory řešení partnerů. Tyto akcelerátory řešení bylo v souladu s [referenční architektura IoT v Azure](https://aka.ms/iotrefarchitecture). Webu můžete použít k rychlému nasazení akcelerátoru řešení jako ukázku nebo produkčního prostředí.

![AzureIoTSolutions.com](media/iot-accelerators-permissions/iotsolutionscom.png)

> [!TIP]
> Pokud potřebujete větší kontrolu nad tímto procesem nasazení, můžete použít [rozhraní příkazového řádku pro nasazení akcelerátoru řešení](iot-accelerators-remote-monitoring-deploy-cli.md).

Můžete nasadit akcelerátory řešení v následující konfigurace:

* **Standardní**: Nasazení rozšířené infrastruktury pro vývoj produkčního prostředí. Azure Container Service nasazuje mikroslužby do několika virtuálních počítačů Azure. Kubernetes orchestruje kontejnery Dockeru, které jsou hostiteli jednotlivých mikroslužeb.
* **Základní**: Verze nižšími náklady pro ukázku nebo otestování nasazení. Všechny mikroslužby se nasazují do jednoho virtuálního počítače Azure.
* **Místní**: Nasazení místního počítače pro vývoj a testování. Tento přístup nasazuje mikroslužby do místní kontejner Dockeru a připojí ke službě IoT Hub, Azure Cosmos DB a služby Azure storage v cloudu.

Každá akcelerátory řešení používá jinou kombinaci služeb Azure, například služby IoT Hub, Azure Stream Analytics a Cosmos DB. Další informace najdete v článku [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) a vybrat akcelerátor řešení.

## <a name="sign-in-at-azureiotsolutionscom"></a>Přihlaste se na azureiotsolutions.com

Před nasazením akcelerátoru řešení, musíte se přihlásit na AzureIoTSolutions.com pomocí přihlašovacích údajů spojené s předplatným Azure. Pokud váš účet je přidruženo více než jednoho tenanta Microsoft Azure Active Directory (AD), můžete použít **rozevírací výběr účtu** zvolte adresář, který chcete použít.

Oprávnění k akcelerátorům řešení nasazení, správu uživatelů a správy služeb Azure závisí na vaší roli ve vybraném adresáři. Běžné úlohy služby Azure AD přidružené k akcelerátorům řešení patří:

* **Globální správce**: Může být mnoho [globální správci](../active-directory/users-groups-roles/directory-assign-admin-roles.md) na tenanta Azure AD:

  * Když vytvoříte tenanta služby Azure AD, které jsou ve výchozím nastavení globálním správcem tohoto tenanta.
  * Globální správce, můžete nasadit akcelerátory řešení basic a standard.

* **Uživatel domény**: Může existovat mnoho uživatelů domény na tenanta služby Azure AD. Uživatel domény můžete nasadit základní řešení akcelerátoru.

* **Uživatel typu Host**: Může existovat mnoho uživatelů typu Host na tenanta Azure AD. Uživatelé typu Host nemůže nasazení akcelerátoru řešení v tenantovi Azure AD.

Další informace týkající se uživatelů a rolí ve službě Azure AD najdete v článku na následujících odkazech:

* [Vytvořit uživatele v Azure Active Directory](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Přiřazení uživatelů k aplikacím](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Zvolte si zařízení

Web AzureIoTSolutions.com odkazuje na [programu Azure Certified for IoT zařízení katalogu](https://catalog.azureiotsolutions.com/).

Katalog obsahuje stovky certifikovaných hardwarových zařízení IoT, se můžete připojit k vaše akcelerátory řešení, abyste mohli začít vytvářet řešení IoT.

![Katalog zařízení](media/iot-accelerators-permissions/devicecatalog.png)

Pokud jste výrobce hardwaru, klikněte na tlačítko **stát se partnerem** Další informace o partnerství s Microsoftem na Certified for IoT programu.

## <a name="next-steps"></a>Další postup

Pokud si chcete vyzkoušet některý z akcelerátorů řešení IoT, projděte si následující rychlé starty:

* [Vyzkoušení řešení vzdáleného monitorování](quickstart-remote-monitoring-deploy.md)
* [Vyzkoušení řešení propojené továrny](quickstart-connected-factory-deploy.md)
* [Vyzkoušení řešení prediktivní údržby](quickstart-predictive-maintenance-deploy.md)
* [Vyzkoušení řešení simulace zařízení](quickstart-device-simulation-deploy.md)
