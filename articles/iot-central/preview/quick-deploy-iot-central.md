---
title: Vytvoření aplikace Azure IoT Central | Microsoft Docs
description: Vytvořte novou aplikaci Azure IoT Central. Vytvoření zkušební nebo standardní aplikace pomocí šablony aplikace
author: lmasieri
ms.author: lmasieri
ms.date: 12/18/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: 1b19909f005fa11b842fccc0497cb49960e32a3b
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989707"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Vytvoření aplikace Azure IoT Central (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

V tomto rychlém startu se dozvíte, jak vytvořit aplikaci IoT Central Azure, která obsahuje funkce verze Preview, jako je například IoT technologie Plug and Play.

> [!WARNING]
> Možnosti IoT technologie Plug and Play v Azure IoT Central jsou momentálně ve verzi Public Preview. Nepoužívejte IoT Central aplikaci technologie Plug and Play IoT povolenou pro produkční úlohy. V produkčních prostředích se používá aplikace IoT Central vytvořená z aktuální, všeobecně dostupné šablony aplikace.

## <a name="create-an-application"></a>Vytvoření aplikace

Přejděte na web [Azure IoT Central Build](https://aka.ms/iotcentral) . Pak se přihlaste pomocí osobního, pracovního nebo školního účtu Microsoft.

Novou aplikaci vytvoříte buď ze seznamu relevantních IoT Central šablon, které vám pomůžou rychle začít, nebo začít úplně od začátku pomocí šablony **vlastní aplikace** .

![Stránka pro vytvoření aplikace v Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate-templates-pnp.png)

Vytvoření nové aplikace Azure IoT Central:

1. Pokud chcete vytvořit novou aplikaci Azure IoT Central ze *šablony odvětví*, vyberte šablonu aplikace ze seznamu dostupných šablon v jednom z odvětví. Můžete také začít od nuly volbou *vlastní aplikace*.
1. Azure IoT Central automaticky navrhuje **název aplikace** na základě šablony aplikace, kterou jste vybrali. Můžete použít tento název nebo zadat vlastní popisný název aplikace.
1. Azure IoT Central také na základě názvu aplikace vygeneruje jedinečnou předponu **adresy URL aplikace** . Tuto adresu URL použijete pro přístup k aplikaci. Tuto předponu adresy URL můžete změnit na něco, co byste si chtěli.

    ![Stránka pro vytvoření aplikace v Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate-industry-pnp.png)

    ![Stránka pro vytvoření aplikace v Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate-billinginfo-pnp.png)
    > [!NOTE]
    > Pokud používáte šablonu vlastní aplikace, zobrazí se pole rozevírací seznam **Šablona aplikace** . Odsud můžete přepínat mezi verzí Preview a všeobecně dostupnými šablonami. Můžete se také podívat na další šablony, které jsou pro vaši organizaci k dispozici.

1. Vyberte, jestli chcete tuto aplikaci vytvořit pomocí bezplatné zkušební verze 7 dní, nebo použijte standardní předplatné.
    - Aplikace, které vytvoříte pomocí plánu **Standard** , se účtují podle jednotlivých zařízení. můžete zvolit Cenový tarif **Standard 1** nebo **Standard 2** s prvními dvěma zařízeními, která jsou zdarma. Další informace o cenových plánech zdarma a Standard najdete na [stránce s cenami za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). Pokud vytvoříte standardní aplikaci, budete muset vybrat svůj *adresář*, *předplatné Azure*a *umístění*:
      - *Adresář* je Azure Active Directory (AAD), ve kterém vytvoříte aplikaci. Služba Azure AD obsahuje identity uživatelů, přihlašovací údaje a další informace o organizaci. Pokud nemáte Azure AD, vytvoří se při vytváření předplatného Azure jedna za vás.
      - *Předplatné Azure* umožňuje vytvářet instance služeb Azure. IoT Central zřídí prostředky v předplatném. Pokud předplatné Azure nemáte, můžete si ho vytvořit na [registrační stránce Azure](https://aka.ms/createazuresubscription). Po vytvoření předplatného Azure přejděte zpátky na stránku **vytvořit aplikaci** . Vaše nové předplatné se zobrazí v rozevíracím seznamu **předplatné Azure** .
      - *Umístění* je [geografická](https://azure.microsoft.com/global-infrastructure/geographies/) oblast, kde byste chtěli vytvořit aplikaci. Obvykle byste měli zvolit umístění, které je fyzicky nejbližší pro vaše zařízení, aby se dosáhlo optimálního výkonu. Služba Azure IoT Central Public Preview je aktuálně dostupná v **USA**nebo v **Evropě**. Jakmile zvolíte umístění, nemůžete později přesunout aplikaci do jiného umístění.
        > [!NOTE]
        > V rámci veřejné verze Preview jsou jediná dostupná umístění pro **aplikace verze Preview** **Evropa** a **USA**.

1. Přečtěte si podmínky a ujednání a v dolní části stránky vyberte **vytvořit** .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili aplikaci IoT Central. Další navrhovaný krok:

> [!div class="nextstepaction"]
> [Přidání simulovaného zařízení do aplikace IoT Central](./quick-create-pnp-device.md)
