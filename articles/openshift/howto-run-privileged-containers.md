---
title: Spouštění privilegovaných kontejnerů v clusteru Azure Red Hat OpenShift | Microsoft Docs
description: Spusťte privilegované kontejnery a sledujte zabezpečení a dodržování předpisů.
author: makdaam
ms.author: b-lejaku
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 12/05/2019
keywords: ARO, OpenShift, aquasec, TwistLock, Red Hat
ms.openlocfilehash: 77e75232b7f9d23f1d07931cc7dc231174e1312d
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100633558"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Spouštění privilegovaných kontejnerů v clusteru Azure Red Hat OpenShift

> [!IMPORTANT]
> Azure Red Hat OpenShift 3,11 bude vyřazeno 30. června 2022. Podpora pro vytváření nových clusterů Azure Red Hat OpenShift 3,11 pokračuje do 30. listopadu 2020. Po vyřazení z provozu budou zbývající clustery Azure Red Hat OpenShift 3,11 vypnuté, aby se předešlo chybám zabezpečení.
> 
> Podle tohoto průvodce [vytvořte cluster Azure Red Hat OpenShift 4](tutorial-create-cluster.md).
> Pokud máte konkrétní otázky, [kontaktujte nás prosím](mailto:arofeedback@microsoft.com).

V clusterech Azure Red Hat OpenShift nemůžete spouštět libovolné privilegované kontejnery.
Pro spouštění v clusterech v AROch je povoleno řešení dvou monitorování zabezpečení a dodržování předpisů.
Tento dokument popisuje rozdíly v dokumentaci k nasazení obecných OpenShift v rámci dodavatelů produktů zabezpečení.


Než budete postupovat podle pokynů dodavatele, přečtěte si tyto pokyny.
Nadpisy oddílů v následujících krocích pro konkrétní produkt odkazují přímo na nadpisy oddílů v dokumentaci pro dodavatele.

## <a name="before-you-begin"></a>Než začnete

Dokumentace k většině produktů zabezpečení předpokládá, že máte oprávnění správce clusteru.
Správci zákazníka nemají všechna oprávnění v Azure Red Hat OpenShift. Oprávnění požadovaná pro úpravu prostředků na úrovni clusteru jsou omezená.

Nejdřív zajistěte, aby byl uživatel přihlášený ke clusteru jako správce zákazníka, a to spuštěním `oc get scc` . Všichni uživatelé, kteří jsou členy skupiny správců zákazníka, mají oprávnění Zobrazit omezení kontextu zabezpečení (SCCs) v clusteru.

Dále se ujistěte, že `oc` binární verze je `3.11.154` .
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Kroky specifické pro produkt pro azurová zabezpečení
Základní pokyny, které se mají upravit, najdete v [dokumentaci k nasazení](https://docs.aquasec.com/docs/openshift-red-hat)v podobě azurová. Postup se spustí společně s dokumentací ke azurová nasazení.

Prvním krokem je opatřit poznámkami požadované SCCs, které se budou aktualizovat. Tyto poznámky zabraňují synchronizaci clusteru pod vrácením změn na těchto SSCs.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>Krok 1: Příprava požadavků
Nezapomeňte se přihlásit ke clusteru jako správce služby ARO a nikoli role Správce clusteru.

Vytvořte projekt a účet služby.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

Místo přiřazení role modulu pro čtení clusteru přiřaďte k azurová účtu roli Customer-admin-cluster pomocí následujícího příkazu.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Pokračujte podle zbývajících pokynů v kroku 1.  Tyto pokyny popisují nastavení tajného klíče pro azurová Registry.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>Krok 2: nasazení azurová serveru, databáze a brány
Postupujte podle kroků uvedených v části azurová dokumentace pro instalaci nástroje azurová-Console. yaml.

Upravte poskytnutý `aqua-console.yaml` .  Odeberte horní dva objekty s označením `kind: ClusterRole` a `kind: ClusterRoleBinding` .  Tyto prostředky se nevytvoří, protože správce zákazníka nemá v tuto chvíli oprávnění upravovat a vytvářet `ClusterRole` `ClusterRoleBinding` objekty.

Druhá změna bude v `kind: Route` části `aqua-console.yaml` . Pro objekt v souboru nahraďte následující YAML `kind: Route` `aqua-console.yaml` .
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    app: aqua-web
  name: aqua-web
  namespace: aqua-security
spec:
  port:
    targetPort: aqua-web
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: aqua-web
    weight: 100
  wildcardPolicy: None
```

Postupujte podle dalších pokynů.

### <a name="step-3-login-to-the-aqua-server"></a>Krok 3: přihlášení k serveru na azurová
Tato část se nijak nijak nemění.  Postupujte podle dokumentace ke azurová.

K získání adresy pro azurová konzolu použijte následující příkaz.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>Krok 4: nasaďte azurová vynucené
Při nasazování vynucení nastavte následující pole:

| Pole          | Hodnota         |
| -------------- | ------------- |
| Orchestrator   | OpenShift     |
| ServiceAccount | Azurová – účet  |
| Project        | Azurová – zabezpečení |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Kroky specifické pro produkt Prisma Cloud/TwistLock

Základní pokyny, které jsme změnili, najdete v [dokumentaci k nasazení cloudu Prisma](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html) .

Začněte tím, že nainstalujete `twistcli` nástroj, jak je popsáno v části "Install Prisma Cloud" a "stažení cloudového softwaru Prisma".

Vytvořit nový projekt OpenShift
```
oc new-project twistlock
```

Přeskočit volitelný oddíl "vložení cloudových imagí Prisma do privátního registru". Nebude fungovat na Azure Red Hat OpenShift. Místo toho použijte online Registry.

Při použití oprav popsaných níže můžete postupovat podle oficiální dokumentace.
Začněte s oddílem "Install Console".

### <a name="install-console"></a>Nainstalovat konzolu

Během `oc create -f twistlock_console.yaml` kroku 2 se při vytváření oboru názvů zobrazí chyba.
Můžete ji bezpečně ignorovat, obor názvů byl dříve vytvořen pomocí `oc new-project` příkazu.

Používá se `azure-disk` pro typ úložiště.

### <a name="create-an-external-route-to-console"></a>Vytvoření externí trasy pro konzolu

Pokud dáváte přednost příkazu oC, můžete postupovat podle dokumentace nebo podle pokynů níže.
Zkopírujte následující definici trasy do souboru s názvem twistlock_route. yaml ve vašem počítači.
```
apiVersion: route.openshift.io/v1
kind: Route
metadata:
  labels:
    name: console
  name: twistlock-console
  namespace: twistlock
spec:
  port:
    targetPort: mgmt-http
  tls:
    insecureEdgeTerminationPolicy: Redirect
    termination: edge
  to:
    kind: Service
    name: twistlock-console
    weight: 100
  wildcardPolicy: None
```
pak spusťte:
```
oc create -f twistlock_route.yaml
```

Adresu URL přiřazenou ke konzole TwistLock můžete získat pomocí tohoto příkazu: `oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Konfigurovat konzolu

Postup najdete v dokumentaci k TwistLock.

### <a name="install-defender"></a>Nainstalovat Defender

Během `oc create -f defender.yaml` kroku 2 se zobrazí chyby při vytváření vazby role clusteru a role clusteru.
Můžete je ignorovat.

Do výpočetních uzlů budou nasazené i ty. Nemusíte je omezovat pomocí voliče uzlů.
