---
title: Konfigurace hostitele Docker pomocí VirtualBox | Dokumentace Microsoftu
description: Podrobné pokyny ke konfiguraci výchozí instanci Dockeru pomocí Docker Machine a VirtualBox
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: ''
ms.assetid: 0b1335a2-7720-42a8-8260-4e06fc00c9f6
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
ms.openlocfilehash: 7c78f27fa948c15202e83df4ed42a805a414a72e
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2018
ms.locfileid: "42060082"
---
# <a name="configure-a-docker-host-with-virtualbox"></a>Konfigurace hostitele Docker pomocí VirtualBox
## <a name="overview"></a>Přehled
Tento článek vás provede procesem konfigurace výchozí instanci Dockeru pomocí Docker Machine a VirtualBox. Pokud používáte [Docker pro Windows](https://www.docker.com/products/docker-desktop), tato konfigurace není nutné.

## <a name="prerequisites"></a>Požadavky
Tyto nástroje je potřeba nainstalovat.

* [Panel nástrojů dockeru](https://github.com/docker/toolbox/releases)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>Konfigurace klienta Dockeru pomocí Windows Powershellu
Abyste mohli nakonfigurovat klienta Dockeru, jednoduše otevřete prostředí Windows PowerShell a proveďte následující kroky:

1. Vytvoření hostitele docker výchozí instanci.
   
    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
2. Ověřte, že výchozí instancí je konfigurována a spuštěna. (Byste měli vidět instance s názvem "default" systémem.
   
    ```PowerShell
    docker-machine ls 
    ```
   
    ![docker-machine ls výstupu][0]
3. Nastavit výchozí jako aktuálního hostitele a nakonfigurovat vaše prostředí.
   
    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```
4. Zobrazte aktivní kontejnery Dockeru. Seznam by měl být prázdný.
   
    ```PowerShell
    docker ps
    ```
   
    ![docker ps výstupu][1]

> [!NOTE]
> Pokaždé, když restartujete vývojovém počítači, budete muset restartovat místní docker hostitele.
> Chcete-li to provést, vydejte následující příkaz z příkazového řádku: `docker-machine start default`.
> 
> 

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png
