---
title: Kurz konfigurace certifikátů pro zařízení Azure Stack Edge pomocí GPU v Azure Portal | Microsoft Docs
description: Kurz nasazení Azure Stack Edge vám dává pokyn ke konfiguraci certifikátů na fyzickém zařízení.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/10/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure certificates for Azure Stack Edge so I can use it to transfer data to Azure.
ms.openlocfilehash: fcaac4c1efe6507d4f69acd897e456400b1f61f1
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89087759"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-with-gpu"></a>Kurz: Konfigurace certifikátů pro Azure Stack Edge pomocí GPU

V tomto kurzu se dozvíte, jak můžete nakonfigurovat certifikáty pro zařízení Azure Stack Edge pomocí integrovaného GPU pomocí místního webového uživatelského rozhraní.

Doba potřebná pro tento krok se může lišit v závislosti na konkrétní možnosti zvolit způsob, jakým se ve vašem prostředí vytváří tok certifikátů. 

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Předpoklady
> * Konfigurace certifikátů pro fyzické zařízení

## <a name="prerequisites"></a>Předpoklady

Před konfigurací a nastavením Azure Stack hraničního zařízení pomocí GPU se ujistěte, že:

* Nainstalovali jste fyzické zařízení, jak je popsáno v části [instalace Azure Stack Edge](azure-stack-edge-gpu-deploy-install.md).
* Pokud máte v plánu používat vlastní certifikáty:
    - Vaše certifikáty by měly být připravené v příslušném formátu včetně certifikátu podpisového řetězce.
    - Pokud je vaše zařízení nasazené v Azure Government nebo Azure Government Secret nebo Azure Government cloudovém cloudu, které není nasazené ve veřejném cloudu Azure, je nutné před aktivací zařízení zadat certifikát podpisového řetězce. 
    Podrobnosti o certifikátu najdete na webu [Správa certifikátů](azure-stack-edge-j-series-manage-certificates.md).


## <a name="configure-certificates-for-device"></a>Konfigurace certifikátů pro zařízení


1. Na dlaždici **zabezpečení** vyberte **Konfigurovat** pro certifikáty. 

    ![Stránka místní webové uživatelské rozhraní "certifikáty"](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)

2. V závislosti na tom, jestli jste změnili název zařízení nebo doménu DNS na dlaždici **instalace zařízení** , můžete pro svoje certifikáty zvolit jednu z následujících možností.

    - Pokud jste v předchozím kroku nezměnili název zařízení nebo doménu DNS a nechcete použít vlastní certifikáty, můžete tento krok přeskočit a přejít k dalšímu kroku. Zařízení automaticky vygenerovalo certifikáty podepsané svým držitelem, které začínají na. 

        ![Stránka místní webové uživatelské rozhraní "certifikáty"](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - Pokud jste změnili název zařízení nebo doménu DNS, pak pokud chcete zařízení úspěšně aktivovat, můžete vybrat jednu z následujících možností: 
    
        - Vygenerujte všechny certifikáty zařízení. **Certifikáty zařízení by se měly používat jenom pro testování a nepoužívají se u produkčních úloh.** Další informace najdete [v poznámkách k vygenerování certifikátů zařízení na Azure Stack hraničních zařízeních](#generate-device-certificates).

        - Přineste si vlastní certifikáty. Můžete přenést vlastní podepsané certifikáty koncových bodů a odpovídající podpisové řetězce. Nejprve přidejte podpisový řetězec a potom nahrajte certifikáty koncového bodu. **Pro produkční úlohy doporučujeme vždycky přenášet vlastní certifikáty.** Další informace najdete v poznámkách k [převedení vlastních certifikátů na zařízení Azure Stack Edge](#bring-your-own-certificates).
    
        - Můžete přenést některé vlastní certifikáty a vygenerovat některé certifikáty zařízení. Možnost **generovat certifikáty** bude znovu generovat jenom certifikáty zařízení.

    - Pokud jste změnili název zařízení nebo doménu DNS a negenerujete certifikáty nebo nepřinesete vlastní certifikáty, aktivace se zablokuje.


### <a name="generate-device-certificates"></a>Generování certifikátů zařízení

Pomocí těchto kroků vygenerujte certifikáty zařízení.

Pomocí těchto kroků můžete znovu vygenerovat a stáhnout Azure Stackch certifikátů hraničních zařízení:

1. V místním uživatelském rozhraní zařízení, přejít na **konfigurační > certifikáty**. Vyberte možnost **generovat certifikáty**.

    ![Vygenerovat a stáhnout certifikát 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. V části **generovat certifikáty zařízení**vyberte **Generovat**.

    ![Vygenerovat a stáhnout certifikát 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    Certifikáty zařízení se teď generují a aplikují. 
    
    > [!IMPORTANT]
    > I když probíhá operace generování certifikátů, nepřineste si vlastní certifikáty a pokuste se je přidat prostřednictvím možnosti **+ Přidat certifikát** .

    Po úspěšném dokončení operace budete upozorněni. Aby nedocházelo k potenciálním problémům s mezipamětí, restartujte prohlížeč. 
    
    ![Vygenerovat a stáhnout certifikát 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. Na stránce **certifikáty** vidíte, že je nyní vyplněn sloupec **Stáhnout** a jsou k dispozici odkazy na stažení obnovených certifikátů. 

    ![Vygenerujte a Stáhněte certifikát 5.](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

4. Vyberte odkaz pro stažení certifikátu a po zobrazení výzvy certifikát uložte. 

    ![Vygenerovat a stáhnout certifikát 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. Tento postup opakujte pro všechny certifikáty, které chcete stáhnout. 
    
    ![Vygenerovat a stáhnout certifikát 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    Certifikáty vygenerované zařízením se ukládají jako certifikáty DER s následujícím formátem názvu: 

    `<Device name>_<Endpoint name>.cer`. Tyto certifikáty obsahují veřejný klíč pro odpovídající certifikáty nainstalované v zařízení. 

Tyto certifikáty budete muset nainstalovat v klientském systému, který používáte pro přístup k koncovým bodům v zařízení pomocného mechanismu. Tyto certifikáty naváží vztah důvěryhodnosti mezi klientem a zařízením.

Při importu a instalaci těchto certifikátů na klienta, který používáte pro přístup k zařízení, postupujte podle kroků v části [Import certifikátů v klientech, kteří přistupují k zařízení Azure Stack Edge](azure-stack-edge-j-series-manage-certificates.md#import-certificates-on-the-client-accessing-the-device). 

Pokud používáte Průzkumník služby Azure Storage, budete muset nainstalovat certifikáty na klienta ve formátu PEM a budete muset převést certifikáty vygenerované zařízením na formát PEM. 

> [!IMPORTANT]
> - Odkaz ke stažení je dostupný jenom pro certifikáty vygenerované zařízením, a ne v případě, že přenesete vlastní certifikáty.
> - Můžete se rozhodnout, že budete mít k dispozici kombinaci certifikátů generovaných zařízením a máte vlastní certifikáty, pokud jsou splněné jiné požadavky na certifikáty. Další informace najdete v požadavcích na [certifikát](azure-stack-edge-j-series-certificate-requirements.md).
    

### <a name="bring-your-own-certificates"></a>Přineste si vlastní certifikáty

Pomocí těchto kroků můžete přidat vlastní certifikáty včetně podpisového řetězce.

1. Certifikát nahrajete tak, že na stránce **certifikát** vyberete **+ Přidat certifikát**.

    ![Stránka místní webové uživatelské rozhraní "certifikáty"](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. Nejdřív nahrajte podpisový řetěz a vyberte **ověřit & přidat**.

    ![Stránka místní webové uživatelské rozhraní "certifikáty"](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. Teď můžete nahrávat další certifikáty. Můžete například nahrát certifikáty koncového bodu služby Azure Resource Manager a BLOB Storage.

    ![Stránka místní webové uživatelské rozhraní "certifikáty"](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    Můžete také nahrát místní certifikát webového uživatelského rozhraní. Po nahrání tohoto certifikátu budete muset spustit prohlížeč a vymazat mezipaměť. Pak se budete muset připojit k místnímu webovému uživatelskému rozhraní zařízení.  

    ![Stránka místní webové uživatelské rozhraní "certifikáty"](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    Můžete také nahrát certifikát uzlu.

    ![Stránka místní webové uživatelské rozhraní "certifikáty"](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    Stránka certifikát by se měla aktualizovat tak, aby odrážela nově přidané certifikáty.

    ![Stránka místní webové uživatelské rozhraní "certifikáty"](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)  

    > [!NOTE]
    > S výjimkou veřejného cloudu Azure je nutné před aktivací pro všechny konfigurace cloudu (Azure Government nebo Azure Stack) uvést certifikáty podpisového řetězce.


Vaše zařízení je teď připravené k aktivaci.


## <a name="next-steps"></a>Další kroky

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Předpoklady
> * Konfigurace certifikátů pro fyzické zařízení

Informace o tom, jak aktivovat Azure Stack hraniční zařízení, najdete v tématech:

> [!div class="nextstepaction"]
> [Aktivovat Azure Stack hraniční zařízení](./azure-stack-edge-gpu-deploy-activate.md)
