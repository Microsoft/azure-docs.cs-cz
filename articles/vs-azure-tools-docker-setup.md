---
title: Konfigurace hostitele Docker s VirtualBox | Microsoft Docs
description: Podrobné pokyny ke konfiguraci výchozí instance Docker pomocí Docker počítač a VirtualBox
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
ms.openlocfilehash: 11e238fa901a164df1dfd896e38df828601e650b
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30190394"
---
# <a name="configure-a-docker-host-with-virtualbox"></a>Konfigurace hostitele Docker s VirtualBox
## <a name="overview"></a>Přehled
Tento článek vás provede konfiguraci výchozí instance Docker pomocí Docker počítač a VirtualBox. Pokud používáte [Docker pro systém Windows beta](http://beta.docker.com/), tato konfigurace není nutné.

## <a name="prerequisites"></a>Požadavky
Je potřeba nainstalovat následující nástroje.

* [Sada nástrojů docker](https://github.com/docker/toolbox/releases)

## <a name="configuring-the-docker-client-with-windows-powershell"></a>Konfigurace klienta Docker v prostředí Windows PowerShell
Konfigurace klienta Docker, jednoduše otevřete prostředí Windows PowerShell a proveďte následující kroky:

1. Vytvořte výchozí instance docker hostitele.
   
    ```PowerShell
    docker-machine create --driver virtualbox default
    ```
2. Ověřte, zda že je výchozí instanci nakonfigurovaná a spuštěná. (Byste měli vidět instanci s názvem výchozí systémem.
   
    ```PowerShell
    docker-machine ls 
    ```
   
    ![výstup ls počítač docker][0]
3. Nastavit výchozí jako aktuální hostitel a nakonfigurujte své prostředí.
   
    ```PowerShell
    docker-machine env default | Invoke-Expression
    ```
4. Zobrazte aktivní Docker kontejnerů. V seznamu by měla být prázdná.
   
    ```PowerShell
    docker ps
    ```
   
    ![výstup ps docker][1]

> [!NOTE]
> Pokaždé, když restartujete vývojovém počítači, budete muset restartovat místní docker hostiteli.
> K tomuto účelu vydejte následující příkaz na příkazovém řádku: `docker-machine start default`.
> 
> 

[0]: ./media/vs-azure-tools-docker-setup/docker-machine-ls.png
[1]: ./media/vs-azure-tools-docker-setup/docker-ps.png
