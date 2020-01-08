---
title: Vytvoření aplikace Azure IoT Central | Microsoft Docs
description: Vytvořte novou aplikaci Azure IoT Central. Vytvořte zkušební verzi aplikace nebo aplikaci s průběžnými platbami s využitím šablony aplikace.
author: viv-liu
ms.author: viviali
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: corywink
ms.openlocfilehash: cb9968d3bcc30fe8e0f0023bcf7101cde5e4a196
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453910"
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

1. Zvolte platební plán:
   - **7 dní bezplatné zkušební verze** aplikace jsou zdarma po dobu sedmi dnů, než vyprší jejich platnost. Můžete je kdykoli převést na **průběžné platby** , a to až do vypršení jejich platnosti. Pokud vytvoříte **zkušební** aplikaci, je nutné zadat své kontaktní informace a vybrat, zda chcete dostávat informace a tipy od společnosti Microsoft.
   - Aplikace s **průběžnými platbami** se účtují podle zařízení a prvních pět zařízení je zdarma. Pokud vytvoříte aplikaci s průběžnými **platbami** , musíte vybrat svůj *adresář*, *předplatné Azure*a *umístění*:
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
