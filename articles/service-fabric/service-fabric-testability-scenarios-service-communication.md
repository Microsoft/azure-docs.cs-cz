---
title: 'Testování: komunikace s službami'
description: Komunikace mezi službami je důležitým integračním bodem aplikace Service Fabric. Tento článek popisuje faktory návrhu a postupy testování.
ms.topic: conceptual
ms.date: 11/02/2017
ms.openlocfilehash: fbf889422d69d031d777acd80cf3becfb3184568
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575920"
---
# <a name="service-fabric-testability-scenarios-service-communication"></a>Service Fabric scénáře testování: komunikace se službou
Mikroslužby a architektury zaměřené na služby se přirozeně nacházejí v Azure Service Fabric. V těchto typech distribuovaných architektur se aplikace mikroslužeb s komponentami typicky skládají z několika služeb, které musí vzájemně komunikovat. I v nejjednodušších případech obvykle máte alespoň bezstavovou webovou službu a službu stavového úložiště dat, která potřebuje komunikovat.

Komunikace mezi službami je důležitým integračním bodem aplikace, protože každá služba zpřístupňuje vzdálené rozhraní API jiným službám. Práce se sadou hranic rozhraní API, která zahrnuje vstupně-výstupní operace obecně vyžaduje určitou péči, s dobrým množstvím testování a ověřením.

V distribuovaném systému je potřeba udělat spoustu informací, když se tyto hranice služby budou v distribuovaném systému navzájemovat:

* *Transportní protokol*. Budete pro zvýšení interoperability používat protokol HTTP nebo vlastní binární protokol pro maximální propustnost?
* *Zpracování chyb*. Jak budou zpracovávány trvalé a přechodné chyby? Co se stane, když se služba přesune na jiný uzel?
* *Vypršení časových limitů a latence*. Jak bude v vícevrstvých aplikacích zacházet při latenci každé vrstvy služby prostřednictvím zásobníku a uživatele?

Bez ohledu na to, jestli používáte jednu z vestavěných komunikačních komponent poskytovaných Service Fabric nebo si vytvoříte vlastní, testování interakcí mezi vašimi službami je důležité pro zajištění odolnosti vaší aplikace.

## <a name="prepare-for-services-to-move"></a>Příprava na přesun služeb
Instance služby se můžou pohybovat v průběhu času. To platí hlavně v případě, že jsou nakonfigurované s metrikami zatížení pro optimální vyrovnávání zatížení prostředků s vlastním přizpůsobením. Service Fabric přesouvá vaše instance služby za účelem maximalizace jejich dostupnosti, a to i během upgradů, převzetí služeb při selhání, škálování na více systémů a dalších situacích, ke kterým dojde po dobu životnosti distribuovaného systému.

Protože se služby v clusteru pohybují, klienti a další služby by se měly připravit na zpracování dvou scénářů při komunikaci se službou:

* Instance služby nebo replika oddílu se přesunuly od doby, kdy jste je naposledy mluvili. Jedná se o běžnou součást životního cyklu služeb a měla by se vám stát, že se bude během životnosti aplikace očekávat.
* Instance služby nebo replika oddílu se právě přesouvá. I když převzetí služeb při selhání služby z jednoho uzlu na jiný probíhá velmi rychle v Service Fabric, může dojít ke zpoždění dostupnosti, pokud se komunikační komponenta vaší služby pomalu spustí.

Řádné zpracování těchto scénářů je důležité pro bezproblémové spuštění systému. V takovém případě mějte na paměti, že:

* Každá služba, ke které se dá připojit, má *adresu* , kterou naslouchá (například http nebo WebSockets). Po přesunutí instance služby nebo oddílu se změní koncový bod adresy. (Přesune se na jiný uzel s jinou IP adresou.) Pokud používáte integrované komunikační komponenty, budou se znovu překládat adresy služby za vás.
* Může docházet k dočasnému nárůstu latence služby, protože instance služby spouští svůj naslouchací proces znovu. To závisí na tom, jak rychle služba otevře naslouchací proces po přesunu instance služby.
* Po otevření služby na novém uzlu je nutné zavřít a znovu otevřít všechna existující připojení. Bezproblémové vypnutí nebo restartování uzlu umožňuje bezproblémové vypnutí stávajících připojení.

### <a name="test-it-move-service-instances"></a>Test: přesunutí instancí služby
Pomocí nástrojů pro testování Service Fabric můžete vytvořit testovací scénář pro otestování těchto situací různými způsoby:

1. Přesuňte primární repliku stavové služby.
   
    Primární repliku oddílu stavové služby lze přesunout z libovolného počtu důvodů. Tuto možnost použijte, chcete-li cílit na primární repliku konkrétního oddílu, abyste viděli, jak vaše služby reagují na pohyb v rámci velmi řízeného způsobu.
   
    ```powershell
   
    PS > Move-ServiceFabricPrimaryReplica -PartitionId 6faa4ffa-521a-44e9-8351-dfca0f7e0466 -ServiceName fabric:/MyApplication/MyService
   
    ```
2. Zastavte uzel.
   
    Když je uzel zastavený, Service Fabric přesune všechny instance služby nebo oddíly, které byly v tomto uzlu, do jednoho z dalších dostupných uzlů v clusteru. Tuto možnost použijte, chcete-li otestovat situaci, kdy dojde ke ztrátě uzlu z clusteru a zda je nutné přesunout všechny instance služby a repliky v tomto uzlu.
   
    Uzel můžete zastavit pomocí rutiny PowerShellu **stop-ServiceFabricNode** :
   
    ```powershell
   
    PS > Stop-ServiceFabricNode -NodeName Node_1
   
    ```

## <a name="maintain-service-availability"></a>Údržba dostupnosti služby
Jako platforma je Service Fabric navržená tak, aby poskytovala vysokou dostupnost vašich služeb. V extrémních případech ale můžou problémy s infrastrukturou způsobit nedostupnost. Pro tyto scénáře je důležité ho otestovat.

Stavové služby používají systém založený na kvoru k replikaci stavu pro zajištění vysoké dostupnosti. To znamená, že musí být k dispozici kvorum replik, aby bylo možné provádět operace zápisu. Ve výjimečných případech, jako je například rozšíření selhání hardwaru, nemusí být k dispozici kvorum replik. V těchto případech nebudete moci provádět operace zápisu, ale stále budete moci provádět operace čtení.

### <a name="test-it-write-operation-unavailability"></a>Test: operace zápisu je nedostupná.
Pomocí nástrojů pro testování v Service Fabric můžete vložit chybu, která vydává ztrátu kvora jako test. I když je takový scénář vzácný, je důležité, aby klienti a služby, které jsou závislé na stavové službě, byly připravené na zpracování v situacích, kdy do nich nemůžou požadavky na zápis dělat. Je také důležité, aby se tato možnost dozvěděla stavová služba a mohla ji řádně sdělit volajícím.

Ztrátu kvora můžete vyvolávat pomocí rutiny **Invoke-ServiceFabricPartitionQuorumLoss** prostředí PowerShell:

```powershell

PS > Invoke-ServiceFabricPartitionQuorumLoss -ServiceName fabric:/Myapplication/MyService -QuorumLossMode QuorumReplicas -QuorumLossDurationInSeconds 20

```

V tomto příkladu jsme nastavili `QuorumLossMode` `QuorumReplicas` , aby označoval, že chceme vyvolat ztrátu kvora bez nutnosti pořizovat všechny repliky. Tímto způsobem jsou operace čtení stále možné. Chcete-li otestovat situaci, kdy není k dispozici celý oddíl, můžete nastavit tento přepínač na `AllReplicas` .

## <a name="next-steps"></a>Další kroky
[Další informace o akcích testování](service-fabric-testability-actions.md)

[Další informace o scénářích testování](service-fabric-testability-scenarios.md)

