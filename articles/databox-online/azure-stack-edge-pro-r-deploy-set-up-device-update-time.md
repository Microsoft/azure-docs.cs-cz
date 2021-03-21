---
title: Kurz nastavení času, zařízení, aktualizace nastavení pro zařízení Azure Stack Edge pro R v Azure Portal
description: Kurz nasazení Azure Stack Edge pro R vám dává pokyn k nastavení zařízení, aktualizace a času nastavení pro vaše fyzické zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/18/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 095eb8c20830b9bfe27e26453567d3c25230f2ef
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100548110"
---
# <a name="tutorial-configure-the-device-settings-for-azure-stack-edge-pro-r"></a>Kurz: Konfigurace nastavení zařízení pro Azure Stack Edge pro R

V tomto kurzu se dozvíte, jak nakonfigurovat nastavení týkající se zařízení pro zařízení Azure Stack Edge pro R. Pomocí místního webového uživatelského rozhraní můžete nastavit název zařízení, server aktualizací a časový server.

Dokončení nastavení zařízení může trvat přibližně 5-7 minut.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Předpoklady
> * Konfigurace nastavení zařízení
> * Konfigurace aktualizace 
> * Konfigurovat čas

## <a name="prerequisites"></a>Předpoklady

Než nakonfigurujete nastavení související se zařízením na zařízení Azure Stack Edge pro R, ujistěte se, že:

* Pro vaše fyzické zařízení:

    - Nainstalovali jste fyzické zařízení, jak je popsáno v části [instalace Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-install.md).
    - V zařízení jste nakonfigurovali síť a povolili a nakonfigurovali výpočetní síť, jak je podrobně popsáno v tomto [kurzu: konfigurace sítě pro Azure Stack Edge pro R ](azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md).


## <a name="configure-device-settings"></a>Konfigurace nastavení zařízení

Pomocí těchto kroků můžete nakonfigurovat nastavení týkající se zařízení:

1. Na stránce **zařízení** proveďte následující kroky:

    1. Zadejte popisný název zařízení. Popisný název musí být v rozmezí od 1 do 13 znaků a může obsahovat písmena, číslice a spojovníky.

    2. Zadejte **doménu DNS** pro vaše zařízení. Tato doména slouží k nastavení zařízení jako souborového serveru.

    3. Pokud chcete ověřit a použít nakonfigurovaná nastavení zařízení, vyberte **použít**.

        ![Místní webové uživatelské rozhraní "zařízení" – Stránka 1](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/device-2.png)

        Pokud jste změnili název zařízení a doménu DNS, nebudou automaticky generované certifikáty podepsané svým držitelem v zařízení fungovat. Při konfiguraci certifikátů je potřeba vybrat jednu z následujících možností: 
        
        - Vygenerujte a stáhněte certifikáty zařízení. 
        - Přineste si vlastní certifikáty pro zařízení včetně podpisového řetězce.
    

        ![Místní webové uživatelské rozhraní "zařízení" – Stránka 2](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/device-3.png)

    4. Když se změní název zařízení a doména DNS, vytvoří se koncový bod SMB.  

    5. Po použití nastavení vyberte **Další: server aktualizací**.

        ![Místní webové uživatelské rozhraní "stránka" zařízení 3](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/device-4.png)

## <a name="configure-update"></a>Konfigurace aktualizace

1. Na stránce **aktualizace** teď můžete nakonfigurovat umístění, ze kterého se mají stahovat aktualizace pro vaše zařízení.  

    - Aktualizace můžete získat přímo ze **serveru Microsoft Update**.

        ![Místní webové uživatelské rozhraní – stránka aktualizace serveru](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/update-2.png)

        Můžete také zvolit nasazení aktualizací ze **služby Windows Server Update Services** (WSUS). Zadejte cestu k serveru WSUS.
        
        ![Místní webové uživatelské rozhraní stránky aktualizace serveru 2](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/update-3.png)

        > [!NOTE] 
        > Pokud je nakonfigurován samostatný web Windows Update Server a pokud se rozhodnete připojit přes *protokol HTTPS* (namísto *http*), jsou vyžadovány podpisové řetězy, které jsou vyžadovány pro připojení k serveru aktualizací. Informace o tom, jak vytvořit a nahrát certifikáty, najdete v tématu [Správa certifikátů](azure-stack-edge-gpu-manage-certificates.md).         
        > Pro práci v odpojeném režimu, jako je například vaše Azure Stack hraničních zařízení vrstev pro modulární datové centrum, povolte možnost WSUS. Při aktivaci zařízení vyhledává aktualizace a pokud server není nastavený, aktivace se nezdaří. 


2. Vyberte **Použít**.
3. Po nakonfigurování serveru aktualizací vyberte **Další: čas**.
    

## <a name="configure-time"></a>Konfigurovat čas

Pomocí těchto kroků nakonfigurujete nastavení času na zařízení. 

> [!IMPORTANT]
> I když je nastavení času volitelné, důrazně doporučujeme nakonfigurovat primární a sekundární server NTP v místní síti pro vaše zařízení. Pokud místní server není dostupný, můžete nakonfigurovat veřejné servery NTP.

Jsou vyžadovány servery NTP, protože vaše zařízení musí synchronizovat čas, aby se mohl ověřit u vašich poskytovatelů cloudových služeb.

1. Na stránce **čas** můžete vybrat časové pásmo a primární a sekundární servery NTP pro vaše zařízení.  
    
    1. V rozevíracím seznamu **časové pásmo** vyberte časové pásmo, které odpovídá geografickému umístění, ve kterém se zařízení nasazuje.
        Výchozím časovým pásmem pro vaše zařízení je PST. Toto časové pásmo bude zařízení používat pro všechny naplánované operace.

    2. V poli **primární server NTP** zadejte primární server pro vaše zařízení nebo přijměte výchozí hodnotu Time.Windows.com.  
        Ujistěte se, že vaše síť umožňuje předávat data NTP z vašeho datacentra na Internet.

    3. Případně můžete v poli **sekundární server NTP** zadat sekundární server pro vaše zařízení.

    4. Chcete-li ověřit a použít nakonfigurované nastavení času, vyberte **použít**.

        ![Stránka čas místního webového uživatelského rozhraní](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/time-2.png)

2. Po použití nastavení vyberte **Další: certifikáty**.


## <a name="next-steps"></a>Další kroky

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Předpoklady
> * Konfigurace nastavení zařízení
> * Konfigurace aktualizace 
> * Konfigurovat čas

Informace o tom, jak nakonfigurovat certifikáty pro zařízení Azure Stack Edge pro R, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Konfigurace certifikátů](./azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)
