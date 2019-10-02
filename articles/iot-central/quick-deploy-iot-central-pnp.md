---
title: Vytvoření aplikace Azure IoT Central | Microsoft Docs
description: Vytvořte novou aplikaci Azure IoT Central. Vytvořte zkušební verzi aplikace nebo aplikaci s průběžnými platbami s využitím šablony aplikace.
author: viv-liu
ms.author: viviali
ms.date: 06/07/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 72d02cecf37c631e6f8097b220848425c5e1fe9c
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719127"
---
# <a name="create-an-azure-iot-central-application-preview-features"></a>Vytvoření aplikace Azure IoT Central (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

V tomto rychlém startu se dozvíte, jak vytvořit aplikaci IoT Central Azure, která používá funkce verze Preview, jako je například IoT technologie Plug and Play.

> [!WARNING]
> Možnosti IoT technologie Plug and Play v Azure IoT Central jsou momentálně ve verzi Public Preview. Nepoužívejte IoT Central aplikaci technologie Plug and Play IoT povolenou pro produkční úlohy. V produkčních prostředích se používá aplikace IoT Central vytvořená z aktuální, všeobecně dostupné šablony aplikace.

## <a name="create-an-application"></a>Vytvoření aplikace

Přejděte na web [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) . Pak se přihlaste pomocí osobního, pracovního nebo školního účtu Microsoft.

Pokud chcete začít vytvářet novou aplikaci Azure IoT Central, vyberte **New Application** (Nová aplikace). Tento odkaz vás přesměruje na stránku **vytvořit aplikaci** .

![Stránka pro vytvoření aplikace v Azure IoT Central](media/quick-deploy-iot-central-pnp/iotcentralcreate-pnp.png)

Vytvoření nové aplikace IoT Central v Azure, která zahrnuje funkce verze Preview, jako je například IoT technologie Plug and Play:

1. Zvolte platební plán:
   - **Zkušební verze** aplikací je zdarma po dobu sedmi dnů, než vyprší jejich platnost. Můžete je kdykoli převést na **průběžné platby** , a to až do vypršení jejich platnosti. Pokud vytvoříte **zkušební** aplikaci, budete muset zadat své kontaktní informace a vybrat, jestli chcete dostávat informace a tipy od Microsoftu.
   - Aplikace s **průběžnými platbami** se účtují podle zařízení a prvních pět zařízení je zdarma. Pokud vytvoříte aplikaci s průběžnými **platbami** , budete muset vybrat svůj *adresář*, *předplatné Azure*a *oblast*:
        - *Adresář* je Azure Active Directory (AD) k vytvoření aplikace. Obsahuje identity uživatelů, přihlašovací údaje a další organizační informace. Pokud nemáte Azure AD, vytvoří se při vytváření předplatného Azure jedna za vás.
        - *Předplatné Azure* umožňuje vytvářet instance služeb Azure. IoT Central bude ve vašem předplatném zřizovat prostředky. Pokud předplatné Azure nemáte, můžete si ho vytvořit na [registrační stránce Azure](https://aka.ms/createazuresubscription). Po vytvoření předplatného Azure přejděte zpátky na stránku **vytvořit aplikaci** . Vaše nové předplatné se zobrazí v rozevírací nabídce **Azure Subscription** (Předplatné Azure).
        - *Oblast* je fyzické umístění, ve kterém chcete aplikaci vytvořit. Obvykle byste měli zvolit oblast, která je fyzicky nejbližší pro vaše zařízení, aby se dosáhlo optimálního výkonu. Během veřejné verze Preview jsou dostupné jenom oblasti aplikace ve **verzi Preview** , které jsou **Severní Evropa** a **střed USA**. Po výběru oblasti nebude možné aplikaci později přesunout do jiné oblasti.

        Další informace o cenách najdete na [stránce s cenami za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

1. Vyberte šablonu **aplikace Preview** . Šablona aplikace může obsahovat předdefinované položky, jako jsou šablony zařízení a řídicí panely, které vám pomůžou začít.

1. Azure IoT Central automaticky navrhuje název aplikace na základě šablony aplikace, kterou jste vybrali. Můžete přijmout tento název nebo zadat vlastní popisný název aplikace, například **Contoso IoT**. Azure IoT Central také na základě názvu aplikace vygeneruje jedinečnou předponu adresy URL. Tuto předponu adresy URL můžete změnit na něco, co byste si chtěli.

1. Vyplňte další informace požadované pro plán platby, který jste vybrali dříve v kroku 1.

1. V dolní části stránky vyberte **vytvořit** .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili IoT Central aplikaci, která používá funkce verze Preview. Další navrhovaný krok:

> [!div class="nextstepaction"]
> [Definování nového typu zařízení v aplikaci Azure IoT Central](./tutorial-define-device-type-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
