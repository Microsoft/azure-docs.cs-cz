---
title: Výrazy zásad správy rozhraní Azure API | Dokumenty společnosti Microsoft
description: Další informace o výrazech zásad ve správě rozhraní Azure API.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: 6614e70d130abe46067c657bda3ccdd7000caddc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244001"
---
# <a name="api-management-policy-expressions"></a>Výrazy zásad správy rozhraní API
Tento článek popisuje syntaxi výrazů zásad v c# 7. Každý výraz má přístup k implicitně poskytnuté [kontextové](api-management-policy-expressions.md#ContextVariables) proměnné a povolené [podmnožině](api-management-policy-expressions.md#CLRTypes) typů rozhraní .NET Framework.

Další informace najdete tady:

- Podívejte se, jak zadat kontextové informace pro back-endovou službu. K poskytnutí těchto informací použijte [parametr Nastavit řetězec dotazu](api-management-transformation-policies.md#SetQueryStringParameter) a Nastavit zásady [záhlaví PROTOKOLU HTTP.](api-management-transformation-policies.md#SetHTTPheader)
- Podívejte se, jak pomocí [zásady Ověření JWT](api-management-access-restriction-policies.md#ValidateJWT) předem autorizovat přístup k operacím na základě deklarací tokenů.
- Podívejte se, jak pomocí trasování [v programu API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) zjistit, jak jsou vyhodnocovány zásady a výsledky těchto hodnocení.
- Podívejte se, jak používat výrazy s [zásadami Get from cache](api-management-caching-policies.md#GetFromCache) a Store to [cache](api-management-caching-policies.md#StoreToCache) ke konfiguraci ukládání odpovědí správy rozhraní API do mezipaměti. Nastavte dobu, která odpovídá ukládání odpovědi do mezipaměti back-endové služby, jak je určeno direktivou zálohované `Cache-Control` služby.
- Přečtěte si, jak provádět filtrování obsahu. Odeberte datové prvky z odpovědi přijaté z back-endu pomocí zásad [Tok řízení](api-management-advanced-policies.md#choose) a [Nastavit tělo.](api-management-transformation-policies.md#SetBody)
- Pokud si stáhnete prohlášení zásad, podívejte se na úložiště GitHub, [které se unítá v ukázkách/zásadách správy rozhraní api.](https://github.com/Azure/api-management-samples/tree/master/policies)


## <a name="syntax"></a><a name="Syntax"></a>Syntaxe
Výrazy jednoho příkazu `@(expression)`jsou `expression` uzavřeny v aplikaci , kde je dobře tvarovaný příkaz výrazu Jazyka C#.

Vícepříkazové výrazy jsou `@{expression}`uzavřeny v aplikaci . Všechny cesty kódu v rámci vícevýrazových `return` výrazů musí končit příkazem.

## <a name="examples"></a><a name="PolicyExpressionsExamples"></a>Příklady

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string[] value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
      if(value != null && value.Length > 0)
      {
          return Encoding.UTF8.GetString(Convert.FromBase64String(value[0]));
      }
  }
  return null;

}
```

## <a name="usage"></a><a name="PolicyExpressionsUsage"></a>Použití
Výrazy lze použít jako hodnoty atributů nebo textové hodnoty v libovolné [masce](api-management-policies.md) správy rozhraní API (pokud odkaz na zásady neurčuje jinak).

> [!IMPORTANT]
> Při použití výrazů zásad je pouze omezené ověření výrazů zásad, pokud je definována zásada. Výrazy jsou spouštěny bránou za běhu, všechny výjimky generované výrazy zásad mají za následek chybu za běhu.

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a>Typy rozhraní .NET Jsou povoleny ve výrazech zásad
V následující tabulce jsou uvedeny typy rozhraní .NET Framework a jejich členy, které jsou povoleny ve výrazech zásad.

|Typ|Podporovaní členové|
|--------------|-----------------------|
|Newtonsoft.Json.Formátování|Všechny|
|Newtonsoft.Json.JsonConvert|SerializeObject, DeserializeObject|
|Newtonsoft.Json.Linq.Rozšíření|Všechny|
|Newtonsoft.Json.Linq.JArray|Všechny|
|Newtonsoft.Json.Linq.JKonstruktor|Všechny|
|Newtonsoft.Json.Linq.JKontejner|Všechny|
|Newtonsoft.Json.Linq.JObjekt|Všechny|
|Newtonsoft.Json.Linq.JNemovitosta|Všechny|
|Newtonsoft.Json.Linq.JRaw|Všechny|
|Newtonsoft.Json.Linq.JToken|Všechny|
|Newtonsoft.Json.Linq.JTokenTyp|Všechny|
|Newtonsoft.Json.Linq.JHodnota|Všechny|
|System.array|Všechny|
|Systém.BitConverter|Všechny|
|Systém.Logická hodnota|Všechny|
|Systém.Bajt|Všechny|
|System.Char|Všechny|
|System.Collections.Generic.Dictionary<TKey, TValue>|Všechny|
|System.Collections.Generic.HashSet\<T>|Všechny|
|System.Collections.Generic.ICollection\<T>|Všechny|
|System.Collections.Generic.IDictionary<TKey, TValue>|Všechny|
|System.Collections.Generic.IEnumerable\<T>|Všechny|
|System.Collections.Generic.IEnumerator\<T>|Všechny|
|System.Collections.Generic.IList\<T>|Všechny|
|System.Collections.Generic.IReadOnlyCollection\<T>|Všechny|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|Všechny|
|System.Collections.Generic.ISet\<T>|Všechny|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Všechny|
|System.Collections.Generic.List\<T>|Všechny|
|System.Collections.Generic.Front\<a>|Všechny|
|System.Collections.Generic.Stack\<T>|Všechny|
|Systém.Převést|Všechny|
|System.datetime|(Konstruktor), Přidat, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, Datum, Den, DayOfWeek, DayOfYear, DaysInMonth, Hour, IsDaylightSavingTime, IsLeapYear, MaxValue, Millisecond, Minute, MinValue, Month, Now , Analyzovat, Druhý, Odečíst, Značky, TimeOfDay, Dnes, ToString, UtcNow, Rok|
|System.DateTimeKind|Utc|
|System.DateTimeOffset|Všechny|
|Systém.Desetinné místo|Všechny|
|Systém.Double|Všechny|
|System.exception|Všechny|
|System.Guid|Všechny|
|Systém.Int16|Všechny|
|Systém.Int32|Všechny|
|Systém.Int64|Všechny|
|Program System.iO.StringReader|Všechny|
|System.io.StringWriter|Všechny|
|System.Linq.Enumerable|Všechny|
|System.Math|Všechny|
|System.MidpointRounding|Všechny|
|Nástroj System.Net.Web|Všechny|
|Systém.Nullable|Všechny|
|System.Random|Všechny|
|System.SByte|Všechny|
|System.Security.Cryptography.AsymmetricAlgoritmus|Všechny|
|System.Security.Cryptography.CipherMode|Všechny|
|System.Security.Cryptography.HashAlgorithm|Všechny|
|System.Security.Cryptography.HashAlgorithmName|Všechny|
|System.Security.Cryptography.HMAC|Všechny|
|System.Security.Cryptography.HMACMD5|Všechny|
|System.Security.Cryptography.HMACSHA1|Všechny|
|System.Security.Cryptography.HMACSHA256|Všechny|
|System.Security.Cryptography.HMACSHA384|Všechny|
|System.Security.Cryptography.HMACSHA512|Všechny|
|System.Security.Cryptography.KeyedHashAlgorithm|Všechny|
|System.Security.Cryptography.MD5|Všechny|
|System.Security.Cryptography.Oid|Všechny|
|System.Security.Cryptography.PaddingMode|Všechny|
|System.Security.Cryptography.RNGCryptoServiceProvider|Všechny|
|System.Security.Cryptography.RSA|Všechny|
|System.Security.Cryptography.RSAEncryptionPadding|Všechny|
|System.Security.Cryptography.RSAPodpisPadding|Všechny|
|System.Security.Cryptography.SHA1|Všechny|
|Systém.Security.Cryptography.SHA1Managed|Všechny|
|System.Security.Kryptografie.SHA256|Všechny|
|Systém.Security.Cryptography.SHA256Spravováno|Všechny|
|System.Security.Kryptografie.SHA384|Všechny|
|Systém.Security.Cryptography.SHA384Spravováno|Všechny|
|System.Security.Kryptografie.SHA512|Všechny|
|Systém.Security.Kryptografie.SHA512Spravováno|Všechny|
|System.Security.Cryptography.SymmetricAlgoritmus|Všechny|
|System.Security.Cryptography.X509Certificates.PublicKey|Všechny|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Všechny|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|Name (Název)|
|System.Security.Cryptography.X509Certificates.X509Certificate|Všechny|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Všechny|
|System.Security.Cryptography.X509Certificates.X509ContentType|Všechny|
|System.Security.Cryptography.X509Certificates.X509NameType|Všechny|
|System.Single|Všechny|
|System.string|Všechny|
|System.StringComparer|Všechny|
|System.StringComparison|Všechny|
|System.StringSplitOptions|Všechny|
|System.Text.Kódování|Všechny|
|System.Text.RegularExpressions.Capture|Index, délka, hodnota|
|System.Text.RegularExpressions.CaptureCollection|Počet, položka|
|System.Text.RegularExpressions.Group|Zachycuje, úspěch|
|System.Text.RegularExpressions.GroupCollection|Počet, položka|
|System.Text.RegularExpressions.Match|Prázdný, Skupiny, Výsledek|
|System.Text.RegularExpressions.Regex|(konstruktor), IsMatch, Shoda, Zápasy, Nahradit, Unescape, Split|
|System.Text.RegularExpressions.RegexOptions|Všechny|
|System.text.stringbuilder|Všechny|
|System.TimeSpan|Všechny|
|Systém.Časové pásmo|Všechny|
|System.TimeZoneInfo.AdjustmentRule|Všechny|
|System.TimeZoneInfo.TransitionTime|Všechny|
|System.TimeZoneInfo|Všechny|
|System.Tuple|Všechny|
|Systém.UInt16|Všechny|
|Systém.UInt32|Všechny|
|Systém.UInt64|Všechny|
|System.uri|Všechny|
|System.UriPartial|Všechny|
|Rozšíření System.Xml.Linq.Extensions|Všechny|
|Atribut System.Xml.Linq.XAttribute|Všechny|
|Data System.Xml.Linq.XCData|Všechny|
|Komentář System.Xml.Linq.X|Všechny|
|Kontejner System.Xml.Linq.X|Všechny|
|Deklarace System.Xml.Linq.XDeclaration|Všechny|
|Dokument System.Xml.Linq.XDocument|Vše kromě: Zatížení|
|System.Xml.Linq.XDocumentType|Všechny|
|System.Xml.Linq.XElement|Všechny|
|Název System.Xml.Linq.XName|Všechny|
|Obor YSystem.Xml.Linq.XNamespace|Všechny|
|Program System.Xml.Linq.XNode|Všechny|
|System.Xml.Linq.XNodeDocumentOrderComparer|Všechny|
|System.Xml.Linq.XNodeEqualityComparer|Všechny|
|Objekt System.Xml.Linq.XObject|Všechny|
|System.Xml.Linq.XProcessingInstruction|Všechny|
|System.Xml.Linq.XText|Všechny|
|System.Xml.XmlNodeType|Všechny|

## <a name="context-variable"></a><a name="ContextVariables"></a>Kontextová proměnná
Proměnná `context` pojmenovaná je implicitně k dispozici v každém [výrazu](api-management-policy-expressions.md#Syntax)zásady . Její členové poskytují informace týkající `\request`se . Všichni `context` členové jsou jen pro čtení.

|Kontextová proměnná|Povolené metody, vlastnosti a hodnoty parametrů|
|----------------------|-------------------------------------------------------|
|kontext|[Api](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Nasazení](#ref-context-deployment)<br /><br /> Uplynulý: TimeSpan - časový interval mezi hodnotou časového razítka a aktuálním časem<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Operace](#ref-context-operation)<br /><br /> [Produktu](#ref-context-product)<br /><br /> [Požadavek](#ref-context-request)<br /><br /> RequestId: Guid - jedinečný identifikátor požadavku<br /><br /> [Reakce](#ref-context-response)<br /><br /> [Předplatné](#ref-context-subscription)<br /><br /> Časové razítko: DateTime - bod v čase, kdy byl požadavek přijat<br /><br /> Trasování: bool - označuje, zda je trasování zapnuto nebo vypnuto <br /><br /> [Uživatel](#ref-context-user)<br /><br /> [Proměnné](#ref-context-variables): IReadOnlyDictionary<řetězec, objekt><br /><br /> void Trace (zpráva: řetězec)|
|<a id="ref-context-api"></a>Kontextu. Rozhraní api|Id: řetězec<br /><br /> IsCurrentRevision: bool<br /><br />  Název: řetězec<br /><br /> Cesta: řetězec<br /><br /> Revize: řetězec<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Verze: řetězec |
|<a id="ref-context-deployment"></a>Kontextu. Nasazení|Region: řetězec<br /><br /> ServiceName: řetězec<br /><br /> Certifikáty: IReadOnlyDictionary<řetězec, X509Certificate2>|
|<a id="ref-context-lasterror"></a>Kontextu. Chyba poslední|Zdroj: řetězec<br /><br /> Důvod: řetězec<br /><br /> Zpráva: řetězec<br /><br /> Obor: řetězec<br /><br /> Sekce: řetězec<br /><br /> Cesta: řetězec<br /><br /> PolicyId: řetězec<br /><br /> Další informace o kontextu. LastError, viz [Zpracování chyb](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>Kontextu. Operace|Id: řetězec<br /><br /> Metoda: řetězec<br /><br /> Název: řetězec<br /><br /> UrlTemplate: řetězec|
|<a id="ref-context-product"></a>Kontextu. Produktu|Apis: IEnumerable<[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Skupiny: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: řetězec<br /><br /> Název: řetězec<br /><br /> Stav: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>Kontextu. Požadavek|Tělo: [IMessageBody](#ref-imessagebody) nebo `null` pokud žádost nemá tělo.<br /><br /> Certifikát: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Záhlaví](#ref-context-request-headers): IReadOnlyDictionary<řetězec, řetězec[]><br /><br /> IpAddress: řetězec<br /><br /> MatchedParameters: IReadOnlyDictionary<řetězec, řetězec><br /><br /> Metoda: řetězec<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> Url: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>kontextu řetězce. Request.Headers.GetValueOrDefault(headerName: řetězec, defaultValue: řetězec)|headerName: řetězec<br /><br /> defaultValue: řetězec<br /><br /> Vrátí hodnoty hlavičky požadavku oddělené `defaultValue` čárkami nebo pokud záhlaví není nalezeno.|
|<a id="ref-context-response"></a>Kontextu. Reakce|Tělo: [IMessageBody](#ref-imessagebody)<br /><br /> [Záhlaví](#ref-context-response-headers): IReadOnlyDictionary<řetězec, řetězec[]><br /><br /> StatusCode: int<br /><br /> StatusReason: řetězec|
|<a id="ref-context-response-headers"></a>kontextu řetězce. Response.Headers.GetValueOrDefault(headerName: řetězec, defaultValue: řetězec)|headerName: řetězec<br /><br /> defaultValue: řetězec<br /><br /> Vrátí hodnoty záhlaví odpovědi oddělené `defaultValue` čárkami nebo pokud záhlaví není nalezeno.|
|<a id="ref-context-subscription"></a>Kontextu. Předplatné|CreatedTime: DateTime<br /><br /> Datum ukončení: DateTime?<br /><br /> Id: řetězec<br /><br /> Klíč: řetězec<br /><br /> Název: řetězec<br /><br /> PrimaryKey: řetězec<br /><br /> SecondaryKey: řetězec<br /><br /> Datum spuštění: DateTime?|
|<a id="ref-context-user"></a>Kontextu. Uživatele|E-mail: řetězec<br /><br /> Jméno: řetězec<br /><br /> Skupiny: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> Id: řetězec<br /><br /> Identity: IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> Příjmení: řetězec<br /><br /> Poznámka: řetězec<br /><br /> Datum registrace: DateTime|
|<a id="ref-iapi"></a>IApi|Id: řetězec<br /><br /> Název: řetězec<br /><br /> Cesta: řetězec<br /><br /> Protokoly: IEnumerable<řetězec\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [iSubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>ISkupina|Id: řetězec<br /><br /> Název: řetězec|
|<a id="ref-imessagebody"></a>IMessageBody|Jako<\>T (preserveContent: bool = false): Kde T: řetězec, bajt[],JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Metody `context.Request.Body.As<T>` `context.Response.Body.As<T>` a se používají ke čtení těl požadavků a `T`zpráv odpovědi v zadaném typu . Ve výchozím nastavení metoda používá původní datového proudu textu zprávy a vykreslí jej k dispozici po návratu. Chcete-li se tomu vyhnout tím, že metoda pracovat `preserveContent` na `true`kopii datového proudu těla, nastavte parametr na . Jděte [sem](api-management-transformation-policies.md#SetBody) a podívejte se na příklad.|
|<a id="ref-iurl"></a>IUrl|Hostitel: řetězec<br /><br /> Cesta: řetězec<br /><br /> Port: int<br /><br /> [Dotaz](#ref-iurl-query): IReadOnlyDictionary<řetězec, řetězec[]><br /><br /> QueryString: řetězec<br /><br /> Schéma: řetězec|
|<a id="ref-iuseridentity"></a>Identita iUserIdentity|Id: řetězec<br /><br /> Zprostředkovatel: řetězec|
|<a id="ref-isubscriptionkeyparameternames"></a>iSubscriptionKeyParameterNames|Záhlaví: řetězec<br /><br /> Dotaz: řetězec|
|<a id="ref-iurl-query"></a>řetězec IUrl.Query.GetValueOrDefault(queryParameterName: řetězec, defaultValue: řetězec)|queryParameterName: řetězec<br /><br /> defaultValue: řetězec<br /><br /> Vrátí hodnoty parametrů dotazu oddělených čárkami nebo `defaultValue` pokud parametr nebyl nalezen.|
|<a id="ref-context-variables"></a>T kontext. Variables.GetValueOrDefault<\>T (variableName: řetězec, defaultValue: T)|variableName: řetězec<br /><br /> defaultValue: T<br /><br /> Vrátí přetypování `T` hodnoty `defaultValue` proměnné pro typ nebo pokud proměnná není nalezena.<br /><br /> Tato metoda vyvolá výjimku, pokud zadaný typ neodpovídá skutečnému typu vrácené proměnné.|
|BasicAuthCredentials AsBasic(input: tento řetězec)|vstup: řetězec<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu hlavičky požadavku na autorizaci základního ověřování HTTP, vrátí metoda objekt typu `BasicAuthCredentials`; jinak metoda vrátí hodnotu null.|
|bool TryParseBasic(vstup: tento řetězec, výsledek: out BasicAuthCredentials)|vstup: řetězec<br /><br /> výsledek: out BasicAuthCredentials<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu autorizace základního `true` ověřování HTTP v hlavičce požadavku, vrátí metoda a parametr result obsahuje hodnotu typu `BasicAuthCredentials`; jinak metoda `false`vrátí .|
|Základní authCredentials|Heslo: řetězec<br /><br /> UserId: řetězec|
|Jwt AsJwt(vstup: tento řetězec)|vstup: řetězec<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu tokenu JWT, vrátí metoda objekt typu `Jwt`; jinak metoda `null`vrátí .|
|bool TryParseJwt(vstup: tento řetězec, výsledek: z Jwt)|vstup: řetězec<br /><br /> výsledek: z Jwt<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu tokenu `true` JWT, metoda vrátí `Jwt`a parametr result obsahuje hodnotu typu ; jinak metoda `false`vrátí .|
|Jwt (Jwt)|Algoritmus: řetězec<br /><br /> Cílová skupina: IEnumerable<řetězec\><br /><br /> Deklarace identity: IReadOnlyDictionary<řetězec, řetězec[]><br /><br /> Expirace: DateTime?<br /><br /> Id: řetězec<br /><br /> Vydavatel: řetězec<br /><br /> IssuedAt: DateTime?<br /><br /> Nedříve: DateTime?<br /><br /> Předmět: řetězec<br /><br /> Typ: řetězec|
|řetězec Jwt.Claims.GetValueOrDefault(claimName: řetězec, defaultValue: řetězec)|claimName: řetězec<br /><br /> defaultValue: řetězec<br /><br /> Vrátí hodnoty deklarací oddělených `defaultValue` čárkami nebo pokud záhlaví není nalezeno.|
|byte[] Encrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - prostý text, který má být šifrován<br /><br />alg - název symetrického šifrovacího algoritmu<br /><br />klíč - šifrovací klíč<br /><br />iv - inicializační vektor<br /><br />Vrátí šifrovaný prostý text.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - prostý text, který má být šifrován<br /><br />alg - šifrovací algoritmus<br /><br />Vrátí šifrovaný prostý text.|
|byte[] Encrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - prostý text, který má být šifrován<br /><br />alg - šifrovací algoritmus<br /><br />klíč - šifrovací klíč<br /><br />iv - inicializační vektor<br /><br />Vrátí šifrovaný prostý text.|
|byte[] Decrypt(input: this byte[], alg: string, key:byte[], iv:byte[])|input - cypher text, který má být dešifrovován<br /><br />alg - název symetrického šifrovacího algoritmu<br /><br />klíč - šifrovací klíč<br /><br />iv - inicializační vektor<br /><br />Vrátí text ve formátu prostého textu.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm)|input - cypher text, který má být dešifrovován<br /><br />alg - šifrovací algoritmus<br /><br />Vrátí text ve formátu prostého textu.|
|byte[] Decrypt(input: this byte[], alg: System.Security.Cryptography.SymmetricAlgorithm, key:byte[], iv:byte[])|input - cypher text, který má být dešifrovován<br /><br />alg - šifrovací algoritmus<br /><br />klíč - šifrovací klíč<br /><br />iv - inicializační vektor<br /><br />Vrátí text ve formátu prostého textu.|
|bool VerifyNoRevocation(input: this System.Security.Cryptography.X509Certificates.X509Certificate2)|Provede ověření řetězce X.509 bez kontroly stavu odvolání certifikátu.<br /><br />input - objekt certifikátu<br /><br />Vrátí, `true` pokud je ověření úspěšné; `false` pokud se ověření nezdaří.|


## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami naleznete v tématu:

+ [Zásady ve správě rozhraní API](api-management-howto-policies.md)
+ [Transformovat api](transform-api.md)
+ [Odkaz na zásady](api-management-policy-reference.md) pro úplný seznam prohlášení zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)
