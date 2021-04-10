---
title: Kurz konfigurace certifikátů pro zařízení Azure Stack Edge pro s grafickým procesorem v Azure Portal | Microsoft Docs
description: Kurz nasazení Azure Stack Edge pro s grafickým procesorem vám dává pokyn ke konfiguraci certifikátů na fyzickém zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: alkohli
ms.openlocfilehash: 8b1203693b67bb1a8b9699b84dd3a437027e4c3d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055310"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-with-gpu"></a>Kurz: Konfigurace certifikátů pro Azure Stack Edge pro pomocí GPU

V tomto kurzu se dozvíte, jak můžete nakonfigurovat certifikáty pro zařízení Azure Stack Edge pro s připojením GPU pomocí místního webového uživatelského rozhraní.

Doba potřebná pro tento krok se může lišit v závislosti na konkrétní zvolené možnosti a způsobu, jakým se ve vašem prostředí vytváří tok certifikátů.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Požadavky
> * Konfigurace certifikátů pro fyzické zařízení

## <a name="prerequisites"></a>Požadavky

Než nakonfigurujete a nastavíte Azure Stack Edge pro zařízení s grafickým procesorem, ujistěte se, že:

* Nainstalovali jste fyzické zařízení, jak je popsáno v části [instalace Azure Stack Edge pro](azure-stack-edge-gpu-deploy-install.md).
* Pokud máte v plánu používat vlastní certifikáty:
    - Vaše certifikáty by měly být připravené v příslušném formátu včetně certifikátu podpisového řetězce. Podrobnosti o certifikátu najdete na webu [Správa certifikátů](azure-stack-edge-gpu-manage-certificates.md) .

<!--    - If your device is deployed in Azure Government or Azure Government Secret or Azure Government top secret cloud and not deployed in Azure public cloud, a signing chain certificate is required before you can activate your device. 
    For details on certificate, go to [Manage certificates](azure-stack-edge-gpu-manage-certificates.md).-->


## <a name="configure-certificates-for-device"></a>Konfigurace certifikátů pro zařízení

1. Na stránce **certifikáty** nakonfigurujete své certifikáty. V závislosti na tom, jestli jste změnili název zařízení nebo doménu DNS na stránce **zařízení** , můžete pro svoje certifikáty zvolit jednu z následujících možností.

    - Pokud jste v předchozím kroku nezměnili název zařízení nebo doménu DNS a nechcete použít vlastní certifikáty, můžete tento krok přeskočit a přejít k dalšímu kroku. Zařízení automaticky vygenerovalo certifikáty podepsané svým držitelem, které začínají na. 

        ![Stránka místní webové uživatelské rozhraní "certifikáty"](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - Pokud jste změnili název zařízení nebo doménu DNS, zobrazí se stav certifikátů jako **neplatných**. 

        ![Místní uživatelské rozhraní webu "certifikáty" – Stránka 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)    

        Vyberte certifikát pro zobrazení podrobností o stavu.

        ![Místní uživatelské rozhraní webu "certifikáty" – Stránka 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)  

        Důvodem je to, že certifikáty neodrážejí aktualizovaný název zařízení a doménu DNS (které se používají v názvu subjektu a alternativním předmětu). Pokud chcete zařízení úspěšně aktivovat, vyberte jednu z následujících možností: 
    
        - **Vygenerujte všechny certifikáty zařízení**. Tyto certifikáty zařízení by se měly používat jenom k testování a nepoužívají se u produkčních úloh. Další informace najdete [v poznámkách k vygenerování certifikátů zařízení na Azure Stack Edge pro](#generate-device-certificates).

        - **Přineste si vlastní certifikáty**. Můžete přenést vlastní podepsané certifikáty koncových bodů a odpovídající podpisové řetězce. Nejprve přidejte podpisový řetězec a potom nahrajte certifikáty koncového bodu. **Pro produkční úlohy doporučujeme vždycky přenášet vlastní certifikáty.** Pokud potřebujete další informace, přečtěte si, jak [převést vlastní certifikáty na zařízení Azure Stack Edge pro](#bring-your-own-certificates).
    
        - Můžete přenést některé vlastní certifikáty a vygenerovat některé certifikáty zařízení. Možnost **generovat certifikáty** bude znovu generovat jenom certifikáty zařízení.

    - Pokud jste změnili název zařízení nebo doménu DNS a negenerujete certifikáty nebo nepřinesete vlastní certifikáty, **Aktivace se zablokuje**.


### <a name="generate-device-certificates"></a>Generování certifikátů zařízení

Pomocí těchto kroků vygenerujte certifikáty zařízení.

Pomocí těchto kroků znovu vygenerujte a stáhněte certifikáty zařízení Azure Stack Edge pro:

1. V místním uživatelském rozhraní zařízení, přejít na **konfigurační > certifikáty**. Vyberte možnost **generovat certifikáty**.

    ![Vygenerovat a stáhnout certifikát 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. V části **generovat certifikáty zařízení** vyberte **Generovat**. 

    ![Vygenerovat a stáhnout certifikát 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    Certifikáty zařízení se teď generují a aplikují. Generování a použití certifikátů trvá několik minut.
    
    > [!IMPORTANT]
    > I když probíhá operace generování certifikátů, nepřineste si vlastní certifikáty a pokuste se je přidat prostřednictvím možnosti **+ Přidat certifikát** .

    Po úspěšném dokončení operace budete upozorněni. **Aby nedocházelo k potenciálním problémům s mezipamětí, restartujte prohlížeč.**
    
    ![Vygenerovat a stáhnout certifikát 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. Po vygenerování certifikátů: 

    - Stav všech certifikátů je zobrazen jako **platný**. 

        ![Vygenerujte a Stáhněte certifikát 5.](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

    - Můžete vybrat konkrétní název certifikátu a zobrazit podrobnosti o certifikátu. 

        ![Vygenerovat a stáhnout certifikát 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6a.png)

    - Sloupec pro **stažení** je nyní vyplněn. Tento sloupec obsahuje odkazy na stažení znovu vygenerovaných certifikátů. 

        ![Vygenerovat a stáhnout certifikát 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6b.png)


4. Vyberte odkaz pro stažení certifikátu a po zobrazení výzvy certifikát uložte. 

    ![Vygenerovat a stáhnout certifikát 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. Tento postup opakujte pro všechny certifikáty, které chcete stáhnout. 
    
    ![Vygenerovat a stáhnout certifikát 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    Certifikáty vygenerované zařízením se ukládají jako certifikáty DER s následujícím formátem názvu: 

    `<Device name>_<Endpoint name>.cer`. Tyto certifikáty obsahují veřejný klíč pro odpovídající certifikáty nainstalované v zařízení. 

Tyto certifikáty budete muset nainstalovat v klientském systému, který používáte pro přístup k koncovým bodům v zařízení pomocného mechanismu. Tyto certifikáty naváží vztah důvěryhodnosti mezi klientem a zařízením.

Při importu a instalaci těchto certifikátů na klienta, který používáte pro přístup k zařízení, postupujte podle kroků v části [Import certifikátů v klientech, kteří přistupují k zařízení Azure Stack Edge pro](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device). 

Pokud používáte Průzkumník služby Azure Storage, budete muset nainstalovat certifikáty na klienta ve formátu PEM a budete muset převést certifikáty vygenerované zařízením na formát PEM. 

> [!IMPORTANT]
> - Odkaz ke stažení je dostupný jenom pro certifikáty vygenerované zařízením, a ne v případě, že přenesete vlastní certifikáty.
> - Můžete se rozhodnout, že budete mít k dispozici kombinaci certifikátů generovaných zařízením a máte vlastní certifikáty, pokud jsou splněné jiné požadavky na certifikáty. Další informace najdete v požadavcích na [certifikát](azure-stack-edge-gpu-certificate-requirements.md).
    

### <a name="bring-your-own-certificates"></a>Používání vlastních certifikátů

Pomocí těchto kroků můžete přidat vlastní certifikáty včetně podpisového řetězce.

1. Certifikát nahrajete tak, že na stránce **certifikát** vyberete **+ Přidat certifikát**.

    ![Místní uživatelské rozhraní webu "certifikáty" – Stránka 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. Nejdřív nahrajte podpisový řetěz a vyberte **ověřit & přidat**.

    ![Místní uživatelské rozhraní webu "certifikáty" – Stránka 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. Teď můžete nahrávat další certifikáty. Můžete například nahrát certifikáty koncového bodu služby Azure Resource Manager a BLOB Storage.

    ![Místní uživatelské rozhraní webu "certifikáty" – Stránka 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    Můžete také nahrát místní certifikát webového uživatelského rozhraní. Po nahrání tohoto certifikátu budete muset spustit prohlížeč a vymazat mezipaměť. Pak se budete muset připojit k místnímu webovému uživatelskému rozhraní zařízení.  

    ![Místní webové uživatelské rozhraní "Stránka 7" certifikátů](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    Můžete také nahrát certifikát uzlu.

    ![Místní webové uživatelské rozhraní "Stránka 8"](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    Kdykoli můžete vybrat certifikát a zobrazit podrobnosti, abyste se ujistili, že se shodují s certifikátem, který jste nahráli.

    ![Místní webové uživatelské rozhraní "Stránka 9" – certifikáty](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)

    Stránka certifikát by se měla aktualizovat tak, aby odrážela nově přidané certifikáty.

    ![Místní webové uživatelské rozhraní "Stránka 10" – certifikáty](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-7.png)  

    > [!NOTE]
    > S výjimkou veřejného cloudu Azure je nutné před aktivací pro všechny konfigurace cloudu (Azure Government nebo Azure Stack) uvést certifikáty podpisového řetězce.


Vaše zařízení je teď připravené k aktivaci. **Začněte výběrem< zpět**.


## <a name="next-steps"></a>Další kroky

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Požadavky
> * Konfigurace certifikátů pro fyzické zařízení

Informace o tom, jak aktivovat zařízení Azure Stack Edge pro, najdete tady:

> [!div class="nextstepaction"]
> [Aktivovat zařízení Azure Stack Edge pro](./azure-stack-edge-gpu-deploy-activate.md)
