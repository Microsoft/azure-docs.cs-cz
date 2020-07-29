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
ms.date: 05/19/2020
ms.openlocfilehash: c38bb6100665cc9456b66608660bdca520b934c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84636236"
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

| Proměnná prostředí | Description |
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

|Možnost|Description|
|:---|:---|
|**Názory zákazníků** | Vyberte, zda SQL Server odesílá zpětnou vazbu společnosti Microsoft. |
|**Profil databázového e-mailu** | Nastavte výchozí profil databázového e-mailu pro SQL Server on Linux. |
|**Vysoká dostupnost** | Povolte skupiny dostupnosti. |
|**DTC (Distributed Transaction Coordinator) Microsoftu** | Konfigurace a řešení potíží s koordinátorem MSDTC v systému Linux. Další možnosti konfigurace související s distribuovanými transakcemi nejsou podporované pro SQL Edge. Další informace o těchto dalších možnostech konfigurace najdete v tématu [Konfigurace MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc). |
|**MLServices smlouvy EULA** | Přijměte smlouvy EULA jazyka R a Python pro balíčky Azure Machine Learning. Platí jenom pro SQL Server 2019.|
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

## <a name="next-steps"></a>Další kroky

- [Připojení k Edge SQL Azure](connect.md)
- [Sestavení komplexního řešení IoT s využitím SQL Edge](tutorial-deploy-azure-resources.md)
