---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: 3c176f103371bfb1b35231f222b2045f1f4a3ef9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91327020"
---
## <a name="download-and-install"></a>Stažení a instalace

#### <a name="windows-install"></a>[Instalace Windows](#tab/windowsinstall)

Pomocí těchto kroků nainstalujete rozhraní příkazového řádku pro rozpoznávání řeči ve Windows:

1. Stáhněte si [archiv zip](https://aka.ms/speech/spx-zips.zip)pro rozpoznávání řeči a pak ho rozbalte.
2. Přejít do kořenového adresáře `spx-zips` , který jste extrahovali ze staženého souboru, a extrahovat podadresář, který potřebujete ( `spx-net471` pro .NET Framework 4,7 nebo `spx-netcore-win-x64` pro .NET Core 3,0 na CPU x64).

Na příkazovém řádku změňte adresář na toto umístění a potom zadejte `spx` nápovědu k rozhraní příkazového řádku pro rozpoznávání řeči.

> [!NOTE]
> V systému Windows může rozhraní příkazového řádku pro rozpoznávání řeči Zobrazit pouze písma dostupná pro příkazový řádek v místním počítači.
> Služba [Windows Terminal](https://www.microsoft.com/en-us/p/windows-terminal/9n0dx20hk701) podporuje všechna písemně vytvořená písma pomocí funkce Speech CLI.
> Pokud výstup do souboru, textový editor, jako je například Poznámkový blok nebo webový prohlížeč, jako je například Microsoft Edge, může zobrazit také všechna písma.

> [!NOTE]
> PowerShell při hledání příkazu nekontroluje místní adresář. V prostředí PowerShell změňte adresář na umístění `spx` a zavolejte nástroj zadáním `.\spx` .
> Pokud přidáte tento adresář do vaší cesty, PowerShell a příkazový řádek Windows se najde `spx` z libovolného adresáře bez zahrnutí `.\` předpony.

#### <a name="linux-install"></a>[Instalace pro Linux](#tab/linuxinstall)

Pomocí těchto kroků nainstalujete rozhraní příkazového řádku pro rozpoznávání řeči v systému Linux na procesor x64:

1. Nainstalujte [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Stáhněte si [archiv zip](https://aka.ms/speech/spx-zips.zip)pro rozpoznávání řeči a pak ho rozbalte.
3. Přejít do kořenového adresáře `spx-zips` , který jste extrahovali ze staženého souboru, a extrahovat `spx-netcore-30-linux-x64` do nového `~/spx` adresáře.
4. V terminálu zadejte tyto příkazy:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Zadáním `spx` zobrazíte nápovědu k rozhraní příkazového řádku pro rozpoznávání řeči.

#### <a name="docker-install"></a>[Instalace Docker](#tab/dockerinstall)

> [!NOTE]
> <a href="https://www.docker.com/get-started" target="_blank">Docker Desktop pro vaši platformu <span class="docon docon-navigate-external x-hidden-focus"></span> </a> musí být nainstalovaný.

Pomocí těchto kroků nainstalujete rozhraní příkazového řádku rozpoznávání řeči v kontejneru Docker:

1. Do nového příkazového řádku nebo terminálu zadejte tento příkaz:  `docker pull msftspeech/spx`
2. Zadejte tento příkaz. Měli byste vidět informace o nápovědě pro rozhraní příkazového řádku pro rozpoznávání řeči: `docker run -it --rm msftspeech/spx help`

### <a name="mount-a-directory-in-the-container"></a>Připojit adresář v kontejneru

Nástroj Speech CLI ukládá nastavení konfigurace do souborů a při provádění libovolného příkazu (kromě příkazů Help) tyto soubory načte.
Pokud používáte funkci rozpoznávání řeči v kontejneru Docker, je nutné připojit místní adresář z kontejneru, aby nástroj mohl uložit nebo najít nastavení konfigurace a také tak, aby nástroj mohl číst nebo zapisovat jakékoli soubory, které příkaz vyžaduje, například zvukové soubory řeči.

V systému Windows zadejte tento příkaz pro vytvoření místního adresáře. rozhraní příkazového řádku pro rozpoznávání řeči může používat v rámci kontejneru:

`mkdir c:\spx-data`

Případně můžete v systému Linux nebo Mac zadat tento příkaz v terminálu a vytvořit adresář a zobrazit jeho absolutní cestu:

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

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx`

V systému Linux nebo Mac se příkazy začnou podobat této:

`sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx`

> [!NOTE]
> Nahraďte `/ABSOLUTE_PATH` absolutní cestou zobrazenou `pwd` příkazem v předchozí části.

Chcete-li použít `spx` příkaz nainstalovaný v kontejneru, vždy zadejte úplný příkaz, následovaný parametry vaší žádosti.
Například v systému Windows tento příkaz nastaví klíč:

`docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY`

> [!NOTE]
> Při spuštění funkce Speech CLI v kontejneru Docker nemůžete použít mikrofon ani mluvčí počítač.
> Pokud chcete tato zařízení používat, předejte zvukové soubory do a z rozhraní příkazového řádku pro záznam/přehrávání mimo kontejner Docker.
> Nástroj Speech CLI má přístup k místnímu adresáři, který jste nastavili v předchozích krocích.

***

## <a name="create-subscription-config"></a>Vytvořit konfiguraci předplatného

Pokud chcete začít používat rozhraní příkazového řádku, musíte nejdřív zadat klíč předplatného pro rozpoznávání řeči a informace o oblasti. Identifikátor vaší oblasti najdete na stránce [podpory oblasti](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) . Jakmile budete mít svůj klíč předplatného a identifikátor oblasti (např. `eastus`, `westus` ) spusťte následující příkazy.

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

Ověřování předplatného je nyní uloženo pro budoucí požadavky SPX. Pokud potřebujete některou z těchto uložených hodnot odebrat, spusťte `spx config @region --clear` nebo `spx config @key --clear` .
