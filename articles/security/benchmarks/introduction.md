---
title: Úvod ke srovnávacím testům zabezpečení Azure
description: Zavedení srovnávacího testu zabezpečení
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 8f65eb008d5df6ea3f2f85778d538023e6ce98eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75945323"
---
# <a name="azure-security-benchmark-introduction"></a>Úvod srovnávacího testu zabezpečení Azure

Můžete mít několik let nebo dokonce desetiletí zkušeností s místními výpočty. Víte, jak zabezpečit tato nasazení; ale mrak je jiný. Jak poznáte, že jsou vaše cloudová nasazení zabezpečená? Jaké jsou rozdíly mezi postupy zabezpečení pro místní systémy a cloudová nasazení?

K dispozici je rozsáhlá sbírka dokumentů white paper, osvědčených postupů, referenčních architektur, webových pokynů, nástrojů s otevřeným zdrojovým kódem, komerčních řešení, informačních kanálů o inteligenci a dalších, které lze použít k zabezpečení cloudu. Kterou možnost byste měli použít? Co můžete udělat pro získání přijatelné úrovně zabezpečení v cloudu? 

Jedním z nejlepších způsobů zabezpečení cloudových nasazení je zaměřit se na doporučení pro srovnávací testy zabezpečení cloudu. Referenční doporučení pro zabezpečení jakékoli služby začínají základním chápáním rizikkybernetické bezpečnosti a jeho řízení. Toto porozumění pak můžete použít přijetím doporučení zabezpečení srovnávacích služeb od poskytovatele cloudových služeb, která vám pomohou vybrat konkrétní nastavení konfigurace zabezpečení ve vašem prostředí. 

Azure Security Benchmark obsahuje kolekci doporučení zabezpečení s vysokým dopadem, které můžete použít k zabezpečení většiny služeb, které používáte v Azure. Tato doporučení si můžete myslet jako "obecné" nebo "organizační", protože jsou použitelné pro většinu služeb Azure. Doporučení Azure Security Benchmark jsou pak přizpůsobeny pro každou službu Azure a tyto přizpůsobené pokyny jsou obsaženy v článcích doporučení služby. 

Dokumentace azure security benchmark určuje ovládací prvky zabezpečení a doporučení služby.

- **Ovládací prvky zabezpečení**: Doporučení azure benchmark zabezpečení jsou rozděleny do kategorií podle ovládacích prvků zabezpečení. Ovládací prvky zabezpečení představují požadavky na zabezpečení na vysoké úrovni agnostiky dodavatele, jako je například zabezpečení sítě a ochrana dat. Každý ovládací prvek zabezpečení obsahuje sadu doporučení zabezpečení a pokynů, které vám pomohou tato doporučení implementovat. 
- **Doporučení služby**: Pokud jsou dostupná doporučení pro srovnávací testy pro služby Azure, budou obsahovat doporučení azure security benchmark, která jsou přizpůsobená speciálně pro tuto službu. 

Termíny "Control", "Benchmark" a "Baseline" se často používají v dokumentaci Azure Security Benchmark a je důležité pochopit, jak Azure používá tyto termíny. 

| Označení | Popis | Příklad |
|--|--|--|
| Řízení | **Ovládací prvek** je popis funkce nebo aktivity na vysoké úrovni, který je třeba řešit a není specifický pro technologii nebo implementaci. | Ochrana dat je jedním z ovládacích prvků zabezpečení. Tento ovládací prvek obsahuje konkrétní akce, které je třeba řešit, aby bylo zajištěno, že jsou chráněna data. |
| Srovnávací test | **Srovnávací test** obsahuje doporučení zabezpečení pro konkrétní technologii, jako je například Azure. Doporučení jsou rozděleny do kategorií podle ovládacího prvku, ke kterému patří. | Referenční hodnota zabezpečení Azure zahrnuje doporučení zabezpečení specifická pro platformu Azure  |
| Standardní hodnoty | **Směrný plán** je požadavky na zabezpečení organizace. Požadavky na zabezpečení jsou založeny na doporučeních pro srovnávací testy. Každá organizace rozhoduje o tom, která doporučení pro srovnávací testy mají být zahrnuta do svého směrného plánu. | Společnost Contoso vytvoří svůj směrný plán zabezpečení tak, že se rozhodne vyžadovat konkrétní doporučení v benchmarku zabezpečení Azure. |

Uvítáme vaši zpětnou vazbu k benchmarku zabezpečení Azure! Doporučujeme vám poskytnout komentáře v oblasti zpětné vazby níže. Pokud dáváte přednost sdílení vašich vstupů více soukromě s týmem Azure Security Benchmark, můžete vyplnit formulář na adresehttps://aka.ms/AzSecBenchmark 
