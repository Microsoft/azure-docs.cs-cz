---
title: Ochrana úloh pro úlohy Kubernetes
description: Přečtěte si, jak Azure Security Center používat Kubernetes sadu doporučení zabezpečení ochrany úloh.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 09/12/2020
ms.author: memildin
ms.openlocfilehash: 8a387adde6c74b8eb1ff950c5e6b5183e43d1f4f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448668"
---
# <a name="protect-your-kubernetes-workloads"></a>Ochrana úloh Kubernetes

Tato stránka popisuje, jak používat sadu Azure Security Centerch doporučení zabezpečení vyhrazených pro ochranu úloh Kubernetes.

Další informace o těchto funkcích najdete v článku [osvědčené postupy ochrany úloh pomocí řízení přístupu Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control) .

Pokud povolíte Azure Defender, Security Center nabízí více funkcí zabezpečení kontejnerů. Konkrétně se jedná o tyto:

- Vyhledá v registrech kontejnerů chyby zabezpečení pomocí [Azure Defenderu pro Registry kontejnerů](defender-for-container-registries-introduction.md) .
- Získání výstrah detekce hrozeb v reálném čase pro clustery K8s [Azure Defender pro Kubernetes](defender-for-kubernetes-introduction.md)

> [!TIP]
> Seznam *všech* doporučení týkajících se zabezpečení, která se můžou zobrazit u clusterů a uzlů Kubernetes, najdete v [části Container](recommendations-reference.md#recs-containers) referenční tabulky doporučení.



## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Preview|
|Stanov|Free|
|Požadované role a oprávnění:|**Vlastník** nebo **Správce zabezpečení** pro úpravu přiřazení<br>**Čtenář** pro zobrazení doporučení|
|Podporované clustery|Kubernetes v 1.14 (nebo vyšší) je povinný údaj.<br>Žádný prostředek PodSecurityPolicy (starý model PSP) v clusterech<br>Uzly Windows nejsou podporované.|
|Cloud|![Yes](./media/icons/yes-icon.png) Komerční cloudy<br>![No](./media/icons/no-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||


## <a name="set-up-your-workload-protection"></a>Nastavení ochrany úloh

Azure Security Center zahrnuje sadu doporučení, která jsou k dispozici po instalaci **Azure Policy doplňku pro Kubernetes**.

1. Pokud chcete nakonfigurovat doporučení, musíte nejdřív nainstalovat doplněk na:

    1. Na stránce doporučení vyhledejte doporučení s názvem **Azure Policy doplněk pro Kubernetes by měl být nainstalovaný a povolený v clusterech**.

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes.png" alt-text="Doporučení * * Azure Policy doplněk pro Kubernetes byste měli nainstalovat a povolit ve svých clusterech * *":::

        > [!TIP]
        > Doporučení je součástí pěti různých ovládacích prvků zabezpečení a nezáleží na tom, který z nich vybíráte v dalším kroku.

    1. V jakémkoli z ovládacích prvků zabezpečení vyberte doporučení, abyste viděli prostředky, na které můžete nainstalovat doplněk, a pak vyberte **opravit**. 

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes-details.png" alt-text="Doporučení * * Azure Policy doplněk pro Kubernetes byste měli nainstalovat a povolit ve svých clusterech * *":::

1. Přibližně 30 minut po dokončení instalace doplňku Security Center zobrazí stav clusterů v následujících doporučeních, v příslušném ovládacím prvku zabezpečení, jak je znázorněno níže:

    > [!TIP]
    > Některá doporučení mají parametry, které je potřeba přizpůsobit pomocí Azure Policy, aby je bylo možné efektivně použít. Chcete-li například využít výhod imagí kontejnerů s doporučeními, které **by měly být nasazeny pouze z důvěryhodných registrů**, budete muset definovat důvěryhodné Registry.
    > 
    > Pokud nezadáte potřebné parametry pro doporučení, které vyžadují konfiguraci, budou se vaše úlohy zobrazovat v pořádku.

    | Název doporučení                                                                   | Řízení zabezpečení                         | Vyžaduje se konfigurace |
    |---------------------------------------------------------------------------------------|------------------------------------------|------------------------|
    | Musí se vymáhat omezení procesoru a paměti kontejneru (Preview).                          | Ochrana aplikací před útoky DDoS | No                     |
    | Měly by se vyhnout privilegovanému kontejneru (Preview).                                     | Správa přístupu a oprávnění            | No                     |
    | Neměnné (jen pro čtení) pro kontejnery (Preview) by se mělo vyhovět kořenovým systémem souborů.     | Správa přístupu a oprávnění            | No                     |
    | Je potřeba zabránit kontejneru s eskalací oprávnění (Preview).                       | Správa přístupu a oprávnění            | No                     |
    | Spouštění kontejnerů jako kořenový uživatel by se mělo vyhnout (Preview)                           | Správa přístupu a oprávnění            | No                     |
    | Kontejnery sdílející obory názvů závislé na hostitelích by se měly vyhnout (Preview)              | Správa přístupu a oprávnění            | No                     |
    | Pro kontejnery (Preview) by se měly vyhovět aspoň privilegované možnosti systému Linux.       | Správa přístupu a oprávnění            | **Ano**                |
    | Použití ovládacího připojení svazku pod HostPath by se mělo omezit na známý seznam (Preview).    | Správa přístupu a oprávnění            | **Ano**                |
    | Kontejnery by měly naslouchat jenom povoleným portům (Preview)                              | Omezit neautorizovaný přístup k síti     | **Ano**                |
    | Služby by měly naslouchat jenom povoleným portům (Preview).                                | Omezit neautorizovaný přístup k síti     | **Ano**                |
    | Používání hostitelských sítí a portů by mělo být omezené (Preview)                     | Omezit neautorizovaný přístup k síti     | **Ano**                |
    | Přepsání nebo zákaz kontejneru AppArmor Profile by mělo být omezeno (Preview) | Náprava konfigurací zabezpečení        | **Ano**                |
    | Image kontejneru by se měly nasadit jenom z důvěryhodných registrů (Preview).            | Napravit ohrožení zabezpečení                | **Ano**                |


1. Doporučení s parametry se musí přizpůsobit nastavením parametrů:

    1. V nabídce Security Center vyberte **zásady zabezpečení**.
    1. Vyberte příslušné předplatné.
    1. V části **Security Center výchozí zásady** vyberte **Zobrazit efektivní zásady**.
    1. Vyberte "ASC výchozí".
    1. Otevřete kartu **parametry** a upravte hodnoty podle potřeby.
    1. Vyberte **zkontrolovat a uložit**.
    1. Vyberte **Uložit**.


1. K vykonání všech doporučení 

    1. Otevřete stránku s podrobnostmi o doporučení a vyberte **Odepřít**:

        :::image type="content" source="./media/defender-for-kubernetes-usage/enforce-workload-protection-example.png" alt-text="Doporučení * * Azure Policy doplněk pro Kubernetes byste měli nainstalovat a povolit ve svých clusterech * *":::

        Otevře se podokno, ve kterém jste obor nastavili. 

    1. Po nastavení oboru vyberte **změnit na odepřít**.

1. Pokud chcete zjistit, která doporučení se vztahují na vaše clustery:

    1. Otevřete stránku [inventáře assetů](asset-inventory.md) Security Center a použijte filtr typ prostředku k **Kubernetes služeb**.

    1. Vyberte cluster, který chcete prozkoumat, a zkontrolujte dostupná doporučení, která jsou k dispozici. 

1. Při zobrazení doporučení ze sady ochrany zatížení se zobrazí počet ovlivněných lusků ("součásti Kubernetes"), které jsou uvedeny vedle clusteru. Pokud chcete zobrazit seznam jednotlivých lusků, vyberte cluster a pak vyberte **provést akci**.

    :::image type="content" source="./media/defender-for-kubernetes-usage/view-affected-pods-for-recommendation.gif" alt-text="Doporučení * * Azure Policy doplněk pro Kubernetes byste měli nainstalovat a povolit ve svých clusterech * *"::: 

1. K otestování vynucování použijte níže uvedená dvě nasazení Kubernetes:

    - Jedno je pro nasazení v pořádku, které je v souladu se sadou doporučení pro ochranu úloh.
    - Druhá je pro špatné nasazení, které nedodržuje *žádné* z doporučení.

    Nasaďte příklady souborů. yaml tak, jak jsou, nebo je použijte jako referenci k napravení vlastního zatížení (krok VIII).  


## <a name="healthy-deployment-example-yaml-file"></a>Příklad nasazení v pořádku soubor. yaml

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-healthy-deployment
  labels:
    app: redis
spec:
  replicas: 3
  selector:
    matchLabels:
      app: redis
  template:
    metadata:
      labels:
        app: redis
      annotations:
        apparmor.security.beta.kubernetes.io/pod: runtime/default
        container.apparmor.security.beta.kubernetes.io/redis: runtime/default
    spec:
      containers:
      - name: redis
        image: healthyClusterRegistry.azurecr.io/redis:latest
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 100m
            memory: 250Mi
        securityContext:
          privileged: false
          readOnlyRootFilesystem: true
          allowPrivilegeEscalation: false
          runAsNonRoot: true
          runAsUser: 1000
---
apiVersion: v1
kind: Service
metadata:
  name: redis-healthy-service
spec:
  type: LoadBalancer
  selector:
    app: redis
  ports:
  - port: 80
    targetPort: 80
```

## <a name="unhealthy-deployment-example-yaml-file"></a>Ukázka nasazení, který není v pořádku. yaml soubor

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-unhealthy-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:      
      labels:
        app: nginx
    spec:
      hostNetwork: true
      hostPID: true 
      hostIPC: true
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 9001
          hostPort: 9001
        securityContext:
          privileged: true
          readOnlyRootFilesystem: false
          allowPrivilegeEscalation: true
          runAsUser: 0
          capabilities:
            add:
              - NET_ADMIN
        volumeMounts:
        - mountPath: /test-pd
          name: test-volume
          readOnly: true
      volumes:
      - name: test-volume
        hostPath:
          # directory location on host
          path: /tmp
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-unhealthy-service
spec:
  type: LoadBalancer
  selector:
    app: nginx
  ports:
  - port: 6001
    targetPort: 9001
```



## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak nakonfigurovat ochranu úloh Kubernetes. 

Další související materiály najdete na následujících stránkách: 

- [Security Center doporučení pro kontejnery](recommendations-reference.md#recs-containers)
- [Výstrahy na úrovni clusteru AKS](alerts-reference.md#alerts-akscluster)
- [Výstrahy na úrovni hostitele kontejneru](alerts-reference.md#alerts-containerhost)