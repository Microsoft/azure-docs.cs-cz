---
title: Vytvoření aplikace Azure IoT Central | Microsoft Docs
description: Jako správce jak vytvořit aplikaci Azure IoT Central.
services: iot-central
ms.service: iot-central
author: tbhagwat3
ms.author: tanmayb
ms.date: 07/09/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 1fbe3ea142e1dd738cd341f57d2b8f48b539ac75
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39003762"
---
# <a name="create-your-azure-iot-central-application"></a>Vytvoření aplikace Azure IoT Central

Při vytváření aplikace z Microsoft Azure IoT Central [vytvořit aplikaci](https://apps.microsoftiotcentral.com/create) stránky. K vytvoření aplikace Azure IoT Central, musíte vyplnit všechna pole na této stránce a klikněte na tlačítko **vytvořit**. Zjistíte další informace o každé z níže uvedených polí.

![Vytvoření stránky aplikace](media\howto-create-application\image1.png)

## <a name="payment-plan"></a>Plán plateb

Můžete vytvořit zkušební nebo placené aplikace. Další informace o zkušebních verzí a placená aplikace na [Azure IoT Central stránce s cenami](https://azure.microsoft.com/pricing/details/iot-central/)...

## <a name="application-name"></a>Název aplikace

Název aplikace se zobrazí na **Správce aplikací** stránky a v každé aplikaci Azure IoT Central. Můžete zvolit libovolný název aplikace Azure IoT Central. Zvolte název, který dává smysl a ostatním uživatelům ve vaší organizaci.

## <a name="application-url"></a>Adresa URL aplikace

Adresa URL aplikace je odkaz na vaši aplikaci. Můžete uložit záložku v prohlížeči nebo sdílet s ostatními.

Pokud zadáte název vaší aplikace, adresa URL vaší aplikace se generuje automaticky. Pokud dáváte přednost, můžete vybrat jinou adresu URL pro aplikaci. Každá Azure IoT Central adresa URL musí být jedinečný v rámci Azure IoT Central. Pokud zvolíte adresa URL již byla přijata se zobrazí chybová zpráva.

## <a name="directory"></a>Adresář

Pouze v placené aplikace.

Zvolte tenanta služby Azure Active Directory vytvořte aplikaci Azure IoT Central. Klient služby Azure Active Directory obsahuje identity uživatelů, pověření a dalších informací v organizaci. Více předplatných Azure lze přidružit jednoho tenanta Azure Active Directory.

Pokud nemáte tenanta služby Azure Active Directory, vytvoří se pro vás při vytváření předplatného Azure.

Další informace najdete v tématu [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Předplatné Azure

Předplatné Azure umožňuje vytvářet instance služby Azure. Azure IoT Central automaticky zjistí Všechna předplatná Azure máte přístup k a zobrazí je v rozevírací seznam na **vytvořit aplikaci** stránky. Zvolte předplatné Azure a vytvořte novou aplikaci Centrální IoT Azure.

Pokud nemáte předplatné Azure, můžete jeden vytvořit na [Azure registrační stránku](https://aka.ms/createazuresubscription). Po vytvoření předplatného Azure, přejděte zpět **vytvořit aplikaci** stránky. Nové předplatné se zobrazí v **předplatné Azure** rozevíracího seznamu.

Další informace najdete v tématu [předplatná Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Oblast

Pouze v placené aplikace.

Vyberte oblast, ve kterém chcete vytvořit aplikaci Azure IoT Central. Obvykle měli byste vybrat oblast, které je nejblíže fyzicky svoje zařízení, abyste získali optimální výkon.

Další informace najdete v tématu [oblastí Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#azure-regions).

Zobrazí se oblasti Azure IoT Central je k dispozici na [dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/regions/services/) stránky.

> [!Note]
> Po zvolení oblast později se nedá přesunout vaše aplikace v jiné oblasti.

## <a name="application-template"></a>Šablona aplikace

Můžete vybrat některou ze šablon dostupné aplikace pro novou aplikaci Azure IoT Central. Šablony aplikace může obsahovat předdefinované položky, jako jsou šablony zařízení a řídicí panely vám pomůžou začít.

| Šablona aplikace | Popis |
| -------------------- | ----------- |
| Vlastní aplikace   | Vytvoří prázdnou aplikaci pro vás bude vyplněn vlastní šablony zařízení a zařízení. |
| Ukázka Contoso       | Vytvoří aplikaci, která obsahuje zařízení šablony pro jednoduchou připojené zařízení. Pomocí této šablony můžete začít zkoumat Azure IoT Central. |
| Ukázka Devkits       | Vytvoří aplikaci s využitím šablon zařízení připraven pro připojení MXChip nebo Raspberry Pi zařízení. Tuto šablonu použijte, pokud jste vývojář zařízení experimentovat s kódem na jedno z těchto zařízení. |

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak vytvořit aplikaci Azure IoT Central, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Spravovat aplikaci](howto-administer.md)