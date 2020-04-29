---
title: Řešení potíží se selháním úlohy U Azure Data Lake Analytics U-SQL kvůli .NET Framework upgradu 4.7.2
description: Řešení chyb úloh U-SQL kvůli upgradu na .NET Framework 4.7.2
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: f909419810cbd837e57b19a13b2df6ae9ad2ee97
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79213580"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Azure Data Lake Analytics upgradovat na .NET Framework v 4.7.2

Azure Data Lake Analytics výchozí modul runtime provádí upgrade z .NET Framework v 4.5.2 na .NET Framework v 4.7.2. Tato změna zavádí malé riziko zásadních změn, pokud váš kód U-SQL používá vlastní sestavení a tato vlastní sestavení používají knihovny .NET.

Tento upgrade z .NET Framework 4.5.2 na verzi 4.7.2 znamená, že .NET Framework nasazený v modulu runtime U-SQL (výchozí modul runtime) se teď vždycky 4.7.2. Pro .NET Framework verze není k dispozici souběžná možnost.

Po dokončení tohoto upgradu na .NET Framework 4.7.2 bude spravovaný kód systému běžet jako verze 4.7.2. uživatelsky zadané knihovny, jako jsou vlastní sestavení U-SQL, budou spouštěny v režimu zpětné kompatibility, který je vhodný pro verzi, pro kterou bylo sestavení vygenerováno.

- Pokud jsou knihovny DLL sestavení generovány pro verzi 4.5.2, nasazené rozhraní je bude považovat za knihovny 4.5.2 a poskytuje (s několika výjimkami) sémantiku 4.5.2.
- Nyní můžete používat vlastní sestavení U-SQL, která využívají funkce 4.7.2 verze, pokud cílíte na .NET Framework 4.7.2.

Vzhledem k tomu, že tento upgrade na .NET Framework 4.7.2, je možné zavést zásadní změny úloh U-SQL, které používají vlastní sestavení .NET. V níže uvedeném postupu doporučujeme, abyste kontrolovali problémy s nekompatibilitou.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>Jak kontrolovat problémy s zpětnou kompatibilitou

Zkontrolujte potenciální problémy s přerušením zpětné kompatibility tím, že spustíte kontroly kompatibility .NET v kódu .NET ve vlastních sestaveních U-SQL.

> [!Note]
> Nástroj nedetekuje skutečné nezměněné změny. označuje jenom označované jako rozhraní .NET API, které může (pro určité vstupy) způsobovat problémy. Pokud se zobrazí upozornění na problém, váš kód může být stále v pořádku, ale měli byste se podívat na další podrobnosti.

1. Spusťte zpětnou kontrolu kompatibility vašich knihoven DLL .NET buď podle
   1. Používání rozšíření sady Visual Studio v [rozšíření .NET přenositelnost Analyzer pro Visual Studio](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)
   1. Stažení a použití samostatného nástroje z [GitHubu dotnetapiport](https://github.com/microsoft/dotnet-apiport). Pokyny pro spuštění samostatného nástroje jsou k dispozici na webu [GitHub dotnetapiport – nejnovější změny](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. Pro 4.7.2. Kompatibilita, `read isRetargeting == True` která identifikuje možné problémy.
2. Pokud nástroj indikuje, jestli váš kód může být ovlivněný jakýmkoli z možných zpětných nekompatibilit (některé běžné příklady nekompatibility jsou uvedené níže), můžete další kontrolu provést
   1. Analýza kódu a identifikace, jestli váš kód předává hodnoty ovlivněným rozhraním API
   1. Proveďte kontrolu za běhu. Nasazení modulu runtime není provedeno souběžně v ADLA. Před upgradem je možné provést kontrolu za běhu, a to pomocí místního spuštění nástroje VisualStudio s místní .NET Framework 4.7.2 na zástupce sady dat.
3. Pokud skutečně máte vliv na zpětnou kompatibilitu, proveďte potřebné kroky, abyste je opravili (například opravit vaše data nebo logiku kódu).

Ve většině případů byste neměli mít vliv na zpětnou nekompatibilitu.

## <a name="timeline"></a>Časová osa

Můžete vyhledat nasazení nového modulu runtime v části [řešení potíží s modulem runtime](runtime-troubleshoot.md)a podívat se na předchozí úspěšné úlohy.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>Co dělat, když nemůžu získat revizi kódu v čase

Můžete odeslat svou úlohu se starou verzí modulu runtime (která je sestavena cílící na 4.5.2), ale v důsledku nedostatku .NET Framework možností souběžného stránkování se pořád spustí jenom v režimu kompatibility 4.5.2. V důsledku tohoto chování můžete stále narazit na některé z těchto potíží s problémy s kompatibilitou.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>Jaké jsou nejběžnější problémy zpětné kompatibility, se kterými se můžete setkat

Nejběžnější zpětně nekompatibility, které je pravděpodobné, že je kontrola pravděpodobně identifikována (Tento seznam jsme vygenerovali spuštěním kontroly na našich vnitřních interních ADLA úloh), na které jsou ovlivněné knihovny (Všimněte si, že můžete knihovny volat jenom nepřímo, takže je důležité, abyste přijali potřebné akce #1, abyste zjistili, jestli jsou vaše úlohy ovlivněné) a jaké akce je potřeba opravit. Poznámka: ve většině případů pro naše vlastní úlohy se upozornění vypnula jako falešně pozitivní vzhledem k úzkým povahám většiny závažných změn.

- Vlastnost IAsyncResult. CompletedSynchronously musí být správná, aby se výsledný úkol dokončil.
  - Při volání TaskFactory. FromAsync musí být implementace vlastnosti IAsyncResult. CompletedSynchronously správná, aby se výsledný úkol dokončil. To znamená, že vlastnost musí vracet hodnotu true, pokud a pouze v případě, že implementace byla dokončena synchronně. Dříve nebyla tato vlastnost zaškrtnuta.
  - Ovlivněné knihovny: mscorlib, System. Threading. Tasks
  - Navrhovaná akce: Ujistěte se, že TaskFactory. FromAsync vrátí hodnotu true správně.

- DataObject. GetData teď načítá data jako UTF-8.
  - Pro aplikace cílené na .NET Framework 4 nebo spuštěné v .NET Framework 4.5.1 nebo dřívějších verzích načítá DataObject. GetData data ve formátu HTML jako řetězec ASCII. V důsledku toho jsou znaky, které nejsou ASCII (znaky, jejichž kódy ASCII jsou větší než 0x7F), reprezentovány dvěma náhodnými znaky. #N # #N # pro aplikace cílené na .NET Framework 4,5 nebo novější a spuštěné v .NET Framework `DataObject.GetData` 4.5.2 NAČTE data ve formátu HTML jako UTF-8, která představuje znaky větší než 0x7F správně.
  - Ovlivněné knihovny: GLO
  - Navrhovaná akce: Ujistěte se, že načtená data jsou ve formátu, který chcete.

- Funkce XmlWriter vyvolá neplatné náhradní páry.
  - Pro aplikace, které cílí na .NET Framework 4.5.2 nebo předchozí verze, zapisuje neplatnou náhradní dvojici pomocí zpracování záložního použití výjimek, vždy výjimku vyvolá. Pro aplikace cílené na .NET Framework 4,6 vyvolá pokus o zápis neplatného náhradního páru `ArgumentException`.
  - Ovlivněné knihovny: System. XML, System. XML. ReaderWriter
  - Navrhovaná akce: Ujistěte se, že nepíšete neplatný náhradní pár, který způsobí výjimku argumentu.

- HtmlTextWriter nevykresluje `<br/>` element správně
  - Počínaje .NET Framework 4,6, `HtmlTextWriter.RenderBeginTag()` volání a `HtmlTextWriter.RenderEndTag()` s `<BR />` elementem budou správně vložena pouze jedna `<BR />` (místo dvou).
  - Ovlivněné knihovny: System. Web
  - Navrhovaná akce: Ujistěte se, že vkládáte `<BR />` očekávané množství, které byste měli vidět, takže se v produkční úloze nezobrazuje žádné náhodné chování.

- Volání CreateDefaultAuthorizationContext s argumentem null se změnilo.
  - Implementace AuthorizationContext, která byla vrácena voláním `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` s argumentem s hodnotou null authorizationPolicies, změnila jeho implementaci v .NET Framework 4,6.
  - Ovlivněné knihovny: System. IdentityModel
  - Navrhovaná akce: Ujistěte se, že zpracováváte nové očekávané chování v případě, že existují zásady autorizace s hodnotou null.
  
- Algoritmem RSACng nyní správně načítá klíče RSA nestandardní velikosti klíče.
  - V .NET Framework verzích starších než 4.6.2 zákazníci, kteří mají nestandardní velikosti klíčů pro certifikáty RSA, nemůžou získat přístup k těmto klíčům prostřednictvím rozšiřujících metod `GetRSAPublicKey()` a `GetRSAPrivateKey()` . A `CryptographicException` se zprávou, že požadovaná velikost klíče není podporována, je vyvolána. S .NET Framework 4.6.2 Tento problém byl vyřešen. Podobně `RSA.ImportParameters()` a `RSACng.ImportParameters()` teď můžete pracovat s nestandardními velikostmi klíčů bez `CryptographicException`vyvolání.
  - Ovlivněné knihovny: mscorlib, System. Core
  - Navrhovaná akce: Zajistěte, aby klíče RSA fungovaly podle očekávání.

- Kontroly dvojtečky cest jsou přísnější
  - V .NET Framework 4.6.2 byl proveden určitý počet změn pro podporu dříve nepodporovaných cest (jak v délce, tak ve formátu). Kontroluje správnost syntaxe oddělovače jednotek (dvojtečky), která měla vedlejší účinky blokování některých cest URI v několika rozhraních API pro výběr cest, kde se používají k tolerování.
  - Ovlivněné knihovny: mscorlib, System. Runtime. Extensions
  - Navrhovaná akce:

- Volání konstruktorů hodnota ClaimsIdentity
  - Počínaje .NET Framework 4.6.2 dojde ke změně v tom, jak `T:System.Security.Claims.ClaimsIdentity` konstruktory s `T:System.Security.Principal.IIdentity` parametrem nastaví `P:System.Security.Claims.ClaimsIdentify.Actor` vlastnost. Pokud je `T:System.Security.Principal.IIdentity` argumentem `T:System.Security.Claims.ClaimsIdentity` objekt `P:System.Security.Claims.ClaimsIdentify.Actor` a vlastnost tohoto `T:System.Security.Claims.ClaimsIdentity` objektu není `null`, je `P:System.Security.Claims.ClaimsIdentify.Actor` vlastnost připojena pomocí `M:System.Security.Claims.ClaimsIdentity.Clone` metody. V rozhraní Framework 4.6.1 a starších verzích je `P:System.Security.Claims.ClaimsIdentify.Actor` vlastnost připojena jako stávající odkaz. Z důvodu `P:System.Security.Claims.ClaimsIdentify.Actor` této změny, počínaje .NET Framework 4.6.2, není vlastnost nového `T:System.Security.Claims.ClaimsIdentity` objektu shodná s `P:System.Security.Claims.ClaimsIdentify.Actor` vlastností `T:System.Security.Principal.IIdentity` argumentu konstruktoru. V .NET Framework 4.6.1 a dřívějších verzích se rovná.
  - Ovlivněné knihovny: mscorlib
  - Navrhovaná akce: Ujistěte se, že hodnota ClaimsIdentity funguje podle očekávání pro nový modul runtime.

- Serializace řídicích znaků pomocí DataContractJsonSerializer je teď kompatibilní s ECMAScript V6 a V8
  - V rozhraní .NET Framework 4.6.2 a starších verzích neserializace DataContractJsonSerializer některé speciální řídicí znaky, jako je například \b, \f a \t, způsobem, který byl kompatibilní s normami ECMAScript V6 a V8. Počínaje .NET Framework 4,7 jsou serializace těchto řídicích znaků kompatibilní s ECMAScript V6 a V8.
  - Ovlivněné knihovny: System. Runtime. Serialization. JSON
  - Navrhovaná akce: zajištění stejného chování s DataContractJsonSerializer
