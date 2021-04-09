---
title: Vytváření a Správa aplikací Azure IoT Central z portálu CSP | Microsoft Docs
description: Jako CSP můžete vytvořit aplikaci Azure IoT Central jménem svého zákazníka.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: f3293ada549351cc7273847cde48c0531f06f028
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "104675803"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Vytvoření a Správa aplikace Azure IoT Central z portálu CSP

Program poskytovatele řešení Microsoft Cloud (CSP) je program pro prodej v Microsoftu. Jejím záměrem je poskytnout našim partnerům pro partnery, kteří budou moci znovu prodávat všechny komerční online služby Microsoftu. Přečtěte si další informace o [programu Cloud Solution Provider](https://partner.microsoft.com/cloud-solution-provider).

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Jako CSP můžete pomocí [partnerského centra Microsoftu](https://partnercenter.microsoft.com/partner/home)vytvářet a spravovat Microsoft Azure IoT Central aplikací jménem zákazníky. Když poskytovatelé cloudu vytvoří aplikace IoT Central Azure jménem zákazníky, stejně jako u jiných služeb Azure spravovaných CSP, můžou CSP spravovat fakturace pro zákazníky. Poplatky za Azure IoT Central se zobrazí v celkovém vyúčtování v partnerském centru Microsoftu.

Začněte tím, že se přihlásíte ke svému účtu na portálu Microsoft Partner Portal a vyberete zákazníka, pro který chcete vytvořit aplikaci Azure IoT Central. Přejděte na správu služby pro zákazníka z levé navigace.

![Centrum partnerů Microsoftu, zobrazení zákazníků](media/howto-create-and-manage-applications-csp/image1.png)

Azure IoT Central je uveden jako služba, kterou je možné spravovat. Na stránce vyberte odkaz Azure IoT Central, abyste mohli vytvářet nové aplikace nebo spravovat existující aplikace pro tohoto zákazníka.

![Správa Azure IoT Central k dispozici pro správu](media/howto-create-and-manage-applications-csp/image2.png)

Na stránce Azure IoT Central Správce aplikací se zakoupíte. Azure IoT Central udržuje kontext, který jste dostali z partnerského centra Microsoftu a kterou jste získali za správu tohoto konkrétního zákazníka. Toto potvrzení se zobrazí v záhlaví stránky Správce aplikací. Z tohoto místa můžete buď přejít na existující aplikaci, kterou jste předtím vytvořili, aby tento zákazník mohl spravovat nebo vytvořit novou aplikaci pro zákazníka.

![Vytvořit správce pro zprostředkovatele CSP](media/howto-create-and-manage-applications-csp/image3.png)

Pokud chcete vytvořit aplikaci Azure IoT Central, v levé nabídce vyberte **sestavit** . Vyberte jednu z průmyslových šablon nebo vyberte **vlastní aplikace** a vytvořte zcela novou aplikaci. Tím se načte stránka pro vytvoření aplikace. Musíte vyplnit všechna pole na této stránce a pak zvolit **vytvořit**. Další informace o každém z těchto polí najdete v části.

![Snímek obrazovky, na které se zobrazuje stránka pro sestavení aplikace IoT s vybraným tlačítkem sestavit](media/howto-create-and-manage-applications-csp/image4.png)

![Vytvořit stránku aplikace pro zprostředkovatele CSP](media/howto-create-and-manage-applications-csp/image4-1.png)

![Vytvořit stránku aplikace pro informace o fakturaci CSP](media/howto-create-and-manage-applications-csp/image4-2.png)

## <a name="pricing-plan"></a>Cenový tarif

Můžete vytvářet jenom aplikace, které jako CSP používají standardní cenový plán. Abyste svým zákazníkům předvedli IoT Central Azure, můžete vytvořit aplikaci, která bude používat bezplatný cenový plán zvlášť. Další informace o cenových plánech zdarma a Standard najdete na [stránce s cenami za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Můžete vytvářet jenom aplikace, které jako CSP používají standardní cenový plán. Abyste svým zákazníkům předvedli IoT Central Azure, můžete vytvořit aplikaci, která bude používat bezplatný cenový plán zvlášť. Další informace o cenových plánech zdarma a Standard najdete na [stránce s cenami za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Název aplikace

Název vaší aplikace se zobrazí na stránce **Správce aplikací** a v každé aplikaci IoT Central Azure. Můžete zvolit libovolný název aplikace IoT Central pro Azure. Vyberte si název, který vám dává smysl, a ostatním uživatelům ve vaší organizaci.

## <a name="application-url"></a>Adresa URL aplikace

Adresa URL aplikace je odkaz na vaši aplikaci. Záložku můžete do ní uložit v prohlížeči nebo ji sdílet s ostatními.

Když zadáte název vaší aplikace, adresa URL vaší aplikace se automaticky vygeneruje. Pokud chcete, můžete pro svou aplikaci zvolit jinou adresu URL. Každá adresa URL IoT Central v Azure musí být v rámci Azure IoT Central jedinečná. V případě, že adresa URL, kterou si zvolíte, se zobrazí chybová zpráva.

## <a name="directory"></a>Adresář

Vzhledem k tomu, že Azure IoT Central obsahuje kontext, který jste získali za správu zákazníka vybraného na portálu Microsoft Partner, zobrazí se v poli Adresář pouze Azure Active Directory tenant tohoto zákazníka. 

Tenant Azure Active Directory obsahuje identity uživatelů, přihlašovací údaje a další informace o organizaci. K jednomu Azure Active Directory tenantovi může být přidruženo několik předplatných Azure.

Další informace najdete v tématu [Azure Active Directory](../../active-directory/index.yml).

## <a name="azure-subscription"></a>Předplatné Azure

Předplatné Azure vám umožňuje vytvářet instance služeb Azure. Azure IoT Central automaticky najde všechna předplatná Azure, ke kterým máte přístup, a zobrazí je v rozevírací nabídce na stránce **vytvořit aplikaci** . Pokud chcete vytvořit novou aplikaci Azure IoT Central, vyberte předplatné Azure.

Pokud nemáte předplatné Azure, můžete ho vytvořit v partnerském centru Microsoftu. Po vytvoření předplatného Azure se vraťte na stránku **Create Application** (Vytvořit aplikaci). Vaše nové předplatné se zobrazí v rozevírací nabídce **Azure Subscription** (Předplatné Azure).

Další informace najdete v tématu [předplatná Azure](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing).

## <a name="location"></a>Umístění

**Umístění** je [geografická](https://azure.microsoft.com/global-infrastructure/geographies/) oblast, kde chcete vytvořit aplikaci. Obvykle byste měli zvolit umístění, které je fyzicky nejbližší pro vaše zařízení, aby se dosáhlo optimálního výkonu. V současné době můžete vytvořit aplikaci IoT Central v oblastech **Austrálie**, **Asie a Tichomoří**, **Evropa**, **USA**, **Spojené království** a **Japonsko** . Jakmile zvolíte umístění, nemůžete později přesunout aplikaci do jiného umístění.

## <a name="application-template"></a>Šablona aplikace

Vyberte šablonu aplikace, kterou chcete použít pro vaši aplikaci.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak vytvořit aplikaci Azure IoT Central jako CSP, je zde doporučený další krok:

> [!div class="nextstepaction"]
> [Správa vaší aplikace](howto-administer.md)