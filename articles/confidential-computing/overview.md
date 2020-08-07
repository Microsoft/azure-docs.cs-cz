---
title: Přehled důvěrných výpočetních řešení Azure
description: Přehled výpočetních funkcí Azure důvěrné (ACC)
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: overview
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 4e92f974ce7d6c03143276808c4ca4d09d607a84
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835812"
---
# <a name="confidential-computing-on-azure"></a>Důvěrné výpočetní prostředí v Azure

Důvěrné výpočetní prostředí Azure umožňuje izolovat citlivá data při jejich zpracovávání v cloudu. Mnohé z odvětví využívají důvěrné výpočetní prostředí k ochraně svých dat. Mezi tyto úlohy patří:

- Zabezpečení finančních dat
- Ochrana informací o pacientech
- Spouštění procesů strojového učení na citlivých informacích
- Provádění algoritmů u šifrovaných datových sad z více zdrojů


## <a name="overview"></a>Přehled
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Víme, že zabezpečení dat v cloudu je důležité. Slyšeli jsme vaše obavy. Tady je jen pár otázek, které můžou mít naši zákazníci při přesunu citlivých úloh do cloudu: 

- Návody zajistěte, aby Microsoft nemohly získat přístup k nešifrovaným datům?
- Návody v rámci své společnosti zabránit bezpečnostním hrozbám od privilegovaných správců?
- Jaké jsou další způsoby, jak můžu zabránit třetím stranám v přístupu k citlivým datům zákazníků?

Microsoft Azure pomáhá minimalizovat plochu útoku a získat tak silnější ochranu dat. Azure už nabízí spoustu nástrojů pro ochranu uložených dat prostřednictvím modelů, jako je šifrování [**na**](../security/fundamentals/encryption-atrest.md) straně klienta a šifrování na straně serveru. Azure navíc nabízí mechanismy pro šifrování [**dat při přenosu**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) prostřednictvím zabezpečených protokolů, jako je TLS a HTTPS. Tato stránka představuje třetí nožku šifrování dat – šifrování **používaných dat**.


## <a name="introduction-to-confidential-computing"></a>Úvod do důvěrného výpočetního prostředí<a id="intro to acc"></a>

Důvěrné výpočetní prostředí je obor, který je definovaný [konsorciem důvěrného computingu](https://confidentialcomputing.io/) (CCC) – základ vyhrazený pro definování a urychlení přijetí důvěrného výpočetního prostředí. CCC definuje důvěrné výpočetní prostředky jako ochranu dat používaných při provádění výpočtů v prostředí TEE (hardwarového důvěryhodného spuštění).

TEE je prostředí, které vynutilo provádění pouze autorizovaného kódu. Žádná data v TEE nemůžou číst ani úmyslně poškodit žádný kód mimo toto prostředí.

### <a name="enclaves"></a>Enclaves

Enclaves jsou zabezpečené části procesoru a paměti hardwaru. Neexistuje žádný způsob, jak zobrazit data nebo kód uvnitř enklávy, a to ani pomocí ladicího programu. Pokud se nedůvěryhodný kód pokusí změnit obsah v enklávy paměti, prostředí se zakáže a operace se odmítne.

Při vývoji aplikací můžete použít [softwarové nástroje](#oe-sdk) k ochraně částí kódu a dat v rámci enklávy. Tyto nástroje zajistí, že váš kód a data nebude moci zobrazit ani upravovat nikdo mimo důvěryhodné prostředí. 

V podstatě si enklávy jako zabezpečené pole. Šifrovaný kód a data vložíte do boxu. Z vnějšku pole neuvidíte cokoli. Dáte enklávy klíč k dešifrování dat, data se pak znovu zpracovávají a šifrují, než se odešlou z enklávy.

### <a name="attestation"></a>Ověření

Budete chtít získat ověření a ověření, že je vaše důvěryhodné prostředí zabezpečené. Toto ověření je proces ověření identity. 

Ověřování identity umožňuje předávající straně zvýšit jistotu, že jejich software je (1) spuštěný v enklávy a (2) že enklávy je aktuální a zabezpečený. Například enklávy požádá o základní hardware, aby vygeneroval přihlašovací údaje, které obsahují důkaz o tom, že enklávy na platformě existuje. Tuto sestavu je pak možné udělit druhému enklávy, který ověří, že se sestava vygenerovala na stejné platformě.

Ověření identity se musí implementovat pomocí služby zabezpečeného ověřování identity, která je kompatibilní se systémovým softwarem a silikonem. [Ověření a služby zřizování Intel](https://software.intel.com/sgx/attestation-services) jsou kompatibilní s důvěrnými výpočetními virtuálními počítači Azure.


## <a name="using-azure-for-cloud-based-confidential-computing"></a>Použití Azure pro cloudově založené důvěrné výpočetní účely<a id="cc-on-azure"></a>

Důvěrné výpočetní prostředí Azure umožňuje využívat důvěrné výpočetní funkce ve virtualizovaném prostředí. Nyní můžete používat nástroje, software a cloudovou infrastrukturu k sestavování nad zabezpečeným hardwarem. 

### <a name="virtual-machines"></a>Virtual Machines

Azure je prvním poskytovatelem cloudu, který nabízí důvěrný výpočetní výkon ve virtualizovaném prostředí. Vyvinuli jsme virtuální počítače, které fungují jako abstrakcská vrstva mezi hardwarem a vaší aplikací. Můžete spouštět úlohy se škálováním a možnostmi redundance a dostupnost.  

#### <a name="intel-sgx-enabled-virtual-machines"></a>Virtual Machines s podporou Intel SGX

V případě důvěrných výpočetních virtuálních počítačů Azure je součást hardwaru CPU vyhrazena pro část kódu a dat v aplikaci. Tato omezená část je enklávy. 

![Model virtuálního počítače](media/overview/hardware-backed-enclave.png)

Infrastruktura důvěrného výpočetního prostředí Azure se v současné době skládá ze speciální SKU virtuálních počítačů (VM). Tyto virtuální počítače běží na procesorech Intel s rozšířením software Guard (Intel SGX). [Intel SGX](https://intel.com/sgx) je komponenta, která umožňuje zvýšenou ochranu, kterou jsme zjistili v tajných výpočetních prostředích. 

V dnešní době Azure nabízí [řadu DCsv2](https://docs.microsoft.com/azure/virtual-machines/dcv2-series) založenou na technologii Intel SGX pro vytváření hardwarových enklávy. Můžete vytvářet zabezpečené aplikace založené na enklávy pro spouštění v DCsv2 virtuálních počítačích pro ochranu dat aplikací a používaného kódu. 

Můžete [si přečíst další](virtual-machine-solutions.md) informace o nasazení virtuálních počítačů s důvěrnými službami Azure pomocí hardwarových důvěryhodných enclaves.

## <a name="application-development"></a>Vývoj aplikací<a id="application-development"></a>

K využití síly enclaves a izolované prostředí budete muset použít nástroje, které podporují důvěrný výpočetní výkon. Existují různé nástroje, které podporují vývoj aplikací enklávy. Můžete například použít tyto Open-Source rozhraní: 

- [Sada open enklávy Software Development Kit (SDK)](https://github.com/openenclave/openenclave)
- [Rámec důvěrného konsorcia (CCF)](https://github.com/Microsoft/CCF)

### <a name="overview"></a>Přehled

Aplikace sestavená pomocí enclaves je rozdělená dvěma způsoby:
1. Nedůvěryhodná součást (hostitel)
1. "Důvěryhodná" komponenta (enklávy)

**Hostitel** je, kde vaše aplikace enklávy běží na začátku a je nedůvěryhodné prostředí. K enklávy kódu nasazenému v hostiteli nelze použít hostitele. 

**Enklávy** je místo, kde se spouští kód aplikace a jeho data/paměť v mezipaměti. K zabezpečeným výpočtům by mělo dojít v enclaves, aby se zajistila ochrana tajných kódů a citlivých dat. 

Během návrhu aplikace je důležité identifikovat a určit, jaká část aplikace musí běžet v enclaves. Kód, který se rozhodnete vložit do důvěryhodné součásti, je izolovaný od zbytku vaší aplikace. Jakmile je enklávy inicializován a kód je načten do paměti, tento kód nelze číst nebo změnit z nedůvěryhodných komponent. 

### <a name="open-enclave-software-development-kit-oe-sdk"></a>Otevřete enklávy Software Development Kit (OE SDK).<a id="oe-sdk"></a>

Použijte knihovnu nebo architekturu podporovanou vaším poskytovatelem, pokud chcete napsat kód, který běží v enklávy. [Open ENKLÁVY SDK](https://github.com/openenclave/openenclave) (OE SDK) je open source sada SDK, která umožňuje abstrakci nad jiným důvěrným hardwarem, který je povolený. 

Sada OE SDK je sestavena tak, aby byla jednou z abstrakcních vrstev na jakémkoli poskytovateli CSP. Sada OE SDK se dá využít na virtuální počítače s důvěrnými výpočetními prostředími Azure k vytváření a spouštění aplikací nad enclaves.

## <a name="next-steps"></a>Další kroky

Nasaďte virtuální počítač s DCsv2-Series a nainstalujte na něj sadu OE SDK.

> [!div class="nextstepaction"]
> [Nasazení virtuálního počítače s důvěrným výpočetním prostředím v Azure Marketplace](quick-create-marketplace.md)
