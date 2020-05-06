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
ms.openlocfilehash: 409ba6cd192f65c73aba6bd6eb11a26ec1f820fa
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82790877"
---
# <a name="what-is-azure-iot-central"></a>Co je Azure IoT Central?

IoT Central je platforma aplikací IoT, která snižuje nároky a náklady na vývoj, správu a údržbu řešení IoT na podnikové úrovni. Volba sestavování pomocí IoT Central vám umožní soustředit se na čas, peníze a energii při transformaci vaší firmy na data IoT, nikoli jenom na údržbu a aktualizaci složitých a nepřetržitě vyvíjejících se infrastruktury IoT.

Webové uživatelské rozhraní umožňuje monitorovat podmínky zařízení, vytvářet pravidla a spravovat miliony zařízení a jejich data v průběhu svého životního cyklu. Kromě toho vám umožňuje reagovat na přehledy zařízení díky rozšíření IoT Intelligence do obchodních aplikací.

Tento článek popisuje, jak IoT Central:

- Typické osoby přidružené k projektu
- Postup vytvoření aplikace
- Postup připojení zařízení do aplikace
- Postup správy aplikace
- Možnosti Azure IoT Edge v IoT Central.
- Jak připojit zařízení s Azure IoT Edge modulem runtime do vaší aplikace

## <a name="personas"></a>Osoby

Dokumentace IoT Central odkazuje na čtyři osobyy, které komunikují s aplikací IoT Central:

- _Tvůrce řešení_ zodpovídá za definování typů zařízení, která se připojují k aplikaci, a přizpůsobení aplikace pro operátor.
- _Operátor_ spravuje zařízení připojená k aplikaci.
- _Správce_ zodpovídá za úlohy správy, jako je například Správa [rolí uživatelů a oprávnění](howto-administer.md) v rámci aplikace.
- _Vývojář zařízení_ vytvoří kód, který běží na zařízení nebo IoT Edge modul připojený k vaší aplikaci.

## <a name="create-your-iot-central-application"></a>Vytvoření aplikace IoT Central

Jako tvůrce řešení můžete pomocí IoT Central vytvořit vlastní řešení IoT hostované v cloudu pro vaši organizaci. Vlastní řešení IoT se obvykle skládá z těchto součástí:

- Cloudová aplikace, která přijímá ze zařízení telemetrická data a umožňuje vám tato zařízení spravovat
- Více zařízení připojených k vaší cloudové aplikaci, na kterých běží vlastní kód

Můžete rychle nasadit novou aplikaci IoT Central a pak ji přizpůsobit konkrétním požadavkům v prohlížeči. Můžete začít se _šablonou obecné aplikace_ nebo s jednou ze šablon aplikací zaměřených na odvětví pro [maloobchodní prodej](../retail/overview-iot-central-retail.md), [energii](../energy/overview-iot-central-energy.md), [státní](../government/overview-iot-central-government.md)správu nebo [zdravotní péči](../healthcare/overview-iot-central-healthcare.md).

Jako tvůrce řešení můžete pomocí webových nástrojů vytvořit _šablonu zařízení_ pro zařízení, která se připojují k vaší aplikaci. Šablona zařízení je plán, který definuje charakteristiky a chování typu zařízení, například:

- Telemetrii, kterou posílá.
- Obchodní vlastnosti, které můžete upravit operátor
- Vlastnosti zařízení nastavené zařízením, které jsou v aplikaci jen pro čtení
- Vlastnosti, které jsou nastaveny jako operátor, které určují chování zařízení.

Tato šablona zařízení zahrnuje:

- _Model schopností zařízení_ , který popisuje možnosti, které by zařízení mělo implementovat, jako je například telemetrie, kterou posílá, a vlastnosti, které vystavuje.
- Vlastnosti cloudu, které nejsou uložené na zařízení.
- Vlastní nastavení, řídicí panely a formuláře, které jsou součástí vaší aplikace IoT Central.

### <a name="pricing"></a>Ceny

IoT Central aplikaci můžete vytvořit pomocí bezplatné zkušební verze 7 dní nebo použít Cenový tarif Standard.

- Aplikace vytvořené pomocí *bezplatného* plánu jsou po dobu sedmi dnů zdarma a podporují až pět zařízení. Můžete je kdykoli převést na používání standardního cenového plánu, než vyprší jejich platnost.
- Aplikace, které vytvoříte pomocí plánu *Standard* , se účtují podle jednotlivých zařízení. můžete si vybrat Cenový tarif **Standard 1** nebo **Standard 2** s prvními dvěma zařízeními, která jsou zdarma. Další informace o cenových plánech zdarma a Standard najdete na [stránce s cenami za Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

### <a name="create-device-templates"></a>Vytvoření šablon zařízení

[IoT technologie Plug and Play (Preview)](../../iot-pnp/overview-iot-plug-and-play.md) umožňuje IoT Central integrovat zařízení bez psaní jakéhokoli vloženého kódu zařízení. V jádru IoT technologie Plug and Play (Preview) je schéma modelu schopností zařízení, které popisuje možnosti zařízení. V aplikaci IoT Central používají šablony zařízení tyto modely schopností zařízení technologie Plug and Play IoT (Preview).

Jako tvůrce řešení máte k dispozici několik možností pro vytváření šablon zařízení:

- Naimportujte model schopností zařízení z [katalogu zařízení Azure Certified for IoT](https://aka.ms/iotdevcat) a pak přidejte jakékoli vlastnosti cloudu, vlastní nastavení a řídicí panely, které vaše IoT Central aplikace potřebuje.
- Navrhněte šablonu zařízení v IoT Central a potom implementujte svůj model schopností zařízení do kódu zařízení.
- Vytvořte model schopností zařízení pomocí nástroje Visual Studio Code a publikujte model do úložiště. Implementujte kód zařízení z modelu a připojte své zařízení k aplikaci IoT Central. IoT Central najde model schopností zařízení z úložiště a vytvoří pro vás jednoduchou šablonu zařízení.
- Vytvořte model schopností zařízení pomocí Visual Studio Code. Implementujte kód zařízení z modelu. Model schopností zařízení naimportujte ručně do aplikace IoT Central a pak přidejte jakékoli vlastnosti cloudu, přizpůsobení a řídicí panely, které aplikace IoT Central potřebuje.

Jako tvůrce řešení můžete použít IoT Central k vygenerování kódu pro testovací zařízení pro ověření šablon zařízení.

### <a name="customize-the-ui"></a>Přizpůsobení uživatelského rozhraní

Jako tvůrce řešení můžete také přizpůsobit uživatelské rozhraní aplikace IoT Central pro operátory, kteří zodpovídají za každodenní použití aplikace. Vlastní nastavení, které může tvůrce řešení provést, zahrnují:

- Definovat rozložení vlastností a nastavení v šabloně zařízení
- Nakonfigurovat vlastní řídicí panely, které operátorům pomůžou rychleji zjišťovat informace a řešit problémy
- Nakonfigurovat vlastní analýzy pro zkoumání časových řad dat z připojených zařízení

## <a name="pricing"></a>Ceny

IoT Central aplikaci můžete vytvořit pomocí bezplatné zkušební verze 7 dní nebo použít Cenový tarif Standard.

- Aplikace vytvořené pomocí *bezplatného* plánu jsou po dobu sedmi dnů zdarma a podporují až pět zařízení. Můžete je kdykoli převést na používání standardního cenového plánu, než vyprší jejich platnost.
- Aplikace, které vytvoříte pomocí plánu *Standard* , se účtují podle jednotlivých zařízení. můžete zvolit Cenový tarif **Standard 1** nebo **Standard 2** s prvními dvěma zařízeními, která jsou zdarma. Přečtěte si další informace o [cenách IoT Central](https://aka.ms/iotcentral-pricing).

## <a name="connect-your-devices"></a>Připojení zařízení

Azure IoT Central používá ke správě všech registrací a připojení zařízení [službu azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) .

Použití DPS umožňuje:

- IoT Central podporovat připojování zařízení a jejich škálování.
- Vygenerujete přihlašovací údaje zařízení a nakonfigurujete zařízení offline bez registrace zařízení prostřednictvím uživatelského rozhraní IoT Central.
- Zařízení, která se mají připojit pomocí sdílených přístupových podpisů
- Zařízení, která se mají připojit pomocí standardních certifikátů X. 509
- K registraci zařízení v IoT Central můžete použít vlastní ID zařízení. Použití vlastních ID zařízení zjednodušuje integraci se stávajícími systémy Back-Office.
- Jediný konzistentní způsob připojení zařízení k IoT Central.

Další informace najdete v tématu věnovaném [připojení k Azure IoT Central](./concepts-get-connected.md).

### <a name="azure-iot-edge-devices"></a>Zařízení Azure IoT Edge

I zařízení vytvořená pomocí [sad SDK služby Azure IoT](https://github.com/Azure/azure-iot-sdks)můžete také připojit [zařízení Azure IoT Edge](../../iot-edge/about-iot-edge.md) k aplikaci IoT Central. Azure IoT Edge umožňuje spustit cloudovou logiku a vlastní logiku přímo na zařízeních IoT spravovaných pomocí IoT Central. Modul runtime IoT Edge umožňuje:

- Instalace a aktualizace úloh na zařízení.
- Udržujte na zařízení standardy zabezpečení Azure IoT Edge.
- Ujistěte se, že moduly IoT Edge jsou vždycky spuštěné.
- Oznamte stav modulu v cloudu pro vzdálené monitorování.
- Spravujte komunikaci mezi moduly pro příjem a IoT Edge zařízení, mezi moduly v zařízení IoT Edge a mezi IoT Edgem zařízením a cloudem.

Další informace najdete v tématu [Azure IoT Edge zařízení a IoT Central](concepts-architecture.md#azure-iot-edge-devices).

## <a name="stay-connected"></a>Stálé připojení

Aplikace IoT Central plně hostují Microsoft, což snižuje nároky na správu při správě vašich aplikací.

Jako operátor můžete použít aplikaci IoT Central ke správě zařízení v řešení IoT Central. Obsluha provede úkoly, jako například:

- Monitorovat zařízení připojená k aplikaci
- Řešit potíže a opravovat problémy se zařízeními
- Zřizovat nová zařízení

Jako tvůrce řešení můžete definovat vlastní pravidla a akce, které provozují streamování dat z připojených zařízení. Operátor může tato pravidla povolit nebo zakázat na úrovni zařízení za účelem řízení a automatizace úloh v rámci aplikace.

Správci spravují přístup k vaší aplikaci pomocí [rolí a oprávnění uživatele](howto-administer.md).

U všech řešení IoT navržených pro provoz ve velkém měřítku je důležité strukturovaný přístup ke správě zařízení. Nestačí, abyste svá zařízení připojili ke cloudu, je potřeba zajistit, aby byla vaše zařízení připojená a v pořádku. Operátor může pomocí následujících možností IoT Central spravovat zařízení během životního cyklu aplikace:

### <a name="dashboards"></a>Řídicí panely

Vestavěné [řídicí panely](./howto-set-up-template.md#generate-default-views) poskytují přizpůsobitelné uživatelské rozhraní pro monitorování stavu a telemetrie zařízení. Začněte s předem vytvořeným řídicím panelem v [šabloně aplikace](howto-use-app-templates.md) nebo vytvořte vlastní řídicí panely přizpůsobené potřebám vašich operátorů. Řídicí panely můžete sdílet se všemi uživateli aplikace nebo je chránit jako soukromé.

### <a name="rules-and-actions"></a>Pravidla a akce

Sestavujte [vlastní pravidla](tutorial-create-telemetry-rules.md) na základě stavu zařízení a telemetrie a Identifikujte zařízení, která potřebují pozornost. Nakonfigurujte akce pro oznamování správných lidí a zajistěte, aby byla včas zajištěna nápravná opatření.

### <a name="jobs"></a>Úlohy

[Úlohy](howto-run-a-job.md) umožňují použít jednotlivé nebo hromadné aktualizace zařízení nastavením vlastností nebo volání příkazů. 

### <a name="user-roles-and-permissions"></a>Role a oprávnění uživatele

[Role a oprávnění](howto-manage-users-roles.md) umožňují správci přizpůsobit uživatelské prostředí. Správce používá webové uživatelské rozhraní k vytváření rolí a přiřazování oprávnění.

## <a name="transform-your-iot-data"></a>Transformace dat IoT

Jako aplikační platforma IoT Central umožňuje transformovat data IoT do obchodních přehledů, které mají za úkol reagovat. Příklady, jak můžete integrovat IoT Central s obchodními aplikacemi, najdete v tématu [pravidla](./tutorial-create-telemetry-rules.md), [Export dat](./howto-export-data.md)a [veřejné REST API](https://docs.microsoft.com/learn/modules/manage-iot-central-apps-with-rest-api/) :

![Jak IoT Central můžou transformovat data IoT](media/overview-iot-central/transform.png)

### <a name="monitor-device-health-and-operations-using-rules"></a>Monitorování stavu zařízení a operací pomocí pravidel

Když jsou zařízení připojená a odesílají data, můžou pravidla identifikovat zařízení, která nastávají problémy, nebo odesílat chybové zprávy, abyste je mohli opravit s minimálními výpadky. Pravidla sestavení v aplikaci IoT Central pro monitorování telemetrie z vašich zařízení a upozornění operátora, když metrika přechází do prahové hodnoty nebo zařízení pošle určitou zprávu. Akce e-mailu a Webhooky pro vaše pravidla upozorňují na správné uživatele a správné systémy pro příjem dat.

### <a name="run-custom-analytics-and-processing-on-your-exported-data"></a>Spouštění vlastních analýz a zpracování u exportovaných dat

Můžete vygenerovat obchodní přehledy, jako je určení trendů efektivity počítače nebo předpověď budoucí spotřeby energie v produkčním prostředí, a to vytvořením vlastních analytických kanálů pro zpracování telemetrie z vašich zařízení a uložení výsledků. Nakonfigurujte exporty dat ve vaší aplikaci IoT Central, abyste mohli exportovat telemetrie, změny vlastností zařízení a změny šablon zařízení do jiných služeb, kde můžete analyzovat, ukládat a vizualizovat data pomocí preferovaných nástrojů.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Vytváření vlastních řešení IoT a integrace s rozhraními REST API

Sestavujte řešení IoT, jako je:

- Mobilní doprovodné aplikace, které můžou vzdáleně nastavovat a řídit zařízení.
- Vlastní integrace, které umožňují stávajícím podnikovým aplikacím pracovat s vašimi zařízeními a daty IoT.
- Aplikace pro správu zařízení pro modelování zařízení, připojování, správu a přístup k datům.

## <a name="quotas"></a>Kvóty

Každé předplatné Azure má výchozí kvóty, které by mohly mít vliv na rozsah vašeho řešení IoT. V současné době IoT Central omezuje počet aplikací, které můžete nasadit v rámci předplatného na 10. Pokud potřebujete tento limit zvýšit, obraťte se na [podporu Microsoftu](https://azure.microsoft.com/support/options/).

## <a name="known-issues"></a>Známé problémy

- Průběžný export dat nepodporuje formát Avro (nekompatibilní).
- Pro injson není aktuálně podporováno.
- Dlaždice mapy není momentálně podporovaná.
- Typy schématu pole nejsou podporovány.
- Podporovaná je jenom sada SDK zařízení C a sady SDK zařízení a služeb Node. js.
- IoT Central je v současné době k dispozici v místech USA, Evropa, Asie a Tichomoří, Austrálie, Spojené království a Japonsko.
- Nemůžete použít šablonu **vlastní aplikace (starší verze)** v umístěních Spojeného království a Japonsko.
- Modely schopností zařízení musí mít všechna rozhraní definovaná vložené do stejného souboru.
- Podpora pro [IoT technologie Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) je ve verzi Preview a je podporovaná jenom ve vybraných oblastech.

## <a name="next-steps"></a>Další kroky

Teď, když máte přehled o IoT Central, tady je několik navrhovaných dalších kroků:

- Seznamte se s dostupnými [technologiemi a službami Azure pro vytváření řešení IoT](../../iot-fundamentals/iot-services-and-technologies.md).
- Seznamte se s [uživatelským rozhraním Azure IoT Central](overview-iot-central-tour.md).
- Začněte [vytvořením aplikace Azure IoT Central](quick-deploy-iot-central.md).
- Přečtěte si další informace o [IoT technologie Plug and Play (Preview)](../../iot-pnp/overview-iot-plug-and-play.md).
- Naučte se [připojit zařízení Azure IoT Edge](./tutorial-add-edge-as-leaf-device.md).
- Přečtěte si další informace o [technologiích a službách Azure IoT](../../iot-fundamentals/iot-services-and-technologies.md).

Pokud jste vývojářem zařízení a chcete podrobně do nějakého kódu, je navržený další krok [Vytvoření a připojení klientské aplikace k aplikaci Azure IoT Central](./tutorial-connect-device-nodejs.md).
