---
title: Vytvoření lístku podpory nebo případ řady StorSimple 8000 | Microsoft Docs
description: Informace o protokolu žádosti o podporu a spustit relaci podporu ve vašem zařízení řady StorSimple 8000.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli;
ms.openlocfilehash: e0df86e40d0dc1092ad7ff04f01bbc3e5e3d1c4e
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2018
ms.locfileid: "27743040"
---
# <a name="contact-microsoft-support"></a>Obraťte se na podporu společnosti Microsoft

Správce zařízení StorSimple poskytuje schopnost **protokolu nové žádosti o podporu** v okně Souhrn služby. Pokud narazíte na potíže s vašeho řešení StorSimple, můžete vytvořit žádost o službu pro technickou podporu. V relaci online s svého specialistu technické podpory může také muset spustit relaci podpory zařízení StorSimple. Tento článek vás provede procesem:

* Postup vytvoření žádosti o podporu.
* Jak spravovat cyklus podpory žádost z portálu.
* Jak spustit relaci podpora v rozhraní Windows PowerShell zařízení StorSimple.

Zkontrolujte [StorSimple 8000 řady podporu SLA a informace o](https://msdn.microsoft.com/library/mt433077.aspx) předtím, než vytvoříte žádost o podporu.

## <a name="create-a-support-request"></a>Vytvořit žádost o podporu

V závislosti na vaší [plán podpory](https://azure.microsoft.com/support/plans/), můžete vytvořit lístky žádostí o podporu pro problém na zařízení StorSimple přímo z okna souhrnu service Manager zařízení StorSimple. Proveďte následující kroky k vytvoření žádosti o podporu:

#### <a name="to-create-a-support-request"></a>Chcete-li vytvořit žádost o podporu

1. Přejděte do služby Správce zařízení StorSimple. V okně Souhrn nastavení služby, přejděte na **podporu + Poradce při potížích s** části a pak klikněte na **nová žádost o podporu**.
     
    ![MS obraťte se na podporu prostřednictvím nového portálu](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. V **nová žádost o podporu** vyberte **Základy**. V **Základy** okno, proveďte následující kroky:
   1. Z **vydat typ** rozevíracího seznamu vyberte **technické**.
   2. Aktuální **předplatné**, **služby** typ a **prostředků** (zařízení služby StorSimple Manager) se automaticky vybraná. 
   3. Vyberte **plán podpory** z rozevíracího seznamu, pokud máte více schématy spojené s vaším předplatným. Je nutné placený plán podpory povolit technická podpora.
   4. Klikněte na **Další**.

       ![MS obraťte se na podporu prostřednictvím nového portálu](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. V **nová žádost o podporu** vyberte **krok 2 problém**. V **problém** okno, proveďte následující kroky:
    
    1. Vyberte **závažnost**.
    2. Zadejte, jestli tento problém se týká zařízení nebo službu StorSimple Manager zařízení.
    3. Vyberte **kategorie** pro toto vydání a zadejte více **podrobnosti** o problému.
    4. Zadejte počáteční datum a čas pro problém.
    5. V **nahrávání souborů**, klikněte na ikonu složky a přejděte do balíčku pro podporu.
    6. Zkontrolujte **sdílet diagnostické informace**.
    7. Klikněte na **Další**.

       ![MS obraťte se na podporu prostřednictvím nového portálu](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. V **nová žádost o podporu** okně klikněte na tlačítko **krok 3 kontaktní údaje**. V **kontaktní informace** okno, proveďte následující kroky:

    1. V **obraťte se na možnosti**, poskytovat upřednostňovaný způsob kontaktování (telefon nebo e-mail) a jazyka. Doba odezvy se vybere automaticky podle plánu předplatného.
    2. V kontaktní údaje zadejte název, e-mailu, obraťte se na volitelné, země. Vyberte **uložit změny kontaktů pro budoucí požadavky na podporu** zaškrtávací políčko.
    3. Klikněte na možnost **Vytvořit**.
   
        ![MS obraťte se na podporu prostřednictvím nového portálu](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

    Microsoft Support bude tyto informace používat k oslovení vám další informace, diagnostiku a řešení.
Po odeslání vaši žádost o vás bude kontaktovat pracovníka podpory vám co nejdříve pokračovat k žádosti.

## <a name="manage-a-support-request"></a>Spravovat žádosti o podporu

Po vytvoření lístku můžete spravovat lístek v celém jeho životním cyklu přímo z portálu.

#### <a name="to-manage-your-support-requests"></a>Ke správě žádostí o podporu

1. Chcete-li se na stránku nápovědy a podpory, přejděte na **procházet > Nápověda a podpora**.

    ![Spravovat žádosti o podporu](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. Tabulkový seznam všechny žádosti o podporu se zobrazí v **Nápověda a podpora** okno.

    ![Spravovat žádosti o podporu](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Vyberte a klikněte na žádost o podporu. Můžete zobrazit stav a podrobnosti o této žádosti. Klikněte na tlačítko **+ novou zprávu** Pokud chcete ke zpracování v této žádosti.

    ![Spravovat žádosti o podporu](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Spusťte relaci podpory v prostředí Windows PowerShell pro StorSimple

Chcete-li vyřešit potíže, které se můžete setkat s zařízení StorSimple, musíte použít s týmem Microsoft Support. Microsoft Support možná muset použít relaci podporu pro přihlášení k zařízení.

Proveďte následující kroky pro spuštění relace podpory:

#### <a name="to-start-a-support-session"></a>Chcete-li spustit relaci podpory

1. Přístup k zařízení přímo pomocí konzoly sériového portu nebo prostřednictvím relace Telnetu ze vzdáleného počítače. Chcete-li to provést, postupujte podle kroků v [použití klienta PuTTY k připojení ke konzole sériového portu zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. V této relaci, které se otevře, stiskněte **Enter** klíč k získání příkazového řádku.
3. V nabídce konzoly sériového portu, vyberte možnost 1, **přihlásit úplný přístup**.
4. Do příkazového řádku zadejte následující heslo:
   
    `Password1`
5. Do příkazového řádku zadejte následující příkaz:
   
    `Enable-HcsSupportAccess`
6. Zobrazí se vám zašifrovaný řetězec. Zkopírujte tento řetězec do textového editoru, například Poznámkový blok.
7. Uložte tento řetězec a odeslání v e-mailovou zprávu na Microsoft Support.

> [!IMPORTANT]
> Podpora přístupu můžete vypnout spuštěním `Disable-HcsSupportAccess`. Zařízení StorSimple se také pokusí zakázat přístup podporu 8 hodin po relace byl inicializován. Je osvědčeným postupem změnit svoje přihlašovací údaje zařízení StorSimple po inicializaci relaci podpory.


## <a name="next-steps"></a>Další postup

Zjistěte, jak [diagnostikovat a vyřešit problémy související s zařízení řady StorSimple 8000](storsimple-8000-troubleshoot-deployment.md)
