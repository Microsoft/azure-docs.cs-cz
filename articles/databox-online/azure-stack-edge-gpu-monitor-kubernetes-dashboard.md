---
title: Monitorování zařízení Azure Stack Edge pro prostřednictvím řídicího panelu Kubernetes | Microsoft Docs
description: Popisuje, jak získat přístup k řídicímu panelu Kubernetes a použít ho k monitorování zařízení Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alkohli
ms.openlocfilehash: 137cff47d49be1405f60bc47cd16f7f027ab63a9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91320825"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-pro-gpu-device"></a>Použití řídicího panelu Kubernetes k monitorování zařízení GPU Azure Stack Edge pro

Tento článek popisuje, jak získat přístup k řídicímu panelu Kubernetes Edge pro a použít ho k monitorování vašeho zařízení GPU pro Azure Stack. Chcete-li monitorovat své zařízení, můžete použít grafy v Azure Portal, zobrazit řídicí panel Kubernetes nebo spustit `kubectl` příkazy prostřednictvím rozhraní PowerShell zařízení. 

Tento článek se zaměřuje jenom na úlohy monitorování, které se dají provádět na řídicím panelu Kubernetes.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Přístup k řídicímu panelu Kubernetes na zařízení
> * Zobrazit moduly nasazené na zařízení
> * Získat IP adresu pro aplikace nasazené na zařízení
> * Zobrazit protokoly kontejnerů pro moduly nasazené v zařízení


## <a name="about-kubernetes-dashboard"></a>O řídicím panelu Kubernetes

Řídicí panel Kubernetes je webové uživatelské rozhraní, které můžete použít k řešení problémů vašich kontejnerových aplikací. Řídicí panel Kubernetes je alternativa založená na uživatelském rozhraní k `kubectl` příkazovému řádku Kubernetes. Další informace najdete v tématu [řídicí panel Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/). 

Na zařízení Azure Stack Edge pro můžete pomocí řídicího panelu Kubernetes v režimu jen *pro čtení* získat přehled o aplikacích spuštěných v zařízení Azure Stack Edge pro, zobrazit stav prostředků clusteru Kubernetes a zobrazit všechny chyby, ke kterým došlo v zařízení.

## <a name="access-dashboard"></a>Přístup k řídicímu panelu

Řídicí panel Kubernetes je *jen pro čtení* a běží na hlavním uzlu Kubernetes na portu 31000. Pro přístup k řídicímu panelu použijte následující postup: 

1. V místním uživatelském rozhraní zařízení, klikněte na **zařízení** a pak na **koncové body zařízení**. 
1. Vyberte **stáhnout config** a Stáhněte si tak `kubeconfig` , abyste měli přístup k řídicímu panelu. Uložte `config.json` soubor do místního systému.
1. Výběrem adresy URL řídicího panelu Kubernetes otevřete řídicí panel v prohlížeči.

    ![Adresa URL řídicího panelu Kubernetes na stránce zařízení v místním uživatelském rozhraní](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. Na **přihlašovací stránce řídicího panelu Kubernetes** :
    
    1. Vyberte **kubeconfig**. 
        ![Vybrat možnost kubeconfig](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png) 
    1. Vyberte tři tečky **...**. Přejděte do `kubeconfig` složky, kterou jste stáhli dříve v místním systému. Vyberte **Sign in** (Přihlásit se).
        ![Přejít k souboru kubeconfig](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-2.png)    

6. Nyní můžete zobrazit řídicí panel Kubernetes pro zařízení Azure Stack Edge pro v režimu jen pro čtení.

    ![Hlavní stránka řídicího panelu Kubernetes](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>Zobrazit stav modulu

Výpočetní moduly jsou kontejnery, které mají implementaci obchodní logiky. Řídicí panel můžete použít k ověření, jestli se výpočetní modul úspěšně nasadil na zařízení Azure Stack Edge pro.

Chcete-li zobrazit stav modulu, postupujte podle následujících kroků na řídicím panelu:

1. V levém podokně řídicího panelu přejít na **obor názvů**. Filtrovat podle oboru názvů, kde se zobrazují IoT Edge moduly, v tomto případě **iotedge**.
1. V levém podokně přejdete na **úlohy > nasazení**.
1. V pravém podokně se zobrazí všechny moduly nasazené na vašem zařízení. V tomto případě byl modul GettingStartedWithGPU nasazený na Azure Stack Edge pro. Můžete vidět, že byl modul nasazen.

    ![Zobrazit nasazení modulu](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>Získat IP adresu pro služby nebo moduly

Řídicí panel můžete použít k získání IP adres služeb nebo modulů, které chcete zveřejnit mimo cluster Kubernetes. 

Rozsah IP adres pro tyto externí služby přiřadíte prostřednictvím místního webového uživatelského rozhraní zařízení na stránce **Nastavení výpočetního sítě** . Po nasazení IoT Edge moduly můžete chtít získat IP adresu přiřazenou konkrétnímu modulu nebo službě. 

Chcete-li získat IP adresu, postupujte podle následujících kroků na řídicím panelu:

1. V levém podokně řídicího panelu přejít na **obor názvů**. Filtrovat podle oboru názvů, ve kterém je nainstalovaná externí služba, v tomto případě **iotedge**.
1. V levém podokně přejdete na **zjišťování a vyrovnávání zatížení > služby**.
1. V pravém podokně se zobrazí všechny služby, které jsou spuštěné v `iotedge` oboru názvů na vašem zařízení Azure Stack Edge pro.

    ![Získat IP adresu pro externí služby](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-get-ip-external-service-1.png)

## <a name="view-container-logs"></a>Zobrazení protokolů kontejneru

K dispozici jsou případy, kdy potřebujete zobrazit protokoly kontejnerů. Řídicí panel můžete použít k získání protokolů pro konkrétní kontejner, který jste nasadili do clusteru Kubernetes.

Chcete-li zobrazit protokoly kontejnerů, postupujte podle těchto kroků na řídicím panelu:

1. V levém podokně řídicího panelu přejít na **obor názvů**. Filtrovat podle oboru názvů, kde jsou nasazené moduly IoT Edge, v tomto případě **iotedge**.
1. V levém podokně přejdete na **úlohy > lusky**.
1. V pravém podokně se zobrazí všechny lusky běžící na vašem zařízení. Identifikujte rozhraní pod, na kterém je spuštěn modul, pro který chcete zobrazit protokoly. Vyberte svislé tři tečky u položky pod, kterou jste identifikovali, a z místní nabídky vyberte **protokoly**.

    ![Zobrazit protokoly kontejneru 1](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)

1. Protokoly se zobrazí v prohlížeči protokolů, který je integrovaný do řídicího panelu. Protokoly si také můžete stáhnout.

    ![Zobrazit protokoly kontejneru 2](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-container-logs-1.png)
    

## <a name="view-cpu-memory-usage"></a>Zobrazení CPU, využití paměti

Řídicí panel Kubernetes pro zařízení Azure Stack Edge pro má taky [doplněk serveru metrik](https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/) , který AGREGUJE využití CPU a paměti napříč prostředky Kubernetes.
 
Můžete například zobrazit procesor a paměť využívané napříč nasazeními ve všech oborech názvů. 

![Zobrazení využití CPU a paměti napříč všemi nasazeními](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-all-1.png)

Můžete také filtrovat podle konkrétního oboru názvů. V následujícím příkladu můžete zobrazit spotřebu CPU a paměti jenom pro nasazení ARC Azure.  

![Zobrazení využití CPU a paměti pro nasazení ARC Azure](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/view-cpu-memory-azure-arc-1.png)

Server metrik Kubernetes poskytuje kanály automatického škálování, jako je například [Automatické škálování vodorovně pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/).


## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [řešit problémy se zařízením](azure-stack-edge-gpu-troubleshoot.md).
