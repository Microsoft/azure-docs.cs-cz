---
title: Kurz konfigurace certifikátů pro zařízení Azure Stack Edge Mini R v Azure Portal | Microsoft Docs
description: Kurz pro nasazení Azure Stack hraničních zařízení na Mini R vám dává pokyn ke konfiguraci certifikátů na fyzickém zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: alkohli
ms.openlocfilehash: 42863c942314d3cb76c7536edc79b08e132f8fa2
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106062994"
---
# <a name="tutorial-configure-certificates-vpn-encryption-for-your-azure-stack-edge-mini-r"></a>Kurz: Konfigurace certifikátů, sítě VPN, šifrování pro Azure Stack hraničních Mini R

V tomto kurzu se dozvíte, jak můžete pomocí místního webového uživatelského rozhraní nakonfigurovat certifikáty, síť VPN a místní šifrování pro Azure Stack hraničních zařízení v jazyce R.

Doba potřebná pro tento krok se může lišit v závislosti na konkrétní zvolené možnosti a způsobu, jakým se ve vašem prostředí vytváří tok certifikátů.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Požadavky
> * Konfigurace certifikátů pro fyzické zařízení
> * Konfigurace sítě VPN
> * Konfigurace šifrování v klidovém umístění

## <a name="prerequisites"></a>Požadavky

Předtím, než nakonfigurujete a nastavíte Azure Stack hraničních zařízení v jazyce R, ujistěte se, že:

* Nainstalovali jste fyzické zařízení, jak je podrobně popsáno v části [Install Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-install.md).

* Pokud máte v plánu používat vlastní certifikáty:
    - Vaše certifikáty by měly být připravené v příslušném formátu včetně certifikátu podpisového řetězce. Podrobnosti o certifikátu najdete na webu [Správa certifikátů](azure-stack-edge-gpu-manage-certificates.md) .

    - Pokud je vaše zařízení nasazené v Azure Government nebo Azure Government Secret nebo Azure Government cloudovém cloudu, které není nasazené ve veřejném cloudu Azure, je nutné před aktivací zařízení zadat certifikát podpisového řetězce. 
    Podrobnosti o certifikátu najdete na webu [Správa certifikátů](azure-stack-edge-gpu-manage-certificates.md).


## <a name="configure-certificates-for-device"></a>Konfigurace certifikátů pro zařízení

1. Na stránce **certifikáty** nakonfigurujete své certifikáty. V závislosti na tom, jestli jste změnili název zařízení nebo doménu DNS na stránce **zařízení** , můžete pro svoje certifikáty zvolit jednu z následujících možností.

    - Pokud jste v předchozím kroku nezměnili výchozí název zařízení nebo výchozí doménu DNS a nechcete používat vlastní certifikáty, můžete tento krok přeskočit a přejít k dalšímu kroku. Zařízení automaticky vygenerovalo certifikáty podepsané svým držitelem, které začínají na. 

       <!-- ![Local web UI "Certificates" page](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-1.png)-->

    - Pokud jste změnili název zařízení nebo doménu DNS, zobrazí se stav certifikátů jako **neplatných**. 

        ![Místní uživatelské rozhraní webu "certifikáty" – Stránka 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/certificate-2.png)    

        Vyberte certifikát pro zobrazení podrobností o stavu.

        <!--![Local web UI "Certificates" page 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)-->  

        Stav certifikátu není **platný** , protože certifikáty neodrážejí aktualizovaný název zařízení a doménu DNS (které se používají v názvu subjektu a alternativním předmětu). K úspěšné aktivaci zařízení můžete použít vlastní podepsané certifikáty koncových bodů a odpovídající podpisové řetězce. Nejprve přidejte podpisový řetězec a potom nahrajte certifikáty koncového bodu. Další informace najdete v poznámkách k [převedení vlastních certifikátů na Azure Stack hraničních zařízení v R](#bring-your-own-certificates).


    - Pokud jste změnili název zařízení nebo doménu DNS a nepřinesete si vlastní certifikáty, **Aktivace se zablokuje**.


#### <a name="bring-your-own-certificates"></a>Používání vlastních certifikátů

Podpisový řetězec jste už přidali v předchozím kroku na tomto zařízení. Nyní můžete odeslat certifikáty koncového bodu, certifikát uzlu, certifikát místního uživatelského rozhraní a certifikát sítě VPN. Pomocí těchto kroků můžete přidat vlastní certifikáty.

1. Certifikát nahrajete tak, že na stránce **certifikát** vyberete **+ Přidat certifikát**.

    [![Místní uživatelské rozhraní webu "certifikáty" – Stránka 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-1.png#lightbox)


1. Můžete nahrávat další certifikáty. Můžete například nahrát certifikáty koncového bodu služby Azure Resource Manager a BLOB Storage.

    ![Místní uživatelské rozhraní webu "certifikáty" – Stránka 6](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-3.png)

1. Můžete také nahrát místní certifikát webového uživatelského rozhraní. Po nahrání tohoto certifikátu budete muset spustit prohlížeč a vymazat mezipaměť. Pak se budete muset připojit k místnímu webovému uživatelskému rozhraní zařízení.  

    ![Místní webové uživatelské rozhraní "Stránka 7" certifikátů](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-4.png)

1. Můžete také nahrát certifikát uzlu.


    ![Místní webové uživatelské rozhraní "Stránka 8"](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-5.png)

1. Nakonec můžete nahrát certifikát sítě VPN.
        
    ![Stránka místní webové uživatelské rozhraní "certifikáty"](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-6.png)

1. Kdykoli můžete vybrat certifikát a zobrazit podrobnosti, abyste se ujistili, že se shodují s certifikátem, který jste nahráli.

    [![Místní webové uživatelské rozhraní "Stránka 9" – certifikáty](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-7.png#lightbox)

    Stránka certifikát by se měla aktualizovat tak, aby odrážela nově přidané certifikáty.

    [![Místní webové uživatelské rozhraní "Stránka 10" – certifikáty](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png)](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/add-certificate-8.png#lightbox)  

    > [!NOTE]
    > S výjimkou veřejného cloudu Azure je nutné před aktivací pro všechny konfigurace cloudu (Azure Government nebo centra Azure Stack) uvést certifikáty podpisového řetězce.


## <a name="configure-vpn"></a>Konfigurace sítě VPN

1. Na dlaždici **zabezpečení** vyberte **Konfigurovat** pro síť VPN. 

    Abyste mohli nakonfigurovat síť VPN, musíte nejdřív zajistit, abyste měli jistotu, že budete mít veškerou potřebnou konfiguraci dokončenou v Azure. Podrobnosti najdete v tématu [Konfigurace sítě VPN prostřednictvím PowerShellu pro Azure Stack hraničních zařízení v jazyce R](azure-stack-edge-placeholder.md). Po dokončení můžete konfiguraci provést v místním uživatelském rozhraní.
    
    1. Na stránce VPN vyberte **Konfigurovat**.
        ![Konfigurace místního uživatelského rozhraní VPN 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-1.png)

    1. V okně **Konfigurace sítě VPN** :

        1. Zadejte telefonní seznam jako vstup.
        2. Zadejte jako vstup soubor JSON rozsahu IP adres datového centra Azure. Stáhnout tento soubor z: [https://www.microsoft.com/download/details.aspx?id=56519](https://www.microsoft.com/download/details.aspx?id=56519) .
        3. Jako oblast vyberte **eastus** .
        4. Vyberte **Použít**.

        ![Konfigurace místního uživatelského rozhraní VPN 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-2.png)
    
    1. Nakonfigurujte rozsahy IP adres, pro které bude k dispozici pouze pomocí sítě VPN. 
    
        - V části **rozsahy IP adres, ke kterým má být přístup jenom přes VPN**, vyberte **Konfigurovat**.
        - Zadejte rozsah adres IPv4, který jste si zvolili pro Azure Virtual Network.
        - Vyberte **Použít**.
    
        ![Konfigurace místního uživatelského rozhraní VPN 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/configure-vpn-3.png)

Vaše zařízení je teď připravené k zašifrování. Konfigurovat šifrování v klidovém umístění.


## <a name="enable-encryption"></a>Povolit šifrování

1. Na dlaždici **zabezpečení** vyberte **Konfigurovat** pro šifrování v klidovém umístění. Toto je požadované nastavení a dokud se tato možnost úspěšně nenakonfigurovala, nebudete moct zařízení aktivovat. 

    ![Místní webové uživatelské rozhraní "šifrování v klidovém prostředí" – Stránka 1](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-1.png)

    Po zobrazení bitové kopie zařízení v továrně je povolené šifrování BitLockeru na úrovni svazku. Po přijetí zařízení je potřeba nakonfigurovat šifrování v klidovém případě. Fond úložiště a svazky se znovu vytvoří a můžete zadat klíče BitLockeru pro povolení šifrování v klidovém prostředí, takže vytvoříte druhou vrstvu šifrování pro vaše data v klidovém umístění.

1. V podokně **šifrování v klidovém režimu** zadejte 32 znaků (32bitový AES-256), který je zakódovaný jako základní-64. Jedná se o jednorázovou konfiguraci a tento klíč se používá k ochraně skutečného šifrovacího klíče. 

    ![Místní webové uživatelské rozhraní "šifrování v klidovém prostředí" na stránce 2](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-2.png)

    Můžete také zvolit automatické vygenerování tohoto klíče.

    ![Místní webové uživatelské rozhraní "šifrování v klidovém prostředí" – Stránka 3](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. Vyberte **Použít**. Tato operace trvá několik minut a na dlaždici **zabezpečení** se zobrazuje stav operace.

    ![Místní webové uživatelské rozhraní "šifrování v klidovém prostředí" – Stránka 4](./media/azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption/encryption-at-rest-3.png)

1. Až se stav zobrazí jako **dokončený**, vraťte se na **začátek**.

Vaše zařízení je teď připravené k aktivaci.

## <a name="next-steps"></a>Další kroky

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Požadavky
> * Konfigurace certifikátů pro fyzické zařízení
> * Konfigurace sítě VPN
> * Konfigurace šifrování v klidovém umístění

Informace o tom, jak aktivovat Azure Stack hraničních zařízení v R, najdete tady:

> [!div class="nextstepaction"]
> [Aktivovat zařízení s Azure Stackovým malým okrajem R](./azure-stack-edge-mini-r-deploy-activate.md)
