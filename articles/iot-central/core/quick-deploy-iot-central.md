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
ms.openlocfilehash: 5268f5438c005033f9c6ecf74657dc1a01d3b673
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989384"
---
# <a name="create-an-azure-iot-central-application"></a>Vytvoření aplikace Azure IoT Central

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Jako _tvůrce_ můžete pomocí uživatelského rozhraní Azure IoT Central definovat vaši aplikaci Azure IoT Central. V tomto rychlém startu se dozvíte, jak vytvořit aplikaci IoT Central Azure, která obsahuje ukázkovou _šablonu zařízení_. Vytvořená aplikace nepoužívá žádné funkce verze Preview.

## <a name="create-an-application"></a>Vytvoření aplikace

Přejděte na web [Azure IoT Central Build](https://aka.ms/iotcentral) . Pak se přihlaste pomocí osobního, pracovního nebo školního účtu Microsoft.

Pokud chcete začít vytvářet aplikaci IoT Central Azure bez povolených funkcí ve verzi Preview, vyberte **Build (sestavit**). Tento odkaz vás přesměruje na stránku **sestavení aplikace IoT** .

![Stránka sestavení Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate.png)

Pak vyberte **vlastní aplikace**.

Vytvoření nové aplikace Azure IoT Central:

1. Azure IoT Central automaticky navrhuje název aplikace na základě šablony aplikace, kterou jste vybrali. Můžete přijmout tento název nebo zadat vlastní popisný název aplikace, například **Contoso IoT**. Azure IoT Central také na základě názvu aplikace vygeneruje jedinečnou adresu URL. Tuto předponu adresy URL můžete změnit na něco, co byste si chtěli.

1. Vyberte šablonu **starší verze aplikace** , která nepoužívá funkce verze Preview.

    | Šablona aplikace | Popis |
    | -------------------- | ----------- |
    | Starší verze aplikace   | Vytvoří prázdnou aplikaci, kterou můžete naplnit vlastními šablonami zařízení a zařízeními. |

    ![Nová aplikace v Azure IoT Central](media/quick-deploy-iot-central/newapplication.png)

    ![Informace o fakturaci IoT Central Azure](media/quick-deploy-iot-central/billinginfo.png)

1. Tuto aplikaci můžete vytvořit pomocí cenového plánu bezplatné zkušební verze 7 dní, případně podle standardních cenových plánů:
   - Aplikace vytvořené pomocí *bezplatného* plánu jsou po dobu sedmi dnů zdarma a podporují až pět zařízení. Můžete je kdykoli převést na používání standardního cenového plánu, než vyprší jejich platnost.
   - Aplikace, které vytvoříte pomocí plánu *Standard* , se účtují podle jednotlivých zařízení. můžete zvolit Cenový tarif **Standard 1** nebo **Standard 2** s prvními dvěma zařízeními, která jsou zdarma. Další informace o cenových plánech zdarma a Standard najdete na [stránce s cenami za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). Pokud vytvoříte standardní aplikaci, budete muset vybrat svůj *adresář*, *předplatné Azure*a *umístění*:
        - *Adresář* je Azure Active Directory (AD) k vytvoření aplikace. Obsahuje identity uživatelů, přihlašovací údaje a další organizační informace. Pokud nemáte Azure AD, vytvoří se při vytváření předplatného Azure jedna za vás.
        - *Předplatné Azure* umožňuje vytvářet instance služeb Azure. IoT Central zřídí prostředky v předplatném. Pokud předplatné Azure nemáte, můžete si ho vytvořit na [registrační stránce Azure](https://aka.ms/createazuresubscription). Po vytvoření předplatného Azure přejděte zpátky na stránku **vytvořit aplikaci** . Vaše nové předplatné se zobrazí v rozevírací nabídce **Azure Subscription** (Předplatné Azure).
        - *Umístění* je [geografická](https://azure.microsoft.com/global-infrastructure/geographies/) oblast, kde byste chtěli vytvořit aplikaci. Obvykle byste měli zvolit umístění, které je fyzicky nejbližší pro vaše zařízení, aby se dosáhlo optimálního výkonu. Služba Azure IoT Central je aktuálně dostupná v **USA**, **Austrálii**, **Asie a Tichomoří**nebo v **Evropě**.  Jakmile zvolíte umístění, nemůžete později přesunout aplikaci do jiného umístění.
        Další informace o cenách najdete na [stránce s cenami za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

1. Vyplňte další informace požadované pro plán platby, který jste vybrali dříve v kroku 1.

1. V dolní části stránky vyberte **vytvořit** .

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili aplikaci IoT Central. Další navrhovaný krok:

> [!div class="nextstepaction"]
> [Definování nového typu zařízení v aplikaci Azure IoT Central](./tutorial-define-device-type.md)
