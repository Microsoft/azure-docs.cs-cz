---
title: Nasazení Azure SQL Edge pomocí Azure Portal
description: Přečtěte si, jak nasadit Azure SQL Edge pomocí Azure Portal
keywords: nasazení Edge SQL
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 683a3fc148d3521366beef4427e87200ea81a9f3
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91445725"
---
# <a name="deploy-azure-sql-edge"></a>Nasazení Edge Azure SQL 

Azure SQL Edge je relační databázový stroj optimalizovaný pro nasazení IoT a Azure IoT Edge. Poskytuje funkce pro vytváření vysoce výkonných vrstev pro ukládání a zpracování dat pro aplikace a řešení IoT. V tomto rychlém startu se dozvíte, jak začít vytvářet modul Edge Azure SQL prostřednictvím Azure IoT Edge pomocí Azure Portal.

## <a name="before-you-begin"></a>Než začnete

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).
* Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
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

4. Na stránce **nastavit moduly na zařízení:** klikněte v části **IoT Edge moduly**na modul Azure SQL Edge. Výchozí název modulu je nastavený na *AzureSQLEdge*. 

5. V části *nastavení modulu* v okně **aktualizovat IoT Edge modul** zadejte požadované hodnoty pro *název IoT Edge modulu*, *zásady restartování* a *požadovaný stav*. 

   > [!IMPORTANT]    
   > Neměňte ani neaktualizujte nastavení **identifikátoru URI image** v modulu.

6. V části *proměnné prostředí* okna **aktualizovat IoT Edge modul** zadejte požadované hodnoty pro proměnné prostředí. Úplný seznam proměnných prostředí Azure SQL Edge najdete v tématu [Konfigurace pomocí proměnných prostředí](configure.md#configure-by-using-environment-variables). Pro modul jsou definovány následující výchozí proměnné prostředí. 

   |**Parametr**  |**Popis**|
   |---------|---------|
   | MSSQL_SA_PASSWORD  | Změnou výchozí hodnoty určete silné heslo pro účet správce SQL Edge. |
   | MSSQL_LCID   | Změnou výchozí hodnoty nastavte požadované ID jazyka pro použití pro SQL Edge. Například 1036 je francouzština. |
   | MSSQL_COLLATION | Změnou výchozí hodnoty nastavte výchozí kolaci pro SQL Edge. Toto nastavení přepíše výchozí mapování ID jazyka (LCID) na kolaci. |

   > [!IMPORTANT]    
   > Neměňte ani neaktualizujte proměnnou prostředí **ACCEPT_EULA** pro modul.

7. V okně pro *vytvoření kontejneru* v okně **aktualizovat IoT Edge modul** aktualizujte podle potřeby následující možnosti. 
   - **Port hostitele:** Namapujte zadaný port hostitele na port 1433 (výchozí port SQL) v kontejneru.
   - **Vytvoří vazby** a **připojí se:** Pokud potřebujete nasadit více než jeden modul SQL Edge, ujistěte se, že jste aktualizovali možnost připojení, aby se vytvořila nová zdrojová & dvojice cílových pro trvalý svazek. Další informace o připojeních a svazcích najdete v dokumentaci k Docker [pomocí svazků](https://docs.docker.com/storage/volumes/) . 

   ```json
   {
    "HostConfig": {
        "CapAdd": [
            "SYS_PTRACE"
        ],
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
        "ClientTransportType=AMQP_TCP_Only",
        "PlanId=asde-developer-on-iot-edge"
    ]
   }
   ```
   > [!IMPORTANT]    
   > Neměňte `PlanId` proměnnou vyhledáte definovanou v nastavení Create config. Pokud se tato hodnota změní, kontejner Edge Azure SQL se nepodaří spustit. 
   
8. V podokně **aktualizovat IoT Edge modul** klikněte na **aktualizovat**.
9. Na stránce **nastavit moduly na zařízení** klikněte na **další: trasy >** , pokud potřebujete definovat trasy pro vaše nasazení. V opačném případě klikněte na tlačítko **zkontrolovat + vytvořit**. Další informace o konfiguraci tras najdete v tématu [nasazení modulů a vytváření tras v IoT Edge](../iot-edge/module-composition.md).
11. Na stránce **nastavit moduly na zařízení** klikněte na **vytvořit**.

## <a name="connect-to-azure-sql-edge"></a>Připojení k Edge SQL Azure

V následujících krocích se připojíte k Edge SQL serveru Azure pomocí nástroje příkazového řádku **Sqlcmd**v rámci služby Azure SQL Edge, který se nachází uvnitř kontejneru.

> [!NOTE]      
> Nástroje příkazového řádku SQL (Sqlcmd) nejsou k dispozici v rámci ARM64 verze kontejnerů Azure SQL Edge.

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

V následujících částech se dozvíte, jak pomocí nástroje **Sqlcmd** a Transact-SQL vytvořit novou databázi, přidat data a spustit dotaz.

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

- [Machine Learning a umělá logika s ONNXem v SQL Edge](onnx-overview.md)
- [Vytvoření kompletního řešení IoT pomocí SQL Edge pomocí IoT Edge](tutorial-deploy-azure-resources.md)
- [Streamování dat ve službě Azure SQL Edge](stream-data.md)
- [Řešení potíží s chybami nasazení](troubleshoot.md)
