---
title: Řešení potíží s chybami úloh Azure Data Lake Analytics U-SQL z důvodu upgradu rozhraní .NET Framework 4.7.2
description: Poradce při potížích s chybami úlohy U-SQL z důvodu upgradu na rozhraní .NET Framework 4.7.2.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: f909419810cbd837e57b19a13b2df6ae9ad2ee97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213580"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure Data Lake Analytics upgraduje na rozhraní .NET Framework v4.7.2

Výchozí runtime Azure Data Lake Analytics upgraduje z rozhraní .NET Framework v4.5.2 na rozhraní .NET Framework v4.7.2. Tato změna zavádí malé riziko porušení změn, pokud váš kód U-SQL používá vlastní sestavení a tato vlastní sestavení používají knihovny .NET.

Tento upgrade z rozhraní .NET Framework 4.5.2 na verzi 4.7.2 znamená, že rozhraní .NET Framework nasazené v modulu runtime U-SQL (výchozí modul runtime) bude nyní vždy 4.7.2. Pro verze rozhraní .NET Framework neexistuje možnost souběžně.

Po dokončení tohoto upgradu na rozhraní .NET Framework 4.7.2 bude spravovaný kód systému spuštěn jako verze 4.7.2, uživatelsky poskytované knihovny, jako jsou vlastní sestavení U-SQL, budou spuštěny v režimu zpětnékompatibility vhodném pro verzi, ve které bylo sestavení generovaných pro.

- Pokud vaše sestavení Knihovny DLL jsou generovány pro verzi 4.5.2, nasazené framework bude zacházet s nimi jako 4.5.2 knihovny, poskytuje (s několika výjimkami) 4.5.2 sémantiku.
- Nyní můžete použít u-SQL vlastní sestavení, které využívají funkce verze 4.7.2, pokud cílíte na rozhraní .NET Framework 4.7.2.

Z důvodu tohoto upgradu na rozhraní .NET Framework 4.7.2 existuje potenciál zavést narušující změny úloh u-SQL, které používají vlastní sestavení rozhraní .NET. Doporučujeme zkontrolovat problémy s zpětnou kompatibilitou pomocí níže uvedeného postupu.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>Jak zkontrolovat problémy s zpětnou kompatibilitou

Zkontrolujte potenciál problémů s narušující zpětnou kompatibilitou spuštěním kontrol kompatibility rozhraní .NET u kódu .NET ve vlastních sestaveních U-SQL.

> [!Note]
> Nástroj nedetekuje skutečné změny. identifikuje pouze volané rozhraní API .NET, které mohou (pro určité vstupy) způsobit problémy. Pokud budete upozorněni na problém, váš kód může být stále v pořádku, ale měli byste zkontrolovat další podrobnosti.

1. Spusťte kontrolu zpětné kompatibility na knihovnách .NET DLL buď
   1. Použití rozšíření Visual Studio na [rozhraní .NET Portability Analyzer Visual Studio Extension](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)
   1. Stahování a používání samostatného nástroje z [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport). Pokyny pro spuštění samostatného nástroje jsou na [GitHub dotnetapiport lámání změny](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. Pro 4.7.2. kompatibilitu, `read isRetargeting == True` identifikuje možné problémy.
2. Pokud nástroj označuje, zda váš kód může být ovlivněn některou z možných nekompatibility dozadu (některé běžné příklady nekompatibility jsou uvedeny níže), můžete dále zkontrolovat
   1. Analýza kódu a určení, zda váš kód předává hodnoty ovlivněným apimům
   1. Proveďte kontrolu za běhu. Nasazení za běhu se v adla neprovádí souběžně. Můžete provést kontrolu za běhu před upgradem pomocí visualstudio místní spuštění s místní rozhraní .NET Framework 4.7.2 proti reprezentativní datové sady.
3. Pokud jste skutečně ovlivněny zpětnou nekompatibilitou, podnikněte nezbytné kroky k jeho opravě (například oprava dat nebo logiky kódu).

Ve většině případů byste neměli být ovlivněny zpětnou nekompatibilitou.

## <a name="timeline"></a>Časová osa

Můžete zkontrolovat nasazení nového runtime zde [Runtime řešení potíží](runtime-troubleshoot.md)a při pohledu na všechny předchozí úspěšné úlohy.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>Co když se mi nepodaří zkontrolovat kód včas

Můžete odeslat úlohu proti staré runtime verzi (která je sestavena cílení 4.5.2), ale z důvodu nedostatku .NET Framework vedle sebe možnosti, bude stále spuštěna pouze v režimu kompatibility 4.5.2. Stále může dojít k některé problémy s kompatibilitou zpět z důvodu tohoto chování.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>Jaké jsou nejčastější problémy se zpětnou kompatibilitou, se kterými se můžete setkat

Nejběžnější zpětné nekompatibility, které kontrola pravděpodobně identifikuje, jsou (tento seznam jsme vytvořili spuštěním kontroly na našich vlastních interních úlohách ADLA), které jsou ovlivněny knihovnami (poznámka: že knihovny můžete volat pouze nepřímo, proto je důležité provést požadovanou akci #1 ke kontrole, zda jsou vaše úlohy ovlivněny) a možné kroky k nápravě. Poznámka: Téměř ve všech případech pro naše vlastní práce se varování ukázala jako falešně pozitivní kvůli úzké povaze většiny průlomových změn.

- Vlastnost IAsyncResult.CompletedSynchronously musí být správná, aby výsledná úloha byla dokončena.
  - Při volání TaskFactory.FromAsync implementace Vlastnost IAsyncResult.CompletedSynchronously musí být správné pro výslednou úlohu dokončit. To znamená, že vlastnost musí vrátit true if a pouze v případě, že implementace byla dokončena synchronně. Dříve nebyla tato vlastnost zaškrtnuta.
  - Ovlivněné knihovny: mscorlib, System.Threading.Tasks
  - Navrhovaná akce: Ujistěte se, že TaskFactory.FromAsync vrátí true správně

- DataObject.GetData nyní načítá data jako UTF-8
  - Pro aplikace, které cílí na rozhraní .NET Framework 4 nebo které běží v rozhraní .NET Framework 4.5.1 nebo starších verzích, data DataObject.GetData načte data ve formátu HTML jako řetězec ASCII. V důsledku toho jsou znaky bez ASCII (znaky, jejichž kódy ASCII jsou větší než 0x7F) reprezentovány dvěma náhodnými znaky.#N##N#Pro aplikace, které `DataObject.GetData` cílí na rozhraní .NET Framework 4.5 nebo novější a běží na rozhraní .NET Framework 4.5.2, načtou data ve formátu HTML jako UTF-8, což představuje znaky větší než 0x7F správně.
  - Ovlivněné knihovny: Glo
  - Navrhovaná akce: Ujistěte se, že načtená data jsou požadovaný formát

- XmlWriter vyvolá neplatné náhradní páry
  - Pro aplikace, které cílí na rozhraní .NET Framework 4.5.2 nebo předchozí verze, zápis neplatné náhradní pár pomocí výjimky záložní zpracování není vždy vyvolat výjimku. Pro aplikace, které cílí na rozhraní .NET Framework 4.6, `ArgumentException`pokus o zápis neplatné náhradní pár vyvolá .
  - Ovlivněné knihovny: System.Xml, System.Xml.ReaderWriter
  - Navrhovaná akce: Ujistěte se, že nepíšete neplatný náhradní pár, který způsobí výjimku argumentu

- HtmlTextWriter nevykresluje `<br/>` prvek správně
  - Počínaje rozhraním .NET Framework 4.6, `HtmlTextWriter.RenderBeginTag()` volání a `HtmlTextWriter.RenderEndTag()` s elementem `<BR />` správně vloží pouze jeden `<BR />` (namísto dvou)
  - Ovlivněné knihovny: System.Web
  - Navrhovaná akce: Ujistěte se, `<BR />` že vkládáte tolik, které očekáváte, že uvidíte, takže v produkční úloze se nezobrazí žádné náhodné chování.

- Volání CreateDefaultAuthorizationContext s nulovým argumentem bylo změněno.
  - Implementace AuthorizationContext vrácené voláním argumentu `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` s null authorizationPolicies změnila jeho implementaci v rozhraní .NET Framework 4.6.
  - Ovlivněné knihovny: System.IdentityModel
  - Navrhovaná akce: Ujistěte se, že zpracováváte nové očekávané chování, pokud existuje zásada nulové autorizace
  
- RSACng nyní správně načte rsa klíče nestandardní velikosti klíče
  - Ve verzích rozhraní .NET Framework před verzí 4.6.2 nemají zákazníci s nestandardními velikostmi `GetRSAPublicKey()` `GetRSAPrivateKey()` klíčů pro certifikáty RSA přístup k těmto klíčům prostřednictvím metod a extension. A `CryptographicException` se zprávou "Požadovaná velikost klíče není podporována" je vyvolána. S rozhraním .NET Framework 4.6.2 byl tento problém vyřešen. Podobně, `RSA.ImportParameters()` a `RSACng.ImportParameters()` nyní pracovat s nestandardní velikosti `CryptographicException`klíčů bez házení 's.
  - Ovlivněné knihovny: mscorlib, System.Core
  - Navrhovaná akce: Ujistěte se, že klíče RSA fungují podle očekávání

- Kontroly dvojtečky cesty jsou přísnější
  - V rozhraní .NET Framework 4.6.2 byla provedena řada změn pro podporu dříve nepodporovaných cest (v délce i formátu). Kontroly správné syntaxe oddělovače jednotek (dvojtečka) byly provedeny více správné, což mělo vedlejší účinek blokování některých cest URI v několika vybraných rozhraní chodišti rozhraní API, kde byly tolerovány.
  - Ovlivněné knihovny: mscorlib, System.Runtime.Extensions
  - Navrhovaná akce:

- Volání konstruktorů ClaimsIdentity
  - Počínaje rozhraním .NET Framework 4.6.2 dochází `T:System.Security.Claims.ClaimsIdentity` ke změně způsobu, jakým konstruktory s parametrem `T:System.Security.Principal.IIdentity` nastavují `P:System.Security.Claims.ClaimsIdentify.Actor` vlastnost. Pokud `T:System.Security.Principal.IIdentity` je argument `T:System.Security.Claims.ClaimsIdentity` emitovaný `P:System.Security.Claims.ClaimsIdentify.Actor` objektem `T:System.Security.Claims.ClaimsIdentity` a `null`vlastnost `P:System.Security.Claims.ClaimsIdentify.Actor` tohoto objektu není `M:System.Security.Claims.ClaimsIdentity.Clone` , je vlastnost připojena pomocí metody. V rámci 4.6.1 a starší `P:System.Security.Claims.ClaimsIdentify.Actor` verze vlastnost je připojen jako existující odkaz. Z důvodu této změny počínaje rozhraním .NET Framework `P:System.Security.Claims.ClaimsIdentify.Actor` 4.6.2 se vlastnost `T:System.Security.Claims.ClaimsIdentity` `P:System.Security.Claims.ClaimsIdentify.Actor` nového objektu nerovná vlastnosti argumentu konstruktoru. `T:System.Security.Principal.IIdentity` V rozhraní .NET Framework 4.6.1 a starších verzích je stejná.
  - Ovlivněné knihovny: mscorlib
  - Navrhovaná akce: Ujistěte se, že identita claimsidentity funguje podle očekávání v novém běhu

- Serializace řídicích znaků pomocí datacontractjsonserializer je nyní kompatibilní s ECMAScript V6 a V8
  - V rozhraní .NET framework 4.6.2 a starších verzích datacontractjsonserializer neserializoval některé speciální řídicí znaky, například \b, \f a \t, způsobem, který byl kompatibilní se standardy ECMAScript V6 a V8. Počínaje rozhraním .NET Framework 4.7 je serializace těchto řídicích znaků kompatibilní s ecmascriptovými v6 a v8.
  - Ovlivněné knihovny: System.Runtime.Serialization.Json
  - Navrhovaná akce: Zajistěte stejné chování pomocí dataContractJsonSerializer
