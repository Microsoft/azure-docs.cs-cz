---
title: Zobrazení monitorování Azure pro kontejnery protokolů v reálném čase | Dokumentace Microsoftu
description: Tento článek popisuje zobrazení protokolů kontejnerů (stdout/stderr) v reálném čase a událostí bez použití kubectl s Azure Monitor pro kontejnery.
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
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: 968ee4c8bb5d7e09ef3c345c46f6c7b839e0e25a
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67990040"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Postup zobrazení protokolů a událostí v reálném čase (Preview)
Azure Monitor for containers obsahuje funkci, která je aktuálně ve verzi Preview, která poskytuje živé zobrazení do protokolů kontejnerů služby Azure Kubernetes (stdout/stderr) a událostí bez nutnosti spouštět příkazy kubectl. Když vyberete jednu z možností, pod tabulkou data o výkonu na **uzlech**, řadičích av zobrazení **kontejnerů** se zobrazí nové podokno. Zobrazuje dynamické protokolování a události generované modulem kontejnerů, které vám pomůžou při řešení problémů v reálném čase.

>[!NOTE]
>Aby tato funkce fungovala, je nutné mít přístup k prostředku clusteru přístup přispěvatele.
>

Živé protokoly podporují tři různé metody řízení přístupu k protokolům:

1. Bez povolené oprávnění Kubernetes RBAC AKS
2. Povolené s autorizací Kubernetes RBAC AKS
3. AKS povolený pomocí jednotného přihlašování založené na Azure Active Directory (AD) založeného na SAML

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster Kubernetes bez povolené RBAC
 
Pokud máte cluster Kubernetes, který není nakonfigurovaný s Kubernetes RBAC se podařilo autorizovat nebo integrované s Azure AD jednotného přihlašování, není nutné postupovat podle následujících kroků. Vzhledem k tomu, že autorizace Kubernetes používá rozhraní Kube-API, vyžadují se oprávnění jen pro čtení.

## <a name="kubernetes-rbac-authorization"></a>Kubernetes RBAC se podařilo autorizovat
Pokud jste povolili Kubernetes RBAC se podařilo autorizovat, je potřeba použít vazbu role clusteru. Následující příklady postupu ukazují, jak nakonfigurovat vazby role clusteru z této šablony konfigurace yaml. 

1. Zkopírujte a vložte soubor yaml a uložte ho jako LogReaderRBAC.yaml.  

    ```
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRole 
    metadata: 
       name: containerHealth-log-reader 
    rules: 
       - apiGroups: [""] 
         resources: ["pods/log", "events"] 
         verbs: ["get", "list"]  
    --- 
    apiVersion: rbac.authorization.k8s.io/v1 
    kind: ClusterRoleBinding 
    metadata: 
       name: containerHealth-read-logs-global 
    roleRef: 
        kind: ClusterRole 
        name: containerHealth-log-reader 
        apiGroup: rbac.authorization.k8s.io 
    subjects: 
       - kind: User 
         name: clusterUser 
         apiGroup: rbac.authorization.k8s.io
    ```

2. Pokud konfigurujete poprvé, aplikujete vazbu pravidla clusteru spuštěním následujícího příkazu: `kubectl create -f LogReaderRBAC.yaml`. Pokud jste dříve povolili podporu pro živé protokoly ve verzi Preview předtím, než jsme napředstavili protokoly událostí v reálném čase, aktualizujte konfiguraci spuštěním následujícího příkazu: `kubectl apply -f LogReaderRBAC.yaml`.

## <a name="configure-aks-with-azure-active-directory"></a>Konfigurace AKS pomocí Azure Active Directory

AKS je nakonfigurovat pro ověřování uživatelů pomocí Azure Active Directory (AD). Pokud konfigurujete poprvé, přečtěte si téma [integrace Azure Active Directory se službou Azure Kubernetes](../../aks/azure-ad-integration.md). Během postupu vytvoření [klientské aplikace](../../aks/azure-ad-integration.md#create-the-client-application)zadejte následující:

- **Identifikátor URI přesměrování (volitelné)** : Toto je typ **webové** aplikace a hodnota základní adresy URL by měla být `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.
- Po registraci aplikace na stránce **Přehled** vyberte **ověřování** v levém podokně. V části **Upřesnit nastavení** na stránce **ověřování** se implicitně udělí **přístupové tokeny** a **tokeny ID** a změny se uloží.

>[!NOTE]
>Konfigurace ověřování pomocí Azure Active Directory pro jednotné přihlašování se dá provést jenom při počátečním nasazení nového clusteru AKS. Nelze nakonfigurovat jednotné přihlašování v pro cluster AKS, už nasazená.
  
>[!IMPORTANT]
>Pokud jste překonfigurovali službu Azure AD pro ověřování uživatelů pomocí aktualizovaného identifikátoru URI, vymažte mezipaměť prohlížeče, aby se zajistilo stažení a použití aktualizovaného ověřovacího tokenu.   

## <a name="view-live-logs-and-events"></a>Zobrazení živých protokolů a událostí

Události protokolu v reálném čase můžete zobrazit tak, jak jsou generovány modulem kontejneru z **uzlů**, **řadičů**a zobrazení **kontejnerů** . V podokně Vlastnosti vyberte možnost **Zobrazit živá data (Preview)** a podokno se zobrazí pod tabulkou data o výkonu, kde můžete zobrazit protokol a události v souvislém datovém proudu.

![Možnost zobrazení živých protokolů v podokně vlastností uzlu](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

Zprávy protokolu a události jsou omezené na základě toho, jaký typ prostředku je vybraný v zobrazení.

| Zobrazení | Typ prostředku | Protokol nebo událost | Zobrazená data |
|------|---------------|--------------|----------------|
| Uzly | Uzel | Událost | Když je uzel vybraný, události se nefiltrují a zobrazují Kubernetes události v rámci clusteru. Název podokna zobrazuje název clusteru. |
| Uzly | Pod | Událost | Když je vybraná událost pod, jsou filtrovány na obor názvů. Název podokna zobrazuje obor názvů pod. | 
| Kontrolery | Pod | Událost | Když je vybraná událost pod, jsou filtrovány na obor názvů. Název podokna zobrazuje obor názvů pod. |
| Kontrolery | Kontrolér | Událost | Když je vybraný kontroler událostí, vyfiltruje se na jeho obor názvů. Název podokna zobrazuje obor názvů kontroleru. |
| Uzly/řadiče/kontejnery | Kontejner | Logs | Název podokna zobrazuje název pod tím, kde je kontejner seskupen. |

Pokud AKS cluster je nakonfigurovaný s jednotným Přihlašováním pomocí AAD, zobrazí se výzva k ověření při prvním použití během této relace prohlížeče. Vyberte svůj účet a dokončete ověření pomocí Azure.  

Po úspěšném ověření se zobrazí v podokně za provozu protokolu v dolní části podokna uprostřed. Pokud se indikátor stavu načítání zobrazí zelená značka zaškrtnutí, která je v pravém podokně, znamená to, že ji může načítat data.
    
  ![Obnovení živé protokoly podokna data](./media/container-insights-live-logs/live-logs-pane-01.png)  

Na panelu hledání můžete filtrovat podle klíčového slova a zvýraznit text v protokolu nebo události a na panelu hledání na pravé straně se zobrazí, kolik výsledků odpovídá filtru.

  ![Živé protokoly podokno filtru příklad](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

Při prohlížení událostí můžete kromě toho omezit výsledky pomocí **filtru** , který se nachází na pravé straně panelu hledání. V závislosti na tom, jaký prostředek jste vybrali, zobrazuje funkce pilla seznam pod, oborem názvů nebo clusteru, ze kterého se má vybrat.  

Chcete-li pozastavit automatické rolování a ovládat chování podokna a chcete-li ručně procházet nově přečtenými daty, klikněte na možnost **posouvání** . Chcete-li znovu povolit automatické posouvání, stačí znovu kliknout na možnost posunu. Můžete také pozastavit načítání dat protokolů nebo událostí kliknutím na možnost **pozastavit** a až budete připraveni k obnovení, stačí kliknout na tlačítko **Přehrát**.  

![Živé protokoly podokna pozastavení živé zobrazení](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

Chcete-li zobrazit historické protokoly kontejnerů, vyberte **Zobrazit** protokoly kontejnerů v rozevíracím seznamu zobrazení **v části analýza**, a můžete přejít na Azure monitor protokoly.

## <a name="next-steps"></a>Další postup

- Chcete-li pokračovat v učení, jak používat Azure Monitor a monitorovat další aspekty vašeho clusteru AKS, přečtěte si téma [zobrazení Azure Kubernetes Service health](container-insights-analyze.md).

- Podívejte se na [příklady dotazů protokolu](container-insights-log-search.md#search-logs-to-analyze-data) , kde najdete předdefinované dotazy a příklady pro vyhodnocení nebo přizpůsobení výstrah, vizualizace a analýzy clusterů.
