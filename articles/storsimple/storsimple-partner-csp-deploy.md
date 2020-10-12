---
title: Nasazení StorSimple Virtual Array pro program poskytovatele Cloud Solution Provider
description: Zjistěte, jak může partner CSP přidat zákazníka nebo nové předplatné stávajícímu zákazníkovi a pak vytvořit službu pro nasazení StorSimple virtuálního pole v CSP.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/08/2017
ms.author: alkohli
ms.openlocfilehash: dc0cf718ee51e23fb749bdf57d5344977de009d8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88182217"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>Nasazení StorSimple Virtual Array pro program poskytovatele Cloud Solution Provider

## <a name="overview"></a>Přehled

StorSimple Virtual Array může být nasazeno partnery poskytovatele Cloud Solution Provider (CSP) pro své zákazníky. Partner CSP může vytvořit službu StorSimple Device Manager. Tato služba se pak dá použít k nasazení a správě virtuálního pole StorSimple a přidružených sdílených složek, svazků a záloh.

Tento článek popisuje, jak může partner CSP přidat zákazníka nebo nové předplatné stávajícímu zákazníkovi a pak vytvořit službu pro nasazení StorSimple virtuálního pole v CSP.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že:

- Jste zaregistrovaní v rámci programu CSP.
- Máte platné přihlašovací údaje [partnerského centra](https://partnercenter.microsoft.com/) . Přihlašovací údaje vám umožní přihlásit se k portálu pro partnery a přidat nové zákazníky, Hledat zákazníky nebo přejít na účet zákazníka z řídicího panelu partnera. CSP může v Azure Portal fungovat jako správce StorSimple jménem zákazníka.
                             
## <a name="add-a-customer"></a>Přidat zákazníka

Pokud přidáte zákazníka, vytvoří se předplatné automaticky. Chcete-li přidat zákazníka (a automaticky vytvořit odběr), proveďte následující kroky na portálu pro partnery.

1. Přejít do [partnerského centra](https://partnercenter.microsoft.com/) a přihlaste se pomocí svých přihlašovacích údajů CSP. Klikněte na **řídicí panel**.

     ![Řídicí panel v partnerském centru](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. V levém podokně klikněte na **zákazníci**. V pravém podokně klikněte na **Přidat zákazníky**. Zadejte podrobnosti o zákazníkovi. Klikněte na **Další: předplatná** pro vytvoření zákaznického předplatného.

    ![Přidat zákazníka](./media/storsimple-partner-csp-deploy/image2.png)

3.  Vyberte **Microsoft Azure** nabídka. Posuňte se do dolní části stránky a klikněte na tlačítko **zkontrolovat**.

    ![Kontrola informací o předplatném](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Zkontrolujte informace a klikněte na **Odeslat**.

    ![Odeslat odběr](./media/storsimple-partner-csp-deploy/image4.png)

5. Uložte informace o potvrzení pro budoucí referenci.

    ![Potvrzení uložení](./media/storsimple-partner-csp-deploy/image5.png)

6. Najděte zákazníka, kterého jste právě přidali, nebo přejděte na něj. Kliknutím na **název společnosti** přejdete k podrobnostem.

    ![Hledat zákazníka](./media/storsimple-partner-csp-deploy/image6.png)  

7. V levém podokně vyberte možnost **Správa služeb**. V pravém podokně klikněte v části **Spravovat služby**na **portál pro správu Microsoft Azure** a přihlaste se jako správce Azure pro zákazníka.

    ![Přihlášení k webu Azure Portal](./media/storsimple-partner-csp-deploy/image9.png)

8. StorSimple Device Manager vytvoříte tak, že kliknete na **+ Nový** a vyhledáte nebo přejdete na **řady virtuálních zařízení StorSimple**. Další informace najdete v [nasazení služby StorSimple Device Manager](storsimple-virtual-array-manage-service.md).

    ![Vytvořit službu StorSimple Device Manager](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Přidání předplatného

V některých případech je možné, že máte existujícího zákazníka a potřebujete přidat předplatné. Pokud chcete přidat předplatné k existujícímu zákazníkovi, na portálu pro partnery proveďte následující kroky.

1. Přejít do [partnerského centra](https://partnercenter.microsoft.com/) a přihlaste se pomocí svých přihlašovacích údajů CSP. Klikněte na **řídicí panel**.

     ![Řídicí panel v partnerském centru](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. V levém podokně klikněte na **zákazníci**. Najděte zákazníka, kterému chcete přidat předplatné, nebo na něj přejděte. Kliknutím na ![ ikonu rozbalit kontrolu ikona ](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) rozbalte řádek pro název společnosti pro zákazníka. V části Podrobnosti klikněte na **Přidat odběry**.

    ![Zákazníci](./media/storsimple-partner-csp-deploy/image10.png)

3. Zaškrtněte **Microsoft Azure** u **hlavních nabídek** v rámci předplatného a klikněte na **Odeslat**. Tím se vytvoří nové předplatné.

    ![Přidat nové předplatné](./media/storsimple-partner-csp-deploy/image11.png)

6. Po vytvoření nového předplatného klikněte **<--zákazníci** v levém podokně, abyste se vrátili na stránku **Customers** . Vyhledejte zákazníka, pro kterého jste právě vytvořili předplatné. Kliknutím na **název společnosti** přejdete k podrobnostem.

    ![Hledat zákazníka](./media/storsimple-partner-csp-deploy/image6.png)  

7. V levém podokně vyberte možnost **Správa služeb**. V pravém podokně klikněte v části **Spravovat služby**na **portál pro správu Microsoft Azure** a přihlaste se jako správce Azure pro zákazníka.

    ![Přihlášení k webu Azure Portal](./media/storsimple-partner-csp-deploy/image9.png)

8. StorSimple Device Manager vytvoříte tak, že kliknete na **+ Nový** a vyhledáte nebo přejdete na **řady virtuálních zařízení StorSimple**. Další informace najdete v [nasazení služby StorSimple Device Manager](storsimple-virtual-array-manage-service.md).

    ![Vytvořit službu StorSimple Device Manager](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Další kroky

- Pokud máte další otázky týkající se StorSimple ve zprostředkovateli CSP, přečtěte si [Nejčastější dotazy v StorSimple v CSP: nejčastější dotazy](storsimple-partner-csp-faq.md).
- Pokud jste připraveni nasadit své StorSimple, přečtěte si [nasazení StorSimple v CSP](storsimple-partner-csp-deploy.md).
