---
title: Výrazy zásad Azure API Management | Dokumentace Microsoftu
description: Další informace o výrazech zásad ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2019
ms.author: apimpm
ms.openlocfilehash: b8bd6e7c77faa54a8ebf0842cf140ef8aa73e953
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65834550"
---
# <a name="api-management-policy-expressions"></a>Výrazy zásad rozhraní API Management
Tento článek popisuje syntaxe výrazy zásad C# 7. Každý výraz má přístup k implicitně zadané [kontextu](api-management-policy-expressions.md#ContextVariables) proměnné a povolený [dílčí](api-management-policy-expressions.md#CLRTypes) typů rozhraní .NET Framework.

Další informace:

- Zjistit, jak poskytnout informace o kontextu back-end službu. Použití [nastavit parametr řetězce dotazu](api-management-transformation-policies.md#SetQueryStringParameter) a [hlavičky protokolu HTTP nastaven](api-management-transformation-policies.md#SetHTTPheader) zásady slouží k poskytování těchto informací.
- Jak používat [ověření tokenů JWT](api-management-access-restriction-policies.md#ValidateJWT) zásad má předběžně autorizovat přístup k operacím na základě tokenu deklarací identity.
- Zjistit, jak pomocí [pro inspekci API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) trasování, pokud chcete zobrazit, jak se vyhodnocují zásady a výsledky těchto hodnocení.
- Jak používat výrazy s [získat z mezipaměti](api-management-caching-policies.md#GetFromCache) a [Store do mezipaměti](api-management-caching-policies.md#StoreToCache) zásady ke konfiguraci ukládání odpovědí do mezipaměti API Management. Nastavení doby trvání, která odpovídá ukládání odpovědí do mezipaměti této služby back-end jako zadané zálohované služby `Cache-Control` směrnice.
- Zjistit, jak provádět filtrování obsahu. Odebrat datové prvky z odpovědi přijaté z back-end pomocí [řízení toku](api-management-advanced-policies.md#choose) a [tělo nastavit](api-management-transformation-policies.md#SetBody) zásady.
- Stáhnout příkazům zásady, najdete v článku [--samples/zásady služby api management](https://github.com/Azure/api-management-samples/tree/master/policies) úložiště GitHub.


## <a name="Syntax"></a> Syntaxe
Jeden příkaz výrazy jsou uzavřeny v `@(expression)`, kde `expression` ve správném formátu C# příkazu výrazu.

Vícepříkazové výrazy jsou uzavřeny v `@{expression}`. Všechny cesty kódu v rámci více příkazy výrazů musí končit `return` příkazu.

## <a name="PolicyExpressionsExamples"></a> Příklady

```
@(true)

@((1+1).ToString())

@("Hi There".Length)

@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)

@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)

@{
  string value;
  if (context.Request.Headers.TryGetValue("Authorization", out value))
  {
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));
  }
  else
  {
    return null;
  }
}
```

## <a name="PolicyExpressionsUsage"></a>Využití
Výrazy můžete použít jako hodnoty atributů nebo textové hodnoty v jakékoli API Management [zásady](api-management-policies.md) (Pokud odkaz na zásady neurčí jinak).

> [!IMPORTANT]
> Při použití výrazy zásad je pouze omezené ověřovací výrazy zásad při definici zásady. Výrazy jsou spouštěny příkazem brána v době běhu, všechny výjimky generované výrazy zásad za následek chybu modulu runtime.

## <a name="CLRTypes"></a> Typy rozhraní .NET framework, které jsou povoleny ve výrazech zásad
V následující tabulce jsou uvedeny typy rozhraní .NET Framework a jejich členy, které jsou povoleny ve výrazech zásad.

|Type|Podporované členy|
|--------------|-----------------------|
|Newtonsoft.Json.Formatting|Všechny|
|Newtonsoft.Json.JsonConvert|Funkce SerializeObject DeserializeObject.|
|Newtonsoft.Json.Linq.Extensions|Všechny|
|Newtonsoft.Json.Linq.JArray|Všechny|
|Newtonsoft.Json.Linq.JConstructor|Všechny|
|Newtonsoft.Json.Linq.JContainer|Všechny|
|Newtonsoft.Json.Linq.JObject|Všechny|
|Newtonsoft.Json.Linq.JProperty|Všechny|
|Newtonsoft.Json.Linq.JRaw|Všechny|
|Newtonsoft.Json.Linq.JToken|Všechny|
|Newtonsoft.Json.Linq.JTokenType|Všechny|
|Newtonsoft.Json.Linq.JValue|Všechny|
|System.Array|Všechny|
|System.BitConverter|Všechny|
|System.Boolean|Všechny|
|System.Byte|Všechny|
|System.Char|Všechny|
|System.Collections.Generic.Dictionary<TKey, TValue>|Všechny|
|System.Collections.Generic.HashSet<T>|Všechny|
|System.Collections.Generic.ICollection<T>|Všechny|
|System.Collections.Generic.IDictionary<TKey, TValue>|Všechny|
|System.Collections.Generic.IEnumerable<T>|Všechny|
|System.Collections.Generic.IEnumerator<T>|Všechny|
|System.Collections.Generic.IList<T>|Všechny|
|System.Collections.Generic.IReadOnlyCollection<T>|Všechny|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|Všechny|
|System.Collections.Generic.ISet<T>|Všechny|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Všechny|
|System.Collections.Generic.List<T>|Všechny|
|System.Collections.Generic.Queue<T>|Všechny|
|System.Collections.Generic.Stack<T>|Všechny|
|System.Convert|Všechny|
|System.DateTime|(Konstruktor), přidejte, Přidat_dny, AddHours, AddMilliseconds, AddMinutes, AddMonths, Přidat_sekundy, AddTicks, AddYears, datum, den, DayOfWeek, DayOfYear, DaysInMonth, hodinu, IsDaylightSavingTime, IsLeapYear, MaxValue, milisekund, minutu, MinValue, měsíc, nyní , Analýzy, druhý, odečítání, značky, TimeOfDay, dnes, ToString, UtcNow, rok|
|System.DateTimeKind|Čas UTC|
|System.DateTimeOffset|Všechny|
|System.Decimal|Všechny|
|System.Double|Všechny|
|System.Exception|Všechny|
|System.Guid|Všechny|
|System.Int16|Všechny|
|System.Int32|Všechny|
|System.Int64|Všechny|
|System.IO.StringReader|Všechny|
|System.IO.StringWriter|Všechny|
|Typy System.Linq.Enumerable|Všechny|
|System.Math|Všechny|
|System.MidpointRounding|Všechny|
|System.Net.WebUtility|Všechny|
|System.Nullable|Všechny|
|System.Random|Všechny|
|System.SByte|Všechny|
|System.Security.Cryptography.AsymmetricAlgorithm|Všechny|
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
|System.Security.Cryptography.RSASignaturePadding|Všechny|
|System.Security.Cryptography.SHA1|Všechny|
|System.Security.Cryptography.SHA1Managed|Všechny|
|System.Security.Cryptography.SHA256|Všechny|
|System.Security.Cryptography.SHA256Managed|Všechny|
|System.Security.Cryptography.SHA384|Všechny|
|System.Security.Cryptography.SHA384Managed|Všechny|
|System.Security.Cryptography.SHA512|Všechny|
|System.Security.Cryptography.SHA512Managed|Všechny|
|System.Security.Cryptography.SymmetricAlgorithm|Všechny|
|System.Security.Cryptography.X509Certificates.PublicKey|Všechny|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Všechny|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|Název|
|System.Security.Cryptography.X509Certificates.X509Certificate|Všechny|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Všechny|
|System.Security.Cryptography.X509Certificates.X509ContentType|Všechny|
|System.Security.Cryptography.X509Certificates.X509NameType|Všechny|
|System.Single|Všechny|
|System.String|Všechny|
|System.StringComparer|Všechny|
|System.StringComparison|Všechny|
|System.StringSplitOptions|Všechny|
|System.Text.Encoding|Všechny|
|System.Text.RegularExpressions.Capture|Hodnota indexu, délka,|
|System.Text.RegularExpressions.CaptureCollection|Počet položek|
|System.Text.RegularExpressions.Group|Zachycení, úspěch|
|System.Text.RegularExpressions.GroupCollection|Počet položek|
|System.Text.RegularExpressions.Match|Prázdná, skupiny, výsledek|
|System.Text.RegularExpressions.Regex|(Konstruktor) IsMatch, shoda, shody, nahraďte, Unescape, rozdělení|
|System.Text.RegularExpressions.RegexOptions|Všechny|
|System.Text.StringBuilder|Všechny|
|System.TimeSpan|Všechny|
|System.TimeZone|Všechny|
|System.TimeZoneInfo.AdjustmentRule|Všechny|
|System.TimeZoneInfo.TransitionTime|Všechny|
|System.TimeZoneInfo|Všechny|
|System.Tuple|Všechny|
|System.UInt16|Všechny|
|System.UInt32|Všechny|
|System.UInt64|Všechny|
|System.Uri|Všechny|
|System.UriPartial|Všechny|
|System.Xml.Linq.Extensions|Všechny|
|System.Xml.Linq.XAttribute|Všechny|
|System.Xml.Linq.XCData|Všechny|
|System.Xml.Linq.XComment|Všechny|
|System.Xml.Linq.XContainer|Všechny|
|System.Xml.Linq.XDeclaration|Všechny|
|System.Xml.Linq.XDocument|Všechny, kromě: Načtení|
|System.Xml.Linq.XDocumentType|Všechny|
|System.Xml.Linq.XElement|Všechny|
|System.Xml.Linq.XName|Všechny|
|System.Xml.Linq.XNamespace|Všechny|
|System.Xml.Linq.XNode|Všechny|
|System.Xml.Linq.XNodeDocumentOrderComparer|Všechny|
|System.Xml.Linq.XNodeEqualityComparer|Všechny|
|System.Xml.Linq.XObject|Všechny|
|System.Xml.Linq.XProcessingInstruction|Všechny|
|System.Xml.Linq.XText|Všechny|
|System.Xml.XmlNodeType|Všechny|

## <a name="ContextVariables"></a> Kontextové proměnné
Proměnné s názvem `context` je implicitně k dispozici v každé zásadě [výraz](api-management-policy-expressions.md#Syntax). Poskytují informace, které jsou relevantní pro jeho členů `\request`. Všechny `context` členy jsou jen pro čtení.

|Kontextové proměnné|Povolené metody, vlastnosti a hodnoty parametrů|
|----------------------|-------------------------------------------------------|
|Kontext|Rozhraní API: IApi<br /><br /> Nasazení<br /><br /> Uplynulý: Časový interval - časový interval mezi hodnotou časového razítka a aktuální čas<br /><br /> LastError<br /><br /> Operace<br /><br /> Product<br /><br /> Požadavek<br /><br /> ID žádosti: Identifikátor GUID - požadavek jedinečného identifikátoru<br /><br /> Odpověď<br /><br /> Předplatné<br /><br /> Časové razítko: Datum a čas - bodu v čase, kdy byl obdržen požadavek<br /><br /> Trasování: bool – označuje, pokud je trasování zapnuto nebo vypnuto <br /><br /> Uživatel<br /><br /> Proměnné: IReadOnlyDictionary < string, object ><br /><br /> void Trace(message: string)|
|context.Api|ID: řetězec<br /><br /> IsCurrentRevision: bool<br /><br />  Název: řetězec<br /><br /> Cesta: řetězec<br /><br /> Revize: řetězec<br /><br /> ServiceUrl: IUrl<br /><br /> Verze: řetězec |
|context.Deployment|Oblast: řetězec<br /><br /> ServiceName: řetězec<br /><br /> Certifikáty: IReadOnlyDictionary<string, X509Certificate2>|
|kontext. Poslední chyba|Zdroj: řetězec<br /><br /> Důvod: řetězec<br /><br /> Zpráva: řetězec<br /><br /> Obor: řetězec<br /><br /> Část: řetězec<br /><br /> Cesta: řetězec<br /><br /> PolicyId: řetězec<br /><br /> Další informace o kontextu. LastError, naleznete v tématu [zpracování chyb](api-management-error-handling-policies.md).|
|context.Operation|ID: řetězec<br /><br /> Metoda: řetězec<br /><br /> Název: řetězec<br /><br /> UrlTemplate: řetězec|
|context.Product|Rozhraní API: IEnumerable < IApi\><br /><br /> ApprovalRequired: bool<br /><br /> Skupiny: IEnumerable < IGroup\><br /><br /> ID: řetězec<br /><br /> Název: řetězec<br /><br /> Stav: výčtu ProductState {NotPublished, publikováno}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|kontext. Požadavek|Text zprávy: IMessageBody<br /><br /> Certifikát: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Záhlaví: IReadOnlyDictionary < string, string [] ><br /><br /> IP adresa: řetězec<br /><br /> MatchedParameters: IReadOnlyDictionary < string, string ><br /><br /> Metoda: řetězec<br /><br /> OriginalUrl:IUrl<br /><br /> Adresa URL: IUrl|
|kontext řetězce. Request.Headers.GetValueOrDefault (Název_hlavičky: řetězec, výchozí hodnota: string)|Název_hlavičky: řetězec<br /><br /> Výchozí hodnota: řetězec<br /><br /> Vrátí hodnoty hlavičky požadavku oddělených čárkou nebo `defaultValue` Pokud hlavička nebyla nalezena.|
|kontext. Odpověď|Text zprávy: IMessageBody<br /><br /> Záhlaví: IReadOnlyDictionary < string, string [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: řetězec|
|kontext řetězce. Response.Headers.GetValueOrDefault (Název_hlavičky: řetězec, výchozí hodnota: string)|Název_hlavičky: řetězec<br /><br /> Výchozí hodnota: řetězec<br /><br /> Vrátí hodnoty hlavičky odpovědi oddělených čárkou nebo `defaultValue` Pokud hlavička nebyla nalezena.|
|kontext. Předplatné|Čas vytvoření: DateTime<br /><br /> Datum ukončení: Datum a čas?<br /><br /> ID: řetězec<br /><br /> Klíč: řetězec<br /><br /> Název: řetězec<br /><br /> PrimaryKey: řetězec<br /><br /> Sekundární klíč: řetězec<br /><br /> StartDate: Datum a čas?|
|context.User|E-mailu: řetězec<br /><br /> Jméno: řetězec<br /><br /> Skupiny: IEnumerable < IGroup\><br /><br /> ID: řetězec<br /><br /> Identity: IEnumerable < IUserIdentity\><br /><br /> Příjmení: řetězec<br /><br /> Poznámka: řetězec<br /><br /> RegistrationDate: DateTime|
|IApi|ID: řetězec<br /><br /> Název: řetězec<br /><br /> Cesta: řetězec<br /><br /> Protokoly: IEnumerable < string\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|
|IGroup|ID: řetězec<br /><br /> Název: řetězec|
|IMessageBody|Jako < T\>(preserveContent: bool = false): Kde T: string, JObject, JToken, JArray, XNode, XElement a XDocument<br /><br /> `context.Request.Body.As<T>` a `context.Response.Body.As<T>` metody se používají ke čtení zpráv v zadaného typu žádostí a odpovědí `T`. Ve výchozím nastavení metoda používá původní datový proud obsahu zprávy a vykreslí není k dispozici po vrátí. Chcete-li tomu zabránili tak, že metoda pracovat na kopii této datový proud obsahu, nastavte `preserveContent` parametr `true`. Přejděte [tady](api-management-transformation-policies.md#SetBody) k prohlédnutí příkladu.|
|IUrl|Hostitel: řetězec<br /><br /> Cesta: řetězec<br /><br /> Port: int<br /><br /> Dotaz: IReadOnlyDictionary < string, string [] ><br /><br /> Řetězec dotazu: řetězec<br /><br /> Schéma: řetězec|
|IUserIdentity|ID: řetězec<br /><br /> Poskytovatel: řetězec|
|ISubscriptionKeyParameterNames|Záhlaví: řetězec<br /><br /> Dotaz: řetězec|
|řetězec IUrl.Query.GetValueOrDefault (queryParameterName: řetězec, výchozí hodnota: string)|queryParameterName: řetězec<br /><br /> Výchozí hodnota: řetězec<br /><br /> Vrátí hodnoty parametru dotazu oddělených čárkou nebo `defaultValue` Pokud parametr nebyl nalezen.|
|T kontextu. Variables.GetValueOrDefault < T\>(NázevProměnné: řetězec, výchozí hodnota: T)|NázevProměnné: řetězec<br /><br /> Výchozí hodnota: út<br /><br /> Vrátí hodnotu proměnné přetypován na typ `T` nebo `defaultValue` Pokud není nalezena proměnné.<br /><br /> Tato metoda vyvolá výjimku, pokud zadaný typ neodpovídá skutečné typ vrácený proměnné.|
|BasicAuthCredentials AsBasic(input: this string)|vstupu: řetězce<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu hlavičky autorizace požadavku HTTP Basic Authentication, vrátí metoda objekt typu `BasicAuthCredentials`; v opačném případě vrátí metoda hodnotu null.|
|BOOL TryParseBasic (vstup: Tento řetězec, výsledek: si BasicAuthCredentials)|vstupu: řetězce<br /><br /> výsledek: si BasicAuthCredentials<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu povolení základního ověřování protokolu HTTP v hlavičce žádosti vrátí metoda `true` a parametr výsledek obsahuje hodnotu typu `BasicAuthCredentials`; v opačném případě vrátí metoda `false`.|
|BasicAuthCredentials|Heslo: řetězec<br /><br /> ID uživatele: řetězec|
|Token Jwt AsJwt(input: this string)|vstupu: řetězce<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu tokenu JWT, vrátí metoda objekt typu `Jwt`; v opačném případě vrátí metoda `null`.|
|BOOL TryParseJwt (vstup: Tento řetězec, výsledek: na token Jwt)|vstupu: řetězce<br /><br /> výsledek: na token Jwt<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu tokenu JWT, metoda vrátí `true` a parametr výsledek obsahuje hodnotu typu `Jwt`; v opačném případě vrátí metoda `false`.|
|Jwt|Algoritmus: řetězec<br /><br /> Cílové skupiny: IEnumerable < string\><br /><br /> Deklarace identity: IReadOnlyDictionary < string, string [] ><br /><br /> ExpirationTime: Datum a čas?<br /><br /> ID: řetězec<br /><br /> Vystavitel: řetězec<br /><br /> IssuedAt: Datum a čas?<br /><br /> Neplatí před: Datum a čas?<br /><br /> Předmět: řetězec<br /><br /> Typ: řetězec|
|řetězec Jwt.Claims.GetValueOrDefault (claimName: řetězec, výchozí hodnota: string)|claimName: řetězec<br /><br /> Výchozí hodnota: řetězec<br /><br /> Vrátí čárkami oddělených hodnot deklarací identity nebo `defaultValue` Pokud hlavička nebyla nalezena.|
|Byte [] zašifrovat (vstupní: Tento byte [], alg: řetězec, klíč: byte [], iv:byte[])|(vstup) – ve formátu prostého textu šifrování<br /><br />alg - název algoritmu symetrického šifrování<br /><br />klíč – šifrovacího klíče<br /><br />vektor IV - inicializační vektor<br /><br />Vrátí zašifrovaný prostý text.|
|Byte [] zašifrovat (vstupní: Tento byte [], alg: System.Security.Cryptography.SymmetricAlgorithm)|(vstup) – ve formátu prostého textu šifrování<br /><br />alg - šifrovací algoritmus<br /><br />Vrátí zašifrovaný prostý text.|
|Byte [] zašifrovat (vstupní: Tento byte [], alg: System.Security.Cryptography.SymmetricAlgorithm, klíč: byte [], iv:byte[])|(vstup) – ve formátu prostého textu šifrování<br /><br />alg - šifrovací algoritmus<br /><br />klíč – šifrovacího klíče<br /><br />vektor IV - inicializační vektor<br /><br />Vrátí zašifrovaný prostý text.|
|Byte [] dešifrovat (vstupní: Tento byte [], alg: řetězec, klíč: byte [], iv:byte[])|vstup - šifer text k dešifrování<br /><br />alg - název algoritmu symetrického šifrování<br /><br />klíč – šifrovacího klíče<br /><br />vektor IV - inicializační vektor<br /><br />Vrátí hodnotu ve formátu prostého textu.|
|Byte [] dešifrovat (vstupní: Tento byte [], alg: System.Security.Cryptography.SymmetricAlgorithm)|vstup - šifer text k dešifrování<br /><br />alg - šifrovací algoritmus<br /><br />Vrátí hodnotu ve formátu prostého textu.|
|Byte [] dešifrovat (vstupní: Tento byte [], alg: System.Security.Cryptography.SymmetricAlgorithm, klíč: byte [], iv:byte[])|vstup - šifer text k dešifrování<br /><br />alg - šifrovací algoritmus<br /><br />klíč – šifrovacího klíče<br /><br />vektor IV - inicializační vektor<br /><br />Vrátí hodnotu ve formátu prostého textu.|
|BOOL VerifyNoRevocation (vstup: Tento System.Security.Cryptography.X509Certificates.X509Certificate2)|Provádí ověřování řetězu X.509 bez kontroly stav odvolání certifikátů.<br /><br />vstup - objekt certifikátu<br /><br />Vrátí `true` Pokud je ověření úspěšné; `false` Pokud se ověření nezdaří.|


## <a name="next-steps"></a>Další postup

Práce se zásadami pro další informace najdete v tématu:

+ [Zásady ve službě API Management](api-management-howto-policies.md)
+ [Transformujte rozhraní API](transform-api.md)
+ [Referenční příručce o zásadách](api-management-policy-reference.md) úplný seznam zásad příkazy a jejich nastavení
+ [Ukázky zásad](policy-samples.md)
