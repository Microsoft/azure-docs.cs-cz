---
title: Vytvořit lístek podpory nebo pouzdro řady StorSimple 8000
description: Přečtěte si, jak protokolovat žádost o podporu a spustit relaci podpory na zařízení řady StorSimple 8000.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 99de3a6fbbbb1c4324df1712a5e24fd334ca4977
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254986"
---
# <a name="contact-microsoft-support"></a>Kontaktovat podporu Microsoftu

Správce zařízení StorSimple poskytuje možnost **protokolovat nový požadavek na podporu** v rámci okna souhrnu služby. Pokud narazíte na nějaké problémy s řešením StorSimple, můžete vytvořit žádost o službu pro technickou podporu. V online relaci s pracovníkem podpory může být také nutné spustit relaci podpory na zařízení StorSimple. Tento článek vás provede:

* Jak vytvořit žádost o podporu.
* Jak spravovat životní cyklus žádosti o podporu z portálu.
* Jak spustit relaci podpory v rozhraní prostředí Windows PowerShell vašeho zařízení StorSimple.

Před vytvořením žádosti o podporu si přečtěte [sla podpory řady StorSimple 8000 a informace.](https://msdn.microsoft.com/library/mt433077.aspx)

## <a name="create-a-support-request"></a>Vytvoření žádosti o podporu

V závislosti na [plánu podpory](https://azure.microsoft.com/support/plans/)můžete vytvořit lístky podpory pro problém na zařízení StorSimple přímo z okna souhrnu služby StorSimple Device Manager. Chcete-li vytvořit žádost o podporu, proveďte následující kroky:

#### <a name="to-create-a-support-request"></a>Vytvoření žádosti o podporu

1. Přejděte do služby Správce zařízení StorSimple. V nastavení okna souhrn služby přejděte do části **SUPPORT + TROUBLESHOOTING** a potom klikněte na **Nový požadavek na podporu**.
     
    ![Kontaktujte ms podporu prostřednictvím nového portálu](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. V okně **Nový požadavek na podporu** vyberte **základy**. V okně **Základy** postupujte takto:
   1. V rozevíracím seznamu **Typ problému** vyberte **možnost Technické**.
   2. Aktuální **předplatné**, typ **služby** a **prostředek** (Služba StorSimple Device Manager) jsou automaticky vybrány. 
   3. Pokud máte k předplatnému přidruženo více plánů, vyberte plán **podpory** z rozevíracího seznamu. K povolení technické podpory potřebujete placený plán podpory.
   4. Klikněte na **Další**.

       ![Kontaktujte ms podporu prostřednictvím nového portálu](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. V okně **Nový požadavek na podporu** vyberte krok 2 **Problém**. V okně **Problém** postupujte takto:
    
    1. Zvolte **závažnost**.
    2. Určete, zda problém souvisí s zařízením nebo službou StorSimple Device Manager.
    3. Zvolte **kategorii** pro tento problém a poskytnout další **podrobnosti** o problému.
    4. Zadejte počáteční datum a čas problému.
    5. Při **nahrávání souborů**klikněte na ikonu složky a přejděte k balíčku podpory.
    6. Zkontrolujte **sdílení diagnostických informací**.
    7. Klikněte na **Další**.

       ![Kontaktujte ms podporu prostřednictvím nového portálu](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. V okně **Nový požadavek na podporu** klepněte na krok 3 Kontaktní **informace**. V okně **Kontaktní informace** proveďte následující kroky:

   1. V **možnostech Kontakt**uveďte preferovaný způsob kontaktu (telefon nebo e-mail) a jazyk. Doba odezvy je automaticky vybrána na základě plánu předplatného.
   2. V kontaktních údajích uveďte své jméno, e-mail, volitelný kontakt, zemi nebo oblast. Zaškrtněte políčko **Uložit změny kontaktů pro budoucí žádosti o podporu.**
   3. Klikněte na **Vytvořit**.
   
       ![Kontaktujte ms podporu prostřednictvím nového portálu](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

      Podpora společnosti Microsoft použije tyto informace k tomu, aby vás oslovila za účelem dalších informací, diagnostiky a řešení.
      Po odeslání žádosti vás co nejdříve kontaktuje pracovník technické podpory, aby mohl pokračovat v žádosti.

## <a name="manage-a-support-request"></a>Správa žádosti o podporu

Po vytvoření lístku můžete spravovat lístek v celém jeho životním cyklu přímo z portálu.

#### <a name="to-manage-your-support-requests"></a>Správa žádostí o podporu

1. Chcete-li se dostat na stránku nápovědy a podpory, přejděte na **procházet > nápovědu + podporu**.

    ![Správa žádostí o podporu](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. V okně **Nápověda + podpora** se zobrazí tabulkový výpis všech žádostí o podporu.

    ![Správa žádostí o podporu](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Vyberte a klikněte na žádost o podporu. Můžete zobrazit stav a podrobnosti pro tento požadavek. Chcete-li na tento požadavek navázat, klepněte na tlačítko **+ Nová zpráva.**

    ![Správa žádostí o podporu](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Spuštění relace podpory v prostředí Windows PowerShell pro StorSimple

Chcete-li vyřešit všechny problémy, které mohou nastat se zařízením StorSimple, budete muset spolupracovat s týmem podpory společnosti Microsoft. Podpora společnosti Microsoft může být nutné použít relaci podpory pro přihlášení k zařízení.

Chcete-li zahájit relaci podpory, proveďte následující kroky:

#### <a name="to-start-a-support-session"></a>Zahájení relace podpory

1. Získejte k zařízení přístup přímo pomocí sériové konzole nebo prostřednictvím relace telnet ze vzdáleného počítače. Chcete-li to provést, postupujte podle pokynů v [části Použití putty pro připojení k sériové konzoli zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
2. V relaci, která se otevře, získáte stisknutím klávesy **Enter** příkazový řádek.
3. V nabídce sériové konzole vyberte možnost 1, **Přihlaste se s úplným přístupem**.
4. Na výzvu zadejte následující heslo:
   
    `Password1`
5. Na řádku zadejte následující příkaz:
   
    `Enable-HcsSupportAccess`
6. Zobrazí se šifrovaný řetězec. Zkopírujte tento řetězec do textového editoru, jako je poznámkový blok.
7. Uložte tento řetězec a odešlete jej v e-mailové zprávě na podporu společnosti Microsoft.

> [!IMPORTANT]
> Přístup k podpoře můžete `Disable-HcsSupportAccess`zakázat spuštěním aplikace . Zařízení StorSimple se také pokusí zakázat přístup k podpoře 8 hodin po zahájení relace. Je osvědčeným postupem změnit pověření zařízení StorSimple po zahájení relace podpory.


## <a name="next-steps"></a>Další kroky

Naučte se [diagnostikovat a řešit problémy související se zařízením řady StorSimple 8000](storsimple-8000-troubleshoot-deployment.md)
