---
title: Vytváření a správa aplikací Azure IoT Central z portálu CSP | Dokumenty společnosti Microsoft
description: Jako csp, jak vytvořit aplikaci Azure IoT Central jménem svého zákazníka.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 02481d5dcbaba15c9b17a27348207d9af64f3355
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982034"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Vytvoření a správa aplikace Azure IoT Central z portálu CSP

Program Zprostředkovatel e-cloudových řešení (CSP) společnosti Microsoft je program prodejce microsoftu. Jeho záměrem je poskytnout našim obchodním partnerům program na jednom zastávku pro další prodej všech komerčních online služeb společnosti Microsoft. Další informace o [programu Poskytovatele cloudových řešení](https://partner.microsoft.com/cloud-solution-provider).

Jako csp můžete vytvářet a spravovat aplikace Microsoft Azure IoT Central jménem svých zákazníků prostřednictvím [Centra partnerů společnosti Microsoft](https://partnercenter.microsoft.com/partner/home). Když jsou aplikace Azure IoT Central vytvářeny jménem zákazníků csp, stejně jako u jiných služeb Azure spravovaných csp, správou služeb Kisp správou fakturace pro zákazníky. Poplatek za Azure IoT Central se zobrazí ve vaší celkové vyúčtování v Centru partnerů Microsoftu.

Chcete-li začít, přihlaste se ke svému účtu na portálu microsoft partner a vyberte zákazníka, pro kterého chcete vytvořit aplikaci Azure IoT Central. Přejděte na služby management pro zákazníka z levé navigace.

![Centrum partnerů společnosti Microsoft, zobrazení pro zákazníky](media/howto-create-and-manage-applications-csp/image1.png)

Azure IoT Central je uveden jako služba, která je k dispozici pro správu. Vyberte odkaz Azure IoT Central na stránce a vytvořte nové aplikace nebo spravujte stávající aplikace pro tohoto zákazníka.

![Azure IoT Central, který je k dispozici pro správu](media/howto-create-and-manage-applications-csp/image2.png)

Přistanete na stránce Azure IoT Central Application Manager. Azure IoT Central udržuje kontext, který jste přišli z Centra partnerů Microsoftu a že jste přišli spravovat konkrétního zákazníka. Zobrazí se v záhlaví stránky Správce aplikací. Zde můžete přejít na existující aplikaci, kterou jste vytvořili dříve, aby tento zákazník mohl spravovat, nebo vytvořit novou aplikaci pro zákazníka.

![Vytvořit správce pro ošetřovně](media/howto-create-and-manage-applications-csp/image3.png)

Pokud chcete vytvořit aplikaci Azure IoT Central, v levém menu vyberte **Build.** Vyberte jednu ze šablon odvětví nebo zvolte **Vlastní aplikaci** a vytvořte aplikaci od začátku. Tím se načte stránka Vytvoření aplikace. Musíte vyplnit všechna pole na této stránce a pak zvolte **Vytvořit**. Další informace o jednotlivých polích naleznete níže.

![Vytvořit stránku aplikace pro ospravky](media/howto-create-and-manage-applications-csp/image4.png)

![Vytvořit stránku aplikace pro ospravky](media/howto-create-and-manage-applications-csp/image4-1.png)

![Vytvořit stránku aplikace pro fakturační údaje kostit](media/howto-create-and-manage-applications-csp/image4-2.png)

## <a name="pricing-plan"></a>Cenový plán

Můžete vytvářet pouze aplikace, které používají standardní cenový plán jako csp. Pokud chcete zákazníkovi předvést Azure IoT Central, můžete vytvořit aplikaci, která používá bezplatný cenový plán samostatně. Další informace o bezplatných a standardních cenových plánech najdete na [stránce s cenami Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

Můžete vytvářet pouze aplikace, které používají standardní cenový plán jako csp. Pokud chcete zákazníkovi předvést Azure IoT Central, můžete vytvořit aplikaci, která používá bezplatný cenový plán samostatně. Další informace o bezplatných a standardních cenových plánech najdete na [stránce s cenami Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>Název aplikace

Název vaší aplikace se zobrazí na stránce **Správce aplikací** a v rámci každé aplikace Azure IoT Central. Můžete zvolit libovolný název pro vaši aplikaci Azure IoT Central. Vyberte jméno, které dává smysl vám i ostatním uživatelům ve vaší organizaci.

## <a name="application-url"></a>Adresa URL aplikace

Adresa URL aplikace je odkaz na vaši aplikaci. Záložku si do ní můžete uložit do prohlížeče nebo ji sdílet s ostatními.

Když zadáte název aplikace, bude automaticky vygenerována adresa URL aplikace. Pokud chcete, můžete pro aplikaci zvolit jinou adresu URL. Každá adresa URL Azure IoT Central musí být v rámci Azure IoT Central jedinečná. Pokud již byla přijata adresa URL, kterou jste zvolili, zobrazí se chybová zpráva.

## <a name="directory"></a>Adresář

Vzhledem k tomu, že Azure IoT Central má kontext, který jste přišli ke správě zákazníka, kterého jste vybrali na portálu Microsoft Partner Portal, zobrazí se v poli Adresář jenom tenant azure active directory pro tohoto zákazníka. 

Tenant Azure Active Directory obsahuje identity uživatelů, přihlašovací údaje a další informace o organizaci. K jednomu tenantovi Služby Azure služby Active Directory lze přidružit více předplatných Azure.

Další informace najdete v [tématu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/).

## <a name="azure-subscription"></a>Předplatné Azure

Předplatné Azure vám umožňuje vytvářet instance služeb Azure. Azure IoT Central automaticky vyhledá všechna předplatná Azure zákazníka, ke kterému máte přístup, a zobrazí je v rozevíracím seznamu na stránce **Vytvořit aplikaci.** Zvolte předplatné Azure a vytvořte novou centrální aplikaci Azure IoT.

Pokud nemáte předplatné Azure, můžete ho vytvořit v Centru partnerů Microsoftu. Po vytvoření předplatného Azure se vraťte na stránku **Create Application** (Vytvořit aplikaci). Vaše nové předplatné se zobrazí v rozevírací nabídce **Azure Subscription** (Předplatné Azure).

Další informace najdete v [tématu Předplatná Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing).

## <a name="location"></a>Umístění

**Umístění** je [zeměpis,](https://azure.microsoft.com/global-infrastructure/geographies/) kde chcete vytvořit aplikaci. Obvykle byste měli zvolit umístění, které je fyzicky nejblíže k vašim zařízením, abyste získali optimální výkon. V současné době můžete vytvořit aplikaci IoT Central v **oblastech Zeměpisné**oblasti Austrálie , Asie **a Tichomoří**, **Evropa**, **Spojené státy**, **Velká Británie**a **Japonsko.** Jakmile zvolíte umístění, nemůžete později přesunout aplikaci do jiného umístění.

## <a name="application-template"></a>Šablona aplikace

Vyberte šablonu aplikace, kterou chcete použít pro vaši aplikaci.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak vytvořit aplikaci Azure IoT Central jako csp, tady je navrhovaný další krok:

> [!div class="nextstepaction"]
> [Správa vaší aplikace](howto-administer.md)
