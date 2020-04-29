---
title: Nasazení SQL Database Edge Preview pomocí Azure Portal | Microsoft Docs
description: Naučte se nasazovat Azure SQL Database Edge pomocí Azure Portal
keywords: nasazení Edge databáze SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da922de38d820864b3f83de80fe64eb3ac792e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246717"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>Nasazení Azure SQL Database Edge Preview

Azure SQL Database Edge Preview je relační databázový stroj optimalizovaný pro nasazení IoT a Azure IoT Edge. Poskytuje funkce pro vytváření vysoce výkonných vrstev pro ukládání a zpracování dat pro aplikace a řešení IoT. V tomto rychlém startu se dozvíte, jak začít vytvářet modul Azure SQL Database Edge pomocí Azure IoT Edge pomocí Azure Portal.

## <a name="before-you-begin"></a>Před zahájením

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).
* Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
* [Sem](https://azure.microsoft.com/services/sql-database-edge/#contact)odešlete žádost, aby bylo předplatné povolené pro nasazení SQL Database Edge.
* Vytvořte [IoT Hub Azure](../iot-hub/iot-hub-create-through-portal.md).
* Zaregistruje [zařízení IoT Edge z Azure Portal](../iot-edge/how-to-register-device-portal.md).
* Připravte zařízení IoT Edge, aby se [IoT Edge modul nasadil z Azure Portal](../iot-edge/how-to-deploy-modules-portal.md).

> [!NOTE]
> Pokud chcete nasadit virtuální počítač Azure Linux jako zařízení IoT Edge, přečtěte si tento [Průvodce rychlým startem](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>Nasadit modul SQL Database Edge z Azure Marketplace

Azure Marketplace je tržiště aplikací a služeb online, kde můžete procházet široké spektrum podnikových aplikací a řešení, která jsou certifikovaná a optimalizovaná pro spouštění v Azure, včetně [modulů IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Azure SQL Database Edge můžete nasadit do hraničního zařízení přes web Marketplace.

1. V Azure Marketplace Najděte modul Azure SQL Database Edge.<br><br>

   ![SQL Database Edge na webu MarketPlace](media/deploy-portal/find-offer-marketplace.png)

2. Vyberte plán softwaru, který nejlépe odpovídá vašim požadavkům a klikněte na **vytvořit**. <br><br>

   ![Výběr správného plánu softwaru](media/deploy-portal/pick-correct-plan.png)

3. Na stránce cílová zařízení pro IoT Edge modul zadejte následující podrobnosti a pak klikněte na **vytvořit** .

   |**Pole**  |**Popis**  |
   |---------|---------|
   |Předplatné  |  Předplatné Azure, v rámci kterého se vytvořilo IoT Hub |
   |IoT Hub   |  Název IoT Hub, kde je IoT Edge zařízení zaregistrované, a pak vyberte možnost nasadit do zařízení.|
   |Název zařízení IoT Edge  |  Název zařízení IoT Edge, kde by se nasadilo SQL Database Edge |

4. Na stránce **nastavit moduly** přejděte na oddíl v části moduly nasazení a klikněte na **konfigurovat** v modulu SQL Database Edge. 

5. V podokně **IoT Edge vlastní moduly** zadejte požadované hodnoty pro proměnné prostředí a/nebo Přizpůsobte možnosti vytvoření a požadované vlastnosti pro modul. Úplný seznam podporovaných proměnných prostředí naleznete [SQL Server proměnných prostředí kontejneru](/sql/linux/sql-server-linux-configure-environment-variables/).

   |**Ukazatele**  |**Popis**|
   |---------|---------|
   | Název | Název modulu. |
   |SA_PASSWORD  | Zadejte silné heslo pro účet správce SQL Database Edge. |
   |MSSQL_LCID   | Nastaví ID jazyka, který se má použít pro SQL Server. Například 1036 je francouzština. |
   |MSSQL_COLLATION | Nastaví výchozí kolaci pro SQL Server. Toto nastavení přepíše výchozí mapování ID jazyka (LCID) na kolaci. |

   > [!NOTE]
   > Neměňte prosím ani neaktualizujte **identifikátor URI image** ani nastavení **ACCEPT_EULA** v modulu.

6. V podokně **IoT Edge vlastní moduly** aktualizujte kontejner možnosti vytvoření požadované hodnoty pro **port hostitele**. Pokud potřebujete nasadit více než jeden modul Edge (SQL DB Edge), nezapomeňte aktualizovat možnost připojení a vytvořit novou zdrojovou & dvojici cílového pro trvalý svazek. Další informace o připojeních a svazcích najdete v dokumentaci k Docker [pomocí svazků](https://docs.docker.com/storage/volumes/) . 

   ```json
       {
         "HostConfig": {
           "Binds": [
             "sqlvolume:/sqlvolume"
           ],
           "PortBindings": {
             "1433/tcp": [
               {
                 "HostPort": "1433"
               }
             ]
           },
           "Mounts": [
             {
               "Type": "volume",
               "Source": "sqlvolume",
               "Target": "/var/opt/mssql"
             }
           ]
         },
         "Env": [
           "MSSQL_AGENT_ENABLED=TRUE",
           "MSSQL_PID=Developer"
         ]
       }
   ```

7. V podokně **IoT Edge vlastní moduly** aktualizujte *požadované vlastnosti modulu set* , aby obsahovaly umístění balíčku SQL a informace o úloze Stream Analytics. Tato dvě pole jsou volitelná a měla by se používat, pokud chcete nasadit modul SQL Database Edge s databází a úlohou streamování.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. V podokně **IoT Edge vlastní moduly** nastavte *zásady restartování* na hodnotu vždy a *požadovaný stav* na spuštěno.
9. V podokně **IoT Edge vlastní moduly** klikněte na **Uložit**.
10. Na stránce **nastavit moduly** klikněte na **Další**.
11. Na stránce **Zadání trasy (volitelné)** na stránce **nastavit moduly** určete trasy pro modul a modul pro IoT Edge komunikaci s rozbočovačem, viz [nasazení modulů a vytváření tras v IoT Edge](../iot-edge/module-composition.md).
12. Klikněte na **Další**.
13. Klikněte na **Odeslat**.

V tomto rychlém startu jste nasadili modul SQL Database Edge do IoT Edge zařízení.

## <a name="next-steps"></a>Další kroky

- [Machine Learning a umělé inteligentní informace s ONNXem v SQL Database Edge](onnx-overview.md).
- Vytvoření kompletního řešení IoT pomocí SQL Database Edge pomocí IoT Edge.
