---
title: Vytvoření aplikace Azure IoT Central | Microsoft Docs
description: Vytvořte novou aplikaci Azure IoT Central. Vytvořte aplikaci pomocí bezplatného cenového plánu nebo jednoho ze standardních cenových plánů.
author: viv-liu
ms.author: viviali
ms.date: 02/12/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 0ed32a4c1272c23c9500b35e05c383eac6dea185
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998952"
---
# <a name="create-an-azure-iot-central-application"></a>Vytvoření aplikace Azure IoT Central

Tento rychlý start ukazuje, jak vytvořit aplikaci Azure IoT Central.

## <a name="create-an-application"></a>Vytvoření aplikace

Přejděte na web [azure iot centrální sestavení.](https://aka.ms/iotcentral) Pak se přihlaste pomocí osobního, pracovního nebo školního účtu Microsoftu.

Novou aplikaci vytvoříte buď ze seznamu šablon IoT Central relevantních pro odvětví, které vám pomůžou rychle začít, nebo začít od začátku pomocí šablony **Vlastní aplikace.** V tomto rychlém startu použijete vlastní šablonu **aplikace.**

Vytvoření nové aplikace Azure IoT Central ze šablony **vlastní aplikace:**

1. Přejděte na stránku **Sestavení:**

    ![Vytvoření stránky aplikace IoT](media/quick-deploy-iot-central/iotcentralcreate-new-application.png)

1. Zvolte **Vlastní aplikace** a ujistěte se, že je vybraná **šablona Vlastní aplikace.**

1. Azure IoT Central automaticky navrhne **název aplikace** na základě vybrané šablony aplikace. Můžete použít tento název nebo zadat vlastní popisný název aplikace.

1. Azure IoT Central také generuje jedinečnou předponu **URL aplikace** pro vás, na základě názvu aplikace. Tuto adresu URL používáte pro přístup k aplikaci. Změňte tuto předponu URL na něco nezapomenutelnějšího, pokud chcete.

    ![Azure IoT Central Vytvoření stránky aplikace](media/quick-deploy-iot-central/iotcentralcreate-custom.png)

    ![Informace o centrální fakturaci Azure IoT](media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png)

    > [!NOTE]
    > Pokud jste na předchozí stránce **zvolili vlastní aplikaci,** zobrazí se rozbalovací seznam **šablony aplikace.** Zde můžete přepínat mezi vlastními a staršími šablonami. Můžete také zobrazit další šablony, které byly zpřístupněny pro vaši organizaci.

1. Zvolte vytvoření této aplikace pomocí 7denního bezplatného zkušebního cenového plánu nebo jednoho ze standardních cenových plánů:

    - Aplikace, které vytvoříte pomocí *bezplatného* plánu, jsou zdarma po dobu sedmi dnů a podporují až pět zařízení. Můžete je převést tak, aby používaly standardní cenový plán kdykoli před vypršením jejich platnosti.
    - Aplikace, které vytvoříte pomocí *standardního* plánu, se účtují podle zařízení, můžete zvolit cenový plán **Standard 1** nebo **Standard 2,** přičemž první dvě zařízení budou zdarma. Další informace o bezplatných a standardních cenových plánech najdete na [stránce s cenami Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). Pokud vytvoříte aplikaci pomocí standardního cenového plánu, budete muset vybrat *adresář*, *předplatné Azure*a *umístění:*
        - *Adresář* je služba Azure Active Directory, ve které vytvoříte aplikaci. Služba Azure Active Directory obsahuje identity uživatelů, přihlašovací údaje a další informace o organizaci. Pokud nemáte Službu Azure Active Directory, vytvoří se pro vás při vytváření předplatného Azure.
        - *Předplatné Azure* umožňuje vytvářet instance služeb Azure. IoT Central zřizovat prostředky ve vašem předplatném. Pokud nemáte předplatné Azure, můžete si ho vytvořit zdarma na [stránce registrace Azure](https://aka.ms/createazuresubscription). Po vytvoření předplatného Azure přejděte zpět na stránku **Nová aplikace.** Vaše nové předplatné se teď zobrazí v rozevíracím období **předplatného Azure.**
        - *Umístění* je [zeměpis,](https://azure.microsoft.com/global-infrastructure/geographies/) kde chcete vytvořit aplikaci. Obvykle byste měli zvolit umístění, které je fyzicky nejblíže k vašim zařízením, abyste získali optimální výkon. Jakmile zvolíte umístění, nemůžete později přesunout aplikaci do jiného umístění.

1. Zkontrolujte smluvní podmínky a v dolní části stránky vyberte **Vytvořit.** Po několika minutách je aplikace IoT Central připravena k použití:

    ![Aplikace Azure IoT Central](media/quick-deploy-iot-central/iotcentral-application.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili aplikaci IoT Central. Další navrhovaný krok:

> [!div class="nextstepaction"]
> [Přidání simulovaného zařízení do aplikace IoT Central](./quick-create-simulated-device.md)
