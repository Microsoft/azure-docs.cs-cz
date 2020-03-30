---
title: Spuštění privilegovaných kontejnerů v clusteru Azure Red Hat OpenShift | Dokumenty společnosti Microsoft
description: Spouštět privilegované kontejnery pro sledování zabezpečení a dodržování předpisů.
author: makdaam
ms.author: b-lejaku
ms.service: container-service
ms.topic: conceptual
ms.date: 12/05/2019
keywords: aro, openshift, aquasec, twistlock, červený klobouk
ms.openlocfilehash: e1c1dd9f27a207f78dd22e271f6b070c7f92f622
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271366"
---
# <a name="run-privileged-containers-in-an-azure-red-hat-openshift-cluster"></a>Spouštění privilegovaných kontejnerů v clusteru Azure Red Hat OpenShift

V clusterech Azure Red Hat OpenShift nelze spouštět libovolné privilegované kontejnery.
Dvě řešení monitorování zabezpečení a dodržování předpisů mohou být spuštěna v clusterech ARO.
Tento dokument popisuje rozdíly od obecné dokumentace nasazení OpenShift dodavatelů bezpečnostních produktů.


Než se podle pokynů dodavatele pořídíte, přečtěte si tyto pokyny.
Názvy oddílů v následujících krocích pro konkrétní produkty odkazují přímo na názvy oddílů v dokumentaci dodavatelů.

## <a name="before-you-begin"></a>Než začnete

Dokumentace většiny produktů zabezpečení předpokládá, že máte oprávnění správce clusteru.
Správci zákazníků nemají všechna oprávnění v Azure Red Hat OpenShift. Oprávnění potřebná k úpravě prostředků celého clusteru jsou omezená.

Nejprve se ujistěte, že je uživatel přihlášen ke `oc get scc`clusteru jako správce zákazníka, spuštěním aplikace . Všichni uživatelé, kteří jsou členy skupiny správců zákazníků, mají oprávnění k zobrazení omezení kontextu zabezpečení (SCC) v clusteru.

Dále se ujistěte, že `oc` binární verze je `3.11.154`.
```
oc version
oc v3.11.154
kubernetes v1.11.0+d4cacc0
features: Basic-Auth GSSAPI Kerberos SPNEGO

Server https://openshift.aqua-test.osadev.cloud:443
openshift v3.11.154
kubernetes v1.11.0+d4cacc0
```

## <a name="product-specific-steps-for-aqua-security"></a>Kroky specifické pro produkt pro zabezpečení aqua
Základní pokyny, které budou upraveny, naleznete v [dokumentaci k nasazení aqua zabezpečení](https://docs.aquasec.com/docs/openshift-red-hat). Kroky zde budou spuštěny ve spojení s dokumentací nasazení Aqua.

Prvním krokem je onicovat požadované řadiče domény, které budou aktualizovány. Tyto poznámky brání synchronizačnímu podu clusteru vrátit všechny změny těchto snopů.

```
oc annotate scc hostaccess openshift.io/reconcile-protect=true
oc annotate scc privileged openshift.io/reconcile-protect=true
```

### <a name="step-1-prepare-prerequisites"></a>Krok 1: Příprava požadavků
Nezapomeňte se přihlásit do clusteru jako správce zákazníka ARO namísto role správce clusteru.

Vytvořte projekt a účet služby.
```
oc new-project aqua-security
oc create serviceaccount aqua-account -n aqua-security
```

Místo přiřazení role čtečky clusteru přiřaďte roli clusteru zákazník-admin-cluster k účtu aqua pomocí následujícího příkazu.
```
oc adm policy add-cluster-role-to-user customer-admin-cluster system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user privileged system:serviceaccount:aqua-security:aqua-account
oc adm policy add-scc-to-user hostaccess system:serviceaccount:aqua-security:aqua-account
```

Pokračujte podle zbývajících pokynů v kroku 1.  Tyto pokyny popisují nastavení tajného klíče pro registr Aqua.

### <a name="step-2-deploy-the-aqua-server-database-and-gateway"></a>Krok 2: Nasazení aquaserveru, databáze a brány
Při instalaci souboru aqua-console.yaml postupujte podle pokynů uvedených v dokumentaci společnosti Aqua.

Upravte zadaný `aqua-console.yaml`.  Odstraňte dva horní objekty označené `kind: ClusterRole` a `kind: ClusterRoleBinding`.  Tyto prostředky nebudou vytvořeny, protože správce zákazníka nemá v tuto `ClusterRole` `ClusterRoleBinding` chvíli oprávnění k úpravám a objektům.

Druhá změna bude na `kind: Route` část `aqua-console.yaml`. Nahraďte následující yaml `kind: Route` pro `aqua-console.yaml` objekt v souboru.
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

Postupujte podle zbývajících pokynů.

### <a name="step-3-login-to-the-aqua-server"></a>Krok 3: Přihlášení k aqua serveru
Tato část není žádným způsobem změněna.  Postupujte podle dokumentace aqua.

Pomocí následujícího příkazu získáte adresu aquakonzole.
```
oc get route aqua-web -n aqua-security
```

### <a name="step-4-deploy-aqua-enforcers"></a>Krok 4: Nasazení aqua enforcerů
Při nasazování vymahačů nastavte následující pole:

| Pole          | Hodnota         |
| -------------- | ------------- |
| Orchestrator   | OpenShift     |
| Účet služby | aqua-účet  |
| Project        | aqua-security |

## <a name="product-specific-steps-for-prisma-cloud--twistlock"></a>Kroky specifické pro prisma cloud / Twistlock

Základní pokyny, které budeme upravovat, najdete v [dokumentaci k nasazení Prisma Cloud.](https://docs.paloaltonetworks.com/prisma/prisma-cloud/19-11/prisma-cloud-compute-edition-admin/install/install_openshift.html)

Začněte instalací `twistcli` nástroje, jak je popsáno v části "Instalace prisma cloudu" a "Stáhněte si software Prisma Cloud".

Vytvoření nového projektu OpenShift
```
oc new-project twistlock
```

Přeskočte volitelnou sekci "Posuňte image Prisma Cloud do soukromého registru". Na Azure Red Hat Openshift to nebude fungovat. Místo toho použijte online registr.

Při použití níže popsaných oprav se můžete řídit oficiální dokumentací.
Začněte s částí "Instalovat konzolu".

### <a name="install-console"></a>Nainstalovat konzolu

Během `oc create -f twistlock_console.yaml` kroku 2 se při vytváření oboru názvů zobrazí chyba.
Můžete jej bez obav ignorovat, obor názvů `oc new-project` byl vytvořen dříve pomocí příkazu.

Používá `azure-disk` se pro typ úložiště.

### <a name="create-an-external-route-to-console"></a>Vytvoření externí trasy do konzoly

Můžete buď postupovat podle dokumentace, nebo pokyny níže, pokud dáváte přednost příkazu oc.
Zkopírujte následující definici trasy do souboru s názvem twistlock_route.yaml v počítači
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
pak spustit:
```
oc create -f twistlock_route.yaml
```

Pomocí tohoto příkazu můžete získat adresu URL přiřazenou konzoli Twistlock:`oc get route twistlock-console -n twistlock`

### <a name="configure-console"></a>Konfigurace konzoly

Postupujte podle dokumentace Twistlock.

### <a name="install-defender"></a>Instalace programu Defender

Během `oc create -f defender.yaml` kroku 2 se při vytváření role clusteru a vazby role clusteru zobrazí chyby.
Můžete je ignorovat.

Obránci budou nasazeni pouze na výpočetních uzlech. Není nutné je omezovat pomocí voliče uzlů.
