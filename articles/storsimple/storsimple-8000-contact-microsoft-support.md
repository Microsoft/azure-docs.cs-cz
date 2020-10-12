---
title: Vytvoření lístku podpory nebo případu pro řady StorSimple 8000
description: Naučte se protokolovat žádosti o podporu a zahájit relaci podpory na zařízení řady StorSimple 8000.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: d5e867849d17a489fad37d09215905d23fb0ed6a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514257"
---
# <a name="contact-microsoft-support"></a>Kontaktovat podporu Microsoftu

StorSimple Device Manager poskytuje možnost **zaprotokolovat novou žádost o podporu** v okně s přehledem služby. Pokud narazíte na problémy s řešením StorSimple, můžete vytvořit žádost o službu pro technickou podporu. V online relaci s pracovníkem technické podpory může být nutné na zařízení StorSimple spustit relaci podpory. Tento článek vás provede:

* Jak vytvořit žádost o podporu.
* Jak spravovat životní cyklus žádosti o podporu v rámci portálu.
* Jak spustit relaci podpory v rozhraní Windows PowerShell zařízení StorSimple.

Před vytvořením Support request si přečtěte [SLA a informace o řadě StorSimple 8000](https://msdn.microsoft.com/library/mt433077.aspx) .

## <a name="create-a-support-request"></a>Vytvoření žádosti o podporu

V závislosti na [plánu podpory](https://azure.microsoft.com/support/plans/)můžete vytvořit lístky podpory pro problém na zařízení StorSimple přímo z okna s přehledem služby StorSimple Device Manager. Chcete-li vytvořit žádost o podporu, proveďte následující kroky:

#### <a name="to-create-a-support-request"></a>Vytvoření žádosti o podporu

1. Přejděte do služby Správce zařízení StorSimple. V okně souhrnu služby přejděte do části **Podpora a řešení potíží** a pak klikněte na **Nová žádost o podporu**.
     
    ![Kontaktovat podporu společnosti Microsoft prostřednictvím nového portálu](./media/storsimple-8000-contact-microsoft-support/contactsupport1.png)
   
2. V okně **Nová žádost o podporu** vyberte **základy**. V okně **základy** proveďte následující kroky:
   1. V rozevíracím seznamu **typ problému** vyberte možnost **technický**.
   2. Automaticky se zvolí aktuální **předplatné**, typ **služby** a **prostředek** (Služba StorSimple Device Manager Service). 
   3. V rozevíracím seznamu vyberte **plán podpory** , pokud máte k vašemu předplatnému k dispozici několik plánů. K povolení technické podpory potřebujete placený plán podpory.
   4. Klikněte na **Next** (Další).

       ![Kontaktovat podporu společnosti Microsoft prostřednictvím nového portálu](./media/storsimple-8000-contact-microsoft-support/contactsupport2.png)

3. V okně **Nová žádost o podporu** vyberte **Krok 2 problém**. V okně **problém** proveďte následující kroky:
    
    1. Vyberte **závažnost**.
    2. Určete, jestli problém souvisí se zařízením nebo službou StorSimple Device Manager.
    3. Vyberte **kategorii** pro tento problém a zadejte další **Podrobnosti** o problému.
    4. Zadejte datum a čas spuštění problému.
    5. V části **nahrání souboru**klikněte na ikonu složky a přejděte do balíčku pro podporu.
    6. Podívejte se na **sdílené diagnostické informace**.
    7. Klikněte na **Next** (Další).

       ![Kontaktovat podporu společnosti Microsoft prostřednictvím nového portálu](./media/storsimple-8000-contact-microsoft-support/contactsupport3.png) 

4. V okně **Nová žádost o podporu** klikněte na **Krok 3 kontaktní informace**. V okně **kontaktní informace** proveďte následující kroky:

   1. V **možnostech kontaktů**zadejte preferovanou metodu kontaktu (telefon nebo e-mail) a jazyk. Doba odezvy se automaticky vybere na základě vašeho plánu předplatného.
   2. V kontaktní informace zadejte své jméno, e-mail, nepovinný kontakt, země/oblast. Zaškrtněte políčko **Uložit změny kontaktů pro budoucí žádosti o podporu** .
   3. Klikněte na **Vytvořit**.
   
       ![Kontaktovat podporu společnosti Microsoft prostřednictvím nového portálu](./media/storsimple-8000-contact-microsoft-support/contactsupport5.png)   

      Podpora Microsoftu použijí tyto informace pro další informace, diagnostiku a řešení.
      Po odeslání vaší žádosti vás pracovník podpory vás bude kontaktovat, jakmile budete moct pokračovat v žádosti.

## <a name="manage-a-support-request"></a>Správa žádosti o podporu

Po vytvoření lístku můžete spravovat lístek v celém jeho životním cyklu přímo z portálu.

#### <a name="to-manage-your-support-requests"></a>Správa žádostí o podporu

1. Pokud se chcete dostat na stránku Nápověda a podpora, přejděte na **Procházet a přejděte > nápovědu a podpora**.

    ![Správa žádostí o podporu](./media/storsimple-8000-contact-microsoft-support/managesupport1.png)

2. V okně **help + support** se zobrazí tabulkový výpis všech žádostí o podporu.

    ![Správa žádostí o podporu](./media/storsimple-8000-contact-microsoft-support/managesupport2.png)

3. Vyberte a klikněte na žádost o podporu. Můžete zobrazit stav a podrobnosti této žádosti. Pokud chcete pokračovat v této žádosti, klikněte na **+ Nová zpráva** .

    ![Správa žádostí o podporu](./media/storsimple-8000-contact-microsoft-support/managesupport3.png)

## <a name="start-a-support-session-in-windows-powershell-for-storsimple"></a>Spuštění relace podpory v Windows PowerShell pro StorSimple

Chcete-li vyřešit všechny problémy, se kterými se můžete setkat se zařízením StorSimple, je třeba se zapojit do týmu podpora Microsoftu. Podpora Microsoftu možná budete muset použít relaci podpory pro přihlášení k vašemu zařízení.

Chcete-li spustit relaci podpory, proveďte následující kroky:

#### <a name="to-start-a-support-session"></a>Spuštění relace podpory

1. Přístup k zařízení přímo pomocí konzoly sériového portu nebo pomocí relace Telnet ze vzdáleného počítače. Pokud se chcete [připojit k konzole sériového portu zařízení](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console), postupujte podle kroků v části použití výstupu.
2. V relaci, která se otevře, stiskněte klávesu **ENTER** a zobrazí se příkazový řádek.
3. V nabídce Konzola sériového portu vyberte možnost 1, **Přihlaste se s úplným přístupem**.
4. Do příkazového řádku zadejte následující heslo:
   
    `Password1`
5. Na příkazovém řádku zadejte následující příkaz:
   
    `Enable-HcsSupportAccess`
6. Zobrazí se vám zašifrovaný řetězec. Zkopírujte tento řetězec do textového editoru, jako je například Poznámkový blok.
7. Uložte tento řetězec a odešlete ho do e-mailové zprávy na podpora Microsoftu.

> [!IMPORTANT]
> Přístup k podpoře můžete zakázat spuštěním `Disable-HcsSupportAccess` . Zařízení StorSimple se taky pokusí zakázat přístup k podpoře 8 hodin po zahájení relace. Osvědčeným postupem je změnit přihlašovací údaje zařízení StorSimple po zahájení relace podpory.


## <a name="next-steps"></a>Další kroky

Naučte se [diagnostikovat a řešit problémy související se zařízením řady StorSimple 8000](storsimple-8000-troubleshoot-deployment.md) .
