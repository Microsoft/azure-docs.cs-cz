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
ms.openlocfilehash: 22be5509a93d0713b8113ba17debfda3cf576006
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508956"
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
|Newtonsoft.Json.Formatting|Vše|
|Newtonsoft.Json.JsonConvert|Funkce SerializeObject DeserializeObject.|
|Newtonsoft.Json.Linq.Extensions|Vše|
|Newtonsoft.Json.Linq.JArray|Vše|
|Newtonsoft.Json.Linq.JConstructor|Vše|
|Newtonsoft.Json.Linq.JContainer|Vše|
|Newtonsoft.Json.Linq.JObject|Vše|
|Newtonsoft.Json.Linq.JProperty|Vše|
|Newtonsoft.Json.Linq.JRaw|Vše|
|Newtonsoft.Json.Linq.JToken|Vše|
|Newtonsoft.Json.Linq.JTokenType|Vše|
|Newtonsoft.Json.Linq.JValue|Vše|
|System.Array|Vše|
|System.BitConverter|Vše|
|System.Boolean|Vše|
|System.Byte|Vše|
|System.Char|Vše|
|System.Collections.Generic.Dictionary<TKey, TValue>|Vše|
|System.Collections.Generic.HashSet\<T>|Vše|
|System.Collections.Generic.ICollection\<T>|Vše|
|System.Collections.Generic.IDictionary<TKey, TValue>|Vše|
|System.Collections.Generic.IEnumerable\<T>|Vše|
|System.Collections.Generic.IEnumerator\<T>|Vše|
|System.Collections.Generic.IList\<T>|Vše|
|System.Collections.Generic.IReadOnlyCollection\<T>|Vše|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|Vše|
|System.Collections.Generic.ISet\<T>|Vše|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Vše|
|System.Collections.Generic.List\<T>|Vše|
|System.Collections.Generic.Queue\<T>|Vše|
|System.Collections.Generic.Stack\<T>|Vše|
|System.Convert|Vše|
|System.DateTime|(Konstruktor), přidejte, Přidat_dny, AddHours, AddMilliseconds, AddMinutes, AddMonths, Přidat_sekundy, AddTicks, AddYears, datum, den, DayOfWeek, DayOfYear, DaysInMonth, hodinu, IsDaylightSavingTime, IsLeapYear, MaxValue, milisekund, minutu, MinValue, měsíc, nyní , Analýzy, druhý, odečítání, značky, TimeOfDay, dnes, ToString, UtcNow, rok|
|System.DateTimeKind|Čas UTC|
|System.DateTimeOffset|Vše|
|System.Decimal|Vše|
|System.Double|Vše|
|System.Exception|Vše|
|System.Guid|Vše|
|System.Int16|Vše|
|System.Int32|Vše|
|System.Int64|Vše|
|System.IO.StringReader|Vše|
|System.IO.StringWriter|Vše|
|Typy System.Linq.Enumerable|Vše|
|System.Math|Vše|
|System.MidpointRounding|Vše|
|System.Net.WebUtility|Vše|
|System.Nullable|Vše|
|System.Random|Vše|
|System.SByte|Vše|
|System.Security.Cryptography.AsymmetricAlgorithm|Vše|
|System.Security.Cryptography.CipherMode|Vše|
|System.Security.Cryptography.HashAlgorithm|Vše|
|System.Security.Cryptography.HashAlgorithmName|Vše|
|System.Security.Cryptography.HMAC|Vše|
|System.Security.Cryptography.HMACMD5|Vše|
|System.Security.Cryptography.HMACSHA1|Vše|
|System.Security.Cryptography.HMACSHA256|Vše|
|System.Security.Cryptography.HMACSHA384|Vše|
|System.Security.Cryptography.HMACSHA512|Vše|
|System.Security.Cryptography.KeyedHashAlgorithm|Vše|
|System.Security.Cryptography.MD5|Vše|
|System.Security.Cryptography.Oid|Vše|
|System.Security.Cryptography.PaddingMode|Vše|
|System.Security.Cryptography.RNGCryptoServiceProvider|Vše|
|System.Security.Cryptography.RSA|Vše|
|System.Security.Cryptography.RSAEncryptionPadding|Vše|
|System.Security.Cryptography.RSASignaturePadding|Vše|
|System.Security.Cryptography.SHA1|Vše|
|System.Security.Cryptography.SHA1Managed|Vše|
|System.Security.Cryptography.SHA256|Vše|
|System.Security.Cryptography.SHA256Managed|Vše|
|System.Security.Cryptography.SHA384|Vše|
|System.Security.Cryptography.SHA384Managed|Vše|
|System.Security.Cryptography.SHA512|Vše|
|System.Security.Cryptography.SHA512Managed|Vše|
|System.Security.Cryptography.SymmetricAlgorithm|Vše|
|System.Security.Cryptography.X509Certificates.PublicKey|Vše|
|System.Security.Cryptography.X509Certificates.RSACertificateExtensions|Vše|
|System.Security.Cryptography.X509Certificates.X500DistinguishedName|Název|
|System.Security.Cryptography.X509Certificates.X509Certificate|Vše|
|System.Security.Cryptography.X509Certificates.X509Certificate2|Vše|
|System.Security.Cryptography.X509Certificates.X509ContentType|Vše|
|System.Security.Cryptography.X509Certificates.X509NameType|Vše|
|System.Single|Vše|
|System.String|Vše|
|System.StringComparer|Vše|
|System.StringComparison|Vše|
|System.StringSplitOptions|Vše|
|System.Text.Encoding|Vše|
|System.Text.RegularExpressions.Capture|Hodnota indexu, délka,|
|System.Text.RegularExpressions.CaptureCollection|Počet položek|
|System.Text.RegularExpressions.Group|Zachycení, úspěch|
|System.Text.RegularExpressions.GroupCollection|Počet položek|
|System.Text.RegularExpressions.Match|Prázdná, skupiny, výsledek|
|System.Text.RegularExpressions.Regex|(Konstruktor) IsMatch, shoda, shody, nahraďte, Unescape, rozdělení|
|System.Text.RegularExpressions.RegexOptions|Vše|
|System.Text.StringBuilder|Vše|
|System.TimeSpan|Vše|
|System.TimeZone|Vše|
|System.TimeZoneInfo.AdjustmentRule|Vše|
|System.TimeZoneInfo.TransitionTime|Vše|
|System.TimeZoneInfo|Vše|
|System.Tuple|Vše|
|System.UInt16|Vše|
|System.UInt32|Vše|
|System.UInt64|Vše|
|System.Uri|Vše|
|System.UriPartial|Vše|
|System.Xml.Linq.Extensions|Vše|
|System.Xml.Linq.XAttribute|Vše|
|System.Xml.Linq.XCData|Vše|
|System.Xml.Linq.XComment|Vše|
|System.Xml.Linq.XContainer|Vše|
|System.Xml.Linq.XDeclaration|Vše|
|System.Xml.Linq.XDocument|Všechny, kromě: Načtení|
|System.Xml.Linq.XDocumentType|Vše|
|System.Xml.Linq.XElement|Vše|
|System.Xml.Linq.XName|Vše|
|System.Xml.Linq.XNamespace|Vše|
|System.Xml.Linq.XNode|Vše|
|System.Xml.Linq.XNodeDocumentOrderComparer|Vše|
|System.Xml.Linq.XNodeEqualityComparer|Vše|
|System.Xml.Linq.XObject|Vše|
|System.Xml.Linq.XProcessingInstruction|Vše|
|System.Xml.Linq.XText|Vše|
|System.Xml.XmlNodeType|Vše|

## <a name="ContextVariables"></a> Kontextové proměnné
Proměnné s názvem `context` je implicitně k dispozici v každé zásadě [výraz](api-management-policy-expressions.md#Syntax). Poskytují informace, které jsou relevantní pro jeho členů `\request`. Všechny `context` členy jsou jen pro čtení.

|Kontextové proměnné|Povolené metody, vlastnosti a hodnoty parametrů|
|----------------------|-------------------------------------------------------|
|context|[Rozhraní API](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Nasazení](#ref-context-deployment)<br /><br /> Uplynulý: Časový interval - časový interval mezi hodnotou časového razítka a aktuální čas<br /><br /> [Poslední chyba](#ref-context-lasterror)<br /><br /> [Operace](#ref-context-operation)<br /><br /> [Produkt](#ref-context-product)<br /><br /> [Požadavek](#ref-context-request)<br /><br /> ID žádosti: Identifikátor GUID - požadavek jedinečného identifikátoru<br /><br /> [Odpověď](#ref-context-response)<br /><br /> [Předplatné](#ref-context-subscription)<br /><br /> Časové razítko: Datum a čas - bodu v čase, kdy byl obdržen požadavek<br /><br /> Trasování: bool – označuje, pokud je trasování zapnuto nebo vypnuto <br /><br /> [Uživatel](#ref-context-user)<br /><br /> [Proměnné](#ref-context-variables): IReadOnlyDictionary < string, object ><br /><br /> void Trace(message: string)|
|<a id="ref-context-api"></a>context.Api|ID: řetězec<br /><br /> IsCurrentRevision: bool<br /><br />  Název: řetězec<br /><br /> Cesta: řetězec<br /><br /> Revize: řetězec<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Verze: řetězec |
|<a id="ref-context-deployment"></a>context.Deployment|Oblast: řetězec<br /><br /> ServiceName: řetězec<br /><br /> Certifikáty: IReadOnlyDictionary<string, X509Certificate2>|
|<a id="ref-context-lasterror"></a>kontext. Poslední chyba|Zdroj: řetězec<br /><br /> Důvod: řetězec<br /><br /> Zpráva: řetězec<br /><br /> Obor: řetězec<br /><br /> Část: řetězec<br /><br /> Cesta: řetězec<br /><br /> PolicyId: řetězec<br /><br /> Další informace o kontextu. LastError, naleznete v tématu [zpracování chyb](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>context.Operation|ID: řetězec<br /><br /> Metoda: řetězec<br /><br /> Název: řetězec<br /><br /> UrlTemplate: řetězec|
|<a id="ref-context-product"></a>context.Product|Rozhraní API: IEnumerable <[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Skupiny: IEnumerable <[IGroup](#ref-igroup)\><br /><br /> ID: řetězec<br /><br /> Název: řetězec<br /><br /> Stav: výčtu ProductState {NotPublished, publikováno}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>kontext. Požadavek|Text: [IMessageBody](#ref-imessagebody) nebo `null` Pokud požadavek nemá tělo.<br /><br /> Certifikát: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Záhlaví](#ref-context-request-headers): IReadOnlyDictionary < string, string [] ><br /><br /> IP adresa: řetězec<br /><br /> MatchedParameters: IReadOnlyDictionary < string, string ><br /><br /> Metoda: řetězec<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> Adresa URL: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>kontext řetězce. Request.Headers.GetValueOrDefault (Název_hlavičky: řetězec, výchozí hodnota: string)|Název_hlavičky: řetězec<br /><br /> Výchozí hodnota: řetězec<br /><br /> Vrátí hodnoty hlavičky požadavku oddělených čárkou nebo `defaultValue` Pokud hlavička nebyla nalezena.|
|<a id="ref-context-response"></a>kontext. Odpověď|Text: [IMessageBody](#ref-imessagebody)<br /><br /> [Záhlaví](#ref-context-response-headers): IReadOnlyDictionary < string, string [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: řetězec|
|<a id="ref-context-response-headers"></a>kontext řetězce. Response.Headers.GetValueOrDefault (Název_hlavičky: řetězec, výchozí hodnota: string)|Název_hlavičky: řetězec<br /><br /> Výchozí hodnota: řetězec<br /><br /> Vrátí hodnoty hlavičky odpovědi oddělených čárkou nebo `defaultValue` Pokud hlavička nebyla nalezena.|
|<a id="ref-context-subscription"></a>kontext. Předplatné|Čas vytvoření: DateTime<br /><br /> Datum ukončení: Datum a čas?<br /><br /> ID: řetězec<br /><br /> Klíč: řetězec<br /><br /> Název: řetězec<br /><br /> PrimaryKey: řetězec<br /><br /> Sekundární klíč: řetězec<br /><br /> StartDate: Datum a čas?|
|<a id="ref-context-user"></a>context.User|E-mailu: řetězec<br /><br /> Jméno: řetězec<br /><br /> Skupiny: IEnumerable <[IGroup](#ref-igroup)\><br /><br /> ID: řetězec<br /><br /> Identity: IEnumerable <[IUserIdentity](#ref-iuseridentity)\><br /><br /> Příjmení: řetězec<br /><br /> Poznámka: řetězec<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>IApi|ID: řetězec<br /><br /> Název: řetězec<br /><br /> Cesta: řetězec<br /><br /> Protokoly: IEnumerable < string\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|ID: řetězec<br /><br /> Název: řetězec|
|<a id="ref-imessagebody"></a>IMessageBody|Jako < T\>(preserveContent: bool = false): Kde T: string, JObject, JToken, JArray, XNode, XElement a XDocument<br /><br /> `context.Request.Body.As<T>` a `context.Response.Body.As<T>` metody se používají ke čtení zpráv v zadaného typu žádostí a odpovědí `T`. Ve výchozím nastavení metoda používá původní datový proud obsahu zprávy a vykreslí není k dispozici po vrátí. Chcete-li tomu zabránili tak, že metoda pracovat na kopii této datový proud obsahu, nastavte `preserveContent` parametr `true`. Přejděte [tady](api-management-transformation-policies.md#SetBody) k prohlédnutí příkladu.|
|<a id="ref-iurl"></a>IUrl|Hostitel: řetězec<br /><br /> Cesta: řetězec<br /><br /> Port: int<br /><br /> [Dotaz:](#ref-iurl-query) IReadOnlyDictionary < string, string [] ><br /><br /> Řetězec dotazu: řetězec<br /><br /> Schéma: řetězec|
|<a id="ref-iuseridentity"></a>IUserIdentity|ID: řetězec<br /><br /> Poskytovatel: řetězec|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Záhlaví: řetězec<br /><br /> Dotaz: řetězec|
|<a id="ref-iurl-query"></a>řetězec IUrl.Query.GetValueOrDefault (queryParameterName: řetězec, výchozí hodnota: string)|queryParameterName: řetězec<br /><br /> Výchozí hodnota: řetězec<br /><br /> Vrátí hodnoty parametru dotazu oddělených čárkou nebo `defaultValue` Pokud parametr nebyl nalezen.|
|<a id="ref-context-variables"></a>T kontextu. Variables.GetValueOrDefault < T\>(NázevProměnné: řetězec, výchozí hodnota: T)|NázevProměnné: řetězec<br /><br /> Výchozí hodnota: T<br /><br /> Vrátí hodnotu proměnné přetypován na typ `T` nebo `defaultValue` Pokud není nalezena proměnné.<br /><br /> Tato metoda vyvolá výjimku, pokud zadaný typ neodpovídá skutečné typ vrácený proměnné.|
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
