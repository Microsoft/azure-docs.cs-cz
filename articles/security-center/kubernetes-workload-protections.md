---
title: Ochrana úloh pro úlohy Kubernetes
description: Přečtěte si, jak Azure Security Center používat Kubernetes sadu doporučení zabezpečení ochrany úloh.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/16/2021
ms.author: memildin
ms.openlocfilehash: f024457ed9f20e6bab0852a53ab6a60ac85ed25a
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100546257"
---
# <a name="protect-your-kubernetes-workloads"></a>Ochrana úloh Kubernetes

Tato stránka popisuje, jak používat sadu Azure Security Centerch doporučení zabezpečení vyhrazených pro ochranu úloh Kubernetes.

Další informace o těchto funkcích najdete v článku [osvědčené postupy ochrany úloh pomocí řízení přístupu Kubernetes](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control) .

Pokud povolíte Azure Defender, Security Center nabízí více funkcí zabezpečení kontejnerů. Konkrétně se jedná o tyto:

- Vyhledá v registrech kontejnerů chyby zabezpečení pomocí [Azure Defenderu pro Registry kontejnerů](defender-for-container-registries-introduction.md) .
- Získání výstrah detekce hrozeb v reálném čase pro clustery K8s [Azure Defender pro Kubernetes](defender-for-kubernetes-introduction.md)

> [!TIP]
> Seznam *všech* doporučení týkajících se zabezpečení, která se můžou zobrazit u clusterů a uzlů Kubernetes, najdete v [části COMPUTE](recommendations-reference.md#recs-compute) v referenční tabulce doporučení.



## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Obecná dostupnost (GA)|
|Stanov|Free|
|Požadované role a oprávnění:|**Vlastník** nebo **Správce zabezpečení** pro úpravu přiřazení<br>**Čtenář** pro zobrazení doporučení|
|Požadavky prostředí:|Kubernetes v 1.14 (nebo vyšší) je povinný údaj.<br>Žádný prostředek PodSecurityPolicy (starý model PSP) v clusterech<br>Uzly Windows nejsou podporované.|
|Cloud|![Ano](./media/icons/yes-icon.png) Komerční cloudy<br>![Ano](./media/icons/yes-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||


## <a name="set-up-your-workload-protection"></a>Nastavení ochrany úloh

Azure Security Center zahrnuje sadu doporučení, která jsou k dispozici po instalaci **Azure Policy doplňku pro Kubernetes**.

### <a name="step-1-deploy-the-add-on"></a>Krok 1: nasazení doplňku

Pokud chcete nakonfigurovat doporučení, nainstalujte  **doplněk Azure Policy pro Kubernetes**. 

- Tento doplněk můžete automaticky nasadit, jak je vysvětleno v tématu [Povolení automatického zřizování rozšíření](security-center-enable-data-collection.md#enable-auto-provisioning-of-extensions). Pokud je Automatické zřizování pro doplněk nastavené na zapnuto, rozšíření je ve výchozím nastavení povolené ve všech stávajících i budoucích clusterech (které splňují požadavky na instalaci doplňku).

    :::image type="content" source="media/defender-for-kubernetes-usage/policy-add-on-auto-provision.png" alt-text="Použití nástroje pro Automatické zřizování Security Center k instalaci doplňku zásad pro Kubernetes":::

- Postup ručního nasazení doplňku:

    1. Na stránce doporučení vyhledejte doporučení "**Azure Policy doplněk pro Kubernetes by měl být nainstalovaný a povolený ve vašich clusterech**". 

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes.png" alt-text="Doporučení * * Azure Policy doplněk pro Kubernetes byste měli nainstalovat a povolit ve svých clusterech * *":::

        > [!TIP]
        > Doporučení je součástí pěti různých ovládacích prvků zabezpečení a nezáleží na tom, který z nich vybíráte v dalším kroku.

    1. V jakémkoli z ovládacích prvků zabezpečení vyberte doporučení, abyste viděli prostředky, na které můžete doplněk nainstalovat.
    1. Vyberte příslušný cluster a **opravte** ho.

        :::image type="content" source="./media/defender-for-kubernetes-usage/recommendation-to-install-policy-add-on-for-kubernetes-details.png" alt-text="Stránka s podrobnostmi doporučení pro * * Azure Policy doplňku pro Kubernetes by měla být na vašich clusterech nainstalována a povolena * *":::

### <a name="step-2-view-and-configure-the-bundle-of-13-recommendations"></a>Krok 2: zobrazení a konfigurace sady 13 doporučení

1. Přibližně 30 minut po dokončení instalace doplňku Security Center zobrazí stav clusterů v následujících doporučeních, v příslušném ovládacím prvku zabezpečení, jak je znázorněno níže:

    > [!TIP]
    > Některá doporučení mají parametry, které je potřeba přizpůsobit pomocí Azure Policy, aby je bylo možné efektivně použít. Chcete-li například využít výhod imagí kontejnerů s doporučeními, které **by měly být nasazeny pouze z důvěryhodných registrů**, budete muset definovat důvěryhodné Registry.
    > 
    > Pokud nezadáte potřebné parametry pro doporučení, které vyžadují konfiguraci, budou se vaše úlohy zobrazovat v pořádku.

    | Název doporučení                                                         | Řízení zabezpečení                         | Vyžaduje se konfigurace |
    |-----------------------------------------------------------------------------|------------------------------------------|------------------------|
    | Měla by se vyhovět omezení procesoru a paměti kontejneru.                          | Ochrana aplikací před útoky DDoS | Ne                     |
    | Měly by se vyhnout privilegovanému kontejneru                                     | Správa přístupu a oprávnění            | Ne                     |
    | Neměnné (jen pro čtení) kořenový systém souborů by měl být vynutil pro kontejnery.     | Správa přístupu a oprávnění            | Ne                     |
    | Je třeba zabránit kontejneru s eskalací oprávnění.                       | Správa přístupu a oprávnění            | Ne                     |
    | Spuštění kontejnerů jako kořenový uživatel by se mělo vyhnout.                           | Správa přístupu a oprávnění            | Ne                     |
    | Kontejnery sdílející závislé obory názvů hostitele by se měly vyhnout              | Správa přístupu a oprávnění            | Ne                     |
    | Pro kontejnery by se měly vyhovět aspoň privilegované možnosti pro Linux.       | Správa přístupu a oprávnění            | **Ano**                |
    | Použití ovládacího připojení svazku pod HostPath by mělo být omezené na známý seznam.    | Správa přístupu a oprávnění            | **Ano**                |
    | Kontejnery by měly naslouchat jenom povoleným portům.                              | Omezit neautorizovaný přístup k síti     | **Ano**                |
    | Služby by měly naslouchat jenom povoleným portům.                                | Omezit neautorizovaný přístup k síti     | **Ano**                |
    | Používání hostitelských sítí a portů by se mělo omezit.                     | Omezit neautorizovaný přístup k síti     | **Ano**                |
    | Přepsání nebo zakázání profilu kontejnerů AppArmor by mělo být omezené. | Náprava konfigurací zabezpečení        | **Ano**                |
    | Image kontejneru by se měly nasadit jenom z důvěryhodných registrů.            | Napravit ohrožení zabezpečení                | **Ano**                |
    |||


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

        :::image type="content" source="./media/defender-for-kubernetes-usage/enforce-workload-protection-example.png" alt-text="Možnost Deny pro parametr Azure Policy":::

        Otevře se podokno, ve kterém jste obor nastavili. 

    1. Po nastavení oboru vyberte **změnit na odepřít**.

1. Pokud chcete zjistit, která doporučení se vztahují na vaše clustery:

    1. Otevřete stránku [inventáře assetů](asset-inventory.md) Security Center a použijte filtr typ prostředku k **Kubernetes služeb**.

    1. Vyberte cluster, který chcete prozkoumat, a zkontrolujte dostupná doporučení, která jsou k dispozici. 

1. Při zobrazení doporučení ze sady ochrany zatížení se zobrazí počet ovlivněných lusků ("součásti Kubernetes"), které jsou uvedeny vedle clusteru. Pokud chcete zobrazit seznam jednotlivých lusků, vyberte cluster a pak vyberte **provést akci**.

    :::image type="content" source="./media/defender-for-kubernetes-usage/view-affected-pods-for-recommendation.gif" alt-text="Zobrazení postiženého lusku pro doporučení K8s"::: 

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

- [Security Center doporučení pro výpočetní prostředky](recommendations-reference.md#recs-compute)
- [Výstrahy na úrovni clusteru AKS](alerts-reference.md#alerts-akscluster)
- [Výstrahy na úrovni hostitele kontejneru](alerts-reference.md#alerts-containerhost)