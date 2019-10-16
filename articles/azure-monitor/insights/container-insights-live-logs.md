---
title: Zobrazit Azure Monitor pro protokoly kontejnerů v reálném čase | Microsoft Docs
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
ms.openlocfilehash: d947b44177e9aa5777d759286d982e974e378497
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389786"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Postup zobrazení protokolů a událostí v reálném čase (Preview)
Azure Monitor for containers obsahuje funkci, která je aktuálně ve verzi Preview, která poskytuje živé zobrazení do protokolů kontejnerů služby Azure Kubernetes (stdout/stderr) a událostí bez nutnosti spouštět příkazy kubectl. Když vyberete jednu z možností, pod tabulkou data o výkonu na **uzlech**, **řadičích**a v zobrazení **kontejnerů** se zobrazí nové podokno. Zobrazuje dynamické protokolování a události generované modulem kontejnerů, které vám pomůžou při řešení problémů v reálném čase.

>[!NOTE]
>Tato funkce je dostupná ve všech oblastech Azure, včetně Azure Čína. V tuto chvíli není dostupná ve službě Azure USA pro státní správu.

>[!NOTE]
>Aby tato funkce fungovala, vyžaduje se přístup k prostředku clusteru prostřednictvím **role uživatele clusteru služby Azure Kubernetes** . [Přečtěte si další informace o roli uživatele clusteru Azure Kubernetes](https://docs.microsoft.com/en-us/azure/aks/control-kubeconfig-access#available-cluster-roles-permissions).

Živé protokoly podporují tři různé metody řízení přístupu k protokolům:

1. AKS bez povoleného ověřování RBAC Kubernetes
2. AKS povolený s autorizací Kubernetes RBAC
3. AKS povolený pomocí jednotného přihlašování založené na Azure Active Directory (AD) založeného na SAML

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster Kubernetes bez RBAC povolen
 
Pokud máte cluster Kubernetes, který není nakonfigurovaný s autorizací Kubernetes RBAC nebo se integruje s jednotným přihlašováním k Azure AD, nemusíte postupovat podle těchto kroků. Vzhledem k tomu, že autorizace Kubernetes používá rozhraní Kube-API, vyžadují se oprávnění jen pro čtení.

## <a name="kubernetes-rbac-authorization"></a>Autorizace Kubernetes RBAC
Pokud jste povolili autorizaci Kubernetes RBAC, budete muset použít vazbu role clusteru. Následující příklady kroků ukazují, jak nakonfigurovat vazbu role clusteru z této šablony konfigurace YAML. 

1. Zkopírujte a vložte soubor YAML a uložte ho jako LogReaderRBAC. yaml.  

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

2. Pokud konfigurujete poprvé, aplikujete vazbu pravidla clusteru spuštěním následujícího příkazu: `kubectl create -f LogReaderRBAC.yaml`. Pokud jste dříve povolili podporu pro živé protokoly ve verzi Preview předtím, než jsme napředstavili protokoly událostí Live, aktualizujte konfiguraci spuštěním následujícího příkazu: `kubectl apply -f LogReaderRBAC.yaml`.

## <a name="configure-aks-with-azure-active-directory"></a>Konfigurace AKS pomocí Azure Active Directory

AKS je možné nakonfigurovat tak, aby pro ověřování uživatelů používala Azure Active Directory (AD). Pokud konfigurujete poprvé, přečtěte si téma [integrace Azure Active Directory se službou Azure Kubernetes](../../aks/azure-ad-integration.md). Během postupu vytvoření [klientské aplikace](../../aks/azure-ad-integration.md#create-the-client-application)zadejte následující:

-  **Identifikátor URI pro přesměrování**: je třeba vytvořit dva typy **webových** aplikací. První základní hodnota URL by měla být `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` a druhá základní hodnota URL by měla být `https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.
- Po registraci aplikace na stránce **Přehled** vyberte **ověřování** v levém podokně. V části **Upřesnit nastavení** na stránce **ověřování** se implicitně udělí **přístupové tokeny** a **tokeny ID** a změny se uloží.

>[!NOTE]
>Pokud tuto funkci používáte v oblasti Azure Čína, první základní hodnota URL by měla být `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` a druhá základní hodnota URL by měla být `https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`.

>[!NOTE]
>Konfigurace ověřování pomocí Azure Active Directory pro jednotné přihlašování se dá provést jenom při počátečním nasazení nového clusteru AKS. Nemůžete nakonfigurovat jednotné přihlašování pro cluster AKS, který je už nasazený.
  
>[!IMPORTANT]
>Pokud jste překonfigurovali službu Azure AD pro ověřování uživatelů pomocí aktualizovaného identifikátoru URI, vymažte mezipaměť prohlížeče, aby se zajistilo stažení a použití aktualizovaného ověřovacího tokenu.   

## <a name="view-live-logs-and-events"></a>Zobrazení živých protokolů a událostí

Události protokolu v reálném čase můžete zobrazit tak, jak jsou generovány modulem kontejneru z **uzlů**, **řadičů**a zobrazení **kontejnerů** . V podokně Vlastnosti vyberte možnost **Zobrazit živá data (Preview)** a podokno se zobrazí pod tabulkou data o výkonu, kde můžete zobrazit protokol a události v souvislém datovém proudu.

![Možnost zobrazení živých protokolů v podokně vlastností uzlu](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

Zprávy protokolu a události jsou omezené na základě toho, jaký typ prostředku je vybraný v zobrazení.

| Zobrazit | Typ prostředku | Protokol nebo událost | Zobrazená data |
|------|---------------|--------------|----------------|
| Uzly | Uzel | Událost | Když je uzel vybraný, události se nefiltrují a zobrazují Kubernetes události v rámci clusteru. Název podokna zobrazuje název clusteru. |
| Uzly | Nulu | Událost | Když je vybraná událost pod, jsou filtrovány na obor názvů. Název podokna zobrazuje obor názvů pod. | 
| Kontrolou | Nulu | Událost | Když je vybraná událost pod, jsou filtrovány na obor názvů. Název podokna zobrazuje obor názvů pod. |
| Kontrolou | Kontrolér | Událost | Když je vybraný kontroler událostí, vyfiltruje se na jeho obor názvů. Název podokna zobrazuje obor názvů kontroleru. |
| Uzly/řadiče/kontejnery | Kontejner | Protokoly | Název podokna zobrazuje název pod tím, kde je kontejner seskupen. |

Pokud je cluster AKS nakonfigurovaný pomocí jednotného přihlašování pomocí AAD, budete vyzváni k ověření při prvním použití během této relace prohlížeče. Vyberte svůj účet a dokončete ověřování pomocí Azure.  

Po úspěšném ověření se v dolní části podokna v prostředním podokně zobrazí podokno se živým protokolem. Pokud indikátor stavu načítání zobrazuje zelený symbol zaškrtnutí, který je na pravé straně podokna, znamená to, že může načíst data.
    
  ![Načtená data v podokně protokoly v reálném čase](./media/container-insights-live-logs/live-logs-pane-01.png)  

Na panelu hledání můžete filtrovat podle klíčového slova a zvýraznit text v protokolu nebo události a na panelu hledání na pravé straně se zobrazí, kolik výsledků odpovídá filtru.

  ![Příklad filtru podokna služby Live logs](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

Při prohlížení událostí můžete kromě toho omezit výsledky pomocí **filtru** , který se nachází na pravé straně panelu hledání. V závislosti na tom, jaký prostředek jste vybrali, zobrazuje funkce pilla seznam pod, oborem názvů nebo clusteru, ze kterého se má vybrat.  

Chcete-li pozastavit automatické rolování a ovládat chování podokna a chcete-li ručně procházet nově přečtenými daty, klikněte na možnost **posouvání** . Chcete-li znovu povolit automatické posouvání, stačí znovu kliknout na možnost **posunu** . Můžete také pozastavit načítání dat protokolů nebo událostí kliknutím na možnost **pozastavit** a až budete připraveni k obnovení, stačí kliknout na tlačítko **Přehrát**.  

![Živý pohled na pozastavit podokno protokolů](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

Chcete-li zobrazit historické protokoly kontejnerů, vyberte **Zobrazit protokoly kontejnerů** v rozevíracím seznamu zobrazení **v části analýza**, a můžete přejít na Azure monitor protokoly.

## <a name="next-steps"></a>Další kroky

- Pokud chcete pokračovat v učení, jak používat Azure Monitor a monitorovat další aspekty clusteru AKS, přečtěte si téma [zobrazení stavu služby Azure Kubernetes](container-insights-analyze.md).

- Podívejte se na [příklady dotazů protokolu](container-insights-log-search.md#search-logs-to-analyze-data) , kde najdete předdefinované dotazy a příklady pro vyhodnocení nebo přizpůsobení výstrah, vizualizace a analýzy clusterů.
