---
title: Horizon SDK
titleSuffix: Azure Defender for IoT
description: Sada Horizon SDK umožňuje vývojářům v programu Azure Defender navrhovat moduly plug-in, které dekódují síťový provoz, aby je bylo možné zpracovat pomocí automatizovaného Defenderu pro programy analýzy sítě IoT.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/13/2021
ms.topic: article
ms.service: azure
ms.openlocfilehash: 53aafc4146680c89dd01174ec5fde765f1cc0c01
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100523358"
---
# <a name="horizon-proprietary-protocol-dissector"></a>Nesektorový speciální protokol pro Horizont

Horizont je otevřený vývojové prostředí (ODE) používané k zabezpečení zařízení IoT a ICS s proprietárními protokoly.

Toto prostředí nabízí zákazníkům a technologickým partnerům následující řešení:

- Neomezená, plná podpora pro běžné, proprietární a vlastní protokoly nebo protokoly, které se odchylují od jakéhokoli standardu. 

- Nová úroveň flexibility a rozsahu pro vývoj v DPI.

- Nástroj, který exponenciálně rozšiřuje viditelnost a kontrolu, aniž by bylo nutné upgradovat Defender pro verze platformy IoT.

- Zabezpečení umožňující proprietární vývoj bez sdělování citlivých informací.

Sada Horizon SDK umožňuje vývojářům v programu Azure Defender navrhovat moduly plug-in, které dekódují síťový provoz, aby je bylo možné zpracovat pomocí automatizovaného Defenderu pro programy analýzy sítě IoT.

Pro služby IoT se vyvíjí protokol z hlediska externích modulů plug-in a jsou integrované s rozsáhlým rozsahem programu Defender. Například služby, které poskytují možnosti monitorování, upozorňování a vytváření sestav.

## <a name="secure-development-environment"></a>Zabezpečené vývojové prostředí 

Tento horizont umožňuje vývoj vlastních nebo vlastnických protokolů, které se nedají sdílet mimo organizaci. Například kvůli právním předpisům nebo podnikovým zásadám.

Vývoj modulů plug-in pro inodvětvovou práci bez: 

- Odhalte všechny proprietární informace o tom, jak jsou vaše protokoly definované.

- sdílení kteréhokoli citlivého PCAPsu.

- porušení předpisů pro dodržování předpisů.

## <a name="customization-and-localization"></a>Přizpůsobení a lokalizace  

Sada SDK podporuje různé možnosti přizpůsobení, včetně:

  - Text pro kódy funkcí 

  - Úplný text lokalizace pro výstrahy, události a parametry protokolu. Další informace najdete v tématu [Vytvoření mapovacích souborů (JSON)](#create-mapping-files-json).

  :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="Zobrazit plně lokalizované výstrahy.":::

## <a name="horizon-architecture"></a>Architektura – Horizont

Model architektury zahrnuje tři vrstvy produktu.

:::image type="content" source="media/references-horizon-sdk/architecture.png" alt-text="https://lh6.googleusercontent.com/YFePqJv_6jbI_oy3lCQv-hHB1Qly9a3QQ05uMnI8UdTwhOuxpNAedj_55wseYEQQG2lue8egZS-mlnQZPWfFU1dF4wzGQSJIlUqeXEHg9CG4M7ASCZroKgbghv-OaNoxr3AIZtIh":::

## <a name="defender-for-iot-platform-layer"></a>Defender pro vrstvu platformy IoT

Umožňuje okamžitou integraci a sledování vlastních modulů plug-in v reálném čase v programu Defender pro IoT Platform, aniž by bylo nutné upgradovat Defender pro verzi platformy IoT.

## <a name="defender-for-iot-services-layer"></a>Defender pro vrstvu služeb IoT

Každá služba je navržená jako kanál, oddělený od konkrétního protokolu, což umožňuje efektivnější a nezávislé vývojové prostředí.

Každá služba je navržená jako kanál, oddělený od konkrétního protokolu. Služby naslouchá provozu v kanálu. Komunikují s daty modulů plug-in a provozem zachyceným senzory k indexování nasazených protokolů a k analýze datové části provozu a umožňují efektivnější a nezávislé vývojové prostředí.

## <a name="custom-dissector-layer"></a>Vlastní vrstva pro odsektory 

Umožňuje vytváření modulů plug-in pomocí programu Defender pro sadu IoT SDK (včetně implementace C++ a konfigurace JSON): 

- Definování způsobu identifikace protokolu

- Definujte, jak namapovat pole, která chcete extrahovat z provozu, a extrahujte je 

- Definování způsobu integrace s Defenderem pro služby IoT

  :::image type="content" source="media/references-horizon-sdk/layers.png" alt-text="Předdefinované vrstvy.":::

Defender pro IoT poskytuje základní oblasti pro běžné protokoly. V těchto protokolech můžete vytvořit své složky pro insektory.

## <a name="before-you-begin"></a>Než začnete

## <a name="what-this-sdk-contains"></a>Co tato sada SDK obsahuje 

Tato sada obsahuje hlavičkové soubory potřebné pro vývoj. Proces vývoje vyžaduje základní kroky a volitelné pokročilé kroky popsané v této sadě SDK.

Kontaktujte <support@cyberx-labs.com> pro informace o přijímání hlavičkových souborů a dalších prostředků.

## <a name="about-the-environment-and-setup"></a>O prostředí a instalaci 

### <a name="requirements"></a>Požadavky 

- Preferované vývojové prostředí je Linux. Pokud vyvíjíte v prostředí Windows, zvažte použití virtuálního počítače se systémem Linux.

- Pro proces kompilace použijte RSZ 7.4.0 nebo novější. Použijte libovolnou standardní třídu z Stdlib, která je podporována v jazyce C++ 17.

- Defender pro IoT verze 3,0 a vyšší.

### <a name="process"></a>Proces

1. [Stáhněte](https://www.eclipse.org/) si integrované vývojové prostředí (IDE) pro vývojáře v jazyce C/C++. Můžete použít jakékoli jiné vývojové prostředí (IDE), které dáváte přednost. Tento dokument vás provede konfigurací pomocí integrovaného vývojového prostředí (zatmění).

1. Po spuštění rozhraní IDE pro zatmění a konfiguraci pracovního prostoru (kde budou vaše projekty uložené) stiskněte klávesy **CTRL + n** a vytvořte ho jako projekt C++.

1. Na další obrazovce nastavte název protokolu, který chcete vyvinout, a vyberte typ projektu jako `Shared Library` a `AND Linux GCC` .

1. Upravte vlastnosti projektu v části nastavení nástroje **C/C++ sestavení**  >  **Nastavení**  >  **nástroje nastavení nástroje**  >  **c++ kompilátor RSZ**  >  **různé**  >  **pozice nezávisle na pozici**.

1. Vložte příklady kódů, které jste obdrželi v sadě SDK, a zkompilujte je.

1. Přidejte artefakty (knihovny, config.jsna a metadata) do souboru tar. gz a změňte příponu souboru na \<XXX> . HDP, kde je \<XXX> název modulu plug-in.

### <a name="research"></a>Studoval 

Než začnete, ověřte, že:

- Pokud je k dispozici, přečtěte si specifikaci protokolu.

- Poznáte, která pole protokolu se chystáte extrahovat.

- Naplánujte své cíle mapování.

## <a name="about-plugin-files"></a>O souborech modulu plug-in 

Tři soubory jsou definovány během procesu vývoje.

### <a name="json-configuration-file-required"></a>Konfigurační soubor JSON (povinné) 

Tento soubor by měl definovat ID a deklarace odsektorů, závislosti, požadavky na integraci, parametry ověření a mapování definic pro překlad hodnot na názvy, čísla na text. Další informace najdete na následujících odkazech:

- [Příprava konfiguračního souboru (JSON)](#prepare-the-configuration-file-json)

- [Příprava ověřování implementačního kódu](#prepare-implementation-code-validations)

- [Extrakce metadat zařízení](#extract-device-metadata)

- [Připojení ke službě indexování (směrný plán)](#connect-to-an-indexing-service-baseline)

### <a name="implementation-code-c-required"></a>Implementační kód: C++ (povinné)

Implementační kód (CPP) analyzuje nezpracovaný provoz a mapuje je na hodnoty, jako jsou služby, třídy a kódy funkcí. Extrahuje pole vrstev a mapuje je na názvy indexů z konfiguračních souborů JSON. Pole pro extrakci z CPP jsou definována v konfiguračním souboru. Další informace najdete v tématu [Příprava implementačního kódu (C++)](#prepare-the-implementation-code-c).

### <a name="mapping-files-optional"></a>Mapování souborů (volitelné)

Výstupní text modulu plug-in můžete přizpůsobit tak, aby splňoval požadavky vašeho podnikového prostředí.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="migrace":::

Můžete definovat a aktualizovat mapovací soubory pro aktualizaci textu bez změny kódu. Každý soubor může namapovat jedno nebo několik polí:

  - Mapování hodnot polí na názvy, například 1: Reset, 2: Start, 3: stop.

  - Mapování textu na podporu více jazyků

Další informace najdete v tématu [Vytvoření mapovacích souborů (JSON)](#create-mapping-files-json).

## <a name="create-a-dissector-plugin-overview"></a>Vytvoření typu inodvětvová modul plug-in (přehled)

1. Přečtěte si část [o prostředí a nastavení](#about-the-environment-and-setup) .

2.  [Připravte implementační kód (C++)](#prepare-the-implementation-code-c). Zkopírujte **template.js** do souboru a upravte ji tak, aby vyhovovala vašim potřebám. Klíče neměňte. 

3. [Připravte konfigurační soubor (JSON)](#prepare-the-configuration-file-json). Zkopírujte soubor **template. cpp** a Implementujte metodu override. Další informace naleznete v tématu [horizont::p rotocol:: BaseParser](#horizonprotocolbaseparser) details.

4. [Připravte ověřování kódu implementace](#prepare-implementation-code-validations).

## <a name="prepare-the-implementation-code-c"></a>Příprava implementačního kódu (C++)

Soubor CPP je analyzátor zodpovědný za:

- Ověřování hlavičky paketu a datové části (například délka záhlaví nebo struktura datové části).

- Extrakce dat z hlavičky a datové části do definovaných polí.

- Implementace nakonfigurovaného extrakce polí souborem JSON.

### <a name="what-to-do"></a>Co dělat

Zkopírujte soubor template **. cpp** a Implementujte metodu override. Další informace najdete v tématu [horizont::p rotocol:: BaseParser](#horizonprotocolbaseparser).

### <a name="basic-c-template-sample"></a>Ukázka základních šablon C++ 

V této části najdete základní šablonu protokolu se standardními funkcemi pro ukázkový Defender pro protokol IoT Horizon.

```C++
#include “plugin/plugin.h”
namespace {
 class CyberxHorizonSDK: public horizon::protocol::BaseParser
  public:
   std::vector<uint64_t> processDissectAs(const std::map<std::string,
                                                         std::vector<std::string>> &filters) const override {
     return std::vector<uint64_t>();
   }
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     return horizon::protocol::ParserResult();
   }
 };
}

extern "C" {
  std::shared_ptr<horizon::protocol::BaseParser> create_parser() {
    return std::make_shared<CyberxHorizonSDK>();
  }
}

```

### <a name="basic-c-template-description"></a>Popis šablony základního C++  

V této části najdete základní šablonu protokolu s popisem standardních funkcí pro ukázkový Defender pro protokol IoT Horizon. 

### <a name="include-pluginpluginh"></a>#include "plugin/plugin. h"

Definice, kterou modul plug-in používá. Hlavičkový soubor obsahuje vše potřebné k dokončení vývoje.

### <a name="horizonprotocolbaseparser"></a>Horizont::p rotocol:: BaseParser

Komunikační rozhraní mezi infrastrukturou a vrstvou modulu plug-in. Další informace najdete v tématu [Architektura horizontu](#horizon-architecture) pro přehled vrstev.

ProcessLayer je metoda, která slouží ke zpracování dat.

- První parametr v kódu funkce je nástroj pro zpracování, který se používá pro načítání dříve zpracovaných dat, vytváření nových polí a vrstev.

- Druhým parametrem v kódu funkce jsou aktuální data předaná z předchozího analyzátoru.

```C++
horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer &data) override {

```

### <a name="create_parser"></a>create_parser

Použijte k vytvoření instance vašeho analyzátoru.

:::image type="content" source="media/references-horizon-sdk/code.png" alt-text="https://lh5.googleusercontent.com/bRNtyLpBA3LvDXttSPbxdBK7sHiHXzGXGhLiX3hJ7zCuFhbVsbBhgJlKI6Fd_yniueQqWbClg5EojDwEZSZ219X1Z7osoa849iE9X8enHnUb5to5dzOx2bQ612XOpWh5xqg0c4vR":::

## <a name="protocol-function-code-sample"></a>Ukázka kódu funkce protokolu 

Tato část poskytuje příklad, jak se extrahuje číslo kódu (2 bajty) a délka zprávy (4 bajty).

To se provádí podle souborů ve formátu JSON, což znamená, že pokud je protokol *malými endianmi*, a senzor běží na počítači s malým množstvím souborů, bude převeden.

Vytvoří se také vrstva pro ukládání dat. K vytvoření nových polí použijte *fieldsManager* ze zpracovatelských procesorů. Pole může mít pouze jeden z následujících typů: *řetězec*, *číslo*, *nezpracovaná data*, *pole* (konkrétního typu) nebo *komplexní*. Tato vrstva může obsahovat číslo, RAW nebo řetězec s ID.

V níže uvedené ukázce jsou extrahována následující dvě pole:

- `function_code_number`

- `headerLength`

Vytvoří se nová vrstva a do ní se zkopíruje extrahované pole.

Následující ukázka popisuje konkrétní funkci, která je hlavní logikou implementovanou pro zpracování modulu plug-in.

```C++
namespace {
 class CyberxHorizonProtocol: public horizon::protocol::BaseParser {
  public:
   
   horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                                horizon::general::IDataBuffer &data) override {
     uint16_t codeNumber = data.readUInt16();
     uint32_t headerLength = data.readUInt32();
 
     auto &layer = ctx.createNewLayer();
 
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("code_number"),codeNumber;    
     ctx.getFieldsManager().create(layer,HORIZON_FIELD("header_length"),headerLength);     
     return horizon::protocol::SuccessResult();
   }
 

```

### <a name="related-json-field"></a>Související pole JSON 

:::image type="content" source="media/references-horizon-sdk/json.png" alt-text="Související pole JSON.":::

## <a name="prepare-the-configuration-file-json"></a>Příprava konfiguračního souboru (JSON)

Sada Horizon SDK používá standardní JavaScript Object Notation (JSON), zjednodušený formát pro ukládání a přenos dat a nevyžaduje speciální skriptovací jazyky.

Tato část popisuje minimální deklarace konfigurace JSON, související strukturu a poskytuje ukázkový konfigurační soubor definující protokol. Tento protokol se automaticky integruje se službou zjišťování zařízení.

## <a name="file-structure"></a>Struktura souborů

Následující ukázka popisuje strukturu souborů.

:::image type="content" source="media/references-horizon-sdk/structure.png" alt-text="Ukázka struktury souboru.":::

### <a name="what-to-do"></a>Co dělat

Zkopírujte soubor šablony `config.json` a upravte jej tak, aby vyhovoval vašim potřebám. Neměňte klíč. Klíče jsou v [ukázkovém konfiguračním souboru JSON](#sample-json-configuration-file)označeny červeně. 

### <a name="file-naming-requirements"></a>Požadavky na pojmenovávání souborů 

Konfigurační soubor JSON musí být uložen jako `config.json` .

### <a name="json-configuration-file-fields"></a>Pole konfiguračního souboru JSON

Tato část popisuje pole konfigurace JSON, která budete definovat. Neměňte *popisky* polí.

### <a name="basic-parameters"></a>Základní parametry

Tato část popisuje základní parametry.

| Popisek parametru | Popis | Typ |
|--|--|--|
| **ID** | Název protokolu. Odstraňte výchozí hodnotu a přidejte název vašeho protokolu, jak je zobrazený. | Řetězec |
| **endianess** | Definuje, jak mají být data ve více bajtech kódovaná. Použijte pouze výraz "Little" nebo "Big". Pořízené ze specifikace protokolu nebo záznamu provozu | Řetězec |
| **sanity_failure_codes** | Jedná se o kódy vrácené z analyzátoru v případě, že dojde ke konfliktu správnosti týkající se identity kódu. Viz ověření Magic Number v sekci C++. | Řetězec |
| **malformed_codes** | Jedná se o správně identifikované kódy, ale zjištěna chyba. Například pokud je délka pole příliš krátká nebo dlouhá, nebo je hodnota neplatná. | Řetězec |
| **dissect_as** | Pole definující, kam by měl dorazit určitý provoz protokolu | TCP/UDP, port atd. |
| **pole** | Deklarace, která pole se extrahují z provozu. Každé pole má své vlastní ID (název) a typ (číslo, řetězec, RAW, pole, komplexní). Například [funkce](https://docs.google.com/document/d/14nm8cyoGiaE0ODOYQd_xjULxVz9U_bjfPKkcDhOFr5Q/edit#bookmark=id.6s1zcxa9184k) pole, která je extrahována v souboru analyzátoru implementace. Pole napsaná v konfiguračním souboru jsou pouze ty, které lze přidat do vrstvy. |  |

### <a name="other-advanced-fields"></a>Další rozšířená pole 

Tato část popisuje další pole.

| Popisek parametru | Description |
|-----------------|--------|
| **seznamy povolených** | Hodnoty protokolu můžete indexovat a zobrazovat v sestavách dolování dat. Tyto sestavy odrážejí vaše standardní hodnoty v síti. :::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Ukázka zobrazení dolování dat."::: <br /> Další informace najdete v tématu věnovaném [připojení ke službě indexování (směrný plán)](#connect-to-an-indexing-service-baseline) k podrobnostem. |
| **Firmwar** | Můžete extrahovat informace o firmwaru, definovat hodnoty indexu a aktivovat výstrahy firmwaru pro protokol plug-in. Další informace najdete v tématu o [extrakci dat firmwaru](#extract-firmware-data) . |
| **value_mapping** | Můžete přizpůsobit výstupní text modulu plug-in tak, aby splňoval požadavky vašeho podnikového prostředí definováním a aktualizací mapování souborů. Například mapování na jazykové soubory. Změny lze snadno implementovat na text bez změny nebo ovlivnění kódu. Další informace najdete v tématu [Vytvoření mapovacích souborů (JSON)](#create-mapping-files-json) pro podrobnosti. |

## <a name="sample-json-configuration-file"></a>Ukázkový konfigurační soubor JSON

```json
{
  "id":"CyberX Horizon Protocol",
  "endianess": "big",
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
{
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ]
}


```

## <a name="prepare-implementation-code-validations"></a>Příprava ověřování implementačního kódu

Tato část popisuje implementaci funkcí ověřování kódu C++ a poskytuje vzorový kód. K dispozici jsou dvě vrstvy ověřování:

- Správnosti.

- Kód je poškozen.

Nemusíte vytvářet ověřovací kód, aby bylo možné vytvořit funkční modul plug-in. Pokud nebudete připravovat ověřovací kód, můžete zkontrolovat sestavy dolování dat senzorů jako indikaci úspěšného zpracování.

Hodnoty polí lze namapovat na text v mapování souborů a bez problémů aktualizovat, aniž by to mělo vliv na zpracování.

## <a name="sanity-code-validations"></a>Ověřování kódu správnosti 

Tím se ověří, že přenášený paket odpovídá parametrům ověření protokolu, které vám pomůžou identifikovat protokol v rámci provozu.

Například použijte prvních 8 bajtů jako *Magic Number*. Pokud správnosti selže, vrátí se odpověď na chybu správnosti.

Například:

```C++
  horizon::protocol::ParserResult 
processLayer(horizon::protocol::management::IProcessingUtils &ctx,
                                               horizon::general::IDataBuffer 
&data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }
```

Pokud jste nasadili jiné relevantní moduly plug-in, paket se na ně ověří.

## <a name="malformed-code-validations"></a>Nepoškozená ověření kódu 

Po kladném ověření protokolu se použijí poškozená ověření.

Pokud dojde k selhání při zpracování paketů na základě protokolu, je vrácena odpověď na selhání.

:::image type="content" source="media/references-horizon-sdk/failure.png" alt-text="nesprávně vytvořený kód":::

## <a name="c-sample-with-validations"></a>Ukázka C++ s ověřením

Podle funkce se proces provede, jak je znázorněno v následujícím příkladu.

### <a name="function-20"></a>Funkce 20 

- Je zpracována jako firmware.

- Pole jsou čtena podle funkce.

- Pole jsou přidána do vrstvy.

### <a name="function-10"></a>Funkce 10 

- Funkce obsahuje další dílčí funkci, což je konkrétnější operace.

- Podfunkce je čtena a přidána do vrstvy.

Až to uděláte, zpracování se dokončí. Vrácená hodnota označuje, zda byla fotosektorová vrstva úspěšně zpracována. Pokud bylo, vrstva bude použitelná.

```C++
#include "plugin/plugin.h"

#define FUNCTION_FIRMWARE_RESPONSE 20

#define FUNCTION_SUBFUNCTION_REQUEST 10

namespace {

class CyberxHorizonSDK: public horizon::protocol::BaseParser {

 public:

  std::vector<uint64_t> processDissectAs(const std::map<std::string,

                                                        std::vector<std::string>> &filters) const override {

    return std::vector<uint64_t>();

  }

  horizon::protocol::ParserResult processLayer(horizon::protocol::management::IProcessingUtils &ctx,

                                               horizon::general::IDataBuffer &data) override {

    uint64_t magic = data.readUInt64();

    if (magic != 0xBEEFFEEB) {

      return horizon::protocol::SanityFailureResult(0);

    }

    uint16_t function = data.readUInt16();

    uint32_t length = data.readUInt32();

    if (length > data.getRemaningData()) {

      return horizon::protocol::MalformedResult(0);

    }

    auto &layer = ctx.createNewLayer();

    ctx.getFieldsManager().create(layer, HORIZON_FIELD("function"), function);

    switch (function) {

      case FUNCTION_FIRMWARE_RESPONSE: {

        uint8_t modelLength = data.readUInt8();

        std::string model = data.readString(modelLength);

        uint16_t firmwareVersion = data.readUInt16();

        uint8_t nameLength = data.readUInt8();

        std::string name = data.readString(nameLength);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("model"), model);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("version"), firmwareVersion);

        ctx.getFieldsManager().create(layer, HORIZON_FIELD("name"), name);

      }

      break;

      case FUNCTION_SUBFUNCTION_REQUEST: {

       uint8_t subFunction = data.readUInt8();

       ctx.getFieldsManager().create(layer, HORIZON_FIELD("sub_function"), subFunction);

      }

      break;

    }

    return horizon::protocol::SuccessResult();

  }

};

}

extern "C" {

 std::shared_ptr<horizon::protocol::BaseParser> create_parser() {

   return std::make_shared<CyberxHorizonSDK>();

 }

}
```

## <a name="extract-device-metadata"></a>Extrakce metadat zařízení

Pro prostředky můžete extrahovat následující metadata:

  - `Is_distributed_control_system` – Určuje, zda je protokol součástí systému distribuovaného řízení. (příklad: protokol SCADA by měl mít hodnotu false)

  - `Has_protocol_address` – Určuje, jestli je adresa protokolu; konkrétní adresa pro aktuální protokol, například identifikátor jednotky MODBUS

  - `Is_scada_protocol` – Určuje, jestli je protokol specifický pro sítě.

  - `Is_router_potential` – Určuje, jestli protokol používá hlavně směrovače. Například LLDP, CDP nebo STP.

Aby to bylo možné, je třeba aktualizovat konfigurační soubor JSON pomocí vlastnosti metadata.

## <a name="json-sample-with-metadata"></a>Ukázka JSON s metadaty 

```json

{
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 "fields": [
   {
     "id": "function",
     "type": "numeric"
},
   {
     "id": "sub_function",
     "type": "numeric"
   },
   {
     "id": "name",
     "type": "string"
   },
   {
     "id": "model",
     "type": "string"
   },
   {
     "id": "version",
     "type": "numeric"
   }
 ],
}

```

## <a name="extract-programming-code"></a>Extrakce programovacího kódu 

V případě, že dojde k události programování, můžete extrahovat obsah kódu. Extrahovaný obsah vám umožní:

- Porovnejte obsah souboru s kódem v různých programovacích událostech.

- Aktivuje výstrahu při neautorizovaném programování.  

- Aktivovat událost pro příjem souboru programovacího kódu.

  :::image type="content" source="media/references-horizon-sdk/change.png" alt-text="Programovací protokol změn.":::

  :::image type="content" source="media/references-horizon-sdk/view.png" alt-text="Kliknutím na tlačítko zobrazte programování.":::

  :::image type="content" source="media/references-horizon-sdk/unauthorized.png" alt-text="Neautorizovaná výstraha programování pro PLC":::

Aby to bylo možné dosáhnout, je nutné aktualizovat konfigurační soubor JSON pomocí `code_extraction` Vlastnosti. 

### <a name="json-configuration-fields"></a>Pole konfigurace JSON 

Tato část popisuje pole konfigurace JSON. 

- **Metoda**

  Určuje způsob, jakým jsou přijímány soubory událostí programování. 

  VŠE (akce každého programování způsobí, že všechny soubory kódu budou přijaty i v případě, že existují soubory beze změn).

- **file_type**  

  Určuje typ obsahu kódu.  

  TEXT (soubor s kódem obsahuje textové informace).

- **code_data_field**
  
  Určuje pole implementace, které se má použít, aby se poskytl obsah kódu.

  Dílčí.

- **code_name_field**

  Určuje pole implementace, které se má použít, aby bylo možné zadat název souboru s kódováním.

  Dílčí.

- **size_limit**

  Určuje omezení velikosti každého obsahu souboru pro kódování v BAJTech, pokud soubor kódu překročí nastavený limit, který bude vyřazen. Pokud toto pole není zadané, výchozí hodnota bude 15 000 000, což je 15 MB.

  Automatické.

- **mezipaměť**

  Označuje další informace pro soubor kódu.

  Pole obsahující objekty se dvěma vlastnostmi:

    - název (String) – určuje, že klíč metadat XSense aktuálně podporuje jenom klíč uživatelského jména.
 
    - Value (pole) – určuje pole implementace, které se má použít, aby se daly zadat data metadat.

## <a name="json-sample-with-programming-code"></a>Ukázka JSON s programovacím kódem

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "script",
      "type": "string"
    },
    {
      "id": "script_name",
      "type": "string"
    }, 
      "id": "username",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon 
                                                Protocol Function",
      "alert_text": "There was an attempt by the source to 
                        invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, 
                  for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"code_extraction": {
    "method": "ALL",
    "file_type": "TEXT",
    "code_data_field": "script",
    "code_name_field": "script_name",
    "size_limit": 15000000,
    "metadata": [
      {
        "name": "username",
        "value": "username"
      }
    ]
  }
}

```
## <a name="custom-horizon-alerts"></a>Výstrahy vlastního horizontu

Některé protokoly kód funkce můžou indikovat chybu. Například pokud protokol řídí kontejner s konkrétní chemickou látkou, která musí být uložena vždy při konkrétní teplotě. V takovém případě může být kód funkce indikující chybu v teploměru. Například pokud je kód funkce 25, můžete aktivovat výstrahu ve webové konzole, která indikuje, že došlo k potížím s kontejnerem. V takovém případě můžete definovat hloubkové výstrahy paketů.

Přidejte parametr **výstrahy** do modulu `config.json` Plug-in.

```json
“alerts”: [{
    “id”: 1,
    “message”: “Problem with thermometer at station {IPv4.src}”,
    “title”: “Thermometer problem”,
    “expression”: “{CyberXHorizonProtocol.function} == 25”
}]

```

## <a name="json-configuration-fields"></a>Pole konfigurace JSON

Tato část popisuje pole konfigurace JSON. 

| Název pole | Description | Možné hodnoty |
|--|--|--|
| **ID** | Představuje jedno ID výstrahy. V tomto kontextu musí být jedinečný. | Číselná hodnota 0-10000 |
| **zpráva** | Informace zobrazené uživateli Toto pole umožňuje používat různá pole. | Použijte libovolné pole z vašeho protokolu nebo jakýkoli protokol nižší vrstvy. |
| **hlava** | Název výstrahy |  |
| **expression** | Když chcete, aby se toto upozornění zobrazovalo. | Použijte libovolné číselné pole, které bylo nalezeno v nižších vrstvách, nebo aktuální vrstva.</br></br> Každé pole by mělo mít obálku `{}` , aby ho sada SDK rozpoznala jako pole, podporované logické operátory jsou</br> = = – Rovná se</br> <=-menší než nebo rovno</br> >=-větší než nebo rovno</br> > – více než</br> < – menší než</br> ~ = – Nerovná se |

## <a name="more-about-expressions"></a>Další informace o výrazech

Pokaždé, když sada SDK horizont vyvolá výraz a bude se jednat o *hodnotu true*, aktivuje se ve snímači výstraha.

Do stejné výstrahy lze zahrnout více výrazů. Třeba

`{CyberXHorizonProtocol.function} == 25 and {IPv4.src} == 269488144`.

Tento výraz ověřuje kód funkce pouze v případě, že je 10.10.10.10 paketu IPv4 src. Což je nezpracovaná reprezentace IP adresy v číselném vyjádření.

Můžete použít `and` , nebo, abyste mohli `or` spojit výrazy.

## <a name="json-sample-custom-horizon-alerts"></a>Výstrahy ukázek vlastního horizontu JSON

```json
 "id":"CyberX Horizon Protocol",
 "endianess": "big",
 "metadata": {
   "is_distributed_control_system": false,
   "has_protocol_address": false,
   "is_scada_protocol": true,
   "is_router_potenial": false
 },
 "sanity_failure_codes": {
   "wrong magic": 0
 },
 "malformed_codes": {
   "not enough bytes": 0
 },
 "exports_dissect_as": {
 },
 "dissect_as": {
   "UDP": {
     "port": ["12345"]
   }
 },
 …………………………………….
 “alerts”: [{
“id”: 1,
“message”: “Problem with thermometer at station {IPv4.src}”,
“title”: “Thermometer problem”,
“expression”: “{CyberXHorizonProtocol.function} == 25”

```

## <a name="connect-to-an-indexing-service-baseline"></a>Připojení ke službě indexování (směrný plán)

Hodnoty protokolu můžete indexovat a zobrazovat v sestavách dolování dat.

:::image type="content" source="media/references-horizon-sdk/data-mining.png" alt-text="Zobrazení možnosti dolování dat.":::

Tyto hodnoty mohou být později namapovány na konkrétní text, například mapování čísel jako textů nebo přidávání informací v libovolném jazyce.

:::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="migrace":::

Další informace najdete v tématu [Vytvoření mapovacích souborů (JSON)](#create-mapping-files-json) pro podrobnosti.

K extrakci dalších informací můžete také použít hodnoty z dříve analyzovaných protokolů.

Například pro hodnotu, která je založená na protokolu TCP, můžete použít hodnoty z vrstvy IPv4. Z této vrstvy můžete extrahovat hodnoty, jako je například zdroj paketu, a cíl.

Aby to bylo možné dosáhnout, je nutné aktualizovat konfigurační soubor JSON pomocí `whitelists` Vlastnosti.

## <a name="allow-list-data-mining-fields"></a>Pole pro povolení seznamu (dolování dat)

K dispozici jsou následující pole povoleného seznamu:

- Název – název, který se používá pro indexování.

- alert_title – krátký jedinečný název, který vysvětluje událost.

- alert_text – Další informace

Je možné přidat více seznamů povolených, což umožňuje úplnou flexibilitu při indexování.

## <a name="json-sample-with-indexing"></a>Ukázka JSON s indexováním 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    }

```
## <a name="extract-firmware-data"></a>Extrakce dat firmwaru

Můžete extrahovat informace o firmwaru, definovat hodnoty indexu a aktivovat výstrahy firmwaru pro protokol plug-in. Třeba

- Extrahujte model nebo verzi firmwaru. Tyto informace se dají dál využít k identifikaci CVEs.

- Aktivuje výstrahu, když je zjištěna nová verze firmwaru.

Aby to bylo možné, je třeba aktualizovat konfigurační soubor JSON pomocí vlastnosti firmware.

## <a name="firmware-fields"></a>Pole firmwaru

Tato část popisuje konfigurační pole firmwaru JSON.

- **Jméno**
  
  Určuje, jak je pole zobrazeno v konzole senzorů.

- **value**

 Určuje pole implementace, které se má použít, aby se daly zadat data. 

- **firmware_index:**

  Vyberte jednu z těchto možností:  
  - **model**: model zařízení. Povolí detekci CVEs.
  - **sériové**: sériové číslo zařízení. Sériové číslo není vždy k dispozici pro všechny protokoly. Tato hodnota je jedinečná pro každé zařízení.
  - **rack**: označuje identifikátor stojanu, pokud je zařízení součástí stojanu.
  - **slot**: identifikátor slotu, pokud je zařízení součástí stojanu.  
  - **module_address**: použijte k prezentaci hierarchie, pokud se modul může prezentovat za jiným zařízením. Použije se místo toho, pokud je kombinace slotu stojanu jednodušší prezentace.  
  - **firmware_version**: Určuje verzi zařízení. Povolí detekci CVEs.
  - **alert_text**: označuje text popisující odchylky firmwaru, například změny verze.  
  - **index_by**: Určuje pole sloužící k identifikaci a indexování zařízení. V následujícím příkladu je identifikována IP adresa zařízení. V některých protokolech je možné použít složitější index. Například pokud je připojeno jiné zařízení a víte, jak extrahovat jeho interní cestu. Například ID jednotky MODBUS lze použít jako součást indexu jako jinou kombinaci IP adresy a identifikátoru jednotky.
  - **firmware_fields**: Určuje, která pole jsou pole metadat zařízení. V tomto příkladu jsou použity následující: model, revize a název. Každý protokol může definovat vlastní data firmwaru.

## <a name="json-sample-with-firmware"></a>Ukázka JSON s firmwarem 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset.   
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
}

```
## <a name="extract-device-attributes"></a>Extrakce atributů zařízení 

V zařízení můžete vylepšit informace, které jsou k dispozici v inventáři, dolování dat a dalších sestavách.

- Název

- Typ

- Dodavatel

- Operační systém

Aby to bylo možné, je třeba aktualizovat konfigurační soubor JSON pomocí vlastnosti **Properties** . 

To můžete provést po vytvoření základního modulu plug-in a extrakci požadovaných polí.

## <a name="properties-fields"></a>Pole vlastností

Tato část popisuje konfigurační pole vlastností JSON. 

**config_file** 

Obsahuje název souboru, který definuje, jak se má každý klíč zpracovat v `key` polích. Samotný konfigurační soubor by měl být ve formátu JSON a měl by být součástí složky protokolu modulů plug-in.

Každý klíč ve formátu JSON definuje sadu akcí, které by se měly provést při extrakci tohoto klíče z paketu.

Každý klíč může mít následující:

- **Data paketů** – určuje vlastnosti, které se budou aktualizovat na základě dat extrahovaných z paketu (pole implementace, které slouží k poskytování těchto dat).

- **Statická data** – označuje předdefinovanou sadu `property-value` akcí, které by se měly aktualizovat.

V tomto souboru můžete nakonfigurovat tyto vlastnosti: 

- **Název** – označuje název zařízení.

- **Typ** – určuje typ zařízení.

- **Dodavatel** – označuje dodavatele zařízení.

- **Popis** – označuje model firmwaru zařízení (nižší priorita než "model").

- **operatingSystem** – označuje operační systém zařízení.

### <a name="fields"></a>Pole

| Pole | Description |
|--|--|
| key | Určuje klíč. |
| hodnota | Určuje pole implementace, které se má použít, aby se daly zadat data. |
| is_static_key | Označuje, zda `key` je pole odvozeno jako hodnota z paketu nebo je předdefinovaná hodnota. |

### <a name="working-with-static-keys-only"></a>Práce jenom se statickými klíči

Pokud pracujete se statickými klíči, nemusíte konfigurovat `config.file` . Můžete nakonfigurovat pouze soubor JSON.

## <a name="json-sample-with-properties"></a>Ukázka JSON s vlastnostmi 

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
  
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    },
    {
      "id": "vendor",
      "type": "string"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. 
                  This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  }
"properties": {
    "config_file": "config_file_example",
"fields": [
  {
    "key": "vendor",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },
{
    "key": "name",
    "value": "CyberXHorizonProtocol.vendor",
    "is_static_key": true
  },

]
  }
}

```

## <a name="config_file_exapmle-json"></a>CONFIG_FILE_EXAPMLE JSON 

```json
{
"someKey": {
  "staticData": {
    "model": "FlashSystem", 
    "vendor": "IBM", 
    "type": "Storage"}
  }
  "packetData": [
    "name”  
  ]
}

```

## <a name="create-mapping-files-json"></a>Vytváření mapovacích souborů (JSON)

Můžete přizpůsobit výstupní text modulu plug-in tak, aby splňoval požadavky vašeho podnikového prostředí, a to tak, že definujete a aktualizujete soubory mapování. Změny lze snadno implementovat na text bez změny nebo ovlivnění kódu. Každý soubor může namapovat jedno nebo několik polí.

- Mapování hodnot polí na názvy, například 1: Reset, 2: Start, 3: stop.

- Mapování textu na podporu více jazyků

Lze definovat dva typy mapovacích souborů.

  - [Soubor jednoduchého mapování](#simple-mapping-file)

  - [Soubor mapování závislostí](#dependency-mapping-file).

    :::image type="content" source="media/references-horizon-sdk/localization.png" alt-text="Etherová čistá":::

    :::image type="content" source="media/references-horizon-sdk/unhandled.png" alt-text="Zobrazení neošetřených výstrah.":::

    :::image type="content" source="media/references-horizon-sdk/policy-violation.png" alt-text="Seznam známých porušení zásad.":::

## <a name="file-naming-and-storage-requirements"></a>Požadavky na pojmenovávání souborů a úložiště 

Soubory mapování by měly být uloženy ve složce metadat.

Název souboru by měl odpovídat ID konfiguračního souboru JSON.

:::image type="content" source="media/references-horizon-sdk/json-config.png" alt-text="Ukázka konfiguračního souboru JSON.":::

## <a name="simple-mapping-file"></a>Soubor jednoduchého mapování 

Následující příklad prezentuje základní soubor JSON jako hodnotu klíče.

Když vytvoříte seznam povolených položek a obsahuje jedno nebo více mapovaných polí. Hodnota bude převedena z čísla, řetězce nebo libovolného typu ve formátu na formátovaný text prezentovaný v souboru mapování.

```json
{
  “10”: “Read”,
  “20”: “Firmware Data”,
  “3”: “Write”
}

```

## <a name="dependency-mapping-file"></a>Soubor mapování závislostí 

Chcete-li určit, že soubor je soubor závislosti, přidejte klíčové slovo `dependency` do konfigurace mapování.

```json
dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value":

```

Soubor obsahuje mapování mezi polem závislosti a polem funkce. Například mezi funkcí a dílčí funkcí. Dílčí funkce se mění podle dodané funkce.

V dříve nakonfigurovaném seznamu povolených nastavení neexistuje žádná konfigurace závislosti, jak je znázorněno níže.

```json
"whitelists": [
{
"name": "Functions",
"alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
"alert_text": "There was an attempt by the source to invoke a new function on the destination",
"fields": [
{
"name": "Source",
"value": "IPv4.src"
},
{
"name": "Destination",
"value": "IPv4.dst"
},
{
"name": "Function",
"value": "CyberXHorizonProtocol.function"
}
]
}

```

Závislost může být založena na konkrétní hodnotě nebo poli. V následujícím příkladu je založena na poli. Pokud je založena na hodnotě, definujte hodnotu extrakce, která bude načtena ze souboru mapování.

V následujícím příkladu se závislost sleduje u stejné hodnoty pole.

Například v dílčí funkci 5 je význam změněn na základě funkce.

  - Pokud se jedná o funkci čtení, pak pět znamená čtení paměti.

  - Pokud se jedná o funkci zápisu, použije se stejná hodnota ke čtení ze souboru.

    ```json
    {
      “10”: {
         “5”:  “Memory”,
         “6”: “File”,
         “7” “Register”
       },
      “3”: {
       “5”: “File”,
       “7”: “Memory”,
       “6”, “Register”
      }
    }

    ```

### <a name="sample-file"></a>Ukázkový soubor

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {"is_distributed_control_system": false, "has_protocol_address": false, "is_scada_protocol": true, "is_router_potenial": false},
  "sanity_failure_codes": { "wrong magic": 0 },
  "malformed_codes": { "not enough bytes": 0  },
  "exports_dissect_as": {  },
  "dissect_as": { "UDP": { "port": ["12345"] }},
  "fields": [{ "id": "function", "type": "numeric" }, { "id": "sub_function", "type": "numeric" },
     {"id": "name", "type": "string" }, { "id": "model", "type": "string" }, { "id": "version", "type": "numeric" }],
  "whitelists": [{
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
      "fields": [{ "name": "Source", "value": "IPv4.src" }, { "name": "Destination", "value": "IPv4.dst" },
        { "name": "Function", "value": "CyberXHorizonProtocol.function" },
        { "name": "Sub function", "value": "CyberXHorizonProtocol.sub_function", "dependency": { "field": "CyberXHorizonProtocol.function"  }}]
  }],
  "firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [{ "name": "Device", "value": "IPv4.src", "owner": true }],
    "firmware_fields": [{ "name": "Model", "value": "CyberXHorizonProtocol.model", "firmware_index": "model" },
       { "name": "Revision", "value": "CyberXHorizonProtocol.version", "firmware_index": "firmware_version" },
       { "name": "Name", "value": "CyberXHorizonProtocol.name" }]
  },
  "value_mapping": {
      "CyberXHorizonProtocol.function": {
        "file": "function-mapping"
      },
      "CyberXHorizonProtocol.sub_function": {
        "dependency": true,
        "file": "sub_function-mapping"
      }
  }
}

```

## <a name="json-sample-with-mapping"></a>Ukázka JSON s mapováním

```json
{
  "id":"CyberXHorizonProtocol",
  "endianess": "big",
  "metadata": {
    "is_distributed_control_system": false,
    "has_protocol_address": false,
    "is_scada_protocol": true,
    "is_router_potenial": false
  },
  "sanity_failure_codes": {
    "wrong magic": 0
  },
  "malformed_codes": {
    "not enough bytes": 0
  },
  "exports_dissect_as": {
  },
  "dissect_as": {
    "UDP": {
      "port": ["12345"]
    }
  },
  "fields": [
    {
      "id": "function",
      "type": "numeric"
    },
    {
      "id": "sub_function",
      "type": "numeric"
    },
    {
      "id": "name",
      "type": "string"
    },
    {
      "id": "model",
      "type": "string"
    },
    {
      "id": "version",
      "type": "numeric"
    }
  ],
"whitelists": [
    {
      "name": "Functions",
      "alert_title": "New Activity Detected - CyberX Horizon Protocol Function",
      "alert_text": "There was an attempt by the source to invoke a new function on the destination",
       "fields": [
        {
          "name": "Source",
          "value": "IPv4.src"
        },
        {
          "name": "Destination",
          "value": "IPv4.dst"
        },
        {
          "name": "Function",
          "value": "CyberXHorizonProtocol.function"
        },
        {
          “name”: “Sub function”,
          “value”: “CyberXHorizonProtocol.sub_function”,
          “dependency”: {
              “field”: “CyberXHorizonProtocol.function”
        }
      ]
    },
"firmware": {
    "alert_text": "Firmware was changed on a network asset. This may be a planned activity, for example an authorized maintenance procedure",
    "index_by": [
      {
        "name": "Device",
        "value": "IPv4.src",
        "owner": true
      }
    ],
    "firmware_fields": [,
      {
        "name": "Model",
        "value": "CyberXHorizonProtocol.model",
        "firmware_index": "model"
      },
      {
        "name": "Revision",
        "value": "CyberXHorizonProtocol.version",
        “Firmware_index”: “firmware_version”
      },
      {
        "name": "Name",
        "value": "CyberXHorizonProtocol.name"
      }
    ]
  },
"value_mapping": {
    "CyberXHorizonProtocol.function": {
      "file": "function-mapping"
    },
    "CyberXHorizonProtocol.sub_function": {
      "dependency": true,
      "file": "sub_function-mapping"
    }
}

```
## <a name="package-upload-and-monitor-the-plugin"></a>Zabalení, nahrání a monitorování modulu plug-in 

Tato část popisuje, jak

  - Zabalit váš modul plug-in.

  - Nahrajte modul plug-in.

  - Monitorujte a ladit modul plug-in a vyhodnoťte, jak dobře funguje.

Postup při zabalení modulu plug-in:

1. Přidejte **artefakt** (může být, knihovna, config.js, nebo metadata) do `tar.gz` souboru.

1. Změňte příponu souboru na \<XXX.hdp> , kde je \<XXX> název modulu plug-in.

Přihlášení k konzole horizontu:

1.  Přihlaste se ke snímači CLI jako správce, CyberX nebo uživatel podpory.

2.  V souboru: `/var/cyberx/properties/horizon.properties` změňte vlastnost **UI. Enabled** na **hodnotu true** ( `horizon.properties:ui.enabled=true` ).

3.  Přihlaste se ke konzole senzorů.

4.  V hlavní nabídce vyberte možnost **horizont** .

    :::image type="content" source="media/references-horizon-sdk/horizon.png" alt-text="V levém podokně vyberte možnost horizont.":::

    Otevře se konzola horizont.

    :::image type="content" source="media/references-horizon-sdk/plugins.png" alt-text="Zobrazení konzole horizontu a všech jeho modulů plug-in.":::

## <a name="plugins-pane"></a>Podokno modulů plug-in

Podokno modul plug-in obsahuje seznam:

  - Moduly plug-in infrastruktury: moduly plug-in ve výchozím nastavení nainstalované v programu Defender pro IoT.

  - Moduly plug-in aplikací: moduly plug-in aplikací nainstalované ve výchozím nastavení s programem Defender pro IoT a další moduly plug-in vyvinuté v programu Defender pro IoT nebo externí
    
Povolte a zakažte moduly plug-in, které byly nahrány pomocí přepínače.

:::image type="content" source="media/references-horizon-sdk/toggle.png" alt-text="Přepínač CIP":::

### <a name="uploading-a-plugin"></a>Nahrává se modul plug-in.

Po vytvoření a zabalení modulu plug-in je můžete odeslat do programu Defender pro IoT snímač. Chcete-li dosáhnout plného pokrytí vaší sítě, měli byste nahrát modul plug-in do každého snímače ve vaší organizaci.

Pro nahrání:

1.  Přihlaste se ke svému senzoru.


2. Vyberte **Nahrát**.

    :::image type="content" source="media/references-horizon-sdk/upload.png" alt-text="Nahrajte své moduly plug-in.":::

3. Přejděte k modulu plug-in a přetáhněte ho do dialogového okna modulu plug-in. Ověřte, zda je předpona `.hdp` . Modul plug-in se načte.

## <a name="plugin-status-overview"></a>Přehled stavu modulu plug-in 

Okno **Přehled** konzoly Horizontal poskytuje informace o modulu plug-in, který jste nahráli, a umožňuje ho zakázat a povolit.

:::image type="content" source="media/references-horizon-sdk/overview.png" alt-text="Přehled konzole horizontu":::

| Pole | Description |
|--|--|
| Aplikace | Název modulu plug-in, který jste nahráli. |
| :::image type="content" source="media/references-horizon-sdk/switch.png" alt-text="Přepínač Zapnuto a vypnuto."::: | Zapne **nebo** **vypne** modul plug-in. Defender pro IoT nebude při přepínání mimo modul plug-in zpracovávat provoz protokolu definovaný v modulu plug-in. |
| Čas | Čas poslední analýzy dat Aktualizováno každých 5 sekund. |
| PPS | Počet paketů za sekundu. |
| Šířka pásma | Průměrná šířka pásma zjištěná během posledních 5 sekund. |
| Malforms | Po kladném ověření protokolu se použijí poškozená ověření. Pokud dojde k selhání při zpracování paketů na základě protokolu, je vrácena odpověď na selhání.   <br><br>V tomto sloupci se zobrazuje počet malform chyb za posledních 5 sekund. Další informace najdete v tématu podrobnosti o [nesprávném kódu](#malformed-code-validations) . |
| Upozornění | Pakety odpovídají struktuře a specifikaci, ale v závislosti na konfiguraci upozornění modulu plug-in došlo k neočekávanému chování. |
| Chyby | Počet paketů, které selhaly při ověřování základního protokolu. Ověří, jestli paket odpovídá definicím protokolu. Zobrazené číslo znamená, že počet chyb zjištěných za posledních 5 sekund. Další informace najdete v tématu [ověřování kódu správnosti](#sanity-code-validations) pro podrobnosti. |
| :::image type="content" source="media/references-horizon-sdk/monitor.png" alt-text="Ikona monitorování"::: | Zkontrolujte podrobnosti o malform a upozorněních zjištěných pro váš modul plug-in. |

## <a name="plugin-details"></a>Podrobnosti modulu plug-in

Můžete monitorovat chování modulu plug-in v reálném čase analýzou počtu *Malform* a *Upozornění* zjištěných pro váš modul plug-in. Možnost je k dispozici pro zablokování obrazovky a exportu pro další šetření.

:::image type="content" source="media/references-horizon-sdk/snmp.png" alt-text="Obrazovka monitorování protokolu SNMP.":::

Monitorování:

Vyberte tlačítko monitor pro modul plug-in z přehledu.

Další kroky

Nastavení [rozhraní API pro horizont](references-horizon-api.md)
