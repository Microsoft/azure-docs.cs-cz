---
title: Spuštění Integration Runtime Self-Hosted v kontejneru Windows
description: Přečtěte si, jak spustit Self-Hosted Integration Runtime v kontejneru Windows.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/05/2020
ms.openlocfilehash: 2423d6bd29d893f9a27749dcc2b6d2af8a12e941
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478127"
---
# <a name="how-to-run-self-hosted-integration-runtime-in-windows-container"></a>Spuštění Integration Runtime Self-Hosted v kontejneru Windows

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

V tomto článku se dozvíte, jak spustit Self-Hosted Integration Runtime v kontejneru Windows.
Azure Data Factory přináší oficiální podporu kontejneru Windows Self-Hosted Integration Runtime. Zdrojový kód sestavení Docker můžete stáhnout a kombinovat proces sestavení a spuštění ve vlastním kanálu průběžného doručování. 

## <a name="prerequisites"></a>Požadavky 
- [Požadavky na kontejner Windows](/virtualization/windowscontainers/deploy-containers/system-requirements)
- Docker verze 2,3 a novější 
- Self-Hosted Integration Runtime verze 5.2.7713.1 a novější 
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
docker run -d -e NODE_NAME="irNodeName" -e AUTH_KEY="IR_AUTHENTICATION_KEY" -e ENABLE_HA=true -e HA_PORT=8060 "yourDockerImageName"    
```
> [!NOTE]
> AUTH_KEY je pro tento příkaz povinné. NODE_NAME ENABLE_HA a HA_PORT jsou volitelné. Pokud hodnotu nenastavíte, použije příkaz výchozí hodnoty. Výchozí hodnota ENABLE_HA je false a HA_PORT je 8060.

## <a name="container-health-check"></a>Kontroly stavu kontejneru 
Po uplynutí 120 sekund počátečního období se kontrola stavu spustí pravidelně každých 30 sekund. Poskytne modulu kontejneru stav IR. 

## <a name="limitations"></a>Omezení
V současné době nepodporujeme při spouštění Self-Hosted Integration Runtime v kontejneru Windows následující funkce:
- Proxy server HTTP 
- Šifrovaná komunikace mezi uzly a certifikátem TLS/SSL 
- Generování a importování zálohy 
- Služba démon 
- Automatická aktualizace 

### <a name="next-steps"></a>Další kroky
- Projděte si [koncept prostředí Integration runtime v Azure Data Factory](./concepts-integration-runtime.md).
- Naučte se [vytvořit místní prostředí Integration runtime v Azure Portal](./create-self-hosted-integration-runtime.md).