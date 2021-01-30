---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: 68362b61aa0d919bd0e95f3cebb1cf1189f9f0f3
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2021
ms.locfileid: "99095173"
---
## <a name="download-and-install"></a>Stažení a instalace

#### <a name="windows-install"></a>[Instalace Windows](#tab/windowsinstall)

Pomocí těchto kroků nainstalujete rozhraní příkazového řádku pro rozpoznávání řeči ve Windows:

1. V systému Windows potřebujete [Microsoft Visual C++ distribuovatelné součásti pro Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) pro vaši platformu. První instalace může vyžadovat restartování.
1. Nainstalujte [.NET Core 3,1](/dotnet/core/install/linux.md).
2. Zadáním tohoto příkazu nainstalujte rozhraní příkazového řádku pro rozpoznávání řeči pomocí NuGet:

    `dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI --version 1.15.0`

Zadáním `spx` zobrazíte nápovědu k rozhraní příkazového řádku pro rozpoznávání řeči.

> [!NOTE]
> Jako alternativu k NuGet můžete stáhnout a extrahovat [komprimovaný archiv zip](https://aka.ms/speech/spx-zips.zip)CLI, najít a extrahovat vaši platformu z `spx-zips` adresáře a přidat `spx` cestu k proměnné systémové **cesty** .

### <a name="run-the-speech-cli"></a>Spuštění funkce CLI pro rozpoznávání řeči

1. Otevřete příkazový řádek nebo PowerShell a potom přejděte do adresáře, do kterého jste extrahovali rozhraní příkazového řádku pro rozpoznávání řeči.  
2. Zadejte `spx` , chcete-li zobrazit příkazy Help pro rozhraní příkazového řádku pro rozpoznávání řeči.

> [!NOTE]
> PowerShell při hledání příkazu nekontroluje místní adresář. V prostředí PowerShell změňte adresář na umístění `spx` a zavolejte nástroj zadáním `.\spx` .
> Pokud přidáte tento adresář do vaší cesty, PowerShell a příkazový řádek Windows se najde `spx` z libovolného adresáře bez zahrnutí `.\` předpony.

### <a name="font-limitations"></a>Omezení písem

V systému Windows může rozhraní příkazového řádku pro rozpoznávání řeči Zobrazit pouze písma dostupná pro příkazový řádek v místním počítači.
Služba [Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) podporuje všechna písemně vytvořená písma pomocí funkce Speech CLI.

Pokud výstup do souboru, textový editor, jako je například Poznámkový blok nebo webový prohlížeč, jako je například Microsoft Edge, může zobrazit také všechna písma.

#### <a name="linux-install"></a>[Instalace pro Linux](#tab/linuxinstall)

Pomocí těchto kroků nainstalujete rozhraní příkazového řádku pro rozpoznávání řeči v systému Linux na procesor x64:

1. Nainstalujte [.NET Core 3,1](/dotnet/core/install/linux.md).
2. Zadáním tohoto příkazu nainstalujte rozhraní příkazového řádku pro rozpoznávání řeči pomocí NuGet:

    `dotnet tool install --global Microsoft.CognitiveServices.Speech.CLI --version 1.15.0`

Zadáním `spx` zobrazíte nápovědu k rozhraní příkazového řádku pro rozpoznávání řeči.

> [!NOTE]
> Jako alternativu k NuGet si můžete stáhnout binární soubory v [archivu zip](https://aka.ms/speech/spx-zips.zip), extrahovat `spx-netcore-30-linux-x64` do nového `~/spx` adresáře, zadat `sudo chmod +r+x spx` binární soubor a přidat `~/spx` cestu k systémové proměnné cesty.


#### <a name="docker-install-windows-linux-macos"></a>[Instalace Docker (Windows, Linux, macOS)](#tab/dockerinstall)

Pomocí těchto kroků nainstalujete rozhraní příkazového řádku rozpoznávání řeči do kontejneru Docker:

1. <a href="https://www.docker.com/get-started" target="_blank">Nainstalovat Docker Desktop <span class="docon docon-navigate-external x-hidden-focus"></span> </a> pro vaši platformu, pokud ještě není nainstalovaná.
2. Do nového příkazového řádku nebo terminálu zadejte tento příkaz:
   ```console   
   docker pull msftspeech/spx
   ```
3. Zadejte tento příkaz. Měli byste vidět informace o nápovědě pro rozhraní příkazového řádku pro rozpoznávání řeči:
   ```console 
   docker run -it --rm msftspeech/spx help
   ```

### <a name="mount-a-directory-in-the-container"></a>Připojit adresář v kontejneru

Nástroj Speech CLI ukládá nastavení konfigurace do souborů a při provádění libovolného příkazu (kromě příkazů Help) tyto soubory načte.
Pokud používáte funkci rozpoznávání řeči v kontejneru Docker, je nutné připojit místní adresář z kontejneru, aby nástroj mohl uložit nebo najít nastavení konfigurace a také tak, aby nástroj mohl číst nebo zapisovat jakékoli soubory, které příkaz vyžaduje, například zvukové soubory řeči.

V systému Windows zadejte tento příkaz pro vytvoření místního adresáře. rozhraní příkazového řádku pro rozpoznávání řeči může používat v rámci kontejneru:

`mkdir c:\spx-data`

Nebo v systému Linux nebo macOS zadejte tento příkaz v terminálu a vytvořte adresář a podívejte se na jeho absolutní cestu:

```bash
mkdir ~/spx-data
cd ~/spx-data
pwd
```

Při volání funkce Speech CLI budete používat absolutní cestu.

### <a name="run-speech-cli-in-the-container"></a>Spuštění rozhraní příkazového řádku pro rozpoznávání řeči v kontejneru

Tato dokumentace ukazuje příkaz rozpoznávání řeči, který se `spx` používá v instalacích bez Docker.
Při volání `spx` příkazu v kontejneru Docker musíte připojit adresář v kontejneru k systému souborů, kde může rozhraní příkazového řádku pro rozpoznávání řeči ukládat a vyhledávat konfigurační hodnoty a číst a zapisovat soubory.

V systému Windows budou vaše příkazy začínat takto:

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

V systému Linux nebo macOS budou vaše příkazy vypadat jako v ukázce níže. Nahraďte `ABSOLUTE_PATH` absolutní cestou k připojenému adresáři. Tato cesta byla vrácena `pwd` příkazem v předchozí části. 

Pokud před nastavením klíče a oblasti spustíte tento příkaz, zobrazí se vám chyba s oznámením, že byste si měli klíč a oblast nastavit:
```console   
sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
```

Chcete-li použít `spx` příkaz nainstalovaný v kontejneru, vždy zadejte úplný příkaz, následovaný parametry vaší žádosti.
Například v systému Windows tento příkaz nastaví klíč:

```console
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY
```

Chcete-li rozšířit interakci s nástrojem příkazového řádku, můžete spustit kontejner s interaktivním prostředím bash přidáním parametru EntryPoint.
V systému Windows zadejte tento příkaz pro spuštění kontejneru, který zveřejňuje interaktivní rozhraní příkazového řádku, kde můžete zadat více `spx` příkazů:
```console
docker run -it --entrypoint=/bin/bash -v c:\spx-data:/data --rm msftspeech/spx
```

> [!WARNING]
> Při spuštění funkce Speech CLI v kontejneru Docker nemůžete použít mikrofon počítače. Můžete si však přečíst a Uložit zvukové soubory do místního připojeného adresáře. 

<!-- Need to troubleshoot issues with docker pull image

### Optional: Create a command line shortcut

If you're running the the Speech CLI from a Docker container on Linux or macOS you can create a shortcut. 

Follow these instructions to create a shortcut:
1. Open `.bash_profile` with your favorite text editor. For example:
   ```shell
   nano ~/.bash_profile
   ```
2. Next, add this function to your `.bash_profile`. Make sure you update this function with the correct path to your mounted directory:
   ```shell   
   spx(){
       sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Source your profile:
   ```shell
   source ~/.bash_profile
   ```
4. Now instead of running `sudo docker run -it -v ABSOLUTE_PATH:/data --rm msftspeech/spx`, you can just type `spx` followed by arguments. For example: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> If you change the mounted directory that Docker is referencing, you need to update the function in `.bash_profile`.
--->
***

## <a name="create-subscription-config"></a>Vytvořit konfiguraci předplatného

Pokud chcete začít používat rozhraní příkazového řádku, musíte zadat klíč předplatného pro rozpoznávání řeči a identifikátor oblasti. Tyto přihlašovací údaje můžete získat podle kroků v [části Vyzkoušejte si službu Speech Service zdarma](../overview.md#try-the-speech-service-for-free).
Jakmile budete mít svůj klíč předplatného a identifikátor oblasti (např. `eastus`, `westus` ) spusťte následující příkazy.

```console
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

Ověřování předplatného je nyní uloženo pro budoucí požadavky SPX. Pokud potřebujete některou z těchto uložených hodnot odebrat, spusťte `spx config @region --clear` nebo `spx config @key --clear` .
