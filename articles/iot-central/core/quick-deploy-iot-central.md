---
title: Rychlý Start – vytvoření aplikace IoT Central v Azure | Microsoft Docs
description: Rychlý Start – vytvoření nové aplikace Azure IoT Central. Vytvořte aplikaci pomocí cenového plánu zdarma nebo některého ze standardních cenových plánů.
author: viv-liu
ms.author: viviali
ms.date: 12/28/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: a1492593c24c2bc350205536006b648608cc5e46
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101712636"
---
# <a name="quickstart---create-an-azure-iot-central-application"></a>Rychlý Start – vytvoření aplikace IoT Central v Azure

V tomto rychlém startu se dozvíte, jak vytvořit aplikaci Azure IoT Central.

## <a name="prerequisite"></a>Požadavek 

Budete potřebovat předplatné Azure s přístupem *přispěvatele* .

## <a name="create-an-application"></a>Vytvoření aplikace

Přejděte na web [Azure IoT Central Build](https://aka.ms/iotcentral) . Pak se přihlaste pomocí osobního, pracovního nebo školního účtu Microsoft.

Novou aplikaci vytvoříte buď ze seznamu relevantních IoT Central šablon, které vám pomůžou rychle začít, nebo začít úplně od začátku pomocí šablony **vlastní aplikace** . V tomto rychlém startu použijete šablonu **vlastní aplikace** .

Vytvoření nové aplikace Azure IoT Central z **vlastní šablony aplikace** :

1. Přejděte na stránku **sestavení** :

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-new-application.png" alt-text="Sestavení stránky aplikace IoT":::

1. Vyberte **vlastní aplikace** a ujistěte se, že je vybraná Šablona **vlastní aplikace** .

1. Azure IoT Central automaticky navrhuje **název aplikace** na základě šablony aplikace, kterou jste vybrali. Můžete použít tento název nebo zadat vlastní popisný název aplikace.

1. Azure IoT Central také na základě názvu aplikace vygeneruje jedinečnou předponu **adresy URL aplikace** . Tuto adresu URL použijete pro přístup k aplikaci. Pokud chcete, změňte tuto předponu adresy URL na něco srozumitelnější.

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-custom.png" alt-text="Stránka pro vytvoření aplikace v Azure IoT Central":::

    :::image type="content" source="media/quick-deploy-iot-central/iotcentralcreate-billinginfo.png" alt-text="Informace o fakturaci IoT Central Azure":::

    > [!Tip]
    > Pokud jste na předchozí stránce vybrali **vlastní aplikaci** , zobrazí se rozevírací nabídka **šablony aplikace** . Rozevírací seznam může zobrazit další šablony, které vám zpřístupnil vaše organizace.

1. Tuto aplikaci můžete vytvořit pomocí cenového plánu bezplatné zkušební verze 7 dní, případně podle standardních cenových plánů:

    - Aplikace vytvořené pomocí *bezplatného* plánu jsou po dobu sedmi dnů zdarma a podporují až pět zařízení. Můžete je kdykoli převést na používání standardního cenového plánu, než vyprší jejich platnost.
        > [!NOTE]
        > Aplikace vytvořené pomocí *bezplatného* plánu nevyžadují předplatná Azure, a proto je v Azure Portal nenajdete v seznamu předplatného Azure. Na portálu IoT Central můžete zobrazit a spravovat jenom bezplatné aplikace.          
    - Aplikace, které vytvoříte pomocí plánu s plánem *Standard* , se účtují podle jednotlivých zařízení. v prvním ze dvou bezplatných zařízení můžete zvolit Cenový tarif **Standard 0**, **Standard 1** nebo **Standard 2** . Další informace o cenových plánech zdarma a Standard najdete na [stránce s cenami za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). Pokud vytvoříte aplikaci pomocí cenového plánu Standard, musíte vybrat svůj *adresář*, *předplatné Azure* a *umístění*:
        - *Adresář* je Azure Active Directory, ve kterém vytvoříte aplikaci. Azure Active Directory obsahuje identity uživatelů, přihlašovací údaje a další informace o organizaci. Pokud nemáte Azure Active Directory, vytvoří se při vytváření předplatného Azure jedna za vás.
        - *Předplatné Azure* umožňuje vytvářet instance služeb Azure. IoT Central zřídí prostředky v předplatném. Pokud předplatné Azure nemáte, můžete si ho na [stránce pro registraci k Azure](https://aka.ms/createazuresubscription)vytvořit zdarma. Po vytvoření předplatného Azure přejděte zpátky na stránku **Nová aplikace** . Vaše nové předplatné se teď zobrazí v rozevíracím seznamu **předplatné Azure** .
        - *Umístění* je [geografická](https://azure.microsoft.com/global-infrastructure/geographies/) oblast, kde byste chtěli vytvořit aplikaci. Obvykle byste měli zvolit umístění, které je fyzicky nejbližší pro vaše zařízení, aby se dosáhlo optimálního výkonu. Jakmile zvolíte umístění, nemůžete později přesunout aplikaci do jiného umístění.

1. Přečtěte si podmínky a ujednání a v dolní části stránky vyberte **vytvořit** . Po několika minutách IoT Central aplikace je připravená k použití:

    :::image type="content" source="media/quick-deploy-iot-central/iotcentral-application.png" alt-text="Aplikace Azure IoT Central":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili aplikaci IoT Central. Tady je doporučený další krok, který vám umožní pokračovat ve studiu o IoT Central:

> [!div class="nextstepaction"]
> [Přidání simulovaného zařízení do aplikace IoT Central](./quick-create-simulated-device.md)

Pokud jste vývojář zařízení a chcete podrobně do nějakého kódu, je navržený další krok:
> [!div class="nextstepaction"]
> [Vytvoření a připojení klientské aplikace k aplikaci Azure IoT Central](./tutorial-connect-device.md)