---
title: Známé problémy a řešení potíží
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
ms.openlocfilehash: 80bb7af0f7ed20336ab08d4f3ca9639057b9c67f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149760"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Známé problémy a řešení problémů služby Azure Machine Learning

Tento článek vám pomůže najít a opravit chyby nebo při použití služby Azure Machine Learning došlo k selhání.

## <a name="visual-interface-issues"></a>Vizuální rozhraní problémy

Vizuální rozhraní pro strojové učení problémy se službou.

### <a name="long-compute-preparation-time"></a>Dlouho jsou výpočetní Přípravný čas

Vytvořit nový výpočet nebo vyvolávat odejít ze výpočetní trvá určitou dobu, může být několik minut nebo i delší dobu. Tým pracuje pro optimalizaci.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>Nelze spustit experiment obsahuje pouze datovou sadu 

Můžete chtít spustit experiment obsahuje pouze datovou sadu, která bude vizualizovat datovou sadu. Však není povolené spuštění experimentu obsahuje pouze datovou sadu ještě dnes. Tento problém aktivně opravujeme.
 
Před opravou můžete připojit datové sady pro jakýkoli modul transformace dat (Výběr sloupců v datové sadě, upravit Metadata, rozdělení dat atd.) a spusťte experiment. Potom můžete vizualizovat datové sady. 

Následující obrázek znázorňuje, jak: ![visulize dat](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>Problémy při instalaci sady SDK

**Chybová zpráva: Nelze odinstalovat 'PyYAML.**

Azure Machine Learning pro Python SDK: PyYAML je projektem organizace nainstalovaná distutils. Proto jsme nelze určit přesné soubory, které patří k němu dojde částečné odinstalovat. Pokud chcete pokračovat v instalaci sady SDK při tato chyba se ignoruje, použijte:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problémy s vytvářením, Azure Machine Learning Compute

Je vzácné pravděpodobné, že někteří uživatelé, kteří si vytvořili jejich pracovního prostoru Azure Machine Learning z portálu Azure portal před verze GA nemusí být možné vytvořit Azure Machine Learning Compute v daném pracovním prostoru. Můžete zvýšit žádost o podporu na službu nebo vytvořit nový pracovní prostor prostřednictvím portálu nebo pomocí sady SDK pro odblokování sami okamžitě.

## <a name="image-building-failure"></a>Chyba vytváření bitové kopie

Obrázek po nasazení webové služby vytvářet selhání. Alternativním řešením je přidat "pynacl == 1.2.1" jako pip závislosti systému Conda v souboru konfigurace image.

## <a name="deployment-failure"></a>Nasazení se nezdařilo.

Pokud zjistíte `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, změna SKU pro virtuální počítače použité ve vašem nasazení, který má více paměti.

## <a name="fpgas"></a>FPGA

Nebude moct nasazovat modely na FPGA, dokud si vyžádáte a byla schválena pro FPGA kvótu. Chcete-li požádat o přístup, vyplňte formulář žádosti o kvóty: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Automatizované strojové učení

Tensor Flow automatizované strojového učení v současné době nepodporuje verzi tensor flow 1.13. Instalace této verze způsobí závislosti balíčků přestane fungovat. Pracujeme na řešení tohoto problému v budoucí verzi. 

### <a name="experiment-charts"></a>Grafy experimentů

Binární klasifikace grafy (přesnosti a úplnosti, roc s více TŘÍDAMI, získat křivky atd.) je znázorněno v automatizovaných iterací experimentů v ML nejsou corectly vykreslení v uživatelském rozhraní od 4/12. Vykreslení grafu jsou aktuálně inverzní výsledky znázorňující, kde jsou uvedené lépe uskutečnitelný modely s nižší výsledky. Řešení je v části šetření.

## <a name="databricks"></a>Databricks

Problémy s Databricks a Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Chyba při instalaci balíčků

Instalace služby Azure Machine Learning SDK se nezdaří v Azure Databricks při instalaci dalších balíčků. Některé balíčky, jako například `psutil`, může způsobit konflikty. Aby nedocházelo k chybám instalace, instalace balíčků zmrazené verze knihovny. Tento problém má vztah k Databricks a ne do sady SDK služby Azure Machine Learning. Tomuto problému s dalšími knihovnami, může dojít příliš. Příklad:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Alternativně můžete použít skripty init-li zachovat nějaké potíže instalace knihovny jazyka Python. Tento přístup není oficiálně podporován. Další informace najdete v tématu [clusterového init skripty](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts).

### <a name="cancel-an-automated-machine-learning-run"></a>Zrušit automatizované strojového učení spuštění

Když používáte automatické strojového učení v Azure Databricks, zrušit běh a začněte nový experiment spustit, restartujte cluster Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 iterací pro automatizované machine learning

V automatizovaných machine learning nastavení, pokud máte více než 10 iterací, nastavte `show_output` k `False` odeslání příkazu run.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget pro službu Azure Machine Learning SDK/automatizované machine learning

Widget SDK služby Azure Machine Learning není podporována v poznámkového bloku Databricks, protože poznámkových bloků nelze analyzovat ve formátu HTML pomůcky. Ve widgetu na portálu můžete zobrazit pomocí tohoto kódu v Pythonu v buňce Azure Databricks Poznámkový blok:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Chyba při importu: Žádný modul s názvem "pandas.core.indexes.

Pokud tato chyba zobrazí při použití automatického strojového učení:

1. Spuštěním následujícího příkazu nainstalujte dva balíčky v clusteru Azure Databricks: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Odpojit a znovu připojte clusteru v poznámkovém bloku. 

Pokud tyto kroky není problém vyřešit, zkuste restartovat clusteru.

## <a name="azure-portal"></a>portál Azure

Pokud přejdete přímo na váš pracovní prostor z sdílet odkaz ze sady SDK nebo na portálu zobrazit, nebudete moct zobrazit stránka s přehledem normální s informace o předplatném v rozšíření. Nebudete také moci přepnout do jiného pracovního prostoru. Pokud je potřeba jiný pracovní prostor zobrazit, alternativním řešením je přejít přímo na [webu Azure portal](https://portal.azure.com) a vyhledejte název pracovního prostoru.

## <a name="diagnostic-logs"></a>Diagnostické protokoly

V některých případech může být užitečné, pokud může poskytnout diagnostické informace, pokud s žádostí o pomoc. Některé protokoly naleznete [webu Azure portal](https://portal.azure.com) a přejděte do pracovního prostoru a vyberte **pracovní prostor > Experiment > spuštění > protokoly**.

## <a name="resource-quotas"></a>Kvóty prostředků

Další informace o [kvóty prostředků](how-to-manage-quotas.md) můžete setkat při práci se službou Azure Machine Learning.

## <a name="authentication-errors"></a>Chyby ověřování

Pokud provádíte operaci správy na cílové výpočetní prostředí ze vzdálené úlohy, zobrazí se jedné z následujících chyb:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Například můžete dojde k chybě při pokusu vytvořit nebo připojit cílové výpočetní prostředí z kanálu služby ML, které je odeslána pro vzdálené spuštění.
