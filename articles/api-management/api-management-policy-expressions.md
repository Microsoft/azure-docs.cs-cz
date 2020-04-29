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
ms.openlocfilehash: 6614e70d130abe46067c657bda3ccdd7000caddc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79244001"
---
# <a name="api-management-policy-expressions"></a>API Management výrazy zásad
Tento článek popisuje syntaxi výrazů zásad v jazyce C# 7. Každý výraz má přístup k implicitně zadané [kontextové](api-management-policy-expressions.md#ContextVariables) proměnné a povolené [podmnožině](api-management-policy-expressions.md#CLRTypes) typů .NET Framework.

Další informace najdete tady:

- Podívejte se, jak do back-endu poskytovat informace o kontextu. K poskytnutí těchto informací použijte [parametr nastavení řetězce dotazu](api-management-transformation-policies.md#SetQueryStringParameter) a [nastavte zásady hlaviček protokolu HTTP](api-management-transformation-policies.md#SetHTTPheader) .
- Podívejte se, jak použít zásadu [ověření JWT](api-management-access-restriction-policies.md#ValidateJWT) k předběžné autorizaci přístupu k operacím na základě deklarací identity tokenu.
- Podívejte se, jak pomocí trasování [inspektoru rozhraní API](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) zjistit, jak se zásady vyhodnocují, a výsledky těchto hodnocení.
- Podívejte se, jak používat výrazy s možnostmi [načíst z mezipaměti](api-management-caching-policies.md#GetFromCache) a [Uložit do](api-management-caching-policies.md#StoreToCache) zásad ukládání do mezipaměti pro konfiguraci API Management ukládání odpovědí do mezipaměti. Nastavte dobu trvání, která odpovídá ukládání do mezipaměti služby back-end, jak je uvedeno v `Cache-Control` direktivě zálohované služby.
- Podívejte se, jak provádět filtrování obsahu. Odeberte datové prvky z odpovědi přijaté z back-endu pomocí [toku řízení](api-management-advanced-policies.md#choose) a nastavte zásady pro [tělo](api-management-transformation-policies.md#SetBody) .
- Pokud chcete stáhnout příkazy zásad, přečtěte si úložiště GitHub [API-Management-Samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) .


## <a name="syntax"></a><a name="Syntax"></a>Syntaktick
Výrazy jednoho příkazu jsou uzavřeny `@(expression)`v, `expression` kde je ve správném formátu C# příkaz výrazu.

Výrazy s více příkazy jsou uzavřeny `@{expression}`v. Všechny cesty kódu v rámci výrazů vícenásobného příkazu musí končit `return` příkazem.

## <a name="examples"></a><a name="PolicyExpressionsExamples"></a>4.6

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

## <a name="usage"></a><a name="PolicyExpressionsUsage"></a>Využívání
Výrazy lze použít jako hodnoty atributů nebo textové hodnoty v jakékoli [zásadě](api-management-policies.md) API Management (Pokud odkaz na zásadu neurčuje jinak).

> [!IMPORTANT]
> Když použijete výrazy zásad, je při definování zásady k dispozici jenom omezené ověření výrazů zásad. Za běhu jsou výrazy spouštěné bránou, takže všechny výjimky vygenerované výrazy zásad mají za následek chybu za běhu.

## <a name="net-framework-types-allowed-in-policy-expressions"></a><a name="CLRTypes"></a>Typy .NET Framework povolené ve výrazech zásad
Následující tabulka uvádí typy .NET Framework a jejich členy, které jsou povoleny ve výrazech zásad.

|Typ|Podporovaní členové|
|--------------|-----------------------|
|Newtonsoft. JSON. Formatting|Všechny|
|Newtonsoft. JSON. JsonConvert|Funkce SerializeObject, DeserializeObject|
|Newtonsoft. JSON. Linq. Extensions|Všechny|
|Newtonsoft. JSON. Linq. JArray|Všechny|
|Newtonsoft. JSON. Linq. JConstructor|Všechny|
|Newtonsoft. JSON. Linq. JContainer|Všechny|
|Newtonsoft. JSON. Linq. JObject|Všechny|
|Newtonsoft. JSON. Linq. JProperty|Všechny|
|Newtonsoft. JSON. Linq. JRaw|Všechny|
|Newtonsoft. JSON. Linq. JToken|Všechny|
|Newtonsoft. JSON. Linq. JTokenType|Všechny|
|Newtonsoft. JSON. Linq. JValue|Všechny|
|System. Array|Všechny|
|System. BitConverter|Všechny|
|System. Boolean|Všechny|
|System. Byte|Všechny|
|System. Char|Všechny|
|System. Collections. Generic. Dictionary<TKey, TValue>|Všechny|
|System. Collections. Generic.\<hashset – T>|Všechny|
|System. Collections. Generic.\<ICollection T>|Všechny|
|System. Collections. Generic. IDictionary<TKey, TValue>|Všechny|
|System. Collections. Generic.\<IEnumerable T>|Všechny|
|System. Collections. Generic.\<IEnumerator T>|Všechny|
|System. Collections. Generic.\<IList T>|Všechny|
|System. Collections. Generic.\<IReadOnlyCollection T>|Všechny|
|System. Collections. Generic. IReadOnlyDictionary<TKey, TValue>|Všechny|
|System. Collections. Generic.\<ISet T>|Všechny|
|System. Collections. Generic. nenašla<TKey, TValue>|Všechny|
|System. Collections. Generic.\<List T>|Všechny|
|System. Collections. Generic.\<Queue T>|Všechny|
|System. Collections. Generic.\<Stack T>|Všechny|
|System. Convert|Všechny|
|System. DateTime|(Konstruktor), Add, AddDays, AddHours, AddMilliseconds, AddMinutes, AddMonths, AddSeconds, AddTicks, AddYears, datum, den, DayOfWeek, DayOfYear, DaysInMonth, hodina, IsDaylightSavingTime, IsLeapYear, MaxValue, milisekunda, minuta, MinValue, měsíc, dnes, ToString, TimeOfDay, Year|
|System. DateTimeKind|UTC|
|System. DateTimeOffset|Všechny|
|System. Decimal|Všechny|
|System. Double|Všechny|
|System. Exception|Všechny|
|System. GUID|Všechny|
|System. Int16|Všechny|
|System. Int32|Všechny|
|System. Int64|Všechny|
|System. IO. StringReader|Všechny|
|System. IO. StringWriter|Všechny|
|System. Linq. vyčíslitelné|Všechny|
|System. Math|Všechny|
|System. MidpointRounding|Všechny|
|System .NET. WebUtility|Všechny|
|System. Nullable|Všechny|
|System. Random|Všechny|
|System. SByte|Všechny|
|System. Security. Cryptography. AsymmetricAlgorithm|Všechny|
|System. Security. Cryptography. režim CipherMode|Všechny|
|System. Security. Cryptography. HashAlgorithm|Všechny|
|System. Security. Cryptography. HashAlgorithmName|Všechny|
|System. Security. Cryptography. HMAC|Všechny|
|System. Security. Cryptography. HMACMD5|Všechny|
|System. Security. Cryptography. HMACSHA1|Všechny|
|System. Security. Cryptography. HMACSHA256|Všechny|
|System. Security. Cryptography. HMACSHA384|Všechny|
|System. Security. Cryptography. HMACSHA512|Všechny|
|System. Security. Cryptography. KeyedHashAlgorithm|Všechny|
|System. Security. Cryptography. MD5|Všechny|
|System. Security. Cryptography. OID|Všechny|
|System. Security. Cryptography. PaddingMode|Všechny|
|System. Security. Cryptography. RNGCryptoServiceProvider|Všechny|
|System. Security. Cryptography. RSA|Všechny|
|System. Security. Cryptography. RSAEncryptionPadding|Všechny|
|System. Security. Cryptography. RSASignaturePadding|Všechny|
|System. Security. Cryptography. SHA1|Všechny|
|System. Security. Cryptography. SHA1Managed|Všechny|
|System. Security. Cryptography. SHA256|Všechny|
|System. Security. Cryptography. SHA256Managed|Všechny|
|System. Security. Cryptography. SHA384|Všechny|
|System. Security. Cryptography. SHA384Managed|Všechny|
|System. Security. Cryptography. SHA512|Všechny|
|System. Security. Cryptography. SHA512Managed|Všechny|
|System. Security. Cryptography. SymmetricAlgorithm|Všechny|
|System. Security. Cryptography. X509Certificates. PublicKey|Všechny|
|System. Security. Cryptography. X509Certificates. RSACertificateExtensions|Všechny|
|System. Security. Cryptography. X509Certificates. X500DistinguishedName|Název|
|System. Security. Cryptography. X509Certificates. certifikátu x509|Všechny|
|System. Security. Cryptography. X509Certificates. X509Certificate2|Všechny|
|System. Security. Cryptography. X509Certificates. X509ContentType|Všechny|
|System. Security. Cryptography. X509Certificates. X509NameType|Všechny|
|System. Single|Všechny|
|System. String|Všechny|
|System. StringComparer|Všechny|
|System. StringComparison|Všechny|
|System. StringSplitOptions|Všechny|
|System. text. Encoding|Všechny|
|System. text. RegularExpressions. Capture|Index, délka, hodnota|
|System. text. RegularExpressions. Capture –Collection|Počet, položka|
|System. text. RegularExpressions. Group|Zachycení, úspěch|
|System. text. RegularExpressions. GroupCollection|Počet, položka|
|System. text. RegularExpressions. Match|Prázdné, skupiny, výsledek|
|System. text. RegularExpressions. Regex|(Konstruktor), neshoda, shoda, shoda, nahradit, zrušit řídicí, rozdělit|
|System. text. RegularExpressions. RegexOptions|Všechny|
|System. text. StringBuilder|Všechny|
|System. TimeSpan|Všechny|
|System. TimeZone|Všechny|
|System. TimeZoneInfo. AdjustmentRule|Všechny|
|System. TimeZoneInfo. TransitionTime|Všechny|
|System. TimeZoneInfo|Všechny|
|System. Tuple|Všechny|
|System. UInt16|Všechny|
|System. UInt32|Všechny|
|System. UInt64|Všechny|
|System. URI|Všechny|
|System. UriPartial|Všechny|
|System. XML. Linq. Extensions|Všechny|
|System. XML. Linq. XAttribute|Všechny|
|System. XML. Linq. XCData|Všechny|
|System. XML. Linq. XComment|Všechny|
|System. XML. Linq. XContainer|Všechny|
|System. XML. Linq. XDeclaration|Všechny|
|System. XML. Linq. XDocument|Vše, s výjimkou: Load|
|System. XML. Linq. XDocumentType|Všechny|
|System. XML. Linq. XElement|Všechny|
|System. XML. Linq. XName|Všechny|
|System. XML. Linq. XNamespace|Všechny|
|System. XML. Linq. XNode|Všechny|
|System. XML. Linq. XNodeDocumentOrderComparer|Všechny|
|System. XML. Linq. XNodeEqualityComparer|Všechny|
|System. XML. Linq. XObject|Všechny|
|System. XML. Linq. XProcessingInstruction|Všechny|
|System. XML. Linq. XText|Všechny|
|System. XML. XmlNodeType|Všechny|

## <a name="context-variable"></a><a name="ContextVariables"></a>Kontextová proměnná
Proměnná s názvem `context` je implicitně dostupná ve všech [výrazech](api-management-policy-expressions.md#Syntax)zásad. Jeho členové poskytují informace, které jsou `\request`užitečné pro. Všichni `context` členové jsou jen pro čtení.

|Kontextová proměnná|Povolené metody, vlastnosti a hodnoty parametrů|
|----------------------|-------------------------------------------------------|
|kontext|[Rozhraní API](#ref-context-api): [IApi](#ref-iapi)<br /><br /> [Nasazení](#ref-context-deployment)<br /><br /> Uplynulý: časový interval časového intervalu mezi hodnotou časového razítka a aktuálním časem<br /><br /> [LastError](#ref-context-lasterror)<br /><br /> [Operace](#ref-context-operation)<br /><br /> [Produktu](#ref-context-product)<br /><br /> [Request](#ref-context-request)<br /><br /> RequestId: GUID – jedinečný identifikátor žádosti<br /><br /> [Základě](#ref-context-response)<br /><br /> [Předplatné](#ref-context-subscription)<br /><br /> Časové razítko: hodnota DateTime – bod v čase, kdy byla přijata žádost<br /><br /> Trasování: bool – určuje, zda je trasování zapnuto nebo vypnuto <br /><br /> [Uživatel](#ref-context-user)<br /><br /> [Proměnné](#ref-context-variables): IReadOnlyDictionary<řetězec,> objektů<br /><br /> void trace (zpráva: String)|
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
|<a id="ref-imessagebody"></a>IMessageBody|As<T\>(preserveContent: bool = false): kde T: String, Byte [], JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Metody `context.Request.Body.As<T>` a `context.Response.Body.As<T>` se používají ke čtení těla zprávy požadavku a odpovědi v zadaném typu `T`. Ve výchozím nastavení používá metoda původní datový proud zprávy a vykresluje jej po jeho vrácení k dispozici. Chcete-li zabránit tomu, aby metoda pracovala na kopii toku textu, nastavte `preserveContent` parametr na. `true` Pokud si chcete prohlédnout příklad, přejděte [sem](api-management-transformation-policies.md#SetBody) .|
|<a id="ref-iurl"></a>IUrl|Hostitel: řetězec<br /><br /> Cesta: řetězec<br /><br /> Port: int<br /><br /> [Dotaz](#ref-iurl-query): IReadOnlyDictionary<řetězec, řetězec [] ><br /><br /> QueryString: řetězec<br /><br /> Schéma: řetězec|
|<a id="ref-iuseridentity"></a>IUserIdentity|ID: řetězec<br /><br /> Zprostředkovatel: řetězec|
|<a id="ref-isubscriptionkeyparameternames"></a>ISubscriptionKeyParameterNames|Záhlaví: řetězec<br /><br /> Dotaz: řetězec|
|<a id="ref-iurl-query"></a>String IUrl. Query. GetValueOrDefault (queryParameterName: String; defaultValue: String)|queryParameterName: řetězec<br /><br /> VýchozíHodnota: řetězec<br /><br /> Vrátí hodnoty parametrů dotazu oddělené čárkou nebo `defaultValue` , pokud nebyl nalezen parametr.|
|<a id="ref-context-variables"></a>Kontext T Variables. GetValueOrDefault<T\>(proměnná: String, VýchozíHodnota: T)|proměnná: řetězec<br /><br /> VýchozíHodnota: T<br /><br /> Vrátí přetypování hodnoty proměnné na `T` typ `defaultValue` nebo, pokud proměnná nebyla nalezena.<br /><br /> Tato metoda vyvolá výjimku, pokud zadaný typ neodpovídá skutečnému typu vrácené proměnné.|
|BasicAuthCredentials AsBasic (vstup: Tento řetězec)|vstup: řetězec<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu hlavičky autorizačního požadavku na ověření Basic protokolu HTTP, metoda vrátí objekt typu `BasicAuthCredentials`; Jinak metoda vrátí hodnotu null.|
|bool TryParseBasic (vstup: Tento řetězec, výsledek: out BasicAuthCredentials)|vstup: řetězec<br /><br /> výsledek: out BasicAuthCredentials<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu autorizace základního ověřování protokolu HTTP v hlavičce požadavku, metoda se vrátí `true` a parametr výsledku obsahuje hodnotu typu; `BasicAuthCredentials` v opačném případě `false`se metoda vrátí.|
|BasicAuthCredentials|Heslo: řetězec<br /><br /> UserId: String|
|JWT AsJwt (vstup: Tento řetězec)|vstup: řetězec<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu tokenu JWT, metoda vrátí objekt typu `Jwt`; v opačném případě `null`se metoda vrátí.|
|bool TryParseJwt (vstup: Tento řetězec, výsledek: odchozí JWT)|vstup: řetězec<br /><br /> výsledek: odchozí JWT<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu tokenu JWT, metoda vrátí `true` a výsledný parametr obsahuje hodnotu typu; `Jwt` v opačném případě `false`se metoda vrátí.|
|Token|Algoritmus: řetězec<br /><br /> Cílová skupina: IEnumerable<řetězec\><br /><br /> Deklarace identity: IReadOnlyDictionary<String, String [] ><br /><br /> ExpirationTime: DateTime?<br /><br /> ID: řetězec<br /><br /> Vystavitel: řetězec<br /><br /> IssuedAt: DateTime?<br /><br /> NotBefore: DateTime?<br /><br /> Předmět: řetězec<br /><br /> Typ: řetězec|
|Token JWT. Claims. GetValueOrDefault (deklarace identity: String, VýchozíHodnota: String)|deklarace identity: řetězec<br /><br /> VýchozíHodnota: řetězec<br /><br /> Vrátí hodnoty deklarace s oddělovači, `defaultValue` nebo pokud záhlaví není nalezeno.|
|Byte [] Encrypted (Input: this Byte []; ALG: String; Key: Byte [], IV: Byte [])|vstup – prostý text, který se má zašifrovat<br /><br />ALG – název algoritmu symetrického šifrování<br /><br />šifrovací klíč klíče<br /><br />IV – vektor inicializace<br /><br />Vrátí šifrovaný prostý text.|
|Byte [] Encrypted (vstup: this Byte [], ALG: System. Security. Cryptography. SymmetricAlgorithm)|vstup – prostý text, který se má zašifrovat<br /><br />ALG – šifrovací algoritmus<br /><br />Vrátí šifrovaný prostý text.|
|Byte [] Encrypted (Input: this Byte []; ALG: System. Security. Cryptography. SymmetricAlgorithm; Key: Byte [], IV: Byte [])|vstup – prostý text, který se má zašifrovat<br /><br />ALG – šifrovací algoritmus<br /><br />šifrovací klíč klíče<br /><br />IV – vektor inicializace<br /><br />Vrátí šifrovaný prostý text.|
|Byte [] dešifrovat (vstup: Tento bajt [], ALG: řetězec, klíč: Byte [], IV: Byte [])|text Input-šifrováním, který se má dešifrovat<br /><br />ALG – název algoritmu symetrického šifrování<br /><br />šifrovací klíč klíče<br /><br />IV – vektor inicializace<br /><br />Vrátí prostý text.|
|Byte [] dešifrovat (vstup: this Byte [], ALG: System. Security. Cryptography. SymmetricAlgorithm)|text Input-šifrováním, který se má dešifrovat<br /><br />ALG – šifrovací algoritmus<br /><br />Vrátí prostý text.|
|Byte [] dešifrovat (vstup: this Byte [], ALG: System. Security. Cryptography. SymmetricAlgorithm, Key: Byte [], IV: Byte [])|text Input-šifrováním, který se má dešifrovat<br /><br />ALG – šifrovací algoritmus<br /><br />šifrovací klíč klíče<br /><br />IV – vektor inicializace<br /><br />Vrátí prostý text.|
|bool VerifyNoRevocation (vstup: This System. Security. Cryptography. X509Certificates. X509Certificate2)|Provede ověření řetězu X. 509 bez kontroly stavu odvolání certifikátu.<br /><br />Input – objekt certifikátu<br /><br />Vrátí `true` , zda je ověření úspěšné; `false` Pokud ověření selhalo.|


## <a name="next-steps"></a>Další kroky

Další informace o práci se zásadami najdete v těchto tématech:

+ [Zásady v API Management](api-management-howto-policies.md)
+ [Transformační rozhraní API](transform-api.md)
+ [Odkaz na zásady](api-management-policy-reference.md) pro úplný seznam příkazů zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)
