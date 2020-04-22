---
title: Ověření přístupu k Centru událostí Azure pomocí sdílených přístupových podpisů
description: Tento článek ukazuje, jak ověřit přístup k prostředkům Centra událostí pomocí sdílených přístupových podpisů.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: spelluru
ms.openlocfilehash: cde5992355d274410bb43b1e3e60fbba1afe4e44
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676357"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>Ověření přístupu k prostředkům Centra událostí pomocí sdílených přístupových podpisů (SAS)
Sdílený přístupový podpis (SAS) umožňuje podrobnou kontrolu nad typem přístupu, který udělíte klientům, kteří mají sdílený přístupový podpis. Zde jsou některé ovládací prvky, které můžete nastavit v SAS: 

- Interval, ve kterém je platný SAS, včetně počáteční čas a čas vypršení platnosti.
- Oprávnění udělená SAS. Například SAS pro obor názvů Event Hubs může udělit oprávnění k naslouchání, ale ne oprávnění k odeslání.
- Data mohou do centra událostí odesílat pouze klienti, kteří představují platná pověření.
- Klient se nemůže vydávat za jiného klienta.
- Neautorizovaný klient může být blokován odesílání dat do centra událostí.

Tento článek popisuje ověřování přístupu k prostředkům Centra událostí pomocí SAS. Informace o **autorizaci přístupu** k prostředkům Centra událostí pomocí SAS naleznete v [tomto článku](authorize-access-shared-access-signature.md). 

> [!NOTE]
> Společnost Microsoft doporučuje používat přihlašovací údaje Azure AD, pokud je to možné jako osvědčený postup zabezpečení, spíše než pomocí sdílených přístupových podpisů, které mohou být snadněji ohroženy. Zatímco můžete i nadále používat sdílené přístupové podpisy (SAS) k udělení jemně odstupňovaného přístupu k prostředkům centra událostí, Azure AD nabízí podobné funkce bez nutnosti spravovat tokeny SAS nebo se obávat zrušení ohroženého SAS.
> 
> Další informace o integraci Azure AD v Azure Event Hubs najdete v [tématu Autorizace přístupu k event hubům pomocí Azure AD](authorize-access-azure-active-directory.md). 


## <a name="configuring-for-sas-authentication"></a>Konfigurace ověřování SAS
Pravidlo autorizace sdíleného přístupu EventHubs můžete nakonfigurovat v oboru názvů Event Hubs nebo v entitě (instance centra událostí nebo téma Kafka v centru událostí). Konfigurace pravidla autorizace sdíleného přístupu pro skupinu spotřebitelů není aktuálně podporována, ale můžete použít pravidla nakonfigurovaná v oboru názvů nebo entitě k zabezpečení přístupu ke skupině spotřebitelů. 

Následující obrázek znázorňuje, jak platí pravidla autorizace na ukázkové entity. 

![Konfigurace pravidla autorizace](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

V tomto příkladu ukázkový obor názvů Event Hubs (ExampleNamespace) má dvě entity: eh1 a topic1. Autorizační pravidla jsou definována jak na úrovni entity, tak na úrovni oboru názvů.  

Pravidla autorizace manageRuleNS, sendRuleNS a listenRuleNS platí pro jak instanci centra událostí eh1, tak pro téma t1. Pravidla autorizace listenRule-eh a sendRule-eh se vztahují pouze na instanci centra událostí eh1 a pravidlo autorizace sendRuleT platí pouze pro téma 1. 

Při použití pravidla autorizace sendRuleNS mohou klientské aplikace odesílat do eh1 i topic1. Při odesíláníRuleT autorizační pravidlo se používá, vynucuje podrobný přístup k topic1 pouze, a proto klientské aplikace pomocí tohoto pravidla pro přístup nyní nelze odeslat eh1, ale pouze topic1.

## <a name="generate-a-shared-access-signature-token"></a>Generovat token sdíleného přístupového podpisu 
Každý klient, který má přístup k názvu autorizačního pravidla a jednomu z jeho podpisových klíčů, může vygenerovat token SAS. Token je generován crafting řetězec v následujícím formátu:

- `se`– Okamžik vypršení platnosti tokenu. Celé číslo odrážející sekundy od epochy 00:00:00 UTC 1.
- `skn`– Název autorizačního pravidla, tedy název klíče SAS.
- `sr`– Identifikátor URI prostředku, ke který se přistupuje.
- `sig`– Podpis.

Řetězec podpisu je algoritmus hash SHA-256 vypočítaný přes identifikátor URI prostředku (obor popsaný v předchozí části) a řetězcovou reprezentaci okamžité vypršení platnosti tokenu oddělené crlf.

Výpočthas vypadá podobně jako následující pseudo kód a vrátí hodnotu hash 256-bit/32 bajtů. 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Token obsahuje hodnoty bez hodnoty hash tak, aby příjemce můžete přepočítat hash se stejnými parametry, ověření, že vystavitel je držitelem platného podpisového klíče.

Identifikátor URI prostředku je úplný identifikátor URI prostředku služby Service Bus, ke kterému je nárokován přístup. Například http://<namespace><entityPath> .servicebus.windows.net/ `sb://<namespace>.servicebus.windows.net/<entityPath>;` nebo `http://contoso.servicebus.windows.net/eventhubs/eh1`tom je .

Identifikátor URI musí být kódován procentuální.

Pravidlo autorizace sdíleného přístupu používané pro podepisování musí být nakonfigurováno na entitě určené tímto identifikátorem URI nebo u jedné z jejích hierarchických nadřazených položek. Například `http://contoso.servicebus.windows.net/eventhubs/eh1` nebo `http://contoso.servicebus.windows.net` v předchozím příkladu.

Token SAS je platný pro všechny <resourceURI> prostředky s předponou s použitým v řetězci podpisu.

> [!NOTE]
> Vygenerujete přístupový token pro centra událostí pomocí zásad sdíleného přístupu. Další informace naleznete v [tématu Zásady autorizace sdíleného přístupu](authorize-access-shared-access-signature.md#shared-access-authorization-policies).

### <a name="generating-a-signaturetoken-from-a-policy"></a>Generování podpisu(tokenu) ze zásady 
Následující část ukazuje generování tokenu SAS pomocí zásad y podpisu sdíleného přístupu,

#### <a name="nodejs"></a>NodeJS

```javascript
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
```

#### <a name="java"></a>Java

```java
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```
#### <a name="php"></a>PHP

```php
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
    $targetUri = strtolower(rawurlencode(strtolower($uri))); 
    $expires = time();  
    $expiresInMins = 60; 
    $week = 60*60*24*7;
    $expires = $expires + $week; 
    $toSign = $targetUri . "\n" . $expires; 
    $signature = rawurlencode(base64_encode(hash_hmac('sha256',             
     $toSign, $sasKeyValue, TRUE))); 
    
    $token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
    return $token; 
}
```

#### <a name="c"></a>C#

```csharp
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="authenticating-event-hubs-publishers-with-sas"></a>Ověřování vydavatelů event hubů pomocí SAS 
Vydavatel události definuje virtuální koncový bod pro centrum událostí. Vydavatele lze použít pouze k odesílání zpráv do centra událostí a nepřijímat zprávy.

Centrum událostí obvykle zaměstnává jednoho vydavatele na klienta. Všechny zprávy, které jsou odesílány některému z vydavatelů centra událostí, jsou zařazeny do fronty v rámci tohoto centra událostí. Vydavatelé povolují jemně odstupňované řízení přístupu.

Každému klientovi event hubů je přiřazen jedinečný token, který se nahraje do klienta. Tokeny jsou vytvořeny tak, že každý jedinečný token uděluje přístup k jinému jedinečnému vydavateli. Klient, který obsahuje token, může odeslat pouze jednomu vydavateli a žádnému jinému vydavateli. Pokud více klientů sdílí stejný token, každý z nich sdílí vydavatele.

Všechny tokeny jsou přiřazeny pomocí klíčů SAS. Obvykle jsou všechny tokeny podepsány stejným klíčem. Klienti nejsou vědomi klíče, který brání klientům ve výrobě tokenů. Klienti pracují na stejných tokenech, dokud nevyprší jejich platnost.

Chcete-li například definovat autorizační pravidla s rozsahem pouze na odesílání a publikování do centra událostí, musíte definovat pravidlo autorizace odesílání. To lze provést na úrovni oboru názvů nebo dát podrobnější rozsah pro konkrétní entitu (instance centra událostí nebo téma). Klient nebo aplikace, která je vymezena s takovým podrobným přístupem se nazývá vydavatel Centra událostí. Postup je následující:

1. Vytvořte klíč SAS pro entitu, kterou chcete publikovat, abyste mu přiřadili obor **odeslání.** Další informace naleznete v [tématu Zásady autorizace sdíleného přístupu](authorize-access-shared-access-signature.md#shared-access-authorization-policies).
2. Vygenerujte token SAS s časem vypršení platnosti pro konkrétního vydavatele pomocí klíče generovaného v kroku 1.

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. Zadejte token klientovi vydavatele, který může odeslat pouze entitě a vydavateli, ke kterému token uděluje přístup.

    Po vypršení platnosti tokenu klient ztratí přístup k odeslání nebo publikování do entity. 


> [!NOTE]
> I když se to nedoporučuje, je možné vybavit zařízení tokeny, které udělují přístup k centru událostí nebo oboru názvů. Jakékoli zařízení, které obsahuje tento token můžete odesílat zprávy přímo do centra událostí. Kromě toho zařízení nemůže být na černé listině z odesílání do tohoto centra událostí.
> 
> Vždy se doporučuje poskytnout specifické a podrobné rozsahy.

> [!IMPORTANT]
> Po vytvoření tokeny, každý klient je zřízena s vlastním jedinečnýtoken.
>
> Když klient odešle data do centra událostí, označí jeho požadavek tokenem. Chcete-li zabránit útočníkovi v odposlouchávání a krádeži tokenu, musí dojít ke komunikaci mezi klientem a centrem událostí prostředkem šifrovaného kanálu.
> 
> Pokud útočník ukradne token, může se zosobnit klienta, jehož token byl odcizen. Zařazení na černou listinu vydavatele vykreslí tohoto klienta jako nepoužitelný, dokud neobdrží nový token, který používá jiného vydavatele.


## <a name="authenticating-event-hubs-consumers-with-sas"></a>Ověřování spotřebitelů event hubů pomocí SAS 
K ověření back-endových aplikací, které spotřebovávají z dat generovaných výrobci event hubů, vyžaduje ověřování tokenů event hubů, aby jeho klienti měli buď práva **ke správě,** nebo oprávnění **pro naslouchání** přiřazená k oboru názvů Event Hubs nebo instancím nebo tématu centra událostí. Data se spotřebovávají z centra událostí pomocí skupin spotřebitelů. Zatímco zásady SAS poskytuje podrobný rozsah, tento obor je definován pouze na úrovni entity a nikoli na úrovni příjemce. Znamená to, že oprávnění definovaná na úrovni oboru názvů nebo instance centra událostí nebo na úrovni tématu budou použita pro skupiny spotřebitelů této entity.

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Autorizace pomocí SAS](authenticate-shared-access-signature.md)
- [Autorizace pomocí řízení přístupu základu role (RBAC)](authenticate-shared-access-signature.md)
- [Další informace o centrech událostí](event-hubs-about.md)

Podívejte se na následující související články:

- [Ověřování požadavků na Centra událostí Azure z aplikace pomocí Služby Azure Active Directory](authenticate-application.md)
- [Ověření spravované identity pomocí Služby Azure Active Directory pro přístup k prostředkům centra událostí](authenticate-managed-identity.md)
- [Autorizace přístupu k prostředkům Centra událostí pomocí Služby Azure Active Directory](authorize-access-azure-active-directory.md)
- [Autorizace přístupu k prostředkům Centra událostí pomocí sdílených přístupových podpisů](authorize-access-shared-access-signature.md)
