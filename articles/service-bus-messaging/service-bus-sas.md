---
title: Řízení přístupu Azure Service Bus se sdílenými přístupovými podpisy
description: Přehled řízení přístupu služby Service Bus pomocí přehledu sdílených přístupových podpisů, podrobnosti o autorizaci SAS pomocí služby Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: c381d9413c4003bc2ab9a9357ff2769e84d14c3e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259471"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Řízení přístupu služby Service Bus se sdílenými přístupovými podpisy

*Sdílené přístupové podpisy* (SAS) jsou primární mechanismus zabezpečení pro zasílání zpráv service bus. Tento článek popisuje SAS, jak fungují a jak je používat způsobem, který je nepříslemňovací platforma.

SAS chrání přístup k service bus na základě autorizačních pravidel. Ty jsou konfigurovány buď v oboru názvů, nebo v entitě zasílání zpráv (přenos, fronta nebo téma). Autorizační pravidlo má název, je přidruženo k určitým právům a nese dvojici kryptografických klíčů. Název a klíč pravidla pomocí sady Service Bus SDK nebo ve vlastním kódu slouží ke generování tokenu SAS. Klient pak může předat token service bus prokázat autorizaci pro požadovanou operaci.

> [!NOTE]
> Azure Service Bus podporuje autorizaci přístupu k oboru názvů Service Bus a jeho entit pomocí Azure Active Directory (Azure AD). Autorizace uživatelů nebo aplikací pomocí tokenu OAuth 2.0 vráceného službou Azure AD poskytuje vynikající zabezpečení a snadné použití oproti sdíleným přístupovým podpisům (SAS). S Azure AD, není nutné ukládat tokeny ve vašem kódu a riskovat potenciální ohrožení zabezpečení.
>
> Společnost Microsoft doporučuje používat Azure AD s aplikacemi Azure Service Bus, pokud je to možné. Další informace najdete v těchto článcích:
> - [Ověřte a autorizujte aplikaci pomocí Služby Azure Active Directory pro přístup k entitám Azure Service Bus](authenticate-application.md).
> - [Ověření spravované identity pomocí Služby Azure Active Directory pro přístup k prostředkům Služby Azure Service Bus](service-bus-managed-service-identity.md)

## <a name="overview-of-sas"></a>Přehled SAS

Sdílené přístupové podpisy jsou mechanismus autorizace založené na deklaracích identity pomocí jednoduchých tokenů. Pomocí SAS klíče nejsou nikdy předány na drátě. Klíče se používají ke kryptograficky podepisovat informace, které mohou být později ověřeny službou. SAS lze použít podobně jako uživatelské jméno a heslo schéma, kde klient je v bezprostředním držení název pravidla autorizace a odpovídající klíč. SAS lze také použít podobně jako federovaný model zabezpečení, kde klient obdrží časově omezený a podepsaný přístupový token ze služby tokenu zabezpečení, aniž by někdy převzal podpisový klíč.

Ověřování SAS v service bus je nakonfigurováno s názvem [Pravidla autorizace sdíleného přístupu](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) s přidruženými přístupovými právy a dvojicí primárních a sekundárních kryptografických klíčů. Klíče jsou 256bitové hodnoty v reprezentaci Base64. Pravidla můžete konfigurovat na úrovni oboru názvů, v [přenosech služby](../service-bus-relay/relay-what-is-it.md)Service Bus , [frontách](service-bus-messaging-overview.md#queues)a [tématech](service-bus-messaging-overview.md#topics).

Token [sdíleného přístupového podpisu](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) obsahuje název zvoleného autorizačního pravidla, identifikátor URI prostředku, ke kterému bude přístup, okamžik vypršení platnosti a kryptografický podpis HMAC-SHA256 vypočítaný přes tato pole pomocí primárního nebo sekundárního kryptografického klíče zvoleného autorizačního pravidla.

## <a name="shared-access-authorization-policies"></a>Zásady autorizace sdíleného přístupu

Každý obor názvů service bus a každá entita služby Service Bus má zásadu autorizace sdíleného přístupu tvořenou pravidly. Zásady na úrovni oboru názvů platí pro všechny entity uvnitř oboru názvů, bez ohledu na jejich individuální konfiguraci zásad.

Pro každé pravidlo zásad autorizace se rozhodnete pro tři informace: **název**, **obor**a **práva**. **Jméno** je právě to; jedinečný název v rámci tohoto oboru. Obor je dost snadné: je identifikátor URI daného prostředku. Pro obor názvů Service Bus je obor plně kvalifikovaný název domény (FQDN), například `https://<yournamespace>.servicebus.windows.net/`.

Práva vyplývající z pravidla politiky mohou být kombinací:

* "Odeslat" - Uděluje právo posílat zprávy účetní jednotce
* 'Listen' - uděluje právo naslouchat (relé) nebo přijímat (fronty, odběry) a všechny související zpracování zpráv
* "Správa" – uděluje právo spravovat topologii oboru názvů, včetně vytváření a mazání entit.

Právo "Spravovat" zahrnuje práva "Odeslat" a "Přijmout".

Obor názvů nebo zásady entity mohou obsahovat až 12 pravidel autorizace sdíleného přístupu, která poskytují prostor pro tři sady pravidel, z nichž každá zahrnuje základní práva a kombinaci odeslat a naslouchat. Toto omezení zdůrazňuje, že úložiště zásad SAS není určeno jako úložiště uživatelských účtů nebo účtů služeb. Pokud vaše aplikace potřebuje udělit přístup k service bus na základě identity uživatele nebo služby, měla by implementovat službu tokenů zabezpečení, která vydává tokeny SAS po ověření a kontrole přístupu.

Autorizačnímu pravidlu je přiřazen *primární klíč* a *sekundární klíč*. Jedná se o kryptograficky silné klíče. Neztraťte je ani je neprozrazujte – budou vždy k dispozici na [webu Azure Portal][Azure portal]. Můžete použít některý z generovaných klíčů a můžete je kdykoli znovu vygenerovat. Pokud znovu vygenerujete nebo změníte klíč v zásadách, všechny dříve vydané tokeny založené na tomto klíči se okamžitě stanou neplatnými. Probíhající připojení vytvořená na základě těchto tokenů však budou pokračovat v práci, dokud platnost tokenu nevyprší.

Při vytváření oboru názvů Service Bus je pro obor názvů automaticky vytvořeno pravidlo zásads názvem **RootManageSharedAccessKey.** Tato zásada má oprávnění Spravovat pro celý obor názvů. Doporučujeme zacházet s tímto pravidlem jako s kořenovým účtem pro **správu** a nepoužívat ho ve vaší aplikaci. Další pravidla zásad můžete vytvořit na **kartě Konfigurace** pro obor názvů na portálu, přes Powershell nebo Azure CLI.

## <a name="configuration-for-shared-access-signature-authentication"></a>Konfigurace pro ověřování pomocí sdíleného přístupového podpisu

Pravidlo [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) můžete nakonfigurovat v oborech názvů service bus, frontách nebo tématech. Konfigurace [sharedaccessauthorizationrule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) na odběr service bus není aktuálně podporována, ale můžete použít pravidla nakonfigurovaná v oboru názvů nebo téma k zabezpečení přístupu k odběrům. Pracovní ukázku, která ilustruje tento postup, naleznete [pomocí sdíleného přístupového podpisu (SAS) ověřování s](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) service bus odběry ukázka.

![Sas](./media/service-bus-sas/service-bus-namespace.png)

Na tomto obrázku platí pravidla autorizace *manageRuleNS*, *sendRuleNS*a *listenRuleNS* pro frontu Q1 i téma T1, zatímco *listenRuleQ* a *sendRuleQ* platí pouze pro frontu Q1 a *sendRuleT* platí pouze pro téma T1.

## <a name="generate-a-shared-access-signature-token"></a>Generovat token sdíleného přístupového podpisu

Každý klient, který má přístup k názvu autorizačního pravidla a jednomu z jeho podpisových klíčů, může vygenerovat token SAS. Token je generován crafting řetězec v následujícím formátu:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`**- Okamžik vypršení platnosti tokenu. Celé číslo odráží sekundy od `00:00:00 UTC` epochy 1.
* **`skn`**- Název autorizačního pravidla.
* **`sr`**- Identifikátor URI přístupného prostředku.
* **`sig`**- Podpis.

Je `signature-string` hodnota hash SHA-256 vypočítaná přes identifikátor URI prostředku **(obor** popsaný v předchozí části) a řetězcová reprezentace okamžité vypršení platnosti tokenu oddělená lf.

Výpočthas vypadá podobně jako následující pseudo kód a vrátí hodnotu hash 256-bit/32 bajtů.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Token obsahuje hodnoty bez hodnoty hash tak, aby příjemce můžete přepočítat hash se stejnými parametry, ověření, že vystavitel je držitelem platného podpisového klíče.

Identifikátor URI prostředku je úplný identifikátor URI prostředku služby Service Bus, ke kterému je nárokován přístup. Například, `http://<namespace>.servicebus.windows.net/<entityPath>` `sb://<namespace>.servicebus.windows.net/<entityPath>`nebo ; to znamená, `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`že . 

**Identifikátor URI musí být [kódován procenty](https://msdn.microsoft.com/library/4fkewx0t.aspx).**

Pravidlo autorizace sdíleného přístupu používané pro podepisování musí být nakonfigurováno na entitě určené tímto identifikátorem URI nebo u jedné z jejích hierarchických nadřazených položek. Například `http://contoso.servicebus.windows.net/contosoTopics/T1` nebo `http://contoso.servicebus.windows.net` v předchozím příkladu.

Token SAS je platný pro všechny `<resourceURI>` prostředky s `signature-string`předponou použité v .

## <a name="regenerating-keys"></a>Regenerační klíče

Doporučujeme pravidelně obnovovat klíče používané v objektu [SharedAccessAuthorizationRule.](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) Sloty primárního a sekundárního klíče existují, takže můžete klávesy otáčet postupně. Pokud vaše aplikace obecně používá primární klíč, můžete zkopírovat primární klíč do slotu sekundárního klíče a teprve potom znovu vygenerovat primární klíč. Novou hodnotu primárního klíče lze poté nakonfigurovat do klientských aplikací, které mají trvalý přístup pomocí starého primárního klíče v sekundárním slotu. Po aktualizaci všech klientů můžete znovu vygenerovat sekundární klíč a nakonec vyřadit starý primární klíč.

Pokud víte nebo máte podezření, že je ohrožena klíč a budete muset odvolat klíče, můžete znovu vygenerovat [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) a [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) [sharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), jejich nahrazení novými klíči. Tento postup zruší platnost všech tokenů podepsaných starými klíči.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Ověřování sdíleného přístupového podpisu pomocí služby Service Bus

Scénáře popsané takto zahrnují konfiguraci autorizačních pravidel, generování tokenů SAS a autorizaci klienta.

Úplný pracovní vzorek aplikace service bus, který ilustruje konfiguraci a používá autorizaci SAS, naleznete v [tématu ověřování podpisu sdíleného přístupu pomocí služby Service Bus](https://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Související ukázka, která ilustruje použití autorizačních pravidel SAS nakonfigurovaných v oborech názvů nebo tématech k zabezpečení odběrů služby Service Bus, je k dispozici zde: [Použití ověřování s sdíleným přístupovým podpisem (SAS) s předplatným islužeb.](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c)

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Pravidla autorizace sdíleného přístupu pro entitu

Pomocí knihoven Service Bus .NET Framework můžete přistupovat k objektu [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) nakonfigurovanému ve frontě nebo tématu služby Service Bus prostřednictvím kolekce [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) v odpovídající [popisu fronty](/dotnet/api/microsoft.servicebus.messaging.queuedescription) nebo [tématu](/dotnet/api/microsoft.servicebus.messaging.topicdescription)Description .

Následující kód ukazuje, jak přidat autorizační pravidla pro frontu.

```csharp
// Create an instance of NamespaceManager for the operation
NamespaceManager nsm = NamespaceManager.CreateFromConnectionString(
    <connectionString> );
QueueDescription qd = new QueueDescription( <qPath> );

// Create a rule with send rights with keyName as "contosoQSendKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoSendKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Send }));

// Create a rule with listen rights with keyName as "contosoQListenKey"
// and add it to the queue description.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQListenKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] { AccessRights.Listen }));

// Create a rule with manage rights with keyName as "contosoQManageKey"
// and add it to the queue description.
// A rule with manage rights must also have send and receive rights.
qd.Authorization.Add(new SharedAccessAuthorizationRule("contosoQManageKey",
    SharedAccessAuthorizationRule.GenerateRandomKey(),
    new[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send }));

// Create the queue.
nsm.CreateQueue(qd);
```

## <a name="use-shared-access-signature-authorization"></a>Použití autorizace sdíleného přístupového podpisu

Aplikace používající sadku Azure .NET SDK s knihovnami Service Bus .NET můžou používat autorizaci SAS prostřednictvím třídy [SharedAccessSignatureTokenProvider.](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) Následující kód ilustruje použití zprostředkovatele tokenu k odesílání zpráv do fronty služby Service Bus. Alternativou k použití zde uvedené, můžete také předat dříve vydaný token metodu factory zprostředkovatele tokenu.

```csharp
Uri runtimeUri = ServiceBusEnvironment.CreateServiceUri("sb",
    <yourServiceNamespace>, string.Empty);
MessagingFactory mf = MessagingFactory.Create(runtimeUri,
    TokenProvider.CreateSharedAccessSignatureTokenProvider(keyName, key));
QueueClient sendClient = mf.CreateQueueClient(qPath);

//Sending hello message to queue.
BrokeredMessage helloMessage = new BrokeredMessage("Hello, Service Bus!");
helloMessage.MessageId = "SAS-Sample-Message";
sendClient.Send(helloMessage);
```

Můžete také použít zprostředkovatele tokenu přímo pro vydávání tokenů předat jiným klientům.

Připojovací řetězce mohou obsahovat název pravidla *(SharedAccessKeyName*) a klíč pravidla (*SharedAccessKey*) nebo dříve vydaný token (*SharedAccessSignature*). Pokud jsou k dispozici v připojovacím řetězci předaném libovolnému konstruktoru nebo metodě výroby, která přijímá připojovací řetězec, je poskytovatel tokenu SAS automaticky vytvořen a naplněn.

Všimněte si, že pro použití autorizace SAS s přenosy služby Service Bus můžete použít klíče SAS nakonfigurované v oboru názvů Service Bus. Pokud explicitně vytvoříte přenos v oboru názvů[(NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) s [Objektem RelayDescription),](/dotnet/api/microsoft.servicebus.messaging.relaydescription)můžete nastavit pravidla SAS pouze pro toto předávání. Chcete-li používat autorizaci SAS s předplatným i služby Service Bus, můžete použít klíče SAS nakonfigurované v oboru názvů service bus nebo v tématu.

## <a name="use-the-shared-access-signature-at-http-level"></a>Použití sdíleného přístupového podpisu (na úrovni PROTOKOLU HTTP)

Nyní, když víte, jak vytvořit sdílené přístupové podpisy pro všechny entity v service bus, jste připraveni provést http post:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
```

Pamatuj, tohle funguje na všechno. SAS můžete vytvořit pro frontu, téma nebo předplatné.

Pokud dáte odesílateli nebo klientovi token SAS, nemají klíč přímo a nemohou stornovat hash k jeho získání. Jako takový, máte kontrolu nad tím, co mají přístup, a na jak dlouho. Důležité je mít na paměti, že pokud změníte primární klíč v zásadě, všechny sdílené přístupové podpisy vytvořené z něj jsou zrušeny.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Použití sdíleného přístupového podpisu (na úrovni AMQP)

V předchozí části jste viděli, jak používat token SAS s požadavkem HTTP POST pro odesílání dat do sběrnice. Jak víte, můžete přistupovat k service bus pomocí protokolu Advanced Message Queuing Protocol (AMQP), který je upřednostňovaným protokolem, který se používá z důvodů výkonu, v mnoha scénářích. Využití tokenu SAS s AMQP je popsáno v dokumentu [AMQP verze zabezpečení založené na deklaraci pohledávky verze 1.0,](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) který je v pracovní verzi od roku 2013, ale dobře podporovaný Azure dnes.

Před zahájením odesílání dat do služby Service Bus musí vydavatel odeslat token SAS uvnitř zprávy AMQP do dobře definovaného uzlu AMQP s názvem **$cbs** (můžete jej zobrazit jako "speciální" frontu používanou službou k získání a ověření všech tokenů SAS). Vydavatel musí zadat pole **ReplyTo** uvnitř zprávy AMQP. Toto je uzel, ve kterém služba odpovídá vydavateli s výsledkem ověření tokenu (jednoduchý vzor požadavku/odpovědi mezi vydavatelem a službou). Tento uzel odpovědi je vytvořen "průběžně", hovoří o "dynamické vytváření vzdáleného uzlu", jak je popsáno ve specifikaci AMQP 1.0. Po kontrole, že token SAS je platný, vydavatel může přejít dopředu a začít odesílat data do služby.

Následující kroky ukazují, jak odeslat token SAS s protokolem AMQP pomocí knihovny [AMQP.NET Lite.](https://github.com/Azure/amqpnetlite) To je užitečné, pokud nelze použít oficiální service bus SDK (například na WinRT, .NET Compact\#Framework, .NET Micro Framework a Mono) vyvíjející se v C . Tato knihovna je samozřejmě užitečná, aby pomohla pochopit, jak funguje zabezpečení založené na deklaracích identity na úrovni AMQP, jak jste viděli, jak funguje na úrovni HTTP (s požadavkem HTTP POST a tokenem SAS odeslaným v hlavičce "Autorizace"). Pokud nepotřebujete tak hluboké znalosti o AMQP, můžete použít oficiální Service Bus SDK s aplikacemi rozhraní .NET Framework, které to udělají za vás.

### <a name="c35"></a>C&#35;

```csharp
/// <summary>
/// Send claim-based security (CBS) token
/// </summary>
/// <param name="shareAccessSignature">Shared access signature (token) to send</param>
private bool PutCbsToken(Connection connection, string sasToken)
{
    bool result = true;
    Session session = new Session(connection);

    string cbsClientAddress = "cbs-client-reply-to";
    var cbsSender = new SenderLink(session, "cbs-sender", "$cbs");
    var cbsReceiver = new ReceiverLink(session, cbsClientAddress, "$cbs");

    // construct the put-token message
    var request = new Message(sasToken);
    request.Properties = new Properties();
    request.Properties.MessageId = Guid.NewGuid().ToString();
    request.Properties.ReplyTo = cbsClientAddress;
    request.ApplicationProperties = new ApplicationProperties();
    request.ApplicationProperties["operation"] = "put-token";
    request.ApplicationProperties["type"] = "servicebus.windows.net:sastoken";
    request.ApplicationProperties["name"] = Fx.Format("amqp://{0}/{1}", sbNamespace, entity);
    cbsSender.Send(request);

    // receive the response
    var response = cbsReceiver.Receive();
    if (response == null || response.Properties == null || response.ApplicationProperties == null)
    {
        result = false;
    }
    else
    {
        int statusCode = (int)response.ApplicationProperties["status-code"];
        if (statusCode != (int)HttpStatusCode.Accepted && statusCode != (int)HttpStatusCode.OK)
        {
            result = false;
        }
    }

    // the sender/receiver may be kept open for refreshing tokens
    cbsSender.Close();
    cbsReceiver.Close();
    session.Close();

    return result;
}
```

Metoda `PutCbsToken()` obdrží *připojení* (instance třídy připojení AMQP podle [knihovny AMQP .NET Lite),](https://github.com/Azure/amqpnetlite)která představuje připojení TCP ke službě a parametr *sasToken,* který je tokenem SAS k odeslání.

> [!NOTE]
> Je důležité, aby připojení je vytvořen s **mechanismem ověřování SASL nastavena na ANONYMOUS** (a ne výchozí PLAIN s uživatelským jménem a heslem používané, když nepotřebujete odeslat token SAS).
>
>

Dále vydavatel vytvoří dva odkazy AMQP pro odeslání tokenu SAS a přijetí odpovědi (výsledek ověření tokenu) ze služby.

Zpráva AMQP obsahuje sadu vlastností a více informací než jednoduchá zpráva. Token SAS je tělo zprávy (pomocí jeho konstruktoru). Vlastnost **"ReplyTo"** je nastavena na název uzlu pro příjem výsledku ověření na odkazu příjemce (můžete změnit jeho název, pokud chcete, a bude vytvořen dynamicky službou). Poslední tři vlastnosti aplikace/vlastní služby jsou používány službou k označení, jaký druh operace má provést. Jak je popsáno v návrhu specifikace CBS, musí se jednat o **název operace** ("put-token"), **typ tokenu** (v tomto případě `servicebus.windows.net:sastoken`a ) a **"název" cílové skupiny,** na kterou se token vztahuje (celá entita).

Po odeslání tokenu SAS na odkaz odesílatele musí vydavatel přečíst odpověď na odkaz příjemce. Odpověď je jednoduchá zpráva AMQP s vlastností aplikace s názvem **"stavový kód",** která může obsahovat stejné hodnoty jako stavový kód HTTP.

## <a name="rights-required-for-service-bus-operations"></a>Práva požadovaná pro provoz sběrnice Service Bus

V následující tabulce jsou uvedena přístupová práva požadovaná pro různé operace s prostředky služby Service Bus.

| Operace | Je vyžadována deklarace | Obor deklarace |
| --- | --- | --- |
| **Namespace** | | |
| Konfigurace autorizačního pravidla v oboru názvů |Správa |Libovolná adresa oboru názvů |
| **Registr služeb** | | |
| Výčet soukromých zásad |Správa |Libovolná adresa oboru názvů |
| Zahájení poslechu v oboru názvů |Naslouchat |Libovolná adresa oboru názvů |
| Odesílání zpráv posluchači v oboru názvů |Odeslat |Libovolná adresa oboru názvů |
| **Fronta** | | |
| Vytvoření fronty |Správa |Libovolná adresa oboru názvů |
| Odstranění fronty |Správa |Libovolná platná adresa fronty |
| Výčet front |Správa |/$Resources/Fronty |
| Získání popisu fronty |Správa |Libovolná platná adresa fronty |
| Konfigurace autorizačního pravidla pro frontu |Správa |Libovolná platná adresa fronty |
| Odeslat do fronty |Odeslat |Libovolná platná adresa fronty |
| Příjem zpráv z fronty |Naslouchat |Libovolná platná adresa fronty |
| Opuštění nebo dokončení zpráv po přijetí zprávy v režimu peek-lock |Naslouchat |Libovolná platná adresa fronty |
| Odložení zprávy pro pozdější načtení |Naslouchat |Libovolná platná adresa fronty |
| Mrtvé písmeno zprávu |Naslouchat |Libovolná platná adresa fronty |
| Získání stavu přidruženého k relaci fronty zpráv |Naslouchat |Libovolná platná adresa fronty |
| Nastavení stavu přidruženého k relaci fronty zpráv |Naslouchat |Libovolná platná adresa fronty |
| Naplánování zprávy pro pozdější doručení. například [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Naslouchat | Libovolná platná adresa fronty
| **Téma** | | |
| Vytvoření tématu |Správa |Libovolná adresa oboru názvů |
| Odstranění tématu |Správa |Libovolná platná adresa tématu |
| Výčet témat |Správa |/$Resources/Témata |
| Získání popisu tématu |Správa |Libovolná platná adresa tématu |
| Konfigurace autorizačního pravidla pro téma |Správa |Libovolná platná adresa tématu |
| Poslat tématu |Odeslat |Libovolná platná adresa tématu |
| **Předplatné** | | |
| Vytvoření odběru |Správa |Libovolná adresa oboru názvů |
| Odstranění předplatného |Správa |.. /myTopic/Subscriptions/mySubscription |
| Výčet předplatných |Správa |.. /myTopic/Odběry |
| Získání popisu předplatného |Správa |.. /myTopic/Subscriptions/mySubscription |
| Opuštění nebo dokončení zpráv po přijetí zprávy v režimu peek-lock |Naslouchat |.. /myTopic/Subscriptions/mySubscription |
| Odložení zprávy pro pozdější načtení |Naslouchat |.. /myTopic/Subscriptions/mySubscription |
| Mrtvé písmeno zprávu |Naslouchat |.. /myTopic/Subscriptions/mySubscription |
| Získání stavu přidruženého k relaci tématu |Naslouchat |.. /myTopic/Subscriptions/mySubscription |
| Nastavení stavu přidruženého k relaci tématu |Naslouchat |.. /myTopic/Subscriptions/mySubscription |
| **Pravidla** | | |
| Vytvoření pravidla |Správa |.. /myTopic/Subscriptions/mySubscription |
| Odstranění pravidla |Správa |.. /myTopic/Subscriptions/mySubscription |
| Zobrazení výčtu pravidel |Správa nebo naslouchání |.. /myTopic/Předplatné/mySubscription/Pravidla

## <a name="next-steps"></a>Další kroky

Pokud se o přenosu zpráv přes Service Bus chcete dozvědět víc, pročtěte si následující témata.

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Použití front služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com
