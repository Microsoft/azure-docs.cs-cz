---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 68c04ef893e5e0826f03bffa93fb7c79b4959fdc
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889261"
---
#### <a name="to-install-an-update-from-the-azure-portal"></a>Instalace aktualizace z webu Azure Portal

1. Na stránce služby StorSimple vyberte zařízení.

    ![Vyberte zařízení.](./media/storsimple-8000-install-update5-via-portal/update1.png)

2. Přejděte do **nastavení zařízení** > **aktualizace zařízení**.

    ![Klikněte na tlačítko aktualizace zařízení](./media/storsimple-8000-install-update5-via-portal/update2.png)

2. Zobrazí se upozornění, pokud jsou k dispozici nové aktualizace. Případně můžete **aktualizace zařízení** okna, klikněte na tlačítko **vyhledat aktualizace**. Vytvoří se úloha vyhledávání dostupných aktualizací. Po úspěšném dokončení úlohy se zobrazí zpráva.

    ![Klikněte na tlačítko aktualizace zařízení](./media/storsimple-8000-install-update5-via-portal/update3.png)

3. Doporučujeme, abyste si před instalací aktualizace na zařízení prošli poznámky k verzi. Chcete-li použít aktualizace, klikněte na tlačítko **instalovat aktualizace**. V **potvrdit pravidelné aktualizace** okno, projděte si požadavky pro dokončení před instalací aktualizace. Vyberte příslušného políčka potvrďte, že budete chtít aktualizovat zařízení a pak klikněte na tlačítko **nainstalovat**.

    ![Klikněte na tlačítko aktualizace zařízení](./media/storsimple-8000-install-update5-via-portal/update4.png)

6. Spustí se sada kontrol požadavků. Mezi tyto kontroly patří:
   
   * **Kontroly stavu kontrolerů** pro ověření, že oba kontrolery zařízení jsou v pořádku a online.
   * **Kontroly stavu hardwarových komponent** pro ověření, že všechny hardwarové komponenty v zařízení StorSimple jsou v pořádku.
   * **Kontroly rozhraní DATA 0** pro ověření, že je na zařízení povolené rozhraní DATA 0. Pokud toto rozhraní není povolené, musíte jej povolit a poté to zkusit znovu.

    Aktualizace se stáhnul a nainstaloval pouze v případě, že jsou všechny kontroly úspěšně dokončeny. Na probíhání kontrol budete upozorněni. Pokud neproběhne úspěšně, potom se bude zobrazovat příčiny chyby. Vyřešení těchto problémů a pak zkuste operaci zopakovat. Pokud si s těmito problémy neporadíte sami, bude muset kontaktovat podporu Microsoftu.

7. Po úspěšném dokončení prechecks se vytvoří úloha aktualizace. Po úspěšném vytvoření úlohy aktualizace se zobrazí upozornění.
   
    ![Vytvoření úlohy aktualizace](./media/storsimple-8000-install-update5-via-portal/update6.png)
   
    Aktualizace se potom nainstaluje na vašem zařízení.

9. Dokončení aktualizace trvá několik hodin. Podrobnosti o úloze můžete kdykoli zobrazit výběrem úlohy aktualizace a kliknutím na **Podrobnosti**.

    ![Vytvoření úlohy aktualizace](./media/storsimple-8000-install-update5-via-portal/update8.png)

     Můžete také sledovat průběh úlohy aktualizace z **nastavení zařízení > úlohy**. Na **úlohy** okně můžete zobrazit průběh aktualizace.

     ![Vytvoření úlohy aktualizace](./media/storsimple-8000-install-update5-via-portal/update7.png)

10. Po dokončení úlohy přejděte **nastavení zařízení > aktualizace zařízení**. Nyní je třeba aktualizovat verzi softwaru.

