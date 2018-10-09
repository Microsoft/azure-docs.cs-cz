---
title: Azure Service Bus Geo-zotavení po havárii | Dokumentace Microsoftu
description: Použití geografických oblastí na převzetí služeb při selhání a provádět zotavení po havárii v Azure Service Bus
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: spelluru
ms.openlocfilehash: 0436248dac2812c447d25de16a4ac6b45bd7248f
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855169"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus Geo-zotavení po havárii

Po celé oblasti Azure nebo datových centrech (Pokud ne [zóny dostupnosti](../availability-zones/az-overview.md) se používají) dojít k výpadku, je velmi důležité pro zpracování dat i nadále fungovat v jiné oblasti nebo datového centra. V důsledku toho *zotavení po havárii geograficky* a *geografickou replikaci* jsou důležité funkce pro všechny podniky. Azure Service Bus podporuje geo-zotavení po havárii a geografická replikace, na úrovni oboru názvů. 

Funkce zotavení po havárii geograficky je globálně dostupná pro skladovou Položku služby Service Bus úrovně Premium. 

## <a name="outages-and-disasters"></a>Výpadků a havárií

Je důležité si uvědomit rozdíl mezi "výpadků" a "havárií." *Výpadek* je dočasná nedostupnost služby Azure Service Bus a může mít vliv na některé součásti služby, jako je například úložiště, nebo dokonce celé datové centrum. Ale po byl problém vyřešen, Service Bus opět k dispozici. Kvůli výpadku obvykle nezpůsobí ztrátu zprávy nebo jiná data. Příkladem takových výpadek může být výpadek napájení v datovém centru. Některé výpadky jsou pouze krátkou připojení ztráty kvůli problémům s přechodným nebo sítě. 

A *po havárii* je definován jako trvalý, nebo dlouhodobější ztrátu clusteru služby Service Bus, oblasti Azure nebo datacenter. Oblasti nebo datového centra může nebo nemusí opět k dispozici nebo je pravděpodobně mimo provoz pro hodiny nebo i dny. Příkladem takové jiného problému ovlivňujícího jsou fire, zahlcení nebo zemětřesení. Po havárii, který se stane trvalé může způsobit ztrátu některé zprávy, události nebo jiná data. Ale ve většině případů by měl být nulové ztráty a zprávy lze obnovit zálohování po datové centrum.

Funkce zotavení po havárii geograficky Azure Service Bus je řešení pro zotavení po havárii. Koncepty a pracovní postup popsaný v tomto článku použít scénáře po havárii a není přechodná nebo dočasné výpadky. Podrobnou diskuzi o zotavení po havárii v Microsoft Azure, najdete v části [v tomto článku](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Základními koncepcemi a termíny

Funkce zotavení po havárii implementuje zotavení po havárii metadata a spoléhá na obory názvů zotavení po havárii primární a sekundární. Všimněte si, že je k dispozici pro funkce zotavení po havárii geograficky [SKU úrovně Premium](service-bus-premium-messaging.md) pouze. Není potřeba nic měnit připojovací řetězec, protože připojení se provádí prostřednictvím alias.

V tomto článku se používají následující termíny:

-  *Alias*: název pro konfigurace zotavení po havárii, které jste nastavili. Alias poskytuje jeden řetězec připojení stabilní plně kvalifikovaný název domény (FQDN). Aplikace použít tento připojovací řetězec aliasu pro připojení k oboru názvů. 

-  *Obor názvů primárního a sekundárního*: obory názvů, které odpovídají na alias. Primární obor názvů je "aktivní" a přijímá zprávy (to může být existující nebo nový obor názvů). Sekundární obor názvů je "pasivní" a nepřijímá zprávy. Metadata mezi oběma jsou synchronizované, tak i hladce přijmout zprávy bez nutnosti jakkoli měnit aplikace kódu nebo připojovací řetězec. Aby bylo zajištěno, že pouze aktivní obor názvů přijímá zprávy, musíte použít alias. 

-  *Metadata*: entity jako jsou fronty, témata a odběry; a jejich vlastnosti služby, které jsou spojeny s oborem názvů. Všimněte si, že se automaticky replikují jenom entity a jejich nastavení. Zprávy se nereplikují. 

-  *Převzetí služeb při selhání*: proces aktivace sekundární obor názvů.

## <a name="setup-and-failover-flow"></a>Instalační program a převzetí služeb při selhání toku

Následující části je uveden přehled procesu převzetí služeb při selhání a vysvětluje, jak nastavit počáteční převzetí služeb při selhání. 

![1][]

### <a name="setup"></a>Nastavení

Můžete nejprve vytvořit, nebo použijte existující primární obor názvů a nový sekundární obor názvů, a spárujte dvě. Toto párování získáte alias, který používáte pro připojení. Vzhledem k tomu, že používáte alias, není nutné změnit připojovací řetězce. Nové obory názvů lze přidat na váš párování převzetí služeb při selhání. A konečně měli byste přidat, některá monitorování ke zjištění, pokud je nutné převzetí služeb při selhání. Ve většině případů je jednou ze součástí sady rozsáhlého ekosystému služby, proto jsou automatické převzetí služeb při selhání jen zřídka je to možné, jak často převzetí služeb při selhání se musí provádět synchronizované s zbývající subsystému nebo infrastruktury.

### <a name="example"></a>Příklad:

V příkladem tohoto scénáře vezměte v úvahu bodu prodej (POS) řešení, které generuje zprávy nebo události. Service Bus se předá tyto události některé mapování nebo přeformátování řešení, které pak předá mapovaná data do jiného systému pro další zpracování. V tu chvíli se všech těchto systémech může být hostovaná ve stejné oblasti Azure. Rozhodnutí o a jaká část převzít služby při selhání závisí na toku dat ve vaší infrastruktuře. 

Můžete automatizovat převzetí služeb při selhání se systémy pro monitorování nebo s vlastními silami sestavených řešení monitorování. Tato automatizace však trvá dodatečné plánování a činnosti, což je mimo rámec tohoto článku.

### <a name="failover-flow"></a>Tok převzetí služeb při selhání

Pokud spustíte převzetí služeb při selhání, jsou požadovány dva kroky:

1. Pokud dojde k jinému výpadku, chcete mít možnost převzetí služeb při selhání znovu. Proto nastavit jiný obor názvů pasivní a aktualizujte párování. 

2. Jakmile je opět k dispozici vytahují zprávy z předchozí primární obor názvů. Potom použijte tento obor názvů pro zasílání zpráv regulární mimo nastavení geografického zotavení nebo odstraňte starý primární obor názvů.

> [!NOTE]
> Pouze dopředné sémantiku selhání jsou podporovány. V tomto scénáři převzetí služeb při selhání a potom znovu spárovat nový obor názvů. Navrácení služeb po obnovení není podporována. například v clusteru serveru SQL. 

![2][]

## <a name="management"></a>Správa

Pokud se jedná o chybu; například spárované nesprávné oblasti při počátečním nastavení, můžete přerušit párování dané dva obory názvů v každém okamžiku. Pokud chcete použít jako regulární obory názvů spárované obory názvů, odstraňte alias.

## <a name="use-existing-namespace-as-alias"></a>Použití existujícího oboru názvů jako alias

Pokud máte scénáře, ve kterém nelze změnit připojení producenti a spotřebitelé, můžete znovu použít název vašeho oboru názvů jako název aliasu. Zobrazit [ukázkový kód na Githubu tady](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Ukázky

[Ukázky na Githubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) ukazují, jak nastavit a spustit převzetí služeb při selhání. Tyto ukázky ukazují následující pojmy:

- Ukázka .NET a nastavení, které jsou nutné ve službě Azure Active Directory pomocí Azure Resource Manageru pomocí služby Service Bus, nastavení a povolení geografické zotavení.
- Kroky potřebné ke spuštění vzorového kódu.
- Jak používat existujícího oboru názvů jako alias.
- Kroky, případně aby Geo-zotavení po havárii prostřednictvím Powershellu nebo rozhraní příkazového řádku.
- [Odesílání a příjem](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) z aktuálního primárního nebo sekundárního oboru názvů pomocí aliasu.

## <a name="considerations"></a>Požadavky

Mějte na paměti následující aspekty brát v úvahu v této vydané verzi:

1. Při plánování převzetí služeb při selhání, měli byste také zvážit faktor čas. Například pokud ztratíte připojení po dobu delší než 15 až 20 minut, můžete se rozhodnout k zahájení převzetí služeb při selhání. 
 
2. Fakt, že žádná data se replikují znamená, že momentálně se nereplikují aktivní relace. Plánované zprávy a duplicit navíc nemusí fungovat. Nové relace, nové plánované zprávy a nové duplikáty budou fungovat. 

3. Přebírání služeb při selhání komplexní distribuované infrastruktury by měla být [vyzkoušená](/azure/architecture/resiliency/disaster-recovery-azure-applications#disaster-simulation) alespoň jednou. 

4. Synchronizace entit může trvat nějakou dobu, přibližně 50 – 100 entit za minutu. Předplatná a pravidla i počítat jako entity. 

## <a name="availability-zones-preview"></a>Zóny dostupnosti (preview)

SKU služby Service Bus úrovně Premium podporuje také [zóny dostupnosti](../availability-zones/az-overview.md), poskytuje umístění s izolací chyb v rámci oblasti Azure. 

> [!NOTE]
> Ve verzi preview zón dostupnosti je podporována pouze v **USA (střed)**, **USA – východ 2**, a **Francie – střed** oblastech.

Zóny dostupnosti můžete povolit na pouze nové obory názvů pomocí webu Azure portal. Service Bus nepodporuje migraci z existující oborů názvů. Po povolení na váš obor názvů nejde zakázat redundanci zón.

![3][]

## <a name="next-steps"></a>Další postup

- Geografické zotavení najdete v článku [zde odkaz rozhraní REST API](/rest/api/servicebus/disasterrecoveryconfigs).
- Spustit obnovení po havárii geograficky [ukázka na Githubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Geografické zotavení najdete v článku [ukázky, která odesílá zprávy do alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Další informace o zasílání zpráv Service Bus, najdete v následujících článcích:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Rozhraní REST API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geo1.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png