---
title: Výrazy zásad Azure API Management | Microsoft Docs
description: Přečtěte si o výrazech zásad v Azure API Management. Podívejte se na příklady a zobrazte další dostupné prostředky.
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
ms.openlocfilehash: ab83344f779f93107b59ca28348da3a66f1efc1a
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076857"
---
# <a name="api-management-policy-expressions"></a>API Management výrazy zásad
Tento článek popisuje syntaxi výrazů zásad v jazyce C# 7. Každý výraz má přístup k implicitně zadané [kontextové](api-management-policy-expressions.md#ContextVariables) proměnné a povolené [podmnožině](api-management-policy-expressions.md#CLRTypes) typů .NET Framework.

Další informace najdete tady:

- Podívejte se, jak do back-endu poskytovat informace o kontextu. K poskytnutí těchto informací použijte [parametr nastavení řetězce dotazu](api-management-transformation-policies.md#SetQueryStringParameter) a [nastavte zásady hlaviček protokolu HTTP](api-management-transformation-policies.md#SetHTTPheader) .
- Podívejte se, jak použít zásadu [ověření JWT](api-management-access-restriction-policies.md#ValidateJWT) k předběžné autorizaci přístupu k operacím na základě deklarací identity tokenu.
- Podívejte se, jak pomocí trasování [inspektoru rozhraní API](./api-management-howto-api-inspector.md) zjistit, jak se zásady vyhodnocují, a výsledky těchto hodnocení.
- Podívejte se, jak používat výrazy s možnostmi [načíst z mezipaměti](api-management-caching-policies.md#GetFromCache) a [Uložit do](api-management-caching-policies.md#StoreToCache) zásad ukládání do mezipaměti pro konfiguraci API Management ukládání odpovědí do mezipaměti. Nastavte dobu trvání, která odpovídá ukládání do mezipaměti služby back-end, jak je uvedeno v direktivě zálohované služby `Cache-Control` .
- Podívejte se, jak provádět filtrování obsahu. Odeberte datové prvky z odpovědi přijaté z back-endu pomocí [toku řízení](api-management-advanced-policies.md#choose) a nastavte zásady pro [tělo](api-management-transformation-policies.md#SetBody) .
- Pokud chcete stáhnout příkazy zásad, přečtěte si úložiště GitHub [API-Management-Samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) .


## <a name="syntax"></a><a name="Syntax"></a> Syntaktick
Výrazy jednoho příkazu jsou uzavřeny v `@(expression)` , kde `expression` je ve správném formátu C# příkaz výrazu.

Výrazy s více příkazy jsou uzavřeny v `@{expression}` . Všechny cesty kódu v rámci výrazů vícenásobného příkazu musí končit `return` příkazem.

## <a name="examples"></a><a name="PolicyExpressionsExamples"></a> 4.6

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

## <a name="usage"></a><a name="PolicyExpressionsUsage"></a>Využití
Výrazy lze použít jako hodnoty atributů nebo textové hodnoty v jakékoli [zásadě](api-management-policies.md) API Management (Pokud odkaz na zásadu neurčuje jinak).

> [!IMPORTANT]
> Když použijete výrazy zásad, je při definování zásady k dispozici jenom omezené ověření výrazů zásad. Za běhu jsou výrazy spouštěné bránou, takže všechny výjimky vygenerované výrazy zásad mají za následek chybu za běhu.

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a> Typy .NET Framework povolené ve výrazech zásad
Následující tabulka uvádí typy .NET Framework a jejich členy, které jsou povoleny ve výrazech zásad.

|Typ|Podporovaní členové|
|--------------|-----------------------|
|Newtonsoft.Js. Kurzívy|Vše|
|Newtonsoft.Json.Jspři převodu|Funkce SerializeObject, DeserializeObject|
|Newtonsoft.Js. LINQ. Extensions|Vše|
|Newtonsoft.Js. LINQ. JArray|Vše|
|Newtonsoft.Js. LINQ. JConstructor|Vše|
|Newtonsoft.Js. LINQ. JContainer|Vše|
|Newtonsoft.Js. LINQ. JObject|Vše|
|Newtonsoft.Js. LINQ. JProperty|Vše|
|Newtonsoft.Js. LINQ. JRaw|Vše|
|Newtonsoft.Js. LINQ. JToken|Vše|
|Newtonsoft.Js. LINQ. JTokenType|Vše|
|Newtonsoft.Js. LINQ. JValue|Vše|
|System. Array|Vše|
|System. BitConverter|Vše|
|System. Boolean|Vše|
|System. Byte|Vše|
|System. Char|Vše|
|System. Collections. Generic. Dictionary<TKey, TValue>|Vše|
|System. Collections. Generic. HashSet –\<T>|Vše|
|System. Collections. Generic. ICollection\<T>|Vše|
|System. Collections. Generic. IDictionary<TKey, TValue>|Vše|
|System. Collections. Generic. IEnumerable\<T>|Vše|
|System. Collections. Generic. IEnumerator\<T>|Vše|
|System. Collections. Generic. IList\<T>|Vše|
|System. Collections. Generic. IReadOnlyCollection\<T>|Vše|
|System. Collections. Generic. IReadOnlyDictionary<TKey, TValue>|Vše|
|System. Collections. Generic. ISet\<T>|Vše|
|System. Collections. Generic. nenašla<TKey, TValue>|Vše|
|System. Collections. Generic. list\<T>|Vše|
|System. Collections. Generic. Queue\<T>|Vše|
|System. Collections. Generic. Stack\<T>|Vše|
|System. Convert|Vše|
|System. DateTime|(Konstruktor), Add, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, datum, den, DayOfWeek, DayOfYear, DaysInMonth, hodina, IsDaylightSavingTime, IsLeapYear, MaxValue, milisekunda, minuta, MinValue, měsíc, dnes, ToString, TimeOfDay, Year|
|System. DateTimeKind|UTC|
|System. DateTimeOffset|Vše|
|System. Decimal|Vše|
|System. Double|Vše|
|System. Exception|Vše|
|System. GUID|Vše|
|System. Int16|Vše|
|System. Int32|Vše|
|System. Int64|Vše|
|System. IO. StringReader|Vše|
|System. IO. StringWriter|Vše|
|System. Linq. vyčíslitelné|Vše|
|System. Math|Vše|
|System. MidpointRounding|Vše|
|System .NET. WebUtility|Vše|
|System. Nullable|Vše|
|System. Random|Vše|
|System. SByte|Vše|
|System. Security. Cryptography. AsymmetricAlgorithm|Vše|
|System. Security. Cryptography. režim CipherMode|Vše|
|System. Security. Cryptography. HashAlgorithm|Vše|
|System. Security. Cryptography. HashAlgorithmName|Vše|
|System. Security. Cryptography. HMAC|Vše|
|System. Security. Cryptography. HMACMD5|Vše|
|System. Security. Cryptography. HMACSHA1|Vše|
|System. Security. Cryptography. HMACSHA256|Vše|
|System. Security. Cryptography. HMACSHA384|Vše|
|System. Security. Cryptography. HMACSHA512|Vše|
|System. Security. Cryptography. KeyedHashAlgorithm|Vše|
|System. Security. Cryptography. MD5|Vše|
|System. Security. Cryptography. OID|Vše|
|System. Security. Cryptography. PaddingMode|Vše|
|System. Security. Cryptography. RNGCryptoServiceProvider|Vše|
|System. Security. Cryptography. RSA|Vše|
|System. Security. Cryptography. RSAEncryptionPadding|Vše|
|System. Security. Cryptography. RSASignaturePadding|Vše|
|System. Security. Cryptography. SHA1|Vše|
|System. Security. Cryptography. SHA1Managed|Vše|
|System. Security. Cryptography. SHA256|Vše|
|System. Security. Cryptography. SHA256Managed|Vše|
|System. Security. Cryptography. SHA384|Vše|
|System. Security. Cryptography. SHA384Managed|Vše|
|System. Security. Cryptography. SHA512|Vše|
|System. Security. Cryptography. SHA512Managed|Vše|
|System. Security. Cryptography. SymmetricAlgorithm|Vše|
|System. Security. Cryptography. X509Certificates. PublicKey|Vše|
|System. Security. Cryptography. X509Certificates. RSACertificateExtensions|Vše|
|System. Security. Cryptography. X509Certificates. X500DistinguishedName|Name|
|System. Security. Cryptography. X509Certificates. certifikátu x509|Vše|
|System. Security. Cryptography. X509Certificates. X509Certificate2|Vše|
|System. Security. Cryptography. X509Certificates. X509ContentType|Vše|
|System. Security. Cryptography. X509Certificates. X509NameType|Vše|
|System. Single|Vše|
|System. String|Vše|
|System. StringComparer|Vše|
|System. StringComparison|Vše|
|System. StringSplitOptions|Vše|
|System. text. Encoding|Vše|
|System. text. RegularExpressions. Capture|Index, délka, hodnota|
|System. text. RegularExpressions. Capture –Collection|Počet, položka|
|System. text. RegularExpressions. Group|Zachycení, úspěch|
|System. text. RegularExpressions. GroupCollection|Počet, položka|
|System. text. RegularExpressions. Match|Prázdné, skupiny, výsledek|
|System. text. RegularExpressions. Regex|(Konstruktor), neshoda, shoda, shoda, nahradit, zrušit řídicí, rozdělit|
|System. text. RegularExpressions. RegexOptions|Vše|
|System. text. StringBuilder|Vše|
|System. TimeSpan|Vše|
|System. TimeZone|Vše|
|System. TimeZoneInfo. AdjustmentRule|Vše|
|System. TimeZoneInfo. TransitionTime|Vše|
|System. TimeZoneInfo|Vše|
|System. Tuple|Vše|
|System. UInt16|Vše|
|System. UInt32|Vše|
|System. UInt64|Vše|
|System. URI|Vše|
|System. UriPartial|Vše|
|System.Xml. LINQ. Extensions|Vše|
|System.Xml. LINQ. XAttribute|Vše|
|System.Xml. LINQ. XCData|Vše|
|System.Xml. LINQ. XComment|Vše|
|System.Xml. LINQ. XContainer|Vše|
|System.Xml. LINQ. XDeclaration|Vše|
|System.Xml. LINQ. XDocument|Vše, s výjimkou: Load|
|System.Xml. LINQ. XDocumentType|Vše|
|System.Xml. LINQ. XElement|Vše|
|System.Xml. LINQ. XName|Vše|
|System.Xml. LINQ. XNamespace|Vše|
|System.Xml. LINQ. XNode|Vše|
|System.Xml. LINQ. XNodeDocumentOrderComparer|Vše|
|System.Xml. LINQ. XNodeEqualityComparer|Vše|
|System.Xml. LINQ. XObject|Vše|
|System.Xml. LINQ. XProcessingInstruction|Vše|
|System.Xml. LINQ. XText|Vše|
|System.Xml.XmlNodeType|Vše|

## <a name="context-variable"></a><a name="ContextVariables"></a> Kontextová proměnná
Proměnná s názvem `context` je implicitně dostupná ve všech [výrazech](api-management-policy-expressions.md#Syntax)zásad. Jeho členové poskytují informace, které jsou užitečné pro `\request` . Všichni `context` Členové jsou jen pro čtení.

|Kontextová proměnná|Povolené metody, vlastnosti a hodnoty parametrů|
|----------------------|-------------------------------------------------------|
|kontext|[Rozhraní API](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Nasazení](#ref-context-deployment)<br /><br /> Uplynulý: časový interval časového intervalu mezi hodnotou časového razítka a aktuálním časem<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Operace](#ref-context-operation)<br /><br /> [Product](#ref-context-product) (Produkt)<br /><br /> [Žádost](#ref-context-request)<br /><br /> RequestId: GUID – jedinečný identifikátor žádosti<br /><br /> [Response](#ref-context-response) (Odpověď)<br /><br /> [Předplatné](#ref-context-subscription)<br /><br /> Časové razítko: hodnota DateTime – bod v čase, kdy byla přijata žádost<br /><br /> Trasování: bool – určuje, zda je trasování zapnuto nebo vypnuto <br /><br /> [Uživatel](#ref-context-user)<br /><br /> [Proměnné](#ref-context-variables): IReadOnlyDictionary<řetězec,> objektů<br /><br /> void trace (zpráva: String)|
|<a id="ref-context-api"></a>souvislost. API|ID: řetězec<br /><br /> IsCurrentRevision: bool<br /><br />  Název: řetězec<br /><br /> Cesta: řetězec<br /><br /> Revize: řetězec<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Verze: řetězec |
|<a id="ref-context-deployment"></a>souvislost. Prostředí|Oblast: řetězec<br /><br /> ServiceName: řetězec<br /><br /> Certifikáty: IReadOnlyDictionary<řetězec, X509Certificate2>|
|<a id="ref-context-lasterror"></a>souvislost. LastError|Zdroj: řetězec<br /><br /> Důvod: řetězec<br /><br /> Zpráva: řetězec<br /><br /> Obor: řetězec<br /><br /> Oddíl: řetězec<br /><br /> Cesta: řetězec<br /><br /> PolicyId: řetězec<br /><br /> Pro další informace o kontextu. Poslední chyba najdete v tématu [zpracování chyb](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>souvislost. NázevOperace|ID: řetězec<br /><br /> Metoda: String<br /><br /> Název: řetězec<br /><br /> UrlTemplate: řetězec|
|<a id="ref-context-product"></a>souvislost. Produktu|Rozhraní API: IEnumerable<[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Skupiny: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> ID: řetězec<br /><br /> Název: řetězec<br /><br /> Stav: enum ProductState {NotPublished, Publikováno}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>souvislost. Request|Tělo: [IMessageBody](#ref-imessagebody) nebo `null` Pokud Request nemá tělo.<br /><br /> Certifikát: System. Security. Cryptography. X509Certificates. X509Certificate2<br /><br /> [Headers](#ref-context-request-headers): IReadOnlyDictionary<String, String [] ><br /><br /> IpAddress: řetězec<br /><br /> MatchedParameters: IReadOnlyDictionary<řetězec, řetězec><br /><br /> Metoda: String<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> Adresa URL: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>kontext řetězce. Request. Headers. GetValueOrDefault (Header: řetězec, defaultValue: String)|záhlaví: řetězec<br /><br /> VýchozíHodnota: řetězec<br /><br /> Vrátí hodnoty záhlaví žádosti oddělené čárkou nebo `defaultValue` , pokud záhlaví není nalezeno.|
|<a id="ref-context-response"></a>souvislost. Základě|Tělo: [IMessageBody](#ref-imessagebody)<br /><br /> [Headers](#ref-context-response-headers): IReadOnlyDictionary<String, String [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: řetězec|
|<a id="ref-context-response-headers"></a>kontext řetězce. Response. Headers. GetValueOrDefault (Header: řetězec, defaultValue: String)|záhlaví: řetězec<br /><br /> VýchozíHodnota: řetězec<br /><br /> Vrátí hodnoty záhlaví odpovědi oddělené čárkou nebo `defaultValue` Pokud záhlaví není nalezeno.|
|<a id="ref-context-subscription"></a>souvislost. Formě|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> ID: řetězec<br /><br /> Klíč: řetězec<br /><br /> Název: řetězec<br /><br /> PrimaryKey: řetězec<br /><br /> SecondaryKey: řetězec<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>souvislost. Uživatelský|E-mail: řetězec<br /><br /> FirstName: řetězec<br /><br /> Skupiny: IEnumerable<[IGroup](#ref-igroup)\><br /><br /> ID: řetězec<br /><br /> Identity: IEnumerable<[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: String<br /><br /> Poznámka: řetězec<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>IApi|ID: řetězec<br /><br /> Název: řetězec<br /><br /> Cesta: řetězec<br /><br /> Protokoly: IEnumerable<řetězec\><br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|ID: řetězec<br /><br /> Název: řetězec|
|<a id="ref-imessagebody"></a>IMessageBody|As<T \> (preserveContent: bool = false): kde T: String, Byte [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> `context.Request.Body.As<T>`Metody a `context.Response.Body.As<T>` se používají ke čtení těla zprávy požadavku a odpovědi v zadaném typu `T` . Ve výchozím nastavení používá metoda původní datový proud zprávy a vykresluje jej po jeho vrácení k dispozici. Chcete-li zabránit tomu, aby metoda pracovala na kopii toku textu, nastavte `preserveContent` parametr na `true` . Pokud si chcete prohlédnout příklad, přejděte [sem](api-management-transformation-policies.md#SetBody) .|
|<a id="ref-iurl"></a>IUrl|Hostitel: řetězec<br /><br /> Cesta: řetězec<br /><br /> Port: int<br /><br /> [Dotaz](#ref-iurl-query): IReadOnlyDictionary<řetězec, řetězec [] ><br /><br /> QueryString: řetězec<br /><br /> Schéma: řetězec|
|<a id="ref-iuseridentity"></a>IUserIdentity|ID: řetězec<br /><br /> Zprostředkovatel: řetězec|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Záhlaví: řetězec<br /><br /> Dotaz: řetězec|
|<a id="ref-iurl-query"></a>String IUrl. Query. GetValueOrDefault (queryParameterName: String; defaultValue: String)|queryParameterName: řetězec<br /><br /> VýchozíHodnota: řetězec<br /><br /> Vrátí hodnoty parametrů dotazu oddělené čárkou nebo `defaultValue` , pokud nebyl nalezen parametr.|
|<a id="ref-context-variables"></a>Kontext T Variables. GetValueOrDefault<T \> (proměnná: String, VýchozíHodnota: T)|proměnná: řetězec<br /><br /> VýchozíHodnota: T<br /><br /> Vrátí přetypování hodnoty proměnné na typ `T` nebo, `defaultValue` Pokud proměnná nebyla nalezena.<br /><br /> Tato metoda vyvolá výjimku, pokud zadaný typ neodpovídá skutečnému typu vrácené proměnné.|
|BasicAuthCredentials AsBasic (vstup: Tento řetězec)|vstup: řetězec<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu hlavičky žádosti o autorizaci ověřování pomocí protokolu HTTP, metoda vrátí objekt typu `BasicAuthCredentials` , jinak metoda vrátí hodnotu null.|
|bool TryParseBasic (vstup: Tento řetězec, výsledek: out BasicAuthCredentials)|vstup: řetězec<br /><br /> výsledek: out BasicAuthCredentials<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu autorizace ověřování Basic protokolu HTTP v hlavičce požadavku, metoda se vrátí `true` a parametr výsledku obsahuje hodnotu typu `BasicAuthCredentials` ; v opačném případě se metoda vrátí `false` .|
|BasicAuthCredentials|Heslo: řetězec<br /><br /> UserId: String|
|JWT AsJwt (vstup: Tento řetězec)|vstup: řetězec<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu tokenu JWT, metoda vrátí objekt typu `Jwt` . v opačném případě metoda vrátí `null` .|
|bool TryParseJwt (vstup: Tento řetězec, výsledek: odchozí JWT)|vstup: řetězec<br /><br /> výsledek: odchozí JWT<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu tokenu JWT, metoda vrátí `true` a výsledný parametr obsahuje hodnotu typu `Jwt` ; v opačném případě se metoda vrátí `false` .|
|Token|Algoritmus: řetězec<br /><br /> Cílové skupiny: IEnumerable<řetězec\><br /><br /> Deklarace identity: IReadOnlyDictionary<String, String [] ><br /><br /> ExpirationTime: DateTime?<br /><br /> ID: řetězec<br /><br /> Vystavitel: řetězec<br /><br /> IssuedAt: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Předmět: řetězec<br /><br /> Typ: řetězec|
|Token JWT. Claims. GetValueOrDefault (deklarace identity: String, VýchozíHodnota: String)|deklarace identity: řetězec<br /><br /> VýchozíHodnota: řetězec<br /><br /> Vrátí hodnoty deklarace s oddělovači, nebo `defaultValue` Pokud záhlaví není nalezeno.|
|Byte [] Encrypted (Input: this Byte []; ALG: String; Key: Byte [], IV: Byte [])|vstup – prostý text, který se má zašifrovat<br /><br />ALG – název algoritmu symetrického šifrování<br /><br />šifrovací klíč klíče<br /><br />IV – vektor inicializace<br /><br />Vrátí šifrovaný prostý text.|
|Byte [] Encrypted (vstup: this Byte [], ALG: System. Security. Cryptography. SymmetricAlgorithm)|vstup – prostý text, který se má zašifrovat<br /><br />ALG – šifrovací algoritmus<br /><br />Vrátí šifrovaný prostý text.|
|Byte [] Encrypted (Input: this Byte []; ALG: System. Security. Cryptography. SymmetricAlgorithm; Key: Byte [], IV: Byte [])|vstup – prostý text, který se má zašifrovat<br /><br />ALG – šifrovací algoritmus<br /><br />šifrovací klíč klíče<br /><br />IV – vektor inicializace<br /><br />Vrátí šifrovaný prostý text.|
|Byte [] dešifrovat (vstup: Tento bajt [], ALG: řetězec, klíč: Byte [], IV: Byte [])|text Input-šifrováním, který se má dešifrovat<br /><br />ALG – název algoritmu symetrického šifrování<br /><br />šifrovací klíč klíče<br /><br />IV – vektor inicializace<br /><br />Vrátí prostý text.|
|Byte [] dešifrovat (vstup: this Byte [], ALG: System. Security. Cryptography. SymmetricAlgorithm)|text Input-šifrováním, který se má dešifrovat<br /><br />ALG – šifrovací algoritmus<br /><br />Vrátí prostý text.|
|Byte [] dešifrovat (vstup: this Byte [], ALG: System. Security. Cryptography. SymmetricAlgorithm, Key: Byte [], IV: Byte [])|text Input-šifrováním, který se má dešifrovat<br /><br />ALG – šifrovací algoritmus<br /><br />šifrovací klíč klíče<br /><br />IV – vektor inicializace<br /><br />Vrátí prostý text.|
|bool VerifyNoRevocation (vstup: This System. Security. Cryptography. X509Certificates. X509Certificate2)|Provede ověření řetězu X. 509 bez kontroly stavu odvolání certifikátu.<br /><br />Input – objekt certifikátu<br /><br />Vrátí `true` , zda je ověření úspěšné `false` . Pokud se ověření nezdaří.|


## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami najdete v těchto tématech:

+ [Zásady v API Management](api-management-howto-policies.md)
+ [Transformační rozhraní API](transform-api.md)
+ [Odkaz na zásady](./api-management-policies.md) pro úplný seznam příkazů zásad a jejich nastavení
+ [Ukázky zásad](./policy-reference.md)