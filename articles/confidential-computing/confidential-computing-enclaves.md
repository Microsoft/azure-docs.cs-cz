---
title: Důvěrné výpočetní virtuální počítače v Azure
description: Přečtěte si o hardwaru Intel SGX, který umožňuje vaše úlohy s důvěrnými výpočetními procesy.
services: virtual-machines
author: JenCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 9/3/2020
ms.author: JenCook
ms.openlocfilehash: 565f4971fffde1cbeb2234b43aaad5cce73b5404
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564374"
---
# <a name="azure-confidential-computing-virtual-machines-vms-overview"></a>Přehled virtuálních počítačů s důvěrnými výpočetními platformami Azure


Azure je prvním poskytovatelem cloudu, který nabízí důvěrný výpočetní výkon ve virtualizovaném prostředí. Vyvinuli jsme virtuální počítače, které fungují jako abstrakcská vrstva mezi hardwarem a vaší aplikací. Můžete spouštět úlohy se škálováním a možnostmi redundance a dostupnost.  

## <a name="intel-sgx-enabled-virtual-machines"></a>Virtual Machines s podporou Intel SGX

V případě důvěrných výpočetních virtuálních počítačů Azure je součást hardwaru CPU vyhrazena pro část kódu a dat v aplikaci. Tato omezená část je enklávy. 

![Model virtuálního počítače](media/overview/hardware-backed-enclave.png)

Infrastruktura důvěrného výpočetního prostředí Azure se v současné době skládá ze speciální SKU virtuálních počítačů (VM). Tyto virtuální počítače běží na procesorech Intel s rozšířením software Guard (Intel SGX). [Intel SGX](https://intel.com/sgx) je komponenta, která umožňuje zvýšenou ochranu, kterou jsme zjistili v tajných výpočetních prostředích. 

V dnešní době Azure nabízí [řadu DCsv2](../virtual-machines/dcv2-series.md) založenou na technologii Intel SGX pro vytváření hardwarových enklávy. Můžete vytvářet zabezpečené aplikace založené na enklávy pro spouštění v DCsv2 virtuálních počítačích pro ochranu dat aplikací a používaného kódu. 

[Přečtěte si další](virtual-machine-solutions.md) informace o nasazení virtuálních počítačů s důvěrnými informacemi z Azure pomocí hardwarových enclavesch důvěryhodných systémů.

## <a name="enclaves"></a>Enclaves

Enclaves jsou zabezpečené části procesoru a paměti hardwaru. Neexistuje žádný způsob, jak zobrazit data nebo kód uvnitř enklávy, a to ani pomocí ladicího programu. Pokud se nedůvěryhodný kód pokusí změnit obsah v enklávy paměti, prostředí se zakáže a operace se odmítne.

V podstatě si enklávy jako zabezpečené pole. Šifrovaný kód a data vložíte do boxu. Z vnějšku pole neuvidíte cokoli. Dáte enklávy klíč k dešifrování dat, data se pak znovu zpracovávají a šifrují, než se odešlou z enklávy.

Každý enklávy má nastavenou velikost šifrované stránky cache (EPC), která určuje velikost paměti, kterou může každý enklávy uchovávat. Větší DCsv2 virtuální počítače mají větší paměť EPC. Přečtěte si stránku [specifikace DCsv2](../virtual-machines/dcv2-series.md) pro maximální velikost EPC na jeden virtuální počítač.



### <a name="developing-applications-to-run-inside-enclaves"></a>Vývoj aplikací pro běh v enclaves
Při vývoji aplikací můžete použít [softwarové nástroje](application-development.md) k ochraně částí kódu a dat v rámci enklávy. Tyto nástroje zajistí, že váš kód a data nebude moci zobrazit ani upravovat nikdo mimo důvěryhodné prostředí. 

## <a name="next-steps"></a>Další kroky
- [Vyčtěte si osvědčené postupy](virtual-machine-solutions.md) pro nasazení řešení na Azure důvěrné výpočetní virtuální počítače.
- [Nasazení virtuálního počítače s DCsv2-Series](quick-create-portal.md)
- [Vývoj aplikace s podporou enklávy](application-development.md) pomocí sady OE SDK