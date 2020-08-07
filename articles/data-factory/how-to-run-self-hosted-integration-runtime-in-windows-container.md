---
title: Spuštění Integration Runtime v místním prostředí v kontejneru Windows
description: Přečtěte si, jak spouštět Integration Runtime v kontejneru Windows v kontejneru pro místní hostování.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: d6f292ff89a70de90e6b86f19f73de26963d997f
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2020
ms.locfileid: "87927565"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Spuštění Integration Runtime v místním prostředí v kontejneru Windows

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

V tomto článku se dozvíte, jak spouštět Integration Runtime v kontejneru Windows v kontejneru pro místní hostování.
Azure Data Factory přináší oficiální podporu kontejnerů Windows pro místní hostování Integration Runtime. Zdrojový kód sestavení Docker můžete stáhnout a kombinovat proces sestavení a spuštění ve vlastním kanálu průběžného doručování. 

## <a name="prerequisites"></a>Požadavky 
- [Požadavky na kontejner Windows](https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker verze 2,3 a novější 
- 4.11.7512.1 v místním prostředí Integration Runtime verze a novější 
## <a name="get-started"></a>Začínáme 
1.  Nainstalovat Docker a povolit kontejner Windows 
2.  Stáhněte zdrojový kód z adresy https://github.com/Azure/Azure-Data-Factory-Integration-Runtime-in-Windows-Container.
3.  Stáhněte si nejnovější verzi SHIR ve složce SHIR 
4.  Otevřete složku v prostředí: 
```console
cd "yourFolderPath"
```

5.  Sestavit bitovou kopii Windows Docker: 
```console
docker build . -t "yourDockerImageName" 
```
6.  Spustit kontejner Docker: 
```console
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> AUTH_KEY je pro tento příkaz povinné. NODE_NAME ENABLE_HA a HA_PORT jsou volitelné. Pokud hodnotu nenastavíte, použije příkaz výchozí hodnoty. Výchozí hodnota ENABLE_HA je false a HA_PORT je 8060.

## <a name="container-health-check"></a>Kontroly stavu kontejneru 
Po uplynutí 120 sekund počátečního období se kontrola stavu spustí pravidelně každých 30 sekund. Poskytne modulu kontejneru stav IR. 

## <a name="limitations"></a>Omezení
V současné době nepodporujeme následující funkce při spouštění Integration Runtime v kontejnerech Windows v místním prostředí:
- Proxy server HTTP 
- Šifrovaná komunikace mezi uzly a certifikátem TLS/SSL 
- Generování a importování zálohy 
- Služba démon 
- Automatická aktualizace 

### <a name="next-steps"></a>Další kroky
- Projděte si [koncept prostředí Integration runtime v Azure Data Factory](https://docs.microsoft.com/azure/data-factory/concepts-integration-runtime).
- Naučte se [vytvořit místní prostředí Integration runtime v Azure Portal](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime).


