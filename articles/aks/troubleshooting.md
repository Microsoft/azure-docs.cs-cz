---
title: S řešením běžných problémů služby Azure Kubernetes
description: Zjistěte, jak odstraňovat potíže a řešit běžné problémy při použití služby Azure Kubernetes Service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: 9f082c5f198ebd7123058bd250d3fef55494d553
ms.sourcegitcommit: f94f84b870035140722e70cab29562e7990d35a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43287535"
---
# <a name="aks-troubleshooting"></a>Řešení potíží s AKS
Pokud vytvoříte nebo clustery správce AKS, někdy můžete setkat s problémy. Tento článek podrobně popisuje některé běžné problémy a postup řešení potíží.

### <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-issues"></a>Obecně platí, kde najdu informace o ladění problémů s Kubernetes?

[Tady] (https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/) je oficiální odkaz pro řešení potíží s clustery kubernetes.
[Tady](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md) je odkaz na Průvodce odstraňováním potíží publikoval(a) technický pracovník Microsoftu týkající se řešení potíží s podů, uzly, clustery, atd.

### <a name="i-am-getting-a-quota-exceeded-error-during-create-or-upgrade-what-should-i-do"></a>Chyby překročení kvóty dochází při vytvoření nebo aktualizace. Co bych měl/a dělat? 

Budete muset požádat o jader [tady](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

### <a name="what-is-the-max-pods-per-node-setting-for-aks"></a>Jaký je maximální počet podů na uzel pro AKS?

Maximální počet podů na uzel nastavené na 30 ve výchozím nastavení při nasazení clusteru AKS na portálu Azure portal.
Maximální počet podů na uzel jsou nastaveny na hodnotu 110 ve výchozím nastavení, pokud nasadíte cluster AKS v Azure CLI. (Ujistěte se, že používáte nejnovější verzi Azure CLI). Toto výchozí nastavení lze změnit pomocí – maximální počet-uzly-za-pod příznak v az aks create příkaz.

### <a name="i-am-getting-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Vyskytla se chyba "insufficientSubnetSize" při nasazování clusteru AKS pomocí rozšířeného sítě. Co bych měl/a dělat?

Ve vybrané pro práci v síti během AKS vytváří možnost vlastní virtuální sítě se používá Azure CNI pro správu IP adres. Počet uzlů v clusteru AKS může být kdekoli v rozmezí 1 a 100. Na základě 2) nad podsíť musí být větší než součinu počtu používaných uzlů a maximální počet podů na velikost podsítě uzlu velikost > počet uzlů v clusteru * maximálního počtu podů na jeden uzel.

### <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Moje pod se zasekla v automatickém režimu "CrashLoopBackOff". Co bych měl/a dělat?

Mohou existovat různé důvody pod se zablokuje a v tomto režimu. Můžete chtít prozkoumat 
* Pod samotné pomocí `kubectl describe pod <pod-name>`
* Protokolování použití  `kubectl log <pod-name>`

### <a name="i-am-trying-to-enable-rbac-on-an-existing-cluster-can-you-tell-me-how-i-can-do-that"></a>Pokouším RBAC povolit v existujícím clusteru. Můžete mi Jak můžu to udělat?

Bohužel povolení RBAC na stávajících clusterů není v tuto chvíli nepodporuje. Je potřeba explicitně vytvoření nových clusterů. Pokud používáte rozhraní příkazového řádku, RBAC je ve výchozím nastavení povolené, že není k dispozici na portálu pro AKS přepínací tlačítko, aby je vytvoření pracovního postupu.

### <a name="i-created-a-cluster-using-the-azure-cli-with-defaults-or-the-azure-portal-with-rbac-enabled-and-numerous-warnings-in-the-kubernetes-dashboard-the-dashboard-used-to-work-before-without-any-warnings-what-should-i-do"></a>Jsem vytvořil clusteru pomocí rozhraní příkazového řádku Azure s výchozí hodnoty nebo na webu Azure portal pomocí RBAC povolená a mnoha upozornění na řídicím panelu kubernetes. Řídicí panel použitý k před bez upozornění fungovat. Co bych měl/a dělat?

Na řídicím panelu se zobrazují upozornění důvodem je, že teď je povolená s RBAC'ed a ve výchozím nastavení byl zakázán přístup k němu. Obecně platí tento přístup se považuje za vhodné od výchozí vystavení řídicího panelu pro všechny uživatele clusteru může vést k ohrožení zabezpečení. Pokud budete chtít povolit řídicího panelu, postupujte podle to [blogu](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/) ho chcete povolit.

### <a name="i-cant-seem-to-connect-to-the-dashboard-what-should-i-do"></a>Nelze zdá se, že pro připojení k řídicímu panelu. Co bych měl/a dělat?

Nejjednodušší způsob, jak získat přístup k vaší službě mimo cluster, je spustit kubectl proxy, který předává požadavky serveru proxy na místním hostiteli port 8001 na serveru Kubernetes API. Odtud můžete apiserver proxy ke službě: http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#! / uzel? obor názvů = default

Pokud se nezobrazí řídicí panel kubernetes, zkontrolujte, zda pod kube proxy server běží v oboru názvů kube-system. Pokud není ve spuštěném stavu, odstraňte pod a bude restartován.

### <a name="i-could-not-get-logs-using-kubectl-logs-or-cannot-connect-to-the-api-server-getting-the-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Můžu nebylo možné získat protokoly pomocí Kubectl protokolů nebo se nemůže připojit k získání rozhraní api serveru "Chyba ze serveru: Chyba volání back-endu: vytáčení tcp...". Co bych měl/a dělat?

Ujistěte se, že je otevřen pro připojení k rozhraní API serveru výchozí skupiny zabezpečení sítě se nezmění a port 22. Kontrola, zda je spuštěná tunnelfront pod v oboru názvů kube-system. Pokud není, platnost ho odstranit a bude restartován.

### <a name="i-am-trying-to-upgrade-or-scale-and-am-getting-message-changing-property-imagereference-is-not-allowed-error--how-do-i-fix-this-issue"></a>Mohu provést upgrade nebo změna velikosti a Power BI Desktop zobrazuje "zpráva": "Změny vlastnosti"imageReference"nejsou povolené." Chyba  Jak opravit tento problém?

Je možné, že se tato chyba zobrazuje, protože jste upravili značky v agentské uzly v clusteru AKS. Úprava a odstranění značek a dalších vlastností prostředků ve skupině prostředků MC_ * může vést k neočekávaným výsledkům. Úprava prostředků v rámci MC_ * v clusteru AKS dělí cíle na úrovni služby.


