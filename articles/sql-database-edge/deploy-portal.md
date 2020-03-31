---
title: Nasazení náhledu sql database edge preview pomocí portálu Azure | Dokumenty společnosti Microsoft
description: Zjistěte, jak nasadit Azure SQL Database Edge pomocí webu Azure Portal
keywords: nasazení okraje databáze SQL
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 11/04/2019
ms.openlocfilehash: 9da922de38d820864b3f83de80fe64eb3ac792e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246717"
---
# <a name="deploy-azure-sql-database-edge-preview"></a>Nasazení azure SQL database edge preview

Azure SQL Database Edge Preview je relační databázový stroj optimalizovaný pro nasazení IoT a Azure IoT Edge. Poskytuje funkce pro vytvoření vysoce výkonné vrstvy pro ukládání a zpracování dat pro aplikace a řešení IoT. Tento úvodní příručka vám ukáže, jak začít s vytvářením modulu Azure SQL Database Edge prostřednictvím Azure IoT Edge pomocí portálu Azure.

## <a name="before-you-begin"></a>Než začnete

* Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/).
* Přihlaste se k [portálu Azure](https://portal.azure.com/).
* Odešlete [zde](https://azure.microsoft.com/services/sql-database-edge/#contact)požadavek , aby bylo předplatné povoleno pro nasazení sql database edge.
* Vytvořte [Azure IoT Hub](../iot-hub/iot-hub-create-through-portal.md).
* Zaregistrujte [zařízení IoT Edge z webu Azure Portal](../iot-edge/how-to-register-device-portal.md).
* Připravte zařízení IoT Edge k [nasazení modulu IoT Edge z webu Azure Portal](../iot-edge/how-to-deploy-modules-portal.md).

> [!NOTE]
> Pokud chcete nasadit virtuální počítač Azure Linux jako zařízení IoT Edge, přečtěte si tento [průvodce rychlým startem](../iot-edge/quickstart-linux.md).

## <a name="deploy-sql-database-edge-module-from-azure-marketplace"></a>Nasazení modulu SQL Database Edge z Azure Marketplace

Azure Marketplace je online trh s aplikacemi a službami, kde můžete procházet širokou škálu podnikových aplikací a řešení, které jsou certifikované a optimalizované pro provoz v Azure, včetně [modulů IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules). Azure SQL Database Edge se dá nasadit na hraniční zařízení prostřednictvím tržiště.

1. Najděte modul Azure SQL Database Edge na Azure Marketplace.<br><br>

   ![Sql Database Edge v MarketPlace](media/deploy-portal/find-offer-marketplace.png)

2. Vyberte softwarový plán, který nejlépe odpovídá vašim požadavkům, a klepněte na tlačítko **Vytvořit**. <br><br>

   ![Výběr správného softwarového plánu](media/deploy-portal/pick-correct-plan.png)

3. Na stránce Modul cílové zařízení pro okrajIOVy IoT zadejte následující podrobnosti a klikněte na **Vytvořit.**

   |**Pole**  |**Popis**  |
   |---------|---------|
   |Předplatné  |  Předplatné Azure, pod kterým bylo vytvořeno služby IoT Hub |
   |IoT Hub   |  Název služby IoT Hub, kde je zařízení IoT Edge registrované, a pak vyberte možnost "Nasadit do zařízení"|
   |Název zařízení IoT Edge  |  Název zařízení IoT Edge, na kterém by se nasazovala funkce SQL Database Edge |

4. Na stránce **Nastavit moduly** přejděte do části modulů nasazení a klikněte na **Konfigurovat** podle modulu SQL Database Edge. 

5. V podokně **Vlastní moduly IoT Edge** zadejte požadované hodnoty pro proměnné prostředí a/nebo přizpůsobte možnosti vytvoření a požadované vlastnosti modulu. Úplný seznam podporovaných proměnných prostředí naleznete v části [Proměnné prostředí kontejneru serveru SQL Server](/sql/linux/sql-server-linux-configure-environment-variables/).

   |**Parametr**  |**Popis**|
   |---------|---------|
   | Name (Název) | Název modulu. |
   |SA_PASSWORD  | Zadejte silné heslo pro účet správce SQL Database Edge. |
   |MSSQL_LCID   | Nastaví ID jazyka, které se má použít pro SQL Server. Například 1036 je francouzština. |
   |MSSQL_COLLATION | Nastaví výchozí řazení pro SQL Server. Toto nastavení přepíše výchozí mapování ID jazyka (LCID) na kolaci. |

   > [!NOTE]
   > Neměňte ani neaktualizujte **identifikátor URI obrázku** nebo **nastavení ACCEPT_EULA** v modulu.

6. V podokně **Vlastní moduly IoT Edge** aktualizujte požadovanou hodnotu kontejneru pro **hostitelský port**. Pokud potřebujete nasadit více než jeden modul SQL DB Edge, nezapomeňte aktualizovat možnost připojení a vytvořit nový zdrojový & cílový pár pro trvalý svazek. Další informace o připojení a svazku najdete v najdete [v dokumentaci k dockeru použití svazků.](https://docs.docker.com/storage/volumes/) 

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

7. V podokně **Vlastní moduly IoT Edge** aktualizujte *požadované vlastnosti dvojčete modulu Set* tak, aby zahrnovaly umístění balíčku SQL a informace o úloze analýzy datového proudu. Tato dvě pole jsou volitelné a měly by být použity, pokud chcete nasadit modul SQL Database Edge s databází a úlohou streamování.

   ```json
       {
         "properties.desired":
         {
           "SqlPackage": "<Optional_DACPAC_ZIP_SAS_URL>",
           "ASAJobInfo": "<Optional_ASA_Job_ZIP_SAS_URL>"
         }
       }
   ```

8. V podokně **Vlastní moduly IoT Edge** nastavte *zásady restartování* na vždy a Požadovaný *stav* na spuštěný.
9. V podokně **Vlastní moduly IoT Edge** klepněte na tlačítko **Uložit**.
10. Na stránce **Nastavit moduly** klepněte na **tlačítko Další**.
11. Na **stránce Určit trasu (volitelné)** na stránce **Nastavit moduly** zadejte trasy pro komunikaci modulu do modulu nebo modulu ke komunikaci služby IoT Edge Hub, viz [Nasazení modulů a vytvoření tras v ioT Edge](../iot-edge/module-composition.md).
12. Klikněte na **Další**.
13. Klepněte na **tlačítko Odeslat**.

V tomto rychlém startu jste nasadili modul SQL Database Edge na zařízení IoT Edge.

## <a name="next-steps"></a>Další kroky

- [Strojové učení a umělá inteligence s ONNX v SQL Database Edge](onnx-overview.md).
- Vytváření komplexního řešení IoT s sql database edge pomocí IoT Edge.
