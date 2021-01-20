---
title: Řízení přístupu Azure Service Bus pomocí sdílených přístupových podpisů
description: Přehled řízení přístupu Service Bus pomocí podpisů sdíleného přístupu najdete v podrobnostech o autorizaci SAS pomocí Azure Service Bus.
ms.topic: article
ms.date: 01/19/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 6bdc167c437a79d609db25a2e3c48b71e0a748b2
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98598817"
---
# <a name="service-bus-access-control-with-shared-access-signatures"></a>Řízení přístupu Service Bus pomocí sdílených přístupových podpisů

Tento článek popisuje, jak fungují *sdílené přístupové podpisy* (SAS), jak fungují, a jak je používat v nezávislá platforem.

SAS chrání přístup k Service Bus na základě autorizačních pravidel. Ty jsou nakonfigurovány buď v oboru názvů, nebo v entitě zasílání zpráv (ve frontě nebo tématu). Autorizační pravidlo má název, je spojeno s konkrétními právy a přináší dvojici kryptografických klíčů. Název a klíč pravidla použijete prostřednictvím sady Service Bus SDK nebo ve vlastním kódu pro vygenerování tokenu SAS. Klient pak může token předat Service Bus, aby prokáže autorizaci pro požadovanou operaci.

> [!NOTE]
> Azure Service Bus podporuje autorizaci přístupu k oboru názvů Service Bus a jeho entitám pomocí Azure Active Directory (Azure AD). Ověřování uživatelů nebo aplikací pomocí tokenu OAuth 2,0 vráceného službou Azure AD poskytuje vynikající zabezpečení a usnadňuje použití přes sdílené přístupové podpisy (SAS). V případě Azure AD není nutné ukládat tokeny do kódu a ohrozit potenciální ohrožení zabezpečení.
>
> Microsoft doporučuje používat Azure AD s aplikacemi Azure Service Bus, pokud je to možné. Další informace najdete v následujících článcích:
> - [Ověřování a autorizace aplikace s Azure Active Directory pro přístup k Azure Service BUSM entitám](authenticate-application.md).
> - [Ověření spravované identity pomocí Azure Active Directory pro přístup k prostředkům Azure Service Bus](service-bus-managed-service-identity.md)

## <a name="overview-of-sas"></a>Přehled SAS

Sdílené přístupové podpisy jsou mechanizmus ověřování založeného na deklaracích využívajících jednoduché tokeny. Pomocí SAS se klíče nikdy nepředávají na lince. Klíče se používají pro kryptografické podepisování informací, které může služba později ověřit. SAS se dá použít podobně jako uživatelské jméno a schéma hesla, kde je klient v bezprostředním vlastnictví názvu autorizačního pravidla a odpovídajícího klíče. SAS se taky dá použít podobně jako model federovaného zabezpečení, kde klient obdrží token zabezpečení s omezeným přístupem od služby tokenů zabezpečení, aniž by musel mít k dispozici podpisový klíč.

Ověřování SAS v Service Bus je nakonfigurované s pojmenovanými [autorizačními pravidly přístupu](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) , která mají přidružená přístupová práva, a pár primárních a sekundárních kryptografických klíčů. Klíče jsou 256 hodnoty v reprezentaci base64. Pravidla můžete konfigurovat na úrovni oboru [názvů, při Service Busch](../azure-relay/relay-what-is-it.md)přenosech, [frontách](service-bus-messaging-overview.md#queues)a [tématech](service-bus-messaging-overview.md#topics).

Token [sdíleného přístupového podpisu](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) obsahuje název zvoleného autorizačního pravidla, identifikátor URI prostředku, ke kterému se má získat přístup, okamžitá platnost a kryptografický podpis HMAC-SHA256 počítaný přes tato pole pomocí primárního nebo sekundárního kryptografického klíče zvoleného autorizačního pravidla.

## <a name="shared-access-authorization-policies"></a>Zásady autorizace sdíleného přístupu

Každý Service Bus obor názvů a každá Service Bus entita má nastavené zásady autorizace sdíleného přístupu z pravidel. Zásady na úrovni oboru názvů platí pro všechny entity v oboru názvů bez ohledu na jejich jednotlivé konfigurace zásad.

U každého pravidla zásad autorizace se rozhodujete o třech údajích, které se týkají **názvu**, **oboru** a **práv**. **Název** je pouze to; jedinečný název v rámci tohoto oboru. Obor je dostatečně snadný: Jedná se o identifikátor URI daného prostředku. V případě oboru názvů Service Bus obor je plně kvalifikovaný název domény (FQDN), například `https://<yournamespace>.servicebus.windows.net/` .

Práva, která jsou odvozená pravidlem zásad, můžou být kombinací:

* Send – uděluje právo odesílat zprávy entitě.
* ' Listen ' – udělí právo přijímat (Queue, Subscriptions) a všechny související zpracování zpráv.
* ' Manage ' – uděluje práva na správu topologie oboru názvů, včetně vytváření a odstraňování entit

Právo spravovat zahrnuje práva Send a Receive.

Zásady oboru názvů nebo entit můžou obsahovat až 12 autorizačních pravidel přístupu a poskytují prostor pro tři sady pravidel, z nichž každý pokrývá základní práva a kombinaci odesílání a naslouchat. Tato mezní hodnota je podtržená, protože úložiště zásad SAS není určené pro uživatele nebo úložiště účtů služeb. Pokud vaše aplikace potřebuje udělit přístup k Service Bus na základě identit uživatelů nebo služeb, měli byste implementovat službu tokenů zabezpečení, která vydává tokeny SAS po ověření a kontrole přístupu.

Autorizačnímu pravidlu je přiřazen *primární klíč* a *sekundární klíč*. Jedná se o kryptograficky silné klíče. Neztratí je, nebo je nepoužívejte – budou vždy k dispozici v [Azure Portal][Azure portal]. Můžete použít buď vygenerované klíče, a můžete je kdykoli znovu vygenerovat. Pokud znovu vygenerujete nebo změníte klíč v zásadě, všechny dříve vydané tokeny na základě tohoto klíče se okamžitě stanou neplatnými. Probíhající připojení vytvořená na základě těchto tokenů ale budou fungovat i nadále, dokud nevyprší platnost tokenu.

Když vytvoříte obor názvů Service Bus, pro obor názvů se automaticky vytvoří pravidlo zásad s názvem **RootManageSharedAccessKey** . Tato zásada má oprávnění ke správě pro celý obor názvů. Doporučuje se toto pravidlo zakládat jako **kořenový** účet pro správu a nepoužívat ho v aplikaci. Další pravidla zásad můžete vytvořit na kartě **Konfigurace** pro obor názvů na portálu prostřednictvím PowerShellu nebo rozhraní příkazového řádku Azure CLI.

## <a name="best-practices-when-using-sas"></a>Osvědčené postupy při používání SAS
Pokud používáte sdílené přístupové podpisy ve svých aplikacích, je nutné mít na paměti dvě možná rizika:

- Pokud se SAS nevrátí, může ho použít kdokoli, kdo ho získá, což může potenciálně ohrozit vaše Service Bus prostředky.
- Pokud platnost SAS poskytnutá klientské aplikaci vyprší a aplikace nebude moct z vaší služby načíst nové přidružení zabezpečení, může být narušena funkčnost aplikace.

Následující doporučení pro použití sdílených přístupových podpisů pomáhají zmírnit tato rizika:

- **Klienti automaticky Obnovují SAS v případě potřeby**: klienti by měli obnovit správné zabezpečení SAS před vypršením platnosti, aby bylo možné pokusy o opakování, pokud služba poskytující SAS není k dispozici. Pokud se má vaše SAS použít pro malý počet okamžitých, krátkodobých operací, které se mají dokončit v rámci období vypršení platnosti, může být zbytečné, protože se neočekává obnovení SAS. Pokud ale máte klienta, který provádí zpracování požadavků prostřednictvím SAS, pak se možnost vypršení platnosti stane hrát. Klíčovým aspektem je vyrovnávání nutnosti, aby SAS bylo krátkodobé (jak bylo uvedeno dříve), aby se zajistilo, že klient požaduje prodloužení na včas. (aby nedocházelo k přerušení, protože platnost SAS vypršela před úspěšným obnovením).
- **Buďte opatrní při počátečním čase SAS**: Pokud nastavíte čas zahájení pro SAS na **nyní**, pak se chyby můžou považovat za občasně po dobu prvních pár minut. V části Obecné nastavte čas spuštění aspoň 15 minut v minulosti. Nebo ji vůbec nenastavte, což zajistí, že bude platit okamžitě ve všech případech. Totéž platí i pro čas vypršení platnosti. Mějte na paměti, že v obou směrech každé žádosti můžete sledovat až 15 minut hodinového zkosení. 
- **Být specifické pro přístup k prostředku**: osvědčeným postupem zabezpečení je poskytnout uživateli minimální požadovaná oprávnění. Pokud uživatel potřebuje k jedné entitě oprávnění ke čtení, přidělte jim přístup pro čtení k této jedné entitě, a ne přístup pro čtení, zápis a odstranění u všech entit. Pomáhá také snížit škodu v případě ohrožení zabezpečení SAS, protože SAS má méně energie jako útočník.
- **Nepoužívejte vždy SAS**: někdy rizika spojená s určitou operací s vaším Event Hubs převažují nad výhodami SAS. U takových operací vytvořte službu střední vrstvy, která po ověření obchodního pravidla, ověřování a auditování zapisuje do vaší Event Hubs.
- **Vždy používat protokol HTTPS**: k vytvoření nebo distribuci SAS použijte vždy protokol HTTPS. Pokud se SAS předává přes protokol HTTP a zachytává, útočník, který provádí připojení prostředníkem, je schopen přečíst SAS a pak ho použít stejným způsobem jako zamýšlený uživatel, potenciálně ohrozit citlivá data nebo umožnit poškození dat uživatelem se zlými úmysly.

## <a name="configuration-for-shared-access-signature-authentication"></a>Konfigurace pro ověřování sdíleného přístupového podpisu

Pravidlo [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) můžete nakonfigurovat na Service Bus obory názvů, fronty nebo témata. Konfigurace [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) v předplatném Service Bus v tuto chvíli není podporovaná, ale pro zabezpečení přístupu k předplatným můžete použít pravidla konfigurovaná v oboru názvů nebo tématu. Pracovní ukázku, která tento postup znázorňuje, najdete v ukázce [použití ověřování pomocí sdíleného přístupového podpisu (SAS) s ukázkami Předplatného Service Bus](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c) .

![SAS](./media/service-bus-sas/service-bus-namespace.png)

Na tomto obrázku se autorizační pravidla *manageRuleNS*, *sendRuleNS* a *ListenRuleNS* vztahují na front-Q1 i na téma T1, zatímco *listenRuleQ* a *SendRuleQ* platí jenom pro zařazení do fronty F1 a *sendRuleT* platí jenom pro téma T1.

## <a name="generate-a-shared-access-signature-token"></a>Generování tokenu sdíleného přístupového podpisu

Každý klient, který má přístup k názvu autorizačního pravidla a jeden z jeho podpisových klíčů, může generovat token SAS. Token je vygenerován vytvořením řetězce v následujícím formátu:

```
SharedAccessSignature sig=<signature-string>&se=<expiry>&skn=<keyName>&sr=<URL-encoded-resourceURI>
```

- `se` – Okamžité vypršení platnosti tokenu Celé číslo odráží sekundy od epocha dne `00:00:00 UTC` 1. ledna 1970 (UNIX epocha), až vyprší platnost tokenu.
- `skn` – Název autorizačního pravidla.
- `sr` – Identifikátor URI zakódovaný pro prostředek, který je k dispozici.
- `sig` -Podpis HMACSHA256 kódovaný v URL. Výpočet hodnoty hash vypadá podobně jako následující pseudo kód a vrací hodnotu Base64 nezpracovaného binárního výstupu.

    ```
    urlencode(base64(hmacsha256(urlencode('https://<yournamespace>.servicebus.windows.net/') + "\n" + '<expiry instant>', '<signing key>')))
    ```

Zde je příklad kódu jazyka C# pro vygenerování tokenu SAS:

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

> [!IMPORTANT]
> Příklady generování tokenu SAS pomocí různých programovacích jazyků najdete v tématu [vygenerování tokenu SAS](/rest/api/eventhub/generate-sas-token). 


Token obsahuje hodnoty, které nejsou hash, aby příjemce mohl znovu vypočítat hodnotu hash se stejnými parametry a ověřit, zda má Vystavitel k dispozici platný podpisový klíč.

Identifikátor URI prostředku je úplný identifikátor URI Service Bus prostředku, ke kterému je nárok na přístup. Například `http://<namespace>.servicebus.windows.net/<entityPath>` nebo `sb://<namespace>.servicebus.windows.net/<entityPath>` ; to znamená `http://contoso.servicebus.windows.net/contosoTopics/T1/Subscriptions/S3` . 

**Identifikátor URI musí být [kódovaný v procentech](/dotnet/api/system.web.httputility.urlencode).**

Autorizační pravidlo sdíleného přístupu použité pro podepisování musí být nakonfigurováno pro entitu určenou tímto identifikátorem URI nebo jedním z jeho hierarchických nadřazených prvků. Například `http://contoso.servicebus.windows.net/contosoTopics/T1` nebo `http://contoso.servicebus.windows.net` v předchozím příkladu.

Token SAS je platný pro všechny prostředky s předponou `<resourceURI>` použitou v `signature-string` .


## <a name="regenerating-keys"></a>Opětovné generování klíčů

Doporučuje se pravidelně znovu vygenerovat klíče používané v objektu [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) . Existují sloty primárního a sekundárního klíče, abyste mohli postupně otáčet klíče. Pokud vaše aplikace obecně používá primární klíč, můžete primární klíč zkopírovat do pozice sekundárního klíče a pak znovu vygenerovat primární klíč. Nová hodnota primárního klíče se pak dá nakonfigurovat na klientské aplikace, které mají pokračující přístup pomocí starého primárního klíče v sekundární patici. Po aktualizaci všech klientů můžete znovu vygenerovat sekundární klíč, abyste mohli nakonec vyřadit starý primární klíč.

Pokud víte nebo máte podezření, že došlo k ohrožení bezpečnosti klíče, a musíte klíče odvolat, můžete znovu vygenerovat jak klíč vlastnosti [PrimaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) , tak i [SecondaryKey](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)a nahradit je novými klíči. Tato procedura zruší platnost všech tokenů podepsaných starými klíči.

## <a name="shared-access-signature-authentication-with-service-bus"></a>Ověřování pomocí sdíleného přístupového podpisu s Service Bus

Zde popsané scénáře zahrnují konfiguraci autorizačních pravidel, generování tokenů SAS a autorizaci klientů.

Úplnou pracovní ukázku aplikace Service Bus, která ilustruje konfiguraci a používá autorizaci SAS, najdete v tématu [ověřování pomocí sdíleného přístupového podpisu s Service Bus](https://code.msdn.microsoft.com/Shared-Access-Signature-0a88adf8). Související ukázka, která demonstruje použití autorizačních pravidel SAS nakonfigurovaných v oborech názvů nebo tématech k zabezpečení předplatného Service Bus, najdete tady: [použití ověřování pomocí sdíleného přístupového podpisu (SAS) s Předplatnými Service Bus](https://code.msdn.microsoft.com/Using-Shared-Access-e605b37c).

## <a name="access-shared-access-authorization-rules-on-an-entity"></a>Přístup k autorizačním pravidlům sdíleného přístupu pro entitu

Pomocí knihoven Service Bus .NET Framework můžete přistupovat k objektu [Microsoft. ServiceBus. Messaging. SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) nakonfigurovanému ve frontě Service Bus nebo tématu prostřednictvím kolekce [autorizačních pravidel](/dotnet/api/microsoft.servicebus.messaging.authorizationrules) v odpovídajícím [QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription) nebo [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription).

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

## <a name="use-shared-access-signature-authorization"></a>Použít autorizaci sdíleného přístupového podpisu

Aplikace používající sadu Azure .NET SDK s Service Bus knihoven .NET můžou používat autorizaci SAS přes třídu [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) . Následující kód ilustruje použití poskytovatele tokenů k posílání zpráv do fronty Service Bus. Alternativu k využití, které vidíte tady, můžete také předat dříve vydaný token do metody factory poskytovatele tokenů.

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

Poskytovatele tokenu můžete také použít přímo pro vydávání tokenů pro předávání jiným klientům.

Připojovací řetězce můžou zahrnovat název pravidla (*SharedAccessKeyName*) a klíč pravidla (*SharedAccessKey*) nebo dříve vydaný token (*SharedAccessSignature*). Pokud jsou k dispozici v připojovacím řetězci předanému libovolnému konstruktoru nebo metodě továrny přijímající připojovací řetězec, je poskytovatel tokenu SAS automaticky vytvořen a vyplněn.

Všimněte si, že k použití autorizace pomocí SAS s Service Bus Relay můžete použít klíče SAS nakonfigurované v oboru názvů Service Bus. Pokud vytvoříte předávací objekt v oboru názvů ([NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) s objektem [RelayDescription](/dotnet/api/microsoft.servicebus.messaging.relaydescription)), můžete pro něj nastavit pravidla SAS jenom pro tento přenos. Pokud chcete použít autorizaci SAS s předplatnými Service Bus, můžete použít klíče SAS nakonfigurované na Service Bus oboru názvů nebo v tématu.

## <a name="use-the-shared-access-signature-at-http-level"></a>Použití sdíleného přístupového podpisu (na úrovni HTTP)

Teď, když víte, jak vytvořit sdílené přístupové podpisy pro všechny entity v Service Bus, jste připraveni provést příspěvek HTTP:

```http
POST https://<yournamespace>.servicebus.windows.net/<yourentity>/messages
Content-Type: application/json
Authorization: SharedAccessSignature sr=https%3A%2F%2F<yournamespace>.servicebus.windows.net%2F<yourentity>&sig=<yoursignature from code above>&se=1438205742&skn=KeyName
ContentType: application/atom+xml;type=entry;charset=utf-8
```

Pamatujte si, že to funguje pro všechno. SAS můžete vytvořit pro frontu, téma nebo odběr.

Pokud odesilateli nebo klientovi poskytnete token SAS, nemá klíč přímo a nemůže vrátit hodnotu hash, aby ji získal. V takovém případě máte kontrolu nad tím, k čemu mají přístup, a za jak dlouho. Je důležité si uvědomit, že pokud změníte primární klíč v zásadě, všechny sdílené přístupové podpisy, které jsou z něho vytvořené, se zruší.

## <a name="use-the-shared-access-signature-at-amqp-level"></a>Použití sdíleného přístupového podpisu (na úrovni AMQP)

V předchozí části jste viděli, jak použít token SAS s požadavkem HTTP POST k odesílání dat do Service Bus. Jak víte, můžete k Service Bus přistupovat pomocí rozšířený protokol řízení front zpráv (AMQP) (AMQP), které jsou preferovaným protokolem k použití z důvodů výkonu, a to v mnoha scénářích. Použití tokenu SAS v AMQP je popsané v dokumentu [AMQP Claim-Based Security verze 1,0](https://www.oasis-open.org/committees/download.php/50506/amqp-cbs-v1%200-wd02%202013-08-12.doc) , který je v pracovním konceptu od 2013, ale podporuje ho Azure ještě dnes.

Předtím, než začnete odesílat data do Service Bus, musí vydavatel odeslat token SAS v rámci zprávy AMQP do dobře definovaného uzlu AMQP s názvem **$CBS** (můžete ho zobrazit jako "speciální" frontu, kterou služba používá k získání a ověření všech tokenů SAS. Vydavatel musí ve zprávě AMQP zadat pole **ReplyTo** ; Jedná se o uzel, ve kterém služba reaguje na vydavatele s výsledkem ověření tokenu (jednoduchý vzor žádosti a odpovědi mezi vydavatelem a službou). Tento uzel odpovědi se vytvoří průběžně, mluví o dynamickém vytváření vzdáleného uzlu, jak je popsáno ve specifikaci AMQP 1,0. Po kontrole platnosti tokenu SAS může vydavatel přejít do služby a začít posílat data.

Následující kroky ukazují, jak odeslat token SAS pomocí protokolu AMQP pomocí knihovny [AMQP.NET Lite](https://github.com/Azure/amqpnetlite) . To je užitečné, pokud nemůžete použít oficiální sadu Service Bus SDK (například na WinRT, prostředí .NET Compact Framework, .NET Micro Framework a mono) vývoj v jazyce C \# . Tato knihovna je samozřejmě užitečná pro pochopení, jak funguje zabezpečení založené na deklaracích na úrovni AMQP, jak jste viděli, jak funguje na úrovni HTTP (s požadavkem HTTP POST a tokenem SAS odesílaným v hlavičce "Authorization"). Pokud nepotřebujete takové hloubkové znalosti o AMQP, můžete použít oficiální sadu Service Bus SDK s .NET Framework aplikacemi, které to provede za vás.

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

`PutCbsToken()`Metoda přijímá *připojení* (instance třídy připojení AMQP, která je poskytována [knihovnou AMQP .NET Lite](https://github.com/Azure/amqpnetlite)), která představuje připojení TCP ke službě a parametr *sasToken* , který je token SAS k odeslání.

> [!NOTE]
> Je důležité, aby se připojení vytvořilo pomocí **mechanismu ověřování SASL nastaveného na anonymní** (a ne jako výchozí jednoduché s uživatelským jménem a heslem, které se používá, pokud nepotřebujete odeslat token SAS).
>
>

V dalším kroku vytvoří Vydavatel dvě AMQP odkazy pro odeslání tokenu SAS a příjem odpovědi (výsledek ověření tokenu) ze služby.

Zpráva AMQP obsahuje sadu vlastností a další informace než jednoduchá zpráva. Token SAS je tělo zprávy (pomocí jejího konstruktoru). Vlastnost **"ReplyTo"** je nastavená na název uzlu pro příjem výsledku ověření na odkazu přijímače (jeho název můžete změnit, pokud chcete, a dynamicky ho vytvoří služba). Poslední tři vlastnosti aplikace nebo vlastní aplikace jsou službou používány k označení toho, jaký druh operace je nutné provést. Jak je popsáno ve specifikaci konceptu CBS, musí se jednat o **název operace** ("Put-token"), **typ tokenu** (v tomto případě a `servicebus.windows.net:sastoken` ) a **"název" cílové skupiny** , na kterou se vztahuje token (celá entita).

Po odeslání tokenu SAS na odkaz odesílatele musí vydavatel přečíst odpověď na odkazu příjemce. Odpověď je jednoduchá AMQP zpráva s vlastností aplikace s názvem **"stavový kód"** , která může obsahovat stejné hodnoty jako stavový kód HTTP.

## <a name="rights-required-for-service-bus-operations"></a>Práva požadovaná pro operace Service Bus

Následující tabulka uvádí přístupová práva požadovaná pro různé operace týkající se Service Busch prostředků.

| Operace | Požadovaná deklarace identity | Rozsah deklarací identity |
| --- | --- | --- |
| **Obor názvů** | | |
| Konfigurace autorizačního pravidla pro obor názvů |Spravovat |Libovolná adresa oboru názvů |
| **Registr služby** | | |
| Zobrazení výčtu privátních zásad |Spravovat |Libovolná adresa oboru názvů |
| Zahájení naslouchání na oboru názvů |Naslouchat |Libovolná adresa oboru názvů |
| Odesílání zpráv do naslouchacího procesu v oboru názvů |Odeslat |Libovolná adresa oboru názvů |
| **Fronta** | | |
| Vytvoření fronty |Spravovat |Libovolná adresa oboru názvů |
| Odstranění fronty |Spravovat |Libovolná platná adresa fronty |
| Zobrazení výčtu front |Spravovat |/Queues $Resources |
| Získat popis fronty |Spravovat |Libovolná platná adresa fronty |
| Konfigurace autorizačního pravidla pro frontu |Spravovat |Libovolná platná adresa fronty |
| Odeslat do fronty |Odeslat |Libovolná platná adresa fronty |
| Přijímání zpráv z fronty |Naslouchat |Libovolná platná adresa fronty |
| Opuštění nebo ukončení zpráv po přijetí zprávy v režimu prohlížení zámku |Naslouchat |Libovolná platná adresa fronty |
| Odložit zprávu pro pozdější načtení |Naslouchat |Libovolná platná adresa fronty |
| Zpráva nedoručených zpráv |Naslouchat |Libovolná platná adresa fronty |
| Získání stavu přidruženého k relaci fronty zpráv |Naslouchat |Libovolná platná adresa fronty |
| Nastavení stavu přidruženého k relaci fronty zpráv |Naslouchat |Libovolná platná adresa fronty |
| Naplánování zprávy pro pozdější doručení; například [ScheduleMessageAsync ()](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync#Microsoft_Azure_ServiceBus_QueueClient_ScheduleMessageAsync_Microsoft_Azure_ServiceBus_Message_System_DateTimeOffset_) |Naslouchat | Libovolná platná adresa fronty
| **Téma** | | |
| Vytvoření tématu |Spravovat |Libovolná adresa oboru názvů |
| Odstranění tématu |Spravovat |Jakákoli platná adresa tématu |
| Vyčíslení výčtu témat |Spravovat |/Topics $Resources |
| Získat popis tématu |Spravovat |Jakákoli platná adresa tématu |
| Konfigurace autorizačního pravidla pro téma |Spravovat |Jakákoli platná adresa tématu |
| Odeslat do tématu |Odeslat |Jakákoli platná adresa tématu |
| **Předplatné** | | |
| Vytvoření odběru |Spravovat |Libovolná adresa oboru názvů |
| Odstranění předplatného |Spravovat |.. /myTopic/Subscriptions/mySubscription |
| Zobrazení výčtu předplatných |Spravovat |.. /myTopic/Subscriptions |
| Získat popis předplatného |Spravovat |.. /myTopic/Subscriptions/mySubscription |
| Opuštění nebo ukončení zpráv po přijetí zprávy v režimu prohlížení zámku |Naslouchat |.. /myTopic/Subscriptions/mySubscription |
| Odložit zprávu pro pozdější načtení |Naslouchat |.. /myTopic/Subscriptions/mySubscription |
| Zpráva nedoručených zpráv |Naslouchat |.. /myTopic/Subscriptions/mySubscription |
| Získání stavu přidruženého k relaci tématu |Naslouchat |.. /myTopic/Subscriptions/mySubscription |
| Nastavení stavu přidruženého k relaci tématu |Naslouchat |.. /myTopic/Subscriptions/mySubscription |
| **Pravidla** | | |
| Vytvoření pravidla |Spravovat |.. /myTopic/Subscriptions/mySubscription |
| Odstranění pravidla |Spravovat |.. /myTopic/Subscriptions/mySubscription |
| Zobrazení výčtu pravidel |Spravovat nebo naslouchat |.. /myTopic/Subscriptions/mySubscription/Rules

## <a name="next-steps"></a>Další kroky

Pokud se o přenosu zpráv přes Service Bus chcete dozvědět víc, pročtěte si následující témata.

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Jak používat fronty Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)

[Azure portal]: https://portal.azure.com