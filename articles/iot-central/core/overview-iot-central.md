---
title: Co je Azure IoT Central | Microsoft Docs
description: Azure IoT Central je aplikační platforma IoT, která zjednodušuje vytváření řešení IoT a pomáhá snižovat zatížení a náklady na operace správy IoT a vývoj. Tento článek obsahuje přehled funkcí Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/10/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: ffaab22efb2f6969f03720abba4a7afc6387021a
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758220"
---
# <a name="what-is-azure-iot-central"></a>Co je Azure IoT Central?

IoT Central je aplikační platforma IoT, která snižuje zátěž a náklady na vývoj, správu a údržbu řešení IoT na podnikové úrovni. Pokud se rozhodnete stavět pomocí IoT Central, můžete se soustředit čas, peníze a energii na transformaci vašeho podnikání pomocí dat IoT, místo toho, abyste jen udržovali a aktualizovali složitou a neustále se vyvíjející infrastrukturu IoT.

Webové uživatelské rozhraní umožňuje sledovat podmínky zařízení, vytvářet pravidla a spravovat miliony zařízení a jejich dat během celého životního cyklu. Kromě toho umožňuje jednat na základě přehledů zařízení rozšířením ioT inteligence do obchodních aplikací.

Tento článek popisuje pro IoT Central:

- Typické osoby přidružené k projektu
- Postup vytvoření aplikace
- Postup připojení zařízení do aplikace
- Postup správy aplikace
- Možnosti Azure IoT Edge v IoT Central.
- Jak připojit runtime zařízení Azure IoT Edge k vaší aplikaci.

## <a name="known-issues"></a>Známé problémy

- Nepřetržitý export dat nepodporuje formát Avro (nekompatibilita).
- GeoJSON není aktuálně podporován.
- Dlaždice mapy není momentálně podporována.
- Úlohy nepodporují složité typy.
- Typy schématu pole nejsou podporovány.
- Podporována je pouze sada SDK zařízení C a sada SDK sady Node.js.
- IoT Central je momentálně dostupný ve Spojených státech, Evropě, Asii a Tichomoří, Austrálii, Velké Británii a Japonsku.
- Šablonu aplikace **Vlastní aplikace (starší verze)** nelze použít v umístění chodnících Ve Spojeném království a Japonsku.
- Modely schopností zařízení musí mít všechna rozhraní definovaná v jednom souboru.
- Podpora pro [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) je ve verzi Preview a je podporovaná jenom ve vybraných oblastech.

## <a name="personas"></a>Osoby

Dokumentace IoT Central odkazuje na čtyři osoby, které interagují s aplikací IoT Central:

- _Tvůrce řešení_ je zodpovědný za definování typů zařízení, které se připojují k aplikaci a přizpůsobení aplikace pro operátora.
- _Operátor_ spravuje zařízení připojená k aplikaci.
- _Správce_ je zodpovědný za úlohy správy, jako je například správa [uživatelských rolí a oprávnění](howto-administer.md) v rámci aplikace.
- _Vývojář zařízení_ vytvoří kód, který běží na zařízení nebo modulIOT Edge připojený k vaší aplikaci.

## <a name="create-your-iot-central-application"></a>Vytvoření aplikace IoT Central

Jako tvůrce řešení používáte IoT Central k vytvoření vlastního řešení IoT hostovaného v cloudu pro vaši organizaci. Vlastní řešení IoT se obvykle skládá z těchto součástí:

- Cloudová aplikace, která přijímá ze zařízení telemetrická data a umožňuje vám tato zařízení spravovat
- Více zařízení připojených k vaší cloudové aplikaci, na kterých běží vlastní kód

Můžete rychle nasadit novou aplikaci IoT Central a pak ji přizpůsobit podle konkrétních požadavků v prohlížeči. Můžete začít s obecnou _šablonou aplikace_ nebo s některou z šablon aplikací zaměřených na odvětví pro [maloobchod](../retail/overview-iot-central-retail-pnp.md), [energetiku](../energy/overview-iot-central-energy.md), [státní správu](../government/overview-iot-central-government.md)nebo [zdravotní péči](../healthcare/overview-iot-central-healthcare.md).

Jako tvůrce řešení použijete webové nástroje k vytvoření _šablony zařízení_ pro zařízení, která se připojují k vaší aplikaci. Šablona zařízení je podrobný plán, který definuje charakteristiky a chování typu zařízení, jako je:

- Telemetrie, co posílá.
- Obchodní vlastnosti, které můžete upravit operátor
- Vlastnosti zařízení nastavené zařízením, které jsou v aplikaci jen pro čtení
- Vlastnosti, že operátor nastaví, které určují chování zařízení.

Tato šablona zařízení obsahuje:

- _Model schopností zařízení,_ který popisuje možnosti zařízení by měl implementovat, jako je například telemetrie odesílá a vlastnosti, které hlásí.
- Cloudové vlastnosti, které nejsou uloženy v zařízení.
- Vlastní nastavení, řídicí panely a formuláře, které jsou součástí aplikace IoT Central.

### <a name="pricing"></a>Ceny

Aplikaci IoT Central můžete vytvořit pomocí 7denní bezplatné zkušební verze nebo použít standardní cenový plán.

- Aplikace, které vytvoříte pomocí *bezplatného* plánu, jsou zdarma po dobu sedmi dnů a podporují až pět zařízení. Můžete je převést tak, aby používaly standardní cenový plán kdykoli před vypršením jejich platnosti.
- Aplikace, které vytvoříte pomocí *standardního* plánu, se účtují podle zařízení, můžete zvolit cenový plán **Standard 1** nebo **Standard 2,** přičemž první dvě zařízení budou zdarma. Další informace o bezplatných a standardních cenových plánech najdete na [stránce s cenami Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

### <a name="create-device-templates"></a>Vytváření šablon zařízení

[IoT Plug and Play (preview)](../../iot-pnp/overview-iot-plug-and-play.md) umožňuje IoT Central integrovat zařízení, aniž byste museli psát nějaký vložený kód zařízení. Jádrem IoT Plug and Play (preview) je schéma modelu schopností zařízení, které popisuje možnosti zařízení. V aplikaci IoT Central používají šablony zařízení tyto modely funkcí zařízení IoT Plug and Play (preview).

Jako tvůrce řešení máte několik možností pro vytváření šablon zařízení:

- Importujte model schopností zařízení z [katalogu zařízení Azure Certified for IoT](https://aka.ms/iotdevcat) a pak přidejte všechny vlastnosti, vlastní nastavení a řídicí panely, které vaše aplikace IoT Central potřebuje.
- Navrhněte šablonu zařízení v ioT central a pak implementujte model schopností zařízení v kódu zařízení.
- Vytvořte model schopností zařízení pomocí kódu sady Visual Studio a publikujte model do úložiště. Implementujte kód zařízení z modelu a připojte zařízení k aplikaci IoT Central. IoT Central najde model schopností zařízení z úložiště a vytvoří pro vás jednoduchou šablonu zařízení.
- Vytvořte model schopností zařízení pomocí kódu sady Visual Studio. Implementujte kód zařízení z modelu. Ručně importujte model schopností zařízení do aplikace IoT Central a pak přidejte všechny vlastnosti cloudu, vlastní nastavení a řídicí panely, které vaše aplikace IoT Central potřebuje.

Jako tvůrce řešení můžete pomocí IoT Central generovat kód pro testovací zařízení k ověření šablon zařízení.

### <a name="customize-the-ui"></a>Přizpůsobení uživatelského rozhraní

Jako tvůrce řešení můžete také přizpůsobit ustavovací nastavení aplikace IoT Central pro operátory, kteří jsou zodpovědní za každodenní použití aplikace. Vlastní nastavení, která může tvůrce řešení provádět, zahrnují:

- Definovat rozložení vlastností a nastavení v šabloně zařízení
- Nakonfigurovat vlastní řídicí panely, které operátorům pomůžou rychleji zjišťovat informace a řešit problémy
- Nakonfigurovat vlastní analýzy pro zkoumání časových řad dat z připojených zařízení

## <a name="pricing"></a>Ceny

Aplikaci IoT Central můžete vytvořit pomocí 7denní bezplatné zkušební verze nebo použít standardní cenový plán.

- Aplikace, které vytvoříte pomocí *bezplatného* plánu, jsou zdarma po dobu sedmi dnů a podporují až pět zařízení. Můžete je převést tak, aby používaly standardní cenový plán kdykoli před vypršením jejich platnosti.
- Aplikace, které vytvoříte pomocí *standardního* plánu, se účtují podle zařízení, můžete zvolit cenový plán **Standard 1** nebo **Standard 2,** přičemž první dvě zařízení budou zdarma. Další informace o [cenách IoT Central](https://aka.ms/iotcentral-pricing).

## <a name="connect-your-devices"></a>Připojení zařízení

Azure IoT Central používá [službu Azure IoT Hub Device Provisioning (DPS)](../../iot-dps/about-iot-dps.md) ke správě všech registrací a připojení zařízení.

Použití DPS umožňuje:

- IoT Central pro podporu onboardingu a připojování zařízení ve velkém měřítku.
- Můžete generovat přihlašovací údaje zařízení a konfigurovat zařízení offline bez registrace zařízení prostřednictvím ioT centrální ui.
- Zařízení pro připojení pomocí sdílených přístupových podpisů.
- Zařízení pro připojení pomocí standardních certifikátů X.509.
- K registraci zařízení v IoT Central můžete používat vlastní ID zařízení. Použití vlastních ID zařízení zjednodušuje integraci se stávajícími back-office systémy.
- Jediný konzistentní způsob připojení zařízení k IoT Central.

Další informace najdete [v tématu Připojení k Azure IoT Central](./concepts-get-connected.md).

### <a name="azure-iot-edge-devices"></a>Zařízení Azure IoT Edge

Kromě zařízení vytvořených pomocí sad [Azure IoT SDK](https://github.com/Azure/azure-iot-sdks)můžete zařízení [Azure IoT Edge](../../iot-edge/about-iot-edge.md) připojit také k aplikaci IoT Central. Azure IoT Edge umožňuje spouštět cloudové inteligence a vlastní logiku přímo na zařízeních IoT spravovaných IoT Central. Runtime IoT Edge umožňuje:

- Nainstalujte a aktualizujte úlohy v zařízení.
- Udržujte na zařízení standardy zabezpečení Azure IoT Edge.
- Ujistěte se, že moduly IoT Edge jsou vždy spuštěné.
- Oznamte stav modulu do cloudu pro vzdálené monitorování.
- Spravujte komunikaci mezi zařízeními s navazujícími listy a zařízením IoT Edge, mezi moduly na zařízení IoT Edge a mezi zařízením IoT Edge a cloudem.

Další informace najdete v [tématu zařízení Azure IoT Edge a IoT Central](concepts-architecture.md#azure-iot-edge-devices).

## <a name="stay-connected"></a>Stálé připojení

Aplikace IoT Central jsou plně hostovány společností Microsoft, což snižuje administrativní nároky na správu aplikací.

Jako operátor používáte aplikaci IoT Central ke správě zařízení v řešení IoT Central. Operátoři dělají úkoly, jako jsou:

- Monitorovat zařízení připojená k aplikaci
- Řešit potíže a opravovat problémy se zařízeními
- Zřizovat nová zařízení

Jako tvůrce řešení můžete definovat vlastní pravidla a akce, které fungují přes datové proudy z připojených zařízení. Operátor může tato pravidla povolit nebo zakázat na úrovni zařízení za účelem řízení a automatizace úloh v rámci aplikace.

Správci spravují přístup k vaší aplikaci pomocí [uživatelských rolí a oprávnění](howto-administer.md).

S jakýmkoli řešením IoT navrženým pro provoz ve velkém měřítku je důležitý strukturovaný přístup ke správě zařízení. Nestačí jen připojit zařízení ke cloudu, musíte mít svá zařízení připojená a zdravá. Operátor může ke správě vašich zařízení během celého životního cyklu aplikace použít následující funkce IoT Central:

### <a name="dashboards"></a>Řídicí panely 

Integrované [řídicí panely](./howto-set-up-template.md#generate-default-views) poskytují přizpůsobitelné rozhraní pro monitorování stavu zařízení a telemetrie. Začněte s předem sestaveným řídicím panelem v [šabloně aplikace](howto-use-app-templates.md) nebo si vytvořte vlastní řídicí panely přizpůsobené potřebám vašich operátorů. Řídicí panely můžete sdílet se všemi uživateli ve vaší aplikaci nebo je zachovat v soukromí.

### <a name="rules-and-actions"></a>Pravidla a akce 

Vytvořte [vlastní pravidla](tutorial-create-telemetry-rules.md) založená na stavu zařízení a telemetrii k identifikaci zařízení, která potřebují pozornost. Nakonfigurujte akce, které upozorní správné osoby a zajistí, aby byla nápravná opatření přijata včas.

### <a name="jobs"></a>Úlohy 

[Úlohy](howto-run-a-job.md) umožňují použít jednoduché nebo hromadné aktualizace pro zařízení nastavením vlastností nebo voláním příkazů. 

### <a name="user-roles-and-permissions"></a>Role a oprávnění uživatelů

[Role a oprávnění](howto-manage-users-roles.md) umožňují správci přizpůsobit prostředí každého uživatele. Správce používá webové uživatelské rozhraní k vytvoření rolí a přiřazení oprávnění.

## <a name="transform-your-iot-data"></a>Transformace dat IoT

IoT Central jako aplikační platforma umožňuje transformovat vaše data IoT do obchodních přehledů, které pohánějí užitečné výsledky. [Pravidla](./tutorial-create-telemetry-rules.md), [export dat](./howto-export-data.md)a veřejné rozhraní [REST API](https://docs.microsoft.com/learn/modules/manage-iot-central-apps-with-rest-api/) jsou příklady toho, jak můžete integrovat IoT Central s obchodními aplikacemi:

![Jak ioT Central může transformovat vaše data IoT](media/overview-iot-central/transform.png)

### <a name="monitor-device-health-and-operations-using-rules"></a>Sledování stavu a provozu zařízení pomocí pravidel

Když jsou vaše zařízení připojena a odesíláte data, pravidla mohou identifikovat zařízení, u kterých dochází k problémům nebo k odesílání chybových zpráv, takže je můžete opravit s minimálními prostoji. Sestavení pravidel v aplikaci IoT Central pro sledování telemetrie z vašich zařízení a upozornění operátora, když metrika překročí prahovou hodnotu nebo zařízení odešle konkrétní zprávu. E-mailové akce a webhooky pro vaše pravidla upozorňují správné lidi a přímo napožadující systémy.

### <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Spuštění vlastní analýzy a zpracování exportovaných dat

Můžete vytvářet obchodní přehledy, jako je určení trendů efektivity stroje nebo předpovídání budoucí spotřeby energie v výrobní mnoství, vytvořením vlastních analytických kanálů pro zpracování telemetrie ze zařízení a ukládání výsledků. Nakonfigurujte exporty dat v aplikaci IoT Central pro export telemetrie, změn vlastností zařízení a změn šablon zařízení do jiných služeb, kde můžete analyzovat, ukládat a vizualizovat data pomocí upřednostňovaných nástrojů.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Vytváření vlastních řešení a integrací IoT s rest API

Vytvářejte řešení IoT, jako jsou:

- Mobilní doprovodné aplikace, které mohou vzdáleně nastavit a ovládat zařízení.
- Vlastní integrace, které umožňují existující obchodní aplikace pro interakci s vašimi zařízeními a daty IoT.
- Aplikace pro správu zařízení pro modelování zařízení, onboarding, správu a přístup k datům.

## <a name="quotas"></a>Kvóty

Každé předplatné Azure má výchozí kvóty, které by mohly mít vliv na rozsah vašeho řešení IoT. V současné době IoT Central omezuje počet aplikací, které můžete nasadit v předplatném na 10. Pokud potřebujete tento limit zvýšit, obraťte se na [podporu společnosti Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Další kroky

Teď, když máte přehled o IoT Central, tady jsou některé navrhované další kroky:

- Seznamte se s [dostupnými technologiemi a službami Azure pro vytváření řešení IoT](../../iot-fundamentals/iot-services-and-technologies.md).
- Seznamte se s [uživatelským rozhraním Azure IoT Central](overview-iot-central-tour.md).
- Začněte [vytvořením aplikace Azure IoT Central](quick-deploy-iot-central.md).
- Další informace o [technologii IoT Plug and Play (preview).](../../iot-pnp/overview-iot-plug-and-play.md)
- Přečtěte si, jak [připojit zařízení Azure IoT Edge](./tutorial-add-edge-as-leaf-device.md).
- Další informace o [technologiích a službách Azure IoT](../../iot-fundamentals/iot-services-and-technologies.md).

Pokud jste vývojář zařízení a chcete se ponořit do nějakého kódu, dalším krokem je [vytvoření a připojení klientské aplikace k aplikaci Azure IoT Central](./tutorial-connect-device-nodejs.md).
