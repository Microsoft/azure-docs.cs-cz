---
title: Horizon API
description: Tato příručka popisuje běžně používané metody horizontu.
ms.date: 1/5/2021
ms.topic: article
ms.openlocfilehash: b65f7663df29e2c82faa5d1aeec3b820d5fbaf70
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786783"
---
# <a name="horizon-api"></a>Horizon API 

Tato příručka popisuje běžně používané metody horizontu.

### <a name="getting-more-information"></a>Získání dalších informací

Další informace o práci s horizontem a s platformou Defender for IoT najdete v následujících informacích:

- V případě sady SDK pro vývoj s otevřeným vývojovým prostředím (ODE) se obraťte na svého zástupce v programu IoT.
- Informace o podpoře a odstraňování potíží získáte od společnosti <support@cyberx-labs.com> .

- Pokud chcete získat přístup k uživatelské příručce Defender for IoT z konzoly Defender pro IoT, vyberte :::image type="icon" source="media/references-horizon-api/profile.png"::: a vyberte **Stáhnout uživatelskou příručku**.


## `horizon::protocol::BaseParser`

Abstraktní pro všechny moduly plug-in To se skládá ze dvou metod:

- Pro zpracování filtrů modulů plug-in definovaných výše. Tímto způsobem Horizon ví, jak komunikovat s analyzátorem.
- Pro zpracování skutečných dat.

## `std::shared_ptr<horizon::protocol::BaseParser> create_parser()`

První funkce, která je volána pro váš modul plug-in, vytvoří instanci analyzátoru pro nástroj horizont a zaregistruje ji.

### <a name="parameters"></a>Parametry 

Žádné

### <a name="return-value"></a>Vrácená hodnota

shared_ptr ke své instanci analyzátoru.

## `std::vector<uint64_t> horizon::protocol::BaseParser::processDissectAs(const std::map<std::string, std::vector<std::string>> &) const`

Tato funkce se bude volat pro každý modul plug-in, který je registrovaný výše. 

Ve většině případů bude tato akce prázdná. Vyvolejte výjimku pro horizont, aby bylo známo, že došlo k špatnému výskytu.

### <a name="parameters"></a>Parametry 

- Mapa obsahující strukturu dissect_as, jak je definována v config.jsjiného modulu plug-in, který se chce zaregistrovat přes sebe.

### <a name="return-value"></a>Vrácená hodnota 

Pole uint64_t, což je registrace zpracovaná do druhu uint64_t. To znamená, že na mapě budete mít seznam portů, jejichž hodnoty budou uin64_t.

## `horizon::protocol::ParserResult horizon::protocol::BaseParser::processLayer(horizon::protocol::management::IProcessingUtils &,horizon::general::IDataBuffer &)`

Funkce main Konkrétně logika modulu plug-in, pokaždé, když nový paket dosáhne vašeho analyzátoru. Tato funkce bude volána, zde by měla být provedena vše související se zpracováním paketů.

### <a name="considerations"></a>Požadavky

Váš modul plug-in by měl být bezpečný pro přístup z více vláken, protože tuto funkci lze volat z různých vláken. Dobrým přístupem je definování všeho v zásobníku.

### <a name="parameters"></a>Parametry

- Řídicí jednotka sady SDK zodpovědná za ukládání dat a vytváření objektů souvisejících s SDK, jako jsou například ILayer a pole.
- Pomocná osoba pro čtení dat nezpracovaného paketu. Je již nastaveno na základě pořadí bajtů, které jste definovali v config.js.

### <a name="return-value"></a>Vrácená hodnota 

Výsledek zpracování. Může to být buď *úspěch*, *poškozený* nebo *správnosti*.

## `horizon::protocol::SanityFailureResult: public horizon::protocol::ParserResult`

Označí zpracování jako kanalizace selhání, což znamená, že paket není rozpoznán aktuálním protokolem a horizont by ho měl předat dalšímu analyzátoru, pokud je jakýkoli zaregistrován ve stejných filtrech.

## `horizon::protocol::SanityFailureResult::SanityFailureResult(uint64_t)`

Konstruktor

### <a name="parameters"></a>Parametry 

- Definuje kód chyby používaný v horizontu pro protokolování, jak je definováno v config.js.

## `horizon::protocol::MalformedResult: public horizon::protocol::ParserResult`

Chybný výsledek, označili jsme, že jsme už tento paket rozpoznali jako náš protokol, ale některé ověření se nepovedlo (rezervované bity jsou zapnuté nebo chybí nějaké pole).

## `horizon::protocol::MalformedResult::MalformedResult(uint64_t)`

Konstruktor

### <a name="parameters"></a>Parametry  

- Kód chyby, jak je definován v config.js.

## `horizon::protocol::SuccessResult: public horizon::protocol::ParserResult`

Oznamuje horizontu úspěšného zpracování. Po úspěšném přijetí paketu, data patří do USA a byla extrahována všechna data.

## `horizon::protocol::SuccessResult()`

Konstruktor Byl vytvořen základní úspěšný výsledek. To znamená, že neznáte směr ani žádná další metadata týkající se paketu.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection)`

Konstruktor

### <a name="parameters"></a>Parametry 

- Směr paketu, je-li určen. Hodnoty mohou být *žádosti* nebo *odpovědi*.

## `horizon::protocol::SuccessResult(horizon::protocol::ParserResultDirection, const std::vector<uint64_t> &)`

Konstruktor

### <a name="parameters"></a>Parametry

- Směr paketu, pokud jsme ho identifikovali, může být *Request*, *Response*.
- Varování. Tyto události nebudou úspěšné, ale Horizont se oznámí.

## `horizon::protocol::SuccessResult(const std::vector<uint64_t> &)`

Konstruktor

### <a name="parameters"></a>Parametry 

-  Varování. Tyto události nebudou úspěšné, ale Horizont se oznámí.

## `HorizonID HORIZON_FIELD(const std::string_view &)`

Převede odkaz založený na řetězci na název pole (například function_code) na HorizonID.

### <a name="parameters"></a>Parametry 

- Řetězec, který se má převést

### <a name="return-value"></a>Vrácená hodnota

- HorizonID vytvořeno z řetězce.

## `horizon::protocol::ILayer &horizon::protocol::management::IProcessingUtils::createNewLayer()`

Vytvoří novou vrstvu, takže horizont chce, aby si modul plug-in uložil nějaká data. Toto je základní jednotka úložiště, kterou byste měli použít.

### <a name="return-value"></a>Vrácená hodnota

Odkaz na vytvořenou vrstvu, takže do ní můžete přidat data.

## `horizon::protocol::management::IFieldManagement &horizon::protocol::management::IProcessingUtils::getFieldsManager()`

Získá objekt správy pole, který je zodpovědný za vytváření polí v různých objektech, například na ILayer.

### <a name="return-value"></a>Vrácená hodnota

Odkaz na manažera.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, uint64_t)`

Vytvoří nové číselné pole 64 bitů ve vrstvě s požadovaným ID.

### <a name="parameters"></a>Parametry 

- Vrstva, kterou jste vytvořili dříve.
- HorizonID vytvořeno pomocí makra **HORIZON_FIELD** .
- Nezpracovaná hodnota, kterou chcete uložit.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::string)`

Vytvoří nové pole řetězce ve vrstvě s požadovaným ID. Paměť se přesune, takže buďte opatrní. Tuto hodnotu už nebudete moct znovu použít.

### <a name="parameters"></a>Parametry  

- Vrstva, kterou jste vytvořili dříve.
- HorizonID vytvořeno pomocí makra **HORIZON_FIELD** .
- Nezpracovaná hodnota, kterou chcete uložit.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, std::vector<char> &)`

Vytvoří novou nezpracovanou hodnotu pole (pole bajtů) na vrstvě s požadovaným ID. Paměť se přesune, takže buďte opatrní, nebudete moct tuto hodnotu znovu použít.

### <a name="parameters"></a>Parametry

- Vrstva, kterou jste vytvořili dříve.
- HorizonID vytvořeno pomocí makra **HORIZON_FIELD** .
- Nezpracovaná hodnota, kterou chcete uložit.

## `horizon::protocol::IFieldValueArray &horizon::protocol::management::IFieldManagement::create(horizon::protocol::ILayer &, HorizonID, horizon::protocol::FieldValueType)`

Vytvoří pole hodnota pole (Array) ve vrstvě zadaného typu s požadovaným ID.

### <a name="parameters"></a>Parametry

- Vrstva, kterou jste vytvořili dříve.
- HorizonID vytvořeno pomocí makra **HORIZON_FIELD** .
- Typ hodnot, které budou uloženy v poli.

### <a name="return-value"></a>Vrácená hodnota

Odkaz na pole, do kterého by měly být připojeny hodnoty.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, uint64_t)`

Připojí novou celočíselnou hodnotu k poli, které bylo dříve vytvořeno.

### <a name="parameters"></a>Parametry

- Pole, které bylo vytvořeno dříve.
- Nezpracovaná hodnota, která má být uložena v poli.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::string)`

Připojí novou řetězcovou hodnotu k poli, které jste vytvořili dříve. Paměť se přesune, takže buďte opatrní, nebudete moct tuto hodnotu znovu použít.

### <a name="parameters"></a>Parametry

- Pole, které bylo vytvořeno dříve.
- Nezpracovaná hodnota, která má být uložena v poli.

## `void horizon::protocol::management::IFieldManagement::create(horizon::protocol::IFieldValueArray &, std::vector<char> &)`

Připojí novou nezpracovanou hodnotu k poli, které bylo dříve vytvořeno. Paměť se přesune, takže buďte opatrní, nebudete moct tuto hodnotu znovu použít.

### <a name="parameters"></a>Parametry

- Pole, které bylo vytvořeno dříve.
- Nezpracovaná hodnota, která má být uložena v poli.

## `bool horizon::general::IDataBuffer::validateRemainingSize(size_t)`

Kontroluje, zda vyrovnávací paměť obsahuje alespoň X bajtů.

### <a name="parameters"></a>Parametry

Počet bajtů, které by měly existovat.

### <a name="return-value"></a>Vrácená hodnota

True, pokud vyrovnávací paměť obsahuje alespoň X bajtů. V opačném případě je to `False` .

## `uint8_t horizon::general::IDataBuffer::readUInt8()`

Přečte hodnotu uint8 (1 bajt) z vyrovnávací paměti podle pořadí bajtů.

### <a name="return-value"></a>Vrácená hodnota

Hodnota načtená z vyrovnávací paměti.

## `uint16_t horizon::general::IDataBuffer::readUInt16()`

Přečte hodnotu UInt16 (2 bajty) z vyrovnávací paměti podle pořadí bajtů.

### <a name="return-value"></a>Vrácená hodnota

Hodnota načtená z vyrovnávací paměti.

## `uint32_t horizon::general::IDataBuffer::readUInt32()`

Načte hodnotu UInt32 (4 bajty) z vyrovnávací paměti podle pořadí bajtů.

### <a name="return-value"></a>Vrácená hodnota

Hodnota načtená z vyrovnávací paměti.

## `uint64_t horizon::general::IDataBuffer::readUInt64()`

Přečte hodnotu UInt64 (8 bajtů) z vyrovnávací paměti podle pořadí bajtů.

### <a name="return-value"></a>Vrácená hodnota

Hodnota načtená z vyrovnávací paměti.

## `void horizon::general::IDataBuffer::readIntoRawData(void *, size_t)`

Čtení do předem přidělené paměti zadané velikosti bude ve skutečnosti kopírovat data do vaší oblasti paměti.

### <a name="parameters"></a>Parametry 

- Oblast paměti, do které se mají kopírovat data
- Velikost oblasti paměti, tento parametr také definuje počet bajtů, které budou zkopírovány.

## `std::string_view horizon::general::IDataBuffer::readString(size_t)`

Načte do řetězce z vyrovnávací paměti.

### <a name="parameters"></a>Parametry 

- Počet bajtů, které mají být čteny.

### <a name="return-value"></a>Vrácená hodnota

Odkaz na oblast paměti řetězce.

## `size_t horizon::general::IDataBuffer::getRemainingData()`

Oznamuje, kolik bajtů zbývá ve vyrovnávací paměti.

### <a name="return-value"></a>Vrácená hodnota

Zbývající velikost vyrovnávací paměti.

## `void horizon::general::IDataBuffer::skip(size_t)`

Přeskočí X bajtů ve vyrovnávací paměti.

### <a name="parameters"></a>Parametry

- Počet bajtů, které mají být přeskočeny.
