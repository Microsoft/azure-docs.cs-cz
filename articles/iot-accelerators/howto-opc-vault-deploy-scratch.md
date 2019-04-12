---
title: Nasazení modulu OPC trezor úplně od začátku – Azure | Dokumentace Microsoftu
description: Jak nasazovat trezor OPC úplně od začátku.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: dedba42d7e4b8d603d97522cf0173f41efd20b3a
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59496892"
---
# <a name="deploy-opc-vault-from-scratch"></a>Nasazení služby OPC Vault od nuly

OPC trezor je mikroslužeb, která můžete konfigurovat, registrace a správa životního cyklu certifikátu pro server OPC UA a klientských aplikací v cloudu. Tento článek ukazuje, jak nasazovat trezor OPC úplně od začátku.

## <a name="configuration-and-environment-variables"></a>Konfigurace a proměnnými prostředí

Konfigurace služby je uložená v appsettings.ini adaptéry konfigurace ASP.NET Core. Formát INI umožňuje k ukládání hodnot v čitelném formátu s komentáři.
Aplikace také podporuje vkládání proměnné prostředí, jako je například přihlašovací údaje a podrobnosti o síti. (V této části je původně s názvem TODO konfigurace a proměnnými prostředí).

Konfigurační soubor v úložišti odkazuje na některé proměnné prostředí, které je potřeba vytvořit alespoň jednou. V závislosti na vašem operačním systému a rozhraní IDE existuje několik způsobů, jak spravovat proměnné prostředí:

- Obálka proměnných setup.cmd skript pro uživatele Windows, musí být připravené a provede pouze jednou. Při spuštění, nastavení se zachová napříč Terminálová relace a restartování počítače.

- Pro prostředí Linuxu a os x instalační program Obálka proměnných skriptu je nutné spustit pokaždé, když se otevře novou konzolu. V závislosti na operačním systému a terminálu způsoby k uchování hodnoty globálně, další informace, které by měly pomoci tyto stránky:

  - https://stackoverflow.com/questions/13046624/how-to-permanently-export-a-variable-in-linux
  
  - https://stackoverflow.com/questions/135688/setting-environment-variables-in-os-x
  
  - https://help.ubuntu.com/community/EnvironmentVariables
  

- Visual Studio: Proměnné prostředí můžete nastavit také z aplikace Visual Studio v části Vlastnosti projektu, v levém podokně vyberte "Vlastnosti konfigurace" a "Prostředí" přístup k oddílu, ve kterém můžete přidat více proměnných.

- IntelliJ Rider: Proměnné prostředí můžete nastavit v každé konfiguraci spuštění, podobně jako pro IntelliJ IDEA https://www.jetbrains.com/help/idea/run-debug-configuration-application.html

## <a name="run-and-debug-with-visual-studio"></a>Spustit a ladit pomocí sady Visual Studio

Visual Studio vám umožňuje rychle otevřete aplikaci bez použití příkazového řádku bez konfigurace něco mimo rozhraní IDE.

Kroky, pomocí sady Visual Studio 2017:

1. Otevřete řešení pomocí `iot-opc-gds-service.sln` souboru.

1. Při načítání řešení, klikněte pravým tlačítkem na `WebService` projektu, vyberte a přejděte `Debug` části.

1. Ve stejném oddílu definujte proměnné prostředí nutné.

1. Stisknutím klávesy **F5**, nebo **spustit** ikonu. Visual Studio by měl otevřete prohlížeč zobrazující stav služby ve formátu JSON.

## <a name="run-and-debug-with-intellij-rider"></a>Spuštění a ladění pomocí IntelliJ Rider

1. Otevřete řešení pomocí `iot-opc-gds-service.sln` souboru.

1. Při načítání řešení, přejděte na `Run > Edit Configurations` a vytvořte nový `.NET Project` konfigurace.

1. V konfiguraci vyberte projekt webové služby.

1. Uložte nastavení a konfigurace, který je vytvořen z panelu nástrojů integrovaného vývojového prostředí spuštění.

1. Služba bootstrap zprávy v okně Spustit IntelliJ podrobnosti, jako je adresa URL webové služby se spuštěným systémem plus služby by se měla zobrazit protokoly.

## <a name="build-and-run-from-the-command-line"></a>Sestavte a spusťte z příkazového řádku

Složka skripty obsahuje některé skripty pro časté úlohy:

- `build`: Všechny projekty zkompilujte a spusťte testy.

- `compile`: Zkompilujte všechny projekty.

- `run`: Kompilace projektů a spuštění služby, která vyzve k zadání zvýšená oprávnění ve Windows, na kterém běží webová služba.

Zkontrolujte skripty pro nastavení proměnných prostředí. Můžete nastavit proměnné prostředí globálně v operačním systému, nebo pomocí skriptu "nastavení Obálka proměnných" ve složce scripts.

### <a name="sandbox"></a>Izolovaný prostor

Skripty se předpokládá, že jste nakonfigurovali vývojové prostředí s .NET Core a Docker. Vyhnout se instalaci .NET Core, Docker pouze nainstalovat a použít parametr příkazového řádku `--in-sandbox` (nebo krátkou formu `-s`), například:

- `build --in-sandbox`: Spustí úlohu uvnitř kontejneru Docker sestavení (krátký tvar `build -s`).

- `compile --in-sandbox`: Spustí úlohu kompilace uvnitř kontejneru Docker (krátký tvar `compile -s`).

- `run --in-sandbox`: Spustí službu uvnitř kontejneru Docker (krátký tvar `run -s`).

Image Dockeru pro izolovaný prostor se hostovanou v Docker Hubu [tady](https://hub.docker.com/r/azureiotpcs/code-builder-dotnet).

## <a name="package-the-application-to-a-docker-image"></a>Balíček aplikace do image Dockeru

`scripts` Složka obsahuje podsložky dockeru s soubory potřebné k balíčku do image Dockeru služby:

- `Dockerfile`: Specifikace Image dockeru.
- `build`: Vytvoření kontejneru Dockeru a uložení image v místním registru.
- `run`: Spuštění kontejneru Dockeru z bitové kopie uložené v místním registru.
- `content`: Složka se soubory zkopírovány do bitové kopie, včetně skript vstupního bodu.

## <a name="azure-iot-hub-setup"></a>Azure IoT Hub – nastavení

Pokud chcete použít mikroslužbách, nastavení služby Azure IoT Hub pro vývoj a integrační testy.

Projekt obsahuje některé skripty Bash, které vám pomůžou se toto nastavení:

- Vytvořte novou službu IoT Hub: `./scripts/iothub/create-hub.sh`

- Seznam existujících centrech: `./scripts/iothub/list-hubs.sh`

- Zobrazit podrobnosti (třeba klíče) služby IoT Hub: `./scripts/iothub/show-hub.sh`

A v případě, že máte více předplatných Azure:

- Zobrazit seznam předplatných: `./scripts/iothub/list-subscriptions.sh`

- Změnit aktuální předplatné: `./scripts/iothub/select-subscription.sh`

## <a name="development-setup"></a>Nastavení vývojového

### <a name="net-setup"></a>Instalace rozhraní .NET

Projekt pracovního postupu se spravuje přes [.NET Core](https://dotnet.github.io) 1.x, které je potřeba nainstalovat ve vašem prostředí, aby mohli spouštět všechny skripty a ujistěte se, že prostředí (IDE) funguje podle očekávání.

Poskytujeme také [verze Javy](https://github.com/Azure/iot-opc-gds-service-dotnet) tohoto projektu a další součásti počítačů s Azure IoT.

### <a name="ide"></a>IDE – integrované vývojové prostředí

Tady jsou některé z prostředí IDE, můžete použít pro práci na počítačích s Azure IoT:

- [Visual Studio](https://www.visualstudio.com)
- [Visual Studio pro Mac](https://www.visualstudio.com/vs/visual-studio-mac)
- [IntelliJ Rider](https://www.jetbrains.com/rider)
- [Visual Studio Code](https://code.visualstudio.com)

### <a name="git-setup"></a>Instalace Gitu

Projekt nezahrnuje hák Git, automatizovat některé kontroly před přijetím změny kódu. Můžete ručně spustit testy, nebo to CI platformu pro spuštění testů. Použijeme následující hooku Gitu automaticky spustit všechny testy před odesláním změn kódu na Githubu a zrychlit pracovního postupu vývoje.

Pokud kdykoli chcete odebrat volání, jednoduše odstranit soubor nainstalované v adresáři `.git/hooks`. Můžete obejít pomocí háku předběžné potvrzení `--no-verify` možnost.

#### <a name="pre-commit-hook-with-sandbox"></a>Zavěšení předběžné potvrzení s sandbox

Nastavit zahrnuté háky, otevřete konzoly Windows, Linux nebo MacOS a spusťte:

```
cd PROJECT-FOLDER
cd scripts/git
setup --with-sandbox
```

Pomocí této konfigurace, při vracení souborů Git ověřuje, že aplikace předá všechny testy spustit sestavení a testů uvnitř kontejneru Dockeru, který je nakonfigurovaný s všechny požadavky na vývoj.

#### <a name="pre-commit-hook-without-sandbox"></a>Před potvrzení hook bez izolovaného prostoru

> [!NOTE] 
> Vyžaduje volání bez izolovaného prostoru [.NET Core](https://dotnet.github.io) v systémové CESTĚ.

Nastavit zahrnuté háky, otevřete konzoly Windows, Linux nebo MacOS a spusťte:

```
cd PROJECT-FOLDER
cd scripts/git
setup --no-sandbox
```
S touto konfigurací, při vrácení souborů se změnami Git ověří, že aplikace úspěšně projde všemi testy, spouštění sestavení a testů v pracovní stanici, pomocí nástroje nainstalované v operačním systému.

Doprovodné materiály k styl kódu projektu:

- Případně, řádky délka je omezená na 80 znaků maximální revize kódu a editory příkazového řádku.

- Odsazení bloků kódu s mezery čtyři. Mělo by se vyhnout char kartu.

- Textové soubory pomocí systému Unix konec formát řádku (LF).

- Spravuje se pomocí vkládání závislostí [Autofac](https://autofac.org).

- Pole rozhraní API webové služby jsou CamelCased s výjimkou metadat.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak nasazovat trezor OPC úplně od začátku, tady je navrhované další krok:

> [!div class="nextstepaction"]
> [Nasazení OPC Dvojčete úplně od začátku](howto-opc-twin-deploy-modules.md)