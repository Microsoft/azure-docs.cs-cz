### <a name="prepare-for-a-push-installation-on-a-linux-server"></a>Příprava nabízené instalace na serveru s Linuxem

1. Zkontrolujte, zda je síťové připojení mezi počítačem s Linuxem a procesovým serverem.
1. Vytvořte účet, pomocí kterého bude procesový server moct přistupovat k počítači. Účet musí být na zdrojovém serveru s Linuxem uživatelem **root**. Použijte tento účet pouze pro nabízenou instalaci a aktualizace.
1. Zkontrolujte, že soubor /etc/hosts na zdrojovém serveru s Linuxem obsahuje položky, které mapují místní název hostitele na IP adresy přidružené ke všem síťovým adaptérům.
1. Na počítači, který chcete replikovat, nainstalujte nejnovější balíčky openssh, openssh-server a openssl.
1. Ujistěte se, že je povolený Secure Shell (SSH) a že běží na portu 22.
1. SFTP subsystému a ověřování heslem v souboru sshd_config povolte. Postupujte následovně:

    a. Přihlaste se jako uživatel **root**.

    b. V **/etc/ssh/sshd_config** souboru, vyhledejte řádek, který začíná **PasswordAuthentication**.

    c. Zrušte na řádku komentář a změňte hodnotu na **Ano**.

    d. Vyhledejte řádek, který začíná **subsystému**, a zrušte komentář na řádku.

      ![Linux](./media/site-recovery-prepare-push-install-mob-svc-lin/mobility2.png)

    e. Restartujte službu **sshd**.

1. V nástroji CSPSConfigtool přidejte účet, který jste vytvořili. Postupujte následovně:

    a. Přihlaste se ke konfiguračnímu serveru.

    b. Otevřete **cspsconfigtool.exe**. Je k dispozici jako zástupce na ploše a ve složce %ProgramData%\home\svsystems\bin.

    c. Na **spravovat účty** kartu, vyberte možnost **přidat účet**.

    d. Přidejte účet, který jste vytvořili.

    d. Zadejte přihlašovací údaje, které používáte při povolení replikace počítače.
