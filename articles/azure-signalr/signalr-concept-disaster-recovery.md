---
title: Odolnost a zotavení po havárii ve službě Azure Signal
description: Přehled postupu nastavení více instancí služby signalizace pro zajištění odolnosti a zotavení po havárii
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 03/01/2019
ms.author: kenchen
ms.openlocfilehash: b1cb48d1ae858dbcd0df80780b4c3cee3deac75b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90976498"
---
# <a name="resiliency-and-disaster-recovery-in-azure-signalr-service"></a>Odolnost a zotavení po havárii ve službě Azure Signal

Odolnost proti chybám a zotavení po havárii je běžnou potřebou pro online systémy. Služba signalizace Azure již garantuje 99,9% dostupnost, ale je stále místní službou.
Instance služby je vždycky spuštěná v jedné oblasti a při výpadku v rámci oblasti se nepřevezme do jiné oblasti.

Místo toho poskytuje naše sada SDK funkce pro podporu více instancí služby signalizace a automatické přepnutí na jiné instance, pokud některé z nich nejsou k dispozici.
Pomocí této funkce se budete moci zotavit, když dojde k havárii, ale budete muset nastavit správnou systémovou topologii sami. V tomto dokumentu se dozvíte, jak to udělat.

## <a name="high-available-architecture-for-signalr-service"></a>Vysoká dostupná architektura pro službu Signal

Aby bylo možné zajistit odolnost pro službu Signal u více oblastí, je nutné nastavit více instancí služby v různých oblastech. Takže pokud je jedna oblast mimo provoz, je možné použít jiné jako zálohu.
Při připojování více instancí služby k aplikačnímu serveru jsou k dispozici dvě role, primární a sekundární.
Primární je instance, která přebírá online provoz a sekundární je plně funkční, ale instance zálohování pro primární.
V naší implementaci sady SDK vrátí funkce Negotiate pouze primární koncové body, takže v normálních případech se klienti připojují pouze k primárním koncovým bodům.
Pokud je však primární instance mimo provoz, vyjednávat vrátí sekundární koncové body, aby klient stále mohl vytvořit připojení.
Primární instance a Server aplikací jsou propojené prostřednictvím normálního připojení k serveru, ale sekundární instance a Server App Server jsou propojené prostřednictvím speciálního typu připojení označovaného jako slabé připojení.
Hlavním rozdílem slabého připojení je, že nepřijímá směrování připojení klienta, protože je sekundární instance umístěna v jiné oblasti. Směrování klienta do jiné oblasti není optimální volbou (zvyšuje latenci).

Jedna instance služby může mít různé role při připojování k více aplikačním serverům.
Jedním z typických nastavení pro scénář mezi jednotlivými oblastmi je mít dvě (nebo více) páry instancí služby signalizace a aplikačních serverů.
V rámci každého páru aplikačního serveru a služby signalizace se nachází ve stejné oblasti a služba signálu je připojená k aplikačnímu serveru jako primární role.
Mezi jednotlivými páry aplikační server a služba signalizace se také připojí, ale při připojování k serveru v jiné oblasti se Signale jako sekundární.

V této topologii se může zpráva z jednoho serveru dál doručovat všem klientům, protože se vzájemně spojují všechny aplikační servery a instance služby Signal.
Pokud je však klient připojen, je vždy směrován do aplikačního serveru ve stejné oblasti, aby bylo dosaženo optimální latence sítě.

Níže je diagram, který znázorňuje takovou topologii:

![Diagram zobrazuje dvě oblasti každý pomocí aplikačního serveru a služby signalizace, kde je každý server přidružený ke službě signalizace ve své oblasti jako primární a ke službě v jiné oblasti jako sekundární.](media/signalr-concept-disaster-recovery/topology.png)

## <a name="configure-app-servers-with-multiple-signalr-service-instances"></a>Konfigurace aplikačních serverů s více instancemi služby signalizace

Jakmile budete mít službu signalizace a aplikační servery vytvořené v každé oblasti, můžete nakonfigurovat aplikační servery tak, aby se připojovaly ke všem instancím služby signalizace.

Můžete to provést dvěma způsoby:

### <a name="through-config"></a>Prostřednictvím konfigurace

Už byste měli znát, jak nastavit připojovací řetězec služby signalizace prostřednictvím proměnných prostředí/nastavení aplikace/Web. cofig v konfigurační položce s názvem `Azure:SignalR:ConnectionString` .
Pokud máte více koncových bodů, můžete je nastavit v několika položkách konfigurace, každý v následujícím formátu:

```
Azure:SignalR:ConnectionString:<name>:<role>
```

Tady `<name>` je název koncového bodu a `<role>` jeho role (primární nebo sekundární).
Název je nepovinný, ale bude užitečný, pokud chcete dále přizpůsobit chování směrování mezi několika koncovými body.

### <a name="through-code"></a>Prostřednictvím kódu

Pokud upřednostňujete ukládání připojovacích řetězců jinam, můžete je také přečíst ve svém kódu a použít je jako parametry při volání `AddAzureSignalR()` (v ASP.NET Core) nebo `MapAzureSignalR()` (v ASP.NET).

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

Můžete nakonfigurovat několik primárních nebo sekundárních instancí. Pokud existuje více primárních nebo sekundárních instancí, vyjednávat vrátí koncový bod v následujícím pořadí:

1. Pokud existuje aspoň jedna primární instance online, vraťte náhodnou primární online instanci.
2. Pokud jsou všechny primární instance mimo provoz, vrátí instanci náhodného sekundárního online instance.

## <a name="failover-sequence-and-best-practice"></a>Sekvence převzetí služeb při selhání a osvědčené postupy

Nyní máte správnou nastavení systémové topologie. Pokaždé, když je instance služby signalizace vypnutá, provoz online bude směrován na jiné instance.
Tady se stane, co se stane, když primární instance nefunguje (a po nějaké době obnoví):

1. Instance primární služby je vypnutá, všechna připojení serveru v této instanci budou zahozena.
2. Všechny servery připojené k této instanci označí jako offline a vyjednávání zastaví vrácení tohoto koncového bodu a začne vracet sekundární koncový bod.
3. Všechna připojení klientů u této instance budou také zavřena, klienti se znovu připojí. Protože aplikační servery teď vracejí sekundární koncový bod, klienti se připojí k sekundární instanci.
4. Nyní sekundární instance přebírá veškerý online provoz. Všechny zprávy ze serveru do klientů je stále možné doručit jako sekundární je připojen ke všem aplikačním serverům. Zprávy klienta se serverem se ale směrují jenom na aplikační server ve stejné oblasti.
5. Po obnovení primární instance a návratu do režimu online bude aplikační server znovu navázat připojení k tomuto serveru a označí ho jako online. Vyjednávání bude nyní vracet znovu primární koncový bod, takže noví klienti budou připojeni zpět k primárnímu. Stávající klienti ale nebudou vyřazeni a budou se dál směrovat do sekundárního, dokud se nepřipojí sami.

Níže uvedené diagramy znázorňují, jak se převzetí služeb při selhání provádí ve službě signalizace:

Obrázek 1 před převzetím služeb při selhání ![ před selháním](media/signalr-concept-disaster-recovery/before-failover.png)

Obrázek. 2 po převzetí služeb při ![ selhání](media/signalr-concept-disaster-recovery/after-failover.png)

Obrázek. 3 krátká doba po primárním obnovování po primárním obnovování ![](media/signalr-concept-disaster-recovery/after-recover.png)

V normálním případě je možné zobrazit pouze primární server aplikací a služba signálu, které mají online provoz (modře).
Po převzetí služeb při selhání se sekundární aplikační server a služba Signal aktivují také jako aktivní.
Po opětovném obnovení služby primárního signálu do režimu online se noví klienti připojí k primárnímu signálu. Stávající klienti se ale pořád připojují k sekundárním, takže obě instance budou mít provoz.
Po odpojení všech stávajících klientů se systém vrátí do normálního (obrázek. 1).

Existují dva hlavní vzory pro implementaci architektury s vysokou dostupností mezi oblastmi:

1. První z nich je mít dvojici instancí aplikačního serveru a služby Signal Service, které přebírají všechny online přenosy, a mají další dvojici jako zálohu (s názvem aktivní/pasivní, znázorněnou na obrázku obrázek 1). 
2. Druhý má dvě (nebo víc) dvojic aplikačních serverů a instancí služby Signal, přičemž každý z nich se účastní provozu online provozu a slouží jako záloha pro jiné páry (s názvem aktivní/aktivní, podobně jako na obrázku. 3).

Služba signalizace podporuje oba vzory. hlavní rozdíl spočívá v tom, jak implementujete aplikační servery.
Pokud jsou aplikační servery aktivní/pasivní, služba signalizace bude taky aktivní/pasivní (protože primární server aplikace vrátí jenom primární instanci služby signalizace).
Pokud jsou aplikační servery aktivní/aktivní, bude služba Signal Service taky aktivní/aktivní (protože všechny aplikační servery vrátí vlastní primární instance signalizace, takže všechny z nich můžou získat provoz).

Poznamenali jste si, které způsoby použití se vám budou muset připojit k aplikačnímu serveru jako primárnímu.

Vzhledem k povaze připojení k signalizaci (Jedná se o připojení k signalizaci) se taky v případě výpadku a převzetí služeb při selhání uskuteční připojení.
Tyto případy budete muset zpracovávat na straně klienta, aby byly koncovým zákazníkům transparentní. Například se po zavření připojení znovu připojte.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili s postupem konfigurace aplikace pro zajištění odolnosti pro službu Signal. Chcete-li získat další podrobnosti o připojení serveru/klienta a směrování připojení ve službě Signaler, můžete si přečíst [Tento článek](signalr-concept-internals.md) pro interní služby signalizace.

Pro scénáře škálování, jako je například horizontálního dělení, které používají více instancí společně pro zpracování velkého počtu připojení, si přečtěte, [Jak škálovat více instancí](signalr-howto-scale-multi-instances.md).
