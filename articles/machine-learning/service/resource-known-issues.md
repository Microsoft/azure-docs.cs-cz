---
title: Známé problémy & řešení potíží
titleSuffix: Azure Machine Learning service
description: Získat seznam známých problémů, řešení a řešení potíží pro službu Azure Machine Learning.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: 7d1bce7575272b7df185c4e261685d989f49436c
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716536"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Známé problémy a řešení problémů služby Azure Machine Learning

Tento článek vám pomůže najít a opravit chyby nebo při použití služby Azure Machine Learning došlo k selhání.

## <a name="visual-interface-issues"></a>Problémy s vizuálním rozhraním

Vizuální rozhraní pro problémy se službou Machine Learning.

### <a name="long-compute-preparation-time"></a>Čas přípravy na dlouhou výpočetní výkon

Vytváření nových výpočetních prostředků nebo EVOKE, které opouští výpočetní výkon, může trvat několik minut nebo i delší dobu. Tým pracuje na optimalizaci.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>Nelze spustit experiment obsahující pouze datovou sadu. 

Možná budete chtít spustit experiment pouze s datovou sadou, která bude vizualizovat datovou sadu. Není však povoleno spouštět experiment pouze v dnešní době. Aktivně opravujeme tento problém.
 
Před opravou můžete datovou sadu připojit k jakémukoli modulu transformace dat (výběr sloupců v datové sadě, upravit metadata, rozdělit data atd.) a spustit experiment. Pak můžete vizualizovat datovou sadu. 

Následující obrázek ukazuje, jak ![: visulize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>Problémy při instalaci sady SDK

**Chybová zpráva: Nelze odinstalovat ' PyYAML '**

Azure Machine Learning SDK pro Python: PyYAML je distutils nainstalovaný projekt. Proto nemůžeme přesně určit, které soubory do ní patří, pokud dojde k částečné odinstalaci. Pokud chcete pokračovat v instalaci sady SDK při tato chyba se ignoruje, použijte:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problémy s vytvářením, Azure Machine Learning Compute

Je vzácné pravděpodobné, že někteří uživatelé, kteří si vytvořili jejich pracovního prostoru Azure Machine Learning z portálu Azure portal před verze GA nemusí být možné vytvořit Azure Machine Learning Compute v daném pracovním prostoru. Můžete zvýšit žádost o podporu na službu nebo vytvořit nový pracovní prostor prostřednictvím portálu nebo pomocí sady SDK pro odblokování sami okamžitě.

## <a name="image-building-failure"></a>Chyba vytváření bitové kopie

Obrázek po nasazení webové služby vytvářet selhání. Alternativním řešením je přidat "pynacl == 1.2.1" jako pip závislosti systému Conda v souboru konfigurace image.

## <a name="deployment-failure"></a>Selhání nasazení

Pokud si `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`myslíte, změňte SKU pro virtuální počítače používané ve vašem nasazení na jednu, která má více paměti.

## <a name="fpgas"></a>FPGA

Nebude moct nasazovat modely na FPGA, dokud si vyžádáte a byla schválena pro FPGA kvótu. Chcete-li požádat o přístup, vyplňte formulář žádosti o kvóty: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Automatizované strojové učení

Služba tensor Flow automatizovaného strojového učení v současné době nepodporuje tensor Flow verze 1,13. Instalace této verze způsobí, že se závislosti balíčku přestanou pracovat. Pracujeme na řešení tohoto problému v budoucí verzi. 

### <a name="experiment-charts"></a>Grafy experimentů

Binární klasifikační grafy (přesnost-odvolání, ROC, křivka získání atd.) zobrazené v automatizovaných iteracích experimentu se nevykresluje v uživatelském rozhraní, od 4/12. V grafu jsou v současné době zobrazeny inverzní výsledky, kde je lepší provádět modely s nižšími výsledky. Řešení je v šetření.

## <a name="databricks"></a>Databricks

Problémy s Databricks a Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Chyba při instalaci balíčků

Instalace sady Azure Machine Learning SDK se v Azure Databricks při instalaci dalších balíčků nezdařila. Některé balíčky, jako například `psutil`, může způsobit konflikty. Aby nedocházelo k chybám při instalaci, nainstalujte balíčky zmrazením verze knihovny. Tento problém se vztahuje k datacihlům a nikoli k sadě SDK služby Azure Machine Learning. Tento problém se může vyskytnout i u jiných knihoven. Příklad:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Případně můžete použít skripty init, pokud máte potíže s instalací v knihovně Python. Tento přístup není oficiálně podporován. Další informace najdete v tématu [skripty init v oboru clusteru](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Zrušení automatizovaného spuštění strojového učení

Když v Azure Databricks používáte automatizované funkce machine learningu, zrušíte spuštění a spustíte nový experiment, restartujete cluster Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 iterací pro automatizované strojové učení

V případě automatizovaného nastavení strojového učení, pokud máte více než 10 iterací `show_output` , `False` nastavte na hodnotu při odeslání běhu.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget pro sadu Azure Machine Learning SDK/automatizované Machine Learning

Pomůcka Azure Machine Learning SDK není v poznámkovém bloku datacihly podporovaná, protože poznámkové bloky nemůžou analyzovat widgety HTML. Widget můžete zobrazit na portálu pomocí tohoto kódu Pythonu v buňce s Azure Databricksm poznámkového bloku:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Chyba importu: Žádný modul s názvem PANDAS. Core. indexs

Pokud se tato chyba zobrazí při použití automatizovaného strojového učení:

1. Spuštěním tohoto příkazu nainstalujete do clusteru Azure Databricks dva balíčky: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Odpojte a znovu připojte cluster ke svému poznámkovému bloku. 

Pokud tyto kroky problém nevyřeší, zkuste restartovat cluster.

### <a name="failtosendfeather"></a>FailToSendFeather

Pokud se při čtení `FailToSendFeather` dat v Azure Databricks clusteru zobrazí chyba, přečtěte si následující řešení:

* Upgradujte `azureml-sdk[automl_databricks]` balíček na nejnovější verzi.
* Přidejte `azure-dataprep` 1.1.8 verze nebo vyšší.
* Přidejte `pyarrow` verzi 0,11 nebo vyšší.

## <a name="azure-portal"></a>portál Azure

Pokud přejdete přímo na váš pracovní prostor z sdílet odkaz ze sady SDK nebo na portálu zobrazit, nebudete moct zobrazit stránka s přehledem normální s informace o předplatném v rozšíření. Nebudete také moci přepnout do jiného pracovního prostoru. Pokud potřebujete zobrazit jiný pracovní prostor, alternativní řešení je přejít přímo na [Azure Portal](https://portal.azure.com) a vyhledat název pracovního prostoru.

## <a name="diagnostic-logs"></a>Diagnostické protokoly

V některých případech může být užitečné, pokud může poskytnout diagnostické informace, pokud s žádostí o pomoc. Pokud chcete zobrazit některé protokoly, navštivte [Azure Portal](https://portal.azure.com) a přejděte do svého pracovního prostoru a vyberte **pracovní prostor > Experiment > Run > log**.

## <a name="resource-quotas"></a>Kvóty prostředků

Další informace o [kvóty prostředků](how-to-manage-quotas.md) můžete setkat při práci se službou Azure Machine Learning.

## <a name="authentication-errors"></a>Chyby ověřování

Pokud provádíte operaci správy na výpočetním cíli ze vzdálené úlohy, zobrazí se jedna z následujících chyb:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Například se zobrazí chyba, pokud se pokusíte vytvořit nebo připojit výpočetní cíl z kanálu ML, který je odeslán pro vzdálené spuštění.

## <a name="overloaded-azurefile-storage"></a>Přetížené úložiště AzureFile

Pokud se zobrazí chyba "nepovedlo se odeslat soubory projektu do pracovního adresáře v AzureFile, protože úložiště je přetížené", použijte následující alternativní řešení.

Pokud používáte sdílenou složku pro jiné úlohy, jako je třeba přenos dat, doporučuje se použít objekty blob, aby bylo možné používat pro odeslání spuštění sdílení souborů. Úlohy můžete rozdělit také mezi dva různé pracovní prostory.
