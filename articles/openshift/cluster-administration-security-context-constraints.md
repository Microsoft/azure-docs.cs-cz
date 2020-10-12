---
title: Správa omezení kontextu zabezpečení v Azure Red Hat OpenShift | Microsoft Docs
description: Omezení kontextu zabezpečení pro správce clusteru Azure Red Hat OpenShift
services: container-service
author: troy0820
ms.author: b-trconn
ms.service: container-service
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 24163adcec889e9eedc2362ff1f01f00257a98f3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80063179"
---
# <a name="manage-security-context-constraints-in-azure-red-hat-openshift"></a>Správa omezení kontextu zabezpečení v Azure Red Hat OpenShift 

Omezení kontextu zabezpečení (SCCs) umožňují správcům clusterů řídit oprávnění pro lusky. Další informace o tomto typu rozhraní API najdete v [dokumentaci k architektuře pro SCCs](https://docs.openshift.com/container-platform/3.11/architecture/additional_concepts/authorization.html). SCCs v instanci můžete spravovat jako normální objekty rozhraní API pomocí rozhraní příkazového řádku (CLI).

## <a name="list-security-context-constraints"></a>Vypsat omezení kontextu zabezpečení

Pokud chcete získat aktuální seznam SCCs, použijte tento příkaz: 

```bash
$ oc get scc

NAME               PRIV      CAPS      SELINUX     RUNASUSER          FSGROUP     SUPGROUP    PRIORITY   READONLYROOTFS   VOLUMES
anyuid             false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    10         false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
hostaccess         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath persistentVolumeClaim secret]
hostmount-anyuid   false     []        MustRunAs   RunAsAny           RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir hostPath nfs persistentVolumeClaim secret]
hostnetwork        false     []        MustRunAs   MustRunAsRange     MustRunAs   MustRunAs   <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
nonroot            false     []        MustRunAs   MustRunAsNonRoot   RunAsAny    RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
privileged         true      [*]       RunAsAny    RunAsAny           RunAsAny    RunAsAny    <none>     false            [*]
restricted         false     []        MustRunAs   MustRunAsRange     MustRunAs   RunAsAny    <none>     false            [configMap downwardAPI emptyDir persistentVolumeClaim secret]
```

## <a name="examine-an-object-for-security-context-constraints"></a>Kontrola objektu pro omezení kontextu zabezpečení

Chcete-li prostudovat konkrétní SCC, použijte `oc get` , `oc describe` nebo `oc edit` .  Chcete-li například prostudovat **omezené** SCC, použijte tento příkaz:
```bash
$ oc describe scc restricted
Name:                    restricted
Priority:                <none>
Access:
  Users:                <none>
  Groups:                system:authenticated
Settings:
  Allow Privileged:            false
  Default Add Capabilities:        <none>
  Required Drop Capabilities:        KILL,MKNOD,SYS_CHROOT,SETUID,SETGID
  Allowed Capabilities:            <none>
  Allowed Seccomp Profiles:        <none>
  Allowed Volume Types:            configMap,downwardAPI,emptyDir,persistentVolumeClaim,projected,secret
  Allow Host Network:            false
  Allow Host Ports:            false
  Allow Host PID:            false
  Allow Host IPC:            false
  Read Only Root Filesystem:        false
  Run As User Strategy: MustRunAsRange
    UID:                <none>
    UID Range Min:            <none>
    UID Range Max:            <none>
  SELinux Context Strategy: MustRunAs
    User:                <none>
    Role:                <none>
    Type:                <none>
    Level:                <none>
  FSGroup Strategy: MustRunAs
    Ranges:                <none>
  Supplemental Groups Strategy: RunAsAny
    Ranges:                <none>
```
## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Vytvoření clusteru Azure Red Hat OpenShift](tutorial-create-cluster.md) 
