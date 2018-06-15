---
title: Azure Service Bus řízení přístupu s podpisy sdíleného přístupu | Microsoft Docs
description: Přehled řízení přístupu k Service Bus pomocí sdílené přístupové podpisy přehled, podrobnosti o autorizace SAS s Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2018
ms.author: sethm
ms.openlocfilehash: 420f4573fbe8b5139a4e1e5fa4dea3404c4e099d
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
ms.locfileid: "32312521"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Service Bus řízení přístupu s podpisy sdíleného přístupu

*Sdílené přístupové podpisy* (SAS) jsou primární zabezpečení mechanismus pro zasílání zpráv Service Bus. Tento článek popisuje SAS, jak fungují a jak je používat způsobem, bez ohledu na platformu.

SAS chrání přístup k Service Bus na základě pravidel autorizace. Ty jsou nakonfigurované buď na obor názvů nebo entity přenosu zpráv (předávání, fronta nebo téma). Autorizační pravidlo, má název, je přidružen konkrétní práva a představuje páru kryptografických klíčů. Použít název pravidla a klíč přes Service Bus SDK nebo v kódu pro vygenerování tokenu SAS. Klienta můžete pak předejte token k Service Bus k prokázání autorizace pro požadovanou operaci.

## <a name="overview-of-sas"></a>Přehled SAS

Sdílené přístupové podpisy jsou mechanismus ověřování založené na deklaracích pomocí jednoduchých tokenů. Pomocí SAS, klíče se nikdy předávají v drátové síti. Klíče se používají k podpisu kryptograficky informace, které lze později ověřit pomocí služby. SAS slouží podobně jako uživatelské jméno a heslo schéma kde klient je okamžitě u sebe název pravidla autorizace a odpovídající klíč. SAS lze také podobná model federované zabezpečení, kdy klient obdrží časově omezené a podepsaný přístupový token ze služby tokenů zabezpečení bez někdy přicházející do podpisový klíč.

Ověřování pomocí SAS v Service Bus s s názvem [sdíleného přístupu autorizační pravidla](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) s související přístupová práva a dvojice primární a sekundární kryptografické klíče. U klíčů se 256 bitů hodnoty ve formátu Base64 reprezentace. Můžete nakonfigurovat pravidla, na úrovni oboru názvů, v Service Bus [předává](service-bus-fundamentals-hybrid-solutions.md#relays), [fronty](service-bus-fundamentals-hybrid-solutions.md#queues), a [témata](service-bus-fundamentals-hybrid-solutions.md#topics).

[Sdíleného přístupového podpisu](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) token zabezpečení obsahuje název vybrané autorizační pravidlo, identifikátor URI prostředku, který musí mít přístup, rychlé, vypršela platnost a podpisu HMAC SHA256 kryptografických vypočítán na těchto polí pomocí primární nebo sekundární kryptografický klíč vybrané autorizační pravidlo.

## <a name="shared-access-authorization-policies"></a>Zásady autorizace sdíleného přístupu

Každý obor názvů Service Bus a každé entity služby sběrnice má zásady sdíleného přístupu autorizace skládá z pravidel. Zásady na úrovni oboru názvů platí pro všechny entity v oboru názvů, bez ohledu na jejich konfiguraci jednotlivých zásad.

Pro každé pravidlo zásady autorizace, rozhodnete na tři údaje: **název**, **oboru**, a **práva**. **Název** je právě toto; jedinečný název v rámci tohoto oboru. Rozsah je dostatečně snadno: je identifikátor URI prostředku nejistá. V případě oboru názvů Service Bus oboru je plně kvalifikovaný název domény (FQDN), jako například `https://<yournamespace>.servicebus.windows.net/`.

Práva podle pravidla zásad může být kombinace:

* "Odeslat" - uděluje práva k odesílání zpráv do entity
* 'Naslouchání' - uděluje práva k naslouchání (relé) nebo přijímat (fronty, odběry) a všechny související zpracování zpráv
* 'Spravovat' – uděluje práva pro správu topologie oboru názvů, včetně vytváření a odstraňování entit

Právo "Správa" obsahují oprávnění "Odeslat" a 'Přijmout'.

Zásadu obor názvů nebo entita může obsahovat až 12 sdíleného přístupu autorizační pravidla, poskytuje místo pro tři sady pravidel, každý pokrývajících základní práva a kombinace Send a naslouchání. Tento limit podtržení, které úložiště SAS zásady neměla být uživatel nebo účet úložiště služby. Pokud aplikace potřebuje k udělení přístupu k Service Bus na základě uživatele nebo identita služby, by měla implementovat služby tokenů zabezpečení, která vydává tokeny SAS po kontrolu ověřování a přístup.

Autorizační pravidlo je přiřazena *primární klíč* a *sekundární klíč*. Toto jsou kryptograficky silnou klíče. Nemusíte je ztratí nebo je úniku – vždy budete mít k dispozici v [portál Azure][Azure portal]. Můžete použít buď generovaného klíče a můžete je obnovit kdykoli. Pokud chcete znovu vygenerovat nebo změnit klíč v zásadách, všechny dřív vystavené tokeny na základě tohoto klíče se okamžitě zneplatní. Probíhající připojení vytvořená podle tyto tokeny se však budou nadále fungovat až do vypršení platnosti tokenu.

Při vytváření oboru názvů Service Bus s názvem pravidla zásad **RootManageSharedAccessKey** se automaticky vytvoří pro obor názvů. Tato zásada nemá oprávnění spravovat pro celý obor názvů. Doporučuje se toto pravidlo jako pro správu zacházet s **kořenové** účtu a nepoužívejte ji v aplikaci. Můžete vytvořit další zásady pravidla v **konfigurace** kartě pro obor názvů na portálu, prostřednictvím prostředí Powershell nebo rozhraní příkazového řádku Azure.

## <a name="configuration-for-shared-access-signature-authentication"></a>Konfigurace pro ověřování sdíleného přístupového podpisu

Můžete nakonfigurovat [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) pravidlo na obory názvů Service Bus, fronty a témata. Konfigurace [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) na Service Bus se aktuálně nepodporuje předplatné, ale nakonfigurovaná v oboru názvů nebo téma pravidla můžete použít k zabezpečení přístupu k odběrům. Pracovní vzorku, který znázorňuje tento postup, najdete v článku [pomocí sdíleného přístupového podpisu (SAS) ověřování pomocí předplatných Service Bus](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) ukázka.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

Na tomto obrázku *manageRuleNS*, *sendRuleNS*, a *listenRuleNS* autorizační pravidla, které se týkají fronta F1 a téma T1, při *listenRuleQ*  a *sendRuleQ* se vztahují pouze na frontu F1 a *sendRuleT* se vztahují pouze k tématu T1.

## <a name="generate-a-shared-access-signature-token"></a>Vygenerování tokenu sdíleného přístupového podpisu

SAS token můžete vygenerovat libovolného klienta, který má přístup k názvu název pravidla autorizace a jeden z jeho podpisové klíče. Vygenerování tokenu tím, že vytvoří řetězec ve formátu:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`** -Token vypršení platnosti rychlé. Celé číslo odrážející počet sekund od epoch `00:00:00 UTC` na 1. ledna pod hodnotou 1970 (UNIX epoch) při vypršení platnosti tokenu.
* **`skn`** -Název autorizační pravidlo.
* **`sr`** -URI prostředku přistupuje.
* **`sig`** -Podpis.

`signature-string` Přes identifikátor URI je počítaný hash SHA-256 (**oboru** jak je popsáno v předchozí části) a řetězcovou reprezentaci tokenu vypršení platnosti rychlých, oddělených Line FEED.

Výpočet hodnoty hash bude vypadat podobně jako následující kód pseudo a vrátí hodnotu hash 256 bitů / 32bajtů.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Token obsahuje hodnoty rozdělí tak, že příjemce může přepočítala hash se stejnými parametry, ověření, že vystavitel je k dispozici platný podpisový klíč. 

Identifikátor URI je úplný identifikátor URI prostředku Service Bus, ke kterému je požadován přístup. Například `http://<namespace>.servicebus.windows.net/<entityPath>` nebo `sb://<namespace>.servicebus.windows.net/<entityPath>`, tj `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`. Identifikátor URI musí být [kódováním procent](https://msdn.microsoft.com/library/4fkewx0t.aspx).

Autorizační pravidlo sdíleného přístupu, který se používá k podepisování musí být nakonfigurované na entitu zadanou tento identifikátor URI, nebo pomocí jedné z jejích hierarchické nadřazených tříd. Například `http://contoso.servicebus.windows.net/contosoTopics/T1` nebo `http://contoso.servicebus.windows.net` v předchozím příkladu.

SAS token je platná pro všechny prostředky s předponou `<resourceURI>` používány `signature-string`.

## <a name="regenerating-keys"></a>Opakované generování klíčů

Doporučujeme pravidelně obnovovat klíčů používaných v [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektu. Primární a sekundární klíče sloty existují, takže můžete otočit klíče postupně. Pokud vaše aplikace se obvykle používá primární klíč, můžete do sekundární klíče slotu zkopírujte primární klíč a pak znovu vygenerovat primární klíč. Novou hodnotu primárního klíče se pak dají nakonfigurovat do klientské aplikace, které neustálý přístup pomocí původní primární klíč v sekundárním slotu. Po aktualizaci všech klientů, můžete obnovit sekundární klíč nakonec vyřadit z provozu původní primární klíč.

Pokud znáte nebo podezření, že dojde k narušení klíč a máte k odvolání klíče, můžete obě obnovit [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) a [sekundární klíč](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) z [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), nahradí je s novými klíči. Tento postup by způsobila neplatnost všechny tokeny, které jsou podepsány pomocí starého klíče.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Ověření pomocí sdíleného přístupového podpisu službou Service Bus

Takto popsané scénáře zahrnují konfigurace autorizačních pravidel, generování tokenů SAS a autorizací klienta.

Pro úplnou pracovní vzorek aplikaci Service Bus, která ukazuje, konfigurace a používá autorizace SAS, najdete v části [sdíleného přístupového podpisu ověřování službou Service Bus](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Související příklad, který znázorňuje použití SAS autorizační pravidla, která je nakonfigurovaná na obory názvů nebo témata zabezpečit odběry služby Service Bus je k dispozici zde: [pomocí sdíleného přístupového podpisu (SAS) ověřování pomocí předplatných Service Bus](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Přístup sdíleného přístupu autorizační pravidla s entitou

Pomocí knihovny Service Bus rozhraní .NET Framework, můžete získat přístup k [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) nakonfigurované na fronty sběrnice nebo téma prostřednictvím objektu [AuthorizationRules](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) kolekce do odpovídajícího [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) nebo [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

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

## <a name="use-shared-access-signature-authorization"></a>Použití autorizačních sdíleného přístupového podpisu

Aplikace pomocí .NET SDK služby Azure na knihovny Service Bus .NET můžete použít autorizace SAS prostřednictvím [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) třídy. Následující kód ukazuje použití zprostředkovatel tokenu k odesílání zpráv do fronty Service Bus. Alternativu k použití zde uvedeny že také předat token u dřív vydaných metoda factory zprostředkovatele tokenu.

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

Můžete taky zprostředkovatel tokenu přímo pro vydávání tokenů předat pro ostatní klienty. 

Připojovací řetězce může zahrnovat název pravidla (*SharedAccessKeyName*) a klíč pravidlo (*SharedAccessKey*), nebo token u dřív vydaných (*SharedAccessSignature*). Když těch, které jsou v něm v připojovacím řetězci předaný žádné konstruktoru nebo přijetí připojovací řetězec metoda factory, je automaticky vytvořen a naplněny zprostředkovatel tokenu SAS.

Všimněte si, že použití autorizace SAS s předávací služby Service Bus, můžete použít klíče SAS, které jsou nakonfigurované na oboru názvů Service Bus. Pokud vytvoříte relé explicitně na obor názvů ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) s [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)) objektu, budete moct nastavit pravidla SAS jen pro tento předávání. Pokud chcete používat autorizace SAS s odběry služby Service Bus, můžete použít klíče SAS, které jsou nakonfigurované na obor názvů sběrnice nebo téma.

## <a name="use-the-shared-access-signature-at-http-level"></a>Použití sdíleného přístupového podpisu (na úrovni protokolu HTTP)

Teď, když víte, jak vytvořit podpisy sdíleného přístupu pro všechny entity v Service Bus, jste připravení na provedení HTTP POST:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 

Pamatujte si, že se tento postup funguje pro vše. Můžete vytvořit SAS pro fronty, tématu nebo předplatného.

Pokud poskytnete odesílatele nebo klienta SAS token, nemají klíč přímo a jejich nelze vrátit hodnotu hash k jeho získání. Jako takový budete mít kontrolu nad co přístupem a jak dlouho. Třeba mít na paměti je, že pokud změníte primární klíč v zásadách, jsou zneplatněny žádné sdílené přístupové podpisy vytvořit z něj.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Použití sdíleného přístupového podpisu (na úrovni AMQP)

V předchozí části znáte postup použití tokenu SAS s požadavek HTTP POST k odesílání dat do služby Service Bus. Jak víte, můžete přistupovat pomocí Advanced zpráv služby Řízení front protokol (AMQP), je upřednostňovanou protokol bude použit z důvodů výkonu v mnoha scénářích Service Bus. Využití tokenu SAS s AMQP je popsané v dokumentu [AMQP Claim-Based zabezpečení verze 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) který je v pracovní koncept od 2013 ale dobře nepodporuje Azure ještě dnes.

Než začnete používat k odesílání dat do služby Service Bus, vydavatele musí poslat tokenu SAS uvnitř zprávu AMQP dobře definovaný AMQP uzel s názvem **$cbs** (zobrazí se jako "speciální" fronty služba používá k získání a ověření všech SAS tokeny). Musíte zadat vydavatele **ReplyTo** pole uvnitř zpráva AMQP; Toto je uzel, ve kterém služba reaguje na vydavatele s výsledek ověření tokenu (jednoduché požadavek nebo odpověď vzor mezi vydavatele a služby ). Tento uzel odpověď se vytvoří "na za chodu," o "dynamické vytvoření vzdáleném uzlu" a Mluvte podle specifikace protokolu AMQP 1.0. Po zkontrolování, že je platný SAS token, můžete vydavatele vpřed a začít odesílat data do služby.

Následující kroky ukazují, jak odeslat token SAS s použitím protokolu AMQP [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) knihovny. To je užitečné, pokud nemůžete použít oficiální Service Bus SDK (například na WinRT, rozhraní .net Compact Framework, rozhraní .net Micro Framework a Mono) vývoj v jazyce C\#. Tato knihovna je samozřejmě užitečné při pochopit, jak založené na deklaracích identity zabezpečení funguje na úrovni protokolu AMQP, jako jste viděli, jak to funguje na úrovni protokolu HTTP (s požadavek HTTP POST a SAS token odeslaný v hlavičce "Autorizace"). Pokud nepotřebujete takové hluboké znalosti o AMQP, můžete použít oficiální Service Bus SDK s rozhraním .net Framework aplikace, které bude to pro vás.

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

`PutCbsToken()` Metoda přijímá *připojení* (instance třídy připojení AMQP jako poskytované [AMQP .NET Lite knihovny](https://github.com/Azure/amqpnetlite)), která představuje připojení TCP a *sasToken* parametr, který je SAS token k odeslání. 

> [!NOTE]
> Je důležité, že připojení je vytvořen s **SASL ověřovací mechanismus nastavená na ANONYMNÍ** (a ne prostý výchozí uživatelské jméno a heslo použít, když je nebudete muset odeslat SAS token).
> 
> 

V dalším kroku vydavatele vytvoří dva odkazy AMQP pro tokenu SAS odesílání a příjmu odpovědi (výsledek ověření tokenu) ze služby.

AMQP zpráva obsahuje sadu vlastností a další informace než jednoduché zprávy. SAS token je do těla zprávy (pomocí jeho konstruktoru). **"ReplyTo"** je nastavena na název uzlu pro příjem výsledek ověření na tento odkaz příjemce (pokud, můžete změnit její název chcete a bude nutné vytvořit dynamicky službou). Poslední tři aplikace nebo vlastní vlastnosti používá službu k označení, jaký druh operace má spustit. Jak je popsáno ve specifikaci CBS koncept, musí být **název operace** ("put-token"), **typ tokenu** (v tomto případě `servicebus.windows.net:sastoken`) a **"název" cílová skupina** na kterou se vztahuje token (celý entity).

Po odeslání tokenu SAS na odkaz odesílatele, musí vydavatele čtení odpovědi na tento odkaz příjemce. Odpověď je jednoduchý AMQP zprávou s vlastností aplikace s názvem **"kód stavu"** obsahující stejné hodnoty jako stavový kód HTTP.

## <a name="rights-required-for-service-bus-operations"></a>Práva potřebná pro operace služby Service Bus

V následující tabulce jsou uvedeny přístupová práva potřebná pro různé operace na prostředcích služby Service Bus.

| Operace | Požadované deklarace identity | Deklarace oboru |
| --- | --- | --- |
| **obor názvů** | | |
| Konfigurovat autorizační pravidlo na obor názvů |Spravovat |Každou adresu, obor názvů |
| **Služba registru** | | |
| Zobrazení výčtu privátní zásady |Spravovat |Každou adresu, obor názvů |
| Zahájit naslouchání na obor názvů |Naslouchání |Každou adresu, obor názvů |
| Odesílání zpráv pro naslouchací proces v oboru názvů |Odeslat |Každou adresu, obor názvů |
| **Fronty** | | |
| Vytvoření fronty |Spravovat |Každou adresu, obor názvů |
| Odstranění fronty |Spravovat |Každou adresu, platný fronty |
| Zobrazení výčtu fronty |Spravovat |$Prostředky nebo fronty |
| Získat popis fronty |Spravovat |Každou adresu, platný fronty |
| Konfigurovat autorizační pravidlo pro frontu |Spravovat |Každou adresu, platný fronty |
| Odeslat do fronty |Odeslat |Každou adresu, platný fronty |
| Příjem zpráv z fronty |Naslouchání |Každou adresu, platný fronty |
| Chyby nebo celé zprávy po přijetí zprávy v režimu zamknutí funkce Náhled |Naslouchání |Každou adresu, platný fronty |
| Odložení zprávu pro pozdější načtení |Naslouchání |Každou adresu, platný fronty |
| Tato zpráva |Naslouchání |Každou adresu, platný fronty |
| Zjištění stavu související s relací fronty zpráv |Naslouchání |Každou adresu, platný fronty |
| Nastavit stav související s relací fronty zpráv |Naslouchání |Každou adresu, platný fronty |
| Plán zprávu pro pozdější doručení; například [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Naslouchání | Každou adresu, platný fronty
| **Téma** | | |
| Vytvoření tématu |Spravovat |Každou adresu, obor názvů |
| Odstranit téma |Spravovat |Každou adresu, platný tématu |
| Zobrazení výčtu témata |Spravovat |$Prostředky nebo témata |
| Získání popisu tématu |Spravovat |Každou adresu, platný tématu |
| Konfigurovat autorizační pravidlo pro téma |Spravovat |Každou adresu, platný tématu |
| Odeslat do tématu |Odeslat |Každou adresu, platný tématu |
| **Předplatné** | | |
| Vytvoření odběru |Spravovat |Každou adresu, obor názvů |
| Odstranit předplatné |Spravovat |.. /myTopic/Subscriptions/mySubscription |
| Zobrazení výčtu odběrů |Spravovat |.. / myTopic/odběrů |
| Získat předplatné popis |Spravovat |.. /myTopic/Subscriptions/mySubscription |
| Chyby nebo celé zprávy po přijetí zprávy v režimu zamknutí funkce Náhled |Naslouchání |.. /myTopic/Subscriptions/mySubscription |
| Odložení zprávu pro pozdější načtení |Naslouchání |.. /myTopic/Subscriptions/mySubscription |
| Tato zpráva |Naslouchání |.. /myTopic/Subscriptions/mySubscription |
| Zjištění stavu související s relací tématu |Naslouchání |.. /myTopic/Subscriptions/mySubscription |
| Nastavit stav související s relací tématu |Naslouchání |.. /myTopic/Subscriptions/mySubscription |
| **Pravidla** | | |
| Vytvoření pravidla |Spravovat |.. /myTopic/Subscriptions/mySubscription |
| Odstranění pravidla |Spravovat |.. /myTopic/Subscriptions/mySubscription |
| Zobrazení výčtu pravidel |Spravovat nebo naslouchání |.. /myTopic/Subscriptions/mySubscription/Rules 

## <a name="next-steps"></a>Další postup

Pokud se o přenosu zpráv přes Service Bus chcete dozvědět víc, pročtěte si následující témata.

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Jak používat fronty Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com