---
title: Spravovat IoT Central z Azure Portal | Microsoft Docs
description: Tento článek popisuje, jak vytvořit a spravovat vaše aplikace IoT Central z Azure Portal.
services: iot-central
ms.service: iot-central
author: vishwam
ms.author: vishwams
ms.date: 04/17/2021
ms.topic: how-to
manager: philmea
ms.openlocfilehash: ed65e85c7428bf59fe770534e97afdd53564086a
ms.sourcegitcommit: 089c2bd1ac4861f43c4b89396d3d056a6eef4913
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2021
ms.locfileid: "107601990"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Správa IoT Central z Azure Portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Pomocí [Azure Portal](https://portal.azure.com) můžete vytvářet a spravovat IoT Central aplikace podobně jako funkce ve [správci aplikací](https://apps.azureiotcentral.com/myapps)v IoT Central.

## <a name="create-iot-central-applications"></a>Vytváření IoT Centralch aplikací

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Chcete-li vytvořit aplikaci, přejděte na stránku [vytvořit IoT Central aplikace](https://ms.portal.azure.com/#create/Microsoft.IoTCentral) v Azure Portal a vyplňte formulář.

![Vytvořit formulář IoT Central](media/howto-manage-iot-central-from-portal/image6a.png)

* **Název prostředku** je jedinečný název, který můžete ve vaší skupině prostředků Azure zvolit pro vaši aplikaci IoT Central.

* **Adresa URL aplikace** je adresa URL, kterou můžete použít pro přístup k aplikaci.

* **Umístění** je [geografická](https://azure.microsoft.com/global-infrastructure/geographies/) oblast, kde byste chtěli vytvořit aplikaci. Obvykle byste měli zvolit umístění, které je fyzicky nejbližší pro vaše zařízení, aby se dosáhlo optimálního výkonu. Služba Azure IoT Central je aktuálně dostupná v následujících umístěních:
    * Asie a Tichomoří
    * Australia
    * Europe
    * Japan
    * Spojené království
    * USA

  Jakmile zvolíte umístění, nemůžete později přesunout aplikaci do jiného umístění.

Po vyplnění všech polí vyberte **vytvořit**. Další informace najdete v tématu rychlý Start [k vytvoření IoT Central aplikace](quick-deploy-iot-central.md) .

## <a name="manage-existing-iot-central-applications"></a>Spravovat existující aplikace IoT Central

Pokud už máte aplikaci Azure IoT Central, můžete ji odstranit nebo ji přesunout do jiného předplatného nebo skupiny prostředků v Azure Portal.

> [!NOTE]
> Aplikace vytvořené pomocí *bezplatného* plánu nevyžadují předplatná Azure, a proto je v Azure Portal nenajdete v seznamu předplatného Azure. Na portálu IoT Central můžete zobrazit a spravovat jenom bezplatné aplikace.

Začněte tím, že na panelu hledání v horní části Azure Portal vyhledáte svoji aplikaci. Všechny aplikace můžete zobrazit také tak, že vyhledáte "IoT Centralé aplikace" a vyberete službu:

![Snímek obrazovky, který zobrazuje výsledky hledání pro "IoT Centralé aplikace" s první vybranou službou.](media/howto-manage-iot-central-from-portal/search-iot-central.png)

Po výběru aplikace ve výsledcích hledání zobrazí Azure Portal přehled. Kliknutím na **IoT Central adresa URL aplikace** můžete přejít do vlastní aplikace:

![Snímek obrazovky zobrazující stránku "Přehled" s zvýrazněnou adresou URL aplikace IoT Central.](media/howto-manage-iot-central-from-portal/image3.png)

Pokud chcete aplikaci přesunout do jiné skupiny prostředků, vyberte u skupiny prostředků možnost **změnit** . Na stránce **přesunout prostředky** vyberte skupinu prostředků, do které chcete tuto aplikaci přesunout:

![Snímek obrazovky zobrazující stránku "Přehled" se zvýrazněnou skupinou prostředků (změnit).](media/howto-manage-iot-central-from-portal/image4a.png)

Pokud chcete aplikaci přesunout do jiného předplatného, vyberte u předplatného možnost  **změnit** . Na stránce **Přesunutí prostředků** vyberte předplatné, do kterého chcete tuto aplikaci přesunout:

![Portál pro správu: Správa prostředků](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak spravovat aplikace IoT Central Azure z Azure Portal, je tady doporučený další krok:

> [!div class="nextstepaction"]
> [Správa vaší aplikace](howto-administer.md)