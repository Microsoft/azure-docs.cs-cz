---
title: "Azure ML Workbench poznámky k sprintu 2 2017 prosinec"
description: "Tento dokument podrobně popisuje aktualizace pro verzi sprintu 2 Azure ML"
services: machine-learning
author: hning86
ms.author: haining
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 12/04/2017
ms.openlocfilehash: cb620040f8c82fd2015f93963d99739d38ec6db3
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="sprint-2---december-2017"></a>Sprint 2 – prosinec 2017 

#### <a name="version-number-01171115263"></a>Číslo verze: 0.1.1711.15263

>Zde je, jak můžete [najít číslo verze](known-issues-and-troubleshooting-guide.md).

Vítá vás třetí aktualizace Azure Machine Learning Workbench. Tato aktualizace obsahuje vylepšení v aplikaci workbench, rozhraní příkazového řádku (CLI) a back endové služby. Děkujeme pro odesílání úsměvy a frowns. Řadu následující aktualizace jsou vytvářeny jako přímý výsledky váš názor. 

## <a name="notable-new-features"></a>Upozorňují na důležité nové funkce
- [Podpora pro SQL Server a databázi SQL Azure jako zdroj dat](data-prep-appendix2-supported-data-sources.md#types) 
- [Hloubkové Learning na Spark s podporou GPU pomocí MMLSpark](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- [Všechny kontejnery AML jsou kompatibilní s Azure IoT hraniční zařízení po nasazení (žádné další kroky požadované)](http://aka.ms/aml-iot-edge-blog)
- Seznam registrovaných modelu a podrobnosti zobrazení k dispozici portál Azure
- Přístup k výpočetní cílů pomocí ověřování na základě klíčů SSH kromě přístupu pomocí uživatelského jména nebo hesla. 
- Nový nástroj Inspector vzor frekvence v datech rychlá příprava prostředí. 

## <a name="detailed-updates"></a>Podrobné aktualizace
Následuje seznam podrobné aktualizací v oblasti Azure Machine Learning v této sprintu jednotlivých součástí.

### <a name="installer"></a>Instalační program
- Instalační program může vlastní aktualizace, která chyb opravy a nové funkce, může být podporovaný bez nutnosti znovu ji nainstalujte uživatele

### <a name="workbench-authentication"></a>Workbench ověřování
- Více opravy pro ověřování systému. Dejte nám vědět, pokud se stále setkáváte přihlášení problémy.
- Změny uživatelského rozhraní, které bylo snazší najít nastavení správce serveru Proxy.

### <a name="workbench"></a>Workbench
- Zobrazení jen pro čtení souboru má nyní světla modré pozadí
- Vpravo zjistitelnost více přesunutý tlačítko Upravit.
- formáty souborů "ipynb", "dprep" a "dsource" lze vykreslit nyní ve formátu raw textu
- Nástroje workbench má nyní nové prostředí pro úpravy, které mohou uživatelé směrem pomocí externí IDE upravit skripty a používat Workbench jenom pro typy souborů, které mají bohaté prostředí úpravy (například poznámkových bloků, zdroje dat, Data přípravy balíčky) upravit
- Načítání seznamu pracovních prostorů a projektů, které má uživatel přístup k je výrazně rychlejší nyní

### <a name="data-preparation"></a>Příprava dat 
- Vzor Inspector frekvence zobrazíte vzory řetězec ve sloupci. Můžete také filtrovat svá data pomocí vzorce. To ukazuje zobrazení podobně jako hodnota počty inspector. Rozdílem je, že vzor frekvence zobrazuje počet jedinečný vzory dat, než počet jedinečných data. Můžete také filtrovat příchozí nebo odchozí všechny řádky splňující určité vzor.

![Obrázek vzor frekvence inspector na číslo produktu](media/release-notes-sprint-2/pattern-inspector-product-number.png)

- Vylepšení výkonu při doporučujeme edge případech zkontrolujte v transformaci 'odvozena sloupec podle příkladu.

- [Podpora pro SQL Server a databázi SQL Azure jako zdroj dat](data-prep-appendix2-supported-data-sources.md#types) 

![Obrázek vytvoříte nový zdroj dat serveru SQL](media/release-notes-sprint-2/sql-server-data-source.png)

- Povolit zobrazení "na první pohled" počty řádků a sloupců

![Obrázek sloupce počet řádků v odkladu](media/release-notes-sprint-2/row-col-count.png)

- Příprava dat je povolena v kontextech všechny výpočetní
- Zdroje dat, které používají databázi systému SQL Server jsou povolené všechny výpočetní kontexty
- Data připravená data mřížky sloupce je možné filtrovat podle typu dat
- Opravené problém s převodem více sloupců k datu
- Pevné problém tento uživatel může vybrat výstupního sloupce jako zdroj v odvozena sloupec podle příkladu Pokud uživatel změnil název výstupního sloupce v rozšířeném režimu.

### <a name="job-execution"></a>Provádění úlohy
Teď můžete vytvářet a přístup k remotedocker nebo clusteru cíli typu výpočetní s SSH klíč ověřování pomocí následujících kroků:
- Připojte cíl výpočetní pomocí následujícího příkazu v rozhraní příkazového řádku

    ```azure-cli
    $ az ml computetarget attach remotedocker --name "remotevm" --address "remotevm_IP_address" --username "sshuser" --use-azureml-ssh-key
    ```
[!NOTE] -k (nebo--použití azureml-ssh-key) možnost v příkazu určuje pro vygenerování a použití klíče SSH.

- Azure ML Workbench bude generovat veřejný klíč a výstup, v konzole. Přihlaste se k cíli výpočetní pomocí stejného uživatelského jména a soubor ~/.ssh/authorized_keys připojit k tomuto veřejnému klíči.

- Můžete připravit tento cíl výpočetní a použít jej pro spuštění a Azure ML Workbench budou používat tento klíč pro ověřování.  

Další informace o vytváření výpočetní cíle, najdete v části [konfigurace služby Azure Machine Learning experimentování](experimentation-service-configuration.md)

### <a name="visual-studio-tools-for-ai"></a>Visual Studio Tools pro AI
- Přidání podpory pro [Visual Studio Tools for AI](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vstoolsai-vs2017). 

### <a name="command-line-interface-cli"></a>Rozhraní příkazového řádku (CLI)
- Přidat `az ml datasource create` příkaz umožňuje vytváření zdroje dat z příkazového řádku

### <a name="model-management-and-operationalization"></a>Model správy a Operationalization
- [Všechny kontejnery AML jsou kompatibilní s Azure IoT hraniční zařízení při operationalized (žádné další kroky požadované)](http://aka.ms/aml-iot-edge-blog) 
- Vylepšení v rozhraní příkazového řádku o16n chybové zprávy
- Opravy chyb v portálu pro správu modelu UX  
- Konzistentní písmeno velká a malá písmena pro atributy model správy na stránce podrobností
- V reálném čase vyhodnocování časový limit volání nastavena na 60 sekund
- Registrovaný modelu seznamu a podrobností zobrazení k dispozici na portálu Azure

![Podrobné informace o modelu portálu](media/release-notes-sprint-2/model-list.jpg)

![Přehled modelu portálu](media/release-notes-sprint-2/model-overview-portal.jpg)

### <a name="mmlspark"></a>MMLSpark
- Učení na Spark s přímým [podporu GPU](https://github.com/Azure/mmlspark/blob/master/docs/gpu-setup.md)
- Podpora pro správce prostředků šablony pro nasazení snadno prostředků
- Podpora pro ekosystému SparklyR
- [Integrace AZTK](https://github.com/Azure/aztk/wiki/Spark-on-Azure-for-Python-Users#optional-set-up-mmlspark)

### <a name="sample-projects"></a>Ukázkové projekty
- [Iris](https://github.com/Azure/MachineLearningSamples-Iris) a [MMLSpark](https://github.com/Azure/mmlspark) ukázky aktualizovat pomocí nové verze sady SDK Azure ML

## <a name="breaking-changes"></a>NEJNOVĚJŠÍ ZMĚNY
- Povýší `--type` přepínač ve `az ml computetarget attach` na dílčí příkaz. 

    - `az ml computetarget attach --type remotedocker` je nyní `az ml computetarget attach remotedocker`
    - `az ml computetarget attach --type cluster` je nyní `az ml computetarget attach cluster`
