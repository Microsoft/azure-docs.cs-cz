---
title: Důvěrné výpočetní uzly ve službě Azure Kubernetes (AKS)
description: Důvěrné výpočetní uzly na AKS
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 2/08/2021
ms.author: amgowda
ms.openlocfilehash: 203185d9f6def2204906b8722f1969b14eee8787
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105933147"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service"></a>Důvěrné výpočetní uzly ve službě Azure Kubernetes

[Důvěrné výpočetní prostředí Azure](overview.md) umožňuje chránit citlivá data, když se používají. Základní tajná cloudová infrastruktura chrání tato data od jiných aplikací, správců a poskytovatelů cloudu pomocí zálohovaných kontejnerů důvěryhodných spuštění s využitím hardwaru. Přidání důvěrných výpočetních uzlů vám umožní cílit aplikaci kontejneru, aby běžela v izolovaném prostředí, chráněném hardwarem a s ověřováním identity.

## <a name="overview"></a>Přehled

Služba Azure Kubernetes Service (AKS) podporuje přidávání [DCsv2 důvěrných výpočetních uzlů](confidential-computing-enclaves.md) využívajících technologii Intel SGX. Tyto uzly umožňují spouštět citlivé úlohy v rámci důvěryhodného spuštění prostředí (TEE) založeného na hardwaru. TEE umožňuje kódu na úrovni uživatele z kontejnerů přidělit privátní oblasti paměti, aby se kód spustil přímo s PROCESORem. Tyto oblasti privátních paměti, které se spouštějí přímo s PROCESORem, se nazývají enclaves. Enclaves chrání důvěrnost dat, integritu dat a integritu kódu z jiných procesů spuštěných na stejných uzlech. Model spuštění Intel SGX také odebere mezilehlé vrstvy hostovaného operačního systému, hostitelského operačního systému a hypervisoru a tím snižuje prostor pro útoky. *Hardware založený na modelu spuštění izolovaného kontejneru* v uzlu umožňuje aplikacím přímým spouštění s procesorem a přitom zachovat speciální blok paměti zašifrovaný na kontejner. Důvěrné výpočetní uzly s důvěrnými kontejnery jsou skvělým doplňkem k vašemu plánování zabezpečení s nulovou důvěryhodností a k důkladné strategii kontejneru.

![Přehled uzlu SGX](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>Funkce AKSch důvěrných uzlů

- Izolace kontejneru na úrovni hardwaru a procesu prostřednictvím prostředí TEE (Trusted Execution Environment) Intel SGX 
- Clustery fondu uzlů heterogenní (kombinace důvěrných a nedůvěrných fondů uzlů)
- Naplánovaná paměťová mezipaměť (EPC) na základě paměti (vyžaduje doplněk)
- Předinstalované ovladače Intel SGX DCAP
- Automatické škálování a automatické škálování v závislosti na PROCESORech na bázi horizontálního škálování pod sebou
- Podpora kontejnerů pro Linux prostřednictvím pracovních uzlů virtuálních počítačů Ubuntu 18,04 Gen 2

## <a name="confidential-computing-add-on-for-aks"></a>Doplněk důvěrné výpočetní služby pro AKS
Funkce doplňku povoluje při spouštění fondů důvěrných výpočetních uzlů v clusteru navíc možnost AKS. Tento doplněk umožňuje následující funkce.

#### <a name="azure-device-plugin-for-intel-sgx"></a>Modul plug-in zařízení Azure pro Intel SGX <a id="sgx-plugin"></a>

Modul plug-in zařízení implementuje rozhraní modulu plug-in zařízení Kubernetes pro paměť služby EPC (Encrypted Page cache) a zpřístupňuje ovladače zařízení z uzlů. Tento modul plug-in v Kubernetes zpřístupňuje paměť EPC jako jiný typ prostředku. Uživatelé můžou u tohoto prostředku zadat omezení stejně jako na jiné prostředky. Kromě funkce plánování pomáhá modul plug-in zařízení přiřadit oprávnění ovladače zařízení Intel SGX k důvěrným kontejnerům úloh. Tento vývojář modulů plug-in se může vyhnout připojení svazků ovladačů Intel SGX v souborech nasazení. Ukázková implementace ukázky nasazení na bázi paměti EPC `kubernetes.azure.com/sgx_epc_mem_in_MiB` je [tady](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml) .


## <a name="programming-models"></a>Programovací modely

### <a name="confidential-containers"></a>Důvěrné kontejnery

[Důvěrné kontejnery](confidential-containers.md) vám pomůžou spouštět stávající neupravené aplikace kontejneru nejběžnějšími moduly runtime **programovacích jazyků** (Python, Node, Java atd.) v tajnosti. Tento model balení nepotřebuje žádné úpravy zdrojového kódu ani rekompilaci. Toto je nejrychlejší způsob, jakým se dá zajistit důvěrnost pomocí balíčků standardních kontejnerů Docker s Open-Source projekty nebo partnerskými řešeními Azure. V tomto balení a prováděcím modelu jsou všechny části aplikace kontejneru načteny do důvěryhodné hranice (enklávy). Tento model funguje dobře pro vypnuté aplikace kontejneru pro polici dostupné na trhu nebo ve vlastních aplikacích, které aktuálně běží na obecných uzlech.

### <a name="enclave-aware-containers"></a>Kontejnery s podporou enklávy
Důvěrné výpočetní uzly v AKS také podporují kontejnery, které jsou naprogramované pro běh v enklávy pro využití **speciální sady instrukcí** dostupné z procesoru. Tento programovací model umožňuje úzkou kontrolu nad tokem spouštění a vyžaduje použití speciálních sad SDK a platforem. Tento programovací model poskytuje většinu řízení toku aplikace s nejnižším důvěryhodným výpočetním základem (TCB). Enklávy, který podporuje vývoj kontejnerů, zahrnuje nedůvěryhodné a důvěryhodné součásti pro kontejnerové aplikace, takže vám umožní spravovat běžná paměť a paměť v mezipaměti šifrované stránky (EPC), kde je spuštěný enklávy. [Přečtěte si další](enclave-aware-containers.md) informace o kontejnerech s podporou enklávy.

## <a name="next-steps"></a>Další kroky

[Nasazení clusteru AKS s důvěrnými výpočetními uzly](./confidential-nodes-aks-get-started.md)

[Stručné ukázky kontejneru důvěrného Starter](https://github.com/Azure-Samples/confidential-container-samples)

[Seznam SKU DCsv2](../virtual-machines/dcv2-series.md)

[Důkladná ochrana díky webinář relaci důvěrných kontejnerů](https://www.youtube.com/watch?reload=9&v=FYZxtHI_Or0&feature=youtu.be)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
