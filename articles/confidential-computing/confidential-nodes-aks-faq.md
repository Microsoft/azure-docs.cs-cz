---
title: Nejčastější dotazy týkající se podpory důvěrných uzlů ve službě Azure Kubernetes (AKS)
description: Vyhledejte odpovědi na některé běžné dotazy týkající se služby Azure Kubernetes Service (AKS) & podpora uzlů pro důvěrný výpočetní výkon (ACC) Azure.
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: amgowda
ms.openlocfilehash: b4b6c04a1e35031387a006b6350ebf6cbe502fee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90995885"
---
# <a name="frequently-asked-questions-about-confidential-computing-nodes-on-azure-kubernetes-service-aks"></a>Nejčastější dotazy týkající se důvěrných výpočetních uzlů ve službě Azure Kubernetes (AKS)

Tento článek popisuje časté otázky týkající se důvěrných výpočetních uzlů na bázi Intel SGX ve službě Azure Kubernetes (AKS). Pokud máte nějaké další dotazy, pošlete e-mail acconaks@microsoft.com .

## <a name="what-service-level-agreement-sla-and-azure-support-is-provided-during-the-preview"></a>Jaká smlouva SLA (SLA) a podpora Azure jsou k dispozici ve verzi Preview? 

Smlouva SLA není k dispozici ve verzi Preview produktu, jak je [zde](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)definována. Podpora produktů je ale poskytována prostřednictvím podpory Azure.

## <a name="what-is-attestation-and-how-can-we-do-attestation-of-apps-running-in-enclaves"></a>Co je ověření identity a jak můžeme ověřit platnost aplikací běžících v enclaves? 

Ověření identity je proces demonstrace a ověření, že se na konkrétní hardwarovou platformu správně vytvořila součást softwaru. Zajišťuje také, že je možné prokázat jejich důkaz, aby bylo zajištěno, že je spuštěn na zabezpečené platformě a nebylo manipulováno s. [Přečtěte si další](attestation.md) informace o tom, jak se ověřování provádí pro aplikace enklávy.

## <a name="can-i-bring-my-existing-containerized-applications-and-run-it-on-aks-with-azure-confidential-computing"></a>Můžu přenášet své stávající aplikace do kontejnerů a spouštět je v AKS s důvěrným výpočetním prostředím Azure? 

Ano, podívejte se na [stránku důvěrné kontejnery](confidential-containers.md) , kde najdete další podrobnosti o enablerssch platforem.

## <a name="what-intel-sgx-driver-version-is-installed-in-the-aks-image"></a>Jaká verze ovladače Intel SGX je nainstalovaná v imagi AKS? 

V současné době jsou virtuální počítače Azure s důvěrnými výpočetními DCSv2 nainstalovány s Intel SGX DCAP 1,33. 

## <a name="can-i-open-an-azure-support-ticket-if-i-run-into-issues"></a>Můžu otevřít lístek podpory Azure, když narazím na problémy? 

Ano. Podpora Azure je poskytována ve verzi Preview. Není připojená žádná smlouva SLA, protože produkt je ve fázi Preview.

## <a name="can-i-inject-post-install-scriptscustomize-drivers-to-the-nodes-provisioned-by-aks"></a>Můžu vložit skripty po instalaci/přizpůsobit ovladače uzlům zřízeným pomocí AKS? 

Ne. [Důvěrné výpočetní uzly založené na modulu AKS](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) podporují důvěrné výpočetní uzly, které umožňují vlastní instalace.

## <a name="should-i-be-using-a-docker-base-image-to-get-started-on-enclave-applications"></a>Mám použít základní image Docker, abyste mohli začít s enklávy aplikacemi? 

Různé EnableRSS (projekty ISV a OSS) poskytují možnosti pro povolení důvěrných kontejnerů. Další podrobnosti a jednotlivé odkazy na implementace najdete na [stránce důvěrné kontejnery](confidential-containers.md) .

## <a name="can-i-run-acc-nodes-with-other-standard-aks-skus-build-a-heterogenous-node-pool-cluster"></a>Můžu spustit ACC uzly s jinými standardními SKU AKS (sestavení clusteru fondu uzlů heterogenní)? 

Ano, můžete spouštět různé fondy uzlů v rámci stejného clusteru AKS, včetně uzlů s účtem. Pokud chcete své aplikace enklávy cílit na konkrétní fond uzlů, zvažte přidání selektorů uzlů nebo aplikování omezení EPC. Další podrobnosti najdete v části rychlý Start na tajných uzlech [zde](confidential-nodes-aks-get-started.md).

## <a name="can-i-run-windows-nodes-and-windows-containers-with-acc"></a>Můžu s účtem spustit uzly Windows a kontejnery Windows? 

V tuto chvíli to není možné. Pokud máte uzly nebo kontejnery Windows, kontaktujte nás. 

## <a name="what-if-my-container-size-is-more-than-available-epc-memory"></a>Co když je velikost kontejneru větší než dostupná paměť EPC? 

Paměť EPC se vztahuje na část vaší aplikace, která je naprogramována pro spuštění v enklávy. Celková velikost kontejneru není správného způsobu, jak ho porovnat s maximální dostupnou pamětí EPC. Ve skutečnosti DCSv2 počítače s SGX umožňují maximální velikost paměti virtuálního počítače 32 GB, kde by vaše nedůvěryhodná část aplikace využila. Pokud však váš kontejner spotřebovává více než dostupnou paměť EPC, může to mít vliv na výkon části programu běžícího v enklávy.

Chcete-li lépe spravovat paměť EPC v pracovních uzlech, zvažte správu omezení založené na paměti EPC prostřednictvím Kubernetes. Použijte následující příklad jako referenci.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
```

## <a name="what-happens-if-my-enclave-consumes-more-than-maximum-available-epc-memory"></a>Co se stane, když můj enklávy spotřebovává více než maximální dostupnou paměť EPC? 

Celkově dostupná paměť EPC je sdílená mezi enklávy aplikacemi ve stejných virtuálních počítačích nebo pracovních uzlech. Pokud vaše aplikace používá paměť EPC více než k dispozici, může to mít vliv na výkon aplikace. Z tohoto důvodu doporučujeme nastavit tolerovat na aplikaci v souboru YAML nasazení tak, aby lépe spravovala dostupnou paměť EPC na jeden pracovní uzel, jak je znázorněno v předchozích příkladech. Případně můžete kdykoli přejít na velikost virtuálního počítače fondu pracovních uzlů nebo přidat další uzly. 

## <a name="why-cant-i-do-forks--and-exec-to-run-multiple-processes-in-my-enclave-application"></a>Proč nemůžu rozvětvení () a exec spustit více procesů v mé aplikaci enklávy? 

Virtuální počítače Azure důvěrné výpočetní služby DCsv2 v současné době podporují jeden adresní prostor pro program, který je spuštěný v enklávy. Jediným procesem je aktuální omezení navržené kolem vysokého zabezpečení. Důvěrné přízpůsoby kontejneru ale můžou mít alternativní implementace pro překonání tohoto omezení.

## <a name="do-you-automatically-install-any-additional-daemonsets-to-expose-the-sgx-drivers"></a>Nainstalujete automaticky další daemonsets a zpřístupníte tak ovladače SGX? 

Ano. Název daemonset je SGX-Device-plug-in a SGX-quot-Helper. [Tady si můžete](confidential-nodes-aks-overview.md)přečíst další informace o příslušných účelech.  

## <a name="what-is-the-vm-sku-i-should-be-choosing-for-confidential-computing-nodes"></a>Jakou SKLADOVOU položku virtuálního počítače mám zvolit pro důvěrné výpočetní uzly? 

DCSv2 SKU. [SKU DCSv2](../virtual-machines/dcv2-series.md) jsou k dispozici v [podporovaných oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all).

## <a name="can-i-still-schedule-and-run-non-enclave-containers-on-confidential-computing-nodes"></a>Můžu v enklávych uzlech s důvěrným výpočetním prostředím naplánovat a spustit kontejnery jiné než? 

Ano. Virtuální počítače mají taky běžnou paměť, která může spouštět standardní úlohy kontejneru. Před tím, než se rozhodnete o modelech nasazení, zvažte model zabezpečení a hrozeb vašich aplikací.

## <a name="can-i-provision-aks-with-dcsv2-node-pools-through-azure-portal"></a>Můžu prostřednictvím Azure Portal zřídit AKS pomocí fondů uzlů DCSv2? 

Ano. Rozhraní příkazového řádku Azure můžete také použít jako alternativu popsanou [tady](confidential-nodes-aks-get-started.md).

## <a name="what-ubuntu-version-and-vm-generation-is-supported"></a>Jakou verzi Ubuntu a generaci virtuálních počítačů se podporují? 

18,04 na Gen 2. 

## <a name="can-we-change-the-current-intel-sgx-dcap-diver-version-on-aks"></a>Můžeme změnit aktuální verzi Intel SGX DCAP Diver na AKS? 

Ne. Abyste mohli provádět jakékoli vlastní instalace, doporučujeme, abyste vybrali nasazení [AKSch pracovních uzlů s důvěrným výpočetním modulem](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) . 

## <a name="what-version-of-kubernetes-do-you-support-and-recommend"></a>Jakou verzi Kubernetes podporujete a doporučujete? 

Podporujeme a doporučujeme verzi Kubernetes 1,16 a vyšší. 

## <a name="what-are-the-known-current-limitation-or-technical-limitations-of-the-product-in-preview"></a>Jaké jsou známá aktuální omezení nebo technická omezení produktu ve verzi Preview? 

- Podporuje jenom uzly virtuálních počítačů s Ubuntu 18,04.2. generace 
- Podpora uzlů Windows ani podpora kontejnerů Windows
- Automatické škálování založené na paměti EPC je ve vodorovném měřítku podporováno. Podporuje se procesor a běžné škálování na bázi paměti.
- Vývojové prostory v AKS pro důvěrné aplikace se momentálně nepodporují.

## <a name="next-steps"></a>Další kroky
Další podrobnosti o důvěrných kontejnerech najdete na [stránce důvěrné kontejnery](confidential-containers.md) .