---
title: Kódy chyb klienta pro aktualizaci zařízení pro Azure IoT Hub | Microsoft Docs
description: Tento dokument poskytuje tabulku kódů chyb klienta pro různé součásti aktualizace zařízení.
author: chrisjlin
ms.author: lichris
ms.date: 2/18/2021
ms.topic: reference
ms.service: iot-hub-device-update
ms.openlocfilehash: dbdddc7cee0c3664a83501ba619a38e1cc44e1f3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103200342"
---
# <a name="device-update-for-iot-hub-error-codes"></a>Aktualizace zařízení pro kódy chyb IoT Hub

Tento dokument poskytuje tabulku chybových kódů pro různé součásti aktualizace zařízení. To je určeno pro použití jako reference pro uživatele, kteří se chtějí pokusit analyzovat své vlastní chybové kódy a diagnostikovat problémy a řešit potíže.

Existují dvě primární komponenty na straně klienta, které mohou vyvolat chybové kódy: Agent aktualizace zařízení a Agent pro optimalizaci doručení.

## <a name="device-update-agent"></a>Agent aktualizace zařízení

### <a name="resultcode-and-extendedresultcode"></a>ResultCode a ExtendedResultCode

Aktualizace zařízení pro IoT Hub základní rozhraní PnP `ResultCode` `ExtendedResultCode` , která se dají použít k diagnostice selhání. [Přečtěte si další informace](device-update-plug-and-play.md) o rozhraní PnP jádra aktualizace zařízení.

#### <a name="resultcode"></a>ResultCode

`ResultCode` je obecný stavový kód a následuje konvence stavových kódů HTTP.
[Přečtěte si další informace](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html) o stavových kódech HTTP.

#### <a name="extendedresultcode"></a>ExtendedResultCode

`ExtendedResultCode` je celé číslo s kódovanými informacemi o chybách.

V rozhraní PnP se pravděpodobně zobrazí `ExtendedResultCode` jako celé číslo se znaménkem. Chcete-li dekódovat `ExtendedResultCode` , převeďte celé číslo se znaménkem na unsigned Hex. Používá se jenom prvních 4 bajtů z, `ExtendedResultCode` `F` `FFFFFFF` kde první Nibble je **kód zařízení** a zbytek bitů je **kód chyby**.

**Kódy zařízení**

| Kód zařízení     | Description  |
|-------------------|--------------|
| D                 | Chyba vyvolaná ze sady SDK do sady|
| E                 | Kód chyby je errno |


Například:

`ExtendedResultCode` je `-536870781`

Šestnáctkové vyjádření bez znaménka `-536870781` je `FFFFFFFF E0000083` .

| Ignorovat    | Kód zařízení  | Kód chyby   |
|-----------|----------------|--------------|
| FFFFFFFF  | E              | 0000083      |

`0x83` v šestnáctkové soustavě je `131` číslo v desítkové soustavě, což je hodnota errno pro `ENOLCK` .

## <a name="delivery-optimization-agent"></a>Agent Optimalizace doručení
V následující tabulce jsou uvedeny kódy chyb, které se týkají součásti Optimalizace doručení (DO) klienta aktualizace zařízení. Komponenta do je zodpovědná za stažení obsahu aktualizace do zařízení IoT.

Kód chyby lze získat prozkoumáním výjimek vyvolaných v reakci na volání rozhraní API. Kódy všech chyb lze identifikovat pomocí předpony 0x80D0.

| Kód chyby  | Chyba řetězce                       | Typ                 | Description |
|-------------|------------------------------------|----------------------|-------------|
| 0x80D01001L | DO_E_NO_SERVICE                    | Není k dispozici                  | Služba optimalizace doručení nemohla poskytnout službu. |
| 0x80D02002L | DO_E_DOWNLOAD_NO_PROGRESS          | Stažení úlohy         | Stažení souboru v rámci definovaného období neprobíhalo. |
| 0x80D02011L | DO_E_UNKNOWN_PROPERTY_ID           | Stažení úlohy         | SetProperty () nebo GetProperty () se volala s neznámým ID vlastnosti. |
| 0x80D02012L | DO_E_READ_ONLY_PROPERTY            | Stažení úlohy         | Nelze volat metodu SetProperty () pro vlastnost, která je jen pro čtení. |
| 0x80D02013L | DO_E_INVALID_STATE                 | Stažení úlohy         | Požadovaná akce není v aktuálním stavu úlohy povolena. Úloha se možná zrušila nebo dokončila její přenos. Nyní je ve stavu jen pro čtení. |
| 0x80D02018L | DO_E_FILE_DOWNLOADSINK_UNSPECIFIED | Stažení úlohy         | Stažení se nedá spustit, protože se nezadala žádná jímka pro stažení (buď lokální soubor, nebo rozhraní streamování). |
| 0x80D02200L | DO_E_DOWNLOAD_NO_URI               | Rozhraní IDODownload| Stahování bylo zahájeno bez zadání identifikátoru URI. |
| 0x80D03805L | DO_E_BLOCKED_BY_NO_NETWORK         | Přechodné podmínky | Stahování pozastaveno z důvodu ztráty připojení k síti |

## <a name="device-update-content-service"></a>Služba obsahu aktualizace zařízení
V následující tabulce jsou uvedeny kódy chyb, které se týkají komponenty služby obsahu služby aktualizace zařízení. Komponenta Content Service zodpovídá za zpracování importu obsahu aktualizace.

| Kód chyby                    | Chyba řetězce                                                               | Další kroky                         |
|-------------------------------|----------------------------------------------------------------------------|------------------------------------|
| "UpdateAlreadyExists"         | Aktualizace se stejnou identitou již existuje.                              | Nezapomeňte importovat aktualizaci, která ještě nebyla naimportována do této instance aktualizace zařízení pro IoT Hub. |
| "DuplicateContentImport"      | Identický obsah byl importován současně vícekrát.                  | Stejné jako pro UpdateAlreadyExists. |
| "CannotProcessImportManifest" | Při zpracování importu manifestu došlo k chybě.                                          | Správné formátování importu manifestu najdete v tématu [Import konceptů](./import-concepts.md) a importu dokumentace k [aktualizaci](./import-update.md) . |
| "CannotDownload"              | Nelze stáhnout manifest importu.                                           | Zkontrolujte, zda je adresa URL souboru manifestu importu stále platná. |
| "CannotParse"                 | Manifest importu nelze analyzovat.                                              | Podívejte se na manifest importu pro přesnost schématu definovaného v dokumentaci k [importu aktualizací](./import-update.md) . |
| "UnsupportedVersion"          | Verze schématu importu manifestu není podporována.                           | Ujistěte se, že manifest importu používá nejnovější schéma definované v dokumentaci k [importu aktualizací](./import-update.md) . |
| "UpdateLimitExceeded"         | Při importování aktualizace z důvodu překročení limitu došlo k chybě.                              | Dosáhli jste limitu počtu různých zprostředkovatelů, názvů nebo verzí povolených ve vaší instanci aktualizace zařízení pro IoT Hub. Odstraňte některé aktualizace z instance a zkuste to znovu. |
| "UpdateProvider"              | Nelze importovat nového poskytovatele aktualizace.                                       | Dosáhli jste limitu počtu různých __zprostředkovatelů__ povolených ve vaší instanci aktualizace zařízení pro IoT Hub. Odstraňte některé aktualizace z instance a zkuste to znovu. |
| "Update"                  | Pro zadaného zprostředkovatele nelze importovat nový název aktualizace.                | Dosáhli jste limitu počtu různých __názvů__ povolených v rámci jednoho poskytovatele v instanci aktualizace zařízení pro IoT Hub. Odstraňte některé aktualizace z instance a zkuste to znovu. |
| UpdateVersion               | Pro zadaného zprostředkovatele a název nelze importovat novou verzi aktualizace.    | Dosáhli jste limitu počtu různých __verzí__ povolených v rámci jednoho poskytovatele a názvu ve vaší instanci aktualizace zařízení pro IoT Hub. Odstraňte některé aktualizace s tímto názvem z vaší instance a zkuste to znovu. |
| "UpdateProviderCompatibility" | Nelze importovat další poskytovatele aktualizace se zadanou kompatibilitou. | Při definování vlastností kompatibility modelů zařízení a modelů zařízení v manifestu importu Pamatujte na to, že aktualizace zařízení pro IoT Hub podporuje jeden zprostředkovatel a kombinaci názvů pro daného výrobce nebo model. To znamená, že pokud se pokusíte použít stejné vlastnosti kompatibility výrobce a modelu s více než jednou kombinací poskytovatele/názvu, zobrazí se tyto chyby. Chcete-li tento problém vyřešit, zajistěte, aby všechny aktualizace daného zařízení (definované výrobcem/modelem) používaly stejné zprostředkovatele a název. I když to není vyžadováno, možná budete chtít vzít v úvahu, že poskytovatel je stejný jako výrobce a stejný jako název modelu, a to jenom pro jednoduchost. |
| "UpdateNameCompatibility"     | Nelze importovat další název aktualizace se zadanou kompatibilitou.     | Stejné jako pro UpdateProviderCompatibility. ContentLimitNamespaceCompatibility. |
| "UpdateVersionCompatibility"  | Nelze importovat další verzi aktualizace se zadanou kompatibilitou.  | Stejné jako pro UpdateProviderCompatibility. ContentLimitNamespaceCompatibility. |
| "CannotProcessUpdateFile"     | Při zpracování zdrojového souboru došlo k chybě.                                              |                                    |
| "ContentFileCannotDownload"   | Zdrojový soubor nelze stáhnout.                                               | Zkontrolujte, zda je adresa URL pro soubory aktualizací stále platná. |

**[Další krok: řešení potíží s aktualizací zařízení](.\troubleshoot-device-update.md)**
