---
title: Nejčastější dotazy k Azure Service Bus | Microsoft Docs
description: Odpovědi na některé nejčastější dotazy týkající se Azure Service Bus.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 80809afc9f2a8e8da2f6adecfe916141c4cd3e45
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68278344"
---
# <a name="service-bus-faq"></a>Nejčastější dotazy k Service Bus

Tento článek popisuje některé časté otázky týkající se Microsoft Azure Service Bus. [Nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq/) můžete také navštívit pro obecné ceny a informace o podpoře Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions-about-azure-service-bus"></a>Obecné otázky týkající se Azure Service Bus
### <a name="what-is-azure-service-bus"></a>Co je Azure Service Bus?
[Azure Service Bus](service-bus-messaging-overview.md) je asynchronní cloudová platforma pro zasílání zpráv, která umožňuje odesílat data mezi oddělenými systémy. Microsoft tuto funkci nabízí jako službu, což znamená, že nemusíte hostovat svůj vlastní hardware, abyste ho mohli používat.

### <a name="what-is-a-service-bus-namespace"></a>Co je obor názvů Service Bus?
[Obor názvů](service-bus-create-namespace-portal.md) poskytuje kontejner oboru pro adresování Service Bus prostředků v rámci aplikace. Vytvoření oboru názvů je nezbytné pro použití Service Bus a je jedním z prvních kroků v části Začínáme.

### <a name="what-is-an-azure-service-bus-queue"></a>Co je Azure Service Bus fronta?
[Service Bus fronta](service-bus-queues-topics-subscriptions.md) je entita, ve které jsou uložené zprávy. Fronty jsou užitečné v případě, že máte více aplikací nebo více částí distribuované aplikace, které potřebují vzájemně komunikovat. Fronta je podobná distribučnímu centru v tom, že je přijímáno více produktů (zpráv) a následně odesláno z tohoto umístění.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Co jsou Azure Service Bus témata a předplatná?
Téma se dá vizuálně rozvažovat za frontu a když se používá víc předplatných, bude se jednat o model bohatšího zasílání zpráv. Nástroj pro komunikaci typu 1: n v podstatě. Tento model publikování/předplatného (nebo *Pub/sub*) umožňuje aplikaci, která pošle zprávu do tématu s více předplatnými, aby tuto zprávu přijímalo více aplikacemi.

### <a name="what-is-a-partitioned-entity"></a>Co je dělená entita?
Konvenční frontu nebo téma zpracovává jeden zprostředkovatel zpráv a ukládá se do jednoho úložiště pro zasílání zpráv. Podporováno pouze ve vrstvách Basic a standard pro zasílání zpráv, které jsou rozdělené do [dělené fronty nebo tématu](service-bus-partitioning.md) , jsou zpracovávány pomocí více zprostředkovatelů zpráv a uloženy v několika úložištích zasílání zpráv. Tato funkce znamená, že celková propustnost dělené fronty nebo tématu již není omezena výkonem jediného zprostředkovatele zpráv nebo úložiště pro zasílání zpráv. Kromě toho dočasný výpadek úložiště pro zasílání zpráv nezobrazuje dělenou frontu nebo téma není k dispozici.

Řazení není při použití dělených entit zajištěno. V případě, že je oddíl nedostupný, můžete i nadále odesílat a přijímat zprávy z dalších oddílů.

 Dělené entity už nejsou v [SKU úrovně Premium](service-bus-premium-messaging.md)podporované. 

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Jaké porty potřebuji v bráně firewall otevřít? 
K posílání a přijímání zpráv můžete použít následující protokoly s Azure Service Bus:

- Rozšířený protokol řízení front zpráv (AMQP) (AMQP)
- Protokol SBMP (Service Bus Messaging Protocol)
- HTTP

V následující tabulce najdete Odchozí porty, které musíte otevřít, abyste mohli tyto protokoly používat ke komunikaci s Azure Event Hubs. 

| Protocol | Porty | Podrobnosti | 
| -------- | ----- | ------- | 
| AMQP | 5671 a 5672 | Viz [Průvodce protokolem AMQP](service-bus-amqp-protocol-guide.md) . | 
| SBMP | 9350 až 9354 | Zobrazit [režim připojení](/dotnet/api/microsoft.servicebus.connectivitymode?view=azure-dotnet) |
| HTTP, HTTPS | 80, 443 | 

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Jaké IP adresy potřebuji pro seznam povolených?
Chcete-li najít správné IP adresy pro připojení k seznamu, postupujte podle následujících kroků:

1. Z příkazového řádku spusťte následující příkaz: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Poznamenejte si IP adresu vrácenou `Non-authoritative answer`v. Tato IP adresa je statická. Jediným bodem v čase, který by měl být změněn, je, že obor názvů obnovíte na jiný cluster.

Pokud používáte redundanci zóny pro svůj obor názvů, musíte provést několik dalších kroků: 

1. Nejprve spustíte nástroj nslookup v oboru názvů.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Poznamenejte si název v části **nesměrodatná odpověď** , která je v jednom z následujících formátů: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Spusťte nástroj nslookup pro každý z nich s příponami S1, S2 a S3 k získání IP adres všech tří instancí spuštěných ve třech zónách dostupnosti. 


## <a name="best-practices"></a>Osvědčené postupy
### <a name="what-are-some-azure-service-bus-best-practices"></a>Co jsou některé Azure Service Bus osvědčené postupy?
Projděte si [osvědčené postupy pro zlepšení výkonu pomocí Service Bus][Best practices for performance improvements using Service Bus] – Tento článek popisuje, jak optimalizovat výkon při výměně zpráv.

### <a name="what-should-i-know-before-creating-entities"></a>Co mám vědět před vytvořením entit?
Následující vlastnosti fronty a tématu jsou neměnné. Při zřizování entit Vezměte v úvahu toto omezení, protože tyto vlastnosti nelze upravovat bez nutnosti vytvořit novou náhradní entitu.

* Dělení
* Relace
* Vyhledávání duplicit
* Entita Express

## <a name="pricing"></a>Ceny
Tato část obsahuje odpovědi na některé nejčastější dotazy týkající se Service Bus cenové struktury.

V článku [Service Bus ceny a fakturace](https://azure.microsoft.com/pricing/details/service-bus/) se vysvětlují měřiče fakturace v Service Bus. Konkrétní informace o možnostech cenového Service Bus najdete v článku [Podrobnosti o cenách Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

Pro obecné informace o cenách Azure můžete také navštívit [Nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq/) . 

### <a name="how-do-you-charge-for-service-bus"></a>Jak se účtují poplatky za Service Bus?
Úplné informace o Service Bus cenách najdete v článku [o cenách Service Bus][Pricing overview]. Kromě uvedených cen se vám budou účtovat přenosy dat pro výstup mimo datové centrum, ve kterém se vaše aplikace zřídí.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Jaké využití Service Bus podléhá přenosu dat? Co není?
Jakýkoli přenos dat v rámci dané oblasti Azure se poskytuje zdarma, stejně jako všechny příchozí přenosy dat. Přenos dat mimo oblast podléhá poplatkům za výstup, který najdete [tady](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>Účtuje se Service Bus za úložiště?
Ne, Service Bus neúčtuje za úložiště. Existuje však kvóta, která omezuje maximální množství dat, která lze uchovávat pro jednotlivé fronty nebo témata. Podívejte se na další Nejčastější dotazy.

## <a name="quotas"></a>Kvóty

Seznam limitů a kvót Service Bus najdete v tématu [Přehled kvót Service Bus][Quotas overview].

### <a name="does-service-bus-have-any-usage-quotas"></a>Má Service Bus nějaké kvóty využití?
Ve výchozím nastavení pro každou cloudovou službu Microsoft nastavuje agregovanou kvótu měsíčního využití, která se počítá napříč předplatnými zákazníka. Pokud potřebujete víc než tato omezení, můžete se kdykoli obrátit na službu zákazníkům, abychom porozuměli vašim potřebám a patřičně upravili tato omezení. Pro Service Bus je agregovaná kvóta využití 5 000 000 000 zpráv měsíčně.

Společnost Microsoft si vyhrazuje právo zakázat účet zákazníka, který překročil kvóty využití v daném měsíci, ale e-mailová oznámení se odešlou a před provedením jakékoli akce provedou několik pokusů o kontaktování zákazníka. Zákazníci překračující tyto kvóty stále zodpovídají za poplatky, které překračují tyto kvóty.

Stejně jako u ostatních služeb v Azure Service Bus vynutila sadu konkrétních kvót, aby bylo zajištěno, že bude k dispozici korektní využití prostředků. Další podrobnosti o těchto kvótách najdete v článku [Přehled kvót Service Bus][Quotas overview].

### <a name="how-to-handle-messages-of-size--1-mb"></a>Jak zpracovávat zprávy o velikosti > 1 MB?
Služba Service Bus Messaging Services (fronty a témata/odběry) umožňuje aplikaci posílat zprávy o velikosti až 256 KB (úroveň Standard) nebo 1 MB (úroveň Premium). Pokud pracujete se zprávami o velikosti větší než 1 MB, použijte vzor kontroly deklarací identity popsaný v [tomto blogovém příspěvku](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern).

## <a name="troubleshooting"></a>Řešení potíží
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Proč nemůžu vytvořit obor názvů po jeho odstranění z jiného předplatného? 
Když odstraníte obor názvů z předplatného, počkejte 4 hodiny, než ho znovu vytvoříte se stejným názvem v jiném předplatném. V opačném případě se může zobrazit následující chybová zpráva `Namespace already exists`:. 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Jaké jsou některé výjimky generované rozhraními API Azure Service Bus a jejich navrhovanými akcemi?
Seznam možných výjimek Service Bus naleznete v tématu [Exception Overview][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Co je sdílený přístupový podpis a které jazyky podporují generování podpisu?
Signatury sdíleného přístupu jsou mechanismy ověřování založené na zabezpečených hodnotách hash nebo identifikátorech URI SHA-256. Informace o tom, jak generovat vlastní signatury v Node. js, PHP, Java, Pythonu a C#, najdete v článku věnovaném [sdíleným přístupovým podpisům][Shared Access Signatures] .

## <a name="subscription-and-namespace-management"></a>Správa předplatného a oboru názvů
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Návody migrovat obor názvů do jiného předplatného Azure?

Obor názvů můžete přesunout z jednoho předplatného Azure do jiného, a to pomocí [Azure Portal](https://portal.azure.com) nebo příkazů PowerShellu. Aby bylo možné provést operaci, obor názvů již musí být aktivní. Uživatel, který spouští příkazy, musí být správce na zdrojových i cílových předplatných.

#### <a name="portal"></a>Portál

Pokud chcete použít Azure Portal k migraci Service Bus oborů názvů do jiného předplatného, postupujte podle pokynů [zde](../azure-resource-manager/resource-group-move-resources.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

Následující pořadí příkazů PowerShellu přesune obor názvů z jednoho předplatného Azure do jiného. Aby bylo možné provést tuto operaci, musí být obor názvů již aktivní a uživatel, který spouští příkazy prostředí PowerShell, musí být správcem na zdrojovém i cílovém předplatném.

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Další postup
Další informace o Service Bus najdete v následujících článcích:

* [Představujeme Azure Service Bus Premium (příspěvek na blogu)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Představujeme Azure Service Bus Premium (channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Přehled Service Bus](service-bus-messaging-overview.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
