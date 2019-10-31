---
title: Vytváření a Správa aplikací Azure IoT Central z portálu CSP | Microsoft Docs
description: Jako CSP můžete vytvořit aplikaci Azure IoT Central jménem svého zákazníka.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 8e9c2077960dc64aa7bac13a53e0b3f74062aa55
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177270"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Vytvoření a Správa aplikace Azure IoT Central z portálu CSP

Program poskytovatele řešení Microsoft Cloud (CSP) je program pro prodej v Microsoftu. Jejím záměrem je poskytnout našim partnerům pro partnery, kteří budou moci znovu prodávat všechny komerční online služby Microsoftu. Přečtěte si další informace o [programu Cloud Solution Provider](https://partner.microsoft.com/cloud-solution-provider).

Jako CSP můžete pomocí [partnerského centra Microsoftu](https://partnercenter.microsoft.com/partner/home)vytvářet a spravovat Microsoft Azure IoT Central aplikací jménem zákazníky. Když poskytovatelé cloudu vytvoří aplikace IoT Central Azure jménem zákazníky, stejně jako u jiných služeb Azure spravovaných CSP, můžou CSP spravovat fakturace pro zákazníky. Poplatky za Azure IoT Central se zobrazí v celkovém vyúčtování v partnerském centru Microsoftu.

Začněte tím, že se přihlásíte ke svému účtu na portálu Microsoft Partner Portal a vyberete zákazníka, pro který chcete vytvořit aplikaci Azure IoT Central. Přejděte na správu služby pro zákazníka z levé navigace.

![Centrum partnerů Microsoftu, zobrazení zákazníků](media/howto-create-application-csp/image1.png)

Azure IoT Central je uveden jako služba, kterou je možné spravovat. Na stránce vyberte odkaz Azure IoT Central, abyste mohli vytvářet nové aplikace nebo spravovat existující aplikace pro tohoto zákazníka.

![Správa Azure IoT Central k dispozici pro správu](media/howto-create-application-csp/image2.png)

Na stránce Azure IoT Central Správce aplikací se zakoupíte. Azure IoT Central udržuje kontext, který jste dostali z partnerského centra Microsoftu a kterou jste získali za správu tohoto konkrétního zákazníka. Toto potvrzení se zobrazí v záhlaví stránky Správce aplikací. Z tohoto místa můžete buď přejít na existující aplikaci, kterou jste předtím vytvořili, aby tento zákazník mohl spravovat nebo vytvořit novou aplikaci pro zákazníka.

![Vytvořit správce pro zprostředkovatele CSP](media/howto-create-application-csp/image3.png)

Pokud chcete vytvořit aplikaci Azure IoT Central, v levé nabídce vyberte **sestavit** . Vyberte jednu z šablon idustry nebo vyberte **vlastní aplikace** a vytvořte zcela novou aplikaci. Tím se načte stránka pro vytvoření aplikace. Musíte vyplnit všechna pole na této stránce a pak zvolit **vytvořit**. Další informace o každém z těchto polí najdete v části.

![Vytvořit stránku aplikace pro zprostředkovatele CSP](media/howto-create-application-csp/image4.png)

![Vytvořit stránku aplikace pro zprostředkovatele CSP](media/howto-create-application-csp/image4-1.png)

## <a name="payment-plan"></a>Platební plán

Jako CSP můžete vytvářet jenom aplikace s průběžnými platbami. Pokud chcete předprezentovat Azure IoT Central zákazníkům, můžete si vytvořit zkušební aplikaci samostatně. Přečtěte si další informace o zkušební verzi a aplikacích s průběžnými platbami na [stránce s cenami za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Název aplikace

Název vaší aplikace se zobrazí na stránce **Správce aplikací** a v každé aplikaci IoT Central Azure. Můžete zvolit libovolný název aplikace IoT Central pro Azure. Vyberte si název, který vám dává smysl, a ostatním uživatelům ve vaší organizaci.

## <a name="application-url"></a>Adresa URL aplikace

Adresa URL aplikace je odkaz na vaši aplikaci. Záložku můžete do ní uložit v prohlížeči nebo ji sdílet s ostatními.

Když zadáte název vaší aplikace, adresa URL vaší aplikace se automaticky vygeneruje. Pokud chcete, můžete pro svou aplikaci zvolit jinou adresu URL. Každá adresa URL IoT Central v Azure musí být v rámci Azure IoT Central jedinečná. V případě, že adresa URL, kterou si zvolíte, se zobrazí chybová zpráva.

## <a name="directory"></a>Adresář

Vzhledem k tomu, že Azure IoT Central obsahuje kontext, který jste získali za správu zákazníka vybraného na portálu Microsoft Partner, zobrazí se v poli Adresář pouze Azure Active Directory tenant tohoto zákazníka. 

Tenant Azure Active Directory obsahuje identity uživatelů, přihlašovací údaje a další informace o organizaci. K jednomu Azure Active Directory tenantovi může být přidruženo několik předplatných Azure.

Další informace najdete v tématu [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Předplatné Azure

Předplatné Azure umožňuje vytvářet instance služeb Azure. Azure IoT Central automaticky najde všechna předplatná Azure, ke kterým máte přístup, a zobrazí je v rozevírací nabídce na stránce **vytvořit aplikaci** . Pokud chcete vytvořit novou aplikaci Azure IoT Central, vyberte předplatné Azure.

Pokud nemáte předplatné Azure, můžete ho vytvořit v partnerském centru Microsoftu. Po vytvoření předplatného Azure se vraťte na stránku **Create Application** (Vytvořit aplikaci). Vaše nové předplatné se zobrazí v rozevírací nabídce **Azure Subscription** (Předplatné Azure).

Další informace najdete v tématu [předplatná Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="region"></a>Oblast

Vyberte oblast nebo [zeměpisnou](https://azure.microsoft.com/global-infrastructure/geographies/) oblast, ve které chcete vytvořit aplikaci Azure IoT Central. Obvykle byste měli zvolit oblast, která je pro vaše zařízení nejbližší, aby získala optimální výkon.

> [!NOTE]
> Šablona **aplikace Preview** je aktuálně dostupná pouze v oblastech **Severní Evropa** a **střed USA** .

Další informace najdete v tématu [oblasti Azure](https://azure.microsoft.com/global-infrastructure/regions/) a [Zeměpisná oblast Azure](https://azure.microsoft.com/global-infrastructure/geographies/).

Oblasti, ve kterých je Azure IoT Central k dispozici, můžete zobrazit na stránce [produkty dostupné podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central) .

> [!Note]
> Po zvolení oblasti už později nebude možné přesunout aplikaci do jiné oblasti.

## <a name="application-template"></a>Šablona aplikace

Můžete zvolit jednu z dostupných šablon aplikací pro novou aplikaci Azure IoT Central. Šablona aplikace může obsahovat předdefinované položky, jako jsou šablony zařízení a řídicí panely, které vám pomůžou začít.

| Šablona aplikace | Popis |
| -------------------- | ----------- |
| Custom application (Vlastní aplikace)   | Vytvoří prázdnou aplikaci, kterou můžete naplnit vlastními šablonami zařízení a zařízeními. |
| Sample Contoso (Ukázka Contoso)       | Vytvoří aplikaci, která bude obsahovat šablonu zařízení pro jednoduché připojené zařízení. Pomocí této šablony můžete začít zkoumat Azure IoT Central. |
| Sample Devkits (Ukázka Devkits)       | Vytvoří aplikaci s připravenými šablonami zařízení pro připojení zařízení MXChip nebo Raspberry Pi. Tuto šablonu použijte, pokud jste vývojář zařízení experimentovat s kódem na jednom z těchto zařízení. |

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak vytvořit aplikaci Azure IoT Central jako CSP, je zde doporučený další krok:

> [!div class="nextstepaction"]
> [Správa aplikace](howto-administer.md)
