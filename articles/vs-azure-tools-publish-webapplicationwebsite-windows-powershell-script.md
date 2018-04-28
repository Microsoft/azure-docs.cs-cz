---
title: Publikování-WebApplicationWebSite (skript prostředí Windows PowerShell) | Microsoft Docs
description: Zjistěte, jak publikovat webového projektu do webové stránky Azure. Tento skript vytvoří požadované prostředky ve vašem předplatném Azure, pokud ještě neexistují.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 63cfaa2d-f04d-40dc-8677-345385c278d5
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: b540734f17ed11e4c438e1248ed9612fb892e89a
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="publish-webapplicationwebsite-windows-powershell-script"></a>Publikování-WebApplicationWebSite (skript prostředí Windows PowerShell)
## <a name="syntax"></a>Syntaxe
Publikuje webového projektu na web Azure. Pokud ještě neexistují, vytvoří skript ve vašem předplatném Azure požadované prostředky.

    Publish-WebApplicationWebSite
    –Configuration <configuration>
    -SubscriptionName <subscriptionName>
    -WebDeployPackage <packageName>
    -DatabaseServerPassword @{Name = "name"; Password = "password"}
    -SendHostMessagesToOutput
    -Verbose


## <a name="configuration"></a>Konfigurace
Cesta k souboru konfigurace JSON, který popisuje podrobnosti o nasazení.

| Parametr | Výchozí hodnota |
| --- | --- |
| Aliasy |Žádné |
| Povinné? |true (pravda) |
| Pozice |S názvem |
| Výchozí hodnota |Žádné |
| Přijímat kanálu vstup? |false (nepravda) |
| Přijímat zástupné znaky? |false (nepravda) |

## <a name="subscriptionname"></a>Název odběru
Název předplatného Azure, který chcete vytvořit web v.

| Parametr | Výchozí hodnota |
| --- | --- |
| Aliasy |Žádné |
| Povinné? |false (nepravda) |
| Pozice |S názvem |
| Výchozí hodnota |Žádné |
| Přijímat kanálu vstup? |false (nepravda) |
| Přijímat zástupné znaky? |false (nepravda) |

## <a name="webdeploypackage"></a>WebDeployPackage
Cesta k balíčku pro nasazení webu k publikování na web. Tento balíček můžete vytvořit pomocí Průvodce Publikovat Web v sadě Visual Studio. Další informace najdete v tématu [Začínáme s Azure Cloud Services a technologií ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089).

| Parametr | Výchozí hodnota |
| --- | --- |
| Aliasy |Žádné |
| Povinné? |false (nepravda) |
| Pozice |S názvem |
| Výchozí hodnota |Žádné |
| Přijímat kanálu vstup? |false (nepravda) |
| Přijímat zástupné znaky? |false (nepravda) |

## <a name="databaseserverpassword"></a>DatabaseServerPassword
Uživatelské jméno a heslo pro databázi SQL v Azure.

| Parametr | Výchozí hodnota |
| --- | --- |
| Aliasy |Žádné |
| Povinné? |false (nepravda) |
| Pozice |S názvem |
| Výchozí hodnota |Žádné |
| Přijímat kanálu vstup? |false (nepravda) |
| Přijímat zástupné znaky? |false (nepravda) |

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
V případě hodnoty true tiskových zpráv ze skriptu do výstupního datového proudu.

| Parametr | Výchozí hodnota |
| --- | --- |
| Aliasy |Žádné |
| Povinné? |false (nepravda) |
| Pozice |S názvem |
| Výchozí hodnota |false (nepravda) |
| Přijímat kanálu vstup? |false (nepravda) |
| Přijímat zástupné znaky? |false (nepravda) |

## <a name="remarks"></a>Poznámky
Podrobné informace o tom, jak vytvořit pomocí skriptu najdete v prostředí vývoje a testování [pomocí skriptů prostředí PowerShell systému Windows k publikování pro vývoj a testovací prostředí](vs-azure-tools-publishing-using-powershell-scripts.md).

Konfigurační soubor JSON Určuje podrobnosti co je k nasazení. Obsahuje informace, kterou jste zadali při vytváření projektu, například název a uživatelské jméno pro web. Zahrnuje také databázi a zřizovat, pokud existuje. Následující kód ukazuje konfigurační soubor JSON příklad:

    {
        "environmentSettings": {
            "webSite": {
                "name": "WebApplication10554",
                "location": "West US"
            },
            "databases": [
                {
                    "connectionStringName": "DefaultConnection",
                    "databaseName": "WebApplication10554_db",
                    "serverName": "iss00brc88",
                    "user": "sqluser2",
                    "password": "",
                    "edition": "",
                    "size": "",
                    "collation": "",
                    "location": "West US"
                }
            ]
        }
    }

Můžete upravit konfigurační soubor JSON změnit, co je nasazen. Části webu není požadována, ale část databáze je volitelný.

## <a name="next-steps"></a>Další postup
Další informace najdete v tématu [Publish-WebApplicationVM (skript prostředí Windows PowerShell)](vs-azure-tools-publish-webapplicationvm.md)

