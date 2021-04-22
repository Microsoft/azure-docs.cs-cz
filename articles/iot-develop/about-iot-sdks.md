---
title: Přehled možností sady SDK pro zařízení Azure IoT
description: Zjistěte, kterou sadu SDK pro zařízení Azure IoT využít v závislosti na vaší vývojové roli a úlohách.
author: philmea
ms.author: philmea
ms.service: iot-develop
ms.topic: overview
ms.date: 02/11/2021
ms.openlocfilehash: c35a9045bf809c03630fbb7c57f9d31e7b143422
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876452"
---
# <a name="overview-of-azure-iot-device-sdks"></a>Přehled sad SDK pro zařízení Azure IoT

Sady SDK pro zařízení Azure IoT jsou sada klientských knihoven zařízení, příručky pro vývojáře, ukázky a dokumentace. Sady SDK pro zařízení vám pomůžou programově připojit zařízení ke službám Azure IoT.

:::image type="content" border="false" source="media/about-iot-sdks/iot-sdk-diagram.png" alt-text="Diagram znázorňující různé sady SDK Azure IoT":::

Jak znázorňuje diagram, je k dispozici několik sad SDK pro zařízení, které odpovídají vašim potřebám vašich zařízení a programovacích jazyků. Pokyny k výběru vhodné sady SDK pro zařízení jsou k dispozici v [sadě SDK, kterou mám použít](#which-sdk-should-i-use). K dispozici jsou taky sady SDK služby Azure IoT pro připojení cloudové aplikace ke službám Azure IoT na back-endu. Tento článek se zaměřuje na sady SDK pro zařízení, ale [tady](#service-sdks)si můžete přečíst další informace o sadách Azure Service SDK.

## <a name="why-should-i-use-the-azure-iot-device-sdks"></a>Proč mám používat sady SDK pro zařízení Azure IoT?

Pokud chcete připojit zařízení k Azure IoT, můžete si vytvořit vlastní vrstvu připojení nebo použít sady SDK pro zařízení Azure IoT. Pro používání sad SDK pro zařízení Azure IoT je k dispozici několik výhod:

| &nbsp; &nbsp; Náklady &nbsp; na vývoj&nbsp; | Vlastní vrstva připojení | Sady SDK pro zařízení Azure IoT |
| :-- | :------------------------------------------------ | :---------------------------------------- |
| Podpora | Je potřeba podporovat a zdokumentovat cokoliv, co sestavíte. | Mít přístup k podpoře Microsoftu (GitHub, Microsoft Q&A, Microsoft Docs, týmy zákaznických podpory). |
| Nové funkce | Je potřeba přidat nové funkce Azure do vlastního middlewaru. | Může okamžitě využívat nové funkce, které Microsoft neustále přidává do sad IoT SDK. |
| Investice | Investovat stovky hodin vloženého vývoje pro návrh, sestavení, testování a údržbu vlastní verze | Může využít bezplatné Open Source nástroje. Jediné náklady spojené s SDK jsou výuková křivka. |

## <a name="which-sdk-should-i-use"></a>Kterou sadu SDK mám použít?

Sady SDK pro zařízení Azure IoT jsou k dispozici v oblíbených programovacích jazycích, včetně jazyků C, C#, Java, Node.js a Pythonu. K dispozici jsou dvě primární předpoklady, pokud zvolíte sadu SDK: možnosti zařízení a známý tým pomocí programovacího jazyka.

### <a name="device-capabilities"></a>Možnosti zařízení

Při výběru sady SDK musíte zvážit omezení zařízení, která používáte. Omezené zařízení je jedním z nich s jedním mikroadaptérem (MCU) a omezenou pamětí. Pokud používáte omezené zařízení, doporučujeme použít [Embedded C SDK](#embedded-c-sdk). Tato sada SDK je navržená tak, aby poskytovala minimální sadu funkcí pro připojení ke službě Azure IoT. Můžete také vybrat komponenty (MQTT Client, TLS a Socket Library), které jsou optimalizované pro vaše integrované zařízení. Pokud vaše omezené zařízení také běží na Azure RTO, můžete k připojení ke službě Azure IoT použít middleware Azure RTO. Middleware Azure RTO zabalí Embedded C SDK s dalšími funkcemi, aby se zjednodušilo připojení zařízení Azure RTO ke cloudu.

Zařízení bez omezení je takové, které má robustnější procesor, který je schopný provozovat operační systém pro podporu jazykového modulu runtime, jako je například .NET nebo Python. Pokud používáte zařízení bez omezení, je důležité se seznámit s jazykem.

### <a name="your-teams-familiarity-with-the-programming-language"></a>Znalost vašeho týmu pomocí programovacího jazyka

Sady SDK pro zařízení Azure IoT se implementují v několika jazycích, takže si můžete vybrat jazyk, který dáváte přednost. Sady SDK pro zařízení se také integrují s dalšími známými nástroji pro konkrétní jazyk. Možnost pracovat se známým vývojovým jazykem a nástroji umožňuje vašemu týmu optimalizovat vývojový cyklus výzkumu, vytváření prototypů, vývoje produktů a průběžné údržby.

Kdykoli je to možné, vyberte sadu SDK, která dobře zná váš vývojový tým. Všechny sady SDK Azure IoT jsou Open Source a mají pro váš tým k dispozici několik ukázek pro vyhodnocení a testování před potvrzením konkrétní sady SDK.

## <a name="how-can-i-get-started"></a>Jak začít?

Místo, kde začít, je prozkoumat úložiště GitHub sad SDK pro zařízení Azure. Můžete také vyzkoušet [rychlý Start](quickstart-send-telemetry-python.md) , který ukazuje, jak rychle použít sadu SDK k posílání telemetrie do Azure IoT.

Vaše možnosti, jak začít, závisí na typu zařízení, které máte:
- Pro omezená zařízení použijte [Embedded C SDK](#embedded-c-sdk). 
- Pro zařízení, která běží na Azure RTO, můžete vyvíjet pomocí [middleware Azure RTO](#azure-rtos-middleware). 
- U zařízení, která jsou neomezená, můžete [zvolit sadu SDK](#unconstrained-device-sdks) v jazyce podle vašeho výběru. 

### <a name="constrained-device-sdks"></a>Sady SDK omezených zařízení
Tyto sady SDK jsou specializované na spouštění na zařízeních s omezenými výpočetními nebo paměťovými prostředky. Další informace o běžných typech zařízení najdete v tématu [Přehled typů zařízení Azure IoT](concepts-iot-device-types.md).

#### <a name="embedded-c-sdk"></a>Vložená sada C SDK
* [Úložiště GitHub](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)
* [ukázky](https://github.com/Azure/azure-sdk-for-c/blob/master/sdk/samples/iot/README.md)
* [Referenční dokumentace](https://azure.github.io/azure-sdk-for-c/)
* [Postup sestavení vložené sady C SDK](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#build)
* [Graf velikosti omezených zařízení](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot#size-chart)

#### <a name="azure-rtos-middleware"></a>Middleware Azure RTO

* [Úložiště GitHub](https://github.com/azure-rtos/netxduo/tree/master/addons/azure_iot)
* [Začínáme příručky](https://github.com/azure-rtos/getting-started) a [Další ukázky](https://github.com/azure-rtos/samples)
* [Referenční dokumentace](/azure/rtos/threadx/)

### <a name="unconstrained-device-sdks"></a>Neomezená sady SDK pro zařízení
Tyto sady SDK můžete spustit na jakémkoli zařízení, které může podporovat modul runtime s vyšším pořadím. To zahrnuje zařízení, jako jsou počítače, maliny Pis a smartphony. Jsou odlišeny primárně podle jazyka, takže můžete zvolit libovolnou knihovnu, která nejlépe vyhovuje vašemu týmu a scénáři.

#### <a name="c-device-sdk"></a>Sada SDK pro zařízení jazyka C
* [Úložiště GitHub](https://github.com/Azure/azure-iot-sdk-c)
* [ukázky](https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples)
* [Balíčky](https://github.com/Azure/azure-iot-sdk-c/blob/master/readme.md#packages-and-libraries)
* [Referenční dokumentace](/azure/iot-hub/iot-c-sdk-ref/)
* [Referenční dokumentace modulu Edge](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)
* [Kompilace sady SDK pro zařízení v jazyce C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compiling-the-c-device-sdk)
* [Přenos sady C SDK na jiné platformy](https://github.com/Azure/azure-c-shared-utility/blob/master/devdoc/porting_guide.md)
* [Dokumentace pro vývojáře](https://github.com/Azure/azure-iot-sdk-c/tree/master/doc) pro informace o křížovém kompilování a zahájení práce na různých platformách
* [Informace o spotřebě prostředků v Azure IoT Hub C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)

#### <a name="c-device-sdk"></a>Sada SDK pro zařízení v C#

* [Úložiště GitHub](https://github.com/Azure/azure-iot-sdk-csharp)
* [ukázky](https://github.com/Azure/azure-iot-sdk-csharp#samples)
* [Balíček](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/)
* [Referenční dokumentace](/dotnet/api/microsoft.azure.devices)
* [Referenční dokumentace modulu Edge](/dotnet/api/microsoft.azure.devices.client.moduleclient)

#### <a name="java-device-sdk"></a>Sada SDK pro zařízení Java

* [Úložiště GitHub](https://github.com/Azure/azure-iot-sdk-java)
* [ukázky](https://github.com/Azure/azure-iot-sdk-java/tree/master/device/iot-device-samples)
* [Balíček](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-device-sdk)
* [Referenční dokumentace](/java/api/com.microsoft.azure.sdk.iot.device)
* [Referenční dokumentace modulu Edge](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)

#### <a name="nodejs-device-sdk"></a>Sada SDK pro Node.js zařízení

* [Úložiště GitHub](https://github.com/Azure/azure-iot-sdk-node)
* [ukázky](https://github.com/Azure/azure-iot-sdk-node/tree/master/device/samples)
* [Balíček](https://www.npmjs.com/package/azure-iot-device)
* [Referenční dokumentace](/javascript/api/azure-iot-device/)
* [Referenční dokumentace modulu Edge](/javascript/api/azure-iot-device/moduleclient)

#### <a name="python-device-sdk"></a>Sada SDK pro zařízení Python

* [Úložiště GitHub](https://github.com/Azure/azure-iot-sdk-python)
* [ukázky](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-device/samples)
* [Balíček](https://pypi.org/project/azure-iot-device/)
* [Referenční dokumentace](/python/api/azure-iot-device)
* [Referenční dokumentace modulu Edge](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)

### <a name="service-sdks"></a>Sady SDK pro služby
Azure IoT taky nabízí sady SDK pro služby, které vám umožňují vytvářet aplikace na straně řešení pro správu zařízení, získávat přehledy, vizualizovat data a další. Tyto sady SDK jsou specifické pro každou službu Azure IoT a jsou k dispozici v jazycích C#, Java, JavaScript a Python, které zjednodušují vývojové prostředí. 

#### <a name="iot-hub"></a>IoT Hub

Sady SDK služby IoT Hub vám umožňují vytvářet aplikace, které se svým IoT Hub můžou snadno komunikovat a spravovat zařízení a zabezpečení. Tyto sady SDK můžete použít k posílání zpráv z cloudu na zařízení, vyvolání přímých metod v zařízeních, aktualizaci vlastností zařízení a k dalším prostředkům.

[**Další informace o IoT Hub**](https://azure.microsoft.com/services/iot-hub/)  |  [ **Zkuste ovládat zařízení** .](../iot-hub/quickstart-control-device-python.md)

**Sada SDK pro IoT Hub služby C#**: [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service)  |  [](https://www.nuget.org/packages/Microsoft.Azure.Devices/)  |  [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/iothub/service/samples)  |  [Referenční dokumentace](/dotnet/api/microsoft.azure.devices) ukázek balíčku úložiště GitHubu

**Sada SDK služby Java IoT Hub Service**: [](https://github.com/Azure/azure-iot-sdk-java/tree/master/service)  |  [](https://github.com/Azure/azure-iot-sdk-java/blob/master/doc/java-devbox-setup.md#for-the-service-sdk)  |  [](https://github.com/Azure/azure-iot-sdk-java/tree/master/service/iot-service-samples)  |  [Referenční dokumentace](/java/api/com.microsoft.azure.sdk.iot.service) ukázek balíčku úložiště GitHubu

**JavaScript IoT Hub Service SDK**: ukázky balíčku [úložiště GitHub](https://github.com/Azure/azure-iot-sdk-node/tree/master/service)–  |  [](https://www.npmjs.com/package/azure-iothub)  |  [](https://github.com/Azure/azure-iot-sdk-node/tree/master/service/samples)  |  [Referenční dokumentace](/javascript/api/azure-iothub/)

**Sada SDK služby IoT Hub pro Python**: [](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub)  |  [](https://pypi.python.org/pypi/azure-iot-hub/)  |  [](https://github.com/Azure/azure-iot-sdk-python/tree/master/azure-iot-hub/samples)  |  [Referenční dokumentace](/python/api/azure-iot-hub) ukázek balíčku úložiště GitHubu

#### <a name="azure-digital-twins"></a>Azure Digital Twins

Digitální vlákna Azure je nabídka typu platforma jako služba (PaaS), která umožňuje vytváření diagramů znalostí na základě digitálních modelů celého prostředí. Tato prostředí můžou být budovy, továrny, farmy, energetické sítě, železnice, Stadium a další – dokonce i celá města. Tyto digitální modely se dají využít k získání přehledů, které poskytují lepší produkty, optimalizované operace, snížené náklady a převratná prostředí pro zákazníky. Azure IoT nabízí sady SDK pro služby, které usnadňují sestavování aplikací, které využívají sílu digitálních vláken Azure.

[**Další informace o digitálních zdvojeních Azure**](https://azure.microsoft.com/services/digital-twins/)  |  [ **Kódování aplikace ADT**](../digital-twins/tutorial-code.md)

**Sada SDK služby C# ADT**: [](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)  |  [](https://www.nuget.org/packages/Azure.DigitalTwins.Core)  |  [](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)  |  [Referenční dokumentace](/dotnet/api/overview/azure/digitaltwins/client) ukázek balíčku úložiště GitHubu

**Java ADT Service SDK**: ukázky balíčku [úložiště GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)–  |  [](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)  |  [](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core/src/samples)  |  [Referenční dokumentace](/java/api/overview/azure/digitaltwins/client)

**Sada SDK službyNode.js ADT**: [](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)  |  [](https://www.npmjs.com/package/@azure/digital-twins-core)  |  [](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core/samples)  |  [Referenční dokumentace](/javascript/api/@azure/digital-twins-core/) ukázek balíčku úložiště GitHubu

**Python ADT Service SDK**: [](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)  |  [](https://pypi.org/project/azure-digitaltwins-core/)  |  dokumentace k [ukázkám](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core/samples)balíčku úložiště GitHubu  |  [Referenční dokumentace](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)

#### <a name="device-provisioning-service"></a>Device Provisioning Service

IoT Hub Device Provisioning Service (DPS) je pomocná služba pro IoT Hub, která umožňuje nulovému zřizování za běhu do správného služby IoT Hub bez nutnosti zásahu člověka. DPS umožňuje bezpečným a škálovatelným způsobem zřizovat miliony zařízení. Sady SDK služby DPS umožňují vytvářet aplikace, které můžou bezpečně spravovat vaše zařízení, a to vytvořením skupin registrací a prováděním hromadných operací.

[**Další informace o službě Device Provisioning Service**](../iot-dps/index.yml)  |  [ **Zkuste vytvořit registraci skupiny pro zařízení X. 509** .](../iot-dps/quick-enroll-device-x509-csharp.md)

**Sada SDK služby Device Provisioning v C#**: [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service)  |  [](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/)  |  [](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/provisioning/service/samples)  |  [Referenční dokumentace](/dotnet/api/microsoft.azure.devices.provisioning.service) ukázek balíčku úložiště GitHubu

**Sada SDK služby Device Provisioning pro zařízení Java**: [](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-service-client/src)  |  [](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client)  |  [](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-samples#provisioning-service-client)  |  [Referenční dokumentace](/java/api/com.microsoft.azure.sdk.iot.provisioning.service) ukázek balíčku úložiště GitHubu

**Sada SDK službyNode.js Device Provisioning**: [](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service)  |  [](https://www.npmjs.com/package/azure-iot-provisioning-service)  |  [](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/service/samples)  |  [Referenční dokumentace](/javascript/api/azure-iot-provisioning-service) ukázek balíčku úložiště GitHubu

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: připojení zařízení k IoT Central (Python)](quickstart-send-telemetry-python.md)
* [Rychlý Start: připojení zařízení k IoT Hub (Python)](quickstart-send-telemetry-cli-python.md)
* [Začínáme s integrovaným vývojem](quickstart-device-development.md)
* Další informace o [výhodách vývoje pomocí sad Azure IoT SDK](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/)