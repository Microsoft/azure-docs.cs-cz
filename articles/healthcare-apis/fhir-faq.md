---
title: Nejčastější dotazy týkající se služeb FHIR v Azure – Azure API pro FHIR
description: Získejte odpovědi na nejčastější dotazy týkající se Azure API pro FHIR, jako je umístění úložiště dat za FHIR rozhraní API a podpora verzí.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: af891935fd474e6f1f83ff1c2ce56ef71cd065c6
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86536721"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Nejčastější dotazy k rozhraní Azure API pro FHIR

## <a name="azure-api-for-fhir"></a>Azure API for FHIR

### <a name="what-is-fhir"></a>Co je FHIR?
Prostředky pro interoperabilitu s rychlými zdravotními péče (FHIR – "oheň") jsou standardem interoperability, který slouží k povolení výměny dat zdravotnictví mezi různými systémy zdravotní péče. Tento standard vyvinula organizace změněného HL7 a je přijímána organizacemi zdravotnictví po celém světě. Nejaktuálnější verze FHIR je k dispozici jako R4 (verze 4). Rozhraní Azure API pro FHIR podporuje R4 a podporuje také předchozí verzi STU3 (standard pro zkušební použití 3). Další informace o FHIR najdete na [HL7.org](http://hl7.org/fhir/summary.html).

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Jsou data za rozhraními API FHIR uložená v Azure?

Ano, data se ukládají do spravovaných databází v Azure. Rozhraní API Azure pro FHIR neposkytuje přímý přístup k podkladovým datovým skladům.

### <a name="what-identity-provider-do-you-support"></a>Jaký poskytovatel identity podporujete?

V tuto chvíli podporujeme Microsoft Azure Active Directory jako zprostředkovatele identity.

### <a name="what-fhir-version-do-you-support"></a>Jakou verzi FHIR podporujete?

Podporujeme verze 4.0.0 a 3.0.1 na rozhraní API Azure pro FHIR (PaaS) i na FHIR serveru pro Azure (Open Source).

Podrobnosti najdete v tématu [podporované funkce](fhir-features-supported.md). Přečtěte si informace o tom, co se změnilo mezi verzemi v [historii verzí pro změněného HL7 FHIR](https://hl7.org/fhir/R4/history.html).

### <a name="whats-the-difference-between-the-open-source-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Jaký je rozdíl mezi open source serverem Microsoft FHIR pro Azure a rozhraním Azure API pro FHIR?

Rozhraní Azure API pro FHIR je hostovaná a spravovaná verze serveru Microsoft FHIR pro Azure. Ve spravované službě poskytuje společnost Microsoft veškerou údržbu a aktualizace. 

Pokud používáte server FHIR pro Azure, máte přímý přístup k příslušným službám. I když ukládáte data FÍ, zodpovídáte i za údržbu a aktualizaci serveru a všech požadovaných dodržování předpisů.

Z hlediska vývoje je každá funkce nasazená na Open Source Microsoft FHIR Server pro Azure jako první. Po ověření v open source se uvolní do PaaS Azure API pro řešení FHIR. Doba mezi vydáním v open source a PaaS závisí na složitosti funkce a dalších prioritách plánu. 

### <a name="what-is-smart-on-fhir"></a>Co je inteligentní na FHIR?

Inteligentní (nahraditelné lékařské aplikace a opakovaně použitelná technologie) na FHIR je sada otevřených specifikací pro integraci partnerských aplikací se servery FHIR a dalšími systémy IT, jako jsou například elektronické záznamy o stavu a výměny informací o stavu. Když vytvoříte inteligentní aplikaci v FHIR, můžete zajistit, že k aplikaci bude možné přistupovat a využívat spoustu různých systémů.
Ověřování a rozhraní Azure API pro FHIR. Další informace o INTELIGENTNÍch možnostech najdete v přehledu [inteligentního stavu](https://smarthealthit.org/).


## <a name="iot-connector-preview"></a>IoT Connector (Preview)

### <a name="what-is-iomt"></a>Co je IoMT?
IoMT se jeví pro Internet lékařských věcí a jedná se o kategorii zařízení IoT, která zachytí a vyměňují data o stavu a wellness s dalšími zdravotnickými systémy IT přes síť. Mezi příklady zařízení IoMT patří vhodnost a klinické wearables, sledovací senzory, sledování aktivit, veřejné terminály nebo dokonce inteligentní klid.

### <a name="how-many-iot-connectors-do-i-need"></a>Kolik konektorů IoT potřebuji?
Jeden konektor IoT se dá použít k ingestování dat z velkého počtu různých typů zařízení. V následujících případech se můžete rozhodnout použít různé konektory:
- **Škálování**: ve verzi Public Preview je kapacita prostředků služby IoT Connector pevná a očekává se, že bude poskytovat propustnost přibližně 200 zpráv za sekundu. Pokud je potřeba vyšší propustnost, můžete přidat další konektory IoT.
- **Typ zařízení**: můžete nastavit samostatný konektor IoT pro každý typ IoMT zařízení, která máte k dispozici pro správu zařízení.

### <a name="is-there-a-limit-on-number-of-iot-connectors-during-public-preview"></a>Existuje limit počtu konektorů IoT během veřejné verze Preview?
Ano, můžete vytvořit pouze dvě konektory IoT na jedno předplatné a tato funkce je ve verzi Public Preview. Toto omezení existuje, aby nedocházelo k neočekávaným výdajům, protože funkce je dostupná zdarma ve verzi Preview. Na vyžádání může být tento limit vystoupl až do maximálního počtu pěti konektorů IoT.

### <a name="what-azure-regions-iot-connector-feature-is-available-during-public-preview"></a>Jaké funkce konektoru IoT pro oblasti Azure jsou dostupné ve verzi Public Preview?
Konektor IoT je k dispozici ve všech oblastech Azure, kde je k dispozici rozhraní Azure API pro FHIR.

### <a name="can-i-configure-scaling-capacity-for-iot-connector"></a>Můžu nakonfigurovat kapacitu škálování pro IoT Connector?
Vzhledem k tomu, že konektor IoT je během veřejné verze Preview zdarma, jeho kapacita škálování je pevná a omezená. Očekává se konfigurace konektoru IoT, která je dostupná ve verzi Public Preview, aby poskytovala propustnost přibližně 200 zpráv za sekundu. K dispozici je určitá forma konfigurace kapacity prostředků ve všeobecné dostupnosti (GA).

### <a name="what-fhir-version-does-iot-connector-support"></a>Jakou verzi FHIR podporuje IoT Connector?
Konektor IoT aktuálně podporuje pouze FHIR verze R4. Proto je tato funkce viditelná jenom v instancích R4 Azure API pro FHIR a Microsoft neplánuje v tomto okamžiku podporu verze STU3.

### <a name="why-cant-i-install-iot-connector-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Proč nejde nainstalovat konektor IoT, pokud je v rozhraní Azure API pro FHIR povolené privátní propojení?
Konektor IoT Connector v tuto chvíli nepodporuje funkce privátního propojení. Proto pokud máte v rozhraní Azure API pro FHIR povolené privátní propojení, nemůžete nainstalovat službu IoT Connector ani naopak. Toto omezení se má vrátit, pokud je konektor IoT dostupný pro obecnou dostupnost (GA).

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-iot-connector-feature-of-azure-api-for-fhir-service"></a>Jaký je rozdíl mezi open source konektorem IoMT FHIR pro Azure a funkci IoT Connector služby Azure API pro FHIR?
IoT Connector je hostovaná a spravovaná verze konektoru open-source IoMT FHIR pro Azure. Ve spravované službě poskytuje společnost Microsoft veškerou údržbu a aktualizace.

Pokud používáte konektor IoMT FHIR pro Azure, máte přímý přístup k podkladovým prostředkům. I když ukládáte data FÍ, zodpovídáte i za údržbu a aktualizaci serveru a všech požadovaných dodržování předpisů.

Z hlediska vývoje se každá funkce nasadí do Open Source konektoru IoMT FHIR pro Azure jako první. Po ověření v open source se uvolní do funkce PaaS IoT Connector služby Azure API pro službu FHIR. Doba mezi vydáním v open-source a PaaS závisí na složitosti funkce a dalších prioritách na mapě provozu.

## <a name="next-steps"></a>Další kroky

V tomto článku jste si přečetli některé z nejčastějších dotazů k rozhraní Azure API pro FHIR. Přečtěte si o podporovaných funkcích serveru FHIR pro Azure:
 
>[!div class="nextstepaction"]
>[Podporované funkce FHIR](fhir-features-supported.md)