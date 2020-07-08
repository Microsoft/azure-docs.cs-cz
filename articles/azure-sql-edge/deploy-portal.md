---
title: Nasazení Azure SQL Edge (Preview) pomocí Azure Portal
description: Přečtěte si, jak nasadit Azure SQL Edge (Preview) pomocí Azure Portal
keywords: nasazení Edge SQL
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 43359b66ba747dba7b3294d022a2c1aa2a3e624c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84233246"
---
# <a name="deploy-azure-sql-edge-preview"></a>Nasazení Azure SQL Edge (Preview) 

Azure SQL Edge (Preview) je relační databázový stroj optimalizovaný pro nasazení IoT a Azure IoT Edge. Poskytuje funkce pro vytváření vysoce výkonných vrstev pro ukládání a zpracování dat pro aplikace a řešení IoT. V tomto rychlém startu se dozvíte, jak začít vytvářet modul Edge Azure SQL prostřednictvím Azure IoT Edge pomocí Azure Portal.

## <a name="before-you-begin"></a>Než začnete

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).
* Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
* Vytvořte [IoT Hub Azure](../iot-hub/iot-hub-create-through-portal.md).
* Zaregistruje [zařízení IoT Edge z Azure Portal](../iot-edge/how-to-register-device-portal.md).
* Připravte zařízení IoT Edge, aby se [IoT Edge modul nasadil z Azure Portal](../iot-edge/how-to-deploy-modules-portal.md).

> [!NOTE]
> Pokud chcete nasadit virtuální počítač Azure Linux jako zařízení IoT Edge, přečtěte si tento [Průvodce rychlým startem](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-edge-module-from-azure-marketplace"></a>Nasadit modul SQL Edge z Azure Marketplace

Azure Marketplace je tržiště aplikací a služeb online, kde můžete procházet široké spektrum podnikových aplikací a řešení, která jsou certifikovaná a optimalizovaná pro spouštění v Azure, včetně [modulů IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Azure SQL Edge se dá nasadit do hraničního zařízení přes web Marketplace.

1. Najděte modul Azure SQL Edge na Azure Marketplace.<br><br>

   ![SQL Edge na webu MarketPlace](media/deploy-portal/find-offer-marketplace.png)

2. Vyberte plán softwaru, který nejlépe odpovídá vašim požadavkům a klikněte na **vytvořit**. <br><br>

   ![Výběr správného plánu softwaru](media/deploy-portal/pick-correct-plan.png)

3. Na stránce cílová zařízení pro IoT Edge modul zadejte následující podrobnosti a pak klikněte na **vytvořit** .

   |**Pole**  |**Popis**  |
   |---------|---------|
   |Předplatné  |  Předplatné Azure, v rámci kterého se vytvořilo IoT Hub |
   |IoT Hub   |  Název IoT Hub, kde je IoT Edge zařízení zaregistrované, a pak vyberte možnost nasadit do zařízení.|
   |Název zařízení IoT Edge  |  Název zařízení IoT Edge, kde by byl nasazený SQL Edge |

4. Na stránce **nastavit moduly** přejděte na oddíl v části moduly nasazení a klikněte na **Konfigurovat** pro modul SQL Edge. 

5. V podokně **IoT Edge vlastní moduly** zadejte požadované hodnoty pro proměnné prostředí a/nebo Přizpůsobte možnosti vytvoření a požadované vlastnosti pro modul. Úplný seznam podporovaných proměnných prostředí naleznete [SQL Server proměnných prostředí kontejneru](/sql/linux/sql-server-linux-configure-environment-variables/).

   |**Parametr**  |**Popis**|
   |---------|---------|
   | Name | Název modulu. |
   |SA_PASSWORD  | Zadejte silné heslo pro účet správce SQL Edge. |
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

7. V podokně **IoT Edge vlastní moduly** aktualizujte *požadované vlastnosti modulu set* , aby obsahovaly umístění balíčku SQL a informace o úloze Stream Analytics. Tato dvě pole jsou volitelná a měla by se používat v případě, že chcete nasadit modul SQL Edge s databází a úlohou streamování.

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

V tomto rychlém startu jste nasadili modul SQL Edge na zařízení IoT Edge.

## <a name="next-steps"></a>Další kroky

- [Machine Learning a uměle inteligentní informace s ONNXem v SQL Edge](onnx-overview.md).
- [Vytvoření kompletního řešení IoT pomocí SQL Edge pomocí IoT Edge](tutorial-deploy-azure-resources.md).