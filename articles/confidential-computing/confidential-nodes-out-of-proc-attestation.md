---
title: Podpora ověřování identity pomocí DaemonSet pomocníka Intel SGX v Azure (Preview)
description: DaemonSet pro generování uvozovek mimo proces aplikace Intel SGX. Tento článek vysvětluje, jak je k dispozici zařízení pro ověření identity mimo procesy pro důvěrné úlohy, které běží uvnitř kontejneru.
ms.service: container-service
ms.subservice: confidential-computing
author: agowdamsft
ms.topic: overview
ms.date: 2/12/2021
ms.author: amgowda
ms.openlocfilehash: 849fd7afa3f9365f31ee8e03d9f9cc2174d64304
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484400"
---
# <a name="platform-software-management-with-intel-sgx-quote-helper-daemonset-preview"></a>Správa softwaru platforem pomocí SGX pomocníka Intel DaemonSet (Preview)

[Enklávy aplikace](confidential-computing-enclaves.md) , které provádějí vzdálené ověření identity, vyžadují vygenerovanou uvozovky. Tato nabídka poskytuje kryptografický důkaz identity a stavu aplikace a také prostředí, ve kterém je spuštěný enklávy. Generace této nabídky vyžaduje důvěryhodné softwarové komponenty, které jsou součástí komponent Intel Platform Software Components (PSW).

## <a name="overview"></a>Přehled
 
Intel podporuje pro spuštění generování nabídky dva režimy ověření identity:

- *V procesu* je hostitelem důvěryhodných softwarových součástí v procesu aplikace enklávy.

- *Mimo proces* hostuje důvěryhodné softwarové komponenty mimo aplikaci enklávy.
 
Aplikace rozšíření Intel software Guard (Intel SGX) sestavené pomocí sady SDK Open enklávy ve výchozím nastavení používají režim ověřování v rámci procesu. Aplikace založené na Intel SGX umožňují režim ověřování mimo proces. Pokud chcete použít tento režim, potřebujete další hostování a potřebujete vystavit požadované součásti, jako je například architektura enklávy Service Manager (AESM), které jsou pro aplikaci externí.

Tato funkce vylepšuje dobu provozu aplikací enklávy během aktualizací platforem Intel nebo aktualizací ovladačů DCAP. Z tohoto důvodu ji doporučujeme použít.

Pokud chcete povolit tuto funkci v clusteru Azure Kubernetes Services (AKS), přidejte `--enable-sgxquotehelper` příkaz do Azure CLI, když povolíte doplněk důvěrného zpracování. 

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled and SGX Quote Helper
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom --enable-sgxquotehelper
```

Další informace najdete v tématu [rychlý Start: nasazení clusteru AKS s důvěrnými výpočetními uzly pomocí Azure CLI](confidential-nodes-aks-get-started.md).

## <a name="benefits-of-the-out-of-process-mode"></a>Výhody režimu mimo proces

Následující seznam popisuje některé z hlavních výhod tohoto režimu ověřování:

-   Pro součásti pro generování uvozovek PSW pro každou aplikaci v kontejneru nejsou vyžadovány žádné aktualizace. Vlastníci kontejneru nepotřebují spravovat aktualizace v kontejneru. Vlastníci kontejnerů místo toho spoléhají na rozhraní poskytovatele, které vyvolá centralizovanou službu mimo kontejner. Poskytovatel aktualizuje a spravuje kontejner.

-   Kvůli zastaralým součástem PSW nemusíte dělat starosti s chybami ověřování. Zprostředkovatel spravuje aktualizace těchto součástí.

-   Režim mimo procesy poskytuje lepší využití paměti EPC než v režimu v procesu. V režimu v procesu musí každá aplikace enklávy vytvářet instance kopie QE a editoru pro vzdálené ověření identity. V režimu mimo procesy není nutné, aby kontejner mohl hostovat tyto enclaves, a proto nespotřebovává enklávy paměť z kvóty kontejneru.

-   Když zahájíte odesílání ovladače Intel SGX do jádra systému Linux, je vynuceno, že enklávy má vyšší oprávnění. Toto oprávnění umožňuje enklávy vyvolat editor, což způsobí přerušení aplikace enklávy běžící v režimu v procesu. Ve výchozím nastavení enclaves toto oprávnění nezíská. Udělení tohoto oprávnění aplikaci enklávy vyžaduje změny v procesu instalace aplikace. Naopak v režimu out-of-Process poskytovatel služby, která zpracovává požadavky mimo proces, zajišťuje, že je služba nainstalována s tímto oprávněním.

-   Nemusíte kontrolovat zpětnou kompatibilitu s PSW a DCAP. Aktualizace komponent pro generování uvozovek v PSW jsou před aktualizací ověřeny pro zpětnou kompatibilitu zprostředkovatelem. To vám pomůže zvládnout problémy s kompatibilitou ještě před nasazením aktualizací pro důvěrné úlohy.

## <a name="confidential-workloads"></a>Důvěrné úlohy

Generování žadatele na citát a citát se spouští samostatně, ale na stejném fyzickém počítači. Generování uvozovek je centralizované a obsluhuje požadavky na nabídky ze všech entit. Aby každá entita požadovala uvozovky, musí být rozhraní správně definované a zjistitelné.

![Diagram znázorňující vztahy mezi žadatelem nabídky, generováním poptávky a rozhraním.](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

Tento abstraktní model se vztahuje na scénář důvěrné úlohy tím, že využívá službu AESM, která je již k dispozici. AESM je kontejnerový a nasazený jako DaemonSet v rámci clusteru Kubernetes. Kubernetes garantuje jednu instanci kontejneru služby AESM, zabalenou v části pod, která se má nasadit na každý uzel agenta. Nové DaemonSet nabídky Intel SGX budou mít závislost na SGX-Device-plugin DaemonSet, protože kontejner služby AESM požaduje paměť EPC z modulu plug-in zařízení SGX pro spouštění QE a sady EDITORů enclaves.

Každý kontejner se musí přihlásit k použití mimoprocesové generace pro zpracování uvozovek nastavením proměnné prostředí `SGX_AESM_ADDR=1` během vytváření. Kontejner by měl zahrnovat také balíček libsgx-quot-ex, který je zodpovědný za přesměrování požadavku na výchozí soket domény systému UNIX.

Aplikace může stále používat ověřování v rámci procesu jako předtím, ale v rámci aplikace se v rámci procesu nepoužívá souběžné a mimoprocesové procesy. Neprocesní infrastruktura je ve výchozím nastavení dostupná a spotřebovává prostředky.

## <a name="sample-implementation"></a>Ukázková implementace

Následující soubor Docker je ukázkou aplikace založené na Open enklávy. Nastavte `SGX_AESM_ADDR=1` proměnnou prostředí v souboru Docker nebo ji nastavte v souboru nasazení. Následující ukázka poskytuje podrobnosti pro soubor Docker a nasazení. 

  > [!Note] 
  > Aby ověření identity mimo proces fungovalo správně, musí být libsgx-quot-ex od společnosti Intel zabaleny do kontejneru aplikace.
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for details
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
# This sets the flag for out-of-process attestation mode. Alternatively you can set this flag on the deployment files.
ENV SGX_AESM_ADDR=1 

CMD make run
```
Případně můžete nastavit režim ověřování mimo procesy v souboru Deployment. yaml. Jak na to:

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

[Rychlý Start: nasazení clusteru AKS s důvěrnými výpočetními uzly pomocí Azure CLI](./confidential-nodes-aks-get-started.md)

[Rychlé úvodní ukázky – důvěrné kontejnery](https://github.com/Azure-Samples/confidential-container-samples)

[DCsv2 SKU](../virtual-machines/dcv2-series.md)
