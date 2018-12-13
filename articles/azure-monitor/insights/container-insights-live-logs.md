---
title: Zobrazení monitorování Azure pro kontejnery protokolů v reálném čase | Dokumentace Microsoftu
description: Tento článek popisuje bez použití kubectl s monitorováním Azure pro kontejnery v reálném čase zobrazení protokolů kontejneru (stdout/stderr).
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/06/2018
ms.author: magoedte
ms.openlocfilehash: da11bb0669bf6bde2c65b2a7a0badaa1ae35abda
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53189115"
---
# <a name="how-to-view-container-logs-real-time-with-azure-monitor-for-containers-preview"></a>Postup zobrazení kontejneru protokoly reálném čase pomocí Azure monitoru pro kontejnery (Preview)
Tuto funkci, která je aktuálně ve verzi preview, poskytuje v reálném čase přehled o vaše protokoly kontejneru Azure Kubernetes Service (AKS) (stdout/stderr) bez nutnosti spuštění příkazů kubectl. Když vyberete tuto možnost, nové podokno se zobrazí pod tabulkou dat výkonu kontejnery na **kontejnery** zobrazení a zobrazuje živé protokolování vygenerované modulem kontejneru pro další pomoc při řešení problémů v reálném čase.  

Živé protokoly podporuje tři různé metody řídit přístup k protokolům:

1. Bez povolené oprávnění Kubernetes RBAC AKS 
2. Povolené s autorizací Kubernetes RBAC AKS
3. Povolené s Azure Active Directory (AD) SAML AKS na základě jednotného přihlašování 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster Kubernetes bez povolené RBAC
 
Pokud máte cluster Kubernetes, který není nakonfigurovaný s Kubernetes RBAC se podařilo autorizovat nebo integrované s Azure AD jednotného přihlašování, není nutné postupovat podle následujících kroků. Vzhledem k tomu Kubernetes autorizace používá rozhraní api kube, vyžaduje se oprávnění jen pro čtení.

## <a name="kubernetes-rbac-authorization"></a>Kubernetes RBAC se podařilo autorizovat
Pokud jste povolili Kubernetes RBAC se podařilo autorizovat, je potřeba použít vazbu role clusteru. Následující příklady postupu ukazují, jak nakonfigurovat vazby role clusteru z této šablony konfigurace yaml.   

1. Zkopírujte a vložte soubor yaml a uložte ho jako LogReaderRBAC.yaml.  

   ```
   kind: ClusterRole 
   apiVersion: rbac.authorization.k8s.io/v1 
   metadata:   
      name: containerHealth-log-reader 
   rules: 
      - apiGroups: [""]   
        resources: ["pods/log"]   
        verbs: ["get"] 
   --- 
   kind: ClusterRoleBinding 
   apiVersion: rbac.authorization.k8s.io/v1 
   metadata:   
      name: containerHealth-read-logs-global 
   subjects:   
      - kind: User     
        name: clusterUser
        apiGroup: rbac.authorization.k8s.io 
    roleRef:   
       kind: ClusterRole
       name: containerHealth-log-reader
       apiGroup: rbac.authorization.k8s.io 
   ```

2. Vytvořit vazbu pravidla cluster spuštěním následujícího příkazu: `kubectl create -f LogReaderRBAC.yaml`. 

## <a name="configure-aks-with-azure-active-directory"></a>Konfigurace AKS pomocí Azure Active Directory
AKS je nakonfigurovat pro ověřování uživatelů pomocí Azure Active Directory (AD). Pokud je konfigurujete poprvé, přečtěte si téma [integrace Azure Active Directory pomocí služby Azure Kubernetes Service](../../aks/aad-integration.md). Během postupu vytvořte [klientská aplikace](../../aks/aad-integration.md#create-client-application) a zadejte **identifikátor URI pro přesměrování**, je třeba přidat do seznamu jiném identifikátoru URI ** https://ininprodeusuxbase.microsoft.com/***.  

>[!NOTE]
>Konfigurace ověřování pomocí Azure Active Directory pro jednotné přihlašování na lze provést pouze během počáteční nasazení nového clusteru AKS. Nelze nakonfigurovat jednotné přihlašování v pro cluster AKS, už nasazená.  
> 

## <a name="view-live-logs"></a>Zobrazit protokoly za provozu
Když zobrazujete **kontejnery**, můžete **protokoly kontejneru zobrazení** nebo **zobrazit živé protokoly kontejneru**.  Když vyberete **živé protokoly kontejneru zobrazení**, nové podokno se zobrazí pod tabulka dat výkonu kontejnery a zobrazení živého protokolování vygenerované modulem kontejneru pro další pomoc při řešení problémů v reálném čase.  
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
2. Z **Microsoft Azure** nabídce vyberte možnost **monitorování** a pak vyberte **kontejnery**.  
3. Vyberte kontejner ze seznamu **monitorovat kontejnery** zobrazení.  
4. Vyberte **kontejnery** zobrazení a na panelu Vlastnosti pro vybraný kontejner odkaz **zobrazit živé protokoly kontejneru** je uvedena.  
5. Pokud AKS cluster je nakonfigurovaný s jednotným Přihlašováním pomocí AAD, zobrazí se výzva k ověření při prvním použití během této relace prohlížeče. Vyberte svůj účet a dokončete ověření pomocí Azure.  

Po úspěšném ověření se zobrazí v podokně za provozu protokolu v dolní části podokna uprostřed. Pokud se indikátor stavu načítání zobrazí zelená značka zaškrtnutí, která je v pravém podokně, znamená to, že ji může načítat data.
    
  ![Obnovení živé protokoly podokna data](./media/container-insights-live-logs/live-logs-pane-01.png)  

V panelu vyhledávání můžete filtrovat podle klíčových slov, zvýrazněte text v protokolu.   

  ![Živé protokoly podokno filtru příklad](./media/container-insights-live-logs/live-logs-pane-filter-01.png)

Pozastavení Automatické posunování a řídit chování v podokně a bylo možné ručně procházet nová data protokolu, čtení, klikněte na **posuvníku** možnost.  Znovu povolit Automatické posunování, stačí kliknout **posuvníku** možnost znovu.  Načítání dat protokolu je také možné pozastavit kliknutím na **pozastavit** možnost a až budete připravení pokračovat, stačí kliknout **Přehrát**.  

![Živé protokoly podokna pozastavení živé zobrazení](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

## <a name="next-steps"></a>Další postup
Chcete-li pokračovat v učení, jak používat Azure Monitor a monitorovat další aspekty vašeho clusteru AKS, přečtěte si téma [zobrazení Azure Kubernetes Service health](container-insights-analyze.md).