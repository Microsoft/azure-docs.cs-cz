1. Když jste připojeni k virtuálnímu počítači pomocí Vzdálené plochy, vyhledejte nástroj **Configuration Manager**:

    ![Otevření SSCM](./media/virtual-machines-sql-server-connection-tcp-protocol/sql-server-configuration-manager.png)

1. V podokně konzoly SQL Server Configuration Manageru rozbalte **SQL Server Network Configuration** (Konfigurace sítě SQL Serveru).

1. V podokně konzoly klikněte na **Protokoly pro MSSQLSERVER** (název výchozí instance). V podokně podrobností klikněte pravým tlačítkem na **TCP** a klikněte na **Povolit** (pokud tento protokol ještě není povolený).

    ![Povolení protokolu TCP](./media/virtual-machines-sql-server-connection-tcp-protocol/enable-tcp.png)

1. V podokně konzoly klikněte na **SQL Server Services** (Služby SQL Serveru). V podokně podrobností klikněte pravým tlačítkem na **systému SQL Server (*název instance*)** (výchozí instancí je **SQL Server (MSSQLSERVER)**) a potom klikněte na tlačítko **restartovat**se zastaví a restartuje instanci systému SQL Server.

    ![Restartování databázového stroje](./media/virtual-machines-sql-server-connection-tcp-protocol/restart-sql-server.png)

1. Zavřete SQL Server Configuration Manager.

Další informace o povolení protokolů pro databázový stroj SQL Serveru najdete v tématu věnovaném [povolení nebo zákazu síťového protokolu serveru](http://msdn.microsoft.com/library/ms191294.aspx).
