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
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 8cfaacad4619bb06536d41e72b9da1eb9c160dc2
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163901"
---
# <a name="api-management-policy-expressions"></a>Výrazy zásad rozhraní API Management
Tento článek popisuje syntaxe výrazy zásad C# 7. Každý výraz má přístup k implicitně zadané [kontextu](api-management-policy-expressions.md#ContextVariables) proměnné a povolený [dílčí](api-management-policy-expressions.md#CLRTypes) typů rozhraní .NET Framework.

Další informace najdete tady:

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
  
|Typ CLR|Podporované členy|
|--------------|-----------------------|
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
|System.Collections.Generic.IReadOnlyCollection<T\>|Vše|
|System.Collections.Generic.IReadOnlyDictionary < TKey, TValue >|Vše|
|System.Collections.Generic.ISet < TKey, TValue >|Vše|
|System.Collections.Generic.KeyValuePair < TKey, TValue >|Klíč, hodnota|
|Třída System.Collections.Generic.List < TKey, TValue >|Vše|
|System.Collections.Generic.Queue < TKey, TValue >|Vše|
|System.Collections.Generic.Stack < TKey, TValue >|Vše|
|System.Convert|Vše|
|System.DateTime|Vše|
|System.DateTimeKind|Čas UTC|
|System.DateTimeOffset|Vše|
|System.Decimal|Vše|
|System.Double|Vše|
|System.Guid|Vše|
|System.IEnumerable < T\>|Vše|
|System.IEnumerator < T\>|Vše|
|System.Int16|Vše|
|System.Int32|Vše|
|System.Int64|Vše|
|System.Linq.Enumerable<T\>|Vše|
|System.Math|Vše|
|System.MidpointRounding|Vše|
|System.Net.WebUtility|Vše|
|System.Nullable<T\>|Vše|
|System.Random|Vše|
|System.SByte|Vše|
|System.Security.Cryptography. HMACSHA384|Vše|
|System.Security.Cryptography. HMACSHA512|Vše|
|System.Security.Cryptography.HashAlgorithm|Vše|
|System.Security.Cryptography.HMAC|Vše|
|System.Security.Cryptography.HMACMD5|Vše|
|System.Security.Cryptography.HMACSHA1|Vše|
|System.Security.Cryptography.HMACSHA256|Vše|
|System.Security.Cryptography.KeyedHashAlgorithm|Vše|
|System.Security.Cryptography.MD5|Vše|
|System.Security.Cryptography.RNGCryptoServiceProvider|Vše|
|System.Security.Cryptography.SHA1|Vše|
|System.Security.Cryptography.SHA1Managed|Vše|
|System.Security.Cryptography.SHA256|Vše|
|System.Security.Cryptography.SHA256Managed|Vše|
|System.Security.Cryptography.SHA384|Vše|
|System.Security.Cryptography.SHA384Managed|Vše|
|System.Security.Cryptography.SHA512|Vše|
|System.Security.Cryptography.SHA512Managed|Vše|
|System.Single|Vše|
|System.String|Vše|
|System.StringSplitOptions|Vše|
|System.Text.Encoding|Vše|
|System.Text.RegularExpressions.Capture|Hodnota indexu, délka,|
|System.Text.RegularExpressions.CaptureCollection|Počet položek|
|System.Text.RegularExpressions.Group|Zachycení, úspěch|
|System.Text.RegularExpressions.GroupCollection|Počet položek|
|System.Text.RegularExpressions.Match|Prázdná, skupiny, výsledek|
|System.Text.RegularExpressions.Regex|(Konstruktor) IsMatch, shoda, odpovídá, nahradí|
|System.Text.RegularExpressions.RegexOptions|Kompilaci, IgnoreCase, IgnorePatternWhitespace, Multiline, None, RightToLeft, Singleline|
|Hodnota System.TimeSpan|Vše|
|System.Tuple|Vše|
|System.UInt16|Vše|
|System.UInt32|Vše|
|System.UInt64|Vše|
|System.Uri|Vše|
|System.Xml.Linq.Extensions|Vše|
|System.Xml.Linq.XAttribute|Vše|
|System.Xml.Linq.XCData|Vše|
|System.Xml.Linq.XComment|Vše|
|System.Xml.Linq.XContainer|Vše|
|System.Xml.Linq.XDeclaration|Vše|
|System.Xml.Linq.XDocument|Vše|
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
|Kontext|Rozhraní API: IApi<br /><br /> Nasazení<br /><br /> Uplynulý: Časový interval - časový interval mezi hodnotou časového razítka a aktuální čas<br /><br /> LastError<br /><br /> Operace<br /><br /> Produkt<br /><br /> Žádost<br /><br /> ID žádosti: Identifikátor GUID - požadavek jedinečného identifikátoru<br /><br /> Odpověď<br /><br /> Předplatné<br /><br /> Časové razítko: Datum a čas - bodu v čase, kdy byl obdržen požadavek<br /><br /> Trasování: bool – označuje, pokud je trasování zapnuto nebo vypnuto <br /><br /> Uživatel<br /><br /> Proměnné: IReadOnlyDictionary < string, object ><br /><br /> void Trace(message: string)|
|context.Api|ID: řetězec<br /><br /> IsCurrentRevision: bool<br /><br />  Název: řetězec<br /><br /> Cesta: řetězec<br /><br /> Revize: řetězec<br /><br /> ServiceUrl: IUrl<br /><br /> Verze: řetězec |
|kontext. Nasazení|Oblast: řetězec<br /><br /> ServiceName: řetězec<br /><br /> Certifikáty: IReadOnlyDictionary < řetězec, X509Certificate2 >|
|kontext. Poslední chyba|Zdroj: řetězec<br /><br /> Důvod: řetězec<br /><br /> Zpráva: řetězec<br /><br /> Obor: řetězec<br /><br /> Část: řetězec<br /><br /> Cesta: řetězec<br /><br /> PolicyId: řetězec<br /><br /> Další informace o kontextu. LastError, naleznete v tématu [zpracování chyb](api-management-error-handling-policies.md).|
|kontext. Operace|ID: řetězec<br /><br /> Metoda: řetězec<br /><br /> Název: řetězec<br /><br /> UrlTemplate: řetězec|
|context.Product|Rozhraní API: IEnumerable < IApi\><br /><br /> ApprovalRequired: bool<br /><br /> Skupiny: IEnumerable < IGroup\><br /><br /> ID: řetězec<br /><br /> Název: řetězec<br /><br /> Stav: výčtu ProductState {NotPublished, publikováno}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|
|kontext. Požadavek|Text zprávy: IMessageBody<br /><br /> Certifikát: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Záhlaví: IReadOnlyDictionary < string, string [] ><br /><br /> IP adresa: řetězec<br /><br /> MatchedParameters: IReadOnlyDictionary < string, string ><br /><br /> Metoda: řetězec<br /><br /> OriginalUrl:IUrl<br /><br /> Adresa URL: IUrl|
|kontext řetězce. Request.Headers.GetValueOrDefault (Název_hlavičky: řetězec, výchozí hodnota: string)|Název_hlavičky: řetězec<br /><br /> Výchozí hodnota: řetězec<br /><br /> Vrátí hodnoty hlavičky požadavku oddělených čárkou nebo `defaultValue` Pokud hlavička nebyla nalezena.|
|kontext. Odpověď|Text zprávy: IMessageBody<br /><br /> Záhlaví: IReadOnlyDictionary < string, string [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: řetězec|
|kontext řetězce. Response.Headers.GetValueOrDefault (Název_hlavičky: řetězec, výchozí hodnota: string)|Název_hlavičky: řetězec<br /><br /> Výchozí hodnota: řetězec<br /><br /> Vrátí hodnoty hlavičky odpovědi oddělených čárkou nebo `defaultValue` Pokud hlavička nebyla nalezena.|
|kontext. Předplatné|Čas vytvoření: DateTime<br /><br /> Datum ukončení: Datum a čas?<br /><br /> ID: řetězec<br /><br /> Klíč: řetězec<br /><br /> Název: řetězec<br /><br /> PrimaryKey: řetězec<br /><br /> Sekundární klíč: řetězec<br /><br /> Datum zahájení: Datum a čas?|
|context.User|E-mailu: řetězec<br /><br /> Jméno: řetězec<br /><br /> Skupiny: IEnumerable < IGroup\><br /><br /> ID: řetězec<br /><br /> Identity: IEnumerable < IUserIdentity\><br /><br /> Příjmení: řetězec<br /><br /> Poznámka: řetězec<br /><br /> RegistrationDate: DateTime|
|IApi|ID: řetězec<br /><br /> Název: řetězec<br /><br /> Cesta: řetězec<br /><br /> Protokoly: IEnumerable < string\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|
|IGroup|ID: řetězec<br /><br /> Název: řetězec|
|IMessageBody|Jako < T\>(preserveContent: bool = false): Kde T: string, JObject, JToken, JArray, XNode, XElement a XDocument<br /><br /> `context.Request.Body.As<T>` a `context.Response.Body.As<T>` metody se používají ke čtení zpráv v zadaného typu žádostí a odpovědí `T`. Ve výchozím nastavení metoda používá původní datový proud obsahu zprávy a vykreslí není k dispozici po vrátí. Chcete-li tomu zabránili tak, že metoda pracovat na kopii této datový proud obsahu, nastavte `preserveContent` parametr `true`. Přejděte [tady](api-management-transformation-policies.md#SetBody) k prohlédnutí příkladu.|
|IUrl|Hostitel: řetězec<br /><br /> Cesta: řetězec<br /><br /> Port: int<br /><br /> Dotaz: IReadOnlyDictionary < string, string [] ><br /><br /> Řetězec dotazu: řetězec<br /><br /> Schéma: řetězec|
|IUserIdentity|ID: řetězec<br /><br /> Poskytovatel: řetězec|
|ISubscriptionKeyParameterNames|Záhlaví: řetězec<br /><br /> Dotaz: řetězec|
|řetězec IUrl.Query.GetValueOrDefault (queryParameterName: řetězec, výchozí hodnota: string)|queryParameterName: řetězec<br /><br /> Výchozí hodnota: řetězec<br /><br /> Vrátí hodnoty parametru dotazu oddělených čárkou nebo `defaultValue` Pokud parametr nebyl nalezen.|
|T kontextu. Variables.GetValueOrDefault < T\>(NázevProměnné: řetězec, výchozí hodnota: T)|NázevProměnné: řetězec<br /><br /> Výchozí hodnota: T<br /><br /> Vrátí hodnotu proměnné přetypován na typ `T` nebo `defaultValue` Pokud není nalezena proměnné.<br /><br /> Tato metoda vyvolá výjimku, pokud zadaný typ neodpovídá skutečné typ vrácený proměnné.|
|BasicAuthCredentials AsBasic(input: this string)|vstupu: řetězce<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu hlavičky autorizace požadavku HTTP Basic Authentication, vrátí metoda objekt typu `BasicAuthCredentials`; v opačném případě vrátí metoda hodnotu null.|
|BOOL TryParseBasic (vstup: Tento řetězec, výsledek: si BasicAuthCredentials)|vstupu: řetězce<br /><br /> výsledek: si BasicAuthCredentials<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu povolení základního ověřování protokolu HTTP v hlavičce žádosti vrátí metoda `true` a parametr výsledek obsahuje hodnotu typu `BasicAuthCredentials`; v opačném případě vrátí metoda `false`.|
|BasicAuthCredentials|Heslo: řetězec<br /><br /> ID uživatele: řetězec|
|Token Jwt AsJwt(input: this string)|vstupu: řetězce<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu tokenu JWT, vrátí metoda objekt typu `Jwt`; v opačném případě vrátí metoda `null`.|
|BOOL TryParseJwt (vstup: Tento řetězec, výsledek: na token Jwt)|vstupu: řetězce<br /><br /> výsledek: na token Jwt<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu tokenu JWT, metoda vrátí `true` a parametr výsledek obsahuje hodnotu typu `Jwt`; v opačném případě vrátí metoda `false`.|
|Token Jwt|Algoritmus: řetězec<br /><br /> Cílové skupiny: IEnumerable < string\><br /><br /> Deklarace identity: IReadOnlyDictionary < string, string [] ><br /><br /> ExpirationTime: Datum a čas?<br /><br /> ID: řetězec<br /><br /> Vystavitel: řetězec<br /><br /> Neplatí před: Datum a čas?<br /><br /> Předmět: řetězec<br /><br /> Typ: řetězec|
|řetězec Jwt.Claims.GetValueOrDefault (claimName: řetězec, výchozí hodnota: string)|claimName: řetězec<br /><br /> Výchozí hodnota: řetězec<br /><br /> Vrátí čárkami oddělených hodnot deklarací identity nebo `defaultValue` Pokud hlavička nebyla nalezena.|
|Byte [] zašifrovat (vstupní: Tento byte [], alg: řetězec, klíč: byte [], iv:byte[])|(vstup) – ve formátu prostého textu šifrování<br /><br />alg - název algoritmu symetrického šifrování<br /><br />klíč – šifrovacího klíče<br /><br />vektor IV - inicializační vektor<br /><br />Vrátí zašifrovaný prostý text.|
|Byte [] zašifrovat (vstupní: Tento byte [], alg: System.Security.Cryptography.SymmetricAlgorithm)|(vstup) – ve formátu prostého textu šifrování<br /><br />alg - šifrovací algoritmus<br /><br />Vrátí zašifrovaný prostý text.|
|Byte [] zašifrovat (vstupní: Tento byte [], alg: System.Security.Cryptography.SymmetricAlgorithm, klíč: byte [], iv:byte[])|(vstup) – ve formátu prostého textu šifrování<br /><br />alg - šifrovací algoritmus<br /><br />klíč – šifrovacího klíče<br /><br />vektor IV - inicializační vektor<br /><br />Vrátí zašifrovaný prostý text.|
|Byte [] dešifrovat (vstupní: Tento byte [], alg: řetězec, klíč: byte [], iv:byte[])|vstup - šifer text k dešifrování<br /><br />alg - název algoritmu symetrického šifrování<br /><br />klíč – šifrovacího klíče<br /><br />vektor IV - inicializační vektor<br /><br />Vrátí hodnotu ve formátu prostého textu.|
|Byte [] dešifrovat (vstupní: Tento byte [], alg: System.Security.Cryptography.SymmetricAlgorithm)|vstup - šifer text k dešifrování<br /><br />alg - šifrovací algoritmus<br /><br />Vrátí hodnotu ve formátu prostého textu.|
|Byte [] dešifrovat (vstupní: Tento byte [], alg: System.Security.Cryptography.SymmetricAlgorithm, klíč: byte [], iv:byte[])|text vstupu - vstupu - šifer k dešifrování<br /><br />alg - šifrovací algoritmus<br /><br />klíč – šifrovacího klíče<br /><br />vektor IV - inicializační vektor<br /><br />Vrátí hodnotu ve formátu prostého textu.|


## <a name="next-steps"></a>Další postup

Práce se zásadami pro další informace najdete v tématu:

+ [Zásady ve službě API Management](api-management-howto-policies.md)
+ [Transformujte rozhraní API](transform-api.md)
+ [Referenční příručce o zásadách](api-management-policy-reference.md) úplný seznam zásad příkazy a jejich nastavení
+ [Ukázky zásad](policy-samples.md)   
