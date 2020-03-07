---
title: Azure Service Bus geograficky zotavení po havárii | Microsoft Docs
description: Použití geografických oblastí k převzetí služeb při selhání a zotavení po havárii v Azure Service Bus
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 24d6658733ea38c15f0673d10db3c0ff5ef51c23
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78355977"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus geografické zotavení po havárii

Při výpadku provozu celé oblasti Azure nebo Datacenter (Pokud se nepoužívají žádné [zóny dostupnosti](../availability-zones/az-overview.md) ) je důležité, aby zpracování dat pokračovalo v práci v jiné oblasti nebo datacentru. V takovém případě *geograficky zotavení po havárii* je důležitou funkcí pro jakýkoli podnik. Azure Service Bus podporuje zotavení geografického havárií na úrovni oboru názvů.

Funkce geografického zotavení po havárii je globálně dostupná pro SKU Service Bus Premium. 

>[!NOTE]
> Geografické zotavení po havárii v současné době zajišťuje, že se při párování zkopírují metadata (fronty, témata, odběry, filtry) z primárního oboru názvů do sekundárního oboru názvů.

## <a name="outages-and-disasters"></a>Výpadků a havárií

Je důležité si uvědomit rozdíl mezi "výpadků" a "havárií." 

*Výpadek* je dočasná nedostupnost Azure Service Bus a může ovlivnit některé součásti služby, jako je třeba úložiště pro zasílání zpráv nebo i celé datacentrum. Po vyřešení problému však bude Service Bus opět k dispozici. Kvůli výpadku obvykle nezpůsobí ztrátu zprávy nebo jiná data. Příkladem takových výpadek může být výpadek napájení v datovém centru. Některé výpadky jsou pouze krátkou připojení ztráty kvůli problémům s přechodným nebo sítě. 

*Havárie* se definuje jako trvalá nebo dlouhodobá ztráta Service Bus clusteru, oblasti Azure nebo datového centra. Oblasti nebo datového centra může nebo nemusí opět k dispozici nebo je pravděpodobně mimo provoz pro hodiny nebo i dny. Příkladem takové jiného problému ovlivňujícího jsou fire, zahlcení nebo zemětřesení. Po havárii, který se stane trvalé může způsobit ztrátu některé zprávy, události nebo jiná data. Ale ve většině případů by měl být nulové ztráty a zprávy lze obnovit zálohování po datové centrum.

Funkce Azure Service Bus geografického zotavení po havárii je řešením zotavení po havárii. Koncepty a pracovní postup popsaný v tomto článku použít scénáře po havárii a není přechodná nebo dočasné výpadky. Podrobnou diskuzi o zotavení po havárii v Microsoft Azure najdete v [tomto článku](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Základními koncepcemi a termíny

Funkce zotavení po havárii implementuje zotavení po havárii metadata a spoléhá na obory názvů zotavení po havárii primární a sekundární. Všimněte si, že funkce geografického zotavení po havárii je dostupná jenom pro [SKU úrovně Premium](service-bus-premium-messaging.md) . Není potřeba nic měnit připojovací řetězec, protože připojení se provádí prostřednictvím alias.

V tomto článku se používají následující termíny:

-  *Alias*: název pro konfiguraci zotavení po havárii, kterou jste nastavili. Alias poskytuje jeden řetězec připojení stabilní plně kvalifikovaný název domény (FQDN). Aplikace použít tento připojovací řetězec aliasu pro připojení k oboru názvů. Použití aliasu zajistí, že se připojovací řetězec při aktivaci převzetí služeb při selhání nezměnil.

-  *Primární nebo sekundární obor názvů*: obory názvů, které odpovídají aliasu. Primární obor názvů je "aktivní" a přijímá zprávy (to může být existující nebo nový obor názvů). Sekundární obor názvů je "pasivní" a nepřijímá zprávy. Metadata mezi oběma jsou synchronizované, tak i hladce přijmout zprávy bez nutnosti jakkoli měnit aplikace kódu nebo připojovací řetězec. Aby bylo zajištěno, že pouze aktivní obor názvů přijímá zprávy, musíte použít alias. 

-  *Metadata*: entity, jako jsou fronty, témata a předplatná; a jejich vlastnosti služby, které jsou přidruženy k oboru názvů. Všimněte si, že se automaticky replikují jenom entity a jejich nastavení. Zprávy nejsou replikovány.

-  *Převzetí služeb při selhání*: proces aktivace sekundárního oboru názvů.

## <a name="setup"></a>Nastavení

V následující části je přehled nastavení párování mezi obory názvů.

![1][]

Proces instalace je následující –

1. Zřídit ***primární*** obor názvů Service Bus úrovně Premium.

2. Zřídit ***sekundární*** obor názvů Service Bus Premium v oblasti *odlišnou od místa, kde se zřizuje primární obor názvů*. To vám pomůže zajistit izolaci chyb napříč různými oblastmi Datacenter.

3. Vytvořte párování mezi primárním a sekundárním oborem názvů pro získání ***aliasu***.

    >[!NOTE] 
    > Pokud jste [přenesli obor názvů Azure Service Bus Standard do Azure Service Bus Premium](service-bus-migrate-standard-premium.md), je nutné použít již existující alias (tj. připojovací řetězec oboru názvů standardu Service Bus) k vytvoření konfigurace zotavení po havárii přes **PS/CLI** nebo **REST API**.
    >
    >
    > Důvodem je, že během migrace se jako připojovací řetězec oboru názvů Azure Service Bus Standard nebo název DNS sám vytvoří alias pro obor názvů Azure Service Bus Premium.
    >
    > Vaše klientské aplikace musí používat tento alias (tj. připojovací řetězec oboru názvů Azure Service Bus Standard) pro připojení k oboru názvů Premium, ve kterém bylo nastaveno párování zotavení po havárii.
    >
    > Pokud použijete portál k nastavení konfigurace zotavení po havárii, portál tuto výstrahu zavede.


4. Použijte ***alias*** získaný v kroku 3 k připojení klientských aplikací k primárnímu oboru názvů s povoleným geografickým Dr. Zpočátku alias odkazuje na primární obor názvů.

5. Volitelné Pokud chcete zjistit, jestli je převzetí služeb při selhání nezbytné, přidejte nějaké monitorování.

## <a name="failover-flow"></a>Tok převzetí služeb při selhání

Převzetí služeb při selhání se aktivuje ručně zákazníkem (buď explicitně prostřednictvím příkazu, nebo prostřednictvím obchodní logiky vlastněné klientem, která spouští příkaz) a nikdy neplatí pro Azure. Tím se zákazníkům poskytne úplné vlastnictví a přehled o řešení výpadků v páteřní síti Azure.

![4][]

Po aktivaci převzetí služeb při selhání –

1. Připojovací řetězec ***aliasu*** je aktualizovaný, aby odkazoval na sekundární obor názvů Premium.

2. Klienti (odesílatelé a přijímače) se automaticky připojí k sekundárnímu oboru názvů.

3. Existující párování mezi primárním a sekundárním oborem názvů Premium je přerušeno.

Po zahájení převzetí služeb při selhání –

1. Pokud dojde k dalšímu výpadku, budete chtít být schopni převzít služby po obnovení. Proto nastavit jiný obor názvů pasivní a aktualizujte párování. 

2. Jakmile je opět k dispozici vytahují zprávy z předchozí primární obor názvů. Potom použijte tento obor názvů pro zasílání zpráv regulární mimo nastavení geografického zotavení nebo odstraňte starý primární obor názvů.

> [!NOTE]
> Pouze dopředné sémantiku selhání jsou podporovány. V tomto scénáři převzetí služeb při selhání a potom znovu spárovat nový obor názvů. Navrácení služeb po obnovení není podporována. například v clusteru serveru SQL. 

Můžete automatizovat převzetí služeb při selhání se systémy pro monitorování nebo s vlastními silami sestavených řešení monitorování. Tato automatizace však trvá dodatečné plánování a činnosti, což je mimo rámec tohoto článku.

![2][]

## <a name="management"></a>Správa

Pokud se jedná o chybu; například spárované nesprávné oblasti při počátečním nastavení, můžete přerušit párování dané dva obory názvů v každém okamžiku. Pokud chcete použít jako regulární obory názvů spárované obory názvů, odstraňte alias.

## <a name="use-existing-namespace-as-alias"></a>Použít existující obor názvů jako alias

Pokud máte scénář, ve kterém nemůžete změnit připojení výrobců a uživatelů, můžete název oboru názvů použít jako název aliasu. Tady najdete [ukázkový kód na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Ukázky

[Ukázky na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) ukazují, jak nastavit a iniciovat převzetí služeb při selhání. Tyto ukázky ukazují následující koncepty:

- Ukázka a nastavení .NET, které jsou požadovány v Azure Active Directory pro použití Azure Resource Manager s Service Bus k nastavení a povolení geografického zotavení po havárii.
- Kroky potřebné ke spuštění vzorového kódu.
- Jak používat existující obor názvů jako alias.
- Postup nebo umožnění geografického zotavení po havárii prostřednictvím PowerShellu nebo rozhraní příkazového řádku.
- [Odeslat a přijmout](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) z aktuálního primárního nebo sekundárního oboru názvů pomocí aliasu

## <a name="considerations"></a>Požadavky

Mějte na paměti následující aspekty brát v úvahu v této vydané verzi:

1. Při plánování převzetí služeb při selhání, měli byste také zvážit faktor čas. Například pokud ztratíte připojení po dobu delší než 15 až 20 minut, můžete se rozhodnout k zahájení převzetí služeb při selhání.

2. Fakt, že žádná data se replikují znamená, že momentálně se nereplikují aktivní relace. Plánované zprávy a duplicit navíc nemusí fungovat. Nové relace budou fungovat nové naplánované zprávy a nové duplicity. 

3. Převzetí služeb při selhání přes složitou distribuovanou infrastrukturu by mělo být alespoň jednou [vyzkoušeno](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) .

4. Synchronizace entit může trvat nějakou dobu, přibližně 50 – 100 entit za minutu. Předplatná a pravidla se také počítají jako entity.

## <a name="availability-zones"></a>Zóny dostupnosti

SKU Service Bus Premium také podporuje [zóny dostupnosti](../availability-zones/az-overview.md)a poskytuje umístění pro izolované chyby v oblasti Azure.

> [!NOTE]
> Podpora Zóny dostupnosti pro Azure Service Bus Premium je dostupná jenom v [oblastech Azure](../availability-zones/az-overview.md#services-support-by-region) , kde se nacházejí zóny dostupnosti.

Zóny dostupnosti můžete povolit na pouze nové obory názvů pomocí webu Azure portal. Service Bus nepodporuje migraci stávajících oborů názvů. Po povolení na váš obor názvů nejde zakázat redundanci zón.

![3][]

## <a name="next-steps"></a>Další kroky

- [Tady najdete referenční](/rest/api/servicebus/disasterrecoveryconfigs)informace o geografickém zotavení po havárii REST API.
- Spusťte ukázku geografického zotavení po havárii [na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Podívejte se na ukázku obnovení geografického po havárii [, které odesílá zprávy do aliasu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Další informace o Service Bus zasílání zpráv najdete v následujících článcích:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Rozhraní REST API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
