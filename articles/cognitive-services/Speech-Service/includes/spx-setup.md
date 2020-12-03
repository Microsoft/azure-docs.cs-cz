---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
ms.openlocfilehash: da88b8554d6c3214da9a386613538c237a318f73
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546895"
---
## <a name="download-and-install"></a>Stažení a instalace

#### <a name="windows-install"></a>[Instalace Windows](#tab/windowsinstall)

Pomocí těchto kroků nainstalujete rozhraní příkazového řádku pro rozpoznávání řeči ve Windows:

1. V systému Windows potřebujete [Microsoft Visual C++ distribuovatelné součásti pro Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) pro vaši platformu. První instalace může vyžadovat restartování.
2. Stáhněte si [archiv zip](https://aka.ms/speech/spx-zips.zip)pro rozpoznávání řeči a pak ho rozbalte.
3. Přejít do adresáře, do kterého jste extrahovali `spx-zips` . Tato složka obsahuje programové soubory pro rozhraní příkazového řádku pro rozpoznávání řeči na různých platformách. 
4. Extrahujte soubory pro vaši platformu ( `spx-net471` pro .NET Framework 4,7 nebo `spx-netcore-win-x64` pro .net Core 3,0 na CPU x64). Mějte na paměti, že budete spouštět `spx` z tohoto adresáře.

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

1. Nainstalujte [.NET Core 3,0](https://dotnet.microsoft.com/download/dotnet-core/3.0).
2. Stáhněte si [archiv zip](https://aka.ms/speech/spx-zips.zip)pro rozpoznávání řeči a pak ho rozbalte.
3. Přejít do kořenového adresáře `spx-zips` , který jste extrahovali ze staženého souboru, a extrahovat `spx-netcore-30-linux-x64` do nového `~/spx` adresáře.
4. V terminálu zadejte tyto příkazy:
   1. `cd ~/spx`
   2. `sudo chmod +r+x spx`
   3. `PATH=~/spx:$PATH`

Zadáním `spx` zobrazíte nápovědu k rozhraní příkazového řádku pro rozpoznávání řeči.

#### <a name="docker-install-windows-linux-macos"></a>[Instalace Docker (Windows, Linux, macOS)](#tab/dockerinstall)

Pomocí těchto kroků nainstalujete rozhraní příkazového řádku rozpoznávání řeči do kontejneru Docker:

1. <a href="https://www.docker.com/get-started" target="_blank">Nainstalovat Docker Desktop <span class="docon docon-navigate-external x-hidden-focus"></span> </a> pro vaši platformu, pokud ještě není nainstalovaná.
2. Do nového příkazového řádku nebo terminálu zadejte tento příkaz:
   ```shell   
   docker pull msftspeech/spx
   ```
3. Zadejte tento příkaz. Měli byste vidět informace o nápovědě pro rozhraní příkazového řádku pro rozpoznávání řeči:
   ```shell 
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

```shell
docker run -it -v c:\spx-data:/data --rm msftspeech/spx
```

V systému Linux nebo macOS se příkazy začnou podobat této:
```shell   
sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx
```

> [!NOTE]
> Nahraďte `/ABSOLUTE_PATH` absolutní cestou zobrazenou `pwd` příkazem v předchozí části.

Chcete-li použít `spx` příkaz nainstalovaný v kontejneru, vždy zadejte úplný příkaz, následovaný parametry vaší žádosti.
Například v systému Windows tento příkaz nastaví klíč:

```shell
docker run -it -v c:\spx-data:/data --rm msftspeech/spx config @key --set SUBSCRIPTION-KEY
```

> [!WARNING]
> Při spuštění funkce Speech CLI v kontejneru Docker nemůžete použít mikrofon počítače. Můžete si však přečíst a Uložit zvukové soubory do místního připojeného adresáře. 

### <a name="optional-create-a-command-line-shortcut"></a>Volitelné: Vytvoření zástupce příkazového řádku

Pokud používáte rozhraní příkazového řádku pro rozpoznávání řeči z kontejneru Docker v systému Linux nebo macOS, můžete vytvořit zástupce. 

Pomocí těchto pokynů vytvořte zástupce:
1. Otevřete `.bash_profile` v oblíbeném textovém editoru. Příklad:
   ```shell
   nano ~/.bash_profile
   ```
2. Dále přidejte tuto funkci do `.bash_profile` . Ujistěte se, že tuto funkci aktualizujete se správnou cestou k připojenému adresáři:
   ```shell   
   spx(){
       sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx
   }
   ```
3. Zdroj profilu:
   ```shell
   source ~/.bash_profile
   ```
4. Místo spuštění můžete hned `sudo docker run -it -v /ABSOLUTE_PATH:/data --rm msftspeech/spx` napsat jenom `spx` argumenty a. Příklad: 
   ```shell
   // Get some help
   spx help recognize

   // Recognize speech from an audio file 
   spx recognize --file /mounted/directory/file.wav
   ```

> [!WARNING]
> Pokud změníte připojený adresář, na který se odkazuje Docker, budete muset funkci aktualizovat v `.bash_profile` .

***

## <a name="create-subscription-config"></a>Vytvořit konfiguraci předplatného

Pokud chcete začít používat rozhraní příkazového řádku, musíte zadat klíč předplatného pro rozpoznávání řeči a identifikátor oblasti. Tyto přihlašovací údaje můžete získat podle kroků v [části Vyzkoušejte si službu Speech Service zdarma](../overview.md#try-the-speech-service-for-free).
Jakmile budete mít svůj klíč předplatného a identifikátor oblasti (např. `eastus`, `westus` ) spusťte následující příkazy.

```shell
spx config @key --set SUBSCRIPTION-KEY
spx config @region --set REGION
```

Ověřování předplatného je nyní uloženo pro budoucí požadavky SPX. Pokud potřebujete některou z těchto uložených hodnot odebrat, spusťte `spx config @region --clear` nebo `spx config @key --clear` .
