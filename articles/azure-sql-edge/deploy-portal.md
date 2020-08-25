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
ms.openlocfilehash: 7af4264860f8d9950515cd5302f03822e7cbac39
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816860"
---
# <a name="deploy-azure-sql-edge-preview"></a>Nasazení Azure SQL Edge (Preview) 

Azure SQL Edge (Preview) je relační databázový stroj optimalizovaný pro nasazení IoT a Azure IoT Edge. Poskytuje funkce pro vytváření vysoce výkonných vrstev pro ukládání a zpracování dat pro aplikace a řešení IoT. V tomto rychlém startu se dozvíte, jak začít vytvářet modul Edge Azure SQL prostřednictvím Azure IoT Edge pomocí Azure Portal.

## <a name="before-you-begin"></a>Než začnete

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).
* Přihlaste se na web [Azure Portal](https://portal.azure.com/).
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
12. Klikněte na **Next** (Další).
13. Klikněte na **Odeslat**.

## <a name="connect-to-azure-sql-edge"></a>Připojení k Edge SQL Azure

V následujících krocích se připojíte k Edge SQL serveru Azure pomocí nástroje příkazového řádku **Sqlcmd**v rámci služby Azure SQL Edge, který se nachází uvnitř kontejneru.

> [!NOTE]
> Nástroj Sqlcmd není k dispozici ve verzi ARM64 kontejnerů SQL Edge.

1. Pomocí `docker exec -it` příkazu spusťte interaktivní prostředí bash v běžícím kontejneru. V následujícím příkladu `azuresqledge` je název určený `Name` parametrem modulu IoT Edge.

   ```bash
   sudo docker exec -it azuresqledge "bash"
   ```

2. V rámci kontejneru se připojte místně pomocí nástroje Sqlcmd. Nástroj Sqlcmd není ve výchozím nastavení v cestě, takže musíte zadat úplnou cestu.

   ```bash
   /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P "<YourNewStrong@Passw0rd>"
   ```

   > [!TIP]
   > Na příkazovém řádku můžete vynechat heslo, aby se zobrazila výzva k jeho zadání.

3. V případě úspěchu byste se měli dostat do příkazového řádku **Sqlcmd** : `1>` .

## <a name="create-and-query-data"></a>Vytvoření a dotazování dat

V následujících částech se dozvíte, jak pomocí nástroje **Sqlcmd** a Transact-SQL vytvořit novou databázi, přidat data a spustit jednoduchý dotaz.

### <a name="create-a-new-database"></a>Vytvoření nové databáze

V následujících krocích se vytvoří nová databáze s názvem `TestDB` .

1. Z příkazového řádku **Sqlcmd** vložte následující příkaz Transact-SQL k vytvoření testovací databáze:

   ```sql
   CREATE DATABASE TestDB
   Go
   ```

2. Na dalším řádku napište dotaz, který vrátí název všech databází na serveru:

   ```sql
   SELECT Name from sys.Databases
   Go
   ```

### <a name="insert-data"></a>Vložení dat

Dále vytvořte novou tabulku, `Inventory` a vložte dva nové řádky.

1. Z příkazového řádku **Sqlcmd** přepněte kontext do nové `TestDB` databáze:

   ```sql
   USE TestDB
   ```

2. Vytvořit novou tabulku s názvem `Inventory` :

   ```sql
   CREATE TABLE Inventory (id INT, name NVARCHAR(50), quantity INT)
   ```

3. Vložte data do nové tabulky:

   ```sql
   INSERT INTO Inventory VALUES (1, 'banana', 150); INSERT INTO Inventory VALUES (2, 'orange', 154);
   ```

4. Zadejte `GO` , chcete-li provést předchozí příkazy:

   ```sql
   GO
   ```

### <a name="select-data"></a>Výběr dat

Nyní spusťte dotaz, který vrátí data z `Inventory` tabulky.

1. Z příkazového řádku **Sqlcmd** zadejte dotaz, který vrátí řádky z tabulky, `Inventory` kde je množství větší než 152:

   ```sql
   SELECT * FROM Inventory WHERE quantity > 152;
   ```

2. Spusťte příkaz:

   ```sql
   GO
   ```

### <a name="exit-the-sqlcmd-command-prompt"></a>Ukončení příkazového řádku Sqlcmd

1. Pokud chcete ukončit relaci **Sqlcmd** , zadejte `QUIT` :

   ```sql
   QUIT
   ```

2. K ukončení interaktivního příkazového řádku ve vašem kontejneru zadejte `exit` . Váš kontejner bude i nadále běžet po ukončení interaktivního prostředí bash.

## <a name="connect-from-outside-the-container"></a>Připojení mimo kontejner

Můžete připojit a spustit dotazy SQL pro vaši instanci Azure SQL Edge ze všech externích nástrojů pro Linux, Windows nebo macOS, které podporují připojení SQL. Další informace o připojení k kontejneru SQL Edge z vnějšku najdete v tématu [připojení a dotazování Azure SQL Edge](https://docs.microsoft.com/azure/azure-sql-edge/connect).

V tomto rychlém startu jste nasadili modul SQL Edge na zařízení IoT Edge. 

## <a name="next-steps"></a>Další kroky

- [Machine Learning a uměle inteligentní informace s ONNXem v SQL Edge](onnx-overview.md).
- [Vytvoření kompletního řešení IoT pomocí SQL Edge pomocí IoT Edge](tutorial-deploy-azure-resources.md).
- [Streamování dat ve službě Azure SQL Edge](stream-data.md)
