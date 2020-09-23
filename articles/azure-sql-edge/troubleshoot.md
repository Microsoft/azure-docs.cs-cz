---
title: Řešení potíží s nasazeními Azure SQL Edge
description: Další informace o možných chybách při nasazení Azure SQL Edge
keywords: SQL Edge, řešení potíží, chyby nasazení
services: sql-edge
ms.service: sql-edge
ms.topic: troubleshooting
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: d8da8bcf3d2bb6b2af2b5c69ce003289d83d3884
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90936210"
---
# <a name="troubleshooting-azure-sql-edge-deployments"></a>Řešení potíží s nasazeními Azure SQL Edge 

Tento článek poskytuje informace o možných chybách, které se vyskytly při nasazování a používání kontejnerů Azure SQL Edge, a poskytuje techniky řešení potíží, které vám pomohou tyto problémy vyřešit. 

Azure SQL Edge podporuje dva modely nasazení: 
- Připojené nasazení prostřednictvím Azure IoT Edge: Azure SQL Edge je k dispozici na Azure Marketplace a je možné ho nasadit jako modul pro [Azure IoT Edge](../iot-edge/about-iot-edge.md). Další informace najdete v tématu [nasazení serveru Azure SQL Edge](deploy-portal.md).<br>

- Odpojené nasazení: image kontejneru Azure SQL Edge se dají z Docker Hub načíst a nasadit buď jako samostatný kontejner Docker, nebo do clusteru Kubernetes. Další informace najdete v tématu [nasazení Azure SQL Edge pomocí Docker](disconnected-deployment.md) a [nasazení kontejneru Edge SQL Azure v Kubernetes](deploy-kubernetes.md).

## <a name="troubleshooting-iot-edge-device-and-deployments"></a>Řešení potíží s IoT Edgemi zařízeními a nasazeními

Pokud se při nasazování Edge SQL Edge přes Azure IoT Edge zobrazí chyba, ujistěte se, že `iotedge` je služba správně nakonfigurovaná a spuštěná. Následující dokumenty můžou být užitečné při řešení potíží souvisejících s Azure IoT Edge:
- [Běžné problémy a jejich řešení pro Azure IoT Edge](../iot-edge/troubleshoot-common-errors.md).
- [Řešení potíží s IoT Edgem zařízením](../iot-edge/troubleshoot.md)

## <a name="docker-command-errors"></a>Chyby příkazu Docker

Pokud se zobrazí chyby pro jakékoli `docker` příkazy, ujistěte se, že je služba Docker spuštěná, a zkuste spustit se zvýšenými oprávněními.

Například v systému Linux se při spouštění příkazů může zobrazit následující chyba `docker` :

```output
Cannot connect to the Docker daemon. Is the docker daemon running on this host?
```

Pokud se tato chyba zobrazí v systému Linux, zkuste spustit stejné příkazy, na kterých je spuštěný `sudo` . Pokud se to nepovede, ověřte, že je spuštěná služba Docker, a v případě potřeby ji spusťte.

```bash
sudo systemctl status docker
sudo systemctl start docker
```

V systému Windows ověřte, že spouštíte PowerShell nebo příkazový řádek jako správce.

## <a name="azure-sql-edge-container-startup-errors"></a>Chyby při spuštění kontejneru Azure SQL Edge

Pokud se nespustí kontejner Edge SQL, zkuste následující testy:

- Pokud používáte Azure IoT Edge, ujistěte se, že se image modulu stáhly úspěšně a že jsou v manifestu modulu správně zadané proměnné prostředí a možnosti vytváření kontejnerů.

- Pokud používáte nasazení na základě Docker nebo Kubernetes, ujistěte se, že `docker run` je příkaz správně vytvořený. Další informace najdete v tématu [nasazení Azure SQL Edge pomocí Docker](disconnected-deployment.md) a [nasazení kontejneru Azure SQL Edge do Kubernetes](deploy-kubernetes.md).

- Pokud se zobrazí chyba `failed to create endpoint CONTAINER_NAME on network bridge. Error starting proxy: listen tcp 0.0.0.0:1433 bind: address already in use.` , například, pokoušíte se namapovat port kontejneru 1433 na port, který je již používán. K tomu může dojít, pokud používáte SQL Edge místně na hostitelském počítači. K tomu může dojít také v případě, že spustíte dva kontejnery SQL Edge a pokusíte se je namapovat na stejný port hostitele. Pokud k tomu dojde, použijte `-p` parametr pro mapování portu kontejneru 1433 na jiný port hostitele. Příklad: 

    ```bash
    sudo docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=yourStrong(!)Password' -p 1433:1433 --name azuresqledge -d mcr.microsoft.com/azure-sql-edge-developer.
    ```

- Pokud se zobrazí chyba `Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.30tdout=1&tail=all: dial unix /var/run/docker.sock: connect: permission denied` , například při pokusu o spuštění kontejneru, přidejte uživatele do skupiny Docker v Ubuntu. Pak se odhlaste a znovu přihlaste, protože tato změna bude mít vliv na nové relace. 

   ```bash
    usermod -aG docker $USER
   ```

- Zkontrolujte, zda neexistují žádné chybové zprávy z kontejneru.

   ```bash
   docker logs e69e056c702d
   ```

- Pokud používáte žádný software pro správu kontejnerů, ujistěte se, že podporuje procesy kontejneru spuštěné jako kořenové. Proces soubor sqlservr v kontejneru spouští jako kořenový adresář.

- Ve výchozím nastavení se kontejnery Azure SQL Edge spouštějí jako nerootový uživatel s názvem `mssql` . Pokud k uchování dat používáte přípojné body nebo datové svazky, ujistěte se, že `mssql` má uživatel příslušná oprávnění ke svazku. Další informace najdete v tématu [Spustit jako nerootový uživatel](configure.md#run-azure-sql-edge-as-non-root-user) a [zachovat data](configure.md#persist-your-data).

- Pokud se Váš kontejner Docker pro SQL Edge ukončí hned po spuštění, podívejte se do protokolů Docker. Pokud v systému Windows používáte PowerShell s `docker run` příkazem, používejte místo jednoduchých uvozovek dvojité uvozovky. Pomocí prostředí PowerShell Core použijte jednoduché uvozovky.

- Zkontrolujte [protokoly chyb SQL Edge](#errorlogs).

## <a name="sql-edge-connection-failures"></a>Selhání připojení k Edge SQL

Pokud se nemůžete připojit k instanci serveru SQL Edge spuštěné v kontejneru, vyzkoušejte následující testy:

- Ujistěte se, že váš kontejner okraje SQL je spuštěný, a to tak, že se podíváte na sloupec **stav** `docker ps -a` výstupu. Pokud ne, použijte `docker start <Container ID>` k jeho spuštění.

- Pokud jste namapovali na jiný než výchozí port hostitele (ne 1433), ujistěte se, že zadáváte port v připojovacím řetězci. Ve výstupu se zobrazí ve sloupci **porty** ve výstupu možnost mapování portů `docker ps -a` . Další informace o připojení k Edge SQL Azure najdete v tématu [připojení a dotazování Azure SQL Edge](connect.md) .

- Pokud jste už dříve nasadili Edge SQL s namapovaným objemem dat nebo kontejnerem datových svazků a teď používáte existující namapovaný datový svazek nebo kontejner datových svazků, bude SQL Edge ignorovat hodnotu `MSSQL_SA_PASSWORD` proměnné prostředí. Místo toho se použije dřív nakonfigurované heslo uživatele SA. K tomu dochází, protože SQL Edge znovu používá stávající soubory hlavních databází v namapovaném svazku nebo kontejneru datových svazků. Pokud narazíte na tento problém, můžete použít následující možnosti:

    - Pokud je stále k dispozici, připojte se pomocí dříve použitého hesla.
    - Nakonfigurujte SQL Edge na použití jiného namapovaného svazku nebo kontejneru datových svazků.
    - Odeberte existující hlavní soubory databáze (Master. mdf a mastlog. mdf) z namapovaného svazku nebo kontejneru datových svazků.

- Zkontrolujte [protokoly chyb SQL Edge](#errorlogs).

## <a name="sql-edge-setup-and-error-logs"></a><a id="errorlogs"></a> Nastavení a protokoly chyb SQL Edge

Ve výchozím nastavení jsou protokoly chyb SQL Edge k dispozici v adresáři **/var/opt/MSSQL/log** v rámci kontejneru a lze k nim přihlašovat pomocí některého z následujících způsobů:

- Pokud jste připojili hostitelský adresář k **/var/opt/MSSQL** při vytváření kontejneru, můžete místo toho Hledat v podadresáři **protokolu** na mapované cestě na hostiteli.
- Pomocí interaktivního příkazového řádku se připojte ke kontejneru. Pokud kontejner není spuštěný, nejdřív spusťte kontejner. Pak pomocí interaktivního příkazového řádku Zkontrolujte protokoly. ID kontejneru můžete získat spuštěním příkazu `docker ps` .

    ```bash
    docker start <ContainerID>
    docker exec -it <ContainerID> "/bin/bash"
    ```

    Z relace bash v kontejneru spusťte následující příkazy:

    ```bash
    cd /var/opt/mssql/log
    cat errorlog
    ```
- Pokud je kontejner Edge SQL v provozu a Vy se můžete připojit k instanci pomocí klientských nástrojů, pak můžete použít uloženou proceduru `sp_readerrorlog` pro čtení obsahu protokolu chyb SQL Edge.

## <a name="execute-commands-in-a-container"></a>Spouštění příkazů v kontejneru

Pokud máte spuštěný kontejner, můžete příkazy v rámci kontejneru spustit z hostitelského terminálu.

Chcete-li získat spuštění ID kontejneru:

```bash
docker ps -a
```

Spuštění terminálu bash v rámci spuštění kontejneru:

```bash
docker exec -it <Container ID> /bin/bash
```

Nyní můžete spouštět příkazy, jako byste je spouštěli v terminálu uvnitř kontejneru. Po dokončení zadejte `exit` . Tím se ukončí relace interaktivního příkazu, ale váš kontejner pokračuje v běhu.

## <a name="troubleshooting-issues-with-data-streaming"></a>Řešení potíží s datovým proudem dat

Ve výchozím nastavení se protokoly modulu streamování pro Azure SQL Edge zapisují do souboru s názvem `current` v adresáři **/var/opt/MSSQL/log/Services/00000001-0000-0000-0000-000000000000** . K souboru lze získat přímý odkaz přímo prostřednictvím namapovaného svazku nebo kontejneru datových svazků nebo spuštěním interaktivní relace příkazového řádku do kontejneru SQL Edge. 

Pokud se navíc můžete připojit k instanci SQL Edge pomocí klientských nástrojů, můžete použít následující příkaz T-SQL pro přístup k aktuálnímu protokolu modulu streamování. 

```sql

select value as log, try_convert(DATETIME2, substring(value, 0, 26)) as timestamp 
from 
    STRING_SPLIT
    (
        (
            select BulkColumn as logs
            FROM OPENROWSET (BULK '/var/opt/mssql/log/services/00000001-0000-0000-0000-000000000000/current', SINGLE_CLOB) MyFile
        ),
        CHAR(10)
    ) 
where datalength(value) > 0

```

### <a name="enabling-verbose-logging"></a>Povolení podrobného protokolování

Pokud výchozí úroveň protokolování pro modul streamování neposkytuje dostatek informací, protokolování ladění pro modul streamování se dá v SQL Edge povolit. Chcete-li povolit protokolování ladění, přidejte `RuntimeLogLevel=debug` proměnnou prostředí do nasazení SQL Edge. Po povolení protokolování ladění se pokuste o reprodukování problému a zjištění všech relevantních zpráv nebo výjimek v protokolech. 



## <a name="next-steps"></a>Další kroky

- [Machine Learning a umělá logika s ONNXem v SQL Edge](onnx-overview.md)
- [Streamování dat ve službě Azure SQL Edge](stream-data.md)
- [Uchovávání a čištění dat](data-retention-overview.md)
- [Mezery při vyplňování a imputace chybějících hodnot](imputing-missing-values.md)







