---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: d2df7388018c463ba58b57be46945915210b84b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175205"
---
#### <a name="to-install-an-update-from-the-azure-portal"></a>Instalace aktualizace z webu Azure Portal

1. Na stránce služby StorSimple vyberte zařízení.

    ![Vybrat zařízení](./media/storsimple-8000-install-update5-via-portal/update1.png)

2. Přejděte na **nastavení** > **zařízení Aktualizace zařízení**.

    ![Klikněte na Aktualizace zařízení.](./media/storsimple-8000-install-update5-via-portal/update2.png)

2. Pokud jsou k dispozici nové aktualizace, zobrazí se oznámení. Případně v okně **Aktualizace zařízení** klepněte na tlačítko **Prohledávat aktualizace**. Vytvoří se úloha vyhledávání dostupných aktualizací. Po úspěšném dokončení úlohy se zobrazí zpráva.

    ![Klikněte na Aktualizace zařízení.](./media/storsimple-8000-install-update5-via-portal/update3.png)

3. Doporučujeme, abyste si před instalací aktualizace na zařízení prošli poznámky k verzi. Chcete-li aktualizace použít, klepněte na tlačítko **Instalovat aktualizace**. V okně **Potvrdit pravidelné aktualizace** zkontrolujte požadavky, které je třeba dokončit před provedením aktualizací. Zaškrtnutím tohoto políčka označte, že jste připraveni zařízení aktualizovat, a klepněte na tlačítko **Instalovat**.

    ![Klikněte na Aktualizace zařízení.](./media/storsimple-8000-install-update5-via-portal/update4.png)

6. Spustí se sada kontrol požadavků. Mezi tyto kontroly patří:
   
   * **Kontroly stavu kontrolerů** pro ověření, že oba kontrolery zařízení jsou v pořádku a online.
   * **Kontroly stavu hardwarových komponent** pro ověření, že všechny hardwarové komponenty v zařízení StorSimple jsou v pořádku.
   * **Kontroly rozhraní DATA 0** pro ověření, že je na zařízení povolené rozhraní DATA 0. Pokud toto rozhraní není povolené, musíte jej povolit a poté to zkusit znovu.

     Aktualizace je stažena a nainstalována pouze v případě, že jsou všechny kontroly úspěšně dokončeny. Na probíhání kontrol budete upozorněni. Pokud předběžné kontroly selžou, budete mít k dispozici důvody selhání. Vyřazujte tyto problémy a opakujte operaci. Pokud si s těmito problémy neporadíte sami, bude muset kontaktovat podporu Microsoftu.

7. Po úspěšném dokončení předběžných kontrol je vytvořena úloha aktualizace. Po úspěšném vytvoření úlohy aktualizace se zobrazí upozornění.
   
    ![Vytvoření úlohy aktualizace](./media/storsimple-8000-install-update5-via-portal/update6.png)
   
    Aktualizace se potom nainstaluje na vašem zařízení.

9. Dokončení aktualizace trvá několik hodin. Podrobnosti o úloze můžete kdykoli zobrazit výběrem úlohy aktualizace a kliknutím na **Podrobnosti**.

    ![Vytvoření úlohy aktualizace](./media/storsimple-8000-install-update5-via-portal/update8.png)

     Průběh úlohy aktualizace můžete také sledovat v **nastavení zařízení > úlohy**. V okně **Úlohy** můžete vidět průběh aktualizace.

     ![Vytvoření úlohy aktualizace](./media/storsimple-8000-install-update5-via-portal/update7.png)

10. Po dokončení úlohy přejděte na **nastavení zařízení > aktualizace zařízení**. Verze softwaru by nyní měla být aktualizována.

