---
title: Podpora ověřování mimo proc pomocí Daemonset pomocníka Intel SGX pro uvozovky v Azure (Preview)
description: DaemonSet pro generování uvozovek mimo proces aplikace SGX. Tento článek vysvětluje, jak se rovided zařízení pro ověřování mimo proc pro důvěrné úlohy běžící uvnitř kontejneru.
ms.service: container-service
ms.subservice: confidential-computing
author: agowdamsft
ms.topic: overview
ms.date: 2/12/2021
ms.author: amgowda
ms.openlocfilehash: 0ebeb96557b7e20d123577c0ab9c8fc392abbfba
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105932621"
---
# <a name="platform-software-management-with-sgx-quote-helper-daemon-set-preview"></a>Správa softwaru platforem pomocí rutiny pomocníka SGX citace (Preview)

[Enklávy aplikace](confidential-computing-enclaves.md) , které provádějí vzdálené ověření identity, vyžadují VYgenerovanou uvozovky. Tato nabídka poskytuje kryptografické ověření identity a stavu aplikace a také prostředí, ve kterém je spuštěný enklávy. Generace této nabídky vyžaduje důvěryhodné softwarové komponenty, které jsou součástí komponent Intel Platform Software Component Components (PSW).

## <a name="overview"></a>Přehled
 
Intel podporuje pro spuštění generování nabídky dva režimy ověření identity:
- **in-proc**: hostuje důvěryhodné softwarové komponenty uvnitř procesu aplikace enklávy.

- **mimo proc**: hostuje důvěryhodné softwarové komponenty mimo aplikaci enklávy.
 
SGX aplikace sestavené pomocí sady Open enklávy SDK ve výchozím nastavení používají režim ověření identity v režimu proc. Aplikace založené na SGX umožňují použití mimo proc a vyžadují další hostování a vystavování požadovaných komponent, jako je například architektura enklávy Service Manager (AESM), které jsou pro aplikaci externí.

Použití této funkce se **důrazně doporučuje**, protože vylepšuje dobu provozu aplikací enklávy během aktualizací platforem Intel nebo aktualizací ovladačů DCAP.

Pokud chcete tuto funkci povolit v clusteru AKS, při povolování doplňku pro důvěrné výpočty prosím změňte příkaz Add--Enable-sgxquotehelper na CLI. Podrobné pokyny pro rozhraní příkazového řádku [najdete tady](confidential-nodes-aks-get-started.md): 

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled and SGX Quote Helper
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom --enable-sgxquotehelper
```

## <a name="why-and-what-are-the-benefits-of-out-of-proc"></a>Proč a jaké jsou výhody mimo proc?

-   Pro součásti pro generování uvozovek PSW pro každou aplikaci s podporou procesu nejsou vyžadovány žádné aktualizace: s out-of-proc vlastníci kontejnerů nepotřebují spravovat aktualizace v kontejneru. Vlastníci kontejnerů místo toho spoléhají na rozhraní poskytnuté poskytovatelem, které vyvolá centralizovanou službu mimo kontejner, který bude aktualizován a spravován zprostředkovatelem.

-   Nemusíte se starat o selhání ověřování z důvodu zastaralých součástí PSW: generace nabídek zahrnuje důvěryhodné Enklávyy SW (QE &), které jsou součástí standardu TCB (Trusted Computing). Aby bylo možné zachovat požadavky na ověření identity, je třeba, aby tyto SW komponenty byly aktuální. Vzhledem k tomu, že zprostředkovatel spravuje aktualizace těchto součástí, zákazníci se nikdy nebudou muset zabývat chybami ověřování z důvodu neaktuálního důvěryhodného SW v kontejneru.

-   Díky lepšímu využití paměti EPC v režimu ověření identity v rámci proc musí každá aplikace enklávy vytvořit instanci QE a editoru pro vzdálené ověření identity. V případě mimoprocesové není nutné, aby kontejner mohl hostovat tyto enclaves, a proto nespotřebovává enklávy paměť z kvóty kontejneru.

-   Ochrana proti vynucení jádra, když je ovladač SGX v jádru v jádře, vynucujeme, že enklávy bude mít vyšší oprávnění. Toto oprávnění umožňuje enklávy vyvolat editor, což způsobí přerušení aplikace enklávy běžící v režimu in-proc. Ve výchozím nastavení enclaves toto oprávnění nezíská. Udělení tohoto oprávnění aplikaci enklávy vyžaduje změny v procesu instalace aplikace. To je snadné pro model mimo proc, protože zprostředkovatel služby, který zpracovává požadavky na zpracování mimo proces, zajistí, že se tato služba nainstaluje s tímto oprávněním.

-   Není nutné kontrolovat zpětnou kompatibilitu s PSW & DCAP. Aktualizace komponent pro generování uvozovek v PSW jsou před aktualizací ověřeny pro zpětnou kompatibilitu zprostředkovatelem. To vám pomůže napřed nasadit problémy s kompatibilitou a vyřešit je před nasazením aktualizací pro důvěrné úlohy.

## <a name="how-does-the-out-of-proc-attestation-mode-work-for-confidential-workloads-scenario"></a>Jak funguje režim ověřování mimo proc pro scénář s důvěrnými úlohami?

Návrh na nejvyšší úrovni se řídí modelem, ve kterém se spouští žadatel nabídky a generování poptávky samostatně, ale na stejném fyzickém počítači. Generace nabídky se provede centralizovaným způsobem a obsluhuje požadavky na nabídky ze všech entit. Rozhraní musí být správně definováno a zjistitelné pro libovolnou entitu pro vyžádání nabídek.

![SGX pomocníka pro citace aesm](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

Výše uvedený abstraktní model se vztahuje na scénář důvěrné úlohy s využitím již dostupné služby AESM. AESM je kontejnerový a nasazený jako daemonSet v rámci clusteru Kubernetes. Kubernetes garantuje jednu instanci kontejneru služby AESM, zabalenou v části pod, která se má nasadit na každý uzel agenta. Nové daemonset nabídky SGX budou mít závislost na SGX-Device-plugin daemonset, protože kontejner služby AESM by vyžadoval paměť EPC od SGX-Device-plug-in pro spouštění QE a sady doplňků enclaves.

Každý kontejner se musí odhlásit, aby bylo možné použít vygenerování citace z provozu, nastavením proměnné prostředí **SGX_AESM_ADDR = 1** během vytváření. Kontejner by měl obsahovat také balíček libsgx-quote-ex, který zodpovídá za přesměrování požadavku na výchozí soket domény systému UNIX.

Aplikace může stále používat ověření identity in-proc jako předtím, ale v rámci aplikace nelze současně použít procedury in-proc i out-of-proc. Infrastruktura mimo proc je ve výchozím nastavení dostupná a spotřebovává prostředky.

## <a name="sample-implementation"></a>Ukázková implementace

Níže uvedený soubor Docker je ukázka pro otevřenou aplikaci založenou na enklávy. V souboru Docker nastavte proměnnou prostředí SGX_AESM_ADDR = 1 nebo ji nastavte v souboru nasazení. Použijte následující ukázku pro soubor Docker a YAML Deployment details. 

  > [!Note] 
  > **Libsgx-quot-ex** od společnosti Intel musí být zabalena do kontejneru aplikace, aby bylo možné správně fungovat ověřování mimo proc.
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for detail
FROM ubuntu:18.04 as sgx_base
RUN apt-get update && apt-get install -y \
    wget \
    gnupg

# Add the repository to sources, and add the key to the list of
# trusted keys used by the apt to authenticate packages
RUN echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" | tee /etc/apt/sources.list.d/intel-sgx.list \
    && wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -
# Add Microsoft repo for az-dcap-client
RUN echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | tee /etc/apt/sources.list.d/msprod.list \
    && wget -qO - https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

FROM sgx_base as sgx_sample
RUN apt-get update && apt-get install -y \
    clang-7 \
    libssl-dev \
    gdb \
    libprotobuf10 \
    libsgx-dcap-ql \
    libsgx-quote-ex \
    az-dcap-client \
    open-enclave
WORKDIR /opt/openenclave/share/openenclave/samples/remote_attestation
RUN . /opt/openenclave/share/openenclave/openenclaverc \
    && make build
# this sets the flag for out of proc attestation mode. alternatively you can set this flag on the deployment files
ENV SGX_AESM_ADDR=1 

CMD make run
```
Případně můžete nastavit režim ověřování mimo proc v souboru YAML nasazení, jak je znázorněno níže.

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
spec:
  template:
    spec:
      containers:
      - name: sgxtest
        image: <registry>/<repository>:<version>
        env:
        - name: SGX_AESM_ADDR
          value: 1
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10
        volumeMounts:
        - name: var-run-aesmd
          mountPath: /var/run/aesmd
      restartPolicy: "Never"
      volumes:
      - name: var-run-aesmd
        hostPath:
          path: /var/run/aesmd
```

## <a name="next-steps"></a>Další kroky
[Zřizování důvěrných uzlů (DCsv2-Series) na AKS](./confidential-nodes-aks-get-started.md)

[Rychlé úvodní ukázky – důvěrné kontejnery](https://github.com/Azure-Samples/confidential-container-samples)

[Seznam SKU DCsv2](../virtual-machines/dcv2-series.md)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
