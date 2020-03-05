---
title: Řešení potíží s připojením služby Azure Virtual Network NAT
titleSuffix: Azure Virtual Network NAT troubleshooting
description: Řešení potíží se službou Virtual Network NAT
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to troubleshoot Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/02/2020
ms.author: allensu
ms.openlocfilehash: 185556e3045cb1a879bc256f4f2932549de71cd8
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274939"
---
# <a name="troubleshoot-azure-virtual-network-nat-connectivity-problems"></a>Řešení potíží s připojením služby Azure Virtual Network NAT

Tento článek pomáhá správcům diagnostikovat a řešit problémy s připojením při použití Virtual Network NAT.

>[!NOTE] 
>Virtual Network překlad adres (NAT) je v tuto chvíli k dispozici jako Public Preview. V současné době je dostupná jenom v omezené sadě [oblastí](nat-overview.md#region-availability). Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce nemusí být podporované nebo můžou mít omezené možnosti. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms).

## <a name="problems"></a>Problémy

- [Vyčerpání SNAT](#snat-exhaustion).
- [Nezdařila se chyba testu ICMP](#icmp-ping-is-failing).

Chcete-li tyto problémy vyřešit, postupujte podle kroků v následující části.

## <a name="resolution"></a>Řešení

### <a name="snat-exhaustion"></a>Vyčerpání SNAT

Jeden [prostředek brány NAT](nat-gateway-resource.md) podporuje 64 000 až 1 000 000 souběžných toků.  Každá IP adresa poskytuje k dostupnému inventáři porty 64 000 SNAT. Můžete použít až 16 IP adres na jeden prostředek brány NAT.  Mechanizmus SNAT je [zde](nat-gateway-resource.md#source-network-address-translation) popsán podrobněji.

#### <a name="steps"></a>Kroky:

1. Prozkoumejte, jak vaše aplikace vytváří odchozí připojení (například revize kódu nebo zachycení paketů). 
2. Určete, zda má tato aktivita očekávané chování nebo zda se aplikace nechová.  Využijte metriky v Azure Monitor k odůvodnění vašich zjištění.
3. Vyhodnotí, zda jsou následovány příslušné vzory.
4. Vyhodnoťte, jestli se má vyčerpání portů SNAT zmírnit pomocí dalších IP adres přiřazených k prostředku brány NAT.

#### <a name="design-pattern"></a>Vzor návrhu:

Kdykoli je to možné, využijte výhod opětovného použití připojení a sdružování připojení.  Tento model se vyhne problémům s vyčerpáním prostředků v nesprávném důsledku a vede k předvídatelnému chování. Primitivní prvky pro tyto vzory se dají najít v řadě vývojových knihoven a architektur.
- Zvažte [asynchronní vzorce cyklického dotazování](https://docs.microsoft.com/azure/architecture/patterns/async-request-reply) pro dlouhotrvající operace pro uvolnění prostředků připojení pro jiné operace.
- Dlouhodobé toky (například opakovaně používané připojení TCP) by měly používat udržení protokolu TCP nebo udržení nedostatku aplikační vrstvy, aby se předešlo vypršení zprostředkujících systémů.
- [Vzorce opakovaného opakování](https://docs.microsoft.com/azure/architecture/patterns/retry) by se měly používat k tomu, aby se předešlo agresivním pokusům o opakování nebo selhání při přechodném selhání nebo obnovení.
Vytvoření nového připojení TCP pro každou operaci HTTP (označované také jako "atomická připojení") je anti-Pattern.  Atomická připojení zabrání vaší aplikaci v škálování prostředků na dobrém a odpadním zdroji.  Vždy prokanálů více operací do stejného připojení.  Vaše aplikace bude využívat rychlost transakcí a náklady na prostředky.  Když vaše aplikace používá šifrování transportní vrstvy (například TLS), jsou k dispozici značné náklady spojené se zpracováním nových připojení.  Další vzory osvědčených postupů najdete v [vzorech návrhu cloudu Azure](https://docs.microsoft.com/azure/architecture/patterns/) .

#### <a name="mitigations"></a>Omezení rizik

Odchozí připojení můžete škálovat následujícím způsobem:

| Scénář | Omezení rizik |
|---|---|
| U portů SNAT a vyčerpání portů SNAT během období vysokého využití dochází ke sporu. | Určete, jestli můžete přidat další prostředky veřejné IP adresy nebo prostředky předpony veřejných IP adres. Díky tomu bude pro vaši bránu NAT možné celkem až 16 IP adres. Toto přidání vám poskytne více inventářů pro dostupné porty SNAT (64 000 na IP adresu) a umožní vám ještě víc škálovat scénář.|
| Už jste zadali 16 IP adres a pořád dochází k vyčerpání portů SNAT. | Distribuujte aplikační prostředí napříč více podsítěmi a poskytněte prostředek brány NAT pro každou podsíť. |

>[!NOTE]
>Je důležité pochopit, proč dochází k vyčerpání SNAT. Ujistěte se, že používáte správné vzory pro škálovatelné a spolehlivé scénáře.  Přidání dalších portů SNAT do scénáře bez porozumění příčině požadavku by mělo být poslední možnost. Pokud nerozumíte tomu, proč váš scénář uplatňuje tlak na inventáři portů SNAT, přidání dalších IP adres do inventáře se dokončí tak, že se při škálování aplikace odloží jenom stejný stav vyčerpání.  Můžete maskovat jiné neefektivity a antipatterny.

### <a name="icmp-ping-is-failing"></a>Nepodaří se odeslat test ICMP

[Virtual Network NAT](nat-overview.md) podporuje protokoly IPv4 UDP a TCP. Protokol ICMP není podporován a očekává se selhání.  Místo toho použijte testy připojení TCP (například "TCP test") a testy aplikační vrstvy specifické pro protokol UDP k ověření koncového a koncového připojení.

Následující tabulka slouží jako výchozí bod, ve kterém se nástroje použijí pro spuštění testů.

| Operační systém | Test obecného připojení TCP | Test aplikační vrstvy TCP | UDP |
|---|---|---|---|
| Linux | NC (test obecného připojení) | kudrlinkou (test aplikační vrstvy TCP) | specifické pro aplikaci |
| Windows | [PsPing](https://docs.microsoft.com/sysinternals/downloads/psping) | Vyvolání PowerShellu [– WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest) | specifické pro aplikaci |

## <a name="next-steps"></a>Další kroky

- Informace o [Virtual Network NAT](nat-overview.md)
- Další informace o [prostředku brány NAT](nat-gateway-resource.md)
