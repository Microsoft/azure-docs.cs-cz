---
title: Řešení potíží s imagemi prostředí
titleSuffix: Azure Machine Learning
description: Naučte se řešit problémy s sestaveními imagí prostředí a instalací balíčků.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 12/3/2020
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: b452c24b4b2ed6021910f267b9941f3829acccd8
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733299"
---
# <a name="troubleshoot-environment-image-builds"></a>Řešení potíží s sestaveními imagí prostředí
Naučte se řešit problémy s buildy imagí prostředí Docker a instalací balíčků.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure** Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).
* [Sada Azure Machine Learning SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest)
* [Rozšíření CLI pro Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Chcete-li ladit místně, je nutné mít v místním systému funkční instalaci Docker.

## <a name="docker-image-build-failures"></a>Selhání sestavení imagí Docker
 
Pro většinu selhání sestavení imagí najdete hlavní příčinu v protokolu sestavení imagí.
Protokol sestavení image můžete najít z portálu Azure Machine Learning (20 \_ \_log.txt sestavení imagí \_ ) nebo z vašich úloh ACR spustí protokoly.
 
Ve většině případů je snazší reprodukování chyb v místním prostředí. Zkontrolujte druh chyby a zkuste provést jednu z následujících `setuptools` akcí:

- Místní instalace závislosti conda `conda install suspicious-dependency==X.Y.Z`
- Nainstalovat závislost PIP místně `pip install suspicious-dependency==X.Y.Z`
- Zkuste vyhodnotit celé prostředí. `conda create -f conda-specification.yml`

> [!IMPORTANT]
> Zajistěte, aby platforma a překladač na vašich místních COMPUTE odpovídaly těm na vzdálených. 

### <a name="timeout"></a>Časový limit 
 
Problémy s časovým limitem se můžou vyskytnout u různých problémů se sítí:
- Nízká šířka pásma internetu
- Problémy serveru
- Velká závislost, kterou nelze stáhnout pomocí zadaného nastavení časového limitu conda nebo PIP
 
K tomuto problému se označují zprávy podobné následujícímu:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Možná řešení:
 
- Zkuste použít jiný zdroj závislosti, pokud je k dispozici jako Zrcadlová, BLOB Storage nebo jiné kanály Pythonu.
- Aktualizujte conda nebo PIP. Pokud se používá vlastní soubor Docker, aktualizujte nastavení časového limitu.
- Některé verze PIP mají známé problémy. Zvažte přidání konkrétní verze PIP do závislostí prostředí.

### <a name="package-not-found"></a>Balíček se nenašel.

Toto je nejběžnější případ selhání sestavení imagí.

- Nepovedlo se najít balíček conda.
        ```
        ResolvePackageNotFound: 
          - not-existing-conda-package
        ```

- Zadaný balíček nebo verzi PIP se nepovedlo najít.
        ```
        ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
        ERROR: No matching distribution found for invalid-pip-package
        ```

- Chybná závislost vnořeného PIP
        ```
        ERROR: No matching distribution found for bad-backage==0.0 (from good-package==1.0)
        ```

Ověřte, zda balíček existuje v zadaných zdrojích. K ověření závislostí PIP použijte [vyhledávání v PIP](https://pip.pypa.io/en/stable/reference/pip_search/) .

`pip search azureml-core`

Pro závislosti conda použijte [hledání conda](https://docs.conda.io/projects/conda/en/latest/commands/search.html).

`conda search conda-forge::numpy`

Další možnosti:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Poznámky k instalačnímu programu

Zajistěte, aby pro zadanou platformu a verzi interpreta Pythonu existovala požadovaná distribuce.

V případě závislostí PIP přejděte na `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` . a podívejte se, jestli je k dispozici požadovaná verze. Například https://pypi.org/project/azureml-core/1.11.0/#files.

V případě závislostí conda ověřte balíček v úložišti kanálu.
Pro kanály udržované společností Anaconda, Inc. se podívejte [sem](https://repo.anaconda.com/pkgs/).

### <a name="pip-package-update"></a>Aktualizace balíčku PIP

Během instalace nebo aktualizace balíčku PIP může překladač potřebovat aktualizovat již nainstalovaný balíček, aby splňoval nové požadavky.
Odinstalace může selhat z různých důvodů vztahujících se k verzi PIP nebo způsobu instalace závislosti.
Nejběžnějším scénářem je, že závislost nainstalovaná pomocí conda nemohla být odinstalována pomocí PIP.
V tomto scénáři zvažte odinstalaci závislosti pomocí `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Problémy instalačního programu

Některé verze instalačního programu mají v překladačích balíčků problémy, které mohou vést k selhání sestavení.

Pokud se používá vlastní základní Image nebo souboru Dockerfile, doporučujeme použít conda verze 4.5.4 nebo vyšší.

Pro instalaci závislostí PIP se vyžaduje balíček PIP. Pokud ve svém prostředí není zadaná verze, použije se nejnovější verze.
Doporučujeme použít známou verzi PIP, abyste předešli přechodným problémům nebo zásadním změnám, které mohou být způsobeny nejnovějšími verzemi nástroje.

Pokud se zobrazí některá z následujících zpráv, zvažte ve svém prostředí připnutí verze PIP:

`Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.`

`Pip subprocess error:
ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.`

Instalaci PIP je navíc možné zablokovat v nekonečné smyčce, pokud se v závislostech nepřeložitelné konflikty. Pokud pracujete místně, nadowngradujte verzi PIP na < 20,3. V prostředí conda vytvořeném ze souboru YAML se tento problém zobrazí jenom v případě, že kanál conda-zfalšovat je kanál s nejvyšší prioritou. Chcete-li zmírnit problém, explicitně zadejte PIP < 20,3 (! = 20,3 nebo = 20.2.4 PIN k jiné verzi) jako závislost conda v souboru specifikace conda.

## <a name="service-side-failures"></a>Selhání na straně služby

### <a name="unable-to-pull-image-from-mcraddress-could-not-be-resolved-for-container-registry"></a>Nepovedlo se přeložit image z MCR nebo adresy pro Container Registry.
Možné problémy:
- Název cesty k registru kontejnerů se nemusí správně přeložit. Ověřte, že názvy obrázků používají dvojitá lomítka a směr lomítka v hostitelích se systémem Linux vs je správný.
- Pokud ACR za virtuální sítí používá privátní koncový bod v [nepodporované oblasti](https://docs.microsoft.com/azure/private-link/private-link-overview#availability), nakonfigurujte ACR za virtuální sítí pomocí koncového bodu služby (veřejného přístupu) z portálu a zkuste to znovu.
- Po vložení ACR za virtuální síť se ujistěte, že je spuštěná [Šablona ARM](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) . To umožňuje pracovnímu prostoru komunikovat s instancí ACR.

### <a name="401-error-from-workspace-acr"></a>401 chyba z pracovního prostoru ACR
Opětovná synchronizace klíčů úložiště pomocí [WS.sync_keys ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#sync-keys--)

### <a name="environment-keeps-throwing-waiting-for-other-conda-operations-to-finish-error"></a>Prostředí neustále vyvolává čekání na dokončení dalších operací conda... Chyba
Když se pokračuje v sestavení bitové kopie, conda klient sady SDK zamkne. V případě, že došlo k chybě procesu nebo byl nesprávně zrušen uživatelem Conda, zůstane v uzamčeném stavu. Pokud to chcete vyřešit, ručně odstraňte soubor zámku. 

### <a name="custom-docker-image-not-in-registry"></a>Vlastní image Docker není v registru
Ověřte, zda je použita [správná značka](https://docs.microsoft.com/azure/machine-learning/how-to-use-environments#create-an-environment) a zda `user_managed_dependencies = True` . `Environment.python.user_managed_dependencies = True` zakáže conda a použije nainstalované balíčky uživatele.

### <a name="common-vnet-issues"></a>Běžné problémy virtuální sítě

1. Ověřte, že je účet úložiště, výpočetní cluster a Azure Container Registry všechny ve stejné podsíti virtuální sítě.
2. Když je ACR za virtuální sítí, nedá se použít přímo k sestavení imagí. K sestavení imagí je potřeba použít výpočetní cluster.
3. Úložiště může být potřeba umístit za virtuální síť v těchto případech:
    - Použití Inferencing nebo privátního kolečka
    - Zobrazení chyb služby 403 (Neautorizováno)
    - Nejde získat podrobnosti o imagi z ACR/MCR.

### <a name="image-build-fails-when-trying-to-access-network-protected-storage"></a>Při pokusu o přístup k úložišti chráněnému síti se nepovedlo sestavit image.
- ACR úkoly nefungují za virtuální sítí. Pokud má uživatel své ACR za virtuální sítí, musí k sestavení image použít výpočetní cluster.
- Úložiště by mělo být za virtuální sítí, aby bylo možné z něj vyžádat od něj závislosti. 

### <a name="cannot-run-experiments-when-storage-has-network-security-enabled"></a>Nejde spustit experimenty, pokud má úložiště zapnuté zabezpečení sítě.
Když použijete výchozí image Docker a povolíte závislosti spravované uživatelem, musíte použít [značky služby](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network) MicrosoftContainerRegistry a AzureFrontDoor. FirstParty pro povolených MCR a jeho závislosti.

 Další informace najdete v tématu [Povolení virtuální](https://docs.microsoft.com/azure/machine-learning/how-to-enable-virtual-network#azure-container-registry) sítě.

### <a name="creating-an-icm"></a>Vytváření ICM

Při vytváření a přiřazování ICM do metastore přidejte prosím lístek podpory CSS, abychom mohli problém lépe pochopit.

## <a name="next-steps"></a>Další kroky

- [Výuka modelu strojového učení pro kategorizaci květin](how-to-train-scikit-learn.md)
- [Výuka modelu strojového učení s využitím vlastní image Docker](how-to-train-with-custom-image.md)