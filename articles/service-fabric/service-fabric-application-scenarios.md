---
title: Scénáře aplikací a návrh
description: Přehled kategorií cloudových aplikací v Service Fabric. Popisuje návrh aplikace, který používá stavové a bezstavové služby.
ms.topic: conceptual
ms.date: 01/08/2020
ms.custom: sfrev
ms.openlocfilehash: 0aeb8ab2923915befdd11f96025687be3b3c4ff9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76024734"
---
# <a name="service-fabric-application-scenarios"></a>Scénáře aplikace Service Fabric

Azure Service Fabric nabízí spolehlivou a flexibilní platformu, kde můžete psát a spouštět mnoho typů obchodních aplikací a služeb. Tyto aplikace a mikroslužby mohou být bezstavové nebo stavové a jsou vyvážené prostředky napříč virtuálními počítači, aby se maximalizovala efektivita.

Jedinečná architektura Service Fabric umožňuje provádět analýzu dat téměř v reálném čase, výpočty v paměti, paralelní transakce a zpracování událostí ve vašich aplikacích. Aplikace můžete snadno škálovat v závislosti na měnících se požadavcích na prostředky.

Pokyny k návrhu pro vytváření aplikací načtete [architekturu mikroslužeb v Azure Service Fabric](https://docs.microsoft.com/azure/architecture/reference-architectures/microservices/service-fabric) a doporučené postupy pro návrh aplikací pomocí service fabric [.](service-fabric-best-practices-applications.md)

Zvažte použití platformy Service Fabric pro následující typy aplikací:

* **Shromažďování, zpracování dat a IoT**: Service Fabric zpracovává ve velkém měřítku a má nízkou latenci prostřednictvím svých stavových služeb. Může pomoci zpracovat data na milionech zařízení, kde jsou data pro zařízení a výpočty umístěna společně.

    Mezi zákazníky, kteří vytvořili služby IoT pomocí service fabric, patří [Honeywell](https://customers.microsoft.com/story/honeywell-builds-microservices-based-thermostats-on-azure), [PCL Construction](https://customers.microsoft.com/story/pcl-construction-professional-services-azure), [Crestron](https://customers.microsoft.com/story/crestron-partner-professional-services-azure), [BMW,](https://customers.microsoft.com/story/bmw-enables-driver-mobility-via-azure-service-fabric/) [Schneider Electric](https://customers.microsoft.com/story/schneider-electric-powers-engergy-solutions-on-azure-service-fabric)a Mesh [Systems](https://customers.microsoft.com/story/mesh-systems-lights-up-the-market-with-iot-based-azure-solutions).

* **Herní a relace založené interaktivní aplikace**: Service Fabric je užitečné, pokud vaše aplikace vyžaduje čtení a zápisy s nízkou latencí, například v online hraní nebo zasílání rychlých zpráv. Service Fabric umožňuje vytvářet tyto interaktivní, stavové aplikace bez nutnosti vytvářet samostatné úložiště nebo mezipaměti. Navštivte [herní řešení Azure,](https://azure.microsoft.com/solutions/gaming/) kde najdete pokyny k návrhu [používání Service Fabric v herních službách](https://docs.microsoft.com/gaming/azure/reference-architectures/multiplayer-synchronous-sf).

    Mezi zákazníky, kteří si vybudovali herní [služby,](https://customers.microsoft.com/story/next-games-media-telecommunications-azure) patří Next Games a [Digamore](https://customers.microsoft.com/story/digamore-entertainment-scores-with-a-new-gaming-platform-based-on-azure-service-fabric/). Mezi zákazníky, kteří vytvořili interaktivní relace, patří [Honeywell s Hololens](https://customers.microsoft.com/story/honeywell-manufacturing-hololens).

* **Analýza dat a zpracování pracovních postupů**: Aplikace, které musí spolehlivě zpracovávat události nebo datové proudy dat, těží z optimalizovaných čtení a zápisů ve službě Service Fabric. Service Fabric také podporuje kanály zpracování aplikací, kde výsledky musí být spolehlivé a předány do další fáze zpracování bez ztráty. Tyto kanály zahrnují transakční a finanční systémy, kde jsou nezbytné záruky konzistence dat a výpočtu.

    Mezi zákazníky, kteří vytvořili služby obchodních pracovních postupů, patří [Zeiss Group](https://customers.microsoft.com/story/zeiss-group-focuses-on-azure-service-fabric-for-key-integration-platform), [Quorum Business Solutions](https://customers.microsoft.com/en-us/story/quorum-business-solutions-expand-energy-managemant-solutions-using-azure-service-fabric)a [Société General](https://customers.microsoft.com/en-us/story/societe-generale-speeds-real-time-market-quotes-using-azure-service-fabric).

* **Výpočty na datech**: Service Fabric umožňuje vytvářet stavové aplikace, které provází intenzivní výpočty dat. Service Fabric umožňuje společné umístění zpracování (výpočtu) a dat v aplikacích. 

   Za normálních okolností, když vaše aplikace vyžaduje přístup k datům, latence sítě spojená s externí mezipamětí dat nebo vrstvou úložiště omezuje výpočetní čas. Stavové služby Fabric tuto latenci eliminují a umožňují optimalizovanější čtení a zápisy.

   Zvažte například aplikaci, která provádí téměř v reálném čase výběry doporučení pro zákazníky, s požadavkem na dobu odezvy menší než 100 milisekund. Latence a charakteristiky výkonu service fabric služby poskytují citlivé prostředí pro uživatele, ve srovnání s modelem standardní implementace museli načíst potřebná data ze vzdáleného úložiště. Systém je citlivější, protože výpočtu výběru doporučení je společně umístěn s daty a pravidly.

    Mezi zákazníky, kteří vytvořili výpočetní služby, patří [Solidsoft Reply](https://customers.microsoft.com/story/solidsoft-reply-platform-powers-e-verification-of-pharmaceuticals) a [Infosupport](https://customers.microsoft.com/story/service-fabric-customer-profile-info-support-and-fudura).

* **Vysoce dostupné služby**: Service Fabric poskytuje rychlé převzetí služeb při selhání vytvořením více replik sekundární služby. Pokud uzel, proces nebo jednotlivé služby přejde dolů z důvodu selhání hardwaru nebo jiného selhání, jedna ze sekundárních replik je povýšen na primární repliky s minimální ztrátou služby.

* **Škálovatelné služby**: Jednotlivé služby lze rozdělit na oddíly, což umožňuje škálovat stav v rámci clusteru. Jednotlivé služby mohou být také vytvořeny a odstraněny za běhu. Služby můžete škálovat z několika instancí na několika uzlech na tisíce instancí na mnoha uzlech a pak je podle potřeby znovu škálovat. Service Fabric můžete použít k vytvoření těchto služeb a spravovat jejich úplné životní cykly.

## <a name="application-design-case-studies"></a>Případové studie návrhu žádosti

Případové studie, které ukazují, jak service fabric se používá k navrhování aplikací jsou publikovány na [příběhy zákazníků](https://customers.microsoft.com/search?sq=%22Azure%20Service%20Fabric%22&ff=&p=2&so=story_publish_date%20desc) a mikroslužeb v lokalitách [Azure.](https://azure.microsoft.com/solutions/microservice-applications/)

## <a name="designing-applications-composed-of-stateless-and-stateful-microservices"></a>Navrhování aplikací složených ze bezstavových a stavových mikroslužeb

Vytváření aplikací s rolemi pracovních pracovníků Cloudových služeb Azure je příkladem bezstavové služby. Naproti tomu stavové mikroslužby zachovat jejich autoritativní stav mimo požadavek a jeho odpověď. Tato funkce poskytuje vysokou dostupnost a konzistenci stavu prostřednictvím jednoduchých api, která poskytují transakční záruky podporované replikací.

Stavové služby v Service Fabric přinášejí vysokou dostupnost pro všechny typy aplikací, nejen databází a dalších úložišť dat. To je přirozený vývoj. Aplikace se již přesunuly z používání čistě relačních databází pro vysokou dostupnost do noSQL databází. Nyní samotné aplikace mohou mít svůj "horký" stav a data v nich spravovaná pro další zvýšení výkonu bez obětování spolehlivosti, konzistence nebo dostupnosti.

Při vytváření aplikací, které se skládají z mikroslužeb, obvykle máte kombinaci bezstavové webové aplikace (jako je ASP.NET a Node.js) volání na bezstavové a stavové obchodní služby střední vrstvy. Všechny aplikace a služby jsou nasazeny ve stejném clusteru Service Fabric prostřednictvím příkazů nasazení Service Fabric. Každá z těchto služeb je nezávislá s ohledem na škálování, spolehlivost a využití prostředků. Tato nezávislost zlepšuje agilitu a flexibilitu ve vývoji a řízení životního cyklu.

Stavové mikroslužby zjednodušují návrhy aplikací, protože odeberou potřebu dalších front a mezipamětí, které byly tradičně vyžadovány k řešení požadavků na dostupnost a latenci čistě bezstavových aplikací. Vzhledem k tomu, že stavové služby mají vysokou dostupnost a nízkou latenci, je ve vaší aplikaci méně podrobností ke správě.

Následující diagramy znázorňují rozdíly mezi návrhem aplikace, která je bezstavová a která je stavová. Využitím spolehlivé [služby](service-fabric-reliable-services-introduction.md) a [spolehlivé actors](service-fabric-reliable-actors-introduction.md) programovací modely, stavové služby snížit složitost aplikace při dosažení vysoké propustnosti a nízké latence.

Zde je příklad aplikace, která používá ![bezstavové služby: Aplikace, která používá bezstavové služby][Image1]

Tady je ukázková aplikace, která ![používá stavové služby: Aplikace, která používá stavové služby][Image2]

## <a name="next-steps"></a>Další kroky

* Můžete začít vytvářet bezstavové a stavové služby s service fabric [spolehlivé služby](service-fabric-reliable-services-quick-start.md) a [spolehlivé actors](service-fabric-reliable-actors-get-started.md) programovací modely.
* Pokyny k vytváření mikroslužeb v [Azure najdete](https://docs.microsoft.com/azure/architecture/microservices/)v Centru architektury Azure .
* Přejděte na [azure service fabric aplikace a clusteru osvědčené postupy](service-fabric-best-practices-overview.md) pro návrh aplikace pokyny.

* Viz také:
  * [Principy mikroslužeb](service-fabric-overview-microservices.md)
  * [Definování a správa stavu služby](service-fabric-concepts-state.md)
  * [Dostupnost služeb](service-fabric-availability-services.md)
  * [Škálovací služby](service-fabric-concepts-scalability.md)
  * [Služby oddílů](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.png
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.png
