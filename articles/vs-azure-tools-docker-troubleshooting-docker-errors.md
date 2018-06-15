---
title: Řešení potíží s chybami Docker klienta v systému Windows pomocí sady Visual Studio | Microsoft Docs
description: Řešení problémů, ke které dojde při použití sady Visual Studio k vytvoření a nasazení webové aplikace do Docker v systému Windows pomocí Visual Studio 2017.
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
ms.openlocfilehash: 90dd5df4a607568e2f3a60791da2948af7ce4e50
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2017
ms.locfileid: "24002926"
---
# <a name="troubleshoot-visual-studio-2017-development-with-docker"></a>Řešení potíží s Visual Studio 2017 vývoj pomocí nástroje Docker

Při práci s nástroji Visual Studio Tools pro Docker, může dojít k potížím při vytváření nebo ladění aplikace. Níže jsou některé běžné při řešení potíží.

## <a name="volume-sharing-is-not-enabled-enable-volume-sharing-in-the-docker-ce-for-windows-settings--linux-containers-only"></a>Není povoleno sdílení svazku. Povolit sdílení v nastavení Docker CE pro Windows (pouze Linux kontejnery) svazku

K vyřešení tohoto problému:

1. Klikněte pravým tlačítkem na **Docker pro systém Windows** v oznamovací oblasti a potom vyberte **nastavení**.
1. Vyberte **sdílené disky** a sdílet systémové jednotce spolu s jednotky, které se nachází projektu.

> [!NOTE]
> Pokud se zobrazí sdílené soubory, může stále muset klikněte na odkaz "Resetovat přihlašovací údaje..." v dolní části dialogového okna, chcete-li znovu povolit sdílení svazku.

![sdílené disky](./media/vs-azure-tools-docker-troubleshooting-docker-errors/shareddrives.png)

## <a name="unable-to-start-debugging"></a>Nelze spustit ladění

Jedním z důvodů může souviset s zastaralé ladění součásti ve složce uživatelského profilu. Spuštěním následujících příkazů odstraníte tyto složky, takže se stáhnou nejnovější ladění komponenty na další relaci ladění.

- del %userprofile%\vsdbg
- del %userprofile%\onecoremsvsmon

## <a name="errors-specific-to-networking-when-debugging-your-application"></a>Chyby, které jsou specifické pro sítě při ladění aplikace

Zkuste provádění skriptu ke stažení z [sítě hostitele kontejneru čištění](https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-server-container-tools/CleanupContainerHostNetworking), který se aktualizuje součásti související se sítí na hostitelském počítači.


## <a name="microsoftdockertools-github-repo"></a>Úložiště GitHub Microsoft/DockerTools

Všechny ostatní problémy narazíte najdete v tématu [Microsoft/DockerTools](https://github.com/microsoft/dockertools/issues) problémy.