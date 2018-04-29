---
title: Známé problémy a Průvodci odstraňováním potíží | Microsoft Docs
description: Příručka k řešení a seznam známých problémů
services: machine-learning
author: svankam
ms.author: svankam
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: db910096f776f2f8710c0ac03f0378491018fc09
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench – známé problémy a Průvodce odstraňováním potíží s 
Tento článek vám pomůže najít a opravy chyb nebo selhání došlo jako součást pomocí aplikace Azure Machine Learning Workbench. 

## <a name="find-the-workbench-build-number"></a>Najít Workbench číslo sestavení
Při komunikaci s tým podpory, je důležité zahrnout číslo sestavení aplikace Workbench. V systému Windows, můžete zjistit číslo sestavení kliknutím na **pomoci** nabídky a zvolte **o Azure ML Workbench**. V systému macOS, můžete kliknutím na **Azure ML Workbench** nabídky a zvolte **o Azure ML Workbench**.

## <a name="machine-learning-msdn-forum"></a>Strojového učení fórum MSDN
Máme fóru MSDN, že můžete účtovat otázky. Produktový tým aktivně sleduje na fóru. Fórum je adresa URL [ https://aka.ms/azureml-forum ](https://aka.ms/azureml-forum). 

## <a name="gather-diagnostics-information"></a>Shromážděte diagnostické informace
V některých případech může být užitečné, pokud zajistíte diagnostické informace, žádostí o pomoc. Tady je bydlišti soubory protokolů:

### <a name="installer-log"></a>Instalační program protokolu
Pokud narazíte na problém během instalace, instalační soubory protokolu jsou tady:

```
# Windows:
%TEMP%\amlinstaller\logs\*

# macOS:
/tmp/amlinstaller/logs/*
```
Můžete si obsah tyto adresáře zip a odeslat do us pro diagnostiku.

### <a name="workbench-desktop-app-log"></a>Workbench protokolu aplikace na ploše
Pokud máte potíže s přihlášením nebo pokud dojde k chybě plochy Workbench, najdete tady soubory protokolu:
```
# Windows
%APPDATA%\AmlWorkbench

# macOS
~/Library/Application Support/AmlWorkbench
``` 
Můžete si obsah tyto adresáře zip a odeslat do us pro diagnostiku.

### <a name="experiment-execution-log"></a>Protokolu spuštění experimentu
Pokud konkrétní skript selže při odeslání z plochy aplikace, zkuste odeslat znovu ji prostřednictvím rozhraní příkazového řádku `az ml experiment submit` příkaz. To měl dát celý text chybové zprávy ve formátu JSON a co je nejdůležitější obsahuje **ID operace** hodnotu. Pošlete nám, včetně soubor JSON **ID operace** a jsme může pomoci diagnostikovat. 

Pokud konkrétní skript v odesílání úspěšné, ale v provádění selže, by měl vytiskněte **spustit ID** k identifikaci této konkrétní spustit. Můžete zabalit příslušných protokolových souborů pomocí následujícího příkazu:

```azurecli
# Create a ZIP file that contains all the diagnostics information
$ az ml experiment diagnostics -r <run_id> -t <target_name>
```

`az ml experiment diagnostics` Příkaz generuje `diagnostics.zip` souboru v kořenové složce projektu. Balíček ZIP obsahuje složku celý projekt ve stavu v době, kdy byla spuštěna, a informace o protokolování. Nezapomeňte odebrat všechny citlivé informace, které nechcete zahrnout před odesláním nám soubor diagnostiky.

## <a name="send-us-a-frown-or-a-smile"></a>Pošlete nám zamračeného smajlíka (nebo smajlíka)

Když pracujete v Azure ML Workbench, můžete také odeslat nám zamračeného smajlíka (nebo smajlíka) kliknutím na ikonu emotikona řez v levém dolním rohu okna aplikace. Volitelně můžete zahrnout e-mailovou adresu (takže jsme můžete získat zpět vám) nebo snímek aktuálního stavu. 

## <a name="known-service-limits"></a>Omezení známé služby
- Maximální povolená velikost složky projektu: 25 MB.
    >[!NOTE]
    >Toto omezení se nevztahuje na `.git`, `docs` a `outputs` složek. Názvy těchto složek rozlišují velká a malá písmena. Pokud pracujete s velkými soubory, podívejte se na [uložením změn a pozornosti s velkými soubory](../desktop-workbench/how-to-read-write-files.md).

- Maximální povolená doba spuštění experimentu: sedm dní

- Maximální velikost souboru sledovaných v `outputs` složka po spuštění: 512 MB
  - To znamená, že pokud váš skript vytvoří soubor větší než 512 MB ve složce výstupy, pokud nejsou zjištěny existuje. Pokud pracujete s velkými soubory, podívejte se na [uložením změn a pozornosti s velkými soubory](../desktop-workbench/how-to-read-write-files.md).

- Klíče SSH nejsou podporovány při připojování ke vzdálenému počítači nebo clusteru Spark přes protokol SSH. Aktuálně je podporována pouze uživatelské jméno a heslo režimu.

- Při použití clusteru HDInsight jako výpočetní cíl, musí používat Azure blob jako primárního úložiště. Použití Azure Data Lake Storage není podporováno.

- Text, který clustering transformací nejsou podporovány na macu.

- Knihovna RevoScalePy je podporována pouze v systému Windows a Linux (v kontejnerech Docker). Není podporována v systému macOS.

- Poznámkové bloky Jupyter mají maximální velikost maximálně 5 MB při jejich otevření z aplikace Workbench. Velké poznámkových bloků můžete otevřít z příkazového řádku pomocí poznámkového bloku ml az spuštění příkazu a vyčištění buňky výstupy ke snížení velikosti souboru.

## <a name="cant-update-workbench"></a>Nelze aktualizovat Workbench
Když je k dispozici nové aktualizace, domovskou stránku aplikace Workbench zobrazí zprávu informující o nové aktualizace. Měli byste vidět aktualizaci oznámení "BADGE" zobrazovaných v levém dolním rohu aplikace na ikonu zvonku. Klikněte na oznámení "BADGE" a postupujte podle pokynů Průvodce instalační program pro instalaci aktualizace. 

![aktualizace image](./media/known-issues-and-troubleshooting-guide/update.png)

Pokud se nezobrazí oznámení, pokuste se restartovat aplikaci. Pokud stále nevidíte oznámení o aktualizaci po restartování, může být několik příčin.

### <a name="you-are-launching-workbench-from-a-pinned-shortcut-on-the-task-bar"></a>Jsou spuštění Workbench z definovaného zástupce na hlavním panelu
Možná jste již nainstalovali aktualizace. Ale vaše připnuté zástupce stále odkazuje na staré bitů na disku. Můžete to ověřit procházením `%localappdata%/AmlWorkbench` složku a zda máte nainstalovanou existuje nejnovější verzi a zkontrolujte vlastnost definovaného zástupce zobrazíte, kde bude ukazovat na. Pokud se ověřit, stačí odstranit staré zástupce, spusťte Workbench z nabídky Start a volitelně vytvořte nový definovaného zástupce odkazující na hlavním panelu úloh.

### <a name="you-installed-workbench-using-the-install-azure-ml-workbench-link-on-a-windows-dsvm"></a>Jste nainstalovali pomocí odkazu "instalace Azure ML Workbench" v systému Windows DSVM Workbench
Bohužel neexistuje žádný snadný opravy na tomto. Je nutné provést následující kroky k odebrání nainstalovaná služba bits a stáhnout nejnovější verzi instalačního programu nástroje Workbench čerstvou – instalace: 
   - Odeberte tuto složku `C:\Users\<Username>\AppData\Local\amlworkbench`
   - odebrat skriptu `C:\dsvm\tools\setup\InstallAMLFromLocal.ps1`
   - odebrat zástupce na ploše, který spouští skript výše
   - stáhnout instalační program https://aka.ms/azureml-wb-msi a znovu nainstalovat.

## <a name="stuck-at-checking-experimentation-account-screen-after-logging-in"></a>Zablokované ve "Kontrola experimentování účet" obrazovky po přihlášení
Po přihlášení může získat na prázdnou obrazovku zprávou zobrazující "Kontroluje experimentování účet" s roztočený kolem zablokované aplikace Workbench. Chcete-li vyřešit tento problém, proveďte následující kroky:
1. Vypnutí aplikace
2. Odstraňte následující soubor:
  ```
  # on Windows
  %appdata%\AmlWorkbench\AmlWb.settings

  # on macOS
  ~/Library/Application Support/AmlWorkbench/AmlWb.settings
  ```
3. Restartujte aplikaci.

## <a name="cant-delete-experimentation-account"></a>Nelze odstranit účet experimentování
Rozhraní příkazového řádku můžete použít k odstranění účtu experimentování, ale je nutné odstranit nejprve podřízených pracovních prostorů a podřízené projekty v rámci těchto podřízených pracovních prostorů. Jinak se zobrazí chyba "prostředek budete moct odstranit po odstranění vnořených prostředků."

```azure-cli
# delete a project
$ az ml project delete -g <resource group name> -a <experimentation account name> -w <worksapce name> -n <project name>

# delete a workspace 
$ az ml workspace delete -g <resource group name> -a <experimentation account name> -n <worksapce name>

# delete an experimentation account
$ az ml account experimentation delete -g <resource group name> -n <experimentation account name>
```

Můžete také odstranit projekty a pracovní prostory v rámci aplikace Workbench.

## <a name="cant-open-file-if-project-is-in-onedrive"></a>Nelze otevřít soubor, pokud je projekt ve Onedrivu
Pokud máte Windows 10 patří Creators aktualizace a vytvoření projektu v místní složce namapované na OneDrive, můžete zjistit, že všechny soubor nelze otevřít v Workbench. Toto je z důvodu chyby zaváděné aktualizace Creators patří, která způsobuje selhání ve složce OneDrive kódu node.js. Chybě bude opraven brzy službou Windows update, ale do té doby, prosím nevytvářejte projekty ve složce OneDrive.

## <a name="file-name-too-long-on-windows"></a>Název souboru příliš dlouho v systému Windows
Pokud používáte Workbench v systému Windows, můžete narazit na výchozí maximální souboru 260 znaků názvu limit délky, která by mohla surface jako chyba "systém nemůže najít zadanou cestu". Můžete upravit nastavení klíče registru a povolit mnohem delší cestu název souboru. Zkontrolujte [v tomto článku](https://msdn.microsoft.com/library/windows/desktop/aa365247%28v=vs.85%29.aspx?#maxpath) další podrobnosti o tom, jak nastavit _hodnotou MAX_PATH_ klíč registru.

## <a name="interrupt-cli-execution-output"></a>Přerušení výstupu spuštění příkazového řádku
Pokud ji experimentování spouštět s využitím `az ml experiment submit` nebo `az ml notebook start` a chcete přerušení výstup: 
- V systému Windows pomocí kombinace kláves Ctrl-Break z klávesnice
- V systému macOS použijte kombinaci kláves Ctrl-c

Upozorňujeme, že pouze přeruší do výstupního datového proudu v okně příkazového řádku. Je ve skutečnosti nezastaví úlohu, která je spouštěna. Pokud chcete zrušit probíhající úlohy, použijte `az ml experiment cancel -r <run_id> -t <target name>` příkaz.

Na počítačích se systémem Windows pomocí klávesnice, které nemají zalomení klíč možné mezi ně patří Fn-B, Ctrl-Fn-B nebo Fn + Esc. Kombinace konkrétní naleznete v dokumentaci od dodavatele hardwaru.

## <a name="docker-error-read-connection-refused"></a>Chyba docker "číst: odmítl připojení"
Při provádění proti místní kontejner Docker, v některých případech může zobrazit chybová zpráva: 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```

Můžete je vyřešit ho změnou Docker serveru DNS z `automatic` na pevnou hodnotu `8.8.8.8`.

## <a name="remove-vm-execution-error-no-tty-present"></a>Odebrat virtuální počítač při provádění "nenachází žádné tty"
Při provádění proti kontejner Docker ve vzdáleném počítači systému Linux, můžete se setkat se následující chybová zpráva:
```
sudo: no tty present and no askpass program specified.
``` 
To může nastat, když změníte kořenové heslo virtuálního počítače s Ubuntu Linux pomocí portálu Azure. 

Azure Machine Learning Workbench vyžaduje heslo bez sudoers přístup ke spuštění na vzdáleného hostitele. Nejjednodušší způsob, jak to udělat, je použití _visudo_ upravte následující soubor (soubor může vytvořit Pokud neexistuje):

```
$ sudo visudo -f /etc/sudoers
```

>[!IMPORTANT]
>Je potřeba upravit soubor s _visudo_ a není jiného příkazu. _visudo_ automaticky syntaxe kontroluje všechny konfigurační soubory sudo a nepodařilo se vytvořit soubor syntakticky správnou sudoers můžete zablokování sudo.

Na konci souboru vložte následující řádek:

```
username ALL=(ALL) NOPASSWD:ALL
```

Kde _uživatelské jméno_ je název Azure Machine Learning Workbench bude používat k přihlášení do vzdáleného hostitele.

Na řádku musí být umístěny po #includedir "/ etc/sudoers.d", v opačném případě může být přepsána jiné pravidlo.

Pokud máte složitější konfigurace sudo, můžete chtít dokumentaci sudo Ubuntu k dispozici zde: https://help.ubuntu.com/community/Sudoers

Výše uvedené chyby může také dojít, pokud nepoužíváte Ubuntu Linux virtuálních počítačů na bázi v Azure jako cíl provádění. Virtuálních počítačů na bázi Ubuntu Linux podporujeme jenom pro vzdálené spuštění. 

## <a name="vm-disk-is-full"></a>Virtuální počítač disk je plný.
Ve výchozím nastavení při vytváření nového virtuálního počítače s Linuxem v Azure, získáte 30 GB disk pro operační systém. Modul docker ve výchozím nastavení používá stejný disk pro stahování dolů bitové kopie a spuštěných kontejnerů. To může zaplnit disk operačního systému a zobrazí chybu "Virtuální počítač Disk je plný", pokud se odehrává se.

Chcete-li odebrat všechny Docker Image, které už používáte je rychlou opravu. Příkaz Docker neobsahuje právě který. (Samozřejmě budete muset SSH do virtuálního počítače Chcete-li spustit příkaz Docker z prostředí bash.)

```
$ docker system prune -a
```

Můžete také přidat datový disk a nakonfigurovat modul Docker použít datový disk pro ukládání bitových kopií. Tady je [jak přidat datový disk](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk). Pak můžete [změnu kde Docker ukládá bitové kopie](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169).

Případně můžete rozšířit disk operačního systému a nemáte přístup ke konfiguraci modulu Docker. Tady je [jak můžete rozšířit disk operačního systému](https://docs.microsoft.com/azure/virtual-machines/linux/expand-disks).

```azure-cli
# Deallocate VM (stopping will not work)
$ az vm deallocate --resource-group myResourceGroup  --name myVM

# Get VM's Disc Name
az disk list --resource-group myResourceGroup --query '[*].{Name:name,Gb:diskSizeGb,Tier:accountType}' --output table

# Update Disc Size using above name
$ az disk update --resource-group myResourceGroup --name myVMdisc --size-gb 250
    
# Start VM    
$ az vm start --resource-group myResourceGroup  --name myVM
```

## <a name="sharing-c-drive-on-windows"></a>Sdílení jednotka C v systému Windows
Pokud jsou prováděny v místní kontejner Docker v systému Windows, nastavení `sharedVolumes` k `true` v `docker.compute` souboru pod `aml_config` může zlepšit výkon provádění. To však vyžaduje sdílet jednotce C _Docker pro nástroj Windows_. Pokud si nejste moci sdílet jednotku C, zkuste následující tipy:

* Kontrola sdílení na jednotce C pomocí Průzkumníka souborů
* Otevřete nastavení síťového adaptéru a odinstalace a přeinstalace "Souborů a tiskáren sdílení v sítích Microsoft" pro vEthernet
* Otevřete nastavení docker a sdílet C jednotku z v rámci nastavení docker
* Změny heslo pro Windows ovlivní sdílení. Otevřete Průzkumníka souborů, dál sdílet. jednotka C a zadejte nové heslo.
* Brány firewall problému může dojít také při pokusu o sdílení jednotce C s Docker. To [Stack Overflow post](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) může být užitečné.
* Při sdílení jednotka C pomocí přihlašovacích údajů do domény, sdílení může přestat pracovat v sítích, kde je řadič domény není dostupný (pro například domácí sítě, veřejnou Wi-Fi atd.). Další informace najdete v tématu [tento příspěvek](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).

Také se můžete vyhnout sdílení problém, na malé výkonu náklady, nastavení `sharedVolumne` k `false` v `docker.compute` souboru.

## <a name="wipe-clean-workbench-installation"></a>Vymazání čistou instalaci Workbench
Obecně nemusíte to udělat. Ale v případě, že je zapotřebí vymazat čisté instalace, tady jsou kroky:

- Ve Windows:
  - Nejdříve se ujistěte, používáte _přidat nebo odebrat programy_ aplet v _ovládací panely_ odebrat _Azure Machine Learning Workbench_ aplikaci.  
  - Potom můžete stáhnout a spustit některý z následujících skriptů:
    - [Skript příkazového řádku Windows](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.cmd).
    - [Skript prostředí Windows PowerShell](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.ps1). (Možná budete muset spustit `Set-ExecutionPolicy Unrestricted` v okně prostředí PowerShell oprávněními zvýšenými na úroveň oprávnění před spuštěním skriptu.)
- V systému macOS:
  - Právě stažení a spuštění [skript prostředí bash systému macOS](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_mac.sh).

## <a name="azure-ml-using-a-different-python-location-than-the-azure-ml-installed-python-environment"></a>Azure ML použití python jiné umístění než Azure ML nainstalované prostředí python
Kvůli změnám v nástroji Azure Machine Learning Workbench uživatelé všimnout, že místní spuštění nemusí ukazují prostředí python, který je nainstalován pomocí nástroje Azure ML Workbench už. To může dojít, pokud uživatel mít jiné prostředí python, které jsou v počítači nainstalovanou a nastavení cesty "Python" tak, aby odkazoval na prostředí. Chcete-li používat Azure ML Workbench nainstalovaná prostředí Python, postupujte takto:
- Přejděte na soubor local.compute složce aml_config v rámci kořenového adresáře projektu.
- Změna proměnnou "pythonLocation" tak, aby odkazoval na fyzickou cestu nástroje Azure ML workbench nainstalovaná prostředí python. Tuto cestu můžete získat dvěma způsoby:
    - Umístění Azure ML python najdete na %localappdata%\AmlWorkbench\python\python.exe
    - můžete otevřít cmd z Azure ML Workbench, zadejte na příkazovém řádku python, importovat sys.exe, spustit sys.executable a získání cesty z ní. 



## <a name="some-useful-docker-commands"></a>Některé užitečné příkazy Docker

Zde jsou některé užitečné příkazy Docker:

```sh
# display all running containers
$ docker ps

# dislplay all containers (running or stopped)
$ docke ps -a

# display all images
$ docker images

# show Docker logs of a container
$ docker logs <container_id>

# create a new container and launch into a bash shell
$ docker run <image_id> /bin/bash

# launch into a bash shell on a running container
$ docker exec -it <container_id> /bin/bash

# stop an running container
$ docker stop <container_id>

# delete a container
$ docker rm <container_id>

# delete an image
$ docker rmi <image_id>

# delete all unussed Docker images 
$ docker system prune -a

```
