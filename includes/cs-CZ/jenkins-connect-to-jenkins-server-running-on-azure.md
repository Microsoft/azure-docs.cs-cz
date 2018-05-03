1. V prohlížeči přejděte k virtuálnímu počítači volaných. Vzhledem k tomu, že řídicí panel volaných není přístupný prostřednictvím HTTP zabezpečená, zobrazí se zpráva, která určuje, že budete muset použít SSH pro tunelové propojení do virtuálního počítače.

    ![Volaných poskytuje informace o vysvětlením, jak se připojit k virtuálnímu počítači volaných prostřednictvím SSH.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ssh-instructions.png)

1. Otevřete okno bash nebo terminálu, s přístupem k SSH.

1. Zadejte následující `ssh` příkaz, nahraďte  **&lt;uživatelské jméno >** a  **&lt;domény >** zástupné symboly hodnoty zadané při vytváření volaných virtuální počítač:

    ```bash
    ssh -L 127.0.0.1:8080:localhost:8080 <username>@<domain>.eastus.cloudapp.azure.com
    ```

1. Postupujte podle `ssh` výzev k přihlášení do volaných virtuálního počítače.

1. Zadejte následující příkaz k načtení počáteční hesla pro odemknutí volaných.

    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

1. Zkopírujte do schránky heslo, které se zobrazí v okně bash nebo terminál.

1. V prohlížeči přejděte na `http://localhost:8080`.

1. Vložte heslo získali dříve do **hesla pro správu** pole a vyberte možnost **pokračovat**.

    ![Volaných ukládá počáteční hesla, které můžete použít k odemknutí volaných virtuální počítač při prvním připojení k němu.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-unlock.png)

1. Vyberte **nainstalovat navrhované modulů plug-in**.

    ![Volaných umožňuje snadno nainstalovat kolekce navrhované modulů plug-in při počáteční vstupu](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-customize.png)

1. Na **vytvořit první uživatele správce** , vytvořte administrativní uživatel a heslo pro volaných řídicího panelu a vyberte **uložit a dokončení**.

1. Na **volaných je připraven!** Vyberte **začít používat volaných**.

    ![Jakmile volaných je nainstalovaný a nakonfigurovaný pro přístup, můžete začít používat ji k vytvoření a vytvoření úlohy.](./media/jenkins-connect-to-jenkins-server-running-on-azure/jenkins-ready.png)