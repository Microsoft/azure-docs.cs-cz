---
title: Aktualizace Azure Databricks směrovacích tabulek a bran firewall s novými IP adresami MySQL
description: Naučte se aktualizovat Azure Databricks směrovací tabulky a brány firewall s novými IP adresami MySQL.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 5bd4822c21e6d9dbe8f3534dd0171a6f785bd5ac
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84325912"
---
# <a name="update-your-azure-databricks-route-tables-and-firewalls-with-new-mysql-ips"></a>Aktualizace Azure Databricks směrovacích tabulek a bran firewall s novými IP adresami MySQL

Všechny IP adresy vašeho Azure Database for MySQL používané jako Azure Databricks metastore se mění. Aktualizujte Azure Databricks směrovací tabulky nebo brány firewall s novými IP adresami MySQL před 30. června 2020, aby nedošlo k přerušení.

Pracovní prostor Azure Databricks nasazený ve vaší vlastní virtuální síti má připojenou [směrovací tabulku](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/udr#--metastore-artifact-blob-storage-log-blob-storage-and-event-hub-endpoint-ip-addresses) . Směrovací tabulka může přímo obsahovat IP adresu Azure Databricks metastore nebo trasu k bráně firewall nebo proxy zařízení, na které může být adresa povolená.

## <a name="recommended-actions"></a>Doporučené akce

Abyste se vyhnuli přerušení služby, přečtěte si a použijte tyto akce před 30. června 2020.

* Ověřte, jestli je tabulka směrování ovlivněná změnou v aktualizaci IP adresy Azure MySQL.

* Pomocí tabulky v následující části můžete vyhledat nové IP adresy. Pro každou oblast se může používat víc IP adres jako primární a sekundární IP adresa Azure Database for MySQL bran. Primární IP adresa je aktuální IP adresa brány a druhé IP adresy jsou IP adresy převzetí služeb při selhání v případě selhání primární služby. Chcete-li zajistit stav služby, měli byste u všech IP adres umožňovat odchozí připojení. Pokud používáte [externí metastore](https://docs.microsoft.com/azure/databricks/data/metastores/external-hive-metastore), ujistěte se, že je v rámci směrovaného nebo povoleného oznámení platné IP adresy na Azure MySQL.

* Aktualizujte směrovací tabulku, bránu firewall nebo proxy zařízení s novými IP adresami.

## <a name="updated-mysql-ip-addresses"></a>Aktualizované IP adresy MySQL

Následující tabulka obsahuje všechny IP adresy, které musí být na seznamu povolených. Nové IP adresy jsou IP adresy **tučným písmem** . 

| Název oblasti          | IP adresy brány                                                                                       |
| -------------------- | ---------------------------------------------------------------------------------------------------------- |
| Austrálie – střed    | 20.36.105.0                                                                                                |
| Austrálie – střed 2  | 20.36.113.0                                                                                                |
| Austrálie – východ       | 13.75.149.87<br><br>40.79.161.1                                                                            |
| Austrálie – jihovýchod | 191.239.192.109<br><br>13.73.109.251                                                                       |
| Brazílie – jih         | 104.41.11.5 <br><br> 191.233.201.8 <br><br> **191.233.200.16**                                             |
| Střední Kanada       | 40.85.224.249                                                                                              |
| Kanada – východ          | 40.86.226.166                                                                                              |
| USA – střed           | 23.99.160.139<br><br>13.67.215.62<br><br>**52.182.136.37**<br><br>**52.182.136.38**                        |
| Čína – východ           | 139.219.130.35                                                                                             |
| Čína – východ 2         | 40.73.82.1                                                                                                 |
| Čína – sever          | 139.219.15.17                                                                                              |
| Čína – sever 2        | 40.73.50.0                                                                                                 |
| Východní Asie            | 191.234.2.139<br><br>52.175.33.150<br><br>**13.75.33.20**<br><br>**13.75.33.21**                           |
| USA – východ              | 40.121.158.30<br>191.238.6.43                                                                              |
| USA – východ 2            | 40.79.84.180<br><br>191.239.224.107<br><br>52.177.185.181<br><br>**40.70.144.38**<br><br>**52.167.105.38** |
| Francie – střed       | 40.79.137.0<br><br>40.79.129.1                                                                             |
| Německo – střed      | 51.4.144.100                                                                                               |
| Německo – sever východ   | 51.5.144.179                                                                                               |
| Indie – střed        | 104.211.96.159                                                                                             |
| Indie – jih          | 104.211.224.146                                                                                            |
| Indie – západ           | 104.211.160.80                                                                                             |
| Japonsko – východ           | 13.78.61.196<br><br>191.237.240.43                                                                         |
| Japonsko – západ           | 104.214.148.156<br><br>191.238.68.11<br><br>**40.74.96.6**<br><br>**40.74.96.7**                           |
| Jižní Korea – střed        | 52.231.32.42                                                                                               |
| Jižní Korea – jih          | 52.231.200.86                                                                                              |
| USA – středosever     | 23.96.178.199<br><br>23.98.55.75<br><br>**52.162.104.35**<br><br>**52.162.104.36**                         |
| Severní Evropa         | 40.113.93.91<br><br>191.235.193.75<br><br>**52.138.224.6**<br><br>**52.138.224.7**                         |
| Jižní Afrika – sever   | 102.133.152.0                                                                                              |
| Jižní Afrika – západ    | 102.133.24.0                                                                                               |
| USA – středojih     | 13.66.62.124<br><br>23.98.162.75<br><br>**104.214.16.39**<br><br>**20.45.120.0**                           |
| Jihovýchodní Asie      | 104.43.15.0<br><br>23.100.117.95<br><br>**40.78.233.2**<br><br>**23.98.80.12**                             |
| Spojené arabské emiráty – střed          | 20.37.72.64                                                                                                |
| Spojené arabské emiráty sever            | 65.52.248.0                                                                                                |
| Spojené království – jih             | 51.140.184.11                                                                                              |
| Spojené království – západ              | 51.141.8.11                                                                                                |
| USA – středozápad      | 13.78.145.25                                                                                               |
| Západní Evropa          | 40.68.37.158<br><br>191.237.232.75<br><br>**13.69.105.208**                                                |
| USA – západ              | 104.42.238.205<br><br>23.99.34.75                                                                          |
| USA – západ 2            | 13.66.226.202                                                                                              |

## <a name="next-steps"></a>Další kroky

* [Nasazení Azure Databricks ve službě Azure Virtual Network (vkládání virtuální sítě)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)
* [Externí Apache Hive metastore](https://docs.microsoft.com/azure/databricks/data/metastores/external-hive-metastore)
