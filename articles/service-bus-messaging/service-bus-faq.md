---
title: Nejčastější dotazy k Azure Service Bus | Microsoft Docs
description: Tento článek obsahuje odpovědi na některé nejčastější dotazy týkající se Azure Service Bus.
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: e5bc9aae081868c92e0968c88c8cb6dcfb8ee57c
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676370"
---
# <a name="azure-service-bus---frequently-asked-questions-faq"></a>Nejčastější dotazy k Azure Service Bus (FAQ)

Tento článek popisuje některé časté otázky týkající se Microsoft Azure Service Bus. [Nejčastější dotazy k podpoře Azure](https://azure.microsoft.com/support/faq/) můžete také navštívit pro obecné ceny a informace o podpoře Azure.


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
Konvenční frontu nebo téma zpracovává jeden zprostředkovatel zpráv a ukládá se do jednoho úložiště pro zasílání zpráv. Podporováno pouze na úrovních Basic a standard pro zasílání zpráv, ve [frontě nebo tématu](service-bus-partitioning.md) je zpracováváno pomocí více zprostředkovatelů zpráv a uložených ve více úložištích zasílání zpráv. Tato funkce znamená, že celková propustnost dělené fronty nebo tématu již není omezena výkonem jediného zprostředkovatele zpráv nebo úložiště pro zasílání zpráv. Dočasný výpadek úložiště pro zasílání zpráv navíc neumožňuje vykreslovat rozdělenou frontu nebo téma jako nedostupné.

Řazení není při použití dělených entit zajištěno. V případě, že je oddíl nedostupný, můžete i nadále odesílat a přijímat zprávy z dalších oddílů.

 Dělené entity už nejsou v [SKU úrovně Premium](service-bus-premium-messaging.md)podporované. 

### <a name="where-does-azure-service-bus-store-customer-data"></a><a name="in-region-data-residency"></a>Kam se Azure Service Bus ukládají zákaznická data?
Azure Service Bus ukládá zákaznická data. Tato data se automaticky ukládají v Service Bus v jedné oblasti, takže tato služba automaticky splňuje požadavky na umístění dat v oblastech, včetně těch, které jsou uvedené v [Centru zabezpečení](https://azuredatacentermap.azurewebsites.net/).

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Jaké porty potřebuji v bráně firewall otevřít? 
K posílání a přijímání zpráv můžete použít následující protokoly s Azure Service Bus:

- Rozšířený protokol řízení front zpráv (AMQP) 1,0 (AMQP)
- Http (Hypertext Transfer Protocol 1,1) s protokolem TLS (HTTPS)

V následující tabulce jsou uvedeny Odchozí porty TCP, které je třeba otevřít pro použití těchto protokolů ke komunikaci s Azure Service Bus:

| Protokol | Port | Podrobnosti | 
| -------- | ----- | ------- | 
| AMQP | 5671 | AMQP s protokolem TLS. Viz [Průvodce protokolem AMQP](service-bus-amqp-protocol-guide.md) . | 
| HTTPS | 443 | Tento port se používá pro HTTP/REST API a pro sokety AMQP-over-Web. |

Port HTTPS se obecně vyžaduje pro odchozí komunikaci, i když se AMQP používá přes port 5671, protože klientské sady SDK a získávání tokenů z Azure Active Directory (Pokud se používají) spouští přes protokol HTTPS několik operací správy prováděných klientskými sadami SDK. 

Oficiální sady Azure SDK obecně používají protokol AMQP k posílání a přijímání zpráv z Service Bus. 

[!INCLUDE [service-bus-websockets-options](../../includes/service-bus-websockets-options.md)]

Starší balíček WindowsAzure. ServiceBus pro .NET Framework má možnost použít starší verzi protokolu Service Bus Messaging Protocol (SBMP), která se také označuje jako "NetMessaging". Tento protokol používá porty TCP 9350-9354. Výchozím režimem tohoto balíčku je automatické zjištění, zda jsou tyto porty k dispozici pro komunikaci, a v případě, že se jedná o tento případ, přepne na objekty WebSockets s protokolem TLS přes port 443. Toto nastavení můžete přepsat a tento režim vynutit nastavením `Https` [ConnectivityMode](/dotnet/api/microsoft.servicebus.connectivitymode) na [`ServiceBusEnvironment.SystemConnectivity`](/dotnet/api/microsoft.servicebus.servicebusenvironment.systemconnectivity) nastavení, které platí globálně pro aplikaci.

### <a name="what-ip-addresses-do-i-need-to-add-to-allow-list"></a>Jaké IP adresy potřebuji přidat do seznamu povolených adres?
Chcete-li najít správné IP adresy, které se mají přidat do seznamu povolených připojení, postupujte podle následujících kroků:

1. Z příkazového řádku spusťte následující příkaz: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Poznamenejte si IP adresu vrácenou v `Non-authoritative answer` . 

Pokud používáte **redundanci zóny** pro svůj obor názvů, musíte provést několik dalších kroků: 

1. Nejprve spustíte nástroj nslookup v oboru názvů.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Poznamenejte si název v části **nesměrodatná odpověď** , která je v jednom z následujících formátů: 

    ```
    <name>-s1.cloudapp.net
    <name>-s2.cloudapp.net
    <name>-s3.cloudapp.net
    ```
3. Spusťte nástroj nslookup pro každý z nich s příponami S1, S2 a S3 k získání IP adres všech tří instancí spuštěných ve třech zónách dostupnosti. 

    > [!NOTE]
    > IP adresa vrácená `nslookup` příkazem není statická IP adresa. Zůstává ale konstantní, dokud se základní nasazení neodstraní nebo nepřesune do jiného clusteru.

### <a name="where-can-i-find-the-ip-address-of-the-client-sendingreceiving-messages-tofrom-a-namespace"></a>Kde najdu IP adresu klienta odesílajícího/přijímaného zprávy do/z oboru názvů? 
Nebudeme protokolovat IP adresy klientů odesílajících nebo přijímaných zpráv do a z vašeho oboru názvů. Znovu vygenerujte klíče, aby se u všech stávajících klientů nepovedlo ověřit a zkontrolovat nastavení [řízení přístupu na základě role (Azure RBAC) v Azure](authenticate-application.md#azure-built-in-roles-for-azure-service-bus)), aby se zajistilo, že přístup k oboru názvů jenom povoleným uživatelům nebo aplikacím. 

Pokud používáte obor názvů **Premium** , omezte přístup k oboru názvů pomocí [filtrování IP adres](service-bus-ip-filtering.md), [koncových bodů služby virtuální sítě](service-bus-service-endpoints.md)a [privátních koncových bodů](private-link-service.md) . 

## <a name="best-practices"></a>Osvědčené postupy
### <a name="what-are-some-azure-service-bus-best-practices"></a>Co jsou některé Azure Service Bus osvědčené postupy?
Projděte si [osvědčené postupy pro zlepšení výkonu pomocí Service Bus][Best practices for performance improvements using Service Bus] – Tento článek popisuje, jak optimalizovat výkon při výměně zpráv.

### <a name="what-should-i-know-before-creating-entities"></a>Co mám vědět před vytvořením entit?
Následující vlastnosti fronty a tématu jsou neměnné. Při zřizování entit Vezměte v úvahu toto omezení, protože tyto vlastnosti není možné upravovat bez nutnosti vytvořit novou náhradní entitu.

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

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-isnt"></a>Jaké využití Service Bus podléhá přenosu dat? Co ne?
Jakýkoli přenos dat v rámci dané oblasti Azure se poskytuje zdarma, stejně jako všechny příchozí přenosy dat. Přenos dat mimo oblast podléhá poplatkům za výstup, který najdete [tady](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>Účtuje se Service Bus za úložiště?
No. Za úložiště se neúčtují Service Bus. Existuje však kvóta, která omezuje maximální množství dat, která lze uchovávat pro jednotlivé fronty nebo témata. Podívejte se na další Nejčastější dotazy.

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>Mám obor názvů Service Bus Standard. Proč se mi v rámci skupiny prostředků ' $system ' účtují poplatky?
Azure Service Bus nedávno upgradovali komponenty fakturace. V důsledku této změny se může stát, že pokud máte obor názvů Service Bus Standard, můžete zobrazit položky řádku pro prostředek "/Subscriptions/<azure_subscription_id>/resourceGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system" v části Skupina prostředků "$system".

Tyto poplatky reprezentují základní poplatek za předplatné Azure, které zřídilo obor názvů Service Bus Standard. 

Je důležité si uvědomit, že tyto poplatky nejsou nové, to znamená, že existovaly i v předchozím modelu fakturace. Jedinou změnou je, že jsou teď uvedené v části $system. Je to z důvodu omezení v novém fakturačním systému, který seskupuje poplatky za úrovni předplatného, které nejsou vázané na konkrétní prostředek, pod ID prostředku $system.

## <a name="quotas"></a>Kvóty

Seznam limitů a kvót Service Bus najdete v tématu [Přehled kvót Service Bus][Quotas overview].

### <a name="how-to-handle-messages-of-size--1-mb"></a>Jak zpracovávat zprávy o velikosti > 1 MB?
Služba Service Bus Messaging Services (fronty a témata/odběry) umožňuje aplikaci posílat zprávy o velikosti až 256 KB (úroveň Standard) nebo 1 MB (úroveň Premium). Pokud pracujete se zprávami o velikosti větší než 1 MB, použijte vzor kontroly deklarací identity popsaný v [tomto blogovém příspěvku](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern).

## <a name="troubleshooting"></a>Řešení potíží
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Proč nemůžu vytvořit obor názvů po jeho odstranění z jiného předplatného? 
Když odstraníte obor názvů z předplatného, počkejte 4 hodiny, než ho znovu vytvoříte se stejným názvem v jiném předplatném. V opačném případě se může zobrazit následující chybová zpráva: `Namespace already exists` . 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Jaké jsou některé výjimky generované rozhraními API Azure Service Bus a jejich navrhovanými akcemi?
Seznam možných výjimek Service Bus naleznete v tématu [Exception Overview][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Co je sdílený přístupový podpis a které jazyky podporují generování podpisu?
Signatury sdíleného přístupu jsou mechanismy ověřování založené na zabezpečených hodnotách hash nebo identifikátorech URI SHA-256. Informace o tom, jak generovat vlastní signatury v Node.js, PHP, Java, Pythonu a C#, najdete v článku věnovaném [sdíleným přístupovým podpisům][Shared Access Signatures] .

## <a name="subscription-and-namespace-management"></a>Správa předplatného a oboru názvů
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Návody migrovat obor názvů do jiného předplatného Azure?

Obor názvů můžete přesunout z jednoho předplatného Azure do jiného, a to pomocí [Azure Portal](https://portal.azure.com) nebo příkazů PowerShellu. Aby bylo možné provést operaci, obor názvů již musí být aktivní. Uživatel, který spouští příkazy, musí být správce na zdrojových i cílových předplatných.

#### <a name="portal"></a>Portál

Pokud chcete použít Azure Portal k migraci Service Bus oborů názvů do jiného předplatného, postupujte podle pokynů [zde](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal). 

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
## <a name="is-it-possible-to-disable-tls-10-or-11-on-service-bus-namespaces"></a>Je možné zakázat TLS 1,0 nebo 1,1 na Service Bus obory názvů?
No. Pro Service Bus obory názvů není možné zakázat TLS 1,0 nebo 1,1. V klientských aplikacích připojujících se k Service Bus použijte protokol TLS 1,2 nebo vyšší. Další informace najdete v tématu [vynucování TLS 1,2 s využitím Azure Service Bus-Microsoft Tech Community](https://techcommunity.microsoft.com/t5/messaging-on-azure/enforcing-tls-1-2-use-with-azure-service-bus/ba-p/370912).

## <a name="next-steps"></a>Další kroky
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
