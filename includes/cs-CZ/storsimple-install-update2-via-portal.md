<!--author=alkohli last changed: 02/06/17-->

#### <a name="to-install-an-update-from-the-azure-portal"></a>Instalace aktualizace z webu Azure Portal

1. Na stránce služby StorSimple vyberte zařízení. Přejděte do **Zařízení** > **Údržba**.
2. Ve spodní části stránky klikněte na **Vyhledat aktualizace**. Vytvoří se úloha vyhledávání dostupných aktualizací. Po úspěšném dokončení úlohy se zobrazí zpráva.
3. V části **Aktualizace softwaru** na stejné stránce budou k dispozici nové aktualizace softwaru. Doporučujeme, abyste si před instalací aktualizace na zařízení prošli poznámky k verzi.
4. V dolní části stránky klikněte na **Nainstalovat aktualizace** a potom na **OK**.
5. V dialogovém okně **Instalace aktualizací** se ujistěte, že jste postupovali podle doporučení, potom vyberte **Rozumím výše uvedeným požadavkům a jsem připraven aktualizovat zařízení** a klikněte na tlačítko se symbolem zaškrtnutí.
   
    ![Potvrzovací zpráva](./media/storsimple-install-update2-via-portal/InstallUpdate12_2M.png)
6. Spustí se sada kontrol požadavků. Mezi tyto kontroly patří:
   
   * **Kontroly stavu kontrolerů** pro ověření, že oba kontrolery zařízení jsou v pořádku a online.
   * **Kontroly stavu hardwarových komponent** pro ověření, že všechny hardwarové komponenty v zařízení StorSimple jsou v pořádku.
   * **Kontroly rozhraní DATA 0** pro ověření, že je na zařízení povolené rozhraní DATA 0. Pokud toto rozhraní není povolené, musíte jej povolit a poté to zkusit znovu.
   * **Kontroly rozhraní DATA 2 a DATA 3** pro ověření, že nejsou povolená síťová rozhraní DATA 2 a DATA 3. Pokud jsou tato rozhraní povolená, musíte je zakázat a potom se pokusit o aktualizaci zařízení. Tato kontrola se provádí, pouze pokud provádíte aktualizaci ze zařízení, na kterém běží software verze GA. U zařízení s verzemi 0.1, 0.2 nebo 0.3 tato kontrola není nutná.
   * **Kontrola brány** na zařízeních s verzí starší než Update 1. Tato kontrola se provádí na všech zařízeních se softwarem starší verze než Update 1, ale selže na zařízeních s bránou nakonfigurovanou pro jiné síťové rozhraní než DATA 0.
     
     Aktualizace se nainstaluje, pokud se úspěšně dokončí všechny kontroly. Na probíhání kontrol budete upozorněni.
     
     ![Upozornění na předběžnou kontrolu](./media/storsimple-install-update2-via-portal/InstallUpdate12_3M.png)
     
     V následujícím příkladu je ukázka selhání kontrol. Je třeba ověřit, že oba kontrolery zařízení jsou v pořádku a online. Také je třeba zkontrolovat stav hardwarových komponent. V tomto příkladu vyžadují pozornost komponenty Controller 0 a Controller 1. Pokud si s těmito problémy neporadíte sami, bude muset kontaktovat podporu Microsoftu.
     
       ![Selhání kontrol](./media/storsimple-install-update2-via-portal/HCS_PreUpgradeChecksFailed-include.png)
7. Po úspěšném dokončení kontrol se vytvoří úloha aktualizace. Po úspěšném vytvoření úlohy aktualizace se zobrazí upozornění.
   
    ![Vytvoření úlohy aktualizace](./media/storsimple-install-update2-via-portal/InstallUpdate12_44M.png)
   
    Aktualizace se potom nainstaluje na vašem zařízení.
    
8. Pokud chcete sledovat průběh úlohy aktualizace, klikněte na **Zobrazit úlohu**. Na stránce **Úlohy** se zobrazí průběh aktualizace.
9. Dokončení aktualizace trvá několik hodin. Podrobnosti o úloze můžete kdykoli zobrazit výběrem úlohy aktualizace a kliknutím na **Podrobnosti**.
10. Po dokončení úlohy přejděte na stránku **Údržba** a přejděte dolů do části **Aktualizace softwaru**.

