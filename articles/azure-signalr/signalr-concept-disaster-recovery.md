---
title: Odolnost proti chybám a zotavení po havárii ve službě Azure SignalR
description: Přehled o tom, jak nastavit více instancí služby SignalR pro dosažení odolnosti proti chybám a zotavení po havárii
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: kenchen
ms.openlocfilehash: cf0f345b0fbf9fea2512f72c1996c9a1597cc0cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73747642"
---
# <a name="resiliency-and-disaster-recovery"></a>Odolnost a zotavení po havárii

Odolnost proti chybám a zotavení po havárii je běžnou potřebou online systémů. Služba Azure SignalR již zaručuje 99,9 % dostupnost, ale stále je to místní služba.
Vaše instance služby je vždy spuštěna v jedné oblasti a nebude převzetí služeb při selhání do jiné oblasti, pokud dojde k výpadku celé oblasti.

Místo toho naše služba SDK poskytuje funkce pro podporu více instancí služby SignalR a automaticky přepnout do jiných instancí, když některé z nich nejsou k dispozici.
Díky této funkci se budete moci zotavit, když dojde ke katastrofě, ale budete muset nastavit správnou topologii systému sami. V tomto dokumentu se dozvíte, jak to udělat.

## <a name="high-available-architecture-for-signalr-service"></a>Vysoce dostupná architektura pro službu SignalR

Chcete-li mít odolnost proti chybám mezi oblastmi pro službu SignalR, je třeba nastavit více instancí služby v různých oblastech. Takže když je jedna oblast mimo, ostatní mohou být použity jako záloha.
Při připojování více instancí služby k aplikačnímu serveru existují dvě role, primární a sekundární.
Primární je instance, která přijímá online provoz a sekundární je plně funkční, ale záložní instance pro primární.
V naší implementaci sady SDK vyjednat vrátí pouze primární koncové body, takže v normálním případě klienti připojit pouze k primární koncové body.
Ale když primární instance je dolů, negotiate vrátí sekundární koncové body, takže klient může stále navázat připojení.
Primární instance a aplikační server jsou připojeny prostřednictvím běžných připojení k serveru, ale sekundární instance a aplikační server jsou připojeny prostřednictvím speciálního typu připojení nazývaného slabé připojení.
Hlavní rozdíl slabépřipojení je, že nepřijímá směrování připojení klienta, protože sekundární instance se nachází v jiné oblasti. Směrování klienta do jiné oblasti není optimální volbou (zvyšuje latenci).

Jedna instance služby může mít různé role při připojování k více serverům aplikace.
Jedním z typických nastavení pro scénář s křížovou oblastí je mít dva (nebo více) párů instancí služby SignalR a aplikačních serverů.
Uvnitř každého páru aplikační server a signalr služba jsou umístěny ve stejné oblasti a SignalR služba je připojena k aplikačnímu serveru jako primární role.
Mezi jednotlivými dvojicemi jsou také připojeny aplikační server a služba SignalR, ale SignalR se stane sekundárním při připojování k serveru v jiné oblasti.

Pomocí této topologie může být zpráva z jednoho serveru stále doručena všem klientům, protože všechny aplikační servery a instance služby SignalR jsou vzájemně propojeny.
Ale když je klient připojený, je vždy směrován na aplikační server ve stejné oblasti, aby bylo dosaženo optimální latence sítě.

Níže je diagram, který ilustruje tyto topologie:

![topologie](media/signalr-concept-disaster-recovery/topology.png)

## <a name="configure-app-servers-with-multiple-signalr-service-instances"></a>Konfigurace aplikačních serverů s více instancemi služby SignalR

Jakmile budete mít signalr služby a aplikační servery vytvořené v každé oblasti, můžete nakonfigurovat servery aplikace pro připojení ke všem instancím služby SignalR.

Můžete to udělat dvěma způsoby:

### <a name="through-config"></a>Prostřednictvím konfigurace

Již byste měli vědět, jak nastavit připojovací řetězec služby SignalR prostřednictvím proměnných `Azure:SignalR:ConnectionString`prostředí/ nastavení aplikace / web.cofig, v položce konfigurace s názvem .
Pokud máte více koncových bodů, můžete je nastavit ve více položkách konfigurace, každý v následujícím formátu:

```
Azure:SignalR:ConnectionString:<name>:<role>
```

Zde `<name>` je název koncového `<role>` bodu a je jeho role (primární nebo sekundární).
Název je volitelný, ale bude užitečný, pokud chcete dále přizpůsobit chování směrování mezi více koncovými body.

### <a name="through-code"></a>Prostřednictvím kódu

Pokud dáváte přednost ukládání připojovacích řetězců někde jinde, můžete si je `AddAzureSignalR()` také přečíst v `MapAzureSignalR()` kódu a použít je jako parametry při volání (v ASP.NET Core) nebo (v ASP.NET).

Zde je ukázkový kód:

ASP.NET Core:

```cs
services.AddSignalR()
        .AddAzureSignalR(options => options.Endpoints = new ServiceEndpoint[]
        {
            new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
            new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
        });
```

ASP.NET:

```cs
app.MapAzureSignalR(GetType().FullName, hub,  options => options.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
        new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
    };
```

Můžete nakonfigurovat více primárních nebo sekundárních instancí. Pokud existuje více primárních a/nebo sekundárních instancí, vyjednávání vrátí koncový bod v následujícím pořadí:

1. Pokud existuje alespoň jedna primární instance online, vraťte náhodnou primární online instanci.
2. Pokud jsou všechny primární instance vypnuty, vraťte náhodnou sekundární online instanci.

## <a name="failover-sequence-and-best-practice"></a>Posloupnost převzetí služeb při selhání a osvědčené postupy

Nyní máte správné nastavení topologie systému. Vždy, když jedna instance služby SignalR je dolů, online provoz bude směrován do jiných instancí.
Zde je to, co se stane, když primární instance je dolů (a obnoví po určité době):

1. Primární instance služby je mimo provoz, všechna připojení serveru v této instanci budou zrušena.
2. Všechny servery připojené k této instanci označí jako offline a vyjednávání přestane vracet tento koncový bod a začne vracet sekundární koncový bod.
3. Všechna připojení klientů v této instanci budou také uzavřena, klienti se znovu připojí. Vzhledem k tomu, že aplikační servery nyní vracejí sekundární koncový bod, klienti se připojí k sekundární instanci.
4. Nyní sekundární instance přebírá veškerý online provoz. Všechny zprávy ze serveru klientům mohou být stále doručovány, protože sekundární je připojen ke všem serverům aplikace. Ale zprávy klienta k serveru jsou směrovány pouze na aplikační server ve stejné oblasti.
5. Po obnovení primární instance a zpět online, app server obnoví připojení k němu a označit ji jako online. Negotiate nyní vrátí primární koncový bod znovu, takže noví klienti jsou připojeni zpět k primární. Ale stávající klienti nebudou vynechány a budou i nadále směrovány na sekundární, dokud se odpojí.

Níže uvedené diagramy znázorňují, jak se provádí převzetí služeb při selhání ve službě SignalR:

1 Před převzetím ![služeb při selhání před převzetím služeb při selhání](media/signalr-concept-disaster-recovery/before-failover.png)

2 Po převzetí ![služeb při selhání po převzetí služeb při selhání](media/signalr-concept-disaster-recovery/after-failover.png)

Krátký čas po primárním ![zotavení Krátký čas po primárním zotavení](media/signalr-concept-disaster-recovery/after-recover.png)

V normálním případě můžete vidět, že pouze primární aplikační server a služba SignalR mají online provoz (modře).
Po převzetí služeb při selhání se také aktivuje sekundární aplikační server a služba SignalR.
Po primární signalr služba je zpět online, noví klienti se připojí k primární SignalR. Ale stávající klienti stále připojit k sekundární, takže obě instance mají provoz.
Po odpojení všech stávajících klientů se systém vrátí do normálu (obr.1).

Existují dva hlavní vzory pro implementaci architektury s vysokou dostupnou oblastí napříč oblastmi:

1. První z nich je mít pár app server a SignalR služby instance s veškerým online provoz, a mají další pár jako zálohu (tzv. aktivní / pasivní, ilustrované na obr.1). 
2. Druhým je mít dva (nebo více) párů aplikačních serverů a instancí služby SignalR, z nichž každá se účastní online provozu a slouží jako záloha pro další páry (nazývané aktivní/aktivní, podobné obrázku 3).

SignalR služba může podporovat oba vzory, hlavní rozdíl je, jak implementovat aplikační servery.
Pokud jsou servery aplikací aktivní/pasivní, služba SignalR bude také aktivní/pasivní (protože primární aplikační server vrátí pouze svou primární instanci služby SignalR).
Pokud jsou servery aplikací aktivní/aktivní, služba SignalR bude také aktivní/aktivní (protože všechny aplikační servery vrátí své vlastní primární instance SignalR, takže všechny z nich mohou získat provoz).

Mějte na vědomí, bez ohledu na to, které vzory se rozhodnete použít, budete muset připojit každou instanci služby SignalR k aplikačnímu serveru jako primární.

Také vzhledem k povaze signalr připojení (je to dlouhé připojení), klienti dojde k výpadku připojení, když dojde k havárii a převzetí služeb při selhání dojít.
Budete muset zpracovat takové případy na straně klienta, aby byly transparentní pro vaše koncové zákazníky. Například znovu připojit po uzavření připojení.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak nakonfigurovat aplikaci k dosažení odolnosti proti chybám pro službu SignalR. Chcete-li porozumět dalším podrobnostem o připojení serveru/klienta a směrování připojení ve službě SignalR, můžete si přečíst [tento článek](signalr-concept-internals.md) pro interní služby SignalR.

Pro škálování scénáře, jako je horizontálního oddílu, které používají více instancí společně pro zpracování velkého počtu připojení, přečtěte si, [jak škálovat více instancí](signalr-howto-scale-multi-instances.md).
