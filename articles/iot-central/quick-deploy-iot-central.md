---
title: Vytvoření aplikace Azure IoT Central | Microsoft Docs
description: Vytvořte novou aplikaci Azure IoT Central. Vytvořte zkušební verzi aplikace nebo aplikaci s průběžnými platbami s využitím šablony aplikace.
author: viv-liu
ms.author: viviali
ms.date: 10/31/2018
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 3ffc361421f57b405c284742b662a833b178f9da
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52260319"
---
# <a name="create-an-azure-iot-central-application"></a>Vytvoření aplikace Azure IoT Central

Jako _tvůrce_ můžete pomocí uživatelského rozhraní Azure IoT Central definovat vaši aplikaci Azure IoT Central. V tomto rychlém startu se dozvíte, jak vytvořit aplikaci Azure IoT Central, která obsahuje ukázkovou _šablonu zařízení_ a simulovaná _zařízení_.

Přejděte na stránku [správce aplikací](https://aka.ms/iotcentral) Azure IoT Central. Budete se muset přihlásit pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.

Pokud chcete začít vytvářet novou aplikaci Azure IoT Central, vyberte **New Application** (Nová aplikace). Tím přejdete na stránku **Create Application** (Vytvořit aplikaci).

![Stránka vytvoření aplikace Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate.png)

Vytvoření nové aplikace Azure IoT Central:

1. Zvolte platební plán:
    - **Trial** – Zkušební verze aplikací jsou bezplatné a jejich platnost vyprší po 7 dnech. Před vypršením platnosti je možné je kdykoli převést na průběžné platby.
    - **Pay-As-You-Go** – Aplikace s průběžnými platbami se účtují podle počtu zařízení a prvních 5 zařízení je zdarma.

    Další informace o cenách najdete na [stránce s cenami za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

1. Zvolte popisný název, jako je třeba **Contoso IoT**. Azure IoT Central pro vás vygeneruje jedinečnou předponu URL. Tuto předponu URL můžete změnit, aby byla snáze zapamatovatelná.

1. Zvolte šablonu aplikace. Šablona aplikace může obsahovat předdefinované položky, jako jsou šablony zařízení a řídicí panely, které vám pomůžou začít.
    | Šablona aplikace | Popis |
    | -------------------- | ----------- |
    | Sample Contoso (Ukázka Contoso)       | Vytvoří aplikaci, která zahrnuje již vytvořenou šablonu zařízení pro chladící prodejní automat. Pomocí této šablony můžete začít zkoumat Azure IoT Central. |
    | Sample Devkits (Ukázka Devkits)       | Vytvoří aplikaci s připravenými šablonami zařízení pro připojení zařízení MXChip nebo Raspberry Pi. Tuto šablonu použijte, pokud jste vývojář zařízení a experimentujete s některým z těchto zařízení. |
    | Custom application (Vlastní aplikace)   | Vytvoří prázdnou aplikaci, kterou můžete naplnit vlastními šablonami zařízení a zařízeními. |

1. Pokud vytváříte aplikaci s **průběžnými platbami**, musíte vybrat *adresář*, *předplatné Azure* a *oblast*. 
    - *Adresář* je služba Azure Active Directory, ve které se vaše aplikace vytvoří. Obsahuje identity uživatelů, přihlašovací údaje a další organizační informace. Pokud službu AAD nemáte, vytvoří se pro vás při vytváření předplatného Azure.

    - *Předplatné Azure* umožňuje vytvářet instance služeb Azure. IoT Central bude ve vašem předplatném zřizovat prostředky. Pokud předplatné Azure nemáte, můžete si ho vytvořit na [registrační stránce Azure](https://aka.ms/createazuresubscription). Po vytvoření předplatného Azure se vraťte na stránku **Create Application** (Vytvořit aplikaci). Vaše nové předplatné se zobrazí v rozevírací nabídce **Azure Subscription** (Předplatné Azure).

    - *Oblast* je fyzické umístění, ve kterém chcete aplikaci vytvořit. Obvykle byste pro zajištění optimálního výkonu měli zvolit oblast, která je fyzicky nejblíže vašim zařízením. Seznam oblastí, ve kterých je Azure IoT Central k dispozici, najdete na stránce [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services/).

    > [!Note]
    > Po zvolení oblasti už později nebude možné přesunout aplikaci do jiné oblasti.

1. Klikněte na možnost **Vytvořit**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste vytvořili aplikaci IoT Central. Další navrhovaný krok:

> [!div class="nextstepaction"]
> [Prohlídka IoT Central](https://docs.microsoft.com/azure/iot-central/overview-iot-central-tour)
