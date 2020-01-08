---
title: Úvod do Azure Security srovnávacích testů
description: Úvod do zabezpečení srovnávacích testů
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-baselines
ms.openlocfilehash: 2d0cceca841a2aff2538dcf8763d245ca8cfd13d
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2019
ms.locfileid: "75564358"
---
# <a name="azure-security-benchmarks-introduction"></a>Úvod do srovnávacích testů zabezpečení Azure

V místním výpočetním prostředí může být několik let nebo dokonce i desetiletí. Víte, jak tato nasazení zabezpečit. Ale Cloud se liší. Jak zjistíte, jestli jsou vaše cloudová nasazení zabezpečená? Jaké jsou rozdíly mezi postupy zabezpečení pro místní systémy a postupy zabezpečení pro cloudová nasazení?

Existuje velký počet dokumentů White Paper, osvědčené postupy, referenční architektury, webové doprovodné materiály, nástroje open source, komerční řešení, informační kanály pro Intelligence a další, které vám můžou pomoci zabezpečit Cloud. Jakou možnost byste měli použít? Co můžete udělat, abyste získali přijatelnou úroveň zabezpečení v cloudu? 

Jedním z nejlepších způsobů, jak zabezpečit nasazení v cloudu, je zaměřit se na doporučení týkající se srovnávacích standardů cloudového zabezpečení. Srovnávací doporučení pro zabezpečení jakékoli služby zahájí základní porozumění riziku kyberbezpečnosti a způsob jejich správy. Toto porozumění pak můžete použít tak, že si od poskytovatele cloudových služeb přijmete doporučení zabezpečení srovnávacích testů, které vám umožní vybrat konkrétní nastavení konfigurace zabezpečení ve vašem prostředí. 

Srovnávací test zabezpečení Azure zahrnuje kolekci vysoce ovlivňujících doporučení zabezpečení, která můžete použít k zabezpečení většiny služeb, které používáte v Azure. Tato doporučení si můžete představit jako "Obecné" nebo "organizační", protože se vztahují na většinu služeb Azure. Doporučení pro srovnávací testy zabezpečení Azure se pak přizpůsobují pro každou službu Azure a tyto přizpůsobené doprovodné materiály jsou uvedené v článcích o doporučeních pro služby. 

V dokumentaci ke srovnávacím testům Azure najdete bezpečnostní mechanismy a doporučení pro služby.

- **Řízení zabezpečení**: doporučení pro srovnávací testy zabezpečení Azure jsou rozdělená do kategorií podle řízení zabezpečení. Ovládací prvky zabezpečení reprezentují požadavky na zabezpečení nezávislá dodavatele na vysoké úrovni, jako je například zabezpečení sítě a ochrana dat. Každé řízení zabezpečení má sadu doporučení zabezpečení a pokyny, které vám pomůžou tato doporučení povolit. 
- **Doporučení služby**: Pokud je k dispozici, doporučení pro srovnávací testy pro služby Azure budou zahrnovat doporučení pro srovnávací testy zabezpečení Azure, která jsou přizpůsobená pro službu, a další doporučení, která jsou pro konkrétní službu jedinečná. 

Výrazy "Control", "srovnávací" a "základní" jsou často používány v dokumentaci srovnávacích testů zabezpečení Azure a je důležité porozumět tomu, jak Azure tyto výrazy používá. 

| Doba účinnosti | Popis | Příklad: |
|--|--|--|
| Řízení | **Ovládací prvek** je podrobný popis funkce nebo aktivity, kterou je třeba řešit, a není specifické pro technologii nebo implementaci. | Ochrana dat je jedním z ovládacích prvků zabezpečení. Tento ovládací prvek obsahuje konkrétní akce, které je třeba řešit, aby bylo zajištěno, že dojde k ochraně dat. |
| Srovnávací test | **Srovnávací test** obsahuje doporučení pro zabezpečení konkrétní technologie, jako je Azure. Doporučení jsou zařazená do kategorie podle ovládacího prvku, ke kterému patří. | Srovnávací test zabezpečení Azure zahrnuje doporučení týkající se zabezpečení, která jsou specifická pro platformu Azure.  |
| Reference | **Základní** hodnota je požadavky na zabezpečení organizace. Požadavky na zabezpečení jsou založeny na doporučeních srovnávacích testů. Každá organizace určuje, která doporučení srovnávacích testů se mají zahrnout do jejich standardních hodnot. | Společnost Contoso vytvoří svůj základní plán zabezpečení tím, že se rozhodne, že bude vyžadovat konkrétní doporučení v rámci srovnávacího testu zabezpečení Azure. |

Uvítáme vaše názory na srovnávací testy zabezpečení Azure. V níže uvedené oblasti zpětné vazby doporučujeme zadat komentáře. Pokud dáváte přednost sdílení vašeho vstupu s týmem Azure Security test, budete připraveni vyplnit formulář na https://aka.ms/AzSecBenchmark 