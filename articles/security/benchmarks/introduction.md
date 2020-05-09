---
title: Úvod ke srovnávacím testům zabezpečení Azure
description: Úvod do zabezpečení srovnávacích testů
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 883042c94576014c3bdeabd38516d9531c8fd3dc
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2020
ms.locfileid: "82606853"
---
# <a name="azure-security-benchmark-introduction"></a>Úvod do Azure Security srovnávacích testů

V místním výpočetním prostředí může být několik let nebo dokonce i desetiletí. Víte, jak tato nasazení zabezpečit; ale Cloud se liší. Jak zjistíte, jestli jsou vaše cloudová nasazení zabezpečená? Jaké jsou rozdíly mezi postupy zabezpečení pro místní systémy a nasazení v cloudu?

Existuje velký počet dokumentů White Paper, osvědčené postupy, referenční architektury, webové doprovodné materiály, nástroje open source, komerční řešení, informační kanály pro Intelligence a další, které vám můžou pomoci zabezpečit Cloud. Jakou možnost byste měli použít? Co můžete udělat, abyste získali přijatelnou úroveň zabezpečení v cloudu? 

Jedním z nejlepších způsobů, jak zabezpečit nasazení v cloudu, je zaměřit se na doporučení týkající se srovnávacích standardů cloudového zabezpečení. Doporučení srovnávacích standardů pro zabezpečení jakékoli služby, zahajte základní porozumění riziku kyberbezpečnosti a způsob jejich správy. Toto porozumění pak můžete použít tak, že si od poskytovatele cloudových služeb přijmete doporučení zabezpečení srovnávacích testů, které vám umožní vybrat konkrétní nastavení konfigurace zabezpečení ve vašem prostředí. 

Srovnávací test zabezpečení Azure zahrnuje kolekci vysoce ovlivňujících doporučení zabezpečení, která můžete použít k zabezpečení většiny služeb, které používáte v Azure. Tato doporučení si můžete představit jako "Obecné" nebo "organizační", protože se vztahují na většinu služeb Azure. Doporučení pro srovnávací testy zabezpečení Azure se pak přizpůsobují pro každou službu Azure a tyto přizpůsobené doprovodné materiály jsou uvedené v článcích o doporučeních pro služby. 

Dokumentace k bezpečnostnímu testu Azure v dokumentaci určuje bezpečnostní mechanismy a doporučení pro služby.

- **Řízení zabezpečení**: doporučení pro srovnávací testy zabezpečení Azure jsou rozdělená do kategorií podle řízení zabezpečení. Ovládací prvky zabezpečení reprezentují požadavky na zabezpečení nezávislá dodavatele na vysoké úrovni, jako je například zabezpečení sítě a ochrana dat. Každé řízení zabezpečení má sadu doporučení zabezpečení a pokyny, které vám pomůžou tato doporučení implementovat. 
- **Doporučení služby**: Pokud je k dispozici, doporučení pro srovnávací testy pro služby Azure budou zahrnovat doporučení pro srovnávací testy zabezpečení Azure, která jsou upravena speciálně pro danou službu. 

Výrazy "Control", "srovnávací" a "základní" jsou často používány v dokumentaci srovnávacích testů zabezpečení Azure a je důležité porozumět tomu, jak Azure tyto výrazy používá. 

| Označení | Popis | Příklad |
|--|--|--|
| Řízení | **Ovládací prvek** je podrobný popis funkce nebo aktivity, která se musí řešit a která není specifická pro technologii nebo implementaci. | Ochrana dat je jedním z ovládacích prvků zabezpečení. Tento ovládací prvek obsahuje konkrétní akce, které je třeba řešit, aby bylo zajištěno, že dojde k ochraně dat. |
| Srovnávací test | **Srovnávací test** obsahuje doporučení pro zabezpečení konkrétní technologie, jako je Azure. Doporučení jsou zařazená do kategorie podle ovládacího prvku, ke kterému patří. | Srovnávací test zabezpečení Azure zahrnuje doporučení týkající se zabezpečení, která jsou specifická pro platformu Azure.  |
| Standardní hodnoty | **Základní** hodnota je požadavky na zabezpečení organizace. Požadavky na zabezpečení jsou založeny na doporučeních srovnávacích testů. Každá organizace určuje, která doporučení srovnávacích testů se mají zahrnout do jejich standardních hodnot. | Společnost Contoso vytvoří svůj základní plán zabezpečení tím, že se rozhodne, že bude vyžadovat konkrétní doporučení v rámci srovnávacího testu zabezpečení Azure. |

Uvítáme vaše názory na srovnávací testy zabezpečení Azure. V níže uvedené oblasti zpětné vazby doporučujeme zadat komentáře. Pokud dáváte přednost sdílení vstupu s týmem Azure Security test, budete připraveni vyplnit formulář na stráncehttps://aka.ms/AzSecBenchmark 
