---
title: Řešení potíží s chybami klienta Dockeru na Windows s použitím sady Visual Studio | Dokumentace Microsoftu
description: Řešení problémů, které zaznamenáte při používání sady Visual Studio k vytvoření a nasazení webové aplikace do Docker ve Windows pomocí sady Visual Studio 2017.
services: azure-container-service
documentationcenter: na
author: devinb
manager: douge
editor: ''
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 10/13/2017
ms.author: devinb
ms.openlocfilehash: cd88dec2ad79ad9f4b4c004866060be86b777cd9
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2018
ms.locfileid: "49311180"
---
# <a name="troubleshoot-visual-studio-2017-development-with-docker"></a>Řešení potíží s Visual Studio 2017 vývoj aplikací pomocí Dockeru

Když pracujete se sadou Visual Studio Tools for Docker, může dojít k potížím při sestavování nebo ladění aplikace. Níže jsou některé běžné kroků pro řešení potíží.

## <a name="volume-sharing-is-not-enabled-enable-volume-sharing-in-the-docker-ce-for-windows-settings--linux-containers-only"></a>Sdílení svazků není povolené. Povolte sdílení svazků v nastavení Dockeru CE pro Windows (pouze pro kontejnery Linuxu)

K vyřešení tohoto problému:

1. Klikněte pravým tlačítkem na **Docker pro Windows** v oznamovací oblasti a pak vyberte **nastavení**.
1. Vyberte **sdílené jednotky** a sdílené složky systémové jednotce spolu s na jednotku, ve které se nachází projektu.

> [!NOTE]
> Pokud se zdá, že sdílené soubory, můžete stále chcete-li znovu povolit sdílení svazků, klikněte na odkaz "Resetovat přihlašovací údaje …" v dolní části dialogového okna. Chcete-li pokračovat po resetování přihlašovacích údajů, budete muset restartovat Visual Studio.

![sdílené jednotky](./media/vs-azure-tools-docker-troubleshooting-docker-errors/shareddrives.png)

## <a name="mounts-denied"></a>Připojení byl odepřen

Při použití Dockeru pro macOS, pravděpodobně dojde k chybě odkazující na složku /usr/local/share/dotnet/sdk/NuGetFallbackFolder. Přidáte složku na kartě Sdílení souborů v Dockeru.

## <a name="unable-to-start-debugging"></a>Nelze spustit ladění

Jedním z důvodů může souviset s tím, že komponenty zastaralé ladění ve složce profilu uživatele. Spusťte následující příkazy k odebrání těchto složek tak, aby se stáhnou nejnovější ladění komponenty na příští relaci ladění.

- del %userprofile%\vsdbg
- del %userprofile%\onecoremsvsmon

## <a name="errors-specific-to-networking-when-debugging-your-application"></a>Chyby specifické pro sítě při ladění aplikace

Pokuste se spustit skript ke stažení z [sítě hostitele kontejneru vyčištění](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-server-container-tools/CleanupContainerHostNetworking), která obnoví komponenty související se sítí na hostitelském počítači.


## <a name="microsoftdockertools-github-repo"></a>Úložiště GitHub Microsoft/DockerTools

V případě jiných problémů dojde, naleznete v tématu [Microsoft/DockerTools](https://github.com/microsoft/dockertools/issues) problémy.