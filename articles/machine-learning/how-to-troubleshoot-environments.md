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
ms.openlocfilehash: ec0c7d64f2145cdaf594cb903c072984f4d376a9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519125"
---
# <a name="troubleshoot-environment-image-builds"></a>Řešení potíží s sestaveními imagí prostředí

Naučte se řešit problémy s buildy imagí prostředí Docker a instalací balíčků.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Vyzkoušení [bezplatné nebo placené verze Azure Machine Learning](https://aka.ms/AMLFree).
* [Sada Azure Machine Learning SDK](/python/api/overview/azure/ml/install).
* Rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli)
* [Rozšíření CLI pro Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Chcete-li ladit místně, je nutné mít v místním systému funkční instalaci Docker.

## <a name="docker-image-build-failures"></a>Selhání sestavení imagí Docker
 
U většiny selhání sestavení imagí najdete hlavní příčinu v protokolu sestavení imagí.
Vyhledá protokol sestavení image z portálu Azure Machine Learning (20 \_ \_log.txt sestavení imagí \_ ) nebo pomocí protokolů, které spouštíte v rámci úlohy Azure Container Registry.
 
Je obvykle snazší reprodukování chyb místně. Zkontrolujte druh chyby a zkuste provést jednu z následujících `setuptools` akcí:

- Místní instalace závislosti conda: `conda install suspicious-dependency==X.Y.Z` .
- Nainstalovat závislost PIP místně: `pip install suspicious-dependency==X.Y.Z` .
- Zkuste vyhodnotit celé prostředí: `conda create -f conda-specification.yml` .

> [!IMPORTANT]
> Ujistěte se, že platforma a překladač na vašem místním výpočetním clusteru odpovídají těm ve vzdáleném výpočetním clusteru. 

### <a name="timeout"></a>Časový limit 
 
Následující problémy se sítí můžou způsobit chyby s časovým limitem:

- Nízká šířka pásma internetu
- Problémy serveru
- Velké závislosti, které nelze stáhnout pomocí zadaného nastavení časových limitů conda nebo PIP
 
K tomuto problému se označují zprávy podobné následujícím příkladům:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Pokud se zobrazí chybová zpráva, vyzkoušejte jedno z následujících možných řešení:
 
- Pro závislost zkuste použít jiný zdroj, jako je například Zrcadlová, Blob Storagea Azure nebo jiné kanály Pythonu.
- Aktualizujte conda nebo PIP. Pokud používáte vlastní soubor Docker, aktualizujte nastavení časového limitu.
- Některé verze PIP mají známé problémy. Zvažte přidání konkrétní verze PIP do závislostí prostředí.

### <a name="package-not-found"></a>Balíček se nenašel.

Následující chyby jsou nejběžnější pro selhání sestavení imagí:

- Nepovedlo se najít balíček conda:

   ```
   ResolvePackageNotFound: 
   - not-existing-conda-package
   ```

- Nepodařilo se najít zadaný balíček nebo verzi PIP:

   ```
   ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
   ERROR: No matching distribution found for invalid-pip-package
   ```

- Chybná závislost vnořeného PIP:

   ```
   ERROR: No matching distribution found for bad-package==0.0 (from good-package==1.0)
   ```

Ověřte, zda balíček existuje v zadaných zdrojích. K ověření závislostí PIP použijte [vyhledávání v PIP](https://pip.pypa.io/en/stable/reference/pip_search/) :

- `pip search azureml-core`

Pro závislosti conda použijte [vyhledávání conda](https://docs.conda.io/projects/conda/en/latest/commands/search.html):

- `conda search conda-forge::numpy`

Pro další možnosti zkuste:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Poznámky k instalačnímu programu

Zajistěte, aby pro zadanou platformu a verzi interpreta Pythonu existovala požadovaná distribuce.

V případě závislostí PIP přejděte na adresu `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` a zjistěte, jestli je požadovaná verze dostupná. Příklad zobrazíte tak, že přejdete na https://pypi.org/project/azureml-core/1.11.0/#files .

Pro závislosti conda se podívejte na balíček v úložišti kanálu.
Pro kanály udržované společností Anaconda, Inc. Ověřte [stránku balíčky Anaconda](https://repo.anaconda.com/pkgs/).

### <a name="pip-package-update"></a>Aktualizace balíčku PIP

Během instalace nebo aktualizace balíčku PIP může překladač potřebovat aktualizovat již nainstalovaný balíček, aby splňoval nové požadavky.
Odinstalace může selhat z různých důvodů vztahujících se k verzi PIP nebo způsobu instalace závislosti.
Nejběžnějším scénářem je, že závislost, kterou nainstaloval Conda, nejde odinstalovat pomocí PIP.
V tomto scénáři zvažte odinstalaci závislosti pomocí `conda remove mypackage` .

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Problémy instalačního programu

Některé verze instalačního programu mají v překladačích balíčků problémy, které mohou vést k selhání sestavení.

Pokud používáte vlastní základní Image nebo souboru Dockerfile, doporučujeme použít conda verze 4.5.4 nebo novější.

K instalaci závislostí PIP se vyžaduje balíček PIP. Pokud v prostředí není zadaná verze, použije se nejnovější verze.
Doporučujeme použít známou verzi PIP, abyste předešli přechodným problémům nebo zásadním změnám, které může způsobit nejnovější verze nástroje.

Pokud se zobrazí následující zpráva, zvažte možnost připnutí verze PIP ve vašem prostředí:

   ```
   Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.
   ```

Chyba podprocesu PIP:
   ```
   ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.
   ```

Instalaci PIP můžete zablokovat v nekonečné smyčce, pokud se v závislostech nepřeložitelné konflikty. Pokud pracujete místně, nadowngradete verzi PIP na < 20,3. V prostředí conda vytvořeném ze souboru YAML se tento problém zobrazí jenom v případě, že je conda-zfalšovat kanál s nejvyšší prioritou. Chcete-li zmírnit problém, explicitně zadejte PIP < 20,3 (! = 20,3 nebo = 20.2.4 PIN k jiné verzi) jako závislost conda v souboru specifikace conda.

## <a name="service-side-failures"></a>Selhání na straně služby

Pokud chcete řešit možné chyby na straně služby, přečtěte si následující scénáře.

### <a name="youre-unable-to-pull-an-image-from-a-container-registry-or-the-address-couldnt-be-resolved-for-a-container-registry"></a>Nemůžete načíst image z registru kontejnerů nebo nemůžete přeložit adresu registru kontejneru.

Možné problémy:
- Název cesty k registru kontejnerů nemusí být správně vyřešen. Ověřte, že názvy obrázků používají dvojitá lomítka a směr lomítka v systémech Linux a hostitelů Windows je správný.
- Pokud registr kontejneru za virtuální sítí používá privátní koncový bod v [nepodporované oblasti](../private-link/private-link-overview.md#availability), nakonfigurujte registr kontejneru pomocí koncového bodu služby (veřejného přístupu) z portálu a zkuste to znovu.
- Po umístění registru kontejneru za virtuální sítí spusťte [šablonu Azure Resource Manager](./how-to-network-security-overview.md) , aby pracovní prostor mohl komunikovat s instancí registru kontejneru.

### <a name="you-get-a-401-error-from-a-workspace-container-registry"></a>Zobrazí se chyba 401 z registru kontejneru pracovního prostoru.

Opětovná synchronizace klíčů úložiště pomocí [WS.sync_keys ()](/python/api/azureml-core/azureml.core.workspace.workspace#sync-keys--).

### <a name="the-environment-keeps-throwing-a-waiting-for-other-conda-operations-to-finish-error"></a>Prostředí udržuje možnost čekat na dokončení dalších operací conda... Chyba

Když se pokračuje v sestavení bitové kopie, conda klient sady SDK zamkne. Pokud uživatel úspěšně přerušil nebo zrušil chybu, conda zůstane ve stavu uzamčeno. Chcete-li tento problém vyřešit, odstraňte soubor zámku ručně. 

### <a name="your-custom-docker-image-isnt-in-the-registry"></a>Vlastní image Docker není v registru.

Ověřte, zda je použita [správná značka](./how-to-use-environments.md#create-an-environment) a zda `user_managed_dependencies = True` . `Environment.python.user_managed_dependencies = True` zakáže conda a použije nainstalované balíčky uživatele.

### <a name="you-get-one-of-the-following-common-virtual-network-issues"></a>Získáte jeden z následujících běžných problémů s virtuální sítí.

- Ověřte, že účet úložiště, výpočetní cluster a registr kontejneru jsou všechny ve stejné podsíti virtuální sítě.
- Pokud je registr kontejneru za virtuální sítí, nejde ho použít přímo k sestavení imagí. K sestavení imagí budete muset použít výpočetní cluster.
- Úložiště může být potřeba umístit za virtuální síť, pokud:
    - Použijte Inferencing nebo soukromé kolo.
    - Viz chyby služby 403 (Neautorizováno).
    - Nejde získat podrobnosti o imagi z Azure Container Registry.

### <a name="the-image-build-fails-when-youre-trying-to-access-network-protected-storage"></a>Při pokusu o přístup k úložišti chráněnému síti se nepodaří sestavení image.

- Úkoly Azure Container Registry nefungují za virtuální sítí. Pokud má uživatel svůj registr kontejneru za virtuální sítí, musí k sestavení image použít výpočetní cluster.
- Úložiště by mělo být za virtuální sítí, aby z něho bylo možné vyžádat závislosti.

### <a name="you-cant-run-experiments-when-storage-has-network-security-enabled"></a>Nemůžete spouštět experimenty, pokud má úložiště zapnuté zabezpečení sítě.

Pokud používáte výchozí image Docker a povolíte závislosti spravované uživatelem, použijte [značky služby](./how-to-network-security-overview.md) MicrosoftContainerRegistry a AzureFrontDoor. FirstParty pro povolených Azure Container registry a jeho závislosti.

 Další informace najdete v tématu [Povolení virtuálních sítí](./how-to-network-security-overview.md).

### <a name="you-need-to-create-an-icm"></a>Je potřeba vytvořit ICM.

Když vytváříte nebo přiřazujete ICM k metastore, zahrňte lístek podpory CSS, abychom mohli problém lépe pochopit.

## <a name="next-steps"></a>Další kroky

- [Výuka modelu strojového učení pro kategorizaci květin](how-to-train-scikit-learn.md)
- [Výuka modelu strojového učení s využitím vlastní image Docker](how-to-train-with-custom-image.md)
