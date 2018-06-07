---
title: Výrazy zásad Azure API Management | Microsoft Docs
description: Další informace o výrazy zásad v Azure API Management.
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
ms.openlocfilehash: 5943357bc421bbae0caef7f0acd7aa3364813826
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34597514"
---
# <a name="api-management-policy-expressions"></a>Výrazy zásad API Management
Tento článek popisuje zásady výrazy syntaxe je C# 7. Každý výraz má přístup k implicitně poskytnutého [kontextu](api-management-policy-expressions.md#ContextVariables) proměnné a povolení [podmnožina](api-management-policy-expressions.md#CLRTypes) typů rozhraní .NET Framework.  

Další informace najdete tady:

- Naleznete v části zadat informace o kontextu k službě back-end. Použití [nastavit parametr řetězce dotazu](api-management-transformation-policies.md#SetQueryStringParameter) a [hlavičky protokolu HTTP nastaven](api-management-transformation-policies.md#SetHTTPheader) zásady k poskytování těchto informací.
- V tématu Jak používat [ověření JWT](api-management-access-restriction-policies.md#ValidateJWT) u tokenu deklarací na základě zásad předem autorizace přístupu k operacím.   
- V tématu Jak používat [rozhraní API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) trasování, které chcete zobrazit, jak se vyhodnocují zásady a výsledky těchto hodnocení.  
- V tématu Jak používat výrazy s [získat z mezipaměti](api-management-caching-policies.md#GetFromCache) a [úložiště do mezipaměti](api-management-caching-policies.md#StoreToCache) zásady Konfigurace ukládání do mezipaměti odpovědi API Management. Nastavení doby trvání, který odpovídá odpovědi ukládání do mezipaměti back-end službu jako zadaný pomocí služby zálohování `Cache-Control` – direktiva.  
- Zjistit, jak provést filtrování obsahu. Odebrání datové prvky odpověď z back-end pomocí [řízení toku](api-management-advanced-policies.md#choose) a [tělo nastavit](api-management-transformation-policies.md#SetBody) zásady. 
- Si můžete stáhnout příkazy zásad [rozhraní api správy – ukázky nebo zásad](https://github.com/Azure/api-management-samples/tree/master/policies) úložiště github.  
  
  
##  <a name="Syntax"></a> Syntaxe  
 Jediný příkaz výrazy jsou uzavřené v `@(expression)`, kde `expression` je ve správném formátu prohlášení výrazu jazyka C#.  
  
 Výrazy vícepříkazové jsou uzavřené v `@{expression}`. Všechny cesty kódu v rámci vícepříkazové výrazy musí končit `return` příkaz.  
  
##  <a name="PolicyExpressionsExamples"></a> Příklady  
  
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
  
##  <a name="PolicyExpressionsUsage"></a> Využití  
 Výrazy lze použít jako hodnoty atributů nebo textové hodnoty v jakékoli API Management [zásady](api-management-policies.md) (Pokud je odkaz na zásady neurčí jinak).  
  
> [!IMPORTANT]
>  Pokud používáte výrazy zásad, je jenom omezené ověření výrazy zásad je definována zásada. Výrazy jsou spustit bránu za běhu, jakékoli výjimky generované zásad výrazy vést k chybě běhového prostředí.  
  
##  <a name="CLRTypes"></a> Povolené ve výrazech zásad typy rozhraní .NET framework  
 Následující tabulka uvádí typy rozhraní .NET Framework a jejich členové, které jsou povoleny ve výrazech zásad.  
  
|Typ CLR|Podporované metody|  
|--------------|-----------------------|  
|Newtonsoft.Json.Linq.Extensions|Jsou podporovány všechny metody|  
|Newtonsoft.Json.Linq.JArray|Jsou podporovány všechny metody|  
|Newtonsoft.Json.Linq.JConstructor|Jsou podporovány všechny metody|  
|Newtonsoft.Json.Linq.JContainer|Jsou podporovány všechny metody|  
|Newtonsoft.Json.Linq.JObject|Jsou podporovány všechny metody|  
|Newtonsoft.Json.Linq.JProperty|Jsou podporovány všechny metody|  
|Newtonsoft.Json.Linq.JRaw|Jsou podporovány všechny metody|  
|Newtonsoft.Json.Linq.JToken|Jsou podporovány všechny metody|  
|Newtonsoft.Json.Linq.JTokenType|Jsou podporovány všechny metody|  
|Newtonsoft.Json.Linq.JValue|Jsou podporovány všechny metody|  
|System.Collections.Generic.IReadOnlyCollection<T\>|Vše|  
|System.Collections.Generic.IReadOnlyDictionary < TKey, TValue >|Vše|  
|System.Collections.Generic.ISet < TKey, TValue >|Vše|  
|System.Collections.Generic.KeyValuePair < TKey, TValue >|Klíč, hodnota|  
|System.Collections.Generic.List < TKey, TValue >|Vše|  
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
|System.Linq.Enumerable<T\>|Jsou podporovány všechny metody|  
|System.Math|Vše|  
|System.MidpointRounding|Vše|  
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
|System.Text.RegularExpressions.Capture|Hodnotu indexu, délka,|  
|System.Text.RegularExpressions.CaptureCollection|Počet položek|  
|System.Text.RegularExpressions.Group|Zachycení úspěch|  
|System.Text.RegularExpressions.GroupCollection|Počet položek|  
|System.Text.RegularExpressions.Match|Prázdné, skupin, výsledek|  
|System.Text.RegularExpressions.Regex|(Konstruktor), IsMatch – shoda, odpovídá, nahradí|  
|System.Text.RegularExpressions.RegexOptions|Kompilovat, IgnoreCase, IgnorePatternWhitespace, víceřádkových, None, RightToLeft, Jednořákový|  
|System.TimeSpan|Vše|  
|System.Tuple|Vše|  
|System.UInt16|Vše|  
|System.UInt32|Vše|  
|System.UInt64|Vše|  
|System.Uri|Vše|  
|System.Xml.Linq.Extensions|Jsou podporovány všechny metody|  
|System.Xml.Linq.XAttribute|Jsou podporovány všechny metody|  
|System.Xml.Linq.XCData|Jsou podporovány všechny metody|  
|System.Xml.Linq.XComment|Jsou podporovány všechny metody|  
|System.Xml.Linq.XContainer|Jsou podporovány všechny metody|  
|System.Xml.Linq.XDeclaration|Jsou podporovány všechny metody|  
|System.Xml.Linq.XDocument|Jsou podporovány všechny metody|  
|System.Xml.Linq.XDocumentType|Jsou podporovány všechny metody|  
|System.Xml.Linq.XElement|Jsou podporovány všechny metody|  
|System.Xml.Linq.XName|Jsou podporovány všechny metody|  
|System.Xml.Linq.XNamespace|Jsou podporovány všechny metody|  
|System.Xml.Linq.XNode|Jsou podporovány všechny metody|  
|System.Xml.Linq.XNodeDocumentOrderComparer|Jsou podporovány všechny metody|  
|System.Xml.Linq.XNodeEqualityComparer|Jsou podporovány všechny metody|  
|System.Xml.Linq.XObject|Jsou podporovány všechny metody|  
|System.Xml.Linq.XProcessingInstruction|Jsou podporovány všechny metody|  
|System.Xml.Linq.XText|Jsou podporovány všechny metody|  
|System.Xml.XmlNodeType|Vše|  
  
##  <a name="ContextVariables"></a> Kontextové proměnné  
 Proměnné s názvem `context` je implicitně k dispozici v každé zásadě [výraz](api-management-policy-expressions.md#Syntax). Její členy poskytování důležitých informací `\request`. Všechny `context` členové jsou jen pro čtení.  
  
|Kontextové proměnné|Povolené metody, vlastnosti a hodnoty parametru|  
|----------------------|-------------------------------------------------------|  
|Kontext|Api: IApi<br /><br /> Nasazení<br /><br /> Uplynulá: Časový interval - časový interval mezi hodnotou časového razítka a aktuální čas<br /><br /> LastError<br /><br /> Operace<br /><br /> Produkt<br /><br /> Žádost<br /><br /> ID žádosti: Guid - požadavek jedinečný identifikátor<br /><br /> Odpověď<br /><br /> Předplatné<br /><br /> Časové razítko: Datum a čas - bodu v čase, kdy byl přijat požadavek<br /><br /> Trasování: bool – označuje, pokud je trasování zapnuto nebo vypnuto <br /><br /> Uživatel<br /><br /> Proměnné: IReadOnlyDictionary < string, object ><br /><br /> void Trace(message: string)|  
|context.Api|ID: řetězec<br /><br /> IsCurrentRevision: bool<br /><br />  Název: řetězec<br /><br /> Cesta: řetězec<br /><br /> Revize: řetězec<br /><br /> ServiceUrl: IUrl<br /><br /> Verze: řetězec |  
|kontext. Nasazení|Oblast: řetězec<br /><br /> ServiceName: řetězec<br /><br /> Certifikáty: IReadOnlyDictionary < řetězec, X509Certificate2 >|  
|kontext. Poslední chyba|Zdroj: řetězec<br /><br /> Důvod: řetězec<br /><br /> Zpráva: řetězec<br /><br /> Obor: řetězec<br /><br /> Část: řetězec<br /><br /> Cesta: řetězec<br /><br /> PolicyId: řetězec<br /><br /> Další informace o kontextu. Poslední chyba, najdete v části [zpracování chyb](api-management-error-handling-policies.md).|  
|kontext. Operace|ID: řetězec<br /><br /> Metoda: řetězec<br /><br /> Název: řetězec<br /><br /> UrlTemplate: řetězec|  
|context.Product|Rozhraní API: IEnumerable < IApi\><br /><br /> ApprovalRequired: bool<br /><br /> Skupiny: Rozhraní IEnumerable < IGroup\><br /><br /> ID: řetězec<br /><br /> Název: řetězec<br /><br /> Stav: výčtu ProductState {NotPublished, publikováno}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|  
|kontext. Požadavek|Text: IMessageBody<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Hlavičky: IReadOnlyDictionary < řetězec, řetězec [] ><br /><br /> IP adresa: řetězec<br /><br /> MatchedParameters: IReadOnlyDictionary < řetězec, řetězec ><br /><br /> Metoda: řetězec<br /><br /> OriginalUrl:IUrl<br /><br /> Adresa URL: IUrl|  
|řetězec kontext. Request.Headers.GetValueOrDefault (headerName: string, výchozí hodnota: string)|headerName: řetězec<br /><br /> Výchozí hodnota: string<br /><br /> Vrátí hodnoty hlavičky oddělené čárkami žádost nebo `defaultValue` Pokud hlavička nebyla nalezena.|  
|kontext. Odpověď|Text: IMessageBody<br /><br /> Hlavičky: IReadOnlyDictionary < řetězec, řetězec [] ><br /><br /> StatusCode: int<br /><br /> StatusReason: řetězec|  
|řetězec kontext. Response.Headers.GetValueOrDefault (headerName: string, výchozí hodnota: string)|headerName: řetězec<br /><br /> Výchozí hodnota: string<br /><br /> Vrátí textový soubor s oddělovači odpověď hodnoty hlavičky nebo `defaultValue` Pokud hlavička nebyla nalezena.|  
|kontext. Předplatné|CreatedTime: data a času<br /><br /> Koncové datum: data a času?<br /><br /> ID: řetězec<br /><br /> Klíč: řetězec<br /><br /> Název: řetězec<br /><br /> PrimaryKey: řetězec<br /><br /> Sekundární klíč: řetězec<br /><br /> Počátečním: data a času?|  
|context.User|E-mailu: řetězec<br /><br /> FirstName: řetězec<br /><br /> Skupiny: Rozhraní IEnumerable < IGroup\><br /><br /> ID: řetězec<br /><br /> Identity: Rozhraní IEnumerable < IUserIdentity\><br /><br /> Příjmení: řetězec<br /><br /> Poznámka: řetězec<br /><br /> RegistrationDate: data a času|  
|IApi|ID: řetězec<br /><br /> Název: řetězec<br /><br /> Cesta: řetězec<br /><br /> Protokoly: Rozhraní IEnumerable < řetězec\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|  
|IGroup|ID: řetězec<br /><br /> Název: řetězec|  
|IMessageBody|Jako < T\>(preserveContent: bool = false): kde T: řetězec, JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> `context.Request.Body.As<T>` a `context.Response.Body.As<T>` metody se používají ke čtení požadavku a odpovědi těla zprávy v zadaného typu `T`. Ve výchozím nastavení metoda používá původní datový proud obsahu zprávy a vykreslí není k dispozici po vrátí. Chcete-li se vyhnout, která metoda pracovat na kopii datový proud obsahu, nastavte `preserveContent` parametru `true`. Přejděte [zde](api-management-transformation-policies.md#SetBody) zobrazíte příklad.|  
|IUrl|Hostitele: řetězec<br /><br /> Cesta: řetězec<br /><br /> Port: int<br /><br /> Dotaz: IReadOnlyDictionary < řetězec, řetězec [] ><br /><br /> Řetězec dotazu: řetězec<br /><br /> Schéma: řetězec|  
|IUserIdentity|ID: řetězec<br /><br /> Zprostředkovatel: řetězec|  
|ISubscriptionKeyParameterNames|Záhlaví: řetězec<br /><br /> Dotaz: řetězec|  
|řetězec IUrl.Query.GetValueOrDefault (queryParameterName: string, výchozí hodnota: string)|queryParameterName: řetězec<br /><br /> Výchozí hodnota: string<br /><br /> Vrátí hodnoty parametru dotazu textový soubor s oddělovači nebo `defaultValue` Pokud není nalezen parametr.|  
|Kontext T. Variables.GetValueOrDefault < T\>(NázevProměnné: string, výchozí hodnota: T)|NázevProměnné: řetězec<br /><br /> Výchozí hodnota: T<br /><br /> Vrátí hodnotu proměnné přetypovat na typ `T` nebo `defaultValue` Pokud není nalezena proměnná.<br /><br /> Tato metoda vyvolá výjimku, pokud zadaný typ neodpovídá skutečný typ vrácený proměnné.|  
|BasicAuthCredentials AsBasic(input: this string)|vstupní: řetězec<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu záhlaví autorizace požadavku HTTP základní ověřování, metoda vrátí objekt typu `BasicAuthCredentials`; v opačném případě vrátí metoda hodnotu null.|  
|BOOL TryParseBasic (vstup: Tento řetězec, výsledek: out BasicAuthCredentials)|vstupní: řetězec<br /><br /> výsledek: out BasicAuthCredentials<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu autorizace základní ověřování protokolu HTTP v hlavičce požadavku, vrátí metoda `true` a parametr výsledek obsahuje hodnotu typu `BasicAuthCredentials`; v opačném případě vrátí metodu `false`.|  
|BasicAuthCredentials|Heslo: řetězec<br /><br /> ID uživatele: řetězec|  
|Jwt AsJwt(input: this string)|vstupní: řetězec<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu tokenu JWT, metoda vrátí objekt typu `Jwt`; v opačném případě vrátí metodu `null`.|  
|BOOL TryParseJwt (vstup: Tento řetězec, výsledek: out Jwt)|vstupní: řetězec<br /><br /> výsledek: out Jwt<br /><br /> Pokud vstupní parametr obsahuje platnou hodnotu tokenu JWT, vrátí metoda `true` a parametr výsledek obsahuje hodnotu typu `Jwt`; v opačném případě vrátí metodu `false`.|  
|Token Jwt|Algoritmus: řetězec<br /><br /> Cílová skupina: IEnumerable < řetězec\><br /><br /> Deklarace identity: IReadOnlyDictionary < řetězec, řetězec [] ><br /><br /> ExpirationTime: data a času?<br /><br /> ID: řetězec<br /><br /> Vystavitel: řetězec<br /><br /> Neplatí před: data a času?<br /><br /> Předmět: řetězec<br /><br /> Typ: řetězec|  
|řetězec Jwt.Claims.GetValueOrDefault (claimName: string, výchozí hodnota: string)|claimName: řetězec<br /><br /> Výchozí hodnota: string<br /><br /> Vrátí textový soubor s oddělovači hodnot deklarací identity nebo `defaultValue` Pokud hlavička nebyla nalezena.|
|Byte [] šifrování (vstupní: Tento byte [], alg: řetězec, klíč: byte [], iv:byte[])|(vstup) – ve formátu prostého textu k zašifrování<br /><br />alg - název algoritmu symetrického šifrování<br /><br />klíč – šifrovací klíč<br /><br />IV - inicializační vektor<br /><br />Vrátí zašifrovaný prostý text.|
|Byte [] šifrování (vstupní: Tento byte [], alg: System.Security.Cryptography.SymmetricAlgorithm)|(vstup) – ve formátu prostého textu k zašifrování<br /><br />alg - šifrovacího algoritmu.<br /><br />Vrátí zašifrovaný prostý text.|
|Byte [] šifrování (vstupní: Tento byte [], alg: System.Security.Cryptography.SymmetricAlgorithm klíč: byte [], iv:byte[])|(vstup) – ve formátu prostého textu k zašifrování<br /><br />alg - šifrovacího algoritmu.<br /><br />klíč – šifrovací klíč<br /><br />IV - inicializační vektor<br /><br />Vrátí zašifrovaný prostý text.|
|Byte [] dešifrování (vstupní: Tento byte [], alg: řetězec, klíč: byte [], iv:byte[])|vstup - šifrováním text k dešifrování<br /><br />alg - název algoritmu symetrického šifrování<br /><br />klíč – šifrovací klíč<br /><br />IV - inicializační vektor<br /><br />Vrátí hodnotu ve formátu prostého textu.|
|Byte [] dešifrování (vstupní: Tento byte [], alg: System.Security.Cryptography.SymmetricAlgorithm)|vstup - šifrováním text k dešifrování<br /><br />alg - šifrovacího algoritmu.<br /><br />Vrátí hodnotu ve formátu prostého textu.|
|Byte [] dešifrování (vstupní: Tento byte [], alg: System.Security.Cryptography.SymmetricAlgorithm klíč: byte [], iv:byte[])|vstupní šifrováním - vstupní - text k dešifrování<br /><br />alg - šifrovacího algoritmu.<br /><br />klíč – šifrovací klíč<br /><br />IV - inicializační vektor<br /><br />Vrátí hodnotu ve formátu prostého textu.|


## <a name="next-steps"></a>Další postup

Práce se zásadami pro další informace najdete v tématu:

+ [Zásady ve službě API Management](api-management-howto-policies.md)
+ [Transformuje rozhraní API](transform-api.md)
+ [Referenční informace o zásadách](api-management-policy-reference.md) pro úplný seznam příkazy zásad a jejich nastavení
+ [Ukázky zásad](policy-samples.md)   
