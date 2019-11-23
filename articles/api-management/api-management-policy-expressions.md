---
title: Výrazy zásad Azure API Management | Microsoft Docs
description: Přečtěte si o výrazech zásad v Azure API Management.
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
ms.openlocfilehash: e9e6eff4c527ff2e22be57ebc1eb3dcdb3c4e0ab
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241979"
---
# <a name="api-management-policy-expressions"></a>API Management výrazy zásad
Tento článek popisuje syntaxi výrazů zásad je C# 7. Každý výraz má přístup k implicitně zadané [kontextové](api-management-policy-expressions.md#ContextVariables) proměnné a povolené [podmnožině](api-management-policy-expressions.md#CLRTypes) typů .NET Framework.

Další informace najdete tady:

- Podívejte se, jak do back-endu poskytovat informace o kontextu. K poskytnutí těchto informací použijte [parametr nastavení řetězce dotazu](api-management-transformation-policies.md#SetQueryStringParameter) a [nastavte zásady hlaviček protokolu HTTP](api-management-transformation-policies.md#SetHTTPheader) .
- Podívejte se, jak použít zásadu [ověření JWT](api-management-access-restriction-policies.md#ValidateJWT) k předběžné autorizaci přístupu k operacím na základě deklarací identity tokenu.
- Podívejte se, jak pomocí trasování [inspektoru rozhraní API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) zjistit, jak se zásady vyhodnocují, a výsledky těchto hodnocení.
- Podívejte se, jak používat výrazy s možnostmi [načíst z mezipaměti](api-management-caching-policies.md#GetFromCache) a [Uložit do](api-management-caching-policies.md#StoreToCache) zásad ukládání do mezipaměti pro konfiguraci API Management ukládání odpovědí do mezipaměti. Nastavte dobu trvání, která odpovídá ukládání do mezipaměti back-end služby, jak je uvedeno v direktivě `Cache-Control` zálohovaných služeb.
- Podívejte se, jak provádět filtrování obsahu. Odeberte datové prvky z odpovědi přijaté z back-endu pomocí [toku řízení](api-management-advanced-policies.md#choose) a nastavte zásady pro [tělo](api-management-transformation-policies.md#SetBody) .
- Pokud chcete stáhnout příkazy zásad, přečtěte si úložiště GitHub [API-Management-Samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) .


## <a name="Syntax"></a>Syntaktick
Výrazy jednoho příkazu jsou uzavřeny v `@(expression)`, kde `expression` je ve správném C# formátu příkazu výrazu.

Výrazy s více příkazy jsou uzavřeny v `@{expression}`. Všechny cesty kódu v rámci výrazů s více příkazy musí končit příkazem `return`.

## <a name="PolicyExpressionsExamples"></a>4.6

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

## <a name="PolicyExpressionsUsage"></a>Využívání
Výrazy lze použít jako hodnoty atributů nebo textové hodnoty v jakékoli [zásadě](api-management-policies.md) API Management (Pokud odkaz na zásadu neurčuje jinak).

> [!IMPORTANT]
> Když použijete výrazy zásad, je při definování zásady k dispozici jenom omezené ověření výrazů zásad. Za běhu jsou výrazy spouštěné bránou, takže všechny výjimky vygenerované výrazy zásad mají za následek chybu za běhu.

## <a name="CLRTypes"></a>Typy .NET Framework povolené ve výrazech zásad
Následující tabulka uvádí typy .NET Framework a jejich členy, které jsou povoleny ve výrazech zásad.

|Typ|Podporovaní členové|
|--------------|-----------------------|
|Newtonsoft. JSON. Formatting|Vše|
|Newtonsoft. JSON. JsonConvert|Funkce SerializeObject, DeserializeObject|
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
|System. Array|Vše|
|System. BitConverter|Vše|
|System. Boolean|Vše|
|System.Byte|Vše|
|System.Char|Vše|
|System.Collections.Generic.Dictionary<TKey, TValue>|Vše|
|System. Collections. Generic. HashSet –\<T >|Vše|
|System.Collections.Generic.ICollection\<T>|Vše|
|System.Collections.Generic.IDictionary<TKey, TValue>|Vše|
|System. Collections. Generic. IEnumerable\<T >|Vše|
|System. Collections. Generic. IEnumerator\<T >|Vše|
|System. Collections. Generic. IList\<T >|Vše|
|System.Collections.Generic.IReadOnlyCollection\<T>|Vše|
|System.Collections.Generic.IReadOnlyDictionary<TKey, TValue>|Vše|
|System.Collections.Generic.ISet\<T>|Vše|
|System.Collections.Generic.KeyValuePair<TKey, TValue>|Vše|
|System. Collections. Generic. list\<T >|Vše|
|System.Collections.Generic.Queue\<T>|Vše|
|System.Collections.Generic.Stack\<T>|Vše|
|System.Convert|Vše|
|System.DateTime|(Konstruktor), Add, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, datum, den, DayOfWeek, DayOfYear, DaysInMonth, hodina, IsDaylightSavingTime, IsLeapYear, MaxValue, milisekunda, Minute, MinValue, month, Now , Analyze, Second, odečíst, takty, TimeOfDay, Today, ToString, UtcNow, Year|
|System.DateTimeKind|UTC|
|System.DateTimeOffset|Vše|
|System.Decimal|Vše|
|System.Double|Vše|
|System. Exception|Vše|
|System.Guid|Vše|
|System.Int16|Vše|
|System.Int32|Vše|
|System.Int64|Vše|
|System.IO.StringReader|Vše|
|System. IO. StringWriter|Vše|
|System. Linq. vyčíslitelné|Vše|
|System.Math|Vše|
|System. MidpointRounding|Vše|
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
|System. Single|Vše|
|System. String|Vše|
|System.StringComparer|Vše|
|System.StringComparison|Vše|
|System.StringSplitOptions|Vše|
|System.Text.Encoding|Vše|
|System.Text.RegularExpressions.Capture|Index, délka, hodnota|
|System.Text.RegularExpressions.CaptureCollection|Počet, položka|
|System.Text.RegularExpressions.Group|Zachycení, úspěch|
|System.Text.RegularExpressions.GroupCollection|Počet, položka|
|System.Text.RegularExpressions.Match|Prázdné, skupiny, výsledek|
|System.Text.RegularExpressions.Regex|(Konstruktor), neshoda, shoda, shoda, nahradit, zrušit řídicí, rozdělit|
|System.Text.RegularExpressions.RegexOptions|Vše|
|System.Text.StringBuilder|Vše|
|System. TimeSpan|Vše|
|System.TimeZone|Vše|
|System.TimeZoneInfo.AdjustmentRule|Vše|
|System.TimeZoneInfo.TransitionTime|Vše|
|System.TimeZoneInfo|Vše|
|System.Tuple|Vše|
|System.UInt16|Vše|
|System.UInt32|Vše|
|System.UInt64|Vše|
|System.Uri|Vše|
|System. UriPartial|Vše|
|System.Xml.Linq.Extensions|Vše|
|System.Xml.Linq.XAttribute|Vše|
|System.Xml.Linq.XCData|Vše|
|System.Xml.Linq.XComment|Vše|
|System.Xml.Linq.XContainer|Vše|
|System.Xml.Linq.XDeclaration|Vše|
|System.Xml.Linq.XDocument|Vše, s výjimkou: Load|
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

## <a name="ContextVariables"></a>Kontextová proměnná
Proměnná s názvem `context` je implicitně dostupná ve všech [výrazech](api-management-policy-expressions.md#Syntax)zásad. Její členové poskytují informace, které se vztahují k `\request`. Všechny členy `context` jsou jen pro čtení.

|Kontextová proměnná|Povolené metody, vlastnosti a hodnoty parametrů|
|----------------------|-------------------------------------------------------|
|context|[Rozhraní API](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Nasazení](#ref-context-deployment)<br /><br /> Uplynulý: časový interval časového intervalu mezi hodnotou časového razítka a aktuálním časem<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Operace](#ref-context-operation)<br /><br /> [Produktu](#ref-context-product)<br /><br /> [Požadavek](#ref-context-request)<br /><br /> RequestId: GUID – jedinečný identifikátor žádosti<br /><br /> [Odpověď](#ref-context-response)<br /><br /> [Předplatné](#ref-context-subscription)<br /><br /> Časové razítko: hodnota DateTime – bod v čase, kdy byla přijata žádost<br /><br /> Trasování: bool – určuje, zda je trasování zapnuto nebo vypnuto <br /><br /> [Uživatelský](#ref-context-user)<br /><br /> [Proměnné](#ref-context-variables): IReadOnlyDictionary < řetězec, > objektů<br /><br /> void trace (zpráva: String)|
|<a id="ref-context-api"></a>context.Api|ID: řetězec<br /><br /> IsCurrentRevision: bool<br /><br />  Název: řetězec<br /><br /> Cesta: řetězec<br /><br /> Revize: řetězec<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Verze: řetězec |
|<a id="ref-context-deployment"></a>souvislost. Prostředí|Oblast: řetězec<br /><br /> ServiceName: řetězec<br /><br /> Certifikáty: IReadOnlyDictionary < řetězec, X509Certificate2 >|
|<a id="ref-context-lasterror"></a>souvislost. LastError|Zdroj: řetězec<br /><br /> Důvod: řetězec<br /><br /> Zpráva: řetězec<br /><br /> Obor: řetězec<br /><br /> Oddíl: řetězec<br /><br /> Cesta: řetězec<br /><br /> PolicyId: řetězec<br /><br /> Pro další informace o kontextu. Poslední chyba najdete v tématu [zpracování chyb](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>context.Operation|ID: řetězec<br /><br /> Metoda: String<br /><br /> Název: řetězec<br /><br /> UrlTemplate: řetězec|
|<a id="ref-context-product"></a>context.Product|Rozhraní API: IEnumerable <[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Skupiny: IEnumerable <[IGroup](#ref-igroup)\><br /><br /> ID: řetězec<br /><br /> Název: řetězec<br /><br /> Stav: enum ProductState {NotPublished, Publikováno}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>souvislost. Request|Tělo: [IMessageBody](#ref-imessagebody) nebo `null`, pokud žádost neobsahuje tělo.<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> [Headers](#ref-context-request-headers): IReadOnlyDictionary < String, String [] ><br /><br /> IpAddress: řetězec<br /><br /> MatchedParameters: IReadOnlyDictionary < řetězec, řetězec ><br /><br /> Metoda: String<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> Adresa URL: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>kontext řetězce. Request. Headers. GetValueOrDefault (Header: řetězec, defaultValue: String)|záhlaví: řetězec<br /><br /> VýchozíHodnota: řetězec<br /><br /> Vrátí hodnoty hlaviček požadavku oddělené čárkou nebo `defaultValue`, pokud záhlaví není nalezeno.|
|<a id="ref-context-response"></a>souvislost. Základě|Tělo: [IMessageBody](#ref-imessagebody)<br /><br /> [Headers](#ref-context-response-headers): IReadOnlyDictionary < String, String [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: řetězec|
|<a id="ref-context-response-headers"></a>kontext řetězce. Response. Headers. GetValueOrDefault (Header: řetězec, defaultValue: String)|záhlaví: řetězec<br /><br /> VýchozíHodnota: řetězec<br /><br /> Vrátí hodnoty záhlaví odpovědi oddělené čárkou nebo `defaultValue`, pokud záhlaví není nalezeno.|
|<a id="ref-context-subscription"></a>souvislost. Formě|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> ID: řetězec<br /><br /> Klíč: řetězec<br /><br /> Název: řetězec<br /><br /> PrimaryKey: řetězec<br /><br /> SecondaryKey: řetězec<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>context.User|E-mail: řetězec<br /><br /> FirstName: řetězec<br /><br /> Skupiny: IEnumerable <[IGroup](#ref-igroup)\><br /><br /> ID: řetězec<br /><br /> Identity: IEnumerable <[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: String<br /><br /> Poznámka: řetězec<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>IApi|ID: řetězec<br /><br /> Název: řetězec<br /><br /> Cesta: řetězec<br /><br /> Protokoly: IEnumerable < řetězec\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|ID: řetězec<br /><br /> Název: řetězec|
|<a id="ref-imessagebody"></a>IMessageBody|Jako < T\>(preserveContent: bool = false): kde T: String, Byte [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Metody `context.Request.Body.As<T>` a `context.Response.Body.As<T>` slouží ke čtení textu zprávy žádosti a odpovědi v zadaném typu `T`. Ve výchozím nastavení používá metoda původní datový proud zprávy a vykresluje jej po jeho vrácení k dispozici. Chcete-li zabránit tomu, aby metoda pracovala na kopii toku textu, nastavte parametr `preserveContent` na hodnotu `true`. Pokud si chcete prohlédnout příklad, přejděte [sem](api-management-transformation-policies.md#SetBody) .|
|<a id="ref-iurl"></a>IUrl|Hostitel: řetězec<br /><br /> Cesta: řetězec<br /><br /> Port: int<br /><br /> [Dotaz](#ref-iurl-query): IReadOnlyDictionary < řetězec, řetězec [] ><br /><br /> QueryString: řetězec<br /><br /> Schéma: řetězec|
|<a id="ref-iuseridentity"></a>IUserIdentity|ID: řetězec<br /><br /> Zprostředkovatel: řetězec|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Záhlaví: řetězec<br /><br /> Dotaz: řetězec|
|<a id="ref-iurl-query"></a>String IUrl. Query. GetValueOrDefault (queryParameterName: String; defaultValue: String)|queryParameterName: řetězec<br /><br /> VýchozíHodnota: řetězec<br /><br /> Vrátí hodnoty parametrů dotazu oddělené čárkou nebo `defaultValue`, pokud parametr nebyl nalezen.|
|<a id="ref-context-variables"></a>Kontext T Variables. GetValueOrDefault < T\>(proměnná: String, VýchozíHodnota: T)|proměnná: řetězec<br /><br /> VýchozíHodnota: T<br /><br /> Vrátí přetypování hodnoty proměnné na typ `T` nebo `defaultValue`, pokud proměnná nebyla nalezena.<br /><br /> Tato metoda vyvolá výjimku, pokud zadaný typ neodpovídá skutečnému typu vrácené proměnné.|
|BasicAuthCredentials AsBasic (vstup: Tento řetězec)|vstup: řetězec<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu hlavičky autorizačního požadavku na ověření Basic protokolu HTTP, metoda vrátí objekt typu `BasicAuthCredentials`; Jinak metoda vrátí hodnotu null.|
|bool TryParseBasic (vstup: Tento řetězec, výsledek: out BasicAuthCredentials)|vstup: řetězec<br /><br /> výsledek: out BasicAuthCredentials<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu autorizace ověřování Basic protokolu HTTP v hlavičce požadavku, metoda vrátí `true` a parametr Result obsahuje hodnotu typu `BasicAuthCredentials`; v opačném případě metoda vrátí `false`.|
|BasicAuthCredentials|Heslo: řetězec<br /><br /> UserId: String|
|JWT AsJwt (vstup: Tento řetězec)|vstup: řetězec<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu tokenu JWT, metoda vrátí objekt typu `Jwt`; v opačném případě metoda vrátí `null`.|
|bool TryParseJwt (vstup: Tento řetězec, výsledek: odchozí JWT)|vstup: řetězec<br /><br /> výsledek: odchozí JWT<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu tokenu JWT, metoda vrátí `true` a parametr Result obsahuje hodnotu typu `Jwt`; v opačném případě metoda vrátí `false`.|
|Jwt|Algoritmus: řetězec<br /><br /> Cílová skupina: IEnumerable < řetězec\><br /><br /> Deklarace identity: IReadOnlyDictionary < String, String [] ><br /><br /> ExpirationTime: DateTime?<br /><br /> ID: řetězec<br /><br /> Vystavitel: řetězec<br /><br /> IssuedAt: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Předmět: řetězec<br /><br /> Typ: řetězec|
|Token JWT. Claims. GetValueOrDefault (deklarace identity: String, VýchozíHodnota: String)|deklarace identity: řetězec<br /><br /> VýchozíHodnota: řetězec<br /><br /> Vrátí hodnoty deklarace, které jsou odděleny čárkou, nebo `defaultValue`, pokud záhlaví není nalezeno.|
|Byte [] Encrypted (Input: this Byte []; ALG: String; Key: Byte [], IV: Byte [])|vstup – prostý text, který se má zašifrovat<br /><br />ALG – název algoritmu symetrického šifrování<br /><br />šifrovací klíč klíče<br /><br />IV – vektor inicializace<br /><br />Vrátí šifrovaný prostý text.|
|Byte [] Encrypted (vstup: this Byte [], ALG: System. Security. Cryptography. SymmetricAlgorithm)|vstup – prostý text, který se má zašifrovat<br /><br />ALG – šifrovací algoritmus<br /><br />Vrátí šifrovaný prostý text.|
|Byte [] Encrypted (Input: this Byte []; ALG: System. Security. Cryptography. SymmetricAlgorithm; Key: Byte [], IV: Byte [])|vstup – prostý text, který se má zašifrovat<br /><br />ALG – šifrovací algoritmus<br /><br />šifrovací klíč klíče<br /><br />IV – vektor inicializace<br /><br />Vrátí šifrovaný prostý text.|
|Byte [] dešifrovat (vstup: Tento bajt [], ALG: řetězec, klíč: Byte [], IV: Byte [])|text Input-šifrováním, který se má dešifrovat<br /><br />ALG – název algoritmu symetrického šifrování<br /><br />šifrovací klíč klíče<br /><br />IV – vektor inicializace<br /><br />Vrátí prostý text.|
|Byte [] dešifrovat (vstup: this Byte [], ALG: System. Security. Cryptography. SymmetricAlgorithm)|text Input-šifrováním, který se má dešifrovat<br /><br />ALG – šifrovací algoritmus<br /><br />Vrátí prostý text.|
|Byte [] dešifrovat (vstup: this Byte [], ALG: System. Security. Cryptography. SymmetricAlgorithm, Key: Byte [], IV: Byte [])|text Input-šifrováním, který se má dešifrovat<br /><br />ALG – šifrovací algoritmus<br /><br />šifrovací klíč klíče<br /><br />IV – vektor inicializace<br /><br />Vrátí prostý text.|
|bool VerifyNoRevocation (vstup: This System. Security. Cryptography. X509Certificates. X509Certificate2)|Provede ověření řetězu X. 509 bez kontroly stavu odvolání certifikátu.<br /><br />Input – objekt certifikátu<br /><br />Vrátí `true`, pokud je ověření úspěšné; `false`, pokud ověření selhalo.|


## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami najdete v těchto tématech:

+ [Zásady v API Management](api-management-howto-policies.md)
+ [Transformační rozhraní API](transform-api.md)
+ [Odkaz na zásady](api-management-policy-reference.md) pro úplný seznam příkazů zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)
