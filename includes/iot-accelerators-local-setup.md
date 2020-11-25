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
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020232"
---
## <a name="download-the-source-code"></a>Stažení zdrojového kódu

Mezi úložiště zdrojového kódu vzdáleného monitorování patří zdrojový kód a konfigurační soubory Docker, které potřebujete ke spuštění imagí Docker mikroslužeb.

Pokud chcete klonovat a vytvořit místní verzi úložiště, použijte prostředí příkazového řádku a přejděte do vhodné složky na místním počítači. Pak spusťte jednu z následujících sad příkazů pro klonování úložiště .NET:

Chcete-li stáhnout nejnovější verzi implementací mikroslužeb .NET, spusťte příkaz:

```cmd/sh
git clone --recurse-submodules https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet.git

# To retrieve the latest submodules, run the following command:

cd azure-iot-pcs-remote-monitoring-dotnet
git submodule foreach git pull origin master
```

> [!NOTE]
> Tyto příkazy stáhnou zdrojový kód pro všechny mikroslužby Kromě skriptů používaných k místnímu spouštění mikroslužeb. I když zdrojový kód nepotřebujete ke spuštění mikroslužeb v Docker, je zdrojový kód užitečný, pokud později plánujete změnit akcelerátor řešení a testovat změny místně.

## <a name="deploy-the-azure-services"></a>Nasazení služeb Azure

I když v tomto článku se dozvíte, jak místně spustit mikroslužby, závisí na službách Azure spuštěných v cloudu. Pomocí následujícího skriptu nasaďte služby Azure. V následujících příkladech skriptu se předpokládá, že používáte úložiště .NET na počítači s Windows. Pokud pracujete v jiném prostředí, upravte správně cesty, přípony souborů a oddělovače cest.

### <a name="create-new-azure-resources"></a>Vytvoření nových prostředků Azure

Pokud jste ještě nevytvořili požadované prostředky Azure, postupujte podle těchto kroků:

1. V prostředí příkazového řádku přejděte do složky **\services\scripts\local\launch** v klonované kopii úložiště.

1. Spuštěním následujících příkazů nainstalujte nástroj pro **počítače** CLI a přihlaste se ke svému účtu Azure:

    ```cmd
    npm install -g iot-solutions
    pcs login
    ```

1. Spusťte skript **Start. cmd** . Skript vás vyzve k zadání následujících informací:
   * Název řešení.
   * Předplatné Azure, které se má použít.
   * Umístění datacentra Azure, které se má použít.

     Skript vytvoří v Azure skupinu prostředků s názvem vašeho řešení. Tato skupina prostředků obsahuje prostředky Azure, které používá akcelerátor řešení. Tuto skupinu prostředků můžete odstranit, až už nebudete potřebovat odpovídající prostředky.

     Skript také přidá sadu proměnných prostředí s předponou **počítačů** do místního počítače. Tyto proměnné prostředí obsahují podrobnosti o tom, jak vzdálené monitorování umožní číst z prostředku Azure Key Vault. V tomto prostředku Key Vault prostředek, ze kterého bude vzdálené monitorování číst hodnoty konfigurace z.

     > [!TIP]
     > Po dokončení skriptu uloží také proměnné prostředí do souboru s názvem **\<your home folder\> \\ . PC \\ \<solution name\> . env**. Můžete je použít pro budoucí nasazení akcelerátoru řešení. Všimněte si, že všechny proměnné prostředí nastavené v místním počítači přepíší hodnoty v souboru **\\ . ENV v souboru \\ \\ . env** , když spustíte **Docker-Dock**.

1. Z prostředí příkazového řádku ukončete.

### <a name="use-existing-azure-resources"></a>Použití existujících prostředků Azure

Pokud jste už vytvořili požadované prostředky Azure, vytvořte na místním počítači odpovídající proměnné prostředí.
Nastavte proměnné prostředí pro následující:
* **PCS_KEYVAULT_NAME** – název prostředku Azure Key Vault
* **PCS_AAD_APPID** – ID aplikace AAD
* **PCS_AAD_APPSECRET** – tajný klíč aplikace AAD

Z tohoto prostředku Azure Key Vault budou načteny konfigurační hodnoty. Tyto proměnné prostředí se můžou v nasazení Uložit do souboru **\<your home folder\> \\ . PC \\ \<solution name\> . env** . Všimněte si, že proměnné prostředí nastavené v místním počítači přepíší hodnoty v souboru **\\ \\ \\ . env skriptu služby** , když spustíte **Docker-Dock**.

Některá z konfigurací, které potřebuje mikroslužba, je uložená v instanci **Key Vault** , která byla vytvořena při počátečním nasazení. Odpovídající proměnné v trezoru klíčů by měly být podle potřeby upraveny.