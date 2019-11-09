---
title: Co je Azure IoT Central | Microsoft Docs
description: Azure IoT Central je ucelené řešení SaaS, které můžete použít k vytvoření a správě vlastního řešení IoT. Tento článek obsahuje přehled funkcí Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2019
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: timlt
ms.openlocfilehash: 1269743d6c8354054a17b94d3470800a94180090
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2019
ms.locfileid: "73893862"
---
# <a name="what-is-azure-iot-central-preview-features"></a>Co je Azure IoT Central (funkce verze Preview)?

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

> [!WARNING]
> Možnosti [IoT technologie Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) v Azure IoT Central jsou momentálně ve verzi Public Preview. Nepoužívejte IoT Central aplikaci technologie Plug and Play IoT povolenou pro produkční úlohy. V produkčních prostředích se používá aplikace IoT Central vytvořená z aktuální, všeobecně dostupné šablony aplikace.

Azure IoT Central je aplikační platforma IoT, která snižuje zatížení a náklady na vývoj, správu a údržbu řešení IoT na podnikové úrovni. Když zvolíte sestavení pomocí Azure IoT Central máte možnost soustředit se na čas, peníze a energii při transformaci vaší firmy na data IoT, nikoli jenom spravovat a aktualizovat složitou a nepřetržitou vývojovou infrastrukturu IoT.

Webové uživatelské rozhraní umožňuje monitorovat podmínky zařízení, vytvářet pravidla a spravovat miliony zařízení a jejich data v průběhu svého životního cyklu. Kromě toho vám umožňuje reagovat na přehledy zařízení díky rozšíření IoT Intelligence do obchodních aplikací.

Tento článek popisuje, jak IoT Central Azure:

- Typické osoby přidružené k projektu
- Postup vytvoření aplikace
- Postup připojení zařízení do aplikace
- Postup správy aplikace
- Přehled možností IoT Edge v IoT Central
- Jak připojit zařízení s Azure IoT Edge modulem runtime do vaší aplikace


## <a name="known-issues"></a>Známé problémy

> [!Note]
> Tyto známé problémy se vztahují pouze na aplikaci IoT Central Preview.

- Pravidla nepodporují všechny akce (jenom e-mail).
- Pro komplexní typy nejsou podporovaná pravidla, analýza a skupiny zařízení.
- Průběžný export dat nepodporuje formát Avro (nekompatibilní).
- Simulovaná zařízení nepodporují všechny komplexní typy.
- Pro injson není aktuálně podporováno.
- Dlaždice mapy není momentálně podporovaná.
- Úlohy nepodporují komplexní typy.
- Typy schématu pole nejsou podporovány.
- Export šablony aplikace a kopie aplikace nejsou podporovány.
- Podporovaná je jenom sada SDK zařízení C a sady SDK zařízení a služeb Node. js.
- Je dostupná jenom v oblastech Severní Evropa a střední USA.
- Modely schopností zařízení musí mít všechna rozhraní definovaná vložené do stejného souboru.

## <a name="personas"></a>Osoby

Dokumentace ke službě Azure IoT Central odkazuje na čtyři osoby, kteří komunikují s aplikací Azure IoT Central:

- _Tvůrce řešení_ zodpovídá za definování typů zařízení, která se připojují k aplikaci, a přizpůsobení aplikace pro operátor.
- _Operátor_ spravuje zařízení připojená k aplikaci.
- _Správce_ zodpovídá za úlohy správy, jako je například Správa [rolí uživatelů a oprávnění](howto-administer.md) v rámci aplikace.
- _Vývojář zařízení_ vytváří kód, který pak běží na zařízení připojeném k aplikaci.
- _Vývojář zařízení nebo modulů_ vytvoří kód nebo modul, který běží na zařízení připojeném k vaší aplikaci.

## <a name="create-your-azure-iot-central-application"></a>Vytvoření aplikace Azure IoT Central

Jako tvůrce vytvoříte pomocí Azure IoT Central pro svoji organizaci vlastní řešení IoT hostované v cloudu. Vlastní řešení IoT se obvykle skládá z těchto součástí:

- Cloudová aplikace, která přijímá ze zařízení telemetrická data a umožňuje vám tato zařízení spravovat
- Více zařízení připojených k vaší cloudové aplikaci, na kterých běží vlastní kód

Můžete rychle nasadit novou aplikaci IoT Central Azure a pak ji přizpůsobit konkrétním požadavkům v prohlížeči. Jako tvůrce použijete webové nástroje k vytvoření _šablony_ pro zařízení, která se připojují k vaší aplikaci. Šablona zařízení je plán, který definuje charakteristiky a chování typu zařízení, například:

- Telemetrii, kterou posílá.
- Obchodní vlastnosti, které můžete upravit operátor
- Vlastnosti zařízení nastavené zařízením, které jsou v aplikaci jen pro čtení
- Vlastnosti, které jsou nastaveny jako operátor, které určují chování zařízení.

Tato šablona zařízení zahrnuje:

- _Model schopností zařízení_ , který popisuje možnosti, které by zařízení mělo implementovat, jako je například telemetrie, kterou posílá, a vlastnosti, které vystavuje.
- Vlastnosti cloudu, které nejsou uloženy v zařízení.
- Vlastní nastavení, řídicí panely a formuláře, které jsou součástí vaší aplikace IoT Central.

### <a name="create-device-templates"></a>Vytvoření šablon zařízení

[IoT technologie Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) umožňuje IoT Central integrovat zařízení bez psaní jakéhokoli vloženého kódu zařízení. V jádru IoT technologie Plug and Play je schéma modelu schopností zařízení, které popisuje možnosti zařízení. V aplikaci IoT Central Preview používají šablony zařízení tyto modely schopností zařízení technologie Plug and Play IoT.

Jako tvůrce máte k dispozici několik možností pro vytváření šablon zařízení:

- Navrhněte šablonu zařízení v IoT Central a potom implementujte svůj model schopností zařízení do kódu zařízení.
- Naimportujte model schopností zařízení z [katalogu zařízení Azure Certified for IoT](https://aka.ms/iotdevcat) a pak přidejte jakékoli vlastnosti cloudu, vlastní nastavení a řídicí panely, které vaše IoT Central aplikace potřebuje.
- Vytvořte model schopností zařízení pomocí Visual Studio Code. Implementujte kód zařízení z modelu a připojte své zařízení k aplikaci IoT Central. IoT Central najde model schopností zařízení z úložiště a vytvoří pro vás jednoduchou šablonu zařízení.
- Vytvořte model schopností zařízení pomocí Visual Studio Code. Implementujte kód zařízení z modelu. Model schopností zařízení naimportujte ručně do aplikace IoT Central a pak přidejte jakékoli vlastnosti cloudu, přizpůsobení a řídicí panely, které aplikace IoT Central potřebuje.

Jako tvůrce můžete použít IoT Central k vygenerování kódu pro testovací zařízení k ověření vašich šablon zařízení.

### <a name="customize-the-ui"></a>Přizpůsobení uživatelského rozhraní

Jako tvůrce můžete taky přizpůsobit uživatelské rozhraní aplikace Azure IoT Central pro operátory, kteří zodpovídají za každodenní použití aplikace. Tvůrce může provést třeba následující přizpůsobení:

- Definovat rozložení vlastností a nastavení v šabloně zařízení
- Nakonfigurovat vlastní řídicí panely, které operátorům pomůžou rychleji zjišťovat informace a řešit problémy
- Nakonfigurovat vlastní analýzy pro zkoumání časových řad dat z připojených zařízení

## <a name="connect-your-devices"></a>Připojení zařízení

Jakmile tvůrce definuje typy zařízení, které se můžou připojovat k aplikaci, vývojář zařízení vytvoří kód, který na zařízeních poběží. Jako vývojář zařízení vytváříte kód zařízení pomocí open source [sad SDK Azure IoT](https://github.com/Azure/azure-iot-sdks) od Microsoftu. Tyto sady SDK nabízejí širokou podporu jazyků, platforem a protokolů, aby vyhověly vašim požadavkům na připojení vašich zařízení k aplikaci Azure IoT Central. Sady SDK vám pomůžou implementovat následující možnosti zařízení:

- Vytvořit zabezpečené připojení
- Odesílat telemetrická data
- Hlásit stav
- Přijímat aktualizace konfigurace

Další informace najdete v příspěvku na blogu [Výhody používání sad SDK Azure IoT a nástrahy, kterým je potřeba se vyhnout v opačném případě](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/).

## <a name="manage-your-application"></a>Správa vaší aplikace

Aplikace Azure IoT Central jsou plně hostované Microsoftem, což snižuje režijní náklady na jejich správu.

Jako operátor používáte aplikaci Azure IoT Central ke správě zařízení v rámci svého řešení Azure IoT Central. Obsluha provede úkoly, jako například:

- Monitorovat zařízení připojená k aplikaci
- Řešit potíže a opravovat problémy se zařízeními
- Zřizovat nová zařízení

Jako tvůrce můžete definovat vlastní pravidla a akce, které provozují streamování dat z připojených zařízení. Operátor může tato pravidla povolit nebo zakázat na úrovni zařízení za účelem řízení a automatizace úloh v rámci aplikace.

Správci spravují přístup k vaší aplikaci pomocí [rolí a oprávnění uživatele](howto-administer.md).


## <a name="what-is-azure-iot-central-with-azure-iot-edge-preview-features"></a>Co je Azure IoT Central s využitím Azure IoT Edge (funkce ve verzi Preview)

IoT Central rozšiřuje své portfolio tím, že podporuje zařízení Azure IoT Edge. 

Firmy teď můžou spouštět cloudové logiky přímo na zařízeních IoT, která spravuje Azure IoT Central. Tato nová funkce pomáhá firmám propojit a spravovat Azure IoT Edgeá zařízení s modulem runtime Azure IoT Edge, nasazovat softwarové moduly, publikovat přehledy a provádět akce ve velkém měřítku – to vše, co je v IoT Central. 

[Přehled Azure IoT Edge](../../iot-edge/about-iot-edge.md)

### <a name="overview-of-iot-edge-capabilities-in-iot-central"></a>Přehled možností IoT Edge v IoT Central

Modul runtime Azure IoT Edge umožňuje používat na hraničních zařízeních IoT vlastní a cloudovou logiku. IoT Edge zařízení používá modul runtime a provádí operace správy a komunikace. 

Azure IoT Edge runtime provádí následující funkce:

- Instalace a aktualizace úloh na zařízení.
- Udržujte na zařízení standardy zabezpečení Azure IoT Edge.
- Ujistěte se, že moduly IoT Edge jsou vždycky spuštěné.
- Oznamte stav modulu v cloudu pro vzdálené monitorování.
- Spravujte komunikaci mezi moduly pro příjem a IoT Edge zařízení, mezi moduly v zařízení IoT Edge a mezi IoT Edgem zařízením a cloudem.

![IoT Central s přehledem Azure IoT Edge](./media/overview-iot-central/iotedge.png)

Azure IoT Central provádí následující funkce: 

- Podpora šablon zařízení Azure IoT Edge, která popisuje možnosti, které by mělo Azure IoT Edge zařízení implementovat, například 
  1. možnosti nahrávání manifestu nasazení, které vám pomůžou spravovat manifest pro loďstva zařízení
  2. moduly, které se spustí na zařízení Azure IoT Edge
  3. telemetrii každý modul odesílá
  4. vlastnosti každého modulu sestavy 
  5. příkaz každý modul reaguje na
  6. Vytvoření vztahů mezi modelem schopností zařízení Azure IoT Edge brány a modelem schopností pro příjem dat z libovolného zařízení
  7. Vlastnosti cloudu, které nejsou uložené na zařízení Azure IoT Edge
  8. Vlastní nastavení, řídicí panely a formuláře, které jsou součástí vaší aplikace IoT Central

  [Vytvořit šablonu zařízení Azure IoT Edge](./tutorial-define-edge-device-type.md)
   
- Zřizování Azure IoT Edgech zařízení ve velkém měřítku pomocí služby Azure IoT Device Provisioning
- Aktivovat pravidla a provádět akce s Azure IoT Edgemi zařízeními
- Vytváření řídicích panelů a analýz 
- Průběžný export dat z Azure IoT Edgech zařízení pomocí exportu telemetrie

### <a name="azure-iot-edge-device-types-in-iot-central"></a>Azure IoT Edge typy zařízení v IoT Central

Azure IoT Central klasifikuje Azure IoT Edge typy zařízení následujícím způsobem:

- Zařízení Azure IoT Edge jako zařízení typu list. Zařízení Azure IoT Edge by mohla mít zařízení s podřízenou výjimkou, ale zařízení se nezřídí v IoT Central
- Zařízení Azure IoT Edge jako zařízení brány se zařízeními pro příjem dat. Zařízení brány i zařízení pro příjem dat jsou zřízena v IoT Central

![IoT Central s přehledem Azure IoT Edge](./media/overview-iot-central/gatewayedge.png)

### <a name="azure-iot-edge-patterns-supported-in-iot-central"></a>Azure IoT Edgeové vzory podporované v IoT Central

- Azure IoT Edge jako ![zařízení Azure IoT Edge jako na listovém zařízení](./media/overview-iot-central/edgeasleafdevice.png) Azure IoT Edge zařízení se zřídí v IoT Central a veškerá zařízení, která se budou vystupovat, a jeho telemetrie se budou prezentovat jako přicházející z Azure IoT Edge zařízení. Podřízená zařízení, pokud se nějaká připojení k Azure IoT Edge zařízení nezřídí v IoT Central. 

- Zařízení Azure IoT Edge brány připojené k zařízením s podřízenou identitou ![Azure IoT Edge s identitou pro příjem zařízení](./media/overview-iot-central/edgewithdownstreamdeviceidentity.png) Azure IoT Edge zařízení se zřídí v IoT Central společně se zařízeními pro příjem dat připojenými k Azure IoT Edgemu zařízení. Modul runtime podporuje zřizování podřízených zařízení prostřednictvím brány a plánuje se na budoucnost. IoT Central Vysvětlete první zřizování cloudových zařízení a přihlašovací údaje se spravují ručně na zařízeních pro příjem dat. První zřízení zařízení pro příjem podřízených zařízení bylo naplánováno pro budoucí pololetí. 

- Zařízení Azure IoT Edge brány připojené k zařízením pro příjem dat pomocí identity poskytované hraniční bránou ![Azure IoT Edge se zařízením pro příjem dat bez identity](./media/overview-iot-central/edgewithoutdownstreamdeviceidentity.png) Azure IoT Edge zařízení se zřídí v IoT Central spolu s podřízenými zařízeními připojenými k Azure IoT Edgemu zařízení. Běhová podpora brány, která poskytuje identitu pro podřízená zařízení a zřizování zařízení pro příjem dat, se plánuje pro budoucnost. Můžete využít vlastní modul překladu identit a IoT Central bude tento model podporovat. 

## <a name="next-steps"></a>Další kroky

Přečetli jste si přehled Azure IoT Central a tady jsou doporučené další kroky:

- Pochopte rozdíly mezi [Azure IoT Central a akcelerátory řešení Azure IoT](../core/overview-iot-options.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json).
- Seznamte se s [uživatelským rozhraním Azure IoT Central](overview-iot-central-tour.md).
- Začněte [vytvořením aplikace Azure IoT Central](quick-deploy-iot-central.md).
- Další informace o [IoT technologie Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md)
- Informace o tom, jak [vytvořit šablonu zařízení Azure IoT Edge](./tutorial-define-edge-device-type.md)
