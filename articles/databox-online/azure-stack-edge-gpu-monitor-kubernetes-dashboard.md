---
title: Monitorování zařízení Azure Stack Edge prostřednictvím řídicího panelu Kubernetes | Microsoft Docs
description: Popisuje, jak přistupovat k Kubernetes hraničnímu zařízení a používat Azure Stack ho pomocí řídicího panelu.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/27/2020
ms.author: alkohli
ms.openlocfilehash: 9224888a38c86e35df9ad516c761fd7012824c15
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89084044"
---
# <a name="use-kubernetes-dashboard-to-monitor-your-azure-stack-edge-gpu-device"></a>Pomocí řídicího panelu Kubernetes monitorujte zařízení GPU Azure Stack Edge.

Tento článek popisuje, jak získat přístup k řídicímu panelu Kubernetes Edge a použít ho k monitorování vašeho zařízení GPU Azure Stack. Chcete-li monitorovat své zařízení, můžete použít grafy v Azure Portal, zobrazit řídicí panel Kubernetes nebo spustit `kubectl` příkazy prostřednictvím rozhraní PowerShell zařízení. 

Tento článek se zaměřuje jenom na úlohy monitorování, které se dají provádět na řídicím panelu Kubernetes.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Přístup k řídicímu panelu Kubernetes na zařízení
> * Zobrazit moduly nasazené na zařízení
> * Získat IP adresu pro aplikace nasazené na zařízení
> * Zobrazit protokoly kontejnerů pro moduly nasazené v zařízení


## <a name="about-kubernetes-dashboard"></a>O řídicím panelu Kubernetes

Řídicí panel Kubernetes je webové uživatelské rozhraní, které můžete použít k řešení problémů vašich kontejnerových aplikací. Řídicí panel Kubernetes je alternativa založená na uživatelském rozhraní k `kubectl` příkazovému řádku Kubernetes. 

Na Azure Stack hraničním zařízení můžete pomocí řídicího panelu Kubernetes v režimu jen pro čtení získat přehled o aplikacích spuštěných v zařízení Azure Stack Edge, zobrazit stav prostředků clusteru Kubernetes a zobrazit všechny chyby, ke kterým došlo v zařízení.

## <a name="access-dashboard"></a>Přístup k řídicímu panelu

Řídicí panel Kubernetes je jen pro čtení a běží na hlavním uzlu Kubernetes na portu 31000. Pro přístup k řídicímu panelu použijte následující postup: 

1. V místním uživatelském rozhraní zařízení, klikněte na **zařízení** a pak na **koncové body zařízení**. Výběrem adresy URL řídicího panelu Kubernetes otevřete řídicí panel v prohlížeči.

    ![Adresa URL řídicího panelu Kubernetes na stránce zařízení v místním uživatelském rozhraní](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-url-local-ui-1.png)

1. Na **přihlašovací stránce řídicího panelu Kubernetes** vyberte **token**. 
1. Zadejte token. 
    1. K získání tokenu se [připojte přes rozhraní PowerShell vašeho zařízení](azure-stack-edge-gpu-connect-powershell-interface.md).
    1. Spusťte příkaz:  `Get-HcsKubernetesDashboardToken`
    
    1. Na příkazovém řádku zkopírujte řetězec tokenu, který vám byl nabídnut. Zde je ukázkový výstup:
        
        ```powershell
        [10.100.10.10]: PS>Get-HcsKubernetesDashboardToken
        eyJhbGciOiJSUzI1NiIsImtpZCI6IkpFTEtBYTMyZ0Ezb01OYTVFSnVaUV85OWtLdXNETTZQR0k0UlFybGdReFUifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZC10b2tlbi03czZ6ayIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjU3NzY3ZDAzLTJlYWUtNDlkMi1hNDEyLTNkOTU3MDFiMThiMyIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlcm5ldGVzLWRhc2hib2FyZDprdWJlcm5ldGVzLWRhc2hib2FyZCJ9.UgNrpVYVJBEaWxFlljuENUQQmzFXMYG2VsJUIYFdp2AO20zX0k5dRvwcCpeGlqSKb9MyYjG0c6RmT9uCOZk-vAwt7btszQLD7KPCwh_nn_NiIyO8ApgGRYZP8NuP8CBTX3tl_hpwfHtZ0ksbuKAduIL-0uPF0rG5wgLk9cTEw6fKSc2UZW6bIzhNSp_uSiP6MexOS6OftF9JFZejkIGd33dSp-k-tgFlm2Zy96sdFJC0q-XsH7jygiVnfxA9XMs5wqW26LkCh0rfO2WI3C1XFK-4TpufRZLJHo5WPlu-Tnsxa8xmtk2jQ3us-sXcBRrvhPNPrNKkbqc9hbjmWfGD0Q
        [10.100.10.10]: PS>
        ```
        
1. Vyberte **Sign in** (Přihlásit se).

    ![Přihlaste se k řídicímu panelu Kubernetes](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-sign-in-1.png)

6. Nyní můžete zobrazit řídicí panel Kubernetes pro zařízení Azure Stack Edge v režimu jen pro čtení.

    ![Hlavní stránka řídicího panelu Kubernetes](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-dashboard-main-page-1.png)

## <a name="view-module-status"></a>Zobrazit stav modulu

Výpočetní moduly jsou kontejnery, které mají implementaci obchodní logiky. Řídicí panel můžete použít k ověření, jestli se výpočetní modul na zařízení Azure Stack Edge úspěšně nasadil.

Chcete-li zobrazit stav modulu, postupujte podle následujících kroků na řídicím panelu:

1. V levém podokně řídicího panelu přejít na **obor názvů**. Filtrovat podle oboru názvů, kde se zobrazují IoT Edge moduly, v tomto případě **iotedge**.
1. V levém podokně přejdete na **úlohy > nasazení**.
1. V pravém podokně se zobrazí všechny moduly nasazené na vašem zařízení. V tomto případě byl modul GettingStartedWithGPU nasazený na Azure Stack hraničních zařízeních. Můžete vidět, že byl modul nasazen.

    ![Zobrazit nasazení modulu](./media/azure-stack-edge-gpu-monitor-kubernetes-dashboard/kubernetes-view-module-deployment-1.png)

 
## <a name="get-ip-address-for-services-or-modules"></a>Získat IP adresu pro služby nebo moduly

Řídicí panel můžete použít k získání IP adres služeb nebo modulů, které chcete zveřejnit mimo cluster Kubernetes. 

Rozsah IP adres pro tyto externí služby přiřadíte prostřednictvím místního webového uživatelského rozhraní zařízení na stránce **Nastavení výpočetního sítě** . Po nasazení IoT Edge moduly můžete chtít získat IP adresu přiřazenou konkrétnímu modulu nebo službě. 

Chcete-li získat IP adresu, postupujte podle následujících kroků na řídicím panelu:

1. V levém podokně řídicího panelu přejít na **obor názvů**. Filtrovat podle oboru názvů, ve kterém je nainstalovaná externí služba, v tomto případě **iotedge**.
1. V levém podokně přejdete na **zjišťování a vyrovnávání zatížení > služby**.
1. V pravém podokně se zobrazí všechny služby, které jsou spuštěné v `iotedge` oboru názvů na vašem zařízení Azure Stack Edge.

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
    

## <a name="next-steps"></a>Další kroky

Naučte se řešit problémy s Kubernetes <!--insert link-->.
