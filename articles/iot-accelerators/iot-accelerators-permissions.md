---
title: Použití webu řešení Azure IoT – Azure | Microsoft Docs
description: Popisuje, jak používat web AzureIoTSolutions.com k nasazení akcelerátoru řešení.
author: dominicbetts
manager: philmea
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 5a088cb07fa74b65e497de54be8c97bd51d31c16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97387832"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>Nasazení akcelerátoru řešení pomocí webu azureiotsolutions.com

Akcelerátory řešení Azure IoT můžete nasadit do předplatného Azure z [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators). AzureIoTSolutions.com je hostitelem akcelerátorů řešení Microsoft Open Source i partner. Tyto akcelerátory řešení se zarovnají s [referenční architekturou Azure IoT](/azure/architecture/reference-architectures/iot). Web můžete použít k rychlému nasazení akcelerátoru řešení jako ukázkové nebo produkční prostředí.

:::image type="content" source="media/iot-accelerators-permissions/iotsolutionscom.png" alt-text="Domovská stránka řešení IoT":::

> [!TIP]
> Pokud potřebujete větší kontrolu nad procesem nasazení, můžete použít rozhraní příkazového řádku k nasazení akcelerátoru řešení.

Akcelerátory řešení můžete nasadit v následujících konfiguracích:

* **Standard**: rozšířené nasazení infrastruktury pro vývoj produkčního prostředí. Azure Container Service nasadí mikroslužby na několik virtuálních počítačů Azure. Kubernetes orchestruje kontejnery Dockeru, které jsou hostiteli jednotlivých mikroslužeb.
* **Basic**: nižší verze nákladů pro ukázku nebo testování nasazení. Všechny mikroslužby se nasazují do jednoho virtuálního počítače Azure.
* **Místní**: nasazení místního počítače pro testování a vývoj. Tento přístup nasadí mikroslužby do místního kontejneru Docker a připojí se ke službám IoT Hub, Azure Cosmos DB a Azure Storage v cloudu.

Každá z akcelerátorů řešení používá jinou kombinaci služeb Azure, jako jsou IoT Hub, Azure Stream Analytics a Cosmos DB. Další informace najdete na [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) a vyberte akcelerátor řešení.

## <a name="sign-in-at-azureiotsolutionscom"></a>Přihlášení na azureiotsolutions.com

Než budete moct nasadit akcelerátor řešení, musíte se přihlásit na AzureIoTSolutions.com pomocí přihlašovacích údajů přidružených k předplatnému Azure. Pokud je váš účet spojený s více Microsoft Azure Active Directory (AD), můžete pomocí **rozevírací nabídky Výběr účtu** vybrat adresář, který chcete použít.

Vaše oprávnění nasadit akcelerátory řešení, spravovat uživatele a spravovat služby Azure závisí na vaší roli ve vybraném adresáři. Mezi běžné role Azure AD přidružené k akcelerátorům řešení patří:

* **Globální správce**: může existovat mnoho [globálních správců](../active-directory/roles/permissions-reference.md) na tenanta Azure AD:

  * Při vytváření tenanta Azure AD jste ve výchozím nastavení globálním správcem tohoto tenanta.
  * Globální správce může nasazovat základní a standardní akcelerátory řešení.

* **Uživatel domény**: může existovat mnoho uživatelů domény na TENANTA Azure AD. Uživatel domény může nasadit základní akcelerátor řešení.

* **Uživatel typu Host**: může existovat mnoho uživatelů typu host na TENANTA Azure AD. Uživatelé typu Host nemůžou nasadit akcelerátor řešení v tenantovi Azure AD.

Další informace o uživatelích a rolích ve službě Azure AD najdete v následujících zdrojích informací:

* [Vytváření uživatelů v Azure Active Directory](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [Přiřazení uživatelů k aplikacím](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>Zvolit zařízení

Lokalita AzureIoTSolutions.com odkazuje na [katalog zařízení Azure Certified for IoT](https://catalog.azureiotsolutions.com/).

V katalogu je uveden seznam stovek certifikovaných hardwarových zařízení IoT, se kterými se můžete připojit k vašim akcelerátorům řešení a začít sestavovat řešení IoT.

![Katalog zařízení](media/iot-accelerators-permissions/devicecatalog.png)

Pokud jste výrobce hardwaru, klikněte na **stát se partnerem** , kde se dozvíte o partnerství s Microsoftem v programu Certified for IoT.

## <a name="next-steps"></a>Další kroky

Pokud si chcete vyzkoušet některý z akcelerátorů řešení IoT, Projděte si rychlý Start: [Vyzkoušejte řešení propojené továrny](quickstart-connected-factory-deploy.md).
