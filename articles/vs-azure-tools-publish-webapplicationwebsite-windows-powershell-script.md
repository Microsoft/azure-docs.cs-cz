---
title: Publikování – WebApplicationWebSite (skript prostředí Windows PowerShell) | Dokumentace Microsoftu
description: Zjistěte, jak publikovat projekt webu na web Azure. Tento skript vytvoří požadované prostředky ve vašem předplatném Azure, pokud ještě neexistují.
services: visual-studio-online
author: ghogen
manager: douge
assetId: 63cfaa2d-f04d-40dc-8677-345385c278d5
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: 7199e382721fed786157b3dcc1d5930d5fdd49cb
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969486"
---
# <a name="publish-webapplicationwebsite-windows-powershell-script"></a>Publikování – WebApplicationWebSite (skript Windows Powershellu)
## <a name="syntax"></a>Syntaxe
Webový projekt se publikuje na web Azure. Skript vytvoří požadované prostředky ve vašem předplatném Azure, pokud ještě neexistují.

    Publish-WebApplicationWebSite
    –Configuration <configuration>
    -SubscriptionName <subscriptionName>
    -WebDeployPackage <packageName>
    -DatabaseServerPassword @{Name = "name"; Password = "password"}
    -SendHostMessagesToOutput
    -Verbose


## <a name="configuration"></a>Konfigurace
Cesta ke konfiguračnímu souboru JSON popisující podrobnosti o nasazení.

| Parametr | Výchozí hodnota |
| --- | --- |
| Aliasy |žádný |
| Povinné? |true (pravda) |
| Umístění |s názvem |
| Výchozí hodnota |žádný |
| Přijmout kanálový vstup? |false (nepravda) |
| Přijímat zástupné znaky? |false (nepravda) |

## <a name="subscriptionname"></a>Název odběru
Název předplatného Azure, který chcete vytvořit web v.

| Parametr | Výchozí hodnota |
| --- | --- |
| Aliasy |žádný |
| Povinné? |false (nepravda) |
| Umístění |s názvem |
| Výchozí hodnota |žádný |
| Přijmout kanálový vstup? |false (nepravda) |
| Přijímat zástupné znaky? |false (nepravda) |

## <a name="webdeploypackage"></a>WebDeployPackage
Cesta k balíčku pro nasazení webu k publikování na web. Tento balíček můžete vytvořit pomocí Průvodce publikováním webu v sadě Visual Studio. Další informace najdete v tématu [Začínáme s Azure Cloud Services a ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089).

| Parametr | Výchozí hodnota |
| --- | --- |
| Aliasy |žádný |
| Povinné? |false (nepravda) |
| Umístění |s názvem |
| Výchozí hodnota |žádný |
| Přijmout kanálový vstup? |false (nepravda) |
| Přijímat zástupné znaky? |false (nepravda) |

## <a name="databaseserverpassword"></a>DatabaseServerPassword
Uživatelské jméno a heslo pro databázi SQL v Azure.

| Parametr | Výchozí hodnota |
| --- | --- |
| Aliasy |žádný |
| Povinné? |false (nepravda) |
| Umístění |s názvem |
| Výchozí hodnota |žádný |
| Přijmout kanálový vstup? |false (nepravda) |
| Přijímat zástupné znaky? |false (nepravda) |

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
Pokud je hodnota true, tisk zpráv ze skriptu do výstupního datového proudu.

| Parametr | Výchozí hodnota |
| --- | --- |
| Aliasy |žádný |
| Povinné? |false (nepravda) |
| Umístění |s názvem |
| Výchozí hodnota |false (nepravda) |
| Přijmout kanálový vstup? |false (nepravda) |
| Přijímat zástupné znaky? |false (nepravda) |

## <a name="remarks"></a>Poznámky
Úplné vysvětlení toho, jak použít skript k vytvoření vývojových a testovacích prostředí, najdete v části [pomocí skriptů Windows Powershellu k publikování do vývojových a testovacích prostředí](vs-azure-tools-publishing-using-powershell-scripts.md).

Konfigurační soubor JSON má podrobnosti o co se má nasadit. Obsahuje informace, které jste zadali při vytváření projektu, jako jsou název a uživatelské jméno pro web. Zahrnuje také databáze, kterou chcete zřídit, pokud existuje. Následující kód ukazuje příklad konfigurace souboru JSON:

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

Můžete upravit konfigurační soubor JSON, chcete-li změnit, co se nasadí. Části webu je povinný, ale databáze část je nepovinná.

## <a name="next-steps"></a>Další postup
Další informace najdete v tématu [Publish-WebApplicationVM (skript Windows Powershellu)](vs-azure-tools-publish-webapplicationvm.md)

