---
title: Zabezpečení aktualizace zařízení pro Azure IoT Hub | Microsoft Docs
description: Pochopte, jak IoT Hub aktualizace zařízení zajišťuje zabezpečené aktualizace zařízení.
author: lichris
ms.author: lichris
ms.date: 2/11/2021
ms.topic: conceptual
ms.service: iot-hub
ms.openlocfilehash: 86b2dbe6a28d1440f93788eb40e133d9b62d3f0c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102489425"
---
# <a name="device-update-security-model"></a>Model zabezpečení aktualizace zařízení

Aktualizace zařízení pro IoT Hub nabízí zabezpečenou metodu nasazení aktualizací pro firmware zařízení, obrázky a aplikace do zařízení IoT. Pracovní postup poskytuje kompletní zabezpečený kanál s úplným modelem, který může zařízení použít k prokázání, že je aktualizace důvěryhodná, nezměněná a úmyslné.

Každý krok v pracovním postupu aktualizace zařízení je chráněn různými funkcemi zabezpečení a procesy, aby se zajistilo, že každý krok v kanálu provede zabezpečený předání do dalšího. Klient aktualizace zařízení identifikuje a správně spravuje všechny žádosti o aktualizaci illegitimate. Klient také kontroluje všechny soubory ke stažení, aby bylo zajištěno, že obsah je důvěryhodný, nezměněný a je úmyslné.

## <a name="for-solution-operators"></a>Pro operátory řešení

Protože operátory řešení importují aktualizace do své instance aktualizace zařízení, služba odesílá a kontroluje binární soubory aktualizace, aby bylo zajištěno, že nebyly změněny ani nahrazeny uživatelem se zlými úmysly. Po ověření služba aktualizace zařízení generuje [manifest interní aktualizace](./update-manifest.md) s hodnotami hash souborů z manifestu importu a dalších metadat. Tento manifest aktualizace je pak podepsaný službou aktualizace zařízení.

Když operátor řešení požaduje aktualizaci zařízení, pošle se na zařízení podepsaná zpráva s podpisem IoT Hub. Signatura žádosti je ověřena agentem aktualizace zařízení zařízení jako platná. 

Výsledný binární soubor ke stažení je zabezpečený pomocí ověřování signatury manifestu aktualizace. Manifest aktualizace obsahuje hodnoty hash binárního souboru, takže jakmile je manifest důvěryhodný, agent aktualizace zařízení důvěřuje hodnoty hash a porovná je s binárními soubory. Po stažení a ověření binárního souboru aktualizace je tato služba předána instalačnímu programu na zařízení.

## <a name="for-device-builders"></a>Pro sestavovatele zařízení

Aby služba aktualizace zařízení mohla škálovat dolů na jednoduchá zařízení s nízkým výkonem, používá model zabezpečení nezpracované asymetrické klíče a nezpracované podpisy. Používají formáty založené na formátu JSON, jako jsou webové tokeny JSON & webové klíče JSON.

### <a name="securing-update-content-via-the-update-manifest"></a>Zabezpečení obsahu aktualizace pomocí manifestu aktualizace

Manifest aktualizace se ověřuje pomocí dvou signatur. Signatury se vytvářejí pomocí struktury sestávající z *podpisových* klíčů a *kořenových* klíčů.

Agent aktualizace zařízení má vložené veřejné klíče, které se používají pro všechna zařízení kompatibilní s aktualizací zařízení. Jedná se o *kořenové* klíče. Odpovídající privátní klíče jsou řízeny společností Microsoft.

Společnost Microsoft také generuje pár veřejného a privátního klíče, který není zahrnutý v agentovi aktualizace zařízení nebo uložený na zařízení. Toto je *podpisový* klíč.

Když se aktualizace importuje do aktualizace zařízení pro IoT Hub a manifest aktualizace je vygenerovaný službou, podepíše manifest pomocí podpisového klíče a zahrne podpisový klíč, který je podepsaný kořenovým klíčem. Agent aktualizace zařízení obdrží při odeslání manifestu aktualizace do zařízení následující data podpisu:

1. Hodnota podpisu sama.
2. Algoritmus používaný pro generování #1.
3. Informace o veřejném klíči pro podpisový klíč používaný k vygenerování #1.
4. Podpis veřejného podpisového klíče v #3.
5. ID veřejného klíče pro kořenový klíč, který se používá pro generování #3.
6. Algoritmus používaný pro generování #4.

Agent aktualizace zařízení používá výše uvedené informace k ověření, že podpis veřejného podpisového klíče je podepsaný kořenovým klíčem. Agent aktualizace zařízení potom ověří, zda podpis manifestu aktualizace je podepsán podpisovým klíčem. Pokud jsou všechny signatury správné, je agentem aktualizace zařízení důvěryhodný manifest aktualizace. Vzhledem k tomu, že manifest aktualizace obsahuje hodnoty hash souborů, které odpovídají samotným aktualizacím souborů, mohou být soubory aktualizace také důvěryhodné, pokud se shodují hodnoty hash.

Kořenový a podpisový klíč umožňuje Microsoftu pravidelně převádět podpisový klíč, což je nejlepší postup zabezpečení.

### <a name="json-web-signature-jws"></a>Webový podpis JSON (JWS)

`updateManifestSignature`Slouží k zajištění, že informace obsažené v rámci nebyly `updateManifest` úmyslně poškozeny. `updateManifestSignature`Je vytvořen pomocí webové signatury JSON s webovými klíči JSON, což umožňuje ověření zdroje. Signatura je Base64Url kódovaný řetězec se třemi oddíly, které jsou odděleny znakem ".".  Informace o analýze a ověřování klíčů a tokenů JSON najdete v [pomocných metodách jws_util. h](https://github.com/Azure/iot-hub-device-update/tree/main/src/utils/jws_utils) .

Webový podpis JSON je široce využívaný [Navrhovaný standard IETF](https://tools.ietf.org/html/rfc7515) pro podepisování obsahu pomocí datových struktur založených na formátu JSON. Je to způsob, jak zajistit integritu dat ověřením podpisu dat. Další informace najdete v [dokumentu RFC 7515](https://www.rfc-editor.org/info/rfc7515)pro webový podpis JSON (JWS).

### <a name="json-web-token"></a>JSON Web Token

Webové tokeny JSON představují otevřenou [standardní](https://tools.ietf.org/html/rfc7519) metodu pro reprezentaci deklarací mezi dvěma stranami.

### <a name="root-keys"></a>Kořenové klíče

Každé zařízení aktualizace zařízení obsahuje sadu kořenových klíčů. Tyto klíče jsou kořenem důvěryhodnosti všech podpisů aktualizace zařízení. Jakýkoli podpis musí být zřetězený přes jeden z těchto kořenových klíčů, aby se mohl považovat za legitimní.

Sada kořenových klíčů se v průběhu času změní tak, aby bylo možné pravidelně střídat podpisové klíče z bezpečnostních důvodů. V důsledku toho bude software agenta aktualizace zařízení muset aktualizovat sám sebe s nejnovější sadou kořenových klíčů. 

### <a name="signatures"></a>Podpisy

Všechny signatury se budou považovat za podpisový (veřejný) klíč podepsaný jedním z kořenových klíčů. Signatura určí, který kořenový klíč se použil k podepsání podpisového klíče. 

Agent aktualizace zařízení musí ověřit signatury tím, že nejprve ověří, jestli je podpis podpisového (veřejného) klíče správný, platný a podepsaný jedním ze schválených kořenových klíčů. Po úspěšném ověření podpisového klíče se může podpis sám ověřit pomocí veřejného klíče důvěryhodného podepsání.

Podpisové klíče se otáčí na mnohem rychlejším tempo než kořenové klíče, takže očekává zprávy podepsané různými podpisovým klíčem. 

Odvolání podpisového klíče je spravováno službou aktualizace zařízení, takže by se uživatelé neměli pokoušet o ukládání podpisových klíčů do mezipaměti. Vždy používejte podpisový klíč doprovázející podpis.

### <a name="receiving-updates"></a>Přijímání aktualizací

Žádosti o aktualizaci přijaté agentem aktualizace zařízení budou obsahovat dokument podepsaný manifest aktualizace (UM). Agent musí ověřit, jestli je podpis UM správný a nedotčený. K tomu je potřeba ověřit, jestli byl podpisový klíč pro UM podpis podepsán správným kořenovým klíčem. Po dokončení agent ověří u podpisového klíče UM.

Jakmile se podpis UM ověří, agent aktualizace zařízení ho může důvěřovat jako "zdroj pravdy". Všechny další z tohoto zdroje vyplývají z dalších bezpečnostních vztahů. 

UM obsahuje adresy URL a soubory hash obsahu ke stažení a instalaci. Jakmile agent stáhne binární aktualizaci, musí aktualizaci ověřit proti hodnotě hash souboru nalezené v doméně UM. To poskytuje model přenositelného vztahu důvěryhodnosti pro ověření stahování. Nezaručuje pouze to, že obsah je neporušený (nezměněný), ale také potvrdí, co bylo staženo, co bylo určeno ke stažení. 

### <a name="securing-the-device"></a>Zabezpečení zařízení

Je důležité zajistit, aby prostředky zabezpečení související s aktualizací zařízení byly v zařízení správně zabezpečené a chráněné. Prostředky, jako jsou kořenové klíče, je třeba chránit proti úpravám. Existují různé způsoby, jak to provést, jako je například použití zabezpečení zařízení (TPM, SGX, HSM, jiná zařízení zabezpečení), nebo dokonce jejich zakódování v agentovi aktualizace zařízení. Druhý postup vyžaduje, aby byl kód agenta aktualizace zařízení digitálně podepsaný a podpora integrity kódu systému je povolená k ochraně před škodlivou úpravou kódu agenta.

Další bezpečnostní opatření můžou být odůvodněná, třeba zajistit zabezpečený způsob přeložení ze součásti na součást. Například registrace konkrétního izolovaného účtu pro spuštění různých součástí. A omezují síťovou komunikaci (například REST API volání) pouze na localhost.

**[Další krok: Přečtěte si další informace o tom, jak aktualizace zařízení používá Azure RBAC.](.\device-update-control-access.md)**