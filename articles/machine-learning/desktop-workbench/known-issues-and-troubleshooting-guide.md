---
title: Známé problémy a Průvodce odstraňováním potíží | Dokumentace Microsoftu
description: Seznam známých problémů a tento průvodce vám pomůže vyřešit
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 01/12/2018
ms.openlocfilehash: d72f1b986671bfab1d79772ac50935918da7e681
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963266"
---
# <a name="azure-machine-learning-workbench---known-issues-and-troubleshooting-guide"></a>Azure Machine Learning Workbench – známé problémy a Průvodce odstraňováním potíží 
Tento článek vám pomůže najít a opravit chyby nebo selhání došlo k jako součást pomocí aplikace Azure Machine Learning Workbench. 

## <a name="find-the-workbench-build-number"></a>Najít číslo sestavení aplikace Workbench
Při komunikaci se na tým podpory, je důležité zahrnout číslo sestavení z aplikace Workbench. Na Windows, můžete zjistit číslo buildu kliknutím na **pomáhají** nabídku a zvolte **o aplikaci Azure ML Workbench**. V systému macOS, můžete kliknout na **aplikaci Azure ML Workbench** nabídku a zvolte **o aplikaci Azure ML Workbench**.

## <a name="machine-learning-msdn-forum"></a>Fórum MSDN služby Machine Learning
Máme fórum na webu MSDN, můžete posílat otázky. Produktový tým Toto fórum aktivně monitoruje. Fórum je adresa URL [ https://aka.ms/azureml-forum ](https://aka.ms/azureml-forum). 

## <a name="gather-diagnostics-information"></a>Shromážděte diagnostické informace
V některých případech může být užitečné, pokud může poskytnout diagnostické informace, pokud s žádostí o pomoc. Zde je, kde live soubory protokolu:

### <a name="installer-log"></a>Protokol instalačního programu
Pokud narazíte na problém během instalace, soubory protokolu Instalační služby systému jsou tady:

```
# Windows:
%TEMP%\amlinstaller\logs\*

# macOS:
/tmp/amlinstaller/logs/*
```
Můžete si obsah tyto adresáře zip a odeslat společnosti Microsoft pro diagnostiku.

### <a name="workbench-desktop-app-log"></a>Protokol desktopovou aplikaci Workbench
Pokud máte potíže s přihlášením nebo pokud dojde k chybě aplikace Workbench desktop, můžete najít zde soubory protokolu:
```
# Windows
%APPDATA%\AmlWorkbench

# macOS
~/Library/Application Support/AmlWorkbench
``` 
Můžete si obsah tyto adresáře zip a odeslat společnosti Microsoft pro diagnostiku.

### <a name="experiment-execution-log"></a>Protokolu spuštění experimentu
Pokud konkrétní skript selže během odesílání z desktopové aplikace, zkuste se znovu ji spustit pomocí rozhraní příkazového řádku `az ml experiment submit` příkazu. To by vám měl dát celé chybové zprávy ve formátu JSON a obsahuje nejdůležitější ale je **ID operace** hodnotu. Pošlete nám, včetně souboru JSON **ID operace** a můžeme vám pomoct diagnostikovat. 

Pokud se odeslání určitého skriptu, ale selže při provádění, by měl vytisknout **ID spuštění** k identifikaci tohoto konkrétního spuštění. Můžete zabalit příslušných protokolových souborů pomocí následujícího příkazu:

```azurecli
# Create a ZIP file that contains all the diagnostics information
$ az ml experiment diagnostics -r <run_id> -t <target_name>
```

`az ml experiment diagnostics` Příkaz vygeneruje `diagnostics.zip` soubor v kořenové složce projektu. Balíček ZIP obsahuje celé složky projektu ve stavu v době, kdy byla spuštěna, a navíc informace o protokolování. Nezapomeňte odebrat všechny citlivé informace, které nechcete zahrnout soubor před odesláním souboru diagnostiky.

## <a name="send-us-a-frown-or-a-smile"></a>Pošlete nám zamračeného smajlíka (nebo smajlíka)

Při práci v aplikaci Azure ML Workbench můžete také nám pošlete zamračeného smajlíka (nebo smajlíka) kliknutím na ikona usměvavého obličeje v levém dolním rohu okna aplikace. Volitelně můžete také zahrnout e-mailovou adresu (takže jsme pro vás získáte zpět) a/nebo snímek aktuálního stavu. 

## <a name="known-service-limits"></a>Omezení služeb
- Maximální povolená velikost složky projektu: 25 MB.
    >[!NOTE]
    >Toto omezení neplatí pro `.git`, `docs` a `outputs` složek. Názvy těchto složek rozlišují malá a velká písmena. Pokud pracujete s velkými soubory, podívejte se na [uložením změn a velké soubory se zabývají](how-to-read-write-files.md).

- Maximální povolená doba spuštění experimentu: 7 dní

- Maximální velikost souboru sledované v `outputs` složka po spuštění: 512 MB
  - To znamená, že pokud váš skript vytvoří soubor, který je větší než 512 MB ve složce výstupy, nejsou shromažďovány existuje. Pokud pracujete s velkými soubory, podívejte se na [uložením změn a velké soubory se zabývají](how-to-read-write-files.md).

- Klíče SSH nejsou podporovány při připojování ke vzdálenému počítači nebo v clusteru Spark přes protokol SSH. Aktuálně podporuje jenom režim uživatelského jména a hesla.

- Při použití clusteru HDInsight jako cílové výpočetní prostředí, musíte použít Azure blob jako primární úložiště. Použití Azure Data Lake Storage se nepodporuje.

- Transformace textu clusteringu nejsou podporovány v systému Mac.

- RevoScalePy knihovna je podporován pouze ve Windows a Linuxu (v kontejnerech Dockeru). Není podporován v systému macOS.

- Poznámkové bloky Jupyter mají omezení maximální velikosti 5 MB při otevírání v aplikaci Workbench. Velké poznámkové bloky můžete otevřít z příkazového řádku pomocí příkazu az ml Poznámkový blok spustit, a vypíše čistou buňky ke zmenšení velikosti souborů.

## <a name="cant-update-workbench"></a>Nelze aktualizovat aplikaci Workbench
Když je k dispozici nové aktualizace, domovskou stránku aplikace Workbench zobrazí zpráva o nové aktualizace. Měli byste vidět aktualizaci oznámení "BADGE" povolí, v levém dolním aplikace na ikonu zvonu. Klikněte na Odznáček a postupujte podle pokynů Průvodce instalačního programu pro instalaci aktualizace. 

![aktualizovat bitovou kopii](../service/media/known-issues-and-troubleshooting-guide/update.png)

Pokud se nezobrazí oznámení, zkuste aplikaci restartovat. Pokud oznámení o aktualizaci po restartu stále nevidíte, může být několik příčin.

### <a name="you-are-launching-workbench-from-a-pinned-shortcut-on-the-task-bar"></a>Aplikace Workbench provádíte spuštění z připojených zástupce na hlavním panelu
Možná jste již nainstalovali aktualizace. Ale připnuté místní stále odkazuje na starý bity na disku. Můžete to ověřit tak, že přejdete na `%localappdata%/AmlWorkbench` složky a jestli máte existuje nainstalovanou nejnovější verzi a prozkoumejte vlastnost připojené místní zobrazíte, kde bude ukazovat na. Je-li ověřit, jednoduše odebrat starý zástupce, spusťte aplikaci Workbench v nabídce Start a volitelně vytvořit nového zástupce připnuté na hlavním panelu.

### <a name="you-installed-workbench-using-the-install-azure-ml-workbench-link-on-a-windows-dsvm"></a>Nainstalované aplikace Workbench pomocí "instalace aplikace Azure ML Workbench" odkaz na Windows DSVM
Bohužel není na tomto žádné snadno napravit. Je nutné provést následující kroky odebrat bits nainstalovaná a stáhněte si nejnovější instalační program pro nové instalace aplikace Workbench: 
   - odebrat složku `C:\Users\<Username>\AppData\Local\amlworkbench`
   - odebrat skript `C:\dsvm\tools\setup\InstallAMLFromLocal.ps1`
   - odebrat zástupce, který se spustí skript výše
   - stažení instalačního programu https://aka.ms/azureml-wb-msi a znovu nainstalujte.

## <a name="stuck-at-checking-experimentation-account-screen-after-logging-in"></a>Zablokuje na obrazovce "Checking účet experimentování ve službě" po přihlášení
Po přihlášení se aplikace Workbench může způsobit zablokování a zobrazení na prázdnou obrazovku a zobrazí se zpráva zobrazující "Kontrola experimentální účet" kolečkem zaneprázdnění. Chcete-li vyřešit tento problém, proveďte následující kroky:
1. Vypnutí aplikace
2. Odstraňte následující soubor:
  ```
  # on Windows
  %appdata%\AmlWorkbench\AmlWb.settings

  # on macOS
  ~/Library/Application Support/AmlWorkbench/AmlWb.settings
  ```
3. Restartujte aplikaci.

## <a name="cant-delete-experimentation-account"></a>Nelze odstranit experimentální účet
Rozhraní příkazového řádku můžete použít k odstranění účtu experimentování, ale je nutné odstranit první podřízený pracovní prostory a podřízené projekty v rámci těchto podřízených pracovních prostorů. V opačném případě se zobrazí chyba "nelze odstranit prostředek než vnořených prostředků se odstraní."

```azure-cli
# delete a project
$ az ml project delete -g <resource group name> -a <experimentation account name> -w <workspace name> -n <project name>

# delete a workspace 
$ az ml workspace delete -g <resource group name> -a <experimentation account name> -n <workspace name>

# delete an experimentation account
$ az ml account experimentation delete -g <resource group name> -n <experimentation account name>
```

Můžete také odstranit projektů a pracovní prostory z v rámci aplikace Workbench.

## <a name="cant-open-file-if-project-is-in-onedrive"></a>Soubor nelze otevřít, pokud je projekt ve Onedrivu
Pokud máte Windows 10 Fall Creators Update a váš projekt je vytvořen v místní složce namapované na Onedrivu, můžete zjistit libovolný soubor nelze otevřít v aplikaci Workbench. Toto je kvůli chybě zavedené Fall Creators Update, která způsobí selhání ve složce Onedrivu kódu node.js. Chyba se opraví brzy ve Windows update, ale do té doby, nevytvářejte prosím projekty ve složce Onedrivu.

## <a name="file-name-too-long-on-windows"></a>Název souboru příliš dlouho ve Windows
Pokud používáte aplikaci Workbench na Windows, můžete narazit na výchozí maximální souboru 260 znaků názvu limit délky, které může přinášet jako chybu "systém nemůže najít zadanou cestu". Můžete upravit nastavení klíče registru a povolit mnohem delší cestu souboru. Kontrola [v tomto článku](https://msdn.microsoft.com/library/windows/desktop/aa365247%28v=vs.85%29.aspx?#maxpath) podrobné informace o tom, jak nastavit _MAX_PATH_ klíč registru.

## <a name="interrupt-cli-execution-output"></a>Přerušit provádění výstupu rozhraní příkazového řádku
Pokud jste pusťte se do experimentování ve službě spuštění pomocí `az ml experiment submit` nebo `az ml notebook start` a chcete přerušit výstup: 
- Ve Windows použijte kombinaci kláves Ctrl + Break z klávesnice
- V systému macOS pomocí Ctrl-C.

Mějte prosím na paměti, že pouze přeruší výstupní datový proud v okně rozhraní příkazového řádku. Je ve skutečnosti nezastaví úlohu, která je spouštěna. Pokud chcete zrušit probíhající úlohy, použijte `az ml experiment cancel -r <run_id> -t <target name>` příkazu.

Počítačích s Windows pomocí klávesnice, které nemají klíč přerušení, je to možné mezi ně patří služby Fn-B nebo Ctrl-Fn-B Fn + Esc. Pro konkrétní kombinaci kláves naleznete v dokumentaci od dodavatele hardwaru.

## <a name="docker-error-read-connection-refused"></a>Chyba docker "čtení: připojení bylo odmítnuto"
Při spouštění proti místní kontejner Dockeru, v některých případech může se zobrazit následující chyba: 
```
Get https://registry-1.docker.io/v2/: 
dial tcp: 
lookup registry-1.docker.io on [::1]:53: read udp [::1]:49385->[::1]:53: 
read: connection refused
```

Můžete to napravit změnou Docker Server DNS z `automatic` pevnou hodnotu `8.8.8.8`.

## <a name="remove-vm-execution-error-no-tty-present"></a>Odebrat Chyba spuštění virtuálního počítače "k dispozici žádné tty"
Při spouštění proti kontejneru Docker ve vzdáleném počítači s Linuxem, můžete se setkat se následující chybová zpráva:
```
sudo: no tty present and no askpass program specified.
``` 
To může nastat, pokud změna kořenové heslo virtuálního počítače s Ubuntu Linuxem pomocí webu Azure portal. 

Azure Machine Learning Workbench vyžaduje přístup bez hesla sudoers ke spouštění na vzdáleného hostitele. Nejjednodušší způsob, jak to udělat, je použití _visudo_ a upravte následující soubor (soubor může vytvořit Pokud neexistuje):

```
$ sudo visudo -f /etc/sudoers
```

>[!IMPORTANT]
>Je potřeba upravit soubor s _visudo_ a ne jiným příkazem. _visudo_ automaticky kontroluje všechny konfigurační soubory sudo, syntaxi a nepodařilo se vytvořit soubor syntakticky správný sudoers můžete zablokovat sudo.

Na konci souboru vložte následující řádek:

```
username ALL=(ALL) NOPASSWD:ALL
```

Kde _uživatelské jméno_ je název aplikace Azure Machine Learning Workbench použije k přihlášení na vzdáleného hostitele.

Na řádku musí být umístěné za #includedir "/ etc/sudoers.d", jinak mohou být přepsána jiné pravidlo.

Pokud máte složitější konfigurace sudo, můžete chtít dokumentaci sudo pro Ubuntu, které jsou k dispozici zde: https://help.ubuntu.com/community/Sudoers

Výše uvedené chyby může také dojít, pokud nepoužíváte založeného na Ubuntu virtuálního počítače s Linuxem v Azure jako cíl provádění. Podporujeme jenom virtuální počítač založený na Ubuntu Linuxu pro vzdálené spuštění. 

## <a name="vm-disk-is-full"></a>Virtuální počítač disk je plný
Ve výchozím nastavení při vytváření nového virtuálního počítače s Linuxem v Azure, získáte 30 GB disk pro operační systém. Modul docker ve výchozím nastavení používá stejný disk potažením dolů imagí a spuštěné kontejnery. To můžete vyplnit disk s operačním systémem a se zobrazí chyba "Virtuální počítač Disk je plný", když k ní dojde.

Rychlé opravy je odebrat všechny Image Dockeru, které už používáte. Následující příkaz Docker právě tohle dělá. (Samozřejmě budete muset SSH k virtuálnímu počítači za účelem provedení příkazu Docker z prostředí bash.)

```
$ docker system prune -a
```

Můžete také přidat datový disk a nakonfigurovat modul Docker datový disk k ukládání imagí. Tady je [přidání datového disku](https://docs.microsoft.com/azure/virtual-machines/linux/add-disk). Pak můžete [změnit, kam Docker ukládá obrázky](https://forums.docker.com/t/how-do-i-change-the-docker-image-installation-directory/1169).

Nebo, můžete rozbalit disk s operačním systémem a není nutné touch konfigurace modulu Docker. Tady je [jak lze rozvinout disk s operačním systémem](https://docs.microsoft.com/azure/virtual-machines/linux/expand-disks).

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

## <a name="sharing-c-drive-on-windows"></a>Sdílení jednotce C na Windows
Pokud jsou spuštěny v místním kontejneru Dockeru na Windows, nastavení `sharedVolumes` k `true` v `docker.compute` soubor `aml_config` může zvýšit výkon spuštění. Ale to vyžaduje, aby sdílet jednotce C v _Docker pro Windows nástrojů_. Pokud nemůžete sdílet jednotce C, zkuste následující tipy:

* Kontrola sdílení na jednotce C pomocí Průzkumníka souborů
* Otevřete nastavení síťového adaptéru a odinstalace a přeinstalace "Souborů a tiskáren sdílení v sítích Microsoft" pro vEthernet
* Otevřete nastavení dockeru a sdílet jednotce C z v rámci nastavení dockeru
* Změny hesla Windows ovlivní sdílení. Otevřete Průzkumníka souborů, dál sdílet. jednotka C a zadejte nové heslo.
* Brány firewall problému může dojít také při pokusu o sdílení jednotce C s Dockerem. To [Stack Overflow příspěvek](http://stackoverflow.com/questions/42203488/settings-to-windows-firewall-to-allow-docker-for-windows-to-share-drive/43904051) může být užitečné.
* Při sdílení jednotce C pomocí přihlašovacích údajů do domény, sdílení může přestat pracovat v sítích, ve kterém řadiči domény není dostupný (pro například domácí sítě, veřejná Wi-Fi atd.). Další informace najdete v tématu [tento příspěvek](https://blogs.msdn.microsoft.com/stevelasker/2016/06/14/configuring-docker-for-windows-volumes/).

Také můžete vyhnout sdílení problém na malé výkon, náklady, nastavením `sharedVolumne` k `false` v `docker.compute` souboru.

## <a name="wipe-clean-workbench-installation"></a>Vymazat čistou instalaci aplikace Workbench
Obvykle není nutné to udělat. Ale v případě, že můžete vymazat čistou instalaci, tady je postup:

- Ve Windows:
  - Nejprve se ujistěte, použijete _přidat nebo odebrat programy_ aplet v _ovládací panely_ odebrat _Azure Machine Learning Workbench_ vstupní aplikace.  
  - Pak si stáhnout a použít jednu z následujících skriptů:
    - [Skript příkazového řádku Windows](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.cmd).
    - [Skript prostředí Windows PowerShell](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_win.ps1). (Možná budete muset spustit `Set-ExecutionPolicy Unrestricted` v okně Powershellu se zvýšenými oprávněními oprávnění před spuštěním skriptu.)
- V systému macOS:
  - Stačí stáhnout a spustit [skriptu prostředí bash macOS](https://github.com/Azure/MachineLearning-Scripts/blob/master/cleanup/cleanup_mac.sh).

## <a name="azure-ml-using-a-different-python-location-than-the-azure-ml-installed-python-environment"></a>Služba Azure ML pomocí umístění pythonu jiné než Azure ML nainstalované prostředí pythonu
Kvůli změnám v aplikaci Azure Machine Learning Workbench uživatelé zaznamenat, že místní spuštění nemusí odkazovat na nainstalovala aplikace Azure ML Workbench už prostředí pythonu. K tomu může dojít, pokud uživatel mít jiné prostředí python nainstalován v počítači, a cesty "Python" je nastavená tak, aby odkazoval do daného prostředí. Chcete-li používat aplikaci Azure ML Workbench nainstalovaná prostředí Pythonu, postupujte podle těchto kroků:
- Přejděte k souboru local.compute v rámci vaší složky aml_config v kořenovém adresáři projektu.
- Změnit proměnné "pythonLocation" tak, aby odkazoval na fyzická cesta k aplikaci Azure ML workbench nainstalovat prostředí pythonu. Tuto cestu můžete získat dvěma způsoby:
    - Umístění pythonu v Azure ML lze nalézt v %localappdata%\AmlWorkbench\python\python.exe
    - můžete otevřít cmd z Azure ML Workbench, na příkazovém řádku zadejte python, importovat sys.exe, spusťte sys.executable a získání cesty z něj. 



## <a name="some-useful-docker-commands"></a>Některé užitečné příkazy Dockeru

Tady jsou některé užitečné příkazy Dockeru:

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
