---
title: Vytvoření aplikace Azure IoT Central | Microsoft Docs
description: Vytvořte novou aplikaci Azure IoT Central. Vytvořte zkušební verzi aplikace nebo aplikaci s průběžnými platbami s využitím šablony aplikace.
author: lmasieri
ms.author: lmasieri
ms.date: 10/24/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: c639cb059d773042b7f45160dea18bfc2130cae9
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893806"
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

    > [!NOTE]
    > Pokud používáte šablonu vlastní aplikace, zobrazí se pole rozevírací seznam **Šablona aplikace** . Odsud můžete přepínat mezi verzí Preview a všeobecně dostupnými šablonami. Můžete se také podívat na další šablony, které jsou pro vaši organizaci k dispozici.

1. Vyberte, jestli chcete tuto aplikaci vytvořit pomocí bezplatné zkušební verze 7 dní, nebo použijte předplatné s průběžnými platbami.
    - **Zkušební verze** aplikací jsou po dobu sedmi dnů zdarma a podporují až pět zařízení. Před vypršením platnosti je možné je kdykoli převést na průběžné platby. Pokud vytvoříte zkušební aplikaci, je nutné zadat své kontaktní informace a vybrat, zda chcete dostávat informace a tipy od společnosti Microsoft.
    - Aplikace s **průběžnými platbami** se účtují podle jednotlivých zařízení a první dvě zařízení jsou zdarma. Přečtěte si další informace o [cenách IoT Central](https://aka.ms/iotcentral-pricing). Pokud vytvoříte aplikaci s průběžnými platbami, budete muset vybrat svůj *adresář*, *předplatné Azure*a *oblast*:
        - *Adresář* je Azure Active Directory (AAD), ve kterém vytvoříte aplikaci. Služba Azure AD obsahuje identity uživatelů, přihlašovací údaje a další informace o organizaci. Pokud nemáte Azure AD, vytvoří se při vytváření předplatného Azure jedna za vás.
        - *Předplatné Azure* umožňuje vytvářet instance služeb Azure. IoT Central zřídí prostředky v předplatném. Pokud předplatné Azure nemáte, můžete si ho vytvořit na [registrační stránce Azure](https://aka.ms/createazuresubscription). Po vytvoření předplatného Azure přejděte zpátky na stránku **vytvořit aplikaci** . Vaše nové předplatné se zobrazí v rozevíracím seznamu **předplatné Azure** .
        - *Oblast* je fyzické místo, kde se budou ukládat data vašich zařízení. Obvykle byste měli zvolit oblast, která je fyzicky nejblíže vašim zařízením, aby dosáhla optimálního výkonu a zajistila dodržování předpisů suverenity dat. Po výběru oblasti nebude možné aplikaci později přesunout do jiné oblasti.

        > [!NOTE]
        > Během veřejné verze Preview jsou dostupné jenom oblasti **aplikace ve verzi Preview** , které jsou **Severní Evropa** a **střed USA**.

1. Přečtěte si podmínky a ujednání a v dolní části stránky vyberte **vytvořit** .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili aplikaci IoT Central. Další navrhovaný krok:

> [!div class="nextstepaction"]
> [Přidání simulovaného zařízení do aplikace IoT Central](./quick-create-pnp-device.md)
