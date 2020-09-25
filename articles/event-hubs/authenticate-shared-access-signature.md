---
title: Ověření přístupu k Azure Event Hubs se signaturami sdíleného přístupu
description: V tomto článku se dozvíte, jak ověřit přístup k prostředkům Event Hubs pomocí sdílených přístupových podpisů.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: f0cdf37963e40d871ad1079e9ccd5d0eb61fa2c0
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91270096"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>Ověřování přístupu k prostředkům Event Hubs pomocí sdílených přístupových podpisů (SAS)
Sdílený přístupový podpis (SAS) poskytuje podrobnější kontrolu nad typem přístupu, který udělíte klientům, kteří mají sdílený přístupový podpis. Tady jsou některé ovládací prvky, které můžete nastavit v SAS: 

- Interval, po který je SAS platný, včetně času zahájení a vypršení platnosti.
- Oprávnění udělená SAS. Například SAS pro obor názvů Event Hubs může udělit oprávnění k naslouchání, ale ne oprávnění Odeslat.
- Do centra událostí mohou odesílat data pouze klienti, kteří jsou přítomni platná pověření.
- Klient nemůže zosobnit jiného klienta.
- Neoprávněnému klientovi se může zablokovat odesílání dat do centra událostí.

Tento článek popisuje ověření přístupu k prostředkům Event Hubs pomocí SAS. Další informace o **autorizaci** přístupu k prostředkům Event HUBS pomocí SAS najdete v [tomto článku](authorize-access-shared-access-signature.md). 

> [!NOTE]
> Microsoft doporučuje používat přihlašovací údaje Azure AD, pokud je to možné, a ne používat sdílené přístupové podpisy, které můžou být snáze ohrožené. I když můžete nadále používat sdílené přístupové podpisy (SAS) pro udělení jemně odstupňovaného přístupu k prostředkům Event Hubs, Azure AD nabízí podobné možnosti bez nutnosti spravovat tokeny SAS nebo se starat o odvolání napadeného SAS.
> 
> Další informace o integraci Azure AD v Azure Event Hubs najdete v tématu [autorizace přístupu k Event Hubs pomocí Azure AD](authorize-access-azure-active-directory.md). 


## <a name="configuring-for-sas-authentication"></a>Konfigurace pro ověřování SAS
Autorizační pravidlo sdíleného přístupu EventHubs můžete nakonfigurovat pro obor názvů Event Hubs, nebo entitu (instance centra událostí nebo Kafka v centru událostí). Konfigurace autorizačního pravidla sdíleného přístupu není v současnosti podporovaná pro skupinu uživatelů, ale k zabezpečení přístupu ke skupině uživatelů můžete použít pravidla konfigurovaná pro obor názvů nebo entitu. 

Následující obrázek ukazuje, jak se pravidla autorizace vztahují k ukázkovým entitám. 

![Konfigurovat autorizační pravidlo](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

V tomto příkladu má ukázkový Event Hubs obor názvů (ExampleNamespace) dvě entity: EH1 a topic1. Autorizační pravidla jsou definována na úrovni entity a také na úrovni oboru názvů.  

Autorizační pravidla manageRuleNS, sendRuleNS a listenRuleNS se vztahují na instanci centra událostí EH1 i pro téma T1. Autorizační pravidla listenRule-EH a sendRule-eh se vztahují pouze na instance centra událostí EH1 a pravidlo autorizace sendRuleT se vztahuje pouze na téma topic1. 

Při použití autorizačního pravidla sendRuleNS můžou klientské aplikace posílat do EH1 i topic1. Při použití autorizačního pravidla sendRuleT vynutilo podrobný přístup pouze k topic1, takže klientské aplikace používající toto pravidlo pro přístup teď nemohou odesílat do EH1, ale pouze do topic1.

## <a name="generate-a-shared-access-signature-token"></a>Generování tokenu sdíleného přístupového podpisu 
Každý klient, který má přístup k názvu autorizačního pravidla a jeden z jeho podpisových klíčů, může generovat token SAS. Token je vygenerován vytvořením řetězce v následujícím formátu:

- `se`  – Okamžité vypršení platnosti tokenu Celé číslo odrážející sekundy od epocha 00:00:00 UTC dne 1. ledna 1970 (UNIX epocha) po vypršení platnosti tokenu
- `skn` – Název autorizačního pravidla, které je název klíče SAS.
- `sr` – Identifikátor URI přistupované prostředku.
- `sig` Označení.

Řetězec signatury je hodnota hash SHA-256 vypočítaná přes identifikátor URI prostředku (rozsah jak je popsáno v předchozí části) a řetězcové vyjádření konce platnosti tokenu, které je oddělené znakem CRLF.

Výpočet hodnoty hash se podobá následujícímu pseudo kódu a vrací hodnotu hash s 256 bity a 32 bajty. 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Token obsahuje hodnoty, které nejsou hash, aby příjemce mohl znovu vypočítat hodnotu hash se stejnými parametry a ověřit, zda má Vystavitel k dispozici platný podpisový klíč.

Identifikátor URI prostředku je úplný identifikátor URI Service Bus prostředku, ke kterému je nárok na přístup. Například http:// <namespace> . ServiceBus.Windows.NET/ <entityPath> nebo `sb://<namespace>.servicebus.windows.net/<entityPath>;` to znamená `http://contoso.servicebus.windows.net/eventhubs/eh1` .

Identifikátor URI musí být kódovaný v procentech.

Autorizační pravidlo sdíleného přístupu použité pro podepisování musí být nakonfigurováno pro entitu určenou tímto identifikátorem URI nebo jedním z jeho hierarchických nadřazených prvků. Například `http://contoso.servicebus.windows.net/eventhubs/eh1` nebo `http://contoso.servicebus.windows.net` v předchozím příkladu.

Token SAS je platný pro všechny prostředky s předponou <resourceURI> použitou v řetězci signatury.

> [!NOTE]
> Vygenerujete přístupový token pro Event Hubs pomocí zásad sdíleného přístupu. Další informace najdete v tématu [zásady autorizace sdíleného přístupu](authorize-access-shared-access-signature.md#shared-access-authorization-policies).

### <a name="generating-a-signaturetoken-from-a-policy"></a>Generování signatury (tokenu) ze zásady 
V následující části se dozvíte, jak vygenerovat token SAS pomocí zásad sdíleného přístupového podpisu.

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

#### <a name="java"></a>Kompilátor

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

## <a name="authenticating-event-hubs-publishers-with-sas"></a>Ověřování Event Hubs vydavatelů pomocí SAS 
Vydavatel události definuje virtuální koncový bod pro centrum událostí. Vydavatel se dá použít jenom k posílání zpráv do centra událostí a nepřijímá zprávy.

Centrum událostí obvykle využívá jednoho vydavatele na každého klienta. Všechny zprávy, které jsou odeslány všem vydavatelům centra událostí, jsou zařazeny do fronty v rámci tohoto centra událostí. Vydavatelé umožňují jemně odstupňované řízení přístupu.

Každému klientovi Event Hubs je přiřazen jedinečný token, který se nahraje do klienta. Tokeny jsou vytvořeny tak, že každý jedinečný token udělí přístup jinému jedinečnému vydavateli. Klient, který obsahuje token, lze odeslat pouze jednomu vydavateli a žádnému vydavateli. Pokud stejný token sdílí více klientů, pak každý z nich sdílí vydavatele.

Všechny tokeny jsou přiřazeny k klíčům SAS. Všechny tokeny jsou obvykle podepsány stejným klíčem. Klienti si nevědí, že se jedná o klíč, který brání klientům ze zpracovatelských tokenů. Klienti pracují se stejnými tokeny, dokud nevyprší jejich platnost.

Pokud chcete například definovat autorizační pravidla s rozsahem, aby se odesílaly a publikují jenom Event Hubs, musíte definovat autorizační pravidlo pro odeslání. To se dá udělat na úrovni oboru názvů nebo podrobnější rozsah konkrétní entity (instance centra událostí nebo téma). Je volán klient nebo aplikace s tímto detailním přístupem, Event Hubs Vydavatel. Postup je následující:

1. Vytvořte klíč SAS na entitě, kterou chcete publikovat, a přiřaďte obor pro **odeslání** . Další informace najdete v tématu [zásady autorizace sdíleného přístupu](authorize-access-shared-access-signature.md#shared-access-authorization-policies).
2. Vygenerujte token SAS s časem vypršení platnosti konkrétního vydavatele pomocí klíče vygenerovaného v Krok 1.

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. Poskytněte token pro klienta vydavatele, který může odeslat pouze entitě a vydavateli, ke kterému token udělí přístup.

    Po vypršení platnosti tokenu ztratí klient svůj přístup k odeslání nebo publikování do entity. 


> [!NOTE]
> I když se to nedoporučuje, je možné zařízení s tokeny, která udělují přístup k centru událostí nebo k oboru názvů, nabavit. Jakékoli zařízení, které obsahuje tento token, může odesílat zprávy přímo do tohoto centra událostí. Zařízení se navíc nedá od odeslání do tohoto centra událostí vyvažovat za zakázané.
> 
> Vždy doporučujeme zadat konkrétní a podrobné obory.

> [!IMPORTANT]
> Po vytvoření tokenů se každý klient zřídí s vlastním jedinečným tokenem.
>
> Když klient odešle data do centra událostí, označí svůj požadavek tokenem. Aby se zabránilo útočníkovi v odposlouchávání a krádeži tokenu, komunikace mezi klientem a centrem událostí musí probíhat přes zašifrovaný kanál.
> 
> Pokud je token odcizen útočníkem, může útočník zosobnit klienta, jehož token byl ukraden. Když je Vydavatel zakázaný, vykreslí ho jako nepoužitelný, dokud neobdrží nový token, který používá jiného vydavatele.


## <a name="authenticating-event-hubs-consumers-with-sas"></a>Ověřování Event Hubsch uživatelů pomocí SAS 
K ověření back-endové aplikace, které využívají data generovaná Event Hubsmi výrobci, Event Hubs ověřování tokenu vyžaduje, aby klienti měli oprávnění ke **správě** nebo aby mohla mít oprávnění k **naslouchání** přiřazená ke svému oboru názvů Event Hubs nebo instanci centra událostí nebo téma. Data se spotřebují z Event Hubs pomocí skupin uživatelů. I když vám zásada SAS poskytuje podrobný rozsah, je tento obor definovaný jenom na úrovni entity a ne na úrovni spotřebitele. To znamená, že oprávnění definovaná na úrovni oboru názvů nebo instance centra událostí nebo na úrovni tématu se použijí na skupiny uživatelů dané entity.

## <a name="next-steps"></a>Další kroky
Viz následující články:

- [Autorizovat pomocí SAS](authenticate-shared-access-signature.md)
- [Autorizovat pomocí řízení přístupu na základě role (RBAC)](authenticate-shared-access-signature.md)
- [Další informace o Event Hubs](event-hubs-about.md)

Podívejte se na následující související články:

- [Ověřování požadavků do Azure Event Hubs z aplikace pomocí Azure Active Directory](authenticate-application.md)
- [Ověření spravované identity pomocí Azure Active Directory pro přístup k prostředkům Event Hubs](authenticate-managed-identity.md)
- [Autorizace přístupu k prostředkům Event Hubs pomocí Azure Active Directory](authorize-access-azure-active-directory.md)
- [Autorizace přístupu k prostředkům Event Hubs pomocí sdílených přístupových podpisů](authorize-access-shared-access-signature.md)
