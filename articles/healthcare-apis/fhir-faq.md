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
ms.openlocfilehash: 744bcfe196629f76c5505f36ef9c8c224fefe601
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95909063"
---
# <a name="frequently-asked-questions-about-the-azure-api-for-fhir"></a>Nejčastější dotazy k rozhraní Azure API pro FHIR

## <a name="azure-api-for-fhir-the-basics"></a>Rozhraní Azure API pro FHIR: Základy

### <a name="what-is-fhir"></a>Co je FHIR?
Prostředky pro interoperabilitu s rychlými zdravotními péče (FHIR – "oheň") jsou standardem interoperability, který slouží k povolení výměny dat zdravotnictví mezi různými systémy zdravotní péče. Tento standard vyvinula organizace změněného HL7 a je přijímána organizacemi zdravotnictví po celém světě. Nejaktuálnější verze FHIR je k dispozici jako R4 (verze 4). Rozhraní Azure API pro FHIR podporuje R4 a podporuje také předchozí verzi STU3 (standard pro zkušební použití 3). Další informace o FHIR najdete na [HL7.org](http://hl7.org/fhir/summary.html).

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Jsou data za rozhraními API FHIR uložená v Azure?

Ano, data se ukládají do spravovaných databází v Azure. Rozhraní API Azure pro FHIR neposkytuje přímý přístup k podkladovým datovým skladům.

### <a name="what-identity-provider-do-you-support"></a>Jaký poskytovatel identity podporujete?

V tuto chvíli podporujeme Microsoft Azure Active Directory jako zprostředkovatele identity.

### <a name="what-is-the-recovery-point-objective-rpo-for-the-azure-api-for-fhir"></a>Jaký je cíl bodu obnovení (RPO) pro Azure API pro FHIR?
Rozhraní Azure API pro FHIR se zálohuje Cosmos DB jako náš poskytovatel trvalosti. Z tohoto důvodu se RPO pro službu rovná [Cosmos dB (jedna oblast)](https://docs.microsoft.com/azure/cosmos-db/consistency-levels) a < 240 minut.

### <a name="what-fhir-version-do-you-support"></a>Jakou verzi FHIR podporujete?

Podporujeme verze 4.0.0 a 3.0.1 na rozhraní API Azure pro FHIR (PaaS) i na FHIR serveru pro Azure (Open Source).

Podrobnosti najdete v tématu [podporované funkce](fhir-features-supported.md). Přečtěte si o tom, co se změnilo mezi FHIR verzemi (tj. STU3 na R4) v [historii verzí pro změněného HL7 FHIR](https://hl7.org/fhir/R4/history.html).

Azure IoT Connector pro FHIR (Preview) aktuálně podporuje jenom FHIR verze R4 a je viditelný jenom pro instance R4 Azure API pro FHIR.

### <a name="whats-the-difference-between-microsoft-fhir-server-for-azure-and-the-azure-api-for-fhir"></a>Jaký je rozdíl mezi Microsoft FHIR serverem pro Azure a rozhraním Azure API pro FHIR?

Rozhraní Azure API pro FHIR je hostovaná a spravovaná verze serveru Microsoft FHIR pro Azure. Ve spravované službě poskytuje společnost Microsoft veškerou údržbu a aktualizace. 

Když spustíte server FHIR pro Azure, budete mít přímý přístup k příslušným službám, ale zodpovídá za údržbu a aktualizaci serveru a veškerou potřebnou práci s dodržováním předpisů, pokud ukládáte data FÍ.

V rámci vývoje se každá funkce, která se netýká jenom spravované služby, nasadí nejdřív na Open Source Microsoft FHIR Server pro Azure. Po ověření v open source se uvolní do PaaS Azure API pro řešení FHIR. Doba mezi vydáním v open source a PaaS závisí na složitosti funkce a dalších prioritách plánu. To je stejný postup pro všechny naše služby, jako je Azure IoT Connector pro FHIR (Preview).

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Kde můžu zjistit, co se uvolňuje do rozhraní API Azure pro FHIR?

Některé z toho, co se uvolňuje do rozhraní API Azure pro FHIR, najdete v článku o [vydání](https://github.com/microsoft/fhir-server/releases) Open-Source serveru FHIR. Od listopadu 2020 jsme označili položky pomocí Azure-API-for-FHIR, pokud se položka Open-Source uvolní do spravované služby. Tyto funkce jsou obvykle k dispozici po dvou týdnech po uplynutí stránky verze v open source. Také jsme zahrnuli pokyny, jak otestovat sestavení [zde] (Pokud chcete https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) testovat ve vašem vlastním prostředí). Vyhodnocujeme, jak nejlépe sdílet další aktualizace spravované služby.

### <a name="in-which-regions-is-azure-api-for-fhir-available"></a>Ve kterých oblastech je Azure API pro FHIR k dispozici?

V současné době máme obecnou dostupnost pro veřejné i státní správu v [několika geografických oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=non-regional,us-east,us-east-2,us-central,us-north-central,us-south-central,us-west-central,us-west,us-west-2,canada-east,canada-central,usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-texas,usgov-virginia). Informace o cloudových službách pro státní správu v Microsoftu najdete v [FedRAMP služeb Azure](../azure-government/compliance/azure-services-in-fedramp-auditscope.md).

### <a name="where-can-i-see-what-is-releasing-into-the-azure-api-for-fhir"></a>Kde můžu zjistit, co se uvolňuje do rozhraní API Azure pro FHIR?

Některé z toho, co se uvolňuje do rozhraní API Azure pro FHIR, najdete v článku o [vydání](https://github.com/microsoft/fhir-server/releases) Open-Source serveru FHIR. Pracovali jsme na označení položek pomocí Azure-API-for-FHIR, pokud se budou vydávat do spravované služby a jsou obvykle dostupné dva týdny po jejich uvedení na stránce pro vydání v open source. Také jsme zahrnuli pokyny k testování sestavení [zde](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) , pokud byste chtěli testovat ve vlastním prostředí. Vyhodnocujeme, jak nejlépe sdílet další aktualizace spravované služby.

### <a name="what-is-smart-on-fhir"></a>Co je inteligentní na FHIR?

Inteligentní (nahraditelné lékařské aplikace a opakovaně použitelná technologie) na FHIR je sada otevřených specifikací pro integraci partnerských aplikací se servery FHIR a dalšími systémy IT, jako jsou například elektronické záznamy o stavu a výměny informací o stavu. Když vytvoříte inteligentní aplikaci v FHIR, můžete zajistit, že k aplikaci bude možné přistupovat a využívat spoustu různých systémů.
Ověřování a rozhraní Azure API pro FHIR. Další informace o INTELIGENTNÍch možnostech najdete v přehledu [inteligentního stavu](https://smarthealthit.org/).

### <a name="where-can-i-find-what-version-of-fhir-is-running-on-my-database"></a>Kde najdu, jaká verze FHIR je spuštěna v databázi. 

Přesnou verzi FHIR, která je vystavena v příkazu Capability, najdete pod vlastností "fhirVersion".

## <a name="fhir-implementations-and-specifications"></a>FHIR implementace a specifikace

### <a name="can-i-create-a-custom-fhir-resource"></a>Můžu vytvořit vlastní prostředek FHIR?

Nepovolujeme vlastní FHIR prostředky. Pokud potřebujete vlastní prostředek FHIR, můžete vytvořit vlastní prostředek nad [základním prostředkem](http://www.hl7.org/fhir/basic.html) s rozšířeními. 

### <a name="are-extensions-supported-on-azure-api-for-fhir"></a>Podporují Azure API pro FHIR [rozšíření](https://www.hl7.org/fhir/extensibility.html) ?

Umožňuje načíst do serveru veškerá platná data FHIR JSON. Pokud chcete uložit definici struktury definující rozšíření, můžete ho uložit jako prostředek definice struktury. V současné době nelze vyhledávat rozšíření.

### <a name="what-is-the-limit-on-_count"></a>Jaký je limit _count?

Aktuální limit _count je 100. Pokud jste nastavili _count na více než 100, zobrazí se v balíčku upozornění, že se zobrazí pouze 100 záznamů.

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

### <a name="what-is-the-default-sort-when-searching-for-resources-in-azure-api-for-fhir"></a>Co je výchozí řazení při hledání prostředků v Azure API pro FHIR?

Podporujeme řazení podle data poslední aktualizace: _sort = _lastUpdated. Další informace o dalších podporovaných parametrech hledání najdete na [stránce podporované funkce](./fhir-features-supported.md#search).

### <a name="how-does-export-work"></a>Jak $export funguje?

$export je součástí specifikace FHIR: https://hl7.org/fhir/uv/bulkdata/export/index.html . Pokud je u služby FHIR nakonfigurovaná spravovaná identita a účet úložiště, a pokud má spravovaná identita přístup k tomuto účtu úložiště, můžete jednoduše volat $export v rozhraní FHIR API a všechny prostředky FHIR se exportují do účtu úložiště. Další informace najdete [v našem článku o $export](./export-data.md).

## <a name="using-azure-api-for-fhir"></a>Používání Azure API pro FHIR

### <a name="how-do-i-enable-log-analytics-for-azure-api-for-fhir"></a>Návody povolit Log Analytics pro Azure API pro FHIR?

Povolujeme diagnostické protokolování a povolujeme prohlížení ukázkových dotazů pro tyto protokoly. Podrobnosti o povolování protokolů auditu a ukázkových dotazech najdete v [této části](./enable-diagnostic-logging.md). Pokud chcete do protokolů zahrnout další informace, podívejte se na [použití vlastních hlaviček protokolu HTTP](./use-custom-headers.md).

### <a name="where-can-i-see-some-examples-of-using-the-azure-api-for-fhir-within-a-workflow"></a>Kde se můžu podívat na některé příklady použití rozhraní Azure API pro FHIR v rámci pracovního postupu?

Na [stránce GitHub architektury Health Architecture](https://github.com/microsoft/health-architectures)máme k dispozici kolekci referenčních architektur.

### <a name="where-can-i-see-an-example-of-connecting-a-web-application-to-azure-api-for-fhir"></a>Kde se dá zobrazit příklad připojení webové aplikace k rozhraní Azure API pro FHIR?

Máme [stránku GitHubu architektury Health](https://github.com/microsoft/health-architectures) , která obsahuje ukázkové aplikace a scénáře. Ukazuje, jak připojit webovou aplikaci k rozhraní Azure API pro FHIR.  

## <a name="azure-api-for-fhir-features-and-services"></a>Rozhraní Azure API pro funkce a služby FHIR 

### <a name="is-there-a-way-to-encrypt-my-data-using-my-personal-key-not-a-default-key"></a>Existuje způsob, jak šifrovat data pomocí mého osobního klíče, který není výchozím klíčem?

Ano, Azure API for FHIR umožňuje konfiguraci klíčů spravovaných zákazníkem s využitím podpory od Cosmos DB. Další informace o šifrování dat pomocí osobního klíče najdete v [této části](./customer-managed-key.md).

## <a name="azure-api-for-fhir-preview-features"></a>Azure API pro FHIR: funkce ve verzi Preview

### <a name="can-i-configure-scaling-capacity-for-azure-iot-connector-for-fhir-preview"></a>Můžu nakonfigurovat kapacitu škálování pro Azure IoT Connector pro FHIR (Preview)?

Vzhledem k tomu, že Azure IoT Connector pro FHIR je v rámci verze Public Preview zdarma, je kapacita škálování pevná a omezená. Pro konfiguraci služby Azure IoT Connector pro FHIR, která je dostupná ve verzi Public Preview, se očekává, že bude poskytovat propustnost přibližně 200 zpráv za sekundu. K dispozici je určitá forma konfigurace kapacity prostředků ve všeobecné dostupnosti (GA).

### <a name="why-cant-i-install-azure-iot-connector-for-fhir-preview-when-private-link-is-enabled-on-azure-api-for-fhir"></a>Proč není možné nainstalovat Azure IoT Connector pro FHIR (Preview), pokud je v rozhraní Azure API pro FHIR povolené privátní propojení?

Azure IoT Connector pro FHIR v tuto chvíli nepodporuje funkce privátního propojení. Proto pokud máte v rozhraní Azure API pro FHIR povolený privátní odkaz, nemůžete pro FHIR a naopak nainstalovat Azure IoT Connector. Toto omezení se očekává, když je Azure IoT Connector pro FHIR dostupný pro obecnou dostupnost (GA).