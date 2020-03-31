---
title: Azure Service Bus nejčastější dotazy (FAQ) | Dokumenty společnosti Microsoft
description: Tento článek obsahuje odpovědi na některé často kladené otázky (FAQ) o Azure Service Bus.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 3cd4e69481fb452391e6dc027cb41fd6dae71b7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760245"
---
# <a name="azure-service-bus---frequently-asked-questions-faq"></a>Azure Service Bus – nejčastější dotazy (nejčastější dotazy)

Tento článek popisuje některé nejčastější dotazy týkající se služby Microsoft Azure Service Bus. Můžete také navštívit [nejčastější dotazy k podpoře Azure, kde](https://azure.microsoft.com/support/faq/) najdete obecné informace o cenách a podpoře Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions-about-azure-service-bus"></a>Obecné otázky týkající se služby Azure Service Bus
### <a name="what-is-azure-service-bus"></a>Co je Azure Service Bus?
[Azure Service Bus](service-bus-messaging-overview.md) je asynchronní cloudová platforma pro zasílání zpráv, která umožňuje odesílat data mezi oddělenými systémy. Společnost Microsoft nabízí tuto funkci jako službu, což znamená, že není nutné hostovat vlastní hardware, abyste ji používali.

### <a name="what-is-a-service-bus-namespace"></a>Co je obor názvů service bus?
[Obor názvů](service-bus-create-namespace-portal.md) poskytuje kontejner oborů pro adresování prostředků service bus v rámci vaší aplikace. Vytvoření oboru názvů je nutné použít service bus a je jedním z prvních kroků při začínáme.

### <a name="what-is-an-azure-service-bus-queue"></a>Co je fronta Azure Service Bus?
[Fronta služby Service Bus](service-bus-queues-topics-subscriptions.md) je entita, ve které jsou uloženy zprávy. Fronty jsou užitečné, pokud máte více aplikací nebo více částí distribuované aplikace, které potřebují komunikovat mezi sebou. Fronta je podobná distribučnímu centru v tom, že je přijato a odesláno více produktů (zpráv) z tohoto umístění.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Co jsou témata a předplatná Azure Service Bus?
Téma lze vizualizovat jako fronty a při použití více odběrů, se stane bohatší model zasílání zpráv; v podstatě komunikační nástroj 1:N. Tento model publikování/odběru (nebo *pub/sub*) umožňuje aplikaci, která odešle zprávu tématu s více odběry, aby tato zpráva byla přijata více aplikacemi.

### <a name="what-is-a-partitioned-entity"></a>Co je dělená entita?
Konvenční fronty nebo tématu je zpracována jeden zprostředkovatel zpráv a uloženy v jednom úložišti zasílání zpráv. Podporována pouze v základní a standardní úrovně zasílání zpráv, [rozdělené fronty nebo téma](service-bus-partitioning.md) je zpracována více zprostředkovatelů zpráv a uloženy ve více úložišť zasílání zpráv. Tato funkce znamená, že celková propustnost rozdělené fronty nebo tématu již není omezena výkonem zprostředkovatele nebo úložiště zpráv. Dočasný výpadek úložiště zpráv navíc neznemožní rozdělení rozdělené fronty nebo tématu.

Řazení není zajištěno při použití dělených entit. V případě, že oddíl není k dispozici, můžete stále odesílat a přijímat zprávy z jiných oddílů.

 Oddíly entity již nejsou podporovány v [Premium Skladové jednotky](service-bus-premium-messaging.md). 

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Jaké porty je třeba otevřít na firewallu? 
Pomocí služby Azure Service Bus můžete pomocí služby Azure Service Bus odesílat a přijímat zprávy:

- Rozšířený protokol řízení front zpráv (AMQP)
- Protokol zasílání zpráv sběrnice (SBMP)
- HTTP

V následující tabulce najdete odchozí porty, které je potřeba otevřít, abyste pomocí těchto protokolů komunikovali s Azure Event Hubs. 

| Protocol (Protokol) | Porty | Podrobnosti | 
| -------- | ----- | ------- | 
| AMQP | 5671 a 5672 | Viz [Průvodce protokolem AMQP](service-bus-amqp-protocol-guide.md) | 
| SBMP | 9350 až 9354 | Viz [režim připojení](/dotnet/api/microsoft.servicebus.connectivitymode?view=azure-dotnet) |
| HTTP, HTTPS | 80, 443 | 

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Jaké IP adresy potřebuji k whitelistu?
Chcete-li najít správné adresy IP do seznamu povolených adres pro vaše připojení, postupujte takto:

1. Z příkazového řádku spusťte následující příkaz: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Poznamenejte si `Non-authoritative answer`adresu IP vrácenou v . Tato adresa IP je statická. Jediný bod v čase, který by se změnil, je, pokud obnovíte obor názvů do jiného clusteru.

Pokud pro obor názvů používáte redundanci zóny, je třeba provést několik dalších kroků: 

1. Nejprve spustit nslookup v oboru názvů.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Poznamenejte si název v části **neautoritativní odpovědi,** která je v jednom z následujících formátů: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Spusťte nslookup pro každý z nich s příponami s1, s2 a s3 získat IP adresy všech tří instancí běžících ve třech zónách dostupnosti, 


## <a name="best-practices"></a>Osvědčené postupy
### <a name="what-are-some-azure-service-bus-best-practices"></a>Jaké jsou některé osvědčené postupy Azure Service Bus?
Podívejte se [na doporučené postupy pro zlepšení výkonu pomocí Service Bus][Best practices for performance improvements using Service Bus] – tento článek popisuje, jak optimalizovat výkon při výměně zpráv.

### <a name="what-should-i-know-before-creating-entities"></a>Co bych měl vědět před vytvořením entit?
Následující vlastnosti fronty a tématu jsou neměnné. Zvažte toto omezení při zřizování entit, protože tyto vlastnosti nelze změnit bez vytvoření nové náhradní entity.

* Dělení
* Relace
* Vyhledávání duplicit
* Expresní entita

## <a name="pricing"></a>Ceny
Tato část odpovídá na některé nejčastější dotazy týkající se cenové struktury service bus.

Článek [o cenách a fakturaci service bus](https://azure.microsoft.com/pricing/details/service-bus/) vysvětluje měřiče fakturace v service bus. Konkrétní informace o možnostech stanovení cen služby Service Bus naleznete [v tématu Podrobnosti o cenách služby Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

Obecné informace o cenách Azure najdete taky v [častých dotazech k podpoře Azure.](https://azure.microsoft.com/support/faq/) 

### <a name="how-do-you-charge-for-service-bus"></a>Jak si účtujete za autobus Service Bus?
Úplné informace o cenách služby Service Bus naleznete [v tématu Podrobnosti o cenách služby Service Bus][Pricing overview]. Kromě uvedených cen se vám účtuje přidružený přenos dat pro odchozí přenosmimo datové centrum, ve kterém je vaše aplikace zřízena.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Jaké využití service bus podléhá přenosu dat? Co není?
Jakýkoli přenos dat v rámci dané oblasti Azure se poskytuje zdarma, stejně jako jakýkoli příchozí přenos dat. Přenos dat mimo oblast podléhá poplatkům za odchozí přenos, které lze nalézt [zde](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>Účtuje si service bus za úložiště?
Ne, služba Service Bus se neúčtuje za úložiště. Existuje však kvóta omezující maximální množství dat, která mohou být uložena na frontu nebo téma. Podívejte se na další nejčastější dotazy.

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>Mám obor názvů Service Bus Standard. Proč se v části $system $system zobrazují poplatky?
Azure Service Bus nedávno upgradoval fakturační komponenty. Z tohoto důvodu, pokud máte obor názvů Service Bus Standard, může se zobrazit řádkové položky pro prostředek '/subscriptions/<azure_subscription_id>/resourceGroups/$system/providers/Microsoft.ServiceBus/namespace/$system' v části $system.

Tyto poplatky představují základní poplatek za předplatné Azure, který zřízený obor názvů Service Bus Standard. 

Je důležité si uvědomit, že se nejedná o nové poplatky, tj. Jedinou změnou je, že jsou nyní uvedeny pod "$system". To se provádí z důvodu contraints v novém fakturačním systému, který seskupuje poplatky na úrovni předplatného, které nejsou vázány na konkrétní prostředek, pod id prostředku "$system".

## <a name="quotas"></a>Kvóty

Seznam omezení a kvót sběrnice service bus naleznete v [přehledu kvót služby Service Bus][Quotas overview].

### <a name="does-service-bus-have-any-usage-quotas"></a>Má service bus nějaké kvóty využití?
Ve výchozím nastavení nastaví společnost Microsoft pro všechny cloudové služby agregovnu měsíční kvótu využití, která se počítá ve všech předplatných zákazníka. Pokud potřebujete více než tyto limity, můžete kdykoli kontaktovat zákaznický servis, abyste porozuměli svým potřebám a odpovídajícím způsobem tyto limity upravili. Pro service bus je souhrnná kvóta využití 5 miliard zpráv za měsíc.

Zatímco společnost Microsoft si vyhrazuje právo zakázat účet zákazníka, který překročil kvóty využití v daném měsíci, e-mailová oznámení jsou odesílána a několik pokusů o kontaktování zákazníka před přijetím jakékoli akce. Zákazníci překračující tyto kvóty jsou stále zodpovědní za poplatky, které kvóty překračují.

Stejně jako u jiných služeb v Azure service bus vynucuje sadu konkrétníkvóty k zajištění spravedlivévyužití prostředků. Další podrobnosti o těchto kvótách naleznete v [přehledu kvót služby Service Bus][Quotas overview].

### <a name="how-to-handle-messages-of-size--1-mb"></a>Jak zacházet se zprávami o velikosti > 1 MB?
Služby zasílání zpráv Service Bus (fronty a témata/odběry) umožňují aplikaci odesílat zprávy o velikosti až 256 kB (úroveň standard) nebo 1 MB (úroveň premium). Pokud máte co do činění se zprávami o velikosti větší než 1 MB, použijte vzor kontroly deklarací popsaný v [tomto příspěvku blogu](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern).

## <a name="troubleshooting"></a>Řešení potíží
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Proč nemohu vytvořit obor názvů po jeho odstranění z jiného předplatného? 
Při odstranění oboru názvů z předplatného počkejte 4 hodiny, než jej znovu vytvoříte se stejným názvem v jiném předplatném. V opačném případě se může `Namespace already exists`zobrazit následující chybová zpráva: . 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Jaké jsou některé výjimky generované azure service bus API a jejich navrhované akce?
Seznam možných výjimek služby Service Bus naleznete v tématu [Přehled výjimek][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Co je sdílený přístupový podpis a které jazyky podporují generování podpisu?
Sdílené přístupové podpisy jsou mechanismus ověřování založený na zabezpečených hashe nebo identifikátorech URI SHA-256. Informace o tom, jak generovat vlastní podpisy v Node.js, PHP, Java, Python a C#, najdete v článku [Sdílené přístupové podpisy.][Shared Access Signatures]

## <a name="subscription-and-namespace-management"></a>Správa předplatného a oboru názvů
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Jak můžu migrovat obor názvů do jiného předplatného Azure?

Obor názvů můžete přesunout z jednoho předplatného Azure do jiného pomocí portálu [Azure](https://portal.azure.com) nebo příkazů PowerShellu. Chcete-li provést operaci, musí být obor názvů již aktivní. Uživatel provádějící příkazy musí být správcem zdrojových i cílových předplatných.

#### <a name="portal"></a>Portál

Pokud chcete pomocí portálu Azure migrovat obory názvů Service Bus do jiného předplatného, postupujte [podle pokynů zde](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

Následující posloupnost příkazů Prostředí PowerShell přesune obor názvů z jednoho předplatného Azure do jiného. Chcete-li provést tuto operaci, musí být obor názvů již aktivní a uživatel s ovládacími příkazy prostředí PowerShell musí být správcem zdrojových i cílových předplatných.

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Další kroky
Další informace o službě Service Bus najdete v následujících článcích:

* [Představujeme Azure Service Bus Premium (blogový příspěvek)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Představujeme Azure Service Bus Premium (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Přehled sběrnice](service-bus-messaging-overview.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
