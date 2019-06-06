---
title: Zobrazení monitorování Azure pro kontejnery protokolů v reálném čase | Dokumentace Microsoftu
description: Tento článek popisuje bez použití kubectl s monitorováním Azure pro kontejnery v reálném čase zobrazit protokoly kontejneru (stdout/stderr) a události.
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
ms.date: 06/04/2019
ms.author: magoedte
ms.openlocfilehash: 8d4cc5e46066ad2f18d596d0484f62f478b4cc23
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514329"
---
# <a name="how-to-view-logs-and-events-in-real-time-preview"></a>Postup zobrazení protokolů a událostí v reálném čase (preview)
Azure Monitor pro kontejnery obsahuje funkci, která je aktuálně ve verzi preview, která poskytuje živé zobrazit protokoly kontejneru Azure Kubernetes Service (AKS) (stdout/stderr) a události bez nutnosti spuštění příkazů kubectl. Když vyberete jednu z možností, nové podokno se zobrazí pod tabulkou dat výkonu na **uzly**, **řadiče**, a **kontejnery** zobrazení. Zobrazuje živé protokolování a události generované modulem kontejneru pro další pomoc při řešení problémů v reálném čase. 

>[!NOTE]
>**Přispěvatel** přístup k prostředku clusteru se vyžaduje pro tuto funkci používat.
>

Živé protokoly podporují tři různé metody řídit přístup k protokolům:

1. Bez povolené oprávnění Kubernetes RBAC AKS 
2. Povolené s autorizací Kubernetes RBAC AKS
3. Povolené s Azure Active Directory (AD) založené na SAML jednotného přihlašování v AKS 

## <a name="kubernetes-cluster-without-rbac-enabled"></a>Cluster Kubernetes bez povolené RBAC
 
Pokud máte cluster Kubernetes, který není nakonfigurovaný s Kubernetes RBAC se podařilo autorizovat nebo integrované s Azure AD jednotného přihlašování, není nutné postupovat podle následujících kroků. Vzhledem k tomu Kubernetes autorizace používá rozhraní api kube, jsou vyžadována oprávnění jen pro čtení.

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

2. Pokud ho konfigurujete poprvé, můžete vytvořit vazbu pravidla cluster spuštěním následujícího příkazu: `kubectl create -f LogReaderRBAC.yaml`. Pokud už dříve povolili podporu pro živé protokoly ve verzi preview předtím, než jsme představili živé protokoly událostí, a aktualizujte konfiguraci, spusťte následující příkaz: `kubectl apply -f LogReaderRBAC.yml`. 

## <a name="configure-aks-with-azure-active-directory"></a>Konfigurace AKS pomocí Azure Active Directory
AKS je nakonfigurovat pro ověřování uživatelů pomocí Azure Active Directory (AD). Pokud je konfigurujete poprvé, přečtěte si téma [integrace Azure Active Directory pomocí služby Azure Kubernetes Service](../../aks/azure-ad-integration.md). Během postupu vytvořte [klientská aplikace](../../aks/azure-ad-integration.md#create-client-application), musíte zadat dva **identifikátor URI pro přesměrování** položky. Dva identifikátory URI jsou:

- https://ininprodeusuxbase.microsoft.com/*
- https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html  

>[!NOTE]
>Konfigurace ověřování pomocí Azure Active Directory pro jednotné přihlašování na lze provést pouze během počáteční nasazení nového clusteru AKS. Nelze nakonfigurovat jednotné přihlašování v pro cluster AKS, už nasazená. Musíte nakonfigurovat ověřování z **registrace aplikace (starší verze)** možnost ve službě Azure AD, aby bylo možné podporovat použití zástupných znaků v identifikátoru URI a při jeho přidání do seznamu, zaregistrujte ho jako **nativní** aplikace.
> 

## <a name="view-live-logs-and-events"></a>Zobrazit živé protokoly a události

Můžete zobrazit události v reálném čase protokolu vygenerovaný modulem kontejneru z **uzly**, **řadiče**, a **kontejnery** zobrazení. V podokně vlastností, vyberte **zobrazení dynamických dat (preview)** možnost a podokno se zobrazí pod tabulkou dat výkonu, kde můžete zobrazit protokol a události v nepřetržitý datový proud. 

![Možnost živé protokoly uzlu vlastnosti podokna zobrazení](./media/container-insights-live-logs/node-properties-live-logs-01.png)  

Zprávy protokolů a událostí jsou omezené na základě, na jaký typ prostředku je vybrána v zobrazení.

| Zobrazení | Typ prostředku | Protokol nebo událost | Data uvedená |
|------|---------------|--------------|----------------|
| Uzly | Node | Událost | Když je vybrán uzel události nefiltrují a zobrazit události celého clusteru Kubernetes. Název podokně zobrazí název clusteru. |
| Uzly | Pod | Událost | Pokud je vybrána pod události jsou filtrovány do svého oboru názvů. Název podokně zobrazí obor názvů pod. | 
| Kontrolery | Pod | Událost | Pokud je vybrána pod události jsou filtrovány do svého oboru názvů. Název podokně zobrazí obor názvů pod. |
| Kontrolery | Kontroler | Událost | Při výběru kontroleru události jsou filtrovány do svého oboru názvů. Název podokně zobrazí obor názvů kontroleru. |
| Uzly/řadiče/kontejnery | Kontejner | Protokoly | Název podokně zobrazí, že název kontejneru pod seskupen s. |

Pokud AKS cluster je nakonfigurovaný s jednotným Přihlašováním pomocí AAD, zobrazí se výzva k ověření při prvním použití během této relace prohlížeče. Vyberte svůj účet a dokončete ověření pomocí Azure.  

Po úspěšném ověření se zobrazí v podokně za provozu protokolu v dolní části podokna uprostřed. Pokud se indikátor stavu načítání zobrazí zelená značka zaškrtnutí, která je v pravém podokně, znamená to, že ji může načítat data.
    
  ![Obnovení živé protokoly podokna data](./media/container-insights-live-logs/live-logs-pane-01.png)  

V panelu vyhledávání můžete filtrovat podle klíčových slov, zvýrazněte text v protokolu nebo události a na panelu hledání úplně vpravo, ukazuje, kolik výsledky odpovídají na filtr.   

  ![Živé protokoly podokno filtru příklad](./media/container-insights-live-logs/live-logs-pane-filter-example-01.png)

Pozastavení automatického posunu a řídit chování v podokně a bylo možné ručně procházet nová data přečíst, klikněte na **posuvníku** možnost. Opětovné povolení automatického posunu, stačí kliknout **posuvníku** možnost znovu. Načítání dat protokolu nebo událostí je také možné pozastavit kliknutím na **pozastavit** možnost a až budete připravení pokračovat, stačí kliknout **Přehrát**.  

![Živé protokoly podokna pozastavení živé zobrazení](./media/container-insights-live-logs/live-logs-pane-pause-01.png)

Můžete přejít na protokoly Azure monitoru chcete zobrazit protokoly historie kontejneru tak, že vyberete **zobrazit protokoly kontejneru** z rozevíracího seznamu **zobrazit v analytics**.

## <a name="next-steps"></a>Další postup
- Chcete-li pokračovat v učení, jak používat Azure Monitor a monitorovat další aspekty vašeho clusteru AKS, přečtěte si téma [zobrazení Azure Kubernetes Service health](container-insights-analyze.md).
- Zobrazení [protokolu Příklady dotazů](container-insights-log-search.md#search-logs-to-analyze-data) předem definovaných dotazů a příklady, které vyhodnotí nebo přizpůsobení pro výstrahy vizualizace a analýza vašich clusterů.
