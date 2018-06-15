---
title: Microsoft Azure StorSimple a poskytovatele cloudových řešení programu přehled | Microsoft Docs
description: Přehled o StorSimple a zprostředkovatele kryptografických služeb pro partnery služby StorSimple.
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
ms.openlocfilehash: c8cb51093142146fc7d43b51a62d949f6cc38988
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875652"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>Nasazení programu poskytovatele cloudových řešení pro pole virtuální zařízení StorSimple

## <a name="overview"></a>Přehled

Pole virtuální zařízení StorSimple se dá nasadit partnery Cloud Solution Provider (CSP) pro své zákazníky. Zprostředkovatel kryptografických služeb partnera můžete vytvořit služby StorSimple Manager zařízení. Tato služba pak slouží k nasazení a správě pole virtuální zařízení StorSimple a přidružené sdílených složek, svazků a zálohování.

Tento článek popisuje, jak přidat do stávající zákazník zákazníka nebo nové předplatné a pak vytvořit službu pro nasazení StorSimple virtuální pole v CSP partnera CSP.

## <a name="prerequisites"></a>Požadavky

Než začnete, zajistěte, aby:

- Jste zaregistrováni v rámci programu CSP.
- Máte platný [Partnerské centrum](http://partnercenter.microsoft.com/) přihlašovací údaje. Přihlašovací údaje umožní se přihlásit k portálu pro partnery přidávat nové zákazníky, vyhledejte zákazníků nebo přejděte na účet zákazníka na řídicím panelu partnera. Zprostředkovatel kryptografických služeb, můžou fungovat jako správce StorSimple jménem zákazníka na portálu Azure.
                             
## <a name="add-a-customer"></a>Přidejte zákazníka

Pokud přidáte zákazníka, se automaticky vytvoří předplatné. Přidejte zákazníka (a automaticky vytvořit odběr), proveďte následující kroky v portálu pro partnery.

1. Přejděte na [Partnerské centrum](http://partnercenter.microsoft.com/) a přihlaste se pomocí svých přihlašovacích údajů CSP. Klikněte na tlačítko **řídicí panel**.

     ![Řídicí panel v partnerské Centrum](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. V levém podokně klikněte na **zákazníci**. V pravém podokně klikněte na **přidejte zákazníky**. Zadejte podrobnosti o zákazníka. Klikněte na tlačítko **Další: odběry** vytvořit odběr zákazníka.

    ![Přidat zákazníka](./media/storsimple-partner-csp-deploy/image2.png)

3.  Vyberte **Microsoft Azure** nabízejí. Přejděte do dolní části stránky a klikněte na tlačítko **zkontrolujte**.

    ![Přečtěte si informace o předplatném](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Zkontrolujte zadané informace a klikněte na tlačítko **odeslání**.

    ![Odeslání předplatného](./media/storsimple-partner-csp-deploy/image4.png)

5. Uložte informace o potvrzení pro budoucí použití.

    ![Uložit potvrzení](./media/storsimple-partner-csp-deploy/image5.png)

6. Najít nebo přejděte na Zákazník jste právě přidali. Klikněte **název společnosti** můžete rozbalit podrobné informace.

    ![Hledat zákazníka](./media/storsimple-partner-csp-deploy/image6.png)  

7. V levém podokně vyberte **Správa služeb**. V pravém podokně v části **správě služeb**, klikněte na tlačítko **Microsoft Azure Management Portal** se přihlásit jako správce Azure pro vaše zákazníky.

    ![Přihlaste se k portálu Azure](./media/storsimple-partner-csp-deploy/image9.png)

8. Chcete-li vytvořit Správce zařízení StorSimple, klikněte na tlačítko **+ nový** a vyhledejte nebo přejděte na **virtuální zařízení řady StorSimple**. Další informace, přejděte na [nasazení služby StorSimple Manager zařízení](storsimple-virtual-array-manage-service.md).

    ![Vytvořit službu StorSimple Manager zařízení](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Přidat předplatné

V některých případech může mít stávající zákazník a je nutné přidat předplatné. Pokud chcete přidat odběr do stávající zákazník, proveďte následující kroky v portálu pro partnery.

1. Přejděte na [Partnerské centrum](http://partnercenter.microsoft.com/) a přihlaste se pomocí svých přihlašovacích údajů CSP. Klikněte na tlačítko **řídicí panel**.

     ![Řídicí panel v partnerské Centrum](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. V levém podokně klikněte na **zákazníci**. Najít nebo přejděte zákazníkovi chcete přidat odběr. Klikněte ![ikona zaškrtnutí rozbalte](./media/storsimple-partner-csp-deploy/expand_pane_icon.png) ikonu rozbalte řádek pro název společnosti pro vaše zákazníky. V podrobnostech, klikněte na tlačítko **přidání předplatného**.

    ![Zákazníci](./media/storsimple-partner-csp-deploy/image10.png)

3. Zkontrolujte **Microsoft Azure** pro **Top nabízí** předplatného a klikněte na **odeslání**. Tím se vytvoří nový odběr.

    ![Přidat nové předplatné](./media/storsimple-partner-csp-deploy/image11.png)

6. Po vytvoření nové předplatné, klikněte na tlačítko **<--zákazníkům** v levém podokně se vraťte do **zákazníci** stránky. Vyhledat zákazníka, pro kterého jste právě vytvořili předplatné. Klikněte **název společnosti** můžete rozbalit podrobné informace.

    ![Hledat zákazníka](./media/storsimple-partner-csp-deploy/image6.png)  

7. V levém podokně vyberte **Správa služeb**. V pravém podokně v části **správě služeb**, klikněte na tlačítko **Microsoft Azure Management Portal** se přihlásit jako správce Azure pro vaše zákazníky.

    ![Přihlaste se k portálu Azure](./media/storsimple-partner-csp-deploy/image9.png)

8. Chcete-li vytvořit Správce zařízení StorSimple, klikněte na tlačítko **+ nový** a vyhledejte nebo přejděte na **virtuální zařízení řady StorSimple**. Další informace, přejděte na [nasazení služby StorSimple Manager zařízení](storsimple-virtual-array-manage-service.md).

    ![Vytvořit službu StorSimple Manager zařízení](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Další kroky

- Pokud máte další otázky ohledně StorSimple v CSP, přejděte na [StorSimple v CSP: Nejčastější dotazy](storsimple-partner-csp-faq.md).
- Pokud jste připravení nasadit vaše zařízení StorSimple, přejděte na [nasazení vaše zařízení StorSimple v CSP](storsimple-partner-csp-deploy.md).
