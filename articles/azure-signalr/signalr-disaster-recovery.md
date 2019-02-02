---
title: Odolnost proti chybám a zotavení po havárii ve službě Azure SignalR
description: Přehled o tom, jak nastavit víc instancí služby SignalR k dosažení odolnost proti chybám a zotavení po havárii
author: chenkennt
ms.service: signalr
ms.topic: overview
ms.date: 01/29/2019
ms.author: kenchen
ms.openlocfilehash: 8852bd5ef9dbe666abebfdc64a31faa135c9b8b6
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55569388"
---
# <a name="resiliency-and-disaster-recovery"></a>Odolnost proti chybám a zotavení po havárii

Odolnost proti chybám a zotavení po havárii je běžné potřebu online systémy. Službě Azure SignalR již zaručuje 99,9 % dostupnost, ale je stále místní služba.
Instance služby je vždy spuštěn v jedné oblasti a nebude převzetí služeb při selhání do jiné oblasti, když dojde k výpadku celé oblasti.

Místo toho naše sady SDK služby poskytuje funkce pro podporu více instancí služby SignalR a automaticky přepnout na další instance, pokud některé z nich nejsou k dispozici.
Díky této funkci bude možné obnovit, když je po havárii probíhá, ale budete muset nastavit topologie systému práva sami. Budete se dozvíte, jak k tomu v tomto dokumentu.

## <a name="high-available-architecture-for-signalr-service"></a>Vysoce dostupnou architekturu pro služby SignalR

Pokud chcete mít mezi různými oblastmi odolnost proti chybám pro služby SignalR, budete muset nastavit víc instancí služby v různých oblastech. Takže když jedné oblasti je vypnutý, ostatní může sloužit jako zálohování.
Při připojování k serveru aplikace víc instancí služby, existují dvě role, primární a sekundární.
Primární je instancí, který trvá online provozu a sekundární je plně funkční, ale zálohování instance pro primární.
V naší implementaci SDK vyjednávání se vrátit pouze primární koncové body, tak v případě běžných klienti připojit jenom k primární koncových bodů.
Ale pokud primární instance je vypnutý, vyjednávání se vrátit sekundární koncové body, takže klient může pořád vytvořit připojení.
Primární instance a aplikačního serveru jsou připojené přes připojení normální serveru ale sekundární server pro instanci a aplikace jsou připojené prostřednictvím zvláštní druh připojení volat slabé připojení.
Hlavní rozdíl slabé připojení je, že ho nepřijme směrování připojení klienta, protože sekundární instance se obvykle nachází v jiné oblasti. Směrování klienta do jiné oblasti obvykle není optimální volbou (zvyšuje latenci).

Jedna instance služby může mít různé role, při připojování k více serverů aplikace.
Jeden typické nastavení pro různé oblasti scénář je, aby dva (nebo více) párů instancí služeb SignalR a serverů aplikací.
V každé aplikaci, která pár serveru a služby SignalR se nacházejí ve stejné oblasti a služby SignalR je připojen k serveru aplikace jako primární roli.
Mezi každé aplikaci, která páry serveru a služby SignalR jsou také připojené, ale SignalR bude sekundární při připojování k serveru v jiné oblasti.

V této topologii zprávy z jednoho serveru pořád se dá doručit do všech klientů jako všem serverům aplikace a ze způsobu vzájemného propojení instance služby SignalR.
Ale když se klient připojí, je vždy směrovat do aplikačního serveru ve stejné oblasti, abyste dosáhli latence sítě optimální.

Tady je diagram, který znázorňuje tyto topologie:

![topologie](media/signalr-disaster-recovery/topology.png)

## <a name="configure-app-servers-with-multiple-signalr-service-instances"></a>Konfigurace serverů aplikace s více instancemi služby SignalR

Jakmile budete mít servery služby a aplikace SignalR vytvořené v jednotlivých oblastech, můžete nakonfigurovat aplikačních serverů pro připojení k všech instancí služby SignalR.

Můžete to provést dvěma způsoby:

### <a name="through-config"></a>Prostřednictvím konfigurace

Už byste měli vědět jak nastavit připojovací řetězec služby SignalR prostřednictvím prostředí settings/web.cofig proměnné nebo aplikace, prostřednictvím položku konfigurace s názvem `Azure:SignalR:ConnectionString`.
Pokud máte více koncových bodů, můžete je nastavit v více položek konfigurace, každou v následujícím formátu:

```
Azure:SignalR:Connection:<name>:<role>
```

Tady `<name>` je název koncového bodu a `<role>` je jeho role (primární nebo sekundární).
Název je volitelný, ale mohou být užitečné, pokud chcete dále přizpůsobit chování směrování mezi několik koncových bodů.

### <a name="through-code"></a>Prostřednictvím kódu.

Pokud chcete uložit připojovací řetězec někde jinde, může také číst ve vašem kódu a použít je jako parametry při volání metody `AddAzureSignalR()` (v ASP.NET Core) nebo `MapAzureSignalR()` (v technologii ASP.NET).

Tady je ukázkový kód:

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

## <a name="failover-sequence-and-best-practice"></a>Pořadí převzetí služeb při selhání a osvědčených postupů

Nyní máte nastavení systému správné topologie. Pokaždé, když jedna instance služby SignalR je vypnutý, bude online provoz směruje do jiné instance.
Zde je, co se stane, když primární instance je mimo provoz (a obnoví po určité době):

1. Instance primární služby je mimo provoz, dojde ke ztrátě připojení všech serverů v této instanci.
2. Všechny servery připojené k této instanci se označí jako offline a vyjednávání se vrátí tento koncový bod a spouštění vrácení sekundárního koncového bodu.
3. Všechna připojení klientů v této instanci také byla uzavřena, klienti se znovu připojit. Vzhledem k tomu, že servery aplikace se teď vrátit sekundární koncový bod, klienti se budou připojovat k sekundární instanci.
4. Sekundární instance nyní přijímá veškerý provoz online. Všechny zprávy ze serveru klientům doručovat stále jako sekundární je připojený ke všem serverům aplikace. Ale klienta na server zprávy jsou směrovány pouze na aplikační server ve stejné oblasti.
5. Po primární instance je obnovená a zpátky do online režimu, budou aplikačního serveru navázat připojení k němu a označte ji jako v režimu online. Vyjednávání se nyní vrácení primárního koncového bodu znovu tak noví klienti připojeni zpět na primární. Ale existující klienti nebudou vyřadit a budou směrovány do sekundárního, dokud se sami odpojit.

Následující diagramy ukazují, jak se provádí převzetí služeb při selhání služby SignalR:

Fig.1 před převzetí služeb při selhání ![před převzetí služeb při selhání](media/signalr-disaster-recovery/before-failover.png)

Fig.2 po převzetí služeb při selhání ![po převzetí služeb při selhání](media/signalr-disaster-recovery/after-failover.png)

Fig.3 krátkého formátu času, po obnovení primární ![krátkého formátu času, po obnovení primární](media/signalr-disaster-recovery/after-recover.png)

Zobrazí se v normální velikosti písmen pouze primární aplikačního serveru a služby SignalR online provoz (modře).
Po převzetí služeb při selhání sekundární aplikační server a služby SignalR také aktivuje.
Po primární služby SignalR je zpátky do online režimu, budou noví klienti připojovat k primární SignalR. Ale existující klienti připojit k sekundární tak mají obě instance provoz.
Všechny existující klienti odpojit, systém bude zpět na normální (Fig.1).

Existují dva hlavní způsoby pro implementaci mezi různými oblastmi vysoce dostupnou architekturu:

1. První z nich je mít pár aplikační server a instance služby SignalR trvá veškerý provoz online, a jinou dvojici jako záložní (volá se aktivní/pasivní vysokou dostupnost, ukazuje Fig.1). 
2. Druhá je mít dva (nebo více) páry serverů aplikace a instancemi služby SignalR, každá část jedno přijímající online provozu a slouží jako zálohu pro ostatní kombinace (označované jako aktivní/aktivní, podobně jako Fig.3).

Služby SignalR může podporovat oba vzorky, hlavní rozdíl je, jak implementovat serverů aplikací.
Servery aplikace jsou aktivní/pasivní vysokou dostupnost, služby SignalR bude aktivní/pasivní vysoká dostupnost také (jak primární aplikačního serveru vrátí pouze jeho primární instance služby SignalR).
Pokud jsou servery aplikace aktivní/aktivní, SignalR také bude služba aktivní/aktivní (jako všechny servery aplikace vrátí svá vlastní primární instance SignalR, takže všechny z nich dokážete zajistit provoz).

Prosím poznamenat, bez ohledu na to, které vzory, které chcete použít, budete muset připojit k serveru aplikace jako primární každá instance služby SignalR.

Také vzhledem k povaze připojení SignalR (jde o připojení pro dlouhé) klientů bude docházet k připojení nedojde k havárii a provést převzetí služeb při selhání.
Budete potřebovat pro zpracování takových případech na straně klienta, aby byla transparentní pro vaše koncové zákazníky. Třeba znovu připojte po ukončení připojení.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak nakonfigurovat svoji aplikaci pro zajištění odolnosti proti chybám pro služby SignalR. Informace o tom další podrobnosti o serveru/klientu připojení a směrování připojení v SignalR služby, můžete si přečíst [v tomto článku](signalr-internals.md) pro interní informace služby SignalR.