---
title: Azure Service Bus Geo-havárii | Microsoft Docs
description: Jak používat zeměpisné oblasti převzetí služeb při selhání a proveďte obnovení po havárii v Azure Service Bus
services: service-bus-messaging
author: sethmanheim
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 06/14/2018
ms.author: sethm
ms.openlocfilehash: b43c5bd6ff6b386e1a2ee0b5e3ae8ec8fa61fb4b
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301515"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus Geo-havárii

Při celý oblastí Azure nebo datových centrech (Pokud žádné [dostupnost zóny](../availability-zones/az-overview.md) se používají) dojít k výpadku, je důležité pro zpracování dat pro i nadále fungovat v jiné oblasti nebo datového centra. Jako takový *geograficky havárii* a *geografická replikace* jsou důležité funkce pro všechny organizace. Azure Service Bus podporuje havárii geografické obnovení a geografická replikace, na úrovni oboru názvů. 

Funkce obnovení Geo-po havárii je globálně dostupnou pro Service Bus Premium SKU. 

## <a name="outages-and-disasters"></a>Výpadky a havárií

Je důležité si uvědomit rozdíl mezi "výpadky" a "havárie." *Výpadku* je dočasné nedostupnosti Azure Service Bus a může mít vliv na některé součásti služby, jako je zasílání zpráv úložiště nebo i celého datového centra. Ale po opravení problému Service Bus opět k dispozici. Výpadek obvykle nezpůsobí ztrátu zprávy nebo jiná data. Příkladem takových výpadku může být výpadku napájení v datovém centru. Některé výpadků jsou jenom krátké připojení ztráty způsobené přechodná nebo síťové problémy. 

A *po havárii* je definován jako trvalé, nebo dlouhodobější ztrátu cluster Service Bus, oblast Azure nebo datacenter. Oblast nebo datacenter může nebo nemusí opět k dispozici nebo může být mimo provoz pro hodin nebo dnů. Příkladem takových havárie jsou ještě efektivněji, zahlcení nebo zemětřesení. Po havárii, který se stane trvalé může způsobit ztrátu některé zprávy, události nebo jiná data. Ale ve většině případů by měla být nedošlo ke ztrátě dat a zprávy lze obnovit po zálohování datového centra.

Funkce obnovení geograficky havárie Azure Service Bus je řešení zotavení po havárii. Koncepty a pracovní postup popsaný v tomto článku použít po havárii scénáře a nikoli výpadků přechodný nebo dočasné. Podrobnou diskuzi o zotavení po havárii v Microsoft Azure, najdete v části [v tomto článku](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Základními koncepcemi a termíny

Funkce obnovení po havárii implementuje zotavení po havárii metadata a spoléhá na obory názvů pro zotavení po havárii primární a sekundární. Upozorňujeme, že je k dispozici pro funkci obnovení Geo-po havárii [skladová položka Premium](service-bus-premium-messaging.md) pouze. Není nutné žádné změny připojovací řetězec, jako je připojení přes alias.

V tomto článku se používají následující termíny:

-  *Alias*: název pro konfiguraci obnovení po havárii, které jste nastavili. Alias poskytuje jeden stabilní připojovací řetězec plně kvalifikovaný název domény (FQDN). Aplikace použít tento připojovací řetězec aliasu se připojit k oboru názvů. 

-  *Obor názvů primární a sekundární*: obory názvů, které odpovídají alias. Primární obor názvů "aktivní" a přijímá zprávy (může to být i na obor názvů existující nebo nové). Sekundární obor názvů je "pasivní" a nepřijímá zprávy. Metadata mezi oběma se synchronizace, tak i může bezproblémově přijmout zprávy beze změn aplikace kód nebo připojovací řetězec. Aby se zajistilo, že pouze aktivní obor názvů přijímá zprávy, musíte použít alias. 

-  *Metadata*: entity, jako je například front, témat a odběrů; a jejich vlastnosti služby, které jsou spojeny s oborem názvů. Všimněte si, že se automaticky replikují jenom entity a jejich nastavení. Nejsou replikovány zprávy. 

-  *Převzetí služeb při selhání*: proces aktivace sekundární obor názvů.

## <a name="setup-and-failover-flow"></a>Instalační program a převzetí služeb při selhání toku

V následující části je přehled procesu převzetí služeb při selhání a vysvětluje, jak nastavit počáteční převzetí služeb při selhání. 

![1][]

### <a name="setup"></a>Nastavení

Můžete nejprve vytvořit nebo použijte existujícího oboru názvů primární a sekundární nový obor názvů, a spárujte dva. Tato párování vám dává alias, který můžete použít pro připojení. Vzhledem k tomu, že používáte alias, nemáte změnit připojovací řetězce. Pouze nové obory názvů mohou být přidány do vaší párování převzetí služeb při selhání. Nakonec je nutné přidat, některá monitorování pro zjištění, pokud je nutné použít převzetí služeb při selhání. Ve většině případů služby je jednou ze součástí velké ekosystému, proto jsou automatické převzetí služeb při selhání zřídka možné, jako je velmi často převzetí služeb při selhání musíte provádět synchronizována s zbývající subsystému nebo infrastruktury.

### <a name="example"></a>Příklad:

V příkladem tohoto scénáře zvažte bodu prodej (POS) řešení, které vysílá zprávy nebo události. Service Bus předává tyto události některé mapování nebo přeformátování řešení, které pak předá namapované data do jiného systému pro další zpracování. V tomto bodě všech těchto systémech může být hostovaná ve stejné oblasti Azure. Rozhodnutí o a jaká část k převzetí služeb při selhání závisí na toku dat ve vaší infrastruktuře. 

Je možné automatizovat převzetí služeb při selhání s monitorováním systémů nebo s uživatelské řešení monitorování. Takové automatizace však trvá navíc plánování a činnosti, což je mimo rámec tohoto článku.

### <a name="failover-flow"></a>Tok převzetí služeb při selhání

Pokud spustíte převzetí služeb při selhání, dva kroky jsou povinné:

1. Pokud dojde k výpadku jiná, budete chtít moci znovu převzetí služeb při selhání. Proto nastavit jiný obor názvů pasivní a aktualizujte párování. 

2. Jakmile je opět k dispozici pro vyžádání obsahu zprávy z předchozí primární oboru názvů. Potom použít tento obor názvů pro regulární zasílání zpráv mimo vašeho nastavení geografické obnovení nebo odstraňte starý primární oboru názvů.

> [!NOTE]
> Jsou podporovány pouze dopředného sémantiku selhání. V tomto scénáři převzetí služeb při selhání a pak znovu spárovat s nový obor názvů. Navrácení služeb po obnovení není podporována; například v clusteru serveru SQL. 

![2][]

## <a name="management"></a>Správa

Pokud se jedná o chybu; například spárovat nesprávné oblasti během počáteční instalace, můžete zrušit párování dva obory názvů kdykoli. Pokud chcete použít jako regulární obory názvů spárované obory názvů, odstraňte alias.

## <a name="use-existing-namespace-as-alias"></a>Použít existující obor názvů jako alias

Pokud máte scénář, ve kterém nelze změnit připojení producenti a spotřebitelé, můžete znovu použít název vašeho oboru názvů jako název aliasu. Najdete v článku [ukázkový kód na Githubu zde](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Ukázky

[Ukázky z webu GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) ukazují, jak nastavit a zahájit převzetí služeb při selhání. Tyto ukázky ukazují následující koncepty:

- Ukázka rozhraní .NET a nastavení, které jsou potřeba v Azure Active Directory pomocí Azure Resource Manageru službou Service Bus, nastavit a zapnout geograficky havárii.
- Kroky potřebné k provedení ukázkový kód.
- Jak používat existujícího oboru názvů jako alias.
- Kroky, případně aby geograficky havárii pomocí prostředí PowerShell nebo rozhraní příkazového řádku.
- [Odesílat a přijímat](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) z aktuální primární nebo sekundární oboru názvů pomocí alias.

## <a name="considerations"></a>Požadavky

Pozorně si projděte následující informace v této verzi nezapomeňte:

1. Při plánování převzetí služeb při selhání, měli byste také zvážit Multi-Factor čas. Například pokud ztratíte připojení po dobu delší než 15-20 minut, můžete se rozhodnout zahájíte převzetí služeb při selhání. 
 
2. Fakt, že žádná data se replikují znamená, že aktuálně nejsou replikovány aktivních relací. Kromě toho nemusí fungovat detekce duplicitních a naplánované zprávy. Nové relace, nové plánované zprávy a nové duplikáty bude fungovat. 

3. Při přechodu komplexní distribuované infrastruktury by měla být [vyzkoušená](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation) alespoň jednou. 

4. Synchronizace entit může trvat delší dobu, přibližně 50 až 100 entit za minutu. Odběry a pravidla se také počítají jako entity. 

## <a name="availability-zones-preview"></a>Dostupnost zóny (preview)

Skladová položka Service Bus Premium podporuje také [dostupnost zóny](../availability-zones/az-overview.md), poskytuje odolnost izolované umístění v rámci oblasti Azure. 

> [!NOTE]
> Ve verzi preview dostupnosti zón je podporována pouze v **střed USA**, **východní USA 2**, a **Francie centrální** oblasti.

Dostupnost zóny můžete povolit na nové obory názvů pouze pomocí portálu Azure. Service Bus nepodporuje migraci z existujících oborů názvů. Po povolení na oboru názvů nelze zakázat zálohování zóny.

![3][]

## <a name="next-steps"></a>Další postup

- Najdete v části geografická havárii [zde odkazu k REST API](/rest/api/servicebus/disasterrecoveryconfigs).
- Spustit obnovení geograficky havárii [ukázku na Githubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Najdete v části geografická havárii [ukázku, která odesílá zprávy do alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Další informace o zasílání zpráv Service Bus, najdete v následujících článcích:

* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Rozhraní REST API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png