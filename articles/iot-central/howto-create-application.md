---
title: Vytvoření aplikace Azure IoT centrální | Microsoft Docs
description: Jako správce jak vytvořit Azure IoT centrální aplikaci.
services: iot-central
author: TanmayBhagwat
ms.author: tanmayb
ms.date: 03/20/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 83879c6b81985f61b9fcff665e9f764c1346592e
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="create-your-azure-iot-central-application"></a>Vytvoření aplikace Azure IoT – střed

Vytvoření aplikace Microsoft Azure IoT centrální z [vytvořit aplikaci](https://apps.microsoftiotcentral.com/create) stránky. Vytvořit aplikaci Azure IoT centrální, musíte vyplnit všechna pole na této stránce a potom zvolte **vytvořit**. Tento článek obsahuje další informace o jednotlivých polí.

![Vytvoření stránky aplikace](media\howto-create-application\image1.png)

## <a name="payment-plan"></a>Plán plateb

Můžete vytvořit a zkušební nebo placené aplikace. Další informace o zkušebních verzí a placená aplikace na této stránce.

## <a name="application-name"></a>Název aplikace

Název aplikace se zobrazí na **Správce aplikací** stránky a v rámci každé Azure IoT centrální aplikaci. Můžete použít libovolný název pro aplikaci Centrální Azure IoT. Zvolte název, který dává smysl pro vás a jiným uživatelům ve vaší organizaci.

## <a name="application-url"></a>Adresa URL aplikace

Adresa URL aplikace je odkaz na aplikaci. Můžete uložit záložku k němu v prohlížeči nebo sdílet s ostatními.

Když zadáte název vaší aplikace, je adresa URL aplikace, automaticky generovaný. Pokud dáváte přednost, můžete na jinou adresu URL pro vaši aplikaci. Jednotlivé Azure IoT centrální adresy URL musí být jedinečný. Zobrazí chybovou zprávu, pokud zvolíte adresa URL se už používá.

## <a name="directory"></a>Adresář

Pouze v placené aplikace.

Zvolte klienta služby Azure Active Directory k vytvoření Azure IoT centrální aplikace. Klient služby Azure Active Directory obsahuje identity uživatelů, pověření a dalších organizační údaje. Víc předplatných Azure může být přidružen jednoho klienta Azure Active Directory.

Pokud nemáte klienta služby Azure Active Directory, jeden se vám vytvoří při vytváření předplatného Azure.

Další informace najdete v tématu [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Předplatné Azure

Předplatné Azure umožňuje vytvoření instance služby Azure. Azure IoT střed automaticky vyhledá všechna předplatná Azure máte přístup k a zobrazí je v rozevíracím seznamu na **vytvořit aplikaci** stránky. Zvolte předplatné Azure k vytvoření nové centrální aplikaci Azure IoT.

Pokud nemáte předplatné Azure, můžete vytvořit jeden na této stránce. Po vytvoření předplatného Azure, přejděte zpět **vytvořit aplikaci** stránky. Vaše nové předplatné se zobrazí v **předplatné Azure** rozevíracího seznamu.

Další informace najdete v tématu [předplatná Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Oblast

Pouze v placené aplikace.

Vyberte oblast, kde chcete vytvořit centrální aplikaci Azure IoT. Obvykle měli byste vybrat oblasti, které je nejblíže fyzicky do zařízení, abyste získali optimální výkon.

Další informace najdete v tématu [oblastí Azure](https://docs.microsoft.com/en-us/azure/guides/developer/azure-developer-guide#azure-regions).

Můžete zobrazit oblasti, ve kterých Azure IoT centrální je k dispozici na [produkty podle oblasti](https://azure.microsoft.com/regions/services/) stránky.

> [!Note]
> Po zvolení oblasti, nemůžete později přesunout vaší aplikace do jiné oblasti.

## <a name="application-template"></a>Šablony aplikace

Vyberte jednu z šablony aplikace k dispozici pro novou aplikaci Azure IoT centrální. Šablony aplikace může obsahovat předdefinovaných položek, jako jsou šablony zařízení a řídicí panely vám pomohou začít:

| Šablony aplikace | Popis |
| -------------------- | ----------- |
| Vlastní aplikace   | Vytvoří prázdnou aplikaci pro vás k naplnění vlastní šablony zařízení a zařízení. |
| Ukázka Contoso       | Vytvoří aplikaci, která obsahuje šablonu zařízení pro jednoduché připojené zařízení. Tuto šablonu použijte začít seznamovat centrální Azure IoT. |
| Ukázka Devkits       | Vytvoří aplikaci se šablonami zařízení připraveno k vám umožní připojit se MXChip nebo malin platformy zařízení. Tuto šablonu použijte, pokud jste vývojář zařízení experimentování s kódem na jedno z těchto zařízení. |

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili postup vytvoření aplikace Azure IoT centrální, zde je navržené další krok:

> [!div class="nextstepaction"]
> [Spravovat aplikace](howto-administer.md)