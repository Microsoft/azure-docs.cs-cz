---
title: Vytvoření aplikace Azure IoT Central | Microsoft Docs
description: Vytvořte novou aplikaci Azure IoT Central. Vytvořte aplikaci pomocí cenového plánu zdarma nebo některého ze standardních cenových plánů.
author: viv-liu
ms.author: viviali
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 09ff84bf17bbc07ee86e90f3985a949f70d9fe27
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77018291"
---
# <a name="create-an-azure-iot-central-application"></a>Vytvoření aplikace Azure IoT Central

V tomto rychlém startu se dozvíte, jak vytvořit aplikaci Azure IoT Central.

## <a name="create-an-application"></a>Vytvoření aplikace

Přejděte na web [Azure IoT Central Build](https://aka.ms/iotcentral) . Pak se přihlaste pomocí osobního, pracovního nebo školního účtu Microsoft.

Novou aplikaci vytvoříte buď ze seznamu relevantních IoT Central šablon, které vám pomůžou rychle začít, nebo začít úplně od začátku pomocí šablony **vlastní aplikace** . V tomto rychlém startu použijete šablonu **vlastní aplikace** .

Vytvoření nové aplikace Azure IoT Central ze šablony **vlastní aplikace** :

1. Přejděte na stránku **sestavení** :

    ![Sestavení stránky aplikace IoT](media/quick-deploy-iot-central/iotcentralcreate-new-application.png)

1. Vyberte **vlastní aplikace** a ujistěte se, že je vybraná Šablona **vlastní aplikace** .

1. Azure IoT Central automaticky navrhuje **název aplikace** na základě šablony aplikace, kterou jste vybrali. Můžete použít tento název nebo zadat vlastní popisný název aplikace.

1. Azure IoT Central také na základě názvu aplikace vygeneruje jedinečnou předponu **adresy URL aplikace** . Tuto adresu URL použijete pro přístup k aplikaci. Pokud chcete, změňte tuto předponu adresy URL na něco srozumitelnější.

    ![Stránka pro vytvoření aplikace v Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate-custom.png)

    ![Informace o fakturaci IoT Central Azure](media/quick-deploy-iot-central/iotcentralcreate-billinginfo-pnp.png)

    > [!NOTE]
    > Pokud jste na předchozí stránce vybrali **vlastní aplikaci** , zobrazí se rozevírací nabídka **šablony aplikace** . Odsud můžete přepínat mezi vlastními a staršími šablonami. Můžete se také podívat na další šablony, které jsou pro vaši organizaci k dispozici.

1. Tuto aplikaci můžete vytvořit pomocí cenového plánu bezplatné zkušební verze 7 dní, případně podle standardních cenových plánů:

    - Aplikace vytvořené pomocí *bezplatného* plánu jsou po dobu sedmi dnů zdarma a podporují až pět zařízení. Můžete je kdykoli převést na používání standardního cenového plánu, než vyprší jejich platnost.
    - Aplikace, které vytvoříte pomocí plánu *Standard* , se účtují podle jednotlivých zařízení. můžete si vybrat Cenový tarif **Standard 1** nebo **Standard 2** s prvními dvěma zařízeními, která jsou zdarma. Další informace o cenových plánech zdarma a Standard najdete na [stránce s cenami za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). Pokud vytvoříte standardní aplikaci, budete muset vybrat svůj *adresář*, *předplatné Azure*a *umístění*:
        - *Adresář* je Azure Active Directory, ve kterém vytvoříte aplikaci. Azure Active Directory obsahuje identity uživatelů, přihlašovací údaje a další informace o organizaci. Pokud nemáte Azure Active Directory, vytvoří se při vytváření předplatného Azure jedna za vás.
        - *Předplatné Azure* umožňuje vytvářet instance služeb Azure. IoT Central zřídí prostředky v předplatném. Pokud předplatné Azure nemáte, můžete si ho na [stránce pro registraci k Azure](https://aka.ms/createazuresubscription)vytvořit zdarma. Po vytvoření předplatného Azure přejděte zpátky na stránku **Nová aplikace** . Vaše nové předplatné se teď zobrazí v rozevíracím seznamu **předplatné Azure** .
        - *Umístění* je [geografická](https://azure.microsoft.com/global-infrastructure/geographies/) oblast, kde byste chtěli vytvořit aplikaci. Obvykle byste měli zvolit umístění, které je fyzicky nejbližší pro vaše zařízení, aby se dosáhlo optimálního výkonu. Jakmile zvolíte umístění, nemůžete později přesunout aplikaci do jiného umístění.

1. Přečtěte si podmínky a ujednání a v dolní části stránky vyberte **vytvořit** . Po několika minutách IoT Central aplikace je připravená k použití:

    ![Aplikace Azure IoT Central](media/quick-deploy-iot-central/iotcentral-application.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili aplikaci IoT Central. Další navrhovaný krok:

> [!div class="nextstepaction"]
> [Přidání simulovaného zařízení do aplikace IoT Central](./quick-create-pnp-device.md)
