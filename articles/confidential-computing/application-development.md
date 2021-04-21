---
title: Nástroje pro vývoj důvěrných výpočetních prostředků Azure
description: Použití nástrojů a knihoven pro vývoj aplikací pro důvěrný výpočetní výkon
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 571c1a4ce545976db09f46a07d963d5344c02c29
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791008"
---
# <a name="application-development-on-intel-sgx"></a>Vývoj aplikací v Intel SGX 


Infrastruktura důvěrné výpočetní techniky vyžaduje konkrétní nástroje a software. Tato stránka konkrétně popisuje koncepty týkající se vývoje aplikací pro virtuální počítače s důvěrnými výpočetními systémy Azure, které běží na Intel SGX. Než si přečtete tuto stránku, [Přečtěte si Úvod k virtuálním počítačům Intel SGX a enclaves](confidential-computing-enclaves.md). 

K využití síly enclaves a izolované prostředí budete muset použít nástroje, které podporují důvěrný výpočetní výkon. Existují různé nástroje, které podporují vývoj aplikací enklávy. Můžete například použít tyto Open-Source rozhraní: 

- [Open enklávy Software Development Kit (OE SDK)](#oe-sdk)
- [Sada EGo Software Development Kit](#ego)
- [Rámec důvěrného konsorcia (CCF)](#ccf)

## <a name="overview"></a>Přehled

Aplikace sestavená pomocí enclaves je rozdělená dvěma způsoby:

1. Nedůvěryhodná součást (hostitel)
1. "Důvěryhodná" komponenta (enklávy)


![Vývoj aplikací](media/application-development/oe-sdk.png)


**Hostitel** je, kde vaše aplikace enklávy běží na začátku a je nedůvěryhodné prostředí. K enklávy kódu nasazenému v hostiteli nelze použít hostitele. 

**Enklávy** je místo, kde se spouští kód aplikace a jeho data/paměť v mezipaměti. K zabezpečeným výpočtům by mělo dojít v enclaves, aby se zajistila ochrana tajných kódů a citlivých dat. 


Během návrhu aplikace je důležité identifikovat a určit, jaká část aplikace musí běžet v enclaves. Kód, který se rozhodnete vložit do důvěryhodné součásti, je izolovaný od zbytku vaší aplikace. Jakmile je enklávy inicializován a kód je načten do paměti, tento kód nelze číst nebo změnit z nedůvěryhodných komponent. 

## <a name="open-enclave-software-development-kit-oe-sdk"></a>Otevřete enklávy Software Development Kit (OE SDK). <a id="oe-sdk"></a>

Použijte knihovnu nebo architekturu podporovanou vaším poskytovatelem, pokud chcete napsat kód, který běží v enklávy. [Open ENKLÁVY SDK](https://github.com/openenclave/openenclave) (OE SDK) je open source sada SDK, která umožňuje abstrakci nad jiným důvěrným hardwarem, který je povolený. 

Sada OE SDK je sestavena tak, aby byla jednou z abstrakcních vrstev na jakémkoli poskytovateli CSP. Sada OE SDK se dá využít na virtuální počítače s důvěrnými výpočetními prostředími Azure k vytváření a spouštění aplikací nad enclaves.

## <a name="ego-software-development-kit"></a>EGo Software Development Kit <a id="ego"></a>

[EGo](https://ego.dev/) je open-source sada SDK, která umožňuje spouštět aplikace napsané v programovacím jazyce jazyka jít v rámci enclaves. EGo sestaví na webu OE SDK a dodává se s knihovnou v enklávy pro ověřování a zapečetění. Mnohé existující aplikace v cestách běží na EGo bez úprav.  

## <a name="confidential-consortium-framework-ccf"></a>Důvěrné rozhraní Consortium (CCF) <a id="ccf"></a>

[CCF](https://github.com/Microsoft/CCF) je distribuovaná síť uzlů, z nichž každá spouští vlastní enclaves. Síť důvěryhodných uzlů umožňuje spustit distribuovanou účetní knihu. Hlavní kniha poskytuje zabezpečené a spolehlivé součásti pro použití protokolu. 

![Uzly CCF](media/application-development/ccf.png)

Tato open-source architektura umožňuje vysoké, jemně odstupňované utajení a zásady správného řízení konsorcia pro blockchain. U každého uzlu pomocí TEEs můžete zajistit zabezpečenou shodu a zpracování transakcí.


## <a name="next-steps"></a>Další kroky 
- [Nasazení virtuálního počítače s důvěrnými výpočetními DCsv2-Series](quick-create-portal.md)
- [Stažení a instalace sady OE SDK a zahájení vývoje aplikací](https://github.com/openenclave/openenclave)
