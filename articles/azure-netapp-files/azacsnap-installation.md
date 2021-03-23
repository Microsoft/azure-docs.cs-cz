---
title: Nainstalujte nástroj pro vytváření snímků konzistentního pro aplikace Azure pro Azure NetApp Files | Microsoft Docs
description: Poskytuje průvodce pro instalaci nástroje Snapshot konzistentního pro aplikace Azure, který můžete použít s Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 458f4d3f29cb08a94095167ed45133f5cd70f5f4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869187"
---
# <a name="install-azure-application-consistent-snapshot-tool-preview"></a>Nainstalovat nástroj pro vytváření snímků konzistentního vzhledem k aplikacím Azure (Preview)

Tento článek poskytuje návod pro instalaci nástroje pro snímek konzistentní s aplikacemi Azure, který můžete použít s Azure NetApp Files.

## <a name="introduction"></a>Úvod

Samoobslužný instalační program ke stažení je navržený tak, aby se nástroje pro vytváření snímků snadno nastavily a běžely s oprávněními nerootového uživatele (například azacsnap). Instalační program nastaví uživatele a umístí nástroje snímku do `$HOME/bin` podadresáře uživatelé (výchozí = `/home/azacsnap/bin` ).
Samoobslužný instalační program se pokusí určit správné nastavení a cesty pro všechny soubory na základě konfigurace uživatele, který provádí instalaci (například root). Pokud byly nezbytné kroky (povolit komunikaci s úložištěm a SAP HANA) jako kořenové, pak instalace zkopíruje privátní klíč a `hdbuserstore` do umístění uživatele zálohy. Je ale možné, že kroky, které umožňují komunikaci s back-endu úložiště a SAP HANA je po instalaci ručně provedli na základě zkušeností správců.

## <a name="prerequisites-for-installation"></a>Předpoklady pro instalaci

Podle pokynů nastavte a spusťte příkazy pro obnovení snímků a zotavení po havárii. Před instalací a používáním nástrojů snímků se doporučuje provést následující kroky jako kořen.

1. **Operační systém je opravený**: informace o [tom, jak nainstalovat a nakonfigurovat SAP Hana (velké instance) v Azure](../virtual-machines/workloads/sap/hana-installation.md#operating-system), najdete v tématu věnovaném opravám a SMT instalačnímu programu.
1. **Synchronizace času je nastavena**. Zákazník bude muset poskytnout časový server kompatibilní s protokolem NTP a odpovídajícím způsobem nakonfigurovat operační systém.
1. **Instalace Hana** : viz pokyny k instalaci Hana v [instalaci SAP NETWEAVER v databázi Hana](/archive/blogs/saponsqlserver/sap-netweaver-installation-on-hana-database).
1. **[Povolit komunikaci s úložištěm](#enable-communication-with-storage)** (Další informace najdete v samostatné části): zákazník musí nastavit SSH s dvojicí privátního/veřejného klíče a poskytnout veřejný klíč pro každý uzel, ve kterém se mají spouštět nástroje snímků pro instalaci do back-endu úložiště.
   1. **Pro Azure NetApp Files (podrobnosti najdete v samostatné části)**: zákazník musí vygenerovat soubor pro ověřování instančního objektu.
   1. **Pro velkou instanci Azure (podrobnosti viz samostatný oddíl)**: zákazník musí nastavit SSH s dvojicí privátního/veřejného klíče a poskytnout veřejný klíč pro každý uzel, ve kterém se mají spouštět nástroje snímků pro instalaci do back-endu úložiště Microsoftu.

      Otestujte to pomocí SSH pro připojení k jednomu z uzlů (například `ssh -l <Storage UserName> <Storage IP Address>` ).
      Zadejte `exit` , chcete-li se odhlásit z výzvy k uložení.

      Operace společnosti Microsoft zajišťují uživateli úložiště a IP adresu úložiště v době zřizování.
  
1. **[Povolit komunikaci s SAP HANA](#enable-communication-with-sap-hana)** (Další informace najdete v samostatné části): zákazník musí pro provedení snímku nastavit příslušného SAP HANAho uživatele s požadovanými oprávněními.
   1. Toto nastavení se dá testovat z příkazového řádku takto: pomocí textu v `grey`
      1. HANAv1

            `hdbsql -n <HANA IP address> -i <HANA instance> -U <HANA user> "\s"`

      1. HANAv2

            `hdbsql -n <HANA IP address> -i <HANA instance> -d SYSTEMDB -U <HANA user> "\s"`

      - Výše uvedené příklady jsou pro komunikaci bez SSL SAP HANA.

## <a name="enable-communication-with-storage"></a>Povolit komunikaci s úložištěm

V této části se dozvíte, jak povolit komunikaci s úložištěm.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Vytvoření instančního objektu RBAC

1. V rámci relace Azure Cloud Shell se ujistěte, že jste přihlášeni k předplatnému, které má být ve výchozím nastavení přidruženo k instančnímu objektu:

    ```azurecli-interactive
    az account show
    ```

1. Pokud předplatné není správné, použijte

    ```azurecli-interactive
    az account set -s <subscription name or id>
    ```

1. Vytvoření instančního objektu pomocí rozhraní příkazového řádku Azure pro následující příklad

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth
    ```

    1. To by mělo vygenerovat výstup podobný následujícímu příkladu:

        ```output
        {
          "clientId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "clientSecret": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "subscriptionId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "tenantId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
          "resourceManagerEndpointUrl": "https://management.azure.com/",
          "activeDirectoryGraphResourceId": "https://graph.windows.net/",
          "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
          "galleryEndpointUrl": "https://gallery.azure.com/",
          "managementEndpointUrl": "https://management.core.windows.net/"
        }
        ```

    > Tento příkaz automaticky přiřadí roli přispěvatele RBAC k instančnímu objektu na úrovni předplatného. rozsah můžete zúžit na konkrétní skupinu prostředků, ve které budou testy prostředky vytvářet.

1. Vyjměte a vložte výstupní obsah do souboru `azureauth.json` s názvem uložený ve stejném systému jako `azacsnap` příkaz a zabezpečte soubor pomocí příslušných systémových oprávnění.

### <a name="azure-large-instance"></a>Velká instance Azure

Komunikace s back-endu úložiště se provádí přes zašifrovaný kanál SSH. Následující příklady kroků poskytují pokyny k instalaci SSH pro tuto komunikaci.

1. Upravit `/etc/ssh/ssh_config` soubor

    Podívejte se na následující výstup, do kterého se `MACs hmac-sha1` přidal řádek:

    ```output
    # RhostsRSAAuthentication no
    # RSAAuthentication yes
    # PasswordAuthentication yes
    # HostbasedAuthentication no
    # GSSAPIAuthentication no
    # GSSAPIDelegateCredentials no
    # GSSAPIKeyExchange no
    # GSSAPITrustDNS no
    # BatchMode no
    # CheckHostIP yes
    # AddressFamily any
    # ConnectTimeout 0
    # StrictHostKeyChecking ask
    # IdentityFile ~/.ssh/identity
    # IdentityFile ~/.ssh/id_rsa
    # IdentityFile ~/.ssh/id_dsa
    # Port 22
    Protocol 2
    # Cipher 3des
    # Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-
    cbc
    # MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd
    MACs hmac-sha
    # EscapeChar ~
    # Tunnel no
    # TunnelDevice any:any
    # PermitLocalCommand no
    # VisualHostKey no
    # ProxyCommand ssh -q -W %h:%p gateway.example.com
    ```

1. Vytvořit pár privátních/veřejných klíčů

    Při generování páru klíčů pomocí následujícího příkladu příkazu nezadávejte heslo při generování klíče.

    ```bash
    ssh-keygen -t rsa –b 5120 -C ""
    ```

1. Odeslání veřejného klíče do operací Microsoftu

    Odešlete výstup `cat /root/.ssh/id_rsa.pub` příkazu (například níže) do operací společnosti Microsoft, čímž umožníte nástrojům snímků komunikovat s podsystémem úložiště.

    ```bash
    cat /root/.ssh/id_rsa.pub
    ```

    ```output
    ssh-rsa
    AAAAB3NzaC1yc2EAAAADAQABAAABAQDoaRCgwn1Ll31NyDZy0UsOCKcc9nu2qdAPHdCzleiTWISvPW
    FzIFxz8iOaxpeTshH7GRonGs9HNtRkkz6mpK7pCGNJdxS4wJC9MZdXNt+JhuT23NajrTEnt1jXiVFH
    bh3jD7LjJGMb4GNvqeiBExyBDA2pXdlednOaE4dtiZ1N03Bc/J4TNuNhhQbdsIWZsqKt9OPUuTfD
    j0XvwUTLQbR4peGNfN1/cefcLxDlAgI+TmKdfgnLXIsSfbacXoTbqyBRwCi7p+bJnJD07zSc9YCZJa
    wKGAIilSg7s6Bq/2lAPDN1TqwIF8wQhAg2C7yeZHyE/ckaw/eQYuJtN+RNBD
    ```

## <a name="enable-communication-with-sap-hana"></a>Povolit komunikaci s SAP HANA

Nástroje Snapshot komunikují s SAP HANA a potřebují uživatele s příslušnými oprávněními k zahájení a uvolnění bodu uložení databáze. Následující příklad ukazuje nastavení uživatele SAP HANA v2 a `hdbuserstore` pro komunikaci s databází SAP HANA.

Následující příklady příkazů nastaví uživatele (AZACSNAP) v SYSTEMDB na SAP HANA 2.
databázi, podle potřeby změňte IP adresu, uživatelská jména a hesla:

1. Připojte se k SYSTEMDB a vytvořte uživatele.

    ```bash
    hdbsql -n <IP_address_of_host>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql SYSTEMDB=>
    ```

1. Vytvoření uživatele

    Tento příklad vytvoří uživatele AZACSNAP v SYSTEMDB.

    ```sql
    hdbsql SYSTEMDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. Udělení uživatelských oprávnění

    V tomto příkladu se nastaví oprávnění pro uživatele AZACSNAP, aby bylo možné provést snímek úložiště konzistentního s databází.

    ```sql
    hdbsql SYSTEMDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *Volitelné* – zabránit vypršení platnosti hesla uživatele

    > [!NOTE]
    > Než tuto změnu provedete, Projděte si podnikové zásady.

   Tento příklad zakáže vypršení platnosti hesla pro uživatele AZACSNAP, aniž by došlo k tomu, že platnost hesla uživatele vyprší, aby se snímky správně nezobrazovaly.  

   ```sql
   hdbsql SYSTEMDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

1. Nastavení SAP HANA zabezpečeného úložiště uživatele (změnit heslo) v tomto příkladu se používá `hdbuserstore` příkaz z prostředí Linux k nastavení SAP HANA zabezpečeného úložiště uživatele.

    ```bash
    hdbuserstore Set AZACSNAP <IP_address_of_host>:30013 AZACSNAP <AZACSNAP_PASSWORD_CHANGE_ME>
    ```

1. Pokud chcete zjistit, jestli je zabezpečené úložiště uživatelů správně nastavené, použijte `hdbuserstore` příkaz pro výpis výstupu podobně jako v následujícím příkladu, v SAP HANA zabezpečeném úložišti uživatelů. Další podrobnosti o používání nástroje `hdbuserstore` jsou k dispozici na webu SAP.

    ```bash
    hdbuserstore List
    ```

    ```output
    DATA FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.DAT
    KEY FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.KEY

    KEY AZACSNAP
    ENV : <IP_address_of_host>:
    USER: AZACSNAP
    ```

### <a name="using-ssl-for-communication-with-sap-hana"></a>Použití protokolu SSL ke komunikaci s SAP HANA

`azacsnap`Nástroj používá SAP HANA `hdbsql` příkaz ke komunikaci s SAP HANA. To zahrnuje použití možností protokolu SSL při šifrování komunikace s SAP HANA. `azacsnap` následujícím `hdbsql` způsobem používá možnosti SSL příkazu.

Při použití možnosti se vždycky používají tyto `azacsnap --ssl` Možnosti:

- `-e` – Povolí šifrování TLS encryptionTLS/SSL. Server zvolí nejvyšší dostupné.
- `-ssltrustcert` – Určuje, jestli se má ověřit certifikát serveru.
- `-sslhostnameincert "*"` – Určuje název hostitele, který se používá k ověření identity serveru. Zadáním `"*"` názvu hostitele se neověří název hostitele tohoto serveru.

Komunikace SSL vyžaduje taky úložiště klíčů a úložiště důvěryhodných souborů.  I když je možné tyto soubory uložit ve výchozích umístěních v instalaci systému Linux, aby se zajistilo, že se pro různé SAP HANA systémy používá správný klíčový materiál (to znamená, v případech, kdy se pro každý SAP HANA systém používají různé soubory úložiště klíčů a důvěryhodné úložiště), očekává, `azacsnap` že se soubory úložiště klíčů a důvěryhodného úložiště ukládají do umístění, které je `securityPath` zadané v `azacsnap` konfiguračním souboru.

#### <a name="key-store-files"></a>Soubory úložiště klíčů

- Pokud používáte více identifikátorů SID se stejným klíčovým materiálem, je snazší vytvořit odkazy na umístění securityPath, jak je definováno v `azacsnap` konfiguračním souboru.  Ujistěte se, že tyto hodnoty existují pro každé SID pomocí protokolu SSL.
  - Pro OpenSSL:
    - `ln $HOME/.ssl/key.pem <securityPath>/<SID>_keystore`
  - Pro commoncrypto:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_keystore`
- Pokud používáte více identifikátorů SID s různými klíčovými materiály na identifikátor SID, zkopírujte (nebo přesuňte a přejmenujte) soubory do umístění securityPath, jak je definováno v `azacsnap` konfiguračním souboru SID.
  - Pro OpenSSL:
    - `mv key.pem <securityPath>/<SID>_keystore`
  - Pro commoncrypto:
    - `mv sapcli.pse <securityPath>/<SID>_keystore`

Při `azacsnap` volání `hdbsql` se přidá `-sslkeystore=<securityPath>/<SID>_keystore` do příkazového řádku.

#### <a name="trust-store-files"></a>Soubory úložiště důvěryhodnosti

- Pokud používáte více identifikátorů SID se stejným materiálem klíče, vytvořte pevné odkazy na umístění securityPath, jak je definováno v `azacsnap` konfiguračním souboru.  Ujistěte se, že tyto hodnoty existují pro každé SID pomocí protokolu SSL.
  - Pro OpenSSL:
    - `ln $HOME/.ssl/trust.pem <securityPath>/<SID>_truststore`
  - Pro commoncrypto:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_truststore`
- Pokud používáte více identifikátorů SID s různými klíčovými materiály na identifikátor SID, zkopírujte (nebo přesuňte a přejmenujte) soubory do umístění securityPath, jak je definováno v `azacsnap` konfiguračním souboru SID.
  - Pro OpenSSL:
    - `mv trust.pem <securityPath>/<SID>_truststore`
  - Pro commoncrypto:
    - `mv sapcli.pse <securityPath>/<SID>_truststore`

> [!NOTE]
> `<SID>`Součástí názvů souborů musí být SAP HANA identifikátor systému všech velkých písmen (například,, `H80` `PR1` atd.).

Při `azacsnap` volání `hdbsql` se přidá `-ssltruststore=<securityPath>/<SID>_truststore` do příkazového řádku.

Proto je spuštěno, `azacsnap -c test --test hana --ssl openssl` kde `SID` je `H80` v konfiguračním souboru, spuštění `hdbsql` připojení následujícím způsobem:

```bash
hdbsql \
    -e \
    -ssltrustcert \
    -sslhostnameincert "*" \
    -sslprovider openssl \
    -sslkeystore ./security/H80_keystore \
    -ssltruststore ./security/H80_truststore
    "sql statement"
```

> [!NOTE]
> `\`Znak je zalomení řádku příkazového řádku pro zlepšení srozumitelnosti více parametrů předaných na příkazovém řádku.

## <a name="installing-the-snapshot-tools"></a>Instalace nástrojů Snapshot Tools

Samoobslužný instalační program ke stažení je navržený tak, aby se nástroje pro vytváření snímků snadno nastavily a běžely s oprávněními nerootového uživatele (například azacsnap). Instalační program nastaví uživatele a umístí nástroje snímku do `$HOME/bin` podadresáře uživatelé (výchozí = `/home/azacsnap/bin` ).

Samoobslužný instalační program se pokusí určit správné nastavení a cesty pro všechny soubory na základě konfigurace uživatele, který provádí instalaci (například root). Pokud jste jako kořenový adresář spustili předchozí kroky instalace (povolit komunikaci s úložištěm a SAP HANA), pak se při instalaci zkopíruje soukromý klíč a `hdbuserstore` do umístění uživatele zálohy. Je ale možné, že kroky, které umožňují komunikaci s back-endu úložiště, a SAP HANA je po instalaci ručně provádět pomocí Správce znalostí.

> [!NOTE]
> Pro starší SAP HANA instalací rozsáhlých instancí Azure byl adresář předinstalovaných nástrojů snímků `/hana/shared/<SID>/exe/linuxx86_64/hdb` .

Po dokončení [požadovaných kroků](#prerequisites-for-installation) je nyní možné nainstalovat nástroje snímků pomocí samoobslužného instalačního programu, a to následujícím způsobem:

1. Zkopírujte stažený samoobslužný instalační program do cílového systému.
1. Spustit samoobslužný instalační program jako `root` uživatel, viz následující příklad. V případě potřeby se souborem pomocí příkazu udělejte spustitelný soubor `chmod +x *.run` .

Při spuštění příkazu pro samoobslužné instalaci bez argumentů se zobrazí informace o použití instalačního programu k instalaci nástrojů snímků následujícím způsobem:

```bash
chmod +x azacsnap_installer_v5.0.run
./azacsnap_installer_v5.0.run
```

```output
Usage: ./azacsnap_installer_v5.0.run [-v] -I [-u <HLI Snapshot Command user>]
./azacsnap_installer_v5.0.run [-v] -X [-d <directory>]
./azacsnap_installer_v5.0.run [-h]

Switches enclosed in [] are optional for each command line.
- h prints out this usage.
- v turns on verbose output.
- I starts the installation.
- u is the Linux user to install the scripts into, by default this is
'azacsnap'.
- X will only extract the commands.
- d is the target directory to extract into, by default this is
'./snapshot_cmds'.
Examples of a target directory are ./tmp or /usr/local/bin
```

> [!NOTE]
> Samoobslužný instalační program obsahuje možnost extrahování (-X) nástrojů snímků ze sady, aniž by bylo nutné provádět vytváření a instalaci uživatelem. To umožňuje zkušeným správcům dokončit ručně instalační kroky nebo zkopírovat příkazy pro upgrade existující instalace.

### <a name="easy-installation-of-snapshot-tools-default"></a>Snadná instalace nástrojů Snapshot Tools (výchozí)

Instalační program byl navržen pro rychlé instalaci nástrojů snímků pro SAP HANA v Azure. Ve výchozím nastavení platí, že pokud je instalační program spuštěn pouze s parametrem-I, provede následující kroky:

1. Vytvořte snímek uživatele ' azacsnap ', domovského adresáře a nastavte členství ve skupině.
1. Nakonfigurujte přihlašovací jméno uživatele azacsnap `~/.profile` .
1. Vyhledat v systému souborů adresáře, které se mají přidat do azacsnap `$PATH` , jsou obvykle cesty k nástrojům pro SAP HANA, jako jsou `hdbsql` a `hdbuserstore` .
1. Vyhledejte v systému souborů adresáře, které chcete přidat do azacsnap `$LD_LIBRARY_PATH` . Mnohé příkazy vyžadují, aby byla cesta ke knihovně nastavená tak, aby se spouštěla správně, tím se nakonfiguruje pro nainstalovaného uživatele.
1. Zkopírujte klíče SSH pro back-end úložiště pro azacsnap od "kořenového" uživatele (uživatel, který spouští instalaci). Předpokládá se, že "kořenový" uživatel už nakonfiguroval připojení k úložišti.
    - Viz část[povolení komunikace s úložištěm](#enable-communication-with-storage).
1. Zkopírujte úložiště zabezpečeného uživatele SAP HANA připojení pro cílového uživatele azacsnap. To předpokládá, že "kořenový" uživatel už nakonfiguroval zabezpečené úložiště uživatelů – viz oddíl povolení komunikace s SAP HANA.
1. Nástroje Snapshot Tools jsou extrahovány do `/home/azacsnap/bin/` .
1. Příkazy v `/home/azacsnap/bin/` mají nastavenou oprávnění (vlastnictví a spustitelný soubor atd.).

Následující příklad ukazuje správný výstup instalační služby při spuštění s možností výchozí instalace.

```bash
./azacsnap_installer_v5.0.run -I
```

```output
+-----------------------------------------------------------+
| Azure Application Consistent Snapshot tool Installer      |
+-----------------------------------------------------------+
|-> Installer version '5.0'
|-> Create Snapshot user 'azacsnap', home directory, and set group membership.
|-> Configure azacsnap .profile
|-> Search filesystem for directories to add to azacsnap's $PATH
|-> Search filesystem for directories to add to azacsnap's $LD_LIBRARY_PATH
|-> Copying SSH keys for back-end storage for azacsnap.
|-> Copying HANA connection keystore for azacsnap.
|-> Extracting commands into /home/azacsnap/bin/.
|-> Making commands in /home/azacsnap/bin/ executable.
|-> Creating symlink for hdbsql command in /home/azacsnap/bin/.
+-----------------------------------------------------------+
| Install complete! Follow the steps below to configure.    |
+-----------------------------------------------------------+
+-----------------------------------------------------------+
|  Install complete!  Follow the steps below to configure.  |
+-----------------------------------------------------------+

1. Change into the snapshot user account.....
     su - azacsnap
2. Set up the HANA Secure User Store..... (command format below)
     hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>
3. Change to location of commands.....
     cd /home/azacsnap/bin/
4. Configure the customer details file.....
     azacsnap -c configure --configuration new
5. Test the connection to storage.....
     azacsnap -c test --test storage
6. Test the connection to HANA.....
   a. without SSL
     azacsnap -c test --test hana
   b. with SSL,  you will need to choose the correct SSL option
     azacsnap -c test --test hana --ssl=<commoncrypto|openssl>
7. Run your first snapshot backup..... (example below)
     azacsnap -c backup --volume=data --prefix=hana_test --frequency=15min --retention=1
```

### <a name="uninstall-the-snapshot-tools"></a>Odinstalace nástrojů snímků

Pokud byly nástroje Snapshot nainstalovány pomocí výchozího nastavení, nástroj pro odinstalaci vyžaduje odebrání uživatele, pro který byly nainstalovány příkazy (výchozí = azacsnap).

```bash
userdel -f -r azacsnap
```

### <a name="manual-installation-of-the-snapshot-tools"></a>Ruční instalace nástrojů Snapshot Tools

V některých případech je nutné nástroje nainstalovat ručně, ale doporučujeme, abyste tento proces usnadnili pomocí výchozí možnosti instalačního programu.

Každý řádek začínající `#` znakem ukazuje příklady příkazů za znakem, které jsou spouštěny kořenovým uživatelem. Na `\` konci řádku je standardní znak pro pokračování řádku pro příkaz Shell.

V případě kořenového uživatele lze ruční instalaci dosáhnout následujícím způsobem:

1. Získá ID skupiny "sapsys", v tomto případě ID skupiny = 1010.

    ```bash
    grep sapsys /etc/group
    ```

    ```output
    sapsys:x:1010:
    ```

1. Vytvořte snímek uživatele ' azacsnap ', domovskému adresáři a nastavte členství ve skupině pomocí ID skupiny z kroku 1.

    ```bash
    useradd -m -g 1010 -c "Azure SAP HANA Snapshots User" azacsnap
    ```

1. Ujistěte se, že přihlášení uživatele azacsnap `.profile` existuje.

    ```bash
    echo "" >> /home/azacsnap/.profile
    ```

1. Vyhledat v systému souborů adresáře, které se mají přidat do $PATH azacsnap. obvykle se jedná o cesty k SAP HANAm nástrojům, jako jsou `hdbsql` a `hdbuserstore` .

    ```bash
    HDBSQL_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name hdbsql -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Přidat aktualizovanou cestu do profilu uživatele

    ```bash
    echo "export PATH=\"\$PATH:$HDBSQL_PATH\"" >> /home/azacsnap/.profile
    ```

1. Vyhledejte v systému souborů adresáře, které chcete přidat do $LD _LIBRARY_PATH azacsnap.

    ```bash
    NEW_LIB_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name "*.so" -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Přidejte aktualizovanou cestu ke knihovně do profilu uživatele.

    ```bash
    echo "export LD_LIBRARY_PATH=\"\$LD_LIBRARY_PATH:$NEW_LIB_PATH\"" >> /home/azacsnap/.profile
    ```

1. Ve velkých instancích Azure
    1. Zkopírujte klíče SSH pro back-end úložiště pro azacsnap od "kořenového" uživatele (uživatel, který spouští instalaci). Předpokládá se, že "kořenový" uživatel už nakonfiguroval připojení k úložišti.
       > Viz část[povolení komunikace s úložištěm](#enable-communication-with-storage).

        ```bash
        cp -pr ~/.ssh /home/azacsnap/.
        ```

    1. Nastavení oprávnění uživatele pro soubory SSH správně

        ```bash
        chown -R azacsnap.sapsys /home/azacsnap/.ssh
        ```

1. V Azure NetApp Files
    1. Nakonfigurujte `DOTNET_BUNDLE_EXTRACT_BASE_DIR` cestu uživatele podle pokynů pro extrakci v jednom souboru .NET Core.
        1. SUSE Linux

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.profile
            ```

        1. RHEL

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.bash_profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.bash_profile
            ```

1. Zkopírujte úložiště zabezpečeného uživatele SAP HANA připojení pro cílového uživatele azacsnap. To předpokládá, že "root" uživatel už nakonfiguroval zabezpečené úložiště uživatelů.
    > Viz část[povolení komunikace s SAP HANA](#enable-communication-with-sap-hana).

    ```bash
    cp -pr ~/.hdb /home/azacsnap/.
    ```

1. Nastavení oprávnění uživatele pro `hdbuserstore` soubory správně

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/.hdb
    ```

1. Extrakce nástrojů snímků do/Home/azacsnap/bin/.

    ```bash
    ./azacsnap_installer_v5.0.run -X -d /home/azacsnap/bin
    ```

1. Nastavit jako spustitelný příkaz

    ```bash
    chmod 700 /home/azacsnap/bin/*
    ```

1. Ujistěte se, že jsou v domovském adresáři uživatele nastavená správná oprávnění vlastnictví.

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/*
    ```

### <a name="complete-the-setup-of-snapshot-tools"></a>Dokončení nastavení nástrojů Snapshot Tools

Instalační program poskytuje kroky, které se dokončí po dokončení instalace nástrojů snímků.
Pomocí těchto kroků nakonfigurujete a otestujete nástroje Snapshot Tools.  Po úspěšném testování proveďte první snímek úložiště konzistentní databáze.

Následující výstup ukazuje kroky, které je potřeba provést po spuštění instalačního programu s výchozími možnostmi instalace:

1. Změnit na uživatelský účet snímku
    1. `su - azacsnap`
1. Nastavení zabezpečeného úložiště uživatelů HANA
   1. `hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>`
1. Změnit na umístění příkazů
   1. `cd /home/azacsnap/bin/`
1. Konfigurace souboru s podrobnostmi o zákaznících
   1. `azacsnap -c configure –-configuration new`
1. Testovat připojení k úložišti....
   1. `azacsnap -c test –-test storage`
1. Testovat připojení k HANA....
    1. bez protokolu SSL
       1. `azacsnap -c test –-test hana`
    1. v případě protokolu SSL budete muset zvolit správnou možnost protokolu SSL.
       1. `azacsnap -c test –-test hana --ssl=<commoncrypto|openssl>`
1. Spuštění první zálohy snímku
    1. `azacsnap -c backup –-volume data--prefix=hana_test --retention=1`

Krok 2 bude nutné v případě, že před instalací nebyla provedena možnost[Povolit komunikaci s SAP HANA](#enable-communication-with-sap-hana).

> [!NOTE]
> Testovací příkazy by se měly spouštět správně. V opačném případě se příkazy nemusí zdařit.

## <a name="configuring-the-database"></a>Konfigurace databáze

V této části se dozvíte, jak nakonfigurovat datovou základnu.

### <a name="sap-hana-configuration"></a>Konfigurace SAP HANA

Existují některé doporučené změny, které je potřeba použít pro SAP HANA k zajištění ochrany záloh a katalogu protokolů. Ve výchozím nastavení `basepath_logbackup` a `basepath_catalogbackup` budou výstupní soubory do `$(DIR_INSTANCE)/backup/log` adresáře a je nepravděpodobné, že se tato cesta nachází na svazku, který `azacsnap` je nakonfigurován na vytvoření snímku těchto souborů, nebude chráněn pomocí snímků úložiště.

Následující `hdbsql` příklady příkazů slouží k demonstraci nastavení cest protokolů a katalogu do umístění, která jsou na svazcích úložiště, na kterých je možné snímky nastavovat `azacsnap` . Nezapomeňte zkontrolovat hodnoty na příkazovém řádku, které odpovídají konfiguraci místní SAP HANA.

### <a name="configure-log-backup-location"></a>Konfigurace umístění zálohy protokolu

V tomto příkladu je změna provedena v `basepath_logbackup` parametru.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_logbackup') = '/hana/logbackups/H80' WITH RECONFIGURE"
```

### <a name="configure-catalog-backup-location"></a>Nakonfigurovat umístění zálohy katalogu

V tomto příkladu je změna provedena v `basepath_catalogbackup` parametru. Nejprve zkontrolujte, zda `basepath_catalogbackup` cesta existuje v systému souborů, pokud není vytvořena cesta se stejným vlastnictvím jako adresář.

```bash
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

Pokud je potřeba vytvořit cestu, následující příklad vytvoří cestu a nastaví správné vlastnictví a oprávnění. Tyto příkazy bude nutné spustit jako kořenový adresář.

```bash
mkdir /hana/logbackups/H80/catalog
chown --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
chmod --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

V následujícím příkladu se změní nastavení SAP HANA.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_catalogbackup') = '/hana/logbackups/H80/catalog' WITH RECONFIGURE"
```

### <a name="check-log-and-catalog-backup-locations"></a>Zkontroluje umístění záloh protokolů a katalogu.

Po provedení výše uvedených změn potvrďte, že jsou nastavení správná, pomocí následujícího příkazu.
V tomto příkladu se nastavení, která jste nastavili podle pokynů uvedených výše, zobrazí jako nastavení systému.

> Tento dotaz také vrátí výchozí nastavení pro porovnání.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where (key = 'basepath_databackup' or key ='basepath_datavolumes' or key = 'basepath_logbackup' or key = 'basepath_logvolumes' or key = 'basepath_catalogbackup')"
```

```output
global.ini,DEFAULT,,,persistence,basepath_catalogbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_databackup,$(DIR_INSTANCE)/backup/data
global.ini,DEFAULT,,,persistence,basepath_datavolumes,$(DIR_GLOBAL)/hdb/data
global.ini,DEFAULT,,,persistence,basepath_logbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_logvolumes,$(DIR_GLOBAL)/hdb/log
global.ini,SYSTEM,,,persistence,basepath_catalogbackup,/hana/logbackups/H80/catalog
global.ini,SYSTEM,,,persistence,basepath_datavolumes,/hana/data/H80
global.ini,SYSTEM,,,persistence,basepath_logbackup,/hana/logbackups/H80
global.ini,SYSTEM,,,persistence,basepath_logvolumes,/hana/log/H80
```

### <a name="configure-log-backup-timeout"></a>Konfigurovat časový limit zálohování protokolu

Výchozí nastavení SAP HANA k provedení zálohy protokolu je 900 sekund (15 minut). Doporučuje se tuto hodnotu zkrátit na 300 sekund (to znamená 5 minut).  Pak je možné spustit pravidelné zálohování (například každých 10 minut) tak, že přidáte log_backups svazek do části Další svazek konfiguračního souboru.

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'log_backup_timeout_s') = '300' WITH RECONFIGURE"
```

#### <a name="check-log-backup-timeout"></a>Kontrolovat časový limit zálohování protokolu

Až provedete změnu časového limitu zálohování protokolu, zkontrolujte, že je toto nastavení nastaveno jako následující.
V tomto příkladu se nastavení, která byla nastavena, zobrazí jako nastavení systému, ale tento dotaz také vrátí výchozí nastavení pro porovnání.

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where key like '%log_backup_timeout%' "
```

```output
global.ini,DEFAULT,,,persistence,log_backup_timeout_s,900
global.ini,SYSTEM,,,persistence,log_backup_timeout_s,300
```

## <a name="next-steps"></a>Další kroky

- [Konfigurace nástroje pro vytváření snímků konzistentního vzhledem k aplikacím Azure](azacsnap-cmd-ref-configure.md)
