---
title: Nejčastější dotazy týkající se služeb FHIR v Azure – Azure API pro FHIR
description: Získejte odpovědi na nejčastější dotazy týkající se Azure API pro FHIR, jako je umístění úložiště dat za FHIR rozhraní API a podpora verzí.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2020
ms.author: matjazl
ms.openlocfilehash: 35b59fb0583911b5b9faee96276d1bb09a8d6679
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91269705"
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

### <a name="can-i-create-a-custom-fhir-resource"></a>Můžu vytvořit vlastní prostředek FHIR?

Nepovolujeme vlastní FHIR prostředky. Pokud potřebujete vlastní prostředek FHIR, můžete vytvořit vlastní prostředek nad [základním prostředkem](http://www.hl7.org/fhir/basic.html) s rozšířeními. 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>Podporují Azure API pro FHIR [rozšíření](https://www.hl7.org/fhir/extensibility.html) ?

Umožňuje načíst do serveru veškerá platná data FHIR JSON. Pokud chcete uložit definici struktury definující rozšíření, můžete ho uložit jako prostředek definice struktury. V současné době nelze vyhledávat rozšíření.

### <a name="what-is-the-limit-on-_count"></a>Jaký je limit _count?

Aktuální limit počtu je 100.

### <a name="are-there-any-limitations-on-the-group-export-functionality"></a>Existují nějaká omezení funkce exportu skupin?

Pro export skupiny exportujeme zahrnuté odkazy ze skupiny, ale ne všechny vlastnosti [prostředku skupiny](https://www.hl7.org/fhir/group.html).

### <a name="can-i-post-a-bundle-to-the-azure-api-for-fhir"></a>Můžu do Azure API pro FHIR Publikovat sadu prostředků?

V současné době podporujeme [dávkové balíky](https://www.hl7.org/fhir/valueset-bundle-type.html) , ale nepodporují transakce se zaúčtováním v rozhraní Azure API pro FHIR. K publikování sad transakcí můžete použít Open Source Server FHIR, který je založený na SQL.

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-azure-api-for-fhir"></a>Jak získám všechny prostředky pro jednoho pacienta v rozhraní API Azure pro FHIR?

Pro FHIR podporujeme [vyhledávání oddílů](https://www.hl7.org/fhir/compartmentdefinition.html) v rozhraní API Azure. To vám umožní získat všechny prostředky, které se týkají konkrétního pacienta. Všimněte si, že teď oddíl obsahuje všechny prostředky, které se týkají pacienta, ale ne samotný pacient, takže budete muset také vyhledat pacienta, pokud potřebujete prostředek pacienta ve výsledcích.

Níže jsou uvedeny některé příklady:

* ZÍSKAT pacienta/<id>/*
* ZÍSKAT pacienta/ <id> /Observation
* ZÍSKAT pacient/ <id> /Observation? Code = 8302-2

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>Kde se můžu podívat na některé příklady použití rozhraní Azure API pro FHIR v rámci pracovního postupu?

Na [stránce GitHub architektury Health Architecture](https://github.com/microsoft/health-architectures)máme k dispozici kolekci referenčních architektur.

## <a name="azure-iot-connector-for-fhir-preview"></a>Azure IoT Connector pro FHIR (Preview)

### <a name="what-is-iomt"></a>Co je IoMT?
IoMT se jeví pro Internet lékařských věcí a jedná se o kategorii zařízení IoT, která zachytí a vyměňují data o stavu a wellness s dalšími zdravotnickými systémy IT přes síť. Mezi příklady zařízení IoMT patří vhodnost a klinické wearables, sledovací senzory, sledování aktivit, veřejné terminály nebo dokonce inteligentní klid.

### <a name="how-many-azure-iot-connector-for-fhir-preview-do-i-need"></a>Kolik Azure IoT Connectoru pro FHIR (Preview) Potřebuji?
Jeden konektor Azure IoT pro FHIR * se dá použít k ingestování dat z velkého počtu různých typů zařízení. V následujících případech se můžete rozhodnout použít různé konektory:
- **Škálování**: ve verzi Public Preview je pro službu Azure IoT Connector pro kapacitu prostředků FHIR pevný a očekává se, že se bude poskytovat propustnost přibližně 200 zpráv za sekundu. Pokud potřebujete vyšší propustnost, můžete přidat další Azure IoT Connector pro FHIR.
- **Typ zařízení**: můžete nastavit samostatný konektor Azure IoT pro FHIR pro každý typ zařízení IoMT, které máte k dispozici pro správu zařízení.

### <a name="is-there-a-limit-on-number-of-azure-iot-connector-for-fhir-preview-during-public-preview"></a>Je k dispozici limit počtu konektorů Azure IoT pro FHIR (Preview) ve verzi Public Preview?
Ano, můžete vytvořit pouze dva konektory Azure IoT pro FHIR pro každé předplatné, zatímco funkce je ve verzi Public Preview. Toto omezení existuje, aby nedocházelo k neočekávaným výdajům, protože funkce je dostupná zdarma ve verzi Preview. Na vyžádání by tento limit mohl být vyvolán až pro maximálně pět Azure IoT Connector pro FHIR.

### <a name="what-azure-regions-azure-iot-connector-for-fhir-preview-feature-is-available-during-public-preview"></a>Jaké oblasti Azure IoT Connector pro FHIR (Preview) jsou k dispozici ve verzi Public Preview?
Azure IoT Connector pro FHIR je k dispozici ve všech oblastech Azure, kde je k dispozici rozhraní Azure API pro FHIR.

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>Můžu nakonfigurovat kapacitu škálování pro Azure IoT Connector pro FHIR (Preview)?
Vzhledem k tomu, že Azure IoT Connector pro FHIR je v rámci verze Public Preview zdarma, je kapacita škálování pevná a omezená. Pro konfiguraci služby Azure IoT Connector pro FHIR, která je dostupná ve verzi Public Preview, se očekává, že bude poskytovat propustnost přibližně 200 zpráv za sekundu. K dispozici je určitá forma konfigurace kapacity prostředků ve všeobecné dostupnosti (GA).

### <a name="what-fhir-version-does-azure-iot-connector-for-fhir-preview-support"></a>Jakou verzi FHIR podporuje Azure IoT Connector pro FHIR (Preview)?
Azure IoT Connector pro FHIR aktuálně podporuje jenom FHIR verze R4. Proto je tato funkce viditelná jenom v instancích R4 Azure API pro FHIR a Microsoft neplánuje v tomto okamžiku podporu verze STU3.

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Proč není možné nainstalovat Azure IoT Connector pro FHIR (Preview), pokud je v rozhraní Azure API pro FHIR povolené privátní propojení?
Azure IoT Connector pro FHIR v tuto chvíli nepodporuje funkce privátního propojení. Proto pokud máte v rozhraní Azure API pro FHIR povolený privátní odkaz, nemůžete pro FHIR a naopak nainstalovat Azure IoT Connector. Toto omezení se očekává, když je Azure IoT Connector pro FHIR dostupný pro obecnou dostupnost (GA).

### <a name="whats-the-difference-between-the-open-source-iomt-fhir-connector-for-azure-and-azure-iot-connector-for-fhir-preview-feature-of-azure-api-for-fhir-service"></a>Jaký je rozdíl mezi open source konektorem IoMT FHIR pro Azure a funkcí Azure IoT Connector pro FHIR (Preview) Azure API pro službu FHIR?
Azure IoT Connector pro FHIR je hostovaná a spravovaná verze konektoru open-source IoMT FHIR pro Azure. Ve spravované službě poskytuje společnost Microsoft veškerou údržbu a aktualizace.

Pokud používáte konektor IoMT FHIR pro Azure, máte přímý přístup k podkladovým prostředkům. I když ukládáte data FÍ, zodpovídáte i za údržbu a aktualizaci serveru a všech požadovaných dodržování předpisů.

Z hlediska vývoje se každá funkce nasadí do Open Source konektoru IoMT FHIR pro Azure jako první. Po ověření v open source se uvolní do PaaS Azure IoT Connector pro FHIR funkce Azure API pro službu FHIR. Doba mezi vydáním v open-source a PaaS závisí na složitosti funkce a dalších prioritách na mapě provozu.

## <a name="next-steps"></a>Další kroky

V tomto článku jste si přečetli některé z nejčastějších dotazů k rozhraní Azure API pro FHIR. Přečtěte si o podporovaných funkcích serveru FHIR pro Azure:
 
>[!div class="nextstepaction"]
>[Podporované funkce FHIR](fhir-features-supported.md)

* V Azure Portal se konektor Azure IoT pro FHIR označuje jako IoT Connector (Preview).

FHIR je registrovaná ochranná známka HL7 a používá se s povolením HL7.