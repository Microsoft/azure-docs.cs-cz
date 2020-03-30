---
title: Azure Service Bus Geografické zotavení po havárii | Dokumenty společnosti Microsoft
description: Jak používat geografické oblasti k převzetí služeb při selhání a provádění zotavení po havárii v Azure Service Bus
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 24d6658733ea38c15f0673d10db3c0ff5ef51c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259575"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Geografické zotavení azure service bus

Když se v ypracují prostoje celé oblasti Azure nebo datových center (pokud se nepoužívají žádné [zóny dostupnosti),](../availability-zones/az-overview.md) je důležité, aby zpracování dat dál fungovalo v jiné oblasti nebo datovém centru. Jako takový, *Geo-zotavení po havárii* je důležitou funkcí pro všechny podniky. Azure Service Bus podporuje geografické zotavení po havárii na úrovni oboru názvů.

Funkce geografického zotavení po havárii je globálně dostupná pro sku Service Bus Premium. 

>[!NOTE]
> Geografické zotavení po havárii aktuálně zajišťuje, že metadata (fronty, témata, odběry, filtry) jsou zkopírovány z primárního oboru názvů do sekundárního oboru názvů při spárování.

## <a name="outages-and-disasters"></a>Výpadky a katastrofy

Je důležité si uvědomit rozdíl mezi "výpadky" a "katastrofy". 

*Výpadek* je dočasná nedostupnost Služby Azure Service Bus a může ovlivnit některé součásti služby, jako je například úložiště zpráv nebo dokonce celé datové centrum. Po vyřešilproblém service bus bude k dispozici znovu. Výpadek obvykle nezpůsobí ztrátu zpráv nebo jiných dat. Příkladem takového výpadku může být výpadku napájení v datovém centru. Některé výpadky jsou pouze krátké ztráty připojení z důvodu přechodných nebo problémů se sítí. 

Po *havárii* je definována jako trvalá nebo dlouhodobá ztráta clusteru Service Bus, oblasti Azure nebo datového centra. Oblast nebo datové centrum může nebo nemusí být znovu k dispozici nebo může být vypnuto po celé hodiny nebo dny. Příkladem takových katastrof jsou požár, záplavy nebo zemětřesení. Havárie, která se stane trvalou, může způsobit ztrátu některých zpráv, událostí nebo jiných dat. Ve většině případů by však neměla být žádná ztráta dat a zprávy lze obnovit po zálohování datového centra.

Funkce geografického zotavení po havárii služby Azure Service Bus je řešení zotavení po havárii. Koncepty a pracovní postup popsané v tomto článku platí pro scénáře katastrofy a nikoli pro přechodné nebo dočasné výpadky. Podrobnou diskusi o zotavení po havárii v Microsoft Azure najdete v [tomto článku](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Základní pojmy a pojmy

Funkce zotavení po havárii implementuje metadata zotavení po havárii a spoléhá na primární a sekundární obory názvů zotavení po havárii. Všimněte si, že funkce geografického zotavení po havárii je k dispozici pouze pro [prémiovou skladovou položku.](service-bus-premium-messaging.md) Není nutné provádět žádné změny připojovacího řetězce, protože připojení je provedeno pomocí aliasu.

V tomto článku se používají následující termíny:

-  *Alias*: Název konfigurace zotavení po havárii, kterou jste nastavili. Alias poskytuje jeden stabilní plně kvalifikovaný připojovací řetězec dns (FQDN). Aplikace používají tento připojovací řetězec aliasu k připojení k oboru názvů. Použití aliasu zajistí, že připojovací řetězec se nezmění při aktivaci převzetí služeb při selhání.

-  *Primární/sekundární obor názvů*: Obory názvů, které odpovídají aliasu. Primární obor názvů je "aktivní" a přijímá zprávy (může se jedná o existující nebo nový obor názvů). Sekundární obor názvů je "pasivní" a nepřijímá zprávy. Metadata mezi oběma jsou synchronizována, takže obě mohou bezproblémově přijímat zprávy bez jakýchkoli změn kódu aplikace nebo připojovacího řetězce. Chcete-li zajistit, aby zprávy přijímali pouze aktivní obor názvů, musíte alias použít. 

-  *Metadata*: Entity, jako jsou fronty, témata a odběry; a jejich vlastnosti služby, které jsou přidruženy k oboru názvů. Všimněte si, že pouze entity a jejich nastavení jsou replikovány automaticky. Zprávy nejsou replikovány.

-  *Převzetí služeb při selhání*: Proces aktivace sekundárního oboru názvů.

## <a name="setup"></a>Nastavení

Následující část je přehled nastavení párování mezi obory názvů.

![1][]

Proces nastavení je následující -

1. Zřízení ***primární*** služby Bus Premium obor názvů.

2. Zřízení ***sekundární služby*** Sběrnice Premium obor názvů v oblasti *odlišné od místa, kde je zřízena primární obor názvů*. To pomůže povolit izolaci chyb v různých oblastech datového centra.

3. Chcete-li získat alias , vytvořte párování mezi primárním oborem názvů a sekundárním oborem ***názvů***.

    >[!NOTE] 
    > Pokud jste [migrovali obor názvů Azure Service Bus Standard do služby Azure Service Bus Premium](service-bus-migrate-standard-premium.md), musíte použít již existující alias (tj. připojovací řetězec oboru názvů Service Bus Standard) k vytvoření konfigurace zotavení po havárii prostřednictvím rozhraní API **PS/CLI** nebo **REST .**
    >
    >
    > Důvodem je, že během migrace se váš připojovací řetězec oboru názvů Azure Service Bus Standard standard nebo samotný název DNS stane aliasem vašeho oboru názvů Azure Service Bus Premium.
    >
    > Vaše klientské aplikace musí využít tento alias (tj. připojovací řetězec oboru oboru názvů Azure Service Bus Standard) pro připojení k oboru názvů Premium, kde bylo nastaveno párování zotavení po havárii.
    >
    > Pokud použijete portál k nastavení konfigurace zotavení po havárii, bude portál abstraktní toto upozornění od vás.


4. Pomocí ***aliasu*** získaného v kroku 3 připojte klientské aplikace k primárnímu oboru názvů s povolenou geodr. Zpočátku alias odkazuje na primární obor názvů.

5. [Nepovinné] Přidejte některé monitorování, abyste zjistili, zda je nutné převzetí služeb při selhání.

## <a name="failover-flow"></a>Tok převzetí služeb při selhání

Převzetí služeb při selhání se aktivuje ručně zákazníkem (explicitně prostřednictvím příkazu nebo prostřednictvím obchodní logiky vlastněné klientem, která aktivuje příkaz) a nikdy Azure. To poskytuje zákazníkovi plné vlastnictví a viditelnost pro rozlišení výpadku na páteři Azure.

![4][]

Po spuštění převzetí služeb při selhání

1. ***Připojovací*** řetězec aliasu je aktualizován tak, aby ukazoval na obor názvů Sekundární premium.

2. Klienti (odesílatelé a příjemci) se automaticky připojují k sekundárnímu oboru názvů.

3. Existující párování mezi primární a sekundární třídy názvů premium je přerušeno.

Jakmile je převzetí služeb při selhání zahájeno -

1. Pokud dojde k jinému výpadku, chcete mít možnost znovu selhat znovu. Proto nastavte jiný pasivní obor názvů a aktualizujte párování. 

2. Vyžádat zprávy z bývalého primárního oboru názvů, jakmile je opět k dispozici. Poté použijte tento obor názvů pro běžné zasílání zpráv mimo nastavení geografické obnovy nebo odstraňte starý primární obor názvů.

> [!NOTE]
> Podporovány jsou pouze selhání dopředu sémantiku. V tomto scénáři převzetí služeb při selhání a znovu spárovat s novým oborem názvů. Vrácení se selháním není podporováno. například v clusteru SQL. 

Převzetí služeb při selhání můžete automatizovat buď pomocí monitorovacích systémů, nebo pomocí vlastních monitorovacích řešení. Taková automatizace však vyžaduje další plánování a práci, která je mimo rozsah tohoto článku.

![2][]

## <a name="management"></a>Správa

Pokud jste udělali chybu; například jste během počátečního nastavení spárovali nesprávné oblasti, můžete kdykoli přerušit párování dvou oborů názvů. Pokud chcete spárované obory názvů použít jako běžné obory názvů, odstraňte alias.

## <a name="use-existing-namespace-as-alias"></a>Použít existující obor názvů jako alias

Pokud máte scénář, ve kterém nelze změnit připojení výrobců a spotřebitelů, můžete znovu použít název oboru názvů jako název aliasu. Podívejte se na [ukázkový kód na GitHubu zde](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>ukázky

[Ukázky na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) ukazují, jak nastavit a iniciovat převzetí služeb při selhání. Tyto ukázky ukazují následující pojmy:

- Ukázka rozhraní .NET a nastavení, která jsou ve službě Azure Active Directory vyžadována k použití Správce prostředků Azure s service bus, k nastavení a povolení geografického zotavení po havárii.
- Kroky potřebné ke spuštění ukázkového kódu.
- Použití existujícího oboru názvů jako aliasu.
- Kroky k alternativnímu povolení geografického zotavení po havárii prostřednictvím prostředí PowerShell nebo CLI.
- [Odesílat a přijímat](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) z aktuálního primárního nebo sekundárního oboru názvů pomocí aliasu.

## <a name="considerations"></a>Požadavky

Všimněte si následujících úvah, které je třeba mít na paměti v této verzi:

1. Při plánování převzetí služeb při selhání byste měli také zvážit faktor času. Pokud například ztratíte připojení po dobu delší než 15 až 20 minut, můžete se rozhodnout zahájit převzetí služeb při selhání.

2. Skutečnost, že žádná data jsou replikována znamená, že aktuálně aktivní relace nejsou replikovány. Navíc nemusí fungovat vyhledávání duplicit a naplánované zprávy. Nové relace, nové naplánované zprávy a nové duplikáty budou fungovat. 

3. Selhání komplexní distribuované infrastruktury by mělo být [nacvičeno](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) alespoň jednou.

4. Synchronizace entit může nějakou dobu trvat, přibližně 50-100 entit za minutu. Odběry a pravidla se také počítají jako entity.

## <a name="availability-zones"></a>Zóny dostupnosti

Skladová položka Service Bus Premium také podporuje [zóny dostupnosti](../availability-zones/az-overview.md)a poskytuje umístění izolovaná v rámci oblasti Azure.

> [!NOTE]
> Podpora zón dostupnosti pro Azure Service Bus Premium je k dispozici jenom v [oblastech Azure,](../availability-zones/az-overview.md#services-support-by-region) kde jsou k dispozici zóny dostupnosti.

Zóny dostupnosti můžete povolit pouze v nových oborech názvů pomocí portálu Azure. Service Bus nepodporuje migraci existujících oborů názvů. Redundanci zóny nelze zakázat po povolení v oboru názvů.

![3][]

## <a name="next-steps"></a>Další kroky

- Podívejte se na [odkaz REST API](/rest/api/servicebus/disasterrecoveryconfigs)pro zotavení po havárii zde .
- Spusťte ukázku geografického zotavení po havárii [na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Podívejte se na ukázku zotavení po [havárii, která odesílá zprávy aliasu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Další informace o zasílání zpráv služby Service Bus najdete v následujících článcích:

* [Fronty, témata a odběry služby Service Bus](service-bus-queues-topics-subscriptions.md)
* [Začínáme s frontami služby Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak používat témata a odběry Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [Rozhraní API pro odpočinek](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
