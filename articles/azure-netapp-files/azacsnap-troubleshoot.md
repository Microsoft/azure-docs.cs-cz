---
title: Řešení potíží s nástrojem pro vytváření snímků konzistentním s aplikacemi Azure pro Azure NetApp Files | Microsoft Docs
description: Poskytuje obsah pro odstraňování potíží pomocí nástroje pro vytváření konzistentních aplikací Azure, který můžete použít s Azure NetApp Files.
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
ms.topic: troubleshooting
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 903cb3323b9441ec8bb382054f065760875e3e89
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97632687"
---
# <a name="troubleshoot-azure-application-consistent-snapshot-tool-preview"></a>Řešení potíží s nástrojem pro vytváření snímků konzistentním s aplikacemi Azure (Preview)

Tento článek poskytuje obsah pro odstraňování potíží pomocí nástroje pro vytváření konzistentních aplikací Azure, který můžete použít s Azure NetApp Files.

Níže jsou uvedené běžné problémy, se kterými se můžete setkat při spouštění příkazů. Pokud chcete problém vyřešit, postupujte podle pokynů uvedených v části řešení. Pokud stále dochází k problému, otevřete žádost o službu z Azure Portal a přiřaďte požadavek do fronty SAP HANA velkých instancí, aby mohla podpora Microsoftu odpovídat.

## <a name="log-files"></a>Soubory protokolu

Jedním z nejlepších zdrojů informací pro ladění všech chyb pomocí AzAcSnap jsou soubory protokolu.  

### <a name="log-file-location"></a>Umístění souboru protokolu

Soubory protokolu jsou uloženy v adresáři nakonfigurovaném podle `logPath` parametru v konfiguračním souboru AzAcSnap.  Výchozí název konfiguračního souboru je `azacsnap.json` a výchozí hodnota pro `logPath` je `"./logs"` to znamená, že soubory protokolu jsou zapsány do `./logs` adresáře relativně k umístění, kde `azacsnap` je příkaz spuštěn.  Vytvoření `logPath` absolutního umístění (např. `/home/azacsnap/logs` ) zajistí, `azacsnap` že se výstupy přihlásí `/home/azacsnap/logs` bez ohledu na to, kde `azacsnap` byl příkaz spuštěn.

### <a name="log-file-naming"></a>Pojmenovávání souborů protokolu

Název souboru protokolu je založen na názvu aplikace (např. `azacsnap` ), použitém parametru příkazu ( `-c` ) (například,, `backup` `test` `details` atd.) a názvu souboru konfigurace (například Default = `azacsnap.json` ).  Takže pokud použijete `-c backup` příkaz, název souboru protokolu ve výchozím nastavení bude `azacsnap-backup-azacsnap.log` a zapíše se do adresáře nakonfigurovaného nástrojem `logPath` .  

Tato konvence pojmenování byla navázána tak, aby povolovala více konfiguračních souborů, jednu pro každou databázi a zajistila snadné vyhledání přidružených souborů protokolu.  Proto pokud název souboru konfigurace je `SID.json` , pak výsledný název souboru při použití `azacsnap -c backup --configfile SID.json` možností bude `azacsnap-backup-SID.log` .

### <a name="result-file-and-syslog"></a>Výsledný soubor a syslog

Pro `-c backup` parametr příkazu AzAcSnap zapisuje do `*.result` souboru a systémového protokolu ( `/var/log/messages` ) pomocí `logger` příkazu.  `*.result`Název souboru má stejný základní název jako [soubor protokolu](#log-file-naming) a přejde do stejného [umístění, ve kterém se nachází soubor protokolu](#log-file-location).  Jedná se o jednoduchý jednořádkový výstupní soubor pro následující příklady.

Příklad výstupu ze `*.result` souboru.
```output
Database # 1 (PR1) : completed ok
```

Příklad výstupu ze `/var/log/messages` souboru.
```output
Dec 17 09:01:13 azacsnap-rhel azacsnap: Database # 1 (PR1) : completed ok
```

## <a name="failed-communication-with-sap-hana"></a>Komunikace s SAP HANA se nezdařila

Při ověřování komunikace s SAP HANA spuštěním testu s a se `azacsnap -c test --test hana` zobrazí následující chyba:

```output
> azacsnap -c test --test hana
BEGIN : Test process started for 'hana'
BEGIN : SAP HANA tests
CRITICAL: Command 'test' failed with error:
Cannot get SAP HANA version, exiting with error: 127
```

**Řešení:**

1. Zkontrolujte konfigurační soubor (například `azacsnap.json` ) pro každou instanci Hana, abyste zajistili správnost hodnot SAP HANA databáze.
1. Pokuste se spustit následující příkaz a ověřte, zda `hdbsql` je příkaz v cestě a zda se může připojit k serveru SAP HANA. Následující příklad ukazuje správné spuštění příkazu a jeho výstupu.

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -d SYSTEMDB -U AZACSNAP "\s"
    ```

    ```output
    host          : 172.18.18.50
    sid           : H80
    dbname        : SYSTEMDB
    user          : AZACSNAP
    kernel version: 2.00.040.00.1553674765
    SQLDBC version:        libSQLDBCHDB 2.04.126.1551801496
    autocommit    : ON
    locale        : en_US.UTF-8
    input encoding: UTF8
    sql port      : saphana1:30013
    ```

    V tomto příkladu `hdbsql` není příkaz v uživatelích `$PATH` .

    ```bash
    hdbsql -n 172.18.18.50 - i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    If 'hdbsql' is not a typo you can use command-not-found to lookup the package that contains it, like this:
    cnf hdbsql
    ```

    V tomto příkladu `hdbsql` je příkaz dočasně přidaný uživateli `$PATH` , ale když příkaz Spustit zobrazuje klíč připojení nebyl nastaven správně pomocí `hdbuserstore Set` příkazu (podrobnosti najdete v tématu Začínáme Guide):

    ```bash
    export PATH=$PATH:/hana/shared/H80/exe/linuxx86_64/hdb/
    ```

    ```bash
    hdbsql -n 172.18.18.50 -i 00 -U SCADMIN "select version from sys.m_database"
    ```

    ```output
    * -10104: Invalid value for KEY (SCADMIN)
    ```

    > [!NOTE]
    > Pokud chcete trvale přidat uživatele `$PATH` , aktualizujte soubor uživatele. `$HOME/.profile`

## <a name="the-hdbuserstore-location"></a>`hdbuserstore`Umístění

Při nastavování komunikace s SAP HANA se `hdbuserstore` program používá k vytvoření nastavení zabezpečené komunikace.  `hdbuserstore`Program se obvykle nachází v části `/usr/sap/<SID>/SYS/exe/hdb/` nebo `/usr/sap/hdbclient` .  Obvykle instalační program přidá do uživatele správné umístění `azacsnap` `$PATH` .

## <a name="failed-test-with-storage"></a>Neúspěšný test s úložištěm

Příkaz se nepovedlo `azacsnap -c test --test storage` úspěšně dokončit.

### <a name="azure-large-instance"></a>Velká instance Azure

Následující příklad se spouští `azacsnap` v SAP HANA ve velké instanci Azure:

```bash
azacsnap -c test --test storage
```

```output
The authenticity of host '172.18.18.11 (172.18.18.11)' can't be established.
ECDSA key fingerprint is SHA256:QxamHRn3ZKbJAKnEimQpVVCknDSO9uB4c9Qd8komDec.
Are you sure you want to continue connecting (yes/no)?
```

**Řešení:** Výše uvedená chyba se normálně zobrazuje, když uživatel úložiště velké instance Azure nemá přístup k základnímu úložišti. Pokud chcete ověřit přístup k úložišti pomocí zadaného uživatele úložiště, spusťte `ssh` příkaz a ověřte komunikaci s platformou úložiště.

```bash
ssh <StorageBackupname>@<Storage IP address> "volume show -fields volume"
```

Příklad s očekávaným výstupem:

```bash
ssh clt1h80backup@10.8.0.16 "volume show -fields volume"
```

```output
vserver volume
--------------------------------- ------------------------------
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00001_t020_vol
osa33-hana-c01v250-client25-nprod hana_data_h80_mnt00002_t020_vol
```

#### <a name="the-authenticity-of-host-172181811-172181811-cant-be-established"></a>Nejde navázat pravost hostitele 172.18.18.11 (172.18.18.11).

```bash
azacsnap -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
The authenticity of host '10.3.0.18 (10.3.0.18)' can't be established.
ECDSA key fingerprint is SHA256:cONAr0lpafb7gY4l31AdWTzM3s9LnKDtpMdPA+cxT7Y.
Are you sure you want to continue connecting (yes/no)?
```

**Řešení:** Nevybírejte možnost Ano. Ujistěte se, že je vaše IP adresa úložiště správná. Pokud stále dochází k problému, potvrďte IP adresu úložiště pomocí Microsoft Operations Team.

### <a name="azure-netapp-files"></a>Azure NetApp Files

Následující příklad se spouští `azacsnap` na virtuálním počítači pomocí Azure NetApp Files:

```bash
azacsnap --configfile azacsnap.json.NOT-WORKING -c test --test storage
```

```output
BEGIN : Test process started for 'storage'
BEGIN : Storage test snapshots on 'data' volumes
BEGIN : 1 task(s) to Test Snapshots for Storage Volume Type 'data'
ERROR: Could not create StorageANF object [authFile = 'azureauth.json']
```

**Řešení:**

1. Ověřte existenci hlavního souboru služby, `azureauth.json` jak je nastaveno v `azacsnap.json` konfiguračním souboru.
1. Zkontrolujte soubor protokolu (například `logs/azacsnap-test-azacsnap.log` ) a zjistěte, zda má objekt služby ( `azureauth.json` ) správný obsah.  Příklad z protokolu následujícím způsobem:

      ```output
      [19/Nov/2020:18:39:49 +13:00] DEBUG: [PID:0020080:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000215: Invalid client secret is provided.
      ```

1. Zkontrolujte soubor protokolu (například `logs/azacsnap-test-azacsnap.log` ) a zjistěte, zda vypršela platnost objektu služby ( `azureauth.json` ). Příklad z protokolu následujícím způsobem:

      ```output
      [19/Nov/2020:18:41:10 +13:00] DEBUG: [PID:0020257:StorageANF:659] [1] Innerexception: Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException AADSTS7000222: The provided client secret keys are expired. Visit the Azure Portal to create new keys for your app, or consider using certificate credentials for added security: https://docs.microsoft.com/azure/active-directory/develop/active-directory-certificate-credentials
      ```

## <a name="next-steps"></a>Další kroky

- [Tipy](azacsnap-tips.md)
