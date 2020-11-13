---
title: Kontejnery s podporou enklávy v Azure
description: Podpora kontejnerů aplikací připravených pro enklávy ve službě Azure Kubernetes (AKS)
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: cdd886bfcfb02aa46e440d2574b241f9b1cf3134
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564136"
---
# <a name="enclave-aware-containers"></a>Kontejnery s podporou enklávy

Enklávy je oblast chráněné paměti, která poskytuje důvěrnost pro data a provádění kódu. Jedná se o instanci důvěryhodného prováděcího prostředí (TEE), která je zabezpečená hardwarem. Důvěrné výpočetní uzly v AKS využívají [rozšíření Intel software Guard (SGX)](https://software.intel.com/sgx) k vytváření izolovaných prostředí enklávy v uzlech mezi jednotlivými aplikacemi kontejneru.

Stejně jako u virtuálních počítačů Intel SGX mají kontejnery aplikace, které jsou vyvíjené ke spouštění v enclaves, dvě komponenty:

- Nedůvěryhodná součást (označovaná jako hostitel) a
- Důvěryhodná součást (nazývaná enklávy).

![Architektura kontejneru s podporou enklávy](./media/enclave-aware-containers/enclaveawarecontainer.png)

Aplikační architektura kontejnerů s podporou enklávy poskytuje největší kontrolu nad implementací a přitom udržuje nároky na kód v enklávy nízké úrovni. Minimalizace kódu, který běží ve enklávy, pomáhá snižovat oblasti útoku.   

## <a name="enablers"></a>Předpokladů

### <a name="open-enclave-sdk"></a>Open Enclave SDK
Open enklávy SDK je hardwarová knihovna Open Source nezávislá pro vývoj aplikací v jazyce C, C++, které využívají prostředí pro důvěryhodné spouštění založené na hardwaru. Aktuální implementace poskytuje podporu pro Intel SGX a podporu verze Preview pro [operační systém op-Tee na ARM TrustZone](https://optee.readthedocs.io/en/latest/general/about.html).

Začínáme s Open enklávy kontejnerem na bázi [here](https://github.com/openenclave/openenclave/tree/master/docs/GettingStartedDocs)

### <a name="intel-sgx-sdk"></a>Sada SGX SDK Intel
Intel udržuje sadu Software Development Kit pro vytváření SGXch aplikací pro úlohy pro Linux i Windows Container. Kontejnery Windows nejsou aktuálně podporované AKS důvěrnými výpočetními uzly.

Začněte používat aplikace založené na technologii Intel SGX [zde](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/sdk.html)

### <a name="confidential-consortium-framework-ccf"></a>Důvěrné rozhraní Consortium (CCF)
Důvěrné konsorcium (CCF) je open-source platforma pro vytváření nové kategorie zabezpečených, vysoce dostupných a výkonných aplikací, které se zaměřují na výpočetní výkon a data více stran. CCF může umožňovat vysoce škálovatelné důvěrné sítě, které splňují klíčové požadavky na podnik, a poskytuje tak prostředky pro urychlení produkčního a podnikového přijímání služeb založených na konsorciu blockchain a výpočetní technologie pro více stran.

Začněte s důvěrnými výpočetními výpočty Azure a CCF [tady](https://github.com/Microsoft/CCF)

### <a name="confidential-inferencing-onnx-runtime"></a>Důvěrný odvozovací modul runtime ONNX

Open source modul ONNX runtime založený na enklávy vytvoří zabezpečený kanál mezi klientem a odvozenou službou – zajišťuje, že ani požadavek ani odpověď nemohou opustit zabezpečený enklávy. 

Toto řešení vám umožňuje přenášet existující model vyškolený v ML a provádět tajně a současně poskytovat důvěru mezi klientem a serverem prostřednictvím ověření identity a ověření. 

Začínáme s přezvednutím modelu ML a přesunem do ONNX runtime [tady](https://aka.ms/confidentialinference)

### <a name="edgeless-rt"></a>Bezokrajový RT

Bezokrajový RT je open source projekt, který se sestaví na Open enklávy SDK. Přidává podporu pro funkci přejít a další funkce jazyka C++. V [tomto](https://github.com/edgelesssys/edgelessrt)článku se seznámíte s jednoduchou aplikací s důvěrnými informacemi, které používají vaše známé vs Code prostředí. Pro bezAKSné aplikace na postupujte [podle pokynů.](https://github.com/edgelesssys/edgelessrt/blob/master/docs/ERTAzureAKSDeployment.md)


## <a name="container-based-sample-implementations"></a>Ukázková implementace založená na kontejneru

[Ukázky Azure pro kontejnery s podporou enklávy na AKS](https://github.com/Azure-Samples/confidential-computing/tree/main/containersamples)

<!-- LINKS - external -->
[Azure Attestation](../attestation/overview.md)


<!-- LINKS - internal -->
[Virtuální počítač DC](./virtual-machine-solutions.md) 
 [Důvěrné kontejnery](./confidential-containers.md)