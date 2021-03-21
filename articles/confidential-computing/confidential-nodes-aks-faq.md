---
title: Nejčastější dotazy týkající se podpory důvěrných uzlů ve službě Azure Kubernetes (AKS)
description: Vyhledejte odpovědi na některé běžné dotazy týkající se služby Azure Kubernetes Service (AKS) & podpora uzlů pro důvěrný výpočetní výkon (ACC) Azure.
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 02/09/2020
ms.author: amgowda
ms.openlocfilehash: 550995f0be3d634e7e9f24a8bf6826916003308e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653393"
---
# <a name="frequently-asked-questions-about-confidential-computing-nodes-on-azure-kubernetes-service-aks"></a>Nejčastější dotazy týkající se důvěrných výpočetních uzlů ve službě Azure Kubernetes (AKS)

Tento článek popisuje časté otázky týkající se důvěrných výpočetních uzlů na bázi Intel SGX ve službě Azure Kubernetes (AKS). Pokud máte nějaké další dotazy, pošlete e-mail **acconaks@microsoft.com** .

<a name="1"></a>
### <a name="are-the-confidential-computing-nodes-on-aks-in-ga"></a>Jsou důvěrné výpočetní uzly na AKS v GA? ###
Yes

<a name="2"></a>
### <a name="what-is-attestation-and-how-can-we-do-attestation-of-apps-running-in-enclaves"></a>Co je ověření identity a jak můžeme ověřit platnost aplikací běžících v enclaves? ###
Ověření identity je proces demonstrace a ověření, že se na konkrétní hardwarovou platformu správně vytvořila součást softwaru. Zajišťuje také, že je možné prokázat jejich důkaz, aby bylo zajištěno, že je spuštěn na zabezpečené platformě a nebylo manipulováno s. [Přečtěte si další](attestation.md) informace o tom, jak se ověřování provádí pro aplikace enklávy.

<a name="3"></a>
### <a name="can-i-enable-accelerated-networking-with-azure-confidential-computing-aks-clusters"></a>Můžu povolit akcelerovanou síť s clustery s důvěrnými výpočetními AKS Azure? ###
No. Akcelerované síťové služby se u virtuálních počítačů s DCSv2, které strukturu důvěrné výpočetní uzly na AKS, nepodporují. 

<a name="4"></a>
### <a name="can-i-bring-my-existing-containerized-applications-and-run-it-on-aks-with-azure-confidential-computing"></a>Můžu přenášet své stávající aplikace do kontejnerů a spouštět je v AKS s důvěrným výpočetním prostředím Azure? ###
Ano, podívejte se na [stránku důvěrné kontejnery](confidential-containers.md) , kde najdete další podrobnosti o enablerssch platforem.

<a name="5"></a>
### <a name="what-version-of-intel-sgx-driver-version-is-on-the-aks-image-for-confidential-nodes"></a>Jakou verzi ovladače Intel SGX je na obrázku AKS pro důvěrné uzly? ### 
V současné době jsou virtuální počítače Azure s důvěrnými výpočetními DCSv2 nainstalovány s Intel SGX DCAP 1,33. 

<a name="6"></a>
### <a name="can-i-inject-post-install-scriptscustomize-drivers-to-the-nodes-provisioned-by-aks"></a>Můžu vložit skripty po instalaci/přizpůsobit ovladače uzlům zřízeným pomocí AKS? ###
No. [Důvěrné výpočetní uzly založené na modulu AKS](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) podporují důvěrné výpočetní uzly, které umožňují vlastní instalace a mají plnou kontrolu nad rovinou ovládacího prvku Kubernetes.
<a name="7"></a>

### <a name="should-i-be-using-a-docker-base-image-to-get-started-on-enclave-applications"></a>Mám použít základní image Docker, abyste mohli začít s enklávy aplikacemi? ###
Různé EnableRSS (projekty ISV a OSS) poskytují možnosti pro povolení důvěrných kontejnerů. Další podrobnosti a jednotlivé odkazy na implementace najdete na [stránce důvěrné kontejnery](confidential-containers.md) .

<a name="8"></a>
### <a name="can-i-run-acc-nodes-with-other-standard-aks-skus-build-a-heterogenous-node-pool-cluster"></a>Můžu spustit ACC uzly s jinými standardními SKU AKS (sestavení clusteru fondu uzlů heterogenní)? ###

Ano, můžete spouštět různé fondy uzlů v rámci stejného clusteru AKS, včetně uzlů s účtem. Pokud chcete své aplikace enklávy cílit na konkrétní fond uzlů, zvažte přidání selektorů uzlů nebo aplikování omezení EPC. Další podrobnosti najdete v části rychlý Start na tajných uzlech [zde](confidential-nodes-aks-get-started.md).

<a name="9"></a>
### <a name="can-i-run-windows-nodes-and-windows-containers-with-acc"></a>Můžu s účtem spustit uzly Windows a kontejnery Windows? ###
V tuto chvíli to není možné. Kontaktujte produktovou tým v *acconaks@microsoft.com* případě, že máte uzly nebo kontejnery Windows. 

<a name="10"></a>
### <a name="what-if-my-container-size-is-more-than-available-epc-memory"></a>Co když je velikost kontejneru větší než dostupná paměť EPC? ###
Paměť EPC se vztahuje na část vaší aplikace, která je naprogramována pro spuštění v enklávy. Celková velikost kontejneru není správného způsobu, jak ho porovnat s maximální dostupnou pamětí EPC. Ve skutečnosti DCSv2 počítače s SGX umožňují maximální velikost paměti virtuálního počítače 32 GB, kde by vaše nedůvěryhodná část aplikace využila. Pokud však váš kontejner spotřebovává více než dostupnou paměť EPC, může to mít vliv na výkon části programu běžícího v enklávy.

Chcete-li lépe spravovat paměť EPC v pracovních uzlech, zvažte správu omezení založené na paměti EPC prostřednictvím Kubernetes. Postupujte podle následujícího příkladu jako reference.

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
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10 # This limit will automatically place the job into confidential computing node. Alternatively, you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
```
<a name="11"></a>
### <a name="what-happens-if-my-enclave-consumes-more-than-maximum-available-epc-memory"></a>Co se stane, když můj enklávy spotřebovává více než maximální dostupnou paměť EPC? ###

Celkově dostupná paměť EPC je sdílená mezi enklávy aplikacemi ve stejných virtuálních počítačích nebo pracovních uzlech. Pokud vaše aplikace používá paměť EPC více než k dispozici, může to mít vliv na výkon aplikace. Z tohoto důvodu doporučujeme nastavit tolerovat na aplikaci v souboru YAML nasazení tak, aby lépe spravovala dostupnou paměť EPC na jeden pracovní uzel, jak je znázorněno v předchozích příkladech. Případně můžete kdykoli přejít na velikost virtuálního počítače fondu pracovních uzlů nebo přidat další uzly. 

<a name="12"></a>
### <a name="why-cant-i-do-forks--and-exec-to-run-multiple-processes-in-my-enclave-application"></a>Proč nemůžu rozvětvení () a exec spustit více procesů v mé aplikaci enklávy? ###

Virtuální počítače Azure důvěrné výpočetní služby DCsv2 v současné době podporují jeden adresní prostor pro program, který je spuštěný v enklávy. Jediným procesem je aktuální omezení navržené kolem vysokého zabezpečení. Důvěrné přízpůsoby kontejneru ale můžou mít alternativní implementace pro překonání tohoto omezení.
<a name="13"></a>
### <a name="do-you-automatically-install-any-additional-daemonset-to-expose-the-sgx-drivers"></a>Nainstalujete automaticky další daemonset a zpřístupníte tak ovladače SGX? ###

Ano. Název daemonset je SGX-Device-plug-in. [Tady si můžete](confidential-nodes-aks-overview.md)přečíst další informace o příslušných účelech.  

<a name="14"></a>
### <a name="what-is-the-vm-sku-i-should-be-choosing-for-confidential-computing-nodes"></a>Jakou SKLADOVOU položku virtuálního počítače mám zvolit pro důvěrné výpočetní uzly? ###

DCSv2 SKU. [SKU DCSv2](../virtual-machines/dcv2-series.md) jsou k dispozici v [podporovaných oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all).

<a name="15"></a>
### <a name="can-i-still-schedule-and-run-non-enclave-containers-on-confidential-computing-nodes"></a>Můžu v enklávych uzlech s důvěrným výpočetním prostředím naplánovat a spustit kontejnery jiné než? ###

Ano. Virtuální počítače mají taky běžnou paměť, která může spouštět standardní úlohy kontejneru. Před tím, než se rozhodnete o modelech nasazení, zvažte model zabezpečení a hrozeb vašich aplikací.
<a name="16"></a>

### <a name="can-i-provision-aks-with-dcsv2-node-pools-through-azure-portal"></a>Můžu prostřednictvím Azure Portal zřídit AKS pomocí fondů uzlů DCSv2? ###

Ano. Rozhraní příkazového řádku Azure můžete také použít jako alternativu popsanou [tady](confidential-nodes-aks-get-started.md).

<a name="17"></a>
### <a name="what-ubuntu-version-and-vm-generation-is-supported"></a>Jakou verzi Ubuntu a generaci virtuálních počítačů se podporují? ###
18,04 na Gen 2. 

<a name="18"></a>
### <a name="can-we-change-the-current-intel-sgx-dcap-diver-version-on-aks"></a>Můžeme změnit aktuální verzi Intel SGX DCAP Diver na AKS? ###

No. Abyste mohli provádět jakékoli vlastní instalace, doporučujeme, abyste vybrali nasazení [AKSch pracovních uzlů s důvěrným výpočetním modulem](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) . 

<a name="19"></a>

### <a name="what-version-of-kubernetes-do-you-support-and-recommend"></a>Jakou verzi Kubernetes podporujete a doporučujete? ###

Podporujeme a doporučujeme verzi Kubernetes 1,16 a novější. 

<a name="20"></a>
### <a name="what-are-the-known-current-limitations-of-the-product"></a>Jaká jsou známá aktuální omezení produktu? ###

- Podporuje jenom uzly virtuálních počítačů s Ubuntu 18,04.2. generace 
- Podpora uzlů Windows ani podpora kontejnerů Windows
- Automatické škálování založené na paměti EPC je ve vodorovném měřítku podporováno. Podporuje se procesor a běžné škálování na bázi paměti.
- Vývojové prostory v AKS pro důvěrné aplikace se momentálně nepodporují.

<a name="21"></a>
### <a name="will-only-signed-and-trusted-images-be-loaded-in-the-enclave-for-confidential-computing"></a>Budou se v enklávy pro důvěrné výpočetní služby načítat jenom podepsané a důvěryhodné image? ###
Při inicializaci enklávy se nedá nativně napravit, ale dá se ověřit podpis procesu ověření identity. Odkaz [sem](../attestation/basic-concepts.md#benefits-of-policy-signing). 

### <a name="next-steps"></a>Další kroky
Další podrobnosti o důvěrných kontejnerech najdete na [stránce důvěrné kontejnery](confidential-containers.md) .
