---
title: Azure Service Bus řízení přístupu pomocí sdílených přístupových podpisů | Dokumentace Microsoftu
description: Přehled řízení přístupu služby Service Bus pomocí sdílených přístupových podpisů přehled, podrobnosti o autorizace SAS pomocí služby Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2018
ms.author: spelluru
ms.openlocfilehash: 2905bff56c5ab49c91f85e0816b84018b27bbb57
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43700235"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Řízení přístupu služby Service Bus se sdílenými přístupovými podpisy

*Sdílené přístupové podpisy* (SAS) je mechanismus primární zabezpečení pro zasílání zpráv Service Bus. Tento článek popisuje SAS, jak fungují a jak je používat způsobem, bez ohledu na platformu.

SAS chrání přístup k Service Bus na základě pravidel autorizace. Ty jsou nakonfigurované v oboru názvů nebo entity pro zasílání zpráv (relay, fronty nebo tématu). Autorizační pravidlo, má název, je přidružený k konkrétní práva a přenáší pár kryptografických klíčů. Použijete název a klíč pomocí sady SDK služby Service Bus nebo ve svém vlastním kódu toto pravidlo k vygenerování tokenu SAS. Klient pak můžete předat token služby Service Bus prokázat, že autorizace pro požadovanou operaci.

## <a name="overview-of-sas"></a>Přehled SAS

Sdílené přístupové podpisy jsou založené na deklaracích autorizační mechanismus, pomocí jednoduchých tokeny. Pomocí SAS, klíče jsou nebyl nikdy předán na lince. Klíče se používají k podpisu kryptograficky informace, které můžete později ověřit pomocí služby. SAS slouží podobně jako uživatelské jméno a heslo schéma kde klient je okamžitě vlastníkem daného názvu pravidla autorizace a odpovídajícího klíče. SAS lze také podobně jako model zabezpečení, kde klient obdrží časově omezené a podepsaný přístupový token od služby tokenů zabezpečení bez někdy přicházejících na vlastnictví podpisový klíč.

SAS ověřování ve službě Service Bus je nakonfigurovaný s názvem [sdíleného přístupu autorizační pravidla](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) s související přístupová práva a dvojice primární a sekundární kryptografických klíčů. Klíče jsou hodnoty 256 bitů v reprezentaci ve formátu Base64. Můžete nakonfigurovat pravidla na úrovni oboru názvů, ve službě Service Bus [předává](service-bus-fundamentals-hybrid-solutions.md#relays), [fronty](service-bus-fundamentals-hybrid-solutions.md#queues), a [témata](service-bus-fundamentals-hybrid-solutions.md#topics).

[Sdílený přístupový podpis](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) token obsahuje název zvoleném autorizační pravidlo, identifikátor URI prostředku, který musí mít přístup, rychlé, vypršení platnosti a kryptografický podpis HMAC SHA256 vypočítaný přes pole primární nebo sekundární kryptografický klíč vybrané autorizační pravidlo.

## <a name="shared-access-authorization-policies"></a>Povolení zásady sdíleného přístupu

Každý obor názvů služby Service Bus a Každá entita služby Service Bus má zásady sdíleného autorizaci pro přístup z pravidla. Tuto zásadu na úrovni oboru názvů se vztahuje na všechny entity v oboru názvů, bez ohledu na jejich konfiguraci jednotlivých zásad.

Pro každé pravidlo zásad autorizace rozhodnout o tři údaje: **název**, **oboru**, a **práva**. **Název** přesně to; je jedinečný název v daném oboru. Obor je docela jednoduché: je identifikátor URI na příslušném prostředku. Pro obor názvů služby Service Bus, obor je plně kvalifikovaný název domény (FQDN), jako například `https://<yournamespace>.servicebus.windows.net/`.

Práva vyplývající ze zásad pravidlo může být kombinací:

* "Odeslat" - uděluje práva k odesílání zpráv do entity
* "Naslouchání" - uděluje právo k naslouchání (relay) nebo přijímání (fronty, odběry) a všech souvisejících zpracování zpráv
* "Správa" – uděluje právo spravovat topologie obor názvů, včetně vytváření a odstraňování entit

Právo "Spravovat" zahrnuje "Odeslat" a "Přijmout" práva.

Zásady oboru názvů nebo entita může obsahovat až 12 sdíleného přístupu autorizační pravidla, poskytuje místo pro tři sady pravidel, každý pokrývají základní práva a kombinace odeslat a naslouchání. Tento limit podtržení, které ukládají se zásady SAS není určena pro uživatele nebo service účtu úložiště. Pokud vaše aplikace potřebuje pro udělení přístupu na základě uživatele nebo identity služby Service Bus, by měly implementovat služby tokenů zabezpečení, která vystavuje tokeny SAS po kontrole ověřování a přístup.

Autorizační pravidlo je přiřazen *primární klíč* a *sekundární klíč*. Jedná o přidělení kryptograficky silného klíče. Nechcete ztratit nebo způsobit únik těchto je – budete je mít vždycky k dispozici v [webu Azure portal][Azure portal]. Můžete použít kteroukoli z generovaného klíče a můžete je znovu v každém okamžiku. Pokud chcete znovu vygenerovat nebo změnit klíč v zásadách, všechny dříve vydané tokeny na základě tohoto klíče se okamžitě zneplatní. Trvalé připojení vytvoří podle těchto tokenů však budou i nadále fungovat až do vypršení platnosti tokenu.

Při vytváření oboru názvů služby Service Bus s názvem pravidla zásad **RootManageSharedAccessKey** pro obor názvů se automaticky vytvoří. Tato zásada má Správa oprávnění pro celý obor názvů. Je doporučeno tato pravidla, třeba pro správu nakládat s **kořenové** účtu a nepoužívejte ho ve vaší aplikaci. Můžete vytvořit další zásady pravidla v **konfigurovat** kartu pro obor názvů na portálu prostřednictvím Powershellu nebo rozhraní příkazového řádku Azure.

## <a name="configuration-for-shared-access-signature-authentication"></a>Konfigurace ověřování sdílený přístupový podpis

Můžete nakonfigurovat [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) pravidlo na obory názvů služby Service Bus, front nebo témat. Konfigurace [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) na Service Bus se předplatné aktuálně nepodporuje, ale pravidla, které jsou nakonfigurované na oboru názvů nebo téma můžete použít k zabezpečení přístupu k předplatným. Funkční příklad, který znázorňuje tento postup, najdete v článku [ověřování pomocí sdíleného přístupového podpisu (SAS) se odběry služby Service Bus](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) vzorku.

![SAS](./media/service-bus-sas/service-bus-namespace.png)

Na tomto obrázku *manageRuleNS*, *sendRuleNS*, a *listenRuleNS* autorizační pravidla platí pro fronty Q1 i téma T1, zatímco *listenRuleQ*  a *sendRuleQ* platí pouze pro fronty Q1 a *sendRuleT* platí pouze pro téma T1.

## <a name="generate-a-shared-access-signature-token"></a>Vygenerovat token sdíleného přístupového podpisu

SAS token můžete vygenerovat libovolného klienta, který má přístup k názvu názvu pravidla autorizace a jeden z jeho podpisové klíče. Token je generován vytváření řetězec v následujícím formátu:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

* **`se`** -Platnost tokenu vypršela rychlé. Celé číslo odráží sekund od epochy `00:00:00 UTC` na 1. ledna 1970 (UNIX epocha) při vypršení platnosti tokenu.
* **`skn`** – Název pravidla autorizace.
* **`sr`** -Identifikátor URI prostředku, ke kterému přistupujete.
* **`sig`** -Podpis.

`signature-string` Je vypočítaný algoritmus hash SHA-256 přes identifikátor URI prostředku (**oboru** jak je popsáno v předchozí části) a řetězcovou reprezentaci platnost tokenu vypršela rychlé, oddělených znaky CRLF.

Výpočet hodnoty hash vypadá podobně jako v následujícím kódu pseudo a vrátí hodnotu hash 256-bit/32 bajtů.

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

Token, který obsahuje hodnoty – hodnoty hash tak, aby příjemce může přepočítá hodnoty hash se stejnými parametry, ověření, že vystavitel je k dispozici platný podpisový klíč. 

Identifikátor URI prostředku je úplný identifikátor URI prostředku služby Service Bus, ke kterému je požadována přístup. Například `http://<namespace>.servicebus.windows.net/<entityPath>` nebo `sb://<namespace>.servicebus.windows.net/<entityPath>`; to znamená `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3`. Identifikátor URI musí být [procentuálně zakódovaný](https://msdn.microsoft.com/library/4fkewx0t.aspx).

Autorizační pravidlo sdíleného přístupu pro podpis musí být nakonfigurované na entitu zadanou tento identifikátor URI, nebo jeden z jejích hierarchické nadřazených tříd. Například `http://contoso.servicebus.windows.net/contosoTopics/T1` nebo `http://contoso.servicebus.windows.net` v předchozím příkladu.

SAS token je platný pro všechny prostředky s předponou `<resourceURI>` používané `signature-string`.

## <a name="regenerating-keys"></a>Opakované generování klíčů

Doporučujeme pravidelně obnovovat klíče používané v [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) objektu. Primární a sekundární klíče sloty existují, takže můžete obměňovat klíče postupně. Pokud vaše aplikace se obvykle používá primární klíč, můžete zkopírujte primární klíč do sekundárního klíče slotu a pak znovu vygenerovat primární klíč. Nová hodnota primárního klíče může být nakonfigurována do klientské aplikace, které mít nadále přístup pomocí staré primární klíč v sekundárním slot. Jakmile všichni klienti se aktualizují, můžete obnovit sekundární klíč se nakonec vyřadit staré primární klíč.

Pokud znáte nebo podezření, že dojde k narušení klíč a je nutné odvolat klíče, můžete vygenerovat znovu i [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_PrimaryKey) a [sekundární klíč](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule#Microsoft_ServiceBus_Messaging_SharedAccessAuthorizationRule_SecondaryKey) z [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule), jejich náhradu za nové klíče. Tento postup zruší platnost všech tokenů, které jsou podepsané pomocí starého klíče.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Ověření pomocí sdíleného přístupového podpisu se Service Bus

Následujícím způsobem popsané scénáře patří konfigurace autorizačních pravidel, generování tokenů SAS a autorizaci klientů.

Pro úplný pracovní ukázkové aplikace Service Bus, která znázorňuje konfiguraci a použití SAS autorizace, naleznete v tématu [sdílený přístupový podpis ověřování pomocí služby Service Bus](http://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Související ukázky, která ukazuje použití SAS autorizační pravidla, která je nakonfigurovaná na obory názvů nebo témata k zabezpečení odběry služby Service Bus je k dispozici tady: [ověřování pomocí sdíleného přístupového podpisu (SAS) se odběry služby Service Bus](http://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Přístup sdíleného přístup autorizačních pravidel na entitu

Pomocí knihovny služby Service Bus rozhraní .NET Framework, můžete získat přístup k [Microsoft.ServiceBus.Messaging.SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) nakonfigurovaného na frontu služby Service Bus nebo téma prostřednictvím objektu [autorizačních pravidel](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) kolekce z odpovídajících [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) nebo [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

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

## <a name="use-shared-access-signature-authorization"></a>Použít sdílený přístupový podpis autorizace

Aplikace pomocí sady Azure .NET SDK s knihovnami .NET služby Service Bus můžete použít autorizace SAS prostřednictvím [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) třídy. Následující kód ukazuje použití poskytovatele tokenu, kterého pro odesílání zpráv do fronty služby Service Bus. Alternativou k použití ukázali, že můžete také předat dříve vystaveného tokenu metoda objektu factory zprostředkovatele tokenu.

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

Poskytovatele tokenu, kterého můžete také použít přímo pro vydávání tokenů předat jiným klientům. 

Připojovací řetězce můžou obsahovat název pravidla (*SharedAccessKeyName*) a klíč pravidlo (*SharedAccessKey*) nebo dříve vystaveného tokenu (*SharedAccessSignature*). Když jsou k dispozici v připojovacím řetězci předány žádné konstruktor nebo výrobní metoda přijímá připojovací řetězec, je automaticky vytvořen a vyplní Poskytovatel tokenu SAS.

Mějte na paměti, aby používal autorizace SAS se předávací službu Service Bus, můžete použít klíče SAS, které jsou nakonfigurované na oboru názvů služby Service Bus. Pokud vytvoříte explicitně předávací službu v oboru názvů ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) s [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)) objektu, můžete nastavit pravidla SAS pro propojení. Autorizace SAS pomocí odběrů služby Service Bus, můžete použít klíče SAS, které jsou nakonfigurované na oboru názvů služby Service Bus nebo téma.

## <a name="use-the-shared-access-signature-at-http-level"></a>Použít sdílený přístupový podpis (na úrovni protokolu HTTP)

Teď, když víte, jak vytvořit sdílené přístupové podpisy pro všechny entity ve službě Service Bus, jste připraveni k provedení metody POST protokolu HTTP:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
``` 

Nezapomeňte, že tento postup funguje pro všechno, co. SAS můžete vytvořit pro fronty, tématu nebo odběru.

Pokud dáte odesílatele nebo klienta tokenu SAS, nebudou mít klíč přímo a k jeho získání-the-hash, nejde vrátit. V důsledku toho budete mít kontrolu nad co získají přístup a jak dlouho. Třeba mít na paměti je, že pokud změníte primární klíč v zásadách, nejsou zneplatněny žádné sdílené přístupové podpisy vytvořených z ní.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Použít sdílený přístupový podpis (na úrovni protokolu AMQP)

V předchozí části jste viděli, jak pomocí tokenu SAS pomocí požadavku HTTP POST pro odesílání dat do služby Service Bus. Jak už víte, můžete přístup k Service Bus pomocí pokročilé řízení front zpráv protokolu (AMQP), který je protokol upřednostňované používat z důvodů výkonu v mnoha scénářích. Použití tokenu SAS pomocí AMQP je popsaný v dokumentu [AMQP Claim-Based zabezpečení verze 1.0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) , který je v pracovní draft od 2013, ale dobře podporovaných službou Azure ještě dnes.

Před zahájením odesílání dat do služby Service Bus, musí vydavatele odeslat token SAS uvnitř zprávu AMQP jasně definovaných AMQP uzel s názvem **$cbs** (zobrazí se jako "speciální" služba používá k získání a ověření všech SAS fronty tokeny). Musíte zadat vydavatele **ReplyTo** pole uvnitř zprávy protokolu AMQP; Toto je uzel, ve kterém služba reaguje na vydavatele s výsledkem ověření tokenu (jednoduchý požadavek/odpověď vzor mezi vydavateli a služby ). Tento uzel odpovědi se vytvoří "v reálném čase," mluvit o "dynamické vytváření vzdálený uzel", jak je popsáno ve specifikaci protokolu AMQP 1.0. Když zkontrolujete, že je platný SAS token, můžete vydavatele dopředu a začít odesílat data do služby.

Následující kroky ukazují, jak odeslat token SAS pomocí protokolu AMQP [AMQP.Net Lite](https://github.com/Azure/amqpnetlite) knihovny. To je užitečné, pokud nemůžete použít oficiální sady SDK služby Service Bus (například na WinRT, .net Compact Framework a .net Micro Framework a Mono) vývoj v jazyce C\#. Samozřejmě, tato knihovna je užitečné porozumět zabezpečení jak na základě deklarací identity funguje na úrovni protokolu AMQP, protože jste viděli, jak to funguje na úrovni protokolu HTTP (s požadavek HTTP POST a token SAS odeslaný v hlavičce "Autorizace"). Pokud není nutné tyto znalosti o AMQP, můžete použít oficiální sady SDK služby Service Bus s .net Framework aplikace, které bude to za vás.

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

`PutCbsToken()` Metoda přijímá *připojení* (instance třídy připojení AMQP podle [AMQP .NET Lite knihovny](https://github.com/Azure/amqpnetlite)), která představuje připojení TCP do služby a *sasToken* parametr, který je SAS token k odeslání. 

> [!NOTE]
> Je důležité, že se připojení vytvoří s **ověřovací mechanismus SASL nastavena na ANONYMNÍ** (a ne výchozí prostý pomocí uživatelského jména a hesla při není nutné odeslat SAS token).
> 
> 

V dalším kroku vydavatele vytvoří dva odkazy AMQP pro SAS token odesílání a příjem odpovědí (výsledek ověření tokenu) ze služby.

Zpráva protokolu AMQP obsahuje sadu vlastností a další informace než jednoduché zprávy. SAS token je text zprávy (pomocí jeho konstruktoru). **"ReplyTo"** je nastavena na název uzlu pro příjem výsledek ověřování na odkaz příjemce (jeho název můžete změnit Pokud chcete, a vytvoří se dynamicky podle služby). Poslední tři aplikace/vlastní vlastnosti se používají ve službě k označení, jaký druh operace má spustit. Jak je popsáno ve specifikaci koncept CBS, musí být **název operace** ("put-token"), **typ tokenu** (v tomto případě `servicebus.windows.net:sastoken`) a **"název"Cílováskupina** ke kterému se vztahuje token (celé entity).

Po odeslání tokenu SAS na odkaz odesílatele, musí vydavatele čtení odpovědi na odkaz příjemce. Odpověď je jednoduchý AMQP zprávu s vlastností aplikace s názvem **"kód stavu"** , který může obsahovat stejné hodnoty jako stavový kód HTTP.

## <a name="rights-required-for-service-bus-operations"></a>Práva potřebná pro operace služby Service Bus

V následující tabulce jsou uvedeny přístupová práva potřebná pro různé operace s prostředky služby Service Bus.

| Operace | Požadované deklarace identity | Deklarace identity oboru |
| --- | --- | --- |
| **Namespace** | | |
| Konfigurovat autorizační pravidlo oboru názvů |Spravovat |Všechny adresy oboru názvů |
| **Služba registru** | | |
| Zobrazit výčet zásad privátní |Spravovat |Všechny adresy oboru názvů |
| Zahájit naslouchání na obor názvů |Naslouchat |Všechny adresy oboru názvů |
| Odesílání zpráv do naslouchacího procesu na obor názvů |Odeslat |Všechny adresy oboru názvů |
| **fronty** | | |
| Vytvoření fronty |Spravovat |Všechny adresy oboru názvů |
| Odstranění fronty |Spravovat |Všechny adresy platná fronta |
| Zobrazení výčtu fronty |Spravovat |Uložený prostředky/front |
| Získat popis fronty |Spravovat |Všechny adresy platná fronta |
| Konfigurovat autorizační pravidlo pro frontu |Spravovat |Všechny adresy platná fronta |
| Odeslat do fronty |Odeslat |Všechny adresy platná fronta |
| Příjem zpráv z fronty |Naslouchat |Všechny adresy platná fronta |
| Zrušení nebo celé zprávy po přijetí zprávy v režimu neboli peek-lock |Naslouchat |Všechny adresy platná fronta |
| Odložit zprávu pro pozdější načtení |Naslouchat |Všechny adresy platná fronta |
| Nedoručené zprávy zprávy |Naslouchat |Všechny adresy platná fronta |
| Získat informace o stavu přidružené k relaci fronty zpráv |Naslouchat |Všechny adresy platná fronta |
| Nastavit stav přidružený k relaci fronty zpráv |Naslouchat |Všechny adresy platná fronta |
| Plán zprávu pro pozdější doručení; například [ScheduleMessageAsync()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Naslouchat | Všechny adresy platná fronta
| **Téma** | | |
| Vytvoření tématu |Spravovat |Všechny adresy oboru názvů |
| Odstranit téma |Spravovat |Libovolné platné tématu adresy |
| Zobrazení výčtu témata |Spravovat |Uložený prostředky a témat |
| Získat popis tématu |Spravovat |Libovolné platné tématu adresy |
| Konfigurovat autorizační pravidlo pro téma |Spravovat |Libovolné platné tématu adresy |
| Odeslání do tématu |Odeslat |Libovolné platné tématu adresy |
| **Předplatné** | | |
| Vytvoření odběru |Spravovat |Všechny adresy oboru názvů |
| Odstranit předplatné |Spravovat |.. /myTopic/Subscriptions/mySubscription |
| Zobrazení výčtu odběrů |Spravovat |.. / myTopic/předplatná |
| Získat popis odběru |Spravovat |.. /myTopic/Subscriptions/mySubscription |
| Zrušení nebo celé zprávy po přijetí zprávy v režimu neboli peek-lock |Naslouchat |.. /myTopic/Subscriptions/mySubscription |
| Odložit zprávu pro pozdější načtení |Naslouchat |.. /myTopic/Subscriptions/mySubscription |
| Nedoručené zprávy zprávy |Naslouchat |.. /myTopic/Subscriptions/mySubscription |
| Získat informace o stavu přidružené k tématu relace |Naslouchat |.. /myTopic/Subscriptions/mySubscription |
| Nastavit stav přidružený k tématu relace |Naslouchat |.. /myTopic/Subscriptions/mySubscription |
| **pravidla** | | |
| Vytvoření pravidla |Spravovat |.. /myTopic/Subscriptions/mySubscription |
| Odstranit pravidlo |Spravovat |.. /myTopic/Subscriptions/mySubscription |
| Zobrazení výčtu pravidel |Spravovat nebo naslouchání |.. /myTopic/Subscriptions/mySubscription/Rules 

## <a name="next-steps"></a>Další postup

Pokud se o přenosu zpráv přes Service Bus chcete dozvědět víc, pročtěte si následující témata.

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Jak používat fronty Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com