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

Další informace:

- Podívejte se, jak do back-endu poskytovat informace o kontextu. K poskytnutí těchto informací použijte [parametr nastavení řetězce dotazu](api-management-transformation-policies.md#SetQueryStringParameter) a [nastavte zásady hlaviček protokolu HTTP](api-management-transformation-policies.md#SetHTTPheader) .
- Podívejte se, jak použít zásadu [ověření JWT](api-management-access-restriction-policies.md#ValidateJWT) k předběžné autorizaci přístupu k operacím na základě deklarací identity tokenu.
- Podívejte se, jak pomocí trasování [inspektoru rozhraní API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) zjistit, jak se zásady vyhodnocují, a výsledky těchto hodnocení.
- Podívejte se, jak používat výrazy s možnostmi [načíst z mezipaměti](api-management-caching-policies.md#GetFromCache) a [Uložit do](api-management-caching-policies.md#StoreToCache) zásad ukládání do mezipaměti pro konfiguraci API Management ukládání odpovědí do mezipaměti. Nastavte dobu trvání, která odpovídá ukládání do mezipaměti back-end služby, jak je určeno direktivou `Cache-Control` služby zálohovaných služeb.
- Podívejte se, jak provádět filtrování obsahu. Odeberte datové prvky z odpovědi přijaté z back-endu pomocí [toku řízení](api-management-advanced-policies.md#choose) a nastavte zásady pro [tělo](api-management-transformation-policies.md#SetBody) .
- Pokud chcete stáhnout příkazy zásad, přečtěte si úložiště GitHub [API-Management-Samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) .


## <a name="Syntax"></a>Syntaktick
Výrazy jednoho příkazu jsou uzavřeny v `@(expression)`, kde `expression` je výrazem výrazu C# ve správném formátu.

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
|Newtonsoft. JSON. Formatting|Všechno|
|Newtonsoft. JSON. JsonConvert|Funkce SerializeObject, DeserializeObject|
|Newtonsoft. JSON. Linq. Extensions|Všechno|
|Newtonsoft. JSON. Linq. JArray|Všechno|
|Newtonsoft. JSON. Linq. JConstructor|Všechno|
|Newtonsoft. JSON. Linq. JContainer|Všechno|
|Newtonsoft. JSON. Linq. JObject|Všechno|
|Newtonsoft. JSON. Linq. JProperty|Všechno|
|Newtonsoft. JSON. Linq. JRaw|Všechno|
|Newtonsoft. JSON. Linq. JToken|Všechno|
|Newtonsoft. JSON. Linq. JTokenType|Všechno|
|Newtonsoft. JSON. Linq. JValue|Všechno|
|System. Array|Všechno|
|System. BitConverter|Všechno|
|System. Boolean|Všechno|
|System. Byte|Všechno|
|System. Char|Všechno|
|System. Collections. Generic. Dictionary < TKey, TValue >|Všechno|
|System. Collections. Generic. HashSet – @ no__t-0T >|Všechno|
|System. Collections. Generic. ICollection @ no__t-0T >|Všechno|
|System. Collections. Generic. IDictionary < TKey, TValue >|Všechno|
|System. Collections. Generic. IEnumerable @ no__t-0T >|Všechno|
|System. Collections. Generic. IEnumerator @ no__t-0T >|Všechno|
|System. Collections. Generic. ILists @ no__t-0T >|Všechno|
|System. Collections. Generic. IReadOnlyCollection @ no__t-0T >|Všechno|
|System. Collections. Generic. IReadOnlyDictionary < TKey, TValue >|Všechno|
|System. Collections. Generic. ISet @ no__t-0T >|Všechno|
|System. Collections. Generic. nenašla < TKey, TValue >|Všechno|
|System. Collections. Generic. list @ no__t-0T >|Všechno|
|System. Collections. Generic. Queue @ no__t-0T >|Všechno|
|System. Collections. Generic. Stack @ no__t-0T >|Všechno|
|System. Convert|Všechno|
|System. DateTime|(Konstruktor), Add, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, datum, den, DayOfWeek, DayOfYear, DaysInMonth, hodina, IsDaylightSavingTime, IsLeapYear, MaxValue, milisekunda, Minute, MinValue, month, Now , Analyze, Second, odečíst, takty, TimeOfDay, Today, ToString, UtcNow, Year|
|System. DateTimeKind|UTC|
|System. DateTimeOffset|Všechno|
|System. Decimal|Všechno|
|System. Double|Všechno|
|System. Exception|Všechno|
|System. GUID|Všechno|
|System. Int16|Všechno|
|System. Int32|Všechno|
|System. Int64|Všechno|
|System. IO. StringReader|Všechno|
|System. IO. StringWriter|Všechno|
|System. Linq. vyčíslitelné|Všechno|
|System. Math|Všechno|
|System. MidpointRounding|Všechno|
|System .NET. WebUtility|Všechno|
|System. Nullable|Všechno|
|System. Random|Všechno|
|System. SByte|Všechno|
|System. Security. Cryptography. AsymmetricAlgorithm|Všechno|
|System. Security. Cryptography. režim CipherMode|Všechno|
|System. Security. Cryptography. HashAlgorithm|Všechno|
|System. Security. Cryptography. HashAlgorithmName|Všechno|
|System. Security. Cryptography. HMAC|Všechno|
|System. Security. Cryptography. HMACMD5|Všechno|
|System. Security. Cryptography. HMACSHA1|Všechno|
|System. Security. Cryptography. HMACSHA256|Všechno|
|System. Security. Cryptography. HMACSHA384|Všechno|
|System. Security. Cryptography. HMACSHA512|Všechno|
|System. Security. Cryptography. KeyedHashAlgorithm|Všechno|
|System. Security. Cryptography. MD5|Všechno|
|System. Security. Cryptography. OID|Všechno|
|System. Security. Cryptography. PaddingMode|Všechno|
|System. Security. Cryptography. RNGCryptoServiceProvider|Všechno|
|System. Security. Cryptography. RSA|Všechno|
|System. Security. Cryptography. RSAEncryptionPadding|Všechno|
|System. Security. Cryptography. RSASignaturePadding|Všechno|
|System. Security. Cryptography. SHA1|Všechno|
|System. Security. Cryptography. SHA1Managed|Všechno|
|System. Security. Cryptography. SHA256|Všechno|
|System. Security. Cryptography. SHA256Managed|Všechno|
|System. Security. Cryptography. SHA384|Všechno|
|System. Security. Cryptography. SHA384Managed|Všechno|
|System. Security. Cryptography. SHA512|Všechno|
|System. Security. Cryptography. SHA512Managed|Všechno|
|System. Security. Cryptography. SymmetricAlgorithm|Všechno|
|System. Security. Cryptography. X509Certificates. PublicKey|Všechno|
|System. Security. Cryptography. X509Certificates. RSACertificateExtensions|Všechno|
|System. Security. Cryptography. X509Certificates. X500DistinguishedName|Name (Název)|
|System. Security. Cryptography. X509Certificates. certifikátu x509|Všechno|
|System. Security. Cryptography. X509Certificates. X509Certificate2|Všechno|
|System. Security. Cryptography. X509Certificates. X509ContentType|Všechno|
|System. Security. Cryptography. X509Certificates. X509NameType|Všechno|
|System. Single|Všechno|
|System. String|Všechno|
|System. StringComparer|Všechno|
|System. StringComparison|Všechno|
|System. StringSplitOptions|Všechno|
|System. text. Encoding|Všechno|
|System. text. RegularExpressions. Capture|Index, délka, hodnota|
|System. text. RegularExpressions. Capture –Collection|Počet, položka|
|System. text. RegularExpressions. Group|Zachycení, úspěch|
|System. text. RegularExpressions. GroupCollection|Počet, položka|
|System. text. RegularExpressions. Match|Prázdné, skupiny, výsledek|
|System. text. RegularExpressions. Regex|(Konstruktor), neshoda, shoda, shoda, nahradit, zrušit řídicí, rozdělit|
|System. text. RegularExpressions. RegexOptions|Všechno|
|System. text. StringBuilder|Všechno|
|System. TimeSpan|Všechno|
|System. TimeZone|Všechno|
|System. TimeZoneInfo. AdjustmentRule|Všechno|
|System. TimeZoneInfo. TransitionTime|Všechno|
|System. TimeZoneInfo|Všechno|
|System. Tuple|Všechno|
|System. UInt16|Všechno|
|System. UInt32|Všechno|
|System. UInt64|Všechno|
|System. URI|Všechno|
|System. UriPartial|Všechno|
|System. XML. Linq. Extensions|Všechno|
|System. XML. Linq. XAttribute|Všechno|
|System. XML. Linq. XCData|Všechno|
|System. XML. Linq. XComment|Všechno|
|System. XML. Linq. XContainer|Všechno|
|System. XML. Linq. XDeclaration|Všechno|
|System. XML. Linq. XDocument|Vše, s výjimkou: Load|
|System. XML. Linq. XDocumentType|Všechno|
|System. XML. Linq. XElement|Všechno|
|System. XML. Linq. XName|Všechno|
|System. XML. Linq. XNamespace|Všechno|
|System. XML. Linq. XNode|Všechno|
|System. XML. Linq. XNodeDocumentOrderComparer|Všechno|
|System. XML. Linq. XNodeEqualityComparer|Všechno|
|System. XML. Linq. XObject|Všechno|
|System. XML. Linq. XProcessingInstruction|Všechno|
|System. XML. Linq. XText|Všechno|
|System. XML. XmlNodeType|Všechno|

## <a name="ContextVariables"></a>Kontextová proměnná
Proměnná s názvem `context` je implicitně dostupná ve všech [výrazech](api-management-policy-expressions.md#Syntax)zásad. Její členové poskytují informace týkající se `\request`. Všechny členy `context` jsou jen pro čtení.

|Kontextová proměnná|Povolené metody, vlastnosti a hodnoty parametrů|
|----------------------|-------------------------------------------------------|
|Souvislost|[Rozhraní API](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Nasazení](#ref-context-deployment)<br /><br /> Uplynulý: časový interval časového intervalu mezi hodnotou časového razítka a aktuálním časem<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Operace](#ref-context-operation)<br /><br /> [Produktu](#ref-context-product)<br /><br /> [Požadavek](#ref-context-request)<br /><br /> RequestId: GUID – jedinečný identifikátor žádosti<br /><br /> [Odpověď](#ref-context-response)<br /><br /> [Předplatné](#ref-context-subscription)<br /><br /> Časové razítko: hodnota DateTime – bod v čase, kdy byla přijata žádost<br /><br /> Trasování: bool – určuje, zda je trasování zapnuto nebo vypnuto <br /><br /> [Uživatelský](#ref-context-user)<br /><br /> [Proměnné](#ref-context-variables): IReadOnlyDictionary < řetězec, > objektů<br /><br /> void trace (zpráva: String)|
|<a id="ref-context-api"></a>souvislost. API|ID: řetězec<br /><br /> IsCurrentRevision: bool<br /><br />  Název: řetězec<br /><br /> Cesta: řetězec<br /><br /> Revize: řetězec<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> Verze: řetězec |
|<a id="ref-context-deployment"></a>souvislost. Prostředí|Oblast: řetězec<br /><br /> ServiceName: řetězec<br /><br /> Certifikáty: IReadOnlyDictionary < řetězec, X509Certificate2 >|
|<a id="ref-context-lasterror"></a>souvislost. LastError|Zdroj: řetězec<br /><br /> Důvod: řetězec<br /><br /> Zpráva: řetězec<br /><br /> Obor: řetězec<br /><br /> Oddíl: řetězec<br /><br /> Cesta: řetězec<br /><br /> PolicyId: řetězec<br /><br /> Pro další informace o kontextu. Poslední chyba najdete v tématu [zpracování chyb](api-management-error-handling-policies.md).|
|<a id="ref-context-operation"></a>souvislost. NázevOperace|ID: řetězec<br /><br /> Metoda: String<br /><br /> Název: řetězec<br /><br /> UrlTemplate: řetězec|
|<a id="ref-context-product"></a>souvislost. Produktu|Rozhraní API: IEnumerable <[IApi](#ref-iapi)\><br /><br /> ApprovalRequired: bool<br /><br /> Skupiny: IEnumerable <[IGroup](#ref-igroup)\><br /><br /> ID: řetězec<br /><br /> Název: řetězec<br /><br /> Stav: enum ProductState {NotPublished, Publikováno}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|<a id="ref-context-request"></a>souvislost. Request|Tělo: [IMessageBody](#ref-imessagebody) nebo `null`, pokud požadavek nemá tělo.<br /><br /> Certifikát: System. Security. Cryptography. X509Certificates. X509Certificate2<br /><br /> [Headers](#ref-context-request-headers): IReadOnlyDictionary < String, String [] ><br /><br /> IpAddress: řetězec<br /><br /> MatchedParameters: IReadOnlyDictionary < řetězec, řetězec ><br /><br /> Metoda: String<br /><br /> OriginalUrl: [IUrl](#ref-iurl)<br /><br /> Adresa URL: [IUrl](#ref-iurl)|
|<a id="ref-context-request-headers"></a>kontext řetězce. Request. Headers. GetValueOrDefault (Header: řetězec, defaultValue: String)|záhlaví: řetězec<br /><br /> VýchozíHodnota: řetězec<br /><br /> Vrátí hodnoty hlaviček požadavku oddělené čárkou nebo `defaultValue`, pokud hlavička nebyla nalezena.|
|<a id="ref-context-response"></a>souvislost. Základě|Tělo: [IMessageBody](#ref-imessagebody)<br /><br /> [Headers](#ref-context-response-headers): IReadOnlyDictionary < String, String [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: řetězec|
|<a id="ref-context-response-headers"></a>kontext řetězce. Response. Headers. GetValueOrDefault (Header: řetězec, defaultValue: String)|záhlaví: řetězec<br /><br /> VýchozíHodnota: řetězec<br /><br /> Vrátí hodnoty hlaviček odpovědi oddělené čárkou nebo `defaultValue`, pokud záhlaví není nalezeno.|
|<a id="ref-context-subscription"></a>souvislost. Formě|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> ID: řetězec<br /><br /> Klíč: řetězec<br /><br /> Název: řetězec<br /><br /> PrimaryKey: řetězec<br /><br /> SecondaryKey: řetězec<br /><br /> StartDate: DateTime?|
|<a id="ref-context-user"></a>souvislost. Uživatelský|E-mail: řetězec<br /><br /> FirstName: řetězec<br /><br /> Skupiny: IEnumerable <[IGroup](#ref-igroup)\><br /><br /> ID: řetězec<br /><br /> Identity: IEnumerable <[IUserIdentity](#ref-iuseridentity)\><br /><br /> LastName: String<br /><br /> Poznámka: řetězec<br /><br /> RegistrationDate: DateTime|
|<a id="ref-iapi"></a>IApi|ID: řetězec<br /><br /> Název: řetězec<br /><br /> Cesta: řetězec<br /><br /> Protokoly: IEnumerable < String @ no__t-0<br /><br /> ServiceUrl: [IUrl](#ref-iurl)<br /><br /> SubscriptionKeyParameterNames: [ISubscriptionKeyParameterNames](#ref-isubscriptionkeyparameternames)|
|<a id="ref-igroup"></a>IGroup|ID: řetězec<br /><br /> Název: řetězec|
|<a id="ref-imessagebody"></a>IMessageBody|As < T @ no__t-0 (preserveContent: bool = false): kde T: String, Byte [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Metody `context.Request.Body.As<T>` a `context.Response.Body.As<T>` se používají ke čtení textu zprávy žádosti a odpovědi v zadaném typu `T`. Ve výchozím nastavení používá metoda původní datový proud zprávy a vykresluje jej po jeho vrácení k dispozici. Aby nedocházelo k tomu, že metoda bude fungovat na kopii datového proudu, nastavte parametr `preserveContent` na hodnotu `true`. Pokud si chcete prohlédnout příklad, přejděte [sem](api-management-transformation-policies.md#SetBody) .|
|<a id="ref-iurl"></a>IUrl|Hostitel: řetězec<br /><br /> Cesta: řetězec<br /><br /> Port: int<br /><br /> [Dotaz](#ref-iurl-query): IReadOnlyDictionary < řetězec, řetězec [] ><br /><br /> QueryString: řetězec<br /><br /> Schéma: řetězec|
|<a id="ref-iuseridentity"></a>IUserIdentity|ID: řetězec<br /><br /> Zprostředkovatel: řetězec|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Záhlaví: řetězec<br /><br /> Dotaz: řetězec|
|<a id="ref-iurl-query"></a>String IUrl. Query. GetValueOrDefault (queryParameterName: String; defaultValue: String)|queryParameterName: řetězec<br /><br /> VýchozíHodnota: řetězec<br /><br /> Vrátí hodnoty parametrů dotazu oddělené čárkou nebo `defaultValue`, pokud parametr nebyl nalezen.|
|<a id="ref-context-variables"></a>Kontext T Variables. GetValueOrDefault < T @ no__t-1 (proměnná: String, VýchozíHodnota: T)|proměnná: řetězec<br /><br /> VýchozíHodnota: T<br /><br /> Vrátí přetypování hodnoty proměnné na typ `T` nebo `defaultValue`, pokud proměnná nebyla nalezena.<br /><br /> Tato metoda vyvolá výjimku, pokud zadaný typ neodpovídá skutečnému typu vrácené proměnné.|
|BasicAuthCredentials AsBasic (vstup: Tento řetězec)|vstup: řetězec<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu hlavičky autorizačního požadavku na ověření Basic protokolu HTTP, metoda vrátí objekt typu `BasicAuthCredentials`; Jinak metoda vrátí hodnotu null.|
|bool TryParseBasic (vstup: Tento řetězec, výsledek: out BasicAuthCredentials)|vstup: řetězec<br /><br /> výsledek: out BasicAuthCredentials<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu autorizace základního ověřování protokolu HTTP v hlavičce požadavku, metoda vrátí `true` a parametr Result obsahuje hodnotu typu `BasicAuthCredentials`;. v opačném případě metoda vrátí `false`.|
|BasicAuthCredentials|Heslo: řetězec<br /><br /> UserId: String|
|JWT AsJwt (vstup: Tento řetězec)|vstup: řetězec<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu tokenu JWT, metoda vrátí objekt typu `Jwt`; v opačném případě metoda vrátí `null`.|
|bool TryParseJwt (vstup: Tento řetězec, výsledek: odchozí JWT)|vstup: řetězec<br /><br /> výsledek: odchozí JWT<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu tokenu JWT, metoda vrátí `true` a parametr Result obsahuje hodnotu typu `Jwt`;. v opačném případě metoda vrátí `false`.|
|Token|Algoritmus: řetězec<br /><br /> Cílová skupina: IEnumerable < řetězec @ no__t-0<br /><br /> Deklarace identity: IReadOnlyDictionary < String, String [] ><br /><br /> ExpirationTime: DateTime?<br /><br /> ID: řetězec<br /><br /> Vystavitel: řetězec<br /><br /> IssuedAt: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Předmět: řetězec<br /><br /> Typ: řetězec|
|Token JWT. Claims. GetValueOrDefault (deklarace identity: String, VýchozíHodnota: String)|deklarace identity: řetězec<br /><br /> VýchozíHodnota: řetězec<br /><br /> Vrátí hodnoty deklarace s čárkami oddělené čárkou nebo `defaultValue`, pokud záhlaví není nalezeno.|
|Byte [] Encrypted (Input: this Byte []; ALG: String; Key: Byte [], IV: Byte [])|vstup – prostý text, který se má zašifrovat<br /><br />ALG – název algoritmu symetrického šifrování<br /><br />šifrovací klíč klíče<br /><br />IV – vektor inicializace<br /><br />Vrátí šifrovaný prostý text.|
|Byte [] Encrypted (vstup: this Byte [], ALG: System. Security. Cryptography. SymmetricAlgorithm)|vstup – prostý text, který se má zašifrovat<br /><br />ALG – šifrovací algoritmus<br /><br />Vrátí šifrovaný prostý text.|
|Byte [] Encrypted (Input: this Byte []; ALG: System. Security. Cryptography. SymmetricAlgorithm; Key: Byte [], IV: Byte [])|vstup – prostý text, který se má zašifrovat<br /><br />ALG – šifrovací algoritmus<br /><br />šifrovací klíč klíče<br /><br />IV – vektor inicializace<br /><br />Vrátí šifrovaný prostý text.|
|Byte [] dešifrovat (vstup: Tento bajt [], ALG: řetězec, klíč: Byte [], IV: Byte [])|text Input-šifrováním, který se má dešifrovat<br /><br />ALG – název algoritmu symetrického šifrování<br /><br />šifrovací klíč klíče<br /><br />IV – vektor inicializace<br /><br />Vrátí prostý text.|
|Byte [] dešifrovat (vstup: this Byte [], ALG: System. Security. Cryptography. SymmetricAlgorithm)|text Input-šifrováním, který se má dešifrovat<br /><br />ALG – šifrovací algoritmus<br /><br />Vrátí prostý text.|
|Byte [] dešifrovat (vstup: this Byte [], ALG: System. Security. Cryptography. SymmetricAlgorithm, Key: Byte [], IV: Byte [])|text Input-šifrováním, který se má dešifrovat<br /><br />ALG – šifrovací algoritmus<br /><br />šifrovací klíč klíče<br /><br />IV – vektor inicializace<br /><br />Vrátí prostý text.|
|bool VerifyNoRevocation (vstup: This System. Security. Cryptography. X509Certificates. X509Certificate2)|Provede ověření řetězu X. 509 bez kontroly stavu odvolání certifikátu.<br /><br />Input – objekt certifikátu<br /><br />Vrátí `true` v případě úspěšného ověření. `false` v případě neúspěšného ověření.|


## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami najdete v těchto tématech:

+ [Zásady v API Management](api-management-howto-policies.md)
+ [Transformační rozhraní API](transform-api.md)
+ [Odkaz na zásady](api-management-policy-reference.md) pro úplný seznam příkazů zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)
