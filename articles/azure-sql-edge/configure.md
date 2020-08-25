---
title: Konfigurace Edge Azure SQL (Preview)
description: Přečtěte si o konfiguraci Azure SQL Edge (Preview).
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 07/28/2020
ms.openlocfilehash: 722d33e76b6009a44811dfcb8a3238b042ec6918
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816877"
---
# <a name="configure-azure-sql-edge-preview"></a>Konfigurace Edge Azure SQL (Preview)

Azure SQL Edge podporuje konfiguraci prostřednictvím jedné z následujících dvou možností:

- Proměnné prostředí
- Soubor MSSQL. conf umístěný ve složce/var/opt/MSSQL

> [!NOTE]
> Nastavení proměnných prostředí přepíše nastavení zadané v souboru MSSQL. conf.

## <a name="configure-by-using-environment-variables"></a>Konfigurace pomocí proměnných prostředí

Azure SQL Edge zpřístupňuje několik různých proměnných prostředí, které se dají použít ke konfiguraci kontejneru SQL Edge. Tyto proměnné prostředí jsou podmnožinou těch, které jsou k dispozici pro SQL Server on Linux. Další informace o SQL Server on Linux proměnných prostředí naleznete v tématu [proměnné prostředí](/sql/linux/sql-server-linux-configure-environment-variables/).

Následující proměnná prostředí SQL Server on Linux pro Azure SQL Edge podporována. Pokud je tato proměnná prostředí definována, bude při inicializaci kontejneru ignorována.

| Proměnná prostředí | Popis |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Povolte skupinu dostupnosti. Například hodnota **1** je povolena a **hodnota 0** je zakázána. |

> [!IMPORTANT]
> Proměnná prostředí **MSSQL_PID** SQL Edge jako platné hodnoty přijímá jenom úrovně **Premium** a **Developer** . Azure SQL Edge nepodporuje inicializaci pomocí kódu Product Key.

> [!NOTE]
> Stažení [licenčních podmínek pro software společnosti Microsoft](https://go.microsoft.com/fwlink/?linkid=2128283) pro Azure SQL Edge.

### <a name="specify-the-environment-variables"></a>Zadat proměnné prostředí

Určete proměnné prostředí pro SQL Edge při nasazení služby prostřednictvím [Azure Portal](deploy-portal.md). Můžete je přidat buď do oddílu **proměnné prostředí** v nasazení modulu, nebo jako součást **možností vytváření kontejnerů**.

Přidejte hodnoty do **proměnných prostředí**.

![Nastavení pomocí seznamu proměnných prostředí](media/configure/set-environment-variables.png)

Přidejte hodnoty do **kontejneru možnosti vytvoření**.

![Nastavení pomocí možností vytvoření kontejneru](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-by-using-an-mssqlconf-file"></a>Konfigurace pomocí souboru MSSQL. conf

Azure SQL Edge neobsahuje [konfigurační nástroj MSSQL-conf](/sql/linux/sql-server-linux-configure-mssql-conf/) , jako je SQL Server on Linux. Musíte ručně nakonfigurovat soubor MSSQL. conf a umístit ho do trvalé úložné jednotky, která je namapovaná do složky/var/opt/MSSQL/v modulu SQL Edge. Pokud nasazujete SQL Edge z Azure Marketplace, toto mapování je zadáno jako možnost **připojení** v **kontejneru možnosti vytvoření**.

```json
    {
        "Mounts": [
          {
            "Type": "volume",
            "Source": "sqlvolume",
            "Target": "/var/opt/mssql"
          }
        ]
      }
    }
```

Následující možnosti MSSQL. conf se nevztahují na SQL Edge:

|Možnost|Popis|
|:---|:---|
|**Názory zákazníků** | Vyberte, zda SQL Server odesílá zpětnou vazbu společnosti Microsoft. |
|**Profil databázového e-mailu** | Nastavte výchozí profil databázového e-mailu pro SQL Server on Linux. |
|**Vysoká dostupnost** | Povolte skupiny dostupnosti. |
|**DTC (Distributed Transaction Coordinator) Microsoftu** | Konfigurace a řešení potíží s koordinátorem MSDTC v systému Linux. Další možnosti konfigurace související s distribuovanými transakcemi nejsou podporované pro SQL Edge. Další informace o těchto dalších možnostech konfigurace najdete v tématu [Konfigurace MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc). |
|**Smlouvy EULA ke službám ML** | Přijměte smlouvy EULA jazyka R a Python pro balíčky Azure Machine Learning. Platí jenom pro SQL Server 2019.|
|**outboundnetworkaccess** |Povolí odchozí síťový přístup pro rozšíření [Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) R, Python a Java.|

Následující ukázkový soubor MSSQL. conf funguje pro SQL Edge. Další informace o formátu souboru MSSQL. conf najdete v tématu [Formát MSSQL. conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

```ini
[EULA]
accepteula = Y

[coredump]
captureminiandfull = true
coredumptype = full

[filelocation]
defaultbackupdir = /var/opt/mssql/backup/
defaultdatadir = /var/opt/mssql/data/
defaultdumpdir = /var/opt/mssql/data/
defaultlogdir = /var/opt/mssql/log/

[language]
lcid = 1033

[memory]
memorylimitmb = 6144

[sqlagent]
errorlogfile = /var/opt/mssql/log/sqlagentlog.log
errorlogginglevel = 7

[traceflag]
traceflag0 = 3604
traceflag1 = 3605
traceflag2 = 1204
```

## <a name="run-azure-sql-edge-as-non-root-user"></a>Spustit Azure SQL Edge jako nerootový uživatel

Od verze Azure SQL Edge CTP 2.2 můžou kontejnery SQL Edge běžet s nekořenovým uživatelem nebo skupinou, které nejsou rootem. Při nasazení prostřednictvím Azure Marketplace, pokud není zadán jiný uživatel nebo skupina, se kontejnery SQL Edge spouštějí jako uživatel MSSQL (nerooted). Pokud chcete během nasazení zadat jiného než kořenového uživatele, přidejte `*"User": "<name|uid>[:<group|gid>]"*` pár klíč-hodnota v části možnosti vytvoření kontejneru. V příkladu pod SQL Edge je nakonfigurované spuštění jako uživatel `*IoTAdmin*` .

```json
{
    ..
    ..
    ..
    "User": "IoTAdmin",
    "Env": [
        "MSSQL_AGENT_ENABLED=TRUE",
        "ClientTransportType=AMQP_TCP_Only",
        "MSSQL_PID=Premium"
    ]
}
```

Pokud chcete, aby uživatel nerootoval přístup k souborům databáze umístěným na připojených svazcích, ujistěte se, že uživatel nebo skupina, ve které tento kontejner spouštíte, mají oprávnění ke čtení & zápisu do trvalého úložiště souborů. V následujícím příkladu nastavíme nerootový uživatel s user_id 10001 jako vlastníkem souborů. 

```bash
chown -R 10001:0 <database file dir>
```

### <a name="upgrading-from-earlier-ctp-releases"></a>Upgrade z dřívějších verzí CTP

Předchozí verze CTP služby Azure SQL Edge byla nakonfigurovaná tak, aby běžela jako uživatelé root. Při upgradu ze starší verze CTP jsou k dispozici následující možnosti:

- Pokračovat v používání kořenového uživatele – Chcete-li pokračovat v používání kořenového uživatele, přidejte `*"User": "0:0"*` dvojici klíč-hodnota v části možnosti vytvoření kontejneru.
- Použijte výchozího uživatele MSSQL – Chcete-li použít výchozího uživatele MSSQL, postupujte podle následujících kroků.
  - Přidejte uživatele s názvem MSSQL na hostiteli Docker. V následujícím příkladu přidáme uživatele MSSQL s ID 10001. Tento uživatel je také přidán do kořenové skupiny.
    ```bash
    sudo useradd -M -s /bin/bash -u 10001 -g 0 mssql
    ```
  - Změna oprávnění pro adresář/přípojný svazek, kde se nachází databázový soubor 
    ```bash
    sudo chgrp -R 0 /var/lib/docker/volumes/kafka_sqldata/
    sudo chmod -R g=u /var/lib/docker/volumes/kafka_sqldata/
    ```
- Použít jiný nekořenový uživatelský účet – pro použití jiného než kořenového uživatelského účtu
  - Aktualizujte možnosti vytvoření kontejneru a `*"User": "user_name | user_id*` v části vytvoření kontejneru Určete možnost Přidat dvojici klíč-hodnota. Nahraďte user_name nebo user_id skutečným user_namem nebo user_idm v hostiteli Docker. 
  - Změňte oprávnění pro adresář nebo přípojný svazek.

## <a name="persist-your-data"></a>Uchování dat

Změny konfigurace vašeho okraje Azure SQL Edge a databázové soubory jsou uložené v kontejneru i v případě, že kontejner restartujete pomocí `docker stop` a `docker start` . Pokud ale kontejner odeberete pomocí `docker rm` , odstraní se všechno z kontejneru, včetně Edge Azure SQL a vašich databází. V následující části se dozvíte, jak používat **datové svazky** k uchování souborů databáze i v případě, že jsou přidružené kontejnery smazány.

> [!IMPORTANT]
> Pro Azure SQL Edge je velmi důležité, abyste porozuměli Trvalost dat v Docker. Kromě diskuze v této části najdete informace v dokumentaci Docker o tom, [jak spravovat data v kontejnerech Docker](https://docs.docker.com/engine/tutorials/dockervolumes/).

### <a name="mount-a-host-directory-as-data-volume"></a>Připojit hostitelský adresář jako datový svazek

První možností je připojit adresář na svém hostiteli jako datový svazek ve vašem kontejneru. K tomu použijte `docker run` příkaz s `-v <host directory>:/var/opt/mssql` příznakem. To umožňuje obnovení dat mezi provedeními kontejneru.

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge-developer
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v <host directory>/data:/var/opt/mssql/data -v <host directory>/log:/var/opt/mssql/log -v <host directory>/secrets:/var/opt/mssql/secrets -d mcr.microsoft.com/azure-sql-edge-developer
```

Tato technika také umožňuje sdílet a zobrazovat soubory na hostiteli mimo Docker.

> [!IMPORTANT]
> Mapování svazků hostitele pro **Docker ve Windows v** současné době nepodporuje mapování úplného `/var/opt/mssql` adresáře. Můžete ale mapovat podadresář, jako je například `/var/opt/mssql/data` na hostitelský počítač.

> [!IMPORTANT]
> Mapování svazků hostitele pro **Docker v počítači Mac** s IMAGÍ Azure SQL Edge se v tuto chvíli nepodporuje. Místo toho použijte kontejnery datových svazků. Toto omezení je specifické pro `/var/opt/mssql` adresář. Čtení z připojeného adresáře funguje správně. Například můžete připojit hostitelský adresář pomocí-v na Macu a obnovit zálohu ze souboru. bak, který se nachází na hostiteli.

### <a name="use-data-volume-containers"></a>Použití kontejnerů datových svazků

Druhou možností je použít kontejner datových svazků. Kontejner objemu dat můžete vytvořit zadáním názvu svazku místo hostitelského adresáře s `-v` parametrem. Následující příklad vytvoří sdílený datový svazek s názvem **sqlvolume**.

```bash
docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>' -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge-developer
```

```PowerShell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong!Passw0rd>" -p 1433:1433 -v sqlvolume:/var/opt/mssql -d mcr.microsoft.com/azure-sql-edge-developer
```

> [!NOTE]
> Tato technika pro implicitní vytváření datových svazků v příkazu Run nefunguje se staršími verzemi Docker. V takovém případě použijte explicitní postup popsaný v dokumentaci k Docker, který [vytváří a připojuje kontejner datových svazků](https://docs.docker.com/engine/tutorials/dockervolumes/#creating-and-mounting-a-data-volume-container).

I v případě, že tento kontejner zastavíte a odstraníte, datový svazek přetrvává. Můžete ji zobrazit pomocí `docker volume ls` příkazu.

```bash
docker volume ls
```

Pokud vytvoříte další kontejner se stejným názvem svazku, bude nový kontejner používat stejná data Azure SQL Edge obsažená ve svazku.

K odebrání kontejneru datových svazků použijte `docker volume rm` příkaz.

> [!WARNING]
> Pokud odstraníte kontejner datových svazků, všechna data z Edge SQL Azure v kontejneru se *trvale* odstraní.


## <a name="next-steps"></a>Další kroky

- [Připojení k Edge SQL Azure](connect.md)
- [Sestavení komplexního řešení IoT s využitím SQL Edge](tutorial-deploy-azure-resources.md)
