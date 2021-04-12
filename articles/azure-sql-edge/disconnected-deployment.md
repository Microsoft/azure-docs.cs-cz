---
title: Nasazení Azure SQL Edge pomocí Docker – Azure SQL Edge
description: Přečtěte si o nasazení Azure SQL Edge pomocí Docker
keywords: SQL Edge, kontejner, Docker
services: sql-edge
ms.service: sql-edge
ms.topic: quickstart
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: fce098767fffd36376399bbd9396699e3d9fbfd3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93392074"
---
# <a name="deploy-azure-sql-edge-with-docker"></a>Nasazení Azure SQL Edge s Docker

V tomto rychlém startu použijete Docker k vyžádání a spuštění image kontejneru Azure SQL Edge. Pak se pomocí **Sqlcmd** připojte k vytvoření první databáze a spuštění dotazů.

Tato image se skládá z Azure SQL Edge na základě Ubuntu 18,04. Dá se použít s modulem Docker 1.8 + v systému Linux nebo v Docker pro Mac/Windows.

## <a name="prerequisites"></a>Požadavky

- Docker Engine 1.8 + u jakékoli podporované distribuce systému Linux nebo Docker pro Mac/Windows. Další informace najdete v tématu [instalace Docker](https://docs.docker.com/engine/installation/). Vzhledem k tomu, že jsou image Azure SQL Edge založené na Ubuntu 18,04, doporučuje se použít hostitele Docker Ubuntu 18,04.
- Ovladač úložiště Docker **overlay2** . Toto je výchozí nastavení pro většinu uživatelů. Pokud zjistíte, že nepoužíváte tohoto poskytovatele úložiště a potřebujete ho změnit, přečtěte si pokyny a upozornění v [dokumentaci k Docker pro konfiguraci overlay2](https://docs.docker.com/storage/storagedriver/overlayfs-driver/#configure-docker-with-the-overlay-or-overlay2-storage-driver).
- Minimálně 10 GB místa na disku.
- Minimálně 1 GB paměti RAM.
- [Požadavky na hardware pro Azure SQL Edge](./features.md#hardware-support).


## <a name="pull-and-run-the-container-image"></a>Vyžádání a spuštění image kontejneru

Než začnete s následujícím postupem, ujistěte se, že jste v horní části tohoto článku vybrali preferované prostředí (bash, PowerShell nebo Cmd).

1. Vyžádat si image kontejneru Azure SQL Edge z Microsoft Container Registry.

    - Získání image kontejneru Azure SQL Edge
        ```bash
        sudo docker pull mcr.microsoft.com/azure-sql-edge:latest 
        ```

> [!NOTE]
> Pro příkazy bash v tomto článku `sudo` se používá. V macOS & Windows se nemusí sudo vyžadovat. Pokud v systému Linux nechcete ke spuštění Docker používat sudo, můžete nakonfigurovat skupinu Docker a přidat uživatele do této skupiny. Další informace najdete v tématu [kroky po instalaci pro Linux](https://docs.docker.com/engine/install/linux-postinstall/).

Předchozí příkaz vyžádá nejnovější image kontejneru Azure SQL Edge. Pokud chcete zobrazit všechny dostupné image, přečtěte si [stránku Azure-SQL-Egde Docker Hub](https://hub.docker.com/_/microsoft-azure-sql-edge).

2. Pokud chcete spustit image kontejneru s Docker, můžete použít následující příkaz z prostředí bash (Linux/macOS) nebo pomocí příkazového řádku PowerShellu se zvýšenými oprávněními.
    
    - Spuštění instance Edge Azure SQL, která běží jako vývojářská edice
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```

    - Spuštění instance Edge Azure SQL, která běží jako edice Premium
        ```bash
        sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -e 'MSSQL_PID=Premium' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge
        ```
    > [!NOTE]
    > Pokud používáte PowerShell ve Windows ke spuštění těchto příkazů, použijte místo jednoduchých uvozovek dvojité uvozovky.


    > [!NOTE]
    > Heslo by mělo následovat po výchozích zásadách pro hesla pro modul Microsoft SQL Database Engine, jinak kontejner nemůže nastavit modul SQL Engine a přestane fungovat. Ve výchozím nastavení musí mít heslo délku alespoň 8 znaků a musí obsahovat znaky ze tří z následujících čtyř sad: velká písmena, malá písmena, základní 10 číslice a symboly. Protokol chyb můžete prostudovat spuštěním příkazu [Docker logs](https://docs.docker.com/engine/reference/commandline/logs/) .
    
    Následující tabulka uvádí popis parametrů v předchozím `docker run` příkladu:

    | Parametr | Popis |
    |-----|-----|
    | **-e "ACCEPT_EULA = Y"** |  Nastavte proměnnou **ACCEPT_EULA** na libovolnou hodnotu pro potvrzení souhlasu s [licenční smlouvou s koncovým uživatelem](https://go.microsoft.com/fwlink/?linkid=2139274). Požadované nastavení pro obrázek Azure SQL Edge. |
    | **-e "MSSQL_SA_PASSWORD = yourStrong (!) Zadáno** | Zadejte vlastní silné heslo, které má aspoň 8 znaků a splňuje požadavky na [heslo pro Edge Azure SQL](/sql/relational-databases/security/password-policy). Požadované nastavení pro obrázek Azure SQL Edge. |
    | **-p 1433:1433** | Namapujte port TCP v hostitelském prostředí (první hodnota) pomocí portu TCP v kontejneru (druhá hodnota). V tomto příkladu Azure SQL Edge naslouchá na TCP 1433 v kontejneru a je vystavený portu 1433 na hostiteli. |
    | **--Name azuresqledge** | Zadejte vlastní název kontejneru, nikoli náhodně generovaný. Pokud spustíte více než jeden kontejner, nelze znovu použít stejný název. |
    | **-d** | Spuštění kontejneru na pozadí (démon) |

    Úplný seznam všech proměnných prostředí Azure SQL Edge najdete v tématu [Konfigurace Edge Azure SQL s proměnnými prostředí](configure.md#configure-by-using-environment-variables). Ke konfiguraci kontejnerů Edge Azure SQL můžete použít taky [soubor MSSQL. conf](configure.md#configure-by-using-an-mssqlconf-file) .

3. K zobrazení kontejnerů Docker použijte `docker ps` příkaz.
   
   ```bash
    sudo docker ps -a
   ```

4. Pokud se ve sloupci **stav** zobrazuje stav **zapnuto**, pak je v kontejneru spuštěno Azure SQL Edge a naslouchá na portu zadaném ve sloupci **porty** . Pokud se ve sloupci **stav** pro váš kontejner Azure SQL Edge zobrazuje jako **ukončené**, přečtěte si část věnované řešení potíží v dokumentaci k Azure SQL Edge.

    `-h`Parametr (název hostitele) je také užitečný, ale v tomto kurzu se pro jednoduchost nepoužívá. Tím se změní interní název kontejneru na vlastní hodnotu. Název, který se zobrazí, se vrátí v následujícím dotazu Transact-SQL:

    ```sql
    SELECT @@SERVERNAME,
        SERVERPROPERTY('ComputerNamePhysicalNetBIOS'),
        SERVERPROPERTY('MachineName'),
        SERVERPROPERTY('ServerName')
    ```

    Nastavení `-h` a `--name` na stejnou hodnotu je dobrým způsobem, jak snadno identifikovat cílový kontejner.

5. V posledním kroku změňte heslo SA, protože `SA_PASSWORD` je viditelný ve `ps -eax` výstupu a uložený v proměnné prostředí se stejným názvem. Viz následující postup.

## <a name="change-the-sa-password"></a>Změna hesla SA

Účet **SA** je správcem systému v instanci Azure SQL Edge, která se vytvoří během instalace. Po vytvoření kontejneru Azure SQL Edge `MSSQL_SA_PASSWORD` je zadaná proměnná prostředí zjistitelná spuštěním `echo $SA_PASSWORD` v kontejneru. Z bezpečnostních důvodů změňte heslo SA.

1. Vyberte silné heslo, které se má použít pro uživatele SA.

2. Použijte `docker exec` ke spuštění příkazu **Sqlcmd** ke změně hesla pomocí jazyka Transact-SQL. V následujícím příkladu nahraďte původní heslo, `<YourStrong!Passw0rd>` a nové heslo `<YourNewStrong!Passw0rd>` s vlastními hodnotami hesel.

   ```bash
   sudo docker exec -it azuresqledge /opt/mssql-tools/bin/sqlcmd \
      -S localhost -U SA -P "<YourStrong@Passw0rd>" \
      -Q 'ALTER LOGIN SA WITH PASSWORD="<YourNewStrong@Passw0rd>"'
   ```

## <a name="connect-to-azure-sql-edge"></a>Připojení k Edge SQL Azure

V následujících krocích se připojíte k Edge SQL serveru Azure pomocí nástroje příkazového řádku **Sqlcmd** v rámci služby Azure SQL Edge, který se nachází uvnitř kontejneru.

> [!NOTE]
> Nástroj Sqlcmd není k dispozici ve verzi ARM64 kontejnerů SQL Edge.

1. Pomocí `docker exec -it` příkazu spusťte interaktivní prostředí bash v běžícím kontejneru. V následujícím příkladu `azuresqledge` je název určený `--name` parametrem při vytváření kontejneru.

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

Můžete se také připojit k instanci Azure SQL Edge na počítači Docker z libovolného externího nástroje pro Linux, Windows nebo macOS, který podporuje připojení SQL. Další informace o připojení k kontejneru SQL Edge z vnějšku najdete v tématu [připojení a dotazování Azure SQL Edge](connect.md).

## <a name="remove-your-container"></a>Odebrat kontejner

Pokud chcete odebrat kontejner Edge Azure SQL, který jste použili v tomto kurzu, spusťte následující příkazy:

```bash
sudo docker stop azuresqledge
sudo docker rm azuresqledge
```

> [!WARNING]
> Zastavení a odebrání kontejneru trvale odstraní veškerá data z Azure SQL Edge v kontejneru. Pokud potřebujete zachovat data, [vytvořte a zkopírujte záložní soubor z kontejneru](backup-restore.md) nebo použijte [techniku pro trvalost dat kontejneru](configure.md#persist-your-data).

## <a name="next-steps"></a>Další kroky

- [Machine Learning a uměle inteligentní informace s ONNXem v SQL Edge](onnx-overview.md).
- [Vytvoření kompletního řešení IoT pomocí SQL Edge pomocí IoT Edge](tutorial-deploy-azure-resources.md).
- [Streamování dat ve službě Azure SQL Edge](stream-data.md)