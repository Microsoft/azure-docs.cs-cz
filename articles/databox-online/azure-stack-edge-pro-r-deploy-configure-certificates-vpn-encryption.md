---
title: Kurz konfigurace certifikátů pro zařízení Azure Stack Edge pro R v Azure Portal | Microsoft Docs
description: Kurz nasazení Azure Stack Edge pro R vám dává pokyn ke konfiguraci certifikátů na fyzickém zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/19/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: abea192f209b968c6842e985620c067c93990687
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100548144"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-r"></a>Kurz: Konfigurace certifikátů pro Azure Stack Edge pro R

V tomto kurzu se dozvíte, jak můžete nakonfigurovat certifikáty pro zařízení Azure Stack Edge pro R pomocí místního webového uživatelského rozhraní.

Doba potřebná pro tento krok se může lišit v závislosti na konkrétní zvolené možnosti a způsobu, jakým se ve vašem prostředí vytváří tok certifikátů.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Požadavky
> * Konfigurace certifikátů pro fyzické zařízení
> * Konfigurace sítě VPN
> * Konfigurace šifrování v klidovém případě

## <a name="prerequisites"></a>Požadavky

Před konfigurací a nastavením Azure Stack hraničního zařízení pro R se ujistěte, že:

* Nainstalovali jste fyzické zařízení, jak je popsáno v části [instalace Azure Stack Edge pro R](azure-stack-edge-pro-r-deploy-install.md).
* Pokud máte v plánu používat vlastní certifikáty:
    - Vaše certifikáty by měly být připravené v příslušném formátu včetně certifikátu podpisového řetězce. Podrobnosti o certifikátu najdete na webu [Správa certifikátů](azure-stack-edge-gpu-manage-certificates.md) .



## <a name="configure-certificates-for-device"></a>Konfigurace certifikátů pro zařízení

1. Na stránce **certifikáty** nakonfigurujete své certifikáty. V závislosti na tom, jestli jste změnili název zařízení nebo doménu DNS na stránce **zařízení** , můžete pro svoje certifikáty zvolit jednu z následujících možností.

    - Pokud jste v předchozím kroku nezměnili název zařízení nebo doménu DNS, můžete tento krok přeskočit a přejít k dalšímu kroku. Zařízení automaticky vygenerovalo certifikáty podepsané svým držitelem, které začínají na. 

    - Pokud jste změnili název zařízení nebo doménu DNS, zobrazí se stav certifikátů jako **neplatných**. 

        ![Místní uživatelské rozhraní webu "certifikáty" – Stránka 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1.png)    

        Vyberte certifikát pro zobrazení podrobností o stavu.

        ![Místní uživatelské rozhraní webu "certifikáty" – Stránka 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/generate-certificate-1a.png)  

        Důvodem je to, že certifikáty neodrážejí aktualizovaný název zařízení a doménu DNS (které se používají v názvu subjektu a alternativním předmětu). K úspěšné aktivaci zařízení můžete použít vlastní podepsané certifikáty koncových bodů a odpovídající podpisové řetězce. Nejprve přidejte podpisový řetězec a potom nahrajte certifikáty koncového bodu. Pokud potřebujete další informace, přečtěte si, jak [převést vlastní certifikáty na zařízení Azure Stack Edge pro R](#bring-your-own-certificates).

    - Pokud jste změnili název zařízení nebo doménu DNS a nepřinesete si vlastní certifikáty, **Aktivace se zablokuje**.

    
#### <a name="bring-your-own-certificates"></a>Používání vlastních certifikátů

Pomocí těchto kroků můžete přidat vlastní certifikáty včetně podpisového řetězce.

1. Certifikát nahrajete tak, že na stránce **certifikát** vyberete **+ Přidat certifikát**.

    ![Místní uživatelské rozhraní webu "certifikáty" – Stránka 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)

2. Nejdřív nahrajte podpisový řetěz a vyberte **ověřit & přidat**.

    ![Místní uživatelské rozhraní webu "certifikáty" – Stránka 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-2.png)

3. Teď můžete nahrávat další certifikáty. Můžete například nahrát certifikáty koncového bodu služby Azure Resource Manager a BLOB Storage.

    ![Místní uživatelské rozhraní webu "certifikáty" – Stránka 6](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

    Můžete také nahrát místní certifikát webového uživatelského rozhraní. Po nahrání tohoto certifikátu budete muset spustit prohlížeč a vymazat mezipaměť. Pak se budete muset připojit k místnímu webovému uživatelskému rozhraní zařízení.  

    ![Místní webové uživatelské rozhraní "Stránka 7" certifikátů](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

    Můžete také nahrát certifikát uzlu.

    ![Místní webové uživatelské rozhraní "Stránka 8"](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    Nakonec můžete nahrát certifikát sítě VPN.

    ![Místní webové uživatelské rozhraní "Stránka 9" – certifikáty](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

    Kdykoli můžete vybrat certifikát a zobrazit podrobnosti, abyste se ujistili, že se shodují s certifikátem, který jste nahráli.

    Stránka certifikát by se měla aktualizovat tak, aby odrážela nově přidané certifikáty.

    ![Místní webové uživatelské rozhraní "Stránka 10" – certifikáty](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)  

    > [!NOTE]
    > S výjimkou veřejného cloudu Azure je nutné před aktivací pro všechny konfigurace cloudu (Azure Government nebo Azure Stack) uvést certifikáty podpisového řetězce.

1. **Začněte výběrem< zpět**.

## <a name="configure-vpn"></a>Konfigurace sítě VPN

1. Na dlaždici **zabezpečení** vyberte **Konfigurovat** pro síť VPN.

    ![Stránka místního webového uživatelského rozhraní "VPN"](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-1.png)  

    Abyste mohli nakonfigurovat síť VPN, musíte nejdřív zajistit, abyste měli jistotu, že budete mít veškerou potřebnou konfiguraci dokončenou v Azure. Podrobnosti najdete v tématu [Konfigurace požadavků](azure-stack-edge-placeholder.md) a [Konfigurace prostředků Azure pro VPN](azure-stack-edge-placeholder.md). Po dokončení můžete konfiguraci provést v místním uživatelském rozhraní.
    
    1. Na stránce VPN vyberte **Konfigurovat**.
    2. V okně **Konfigurace sítě VPN** :

    - Povolte **nastavení sítě VPN**.
    - Zadejte **sdílený tajný klíč sítě VPN**. Jedná se o sdílený klíč, který jste zadali při vytváření objektu připojení VPN Azure.
    - Zadejte **IP adresu brány VPN** . Toto je IP adresa brány místní sítě Azure.
    - V případě **skupiny PFS** vyberte **None (žádné**). 
    - V případě **skupiny DH** vyberte **skupina2**.
    - V případě **metody integrity protokolu IPSec** vyberte **SHA256**.
    - Pro **transformační konstanty IPseccipher** vyberte **GCMAES256**.
    - Pro **transformační konstanty ověřování IPsec** vyberte **GCMAES256**.
    - V případě **metody šifrování IKE** vyberte **AES256**.
    - Vyberte **Použít**.

        ![Konfigurace místního uživatelského rozhraní 2](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-2.png)

    3. Pokud chcete nahrát konfigurační soubor směrování sítě VPN, vyberte **nahrát**. 
    
        ![Konfigurace místního uživatelského rozhraní 3](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-3.png)
    
        - Přejděte do konfiguračního souboru *JSON* konfigurace sítě VPN, který jste stáhli v místním systému v předchozím kroku.
        - Vyberte oblast jako oblast Azure, která je přidružená k zařízení, virtuální síti a branám.
        - Vyberte **Použít**.
    
            ![Konfigurace místního uživatelského rozhraní 4](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-4.png)
    
    4. Chcete-li přidat trasy specifické pro klienta, nakonfigurujte rozsahy IP adres, pro které bude k dispozici pouze pomocí sítě VPN. 
    
        - V části **rozsahy IP adres, ke kterým má být přístup jenom přes VPN**, vyberte **Konfigurovat**.
        - Zadejte platný rozsah IPv4 a vyberte **Přidat**. Opakováním kroků přidejte další rozsahy.
        - Vyberte **Použít**.
    
            ![Konfigurace místního uživatelského rozhraní 5](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/configure-vpn-local-ui-5.png)

1. **Začněte výběrem< zpět**.

## <a name="configure-encryption-at-rest"></a>Konfigurace šifrování v klidovém případě

1. Na dlaždici **zabezpečení** vyberte **Konfigurovat** pro šifrování v klidovém umístění. Toto je požadované nastavení a dokud se tato možnost úspěšně nenakonfigurovala, nebudete moct zařízení aktivovat. 

    Po zobrazení bitové kopie zařízení v továrně je povolené šifrování BitLockeru na úrovni svazku. Po přijetí zařízení je potřeba nakonfigurovat šifrování v klidovém případě. Fond úložiště a svazky se znovu vytvoří a můžete zadat klíče BitLockeru pro povolení šifrování v klidovém prostředí, takže vytvoříte druhou vrstvu šifrování pro vaše data v klidovém umístění.

1. V podokně **šifrování –** místní zadejte 32 znak dlouhý 64 základ kódování. Jedná se o jednorázovou konfiguraci a tento klíč se používá k ochraně skutečného šifrovacího klíče. Můžete zvolit automatické vygenerování tohoto klíče nebo ho zadat.

    ![Místní webové uživatelské rozhraní v podokně "šifrování v klidovém prostředí"](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-key-1.png)

    Klíč se uloží do souboru klíče na stránce s **informacemi o cloudu** po aktivaci zařízení.

1. Vyberte **Použít**. Tato operace trvá několik minut a na dlaždici **zabezpečení** se zobrazuje stav operace.

    ![Stránka místního webového uživatelského rozhraní na stránce šifrování na klidovém umístění](./media/azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-status-1.png)

1. Až se stav zobrazí jako **dokončený**, vyberte **< zpět a** začněte.

Vaše zařízení je teď připravené k aktivaci. 


## <a name="next-steps"></a>Další kroky

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Požadavky
> * Konfigurace certifikátů pro fyzické zařízení
> * Konfigurace sítě VPN
> * Konfigurace šifrování v klidovém případě

Informace o tom, jak aktivovat Azure Stack hraniční zařízení pro R, najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Aktivovat zařízení Azure Stack Edge pro R](./azure-stack-edge-pro-r-deploy-activate.md)
