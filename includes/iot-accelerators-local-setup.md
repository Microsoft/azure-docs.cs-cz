---
title: zahrnout soubor
description: zahrnout soubor
services: iot-accelerators
author: avneet723
ms.service: iot-accelerators
ms.topic: include
ms.date: 01/17/2019
ms.author: avneet723
ms.custom: include file
ms.openlocfilehash: 1f567b3d083853f9bb342bfad462e8545caa6480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175440"
---
## <a name="download-the-source-code"></a>Stáhnout zdrojový kód

Úložiště zdrojového kódu vzdáleného monitorování zahrnují zdrojový kód a konfigurační soubory Dockeru, které potřebujete ke spuštění bitových kopií Dockeru mikroslužeb.

Chcete-li klonovat a vytvořit místní verzi úložiště, použijte prostředí příkazového řádku k přechodu do vhodné složky v místním počítači. Potom spusťte jednu z následujících sad příkazů a klonujte úložiště .NET:

Chcete-li stáhnout nejnovější verzi implementací mikroslužeb .NET, spusťte:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Tyto příkazy stáhnout zdrojový kód pro všechny mikroslužeb kromě skripty, které používáte ke spuštění mikroslužeb místně. I když nepotřebujete zdrojový kód ke spuštění mikroslužeb v Dockeru, zdrojový kód je užitečné, pokud později plánujete upravit akcelerátor řešení a otestovat změny místně.

## <a name="deploy-the-azure-services"></a>Nasazení služeb Azure

I když tento článek ukazuje, jak spustit mikroslužeb místně, závisí na službách Azure spuštěných v cloudu. K nasazení služeb Azure použijte následující skript. Následující příklady skriptů předpokládají, že používáte úložiště .NET v počítači se systémem Windows. Pokud pracujete v jiném prostředí, upravte odpovídajícím způsobem cesty, přípony souborů a oddělovače cest.

### <a name="create-new-azure-resources"></a>Vytvoření nových prostředků Azure

Pokud jste ještě nevytvořili požadované prostředky Azure, postupujte takto:

1. V prostředí příkazového řádku přejděte do složky **\services\scripts\local\launch** ve své klonované kopii úložiště.

1. Spusťte následující příkazy k instalaci nástroje **příkazu příkazu k lis ování počítačů** a přihlaste se ke svému účtu Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Spusťte skript **start.cmd.** Skript vás vyzve k zadání následujících informací:
   * Název řešení.
   * Předplatné Azure, které se má použít.
   * Umístění datového centra Azure, které chcete použít.

     Skript vytvoří skupinu prostředků v Azure s názvem vašeho řešení. Tato skupina prostředků obsahuje prostředky Azure, které používá akcelerátor řešení. Tuto skupinu prostředků můžete odstranit, jakmile již nebudete potřebovat odpovídající prostředky.

     Skript také přidá sadu proměnných prostředí s předponou **PCS** do místního počítače. Tyto proměnné prostředí poskytují podrobnosti pro vzdálené monitorování, které mají být možné číst z prostředku Azure Key Vault. Tento prostředek trezoru klíčů je místo, kde vzdálené monitorování bude číst jeho hodnoty konfigurace z.

     > [!TIP]
     > Po dokončení skriptu se také uloží proměnné prostředí do souboru nazývaného ** \<název\>řešení .env domovské složky\>\\\\\<.pcs**. Můžete je použít pro budoucí nasazení akcelerátoru řešení. Všimněte si, že všechny proměnné prostředí nastavené v místním počítači přepíší hodnoty v **místním\\\\\\** souboru ENV služby při spuštění **docker-compose**.

1. Ukončete prostředí příkazového řádku.

### <a name="use-existing-azure-resources"></a>Použití existujících prostředků Azure

Pokud jste už vytvořili požadované prostředky Azure, vytvořte odpovídající proměnné prostředí v místním počítači.
Nastavte proměnné prostředí pro následující:
* **PCS_KEYVAULT_NAME** – název prostředku azure key vault
* **PCS_AAD_APPID** - ID aplikace AAD
* **PCS_AAD_APPSECRET** - Tajný klíč aplikace AAD

Hodnoty konfigurace se budou číst z tohoto prostředku Azure Key Vault. Tyto proměnné prostředí mohou být uloženy v souboru ** \<\>\\\\\<\>.env složky .pcs domovské složky z** nasazení. Všimněte si, že proměnné prostředí nastavené v místním počítači přepíší hodnoty v **místním\\\\\\** souboru ENV služby při spuštění **docker-compose**.

Některé konfigurace potřebné mikroslužby je uložen v instanci **trezoru klíčů,** který byl vytvořen při počátečním nasazení. Odpovídající proměnné v trezoru klíčů by měly být upraveny podle potřeby.