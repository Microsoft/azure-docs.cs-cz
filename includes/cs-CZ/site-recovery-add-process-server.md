1. Spuštění UnifiedSetup.exe služby Azure Site Recovery
2. V části **Než začnete** vyberte **Přidat další procesové servery pro horizontální navýšení kapacity nasazení**.

  ![Přidání procesového serveru](./media/site-recovery-add-process-server/ps-page-1.png)

3. V části **Podrobnosti konfiguračního serveru** zadejte IP adresu konfiguračního serveru a přístupové heslo.

  ![Přidání procesového serveru 2](./media/site-recovery-add-process-server/ps-page-2.png)
4. Na stránce **Nastavení internetu** určete, jak se zprostředkovatel, který běží na konfiguračním serveru, připojí k Azure Site Recovery přes internet.

  ![Přidání procesového serveru 3](./media/site-recovery-add-process-server/ps-page-3.png)

   * Pokud chcete nastavit připojení s proxy serverem, který je aktuálně nastavený na počítači, vyberte **Připojit se s existujícím nastavením proxy serveru**.
   * Pokud chcete, aby se zprostředkovatel připojil přímo, vyberte **Připojit přímo bez proxy serveru**.
   * Pokud stávající proxy server vyžaduje ověření nebo pokud chcete pro připojení zprostředkovatele používat vlastní proxy server, vyberte **Připojit se s vlastním nastavením proxy serveru**.

     * Pokud použijete vlastní proxy server, budete muset zadat adresu, port a přihlašovací údaje.
     * Pokud používáte proxy server, měli byste už mít povolený přístup k adresám URL služby.

5. Na stránce **Kontrola předpokladů** instalační program provede kontrolu a ověří, že lze spustit instalaci. Pokud se zobrazí varování u položky **Kontrola synchronizace globálního času**, ověřte, že čas na systémových hodinách (nastavení **Datum a čas**) je stejný jako časové pásmo.

     ![Přidání procesového serveru 4](./media/site-recovery-add-process-server/ps-page-4.png)

6. Na stránce **Podrobnosti o prostředí** vyberte, zda se chystáte replikovat virtuální počítače VMware. Pokud ano, instalační program zkontroluje, že je nainstalované rozhraní PowerCLI 6.0.

     ![Přidání procesového serveru 5](./media/site-recovery-add-process-server/ps-page-5.png)

7. Na stránce **Umístění instalace** vyberte, kam chcete nainstalovat binární soubory a ukládat mezipaměť. Vybraná jednotka musí mít minimálně 5 GB dostupného místa na disku, ale pro mezipaměť doporučujeme jednotku alespoň s 600 GB volného místa.
     ![Přidání procesového serveru 5](./media/site-recovery-add-process-server/ps-page-6.png)

8. Na stránce **Výběr sítě** zadejte naslouchací proces (síťový adaptér a port SSL), na kterém konfigurační server odesílá a přijímá data replikace. Výchozím portem pro odesílání a příjem přenosů replikace je port 9443, ale toto číslo portu můžete změnit podle potřeb vašeho prostředí. Kromě portu 9443 otevíráme také port 443, který používá webový server k orchestraci operací replikace. Nepoužívejte port 443 pro odesílání nebo příjem provozu replikace.

     ![Přidání procesového serveru 6](./media/site-recovery-add-process-server/ps-page-7.png)
9. Na stránce **Souhrn** zkontrolujte informace a klikněte na **Nainstalovat**. Po dokončení instalace se vygeneruje heslo. Budete ho potřebovat k povolení replikace, proto si ho zkopírujte a uložte na bezpečném místě.

     ![Přidání procesového serveru 7](./media/site-recovery-add-process-server/ps-page-8.png)
