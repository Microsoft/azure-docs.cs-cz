---
title: Konfigurace Edge Azure SQL (Preview)
description: Další informace o konfiguraci Azure SQL Edge (Preview)
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: dc2b76a31982a3f72da02348c1a4796212887cb7
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/29/2020
ms.locfileid: "84168246"
---
# <a name="configure-azure-sql-edge-preview"></a>Konfigurace Edge Azure SQL (Preview)

Azure SQL Edge podporuje konfiguraci prostřednictvím jedné z následujících dvou možností:

- Použití proměnných prostředí.
- Používá se soubor MSSQL. conf umístěný ve složce/var/opt/MSSQL.

> [!NOTE]
> Nastavení proměnných prostředí přepíše nastavení zadané v souboru MSSQL. conf.

## <a name="configure-using-environment-variables"></a>Konfigurace pomocí proměnných prostředí

Azure SQL Edge zpřístupňuje několik různých proměnných prostředí, které se dají použít ke konfiguraci kontejneru SQL Edge. Tyto proměnné prostředí jsou podmnožinou proměnných prostředí, které jsou k dispozici pro SQL Server on Linux. Další informace o SQL Server on Linux proměnných prostředí naleznete v tématu [proměnné prostředí](/sql/linux/sql-server-linux-configure-environment-variables/).

Následující proměnné prostředí SQL Server on Linux pro Azure SQL Edge podporované nejsou. Při definování budou tyto proměnné prostředí při inicializaci kontejneru ignorovány.

| Proměnná prostředí | Popis |
|-----|-----|
| **MSSQL_ENABLE_HADR** | Povolte skupinu dostupnosti. Například ' 1 ' je povolen a ' 0 ' je zakázán |

> [!IMPORTANT]
> Proměnná prostředí *MSSQL_PID* SQL Edge jako platné hodnoty přijímá jenom úrovně **Premium** a **Developer** . Azure SQL Edge nepodporuje inicializaci pomocí kódu Product Key.

> [!NOTE]
> Pokud si chcete stáhnout licenční smlouvu s koncovým uživatelem pro Azure SQL Edge, přečtěte si licenční [smlouvu s koncovým uživatelem](https://go.microsoft.com/fwlink/?linkid=2128283).

### <a name="specifying-the-environment-variables"></a>Určení proměnných prostředí

Proměnné prostředí pro SQL Edge můžete zadat při nasazování Azure SQL Edge přes [Azure Portal](deploy-portal.md). To lze přidat buď do oddílu "proměnné prostředí" v nasazení modulu, nebo jako součást možnosti vytvořit kontejner, jak je popsáno níže.

*Nastavení možností použití proměnných prostředí*

![nastavit pomocí seznamu proměnných prostředí](media/configure/set-environment-variables.png)

*Nastavení pomocí možností vytvoření kontejneru*

![nastavení pomocí možností vytvoření kontejneru](media/configure/set-environment-variables-using-create-options.png)

## <a name="configure-using-mssqlconf-file"></a>Konfigurace pomocí souboru MSSQL. conf

Azure SQL Edge neobsahuje [konfigurační nástroj MSSQL-conf](/sql/linux/sql-server-linux-configure-mssql-conf/) , jako je SQL Server on Linux, protože je potřeba ručně nakonfigurovat soubor MSSQL. conf a umístit ho do trvalé jednotky úložiště, která je namapovaná na složku/var/opt/MSSQL/v modulu SQL Edge. Při nasazování okraje SQL Edge z Azure Marketplace je toto mapování zadáno jako možnost * * připojení v možnosti vytvořit kontejner.

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
|**Názory zákazníků** | Určete, zda SQL Server odesílá zpětnou vazbu společnosti Microsoft. |
|**Profil Databázová pošta** | Nastavte výchozí profil databázového e-mailu pro SQL Server on Linux. |
|**Vysoká dostupnost** | Povolte skupiny dostupnosti. |
|**DTC (Distributed Transaction Coordinator) Microsoftu** | Konfigurace a řešení potíží s koordinátorem MSDTC v systému Linux. Další možnosti konfigurace související s distribuovanými transakcemi se také pro SQL Edge nepodporují. Další informace o těchto dalších možnostech konfigurace najdete v tématu [Konfigurace MSDTC](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#msdtc) . |
|**MLServices smlouvy EULA** | Přijměte smlouvy EULA jazyka R a Python pro balíčky Machine Learning Services. Platí jenom pro SQL Server 2019.|
|**outboundnetworkaccess** |Povolí odchozí síťový přístup pro rozšíření [Machine Learning Services](/sql/linux/sql-server-linux-setup-machine-learning/) R, Python a Java.|

Ukázkový soubor MSSQL. conf, který funguje pro SQL Edge, je uvedený níže. Další informace o formátu souboru MSSQL. conf najdete v tématu [Formát MSSQL. conf](https://docs.microsoft.com/sql/linux/sql-server-linux-configure-mssql-conf#mssql-conf-format).

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

## <a name="next-step"></a>Další krok

- [Připojení k Edge SQL Azure](connect.md)
- [Sestavení komplexního řešení IoT pomocí SQL Edge](tutorial-deploy-azure-resources.md)
