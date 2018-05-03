#### <a name="to-install-mpio-on-the-host"></a>K instalaci funkce MPIO na hostiteli
1. Otevřete správce serveru na hostiteli s Windows Server. Ve výchozím nastavení správce serveru spustí, když se člen skupiny Administrators přihlásí k počítači se systémem Windows Server 2012 R2 nebo Windows Server 2012. Pokud správce serveru už není otevřený, klikněte na tlačítko **Start > Správce serveru**.
   
    ![Správce serveru](./media/storsimple-install-mpio-windows-server/IC740997.png)
2. Klikněte na tlačítko **správce serveru > řídicí panel > Přidat role a funkce**. Tím se spustí **přidat role a funkce** průvodce.
   
    ![Přidat role a funkce Průvodce 1](./media/storsimple-install-mpio-windows-server/IC740998.png)
3. V **přidat role a funkce** průvodce, postupujte takto:
   
   * Na **před zahájením** klikněte na tlačítko **Další**.
   * Na **vybrat typ instalace** přijměte výchozí nastavení **na základě rolí nebo na základě funkcí** instalace. Klikněte na **Další**.
     
       ![Přidat role a funkce Průvodce 2](./media/storsimple-install-mpio-windows-server/IC740999.png)
   * Na **vybrat cílový server** vyberte **vyberte server z fondu serverů**. Hostitelský server by měly být zjištěny automaticky. Klikněte na **Další**.
   * Na **vybrat role serveru** klikněte na tlačítko **Další**.
   * Na **vyberte funkce,** vyberte **funkce Multipath I/O**a klikněte na tlačítko **Další**.
     
       ![Přidat role a funkce Průvodce 5](./media/storsimple-install-mpio-windows-server/IC741000.png)
   * Na **potvrdit vybrané možnosti instalace** potvrďte výběr a potom vyberte **cílový server automaticky restartovat, pokud je to nutné**, jak je uvedeno níže. Klikněte na **Nainstalovat**.
     
       ![Přidat role a funkce Průvodce 8](./media/storsimple-install-mpio-windows-server/IC741001.png)
   * Po dokončení instalace, budete upozorněni. Průvodce zavřete kliknutím na **Zavřít**.
     
       ![Přidat role a funkce Průvodce 9](./media/storsimple-install-mpio-windows-server/IC741002.png)

