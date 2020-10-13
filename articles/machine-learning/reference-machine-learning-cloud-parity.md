---
title: Parita mezi veřejnými a svrchovanými regiony
titleSuffix: Azure Machine Learning
description: Některé funkce Azure Machine Learning, jako jsou funkce Public Preview, můžou být dostupné jenom v oblastech veřejného cloudu. V tomto článku najdete seznam funkcí, které jsou dostupné i v oblastech Azure Government, Azure Německo a Azure Čína 21Vianet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: andzha
author: Anurzeuii
ms.date: 08/24/2020
ms.custom: references_regions
ms.openlocfilehash: 4a6f09fdff82b8e86c7fe75018c5267dba3c1b4a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90892972"
---
# <a name="azure-machine-learning-sovereign-cloud-parity"></a>Parita cloudu Azure Machine Learning svrchovan

Přečtěte si, jaké funkce Azure Machine Learning jsou dostupné v svrchovaných cloudových oblastech. 

V seznamu globálních oblastí Azure existuje několik oblastí "svrchovaného", které obsluhují konkrétní trhy. Například Azure Government a oblasti Azure Čína 21Vianet. V současné době je Azure Machine Learning nasazena do následujících svrchovaných cloudových oblastí:

* Azure Government oblasti **pro nás – Arizona** a **US-) – Virginia**.
* Azure Čína 21Vianet region **Čína – východ – 2**.

> [!TIP]
> K rozlišení mezi svrchovanými a nesvrchovanými regiony se v tomto článku používá pojem __veřejný cloud__ , který odkazuje na jiné oblasti než svrchované.

Cílem je poskytnout maximální paritu mezi veřejným cloudem a oblastmi svrchovaného světa. Všechny funkce Azure Machine Learning budou v těchto oblastech k dispozici do **30 dnů od GA** (všeobecně dostupné) v našem veřejném cloudu. V těchto oblastech také umožňuje vybrat počet funkcí verze Preview. Níže jsou uvedené aktuální rozdíly v paritě mezi našimi svrchovanými a veřejnými cloudy.

## <a name="azure-government"></a>Azure Government 

| Příznak | Stav veřejného cloudu  | US-Virginia | US-Arizona| 
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|:-------------:|
| **Automatizované strojové učení** | | | |
| Vytváření a spouštění experimentů v poznámkových blocích                                    | GA                   | ANO                | ANO         |
| Vytváření a spouštění experimentů ve webovém prostředí studia                        | Public Preview       | ANO                | ANO         |
| Špičkové možnosti prognózování v oboru                                  | GA                   | ANO                | ANO         |
| Podpora hloubkového učení a dalších pokročilých učících                      | GA                   | ANO                | ANO         |
| Podpora velkých objemů dat (až 100 GB)                                          | Public Preview       | ANO                | ANO         |
| Integrace Azure Databricks                                              | GA                   | NO                 | NO          |
| Integrace SQL, CosmosDB a HDInsight                                   | GA                   | ANO                | ANO         |
| **Machine Learning kanály** |   |  | | 
| Vytváření, spouštění a publikování kanálů pomocí Azure ML SDK                   | GA                   | ANO                | ANO         |
| Vytváření koncových bodů kanálu pomocí sady Azure ML SDK                           | GA                   | ANO                | ANO         |
| Vytváření, úpravy a odstraňování naplánovaných běhů kanálů pomocí sady Azure ML SDK | GA                   | ODPOVÍ               | ODPOVÍ        |
| Zobrazení podrobností o spuštění kanálu v studiu                                        | GA                   | ANO                | ANO         |
| Vytváření, spouštění, vizualizace a publikování kanálů v Azure ML designeru          | GA      | ANO                | ANO         |
| Azure Databricks integrace s kanálem ML                             | GA                   | NO                 | NO          |
| Vytváření koncových bodů kanálu v Návrháři Azure ML                             | GA      | ANO                | ANO         |
| **Integrované poznámkové bloky** |   |  | | 
| Notebook a sdílení souborů v pracovním prostoru                                        | GA                   | ANO                | ANO         |
| Podpora R a Pythonu                                                       | GA                   | ANO                | ANO         |
| Podpora virtuální sítě                                                    | Public Preview       | NO                 | NO          |
| **Instance služby Compute** |   |  | | 
| Spravované výpočetní instance pro integrované poznámkové bloky                         | GA                   | ANO                | ANO         |
| Integrace Jupyter, JupyterLab                                            | GA                   | ANO                | ANO         |
| Podpora Virtual Network (VNet)                                             | Public Preview       | ANO                | ANO         |
| **Podpora SDK** |  |  | | 
| Podpora sady R SDK                                                              | Public Preview       | ANO                | ANO         |
| Podpora sady Python SDK                                                         | GA                   | ANO                | ANO         |
| **Zabezpečení** |   | | | 
| Podpora Virtual Network (VNet) pro školení                                | GA                   | ANO                | ANO         |
| Podpora Virtual Network (VNet) pro odvození                               | GA                   | ANO                | ANO         |
| Bodování ověřování koncového bodu                                            | Public Preview       | ANO                | ANO         |
| Privátní odkaz na pracoviště                                                     | Public Preview       | NO                 | NO          |
| ACI za virtuální sítí                                                            | Public Preview       | NO                 | NO          |
| ACR za virtuální sítí                                                            | Public Preview       | NO                 | NO          |
| Privátní IP adresa clusteru AKS                                                  | Public Preview       | NO                 | NO          |
| **Výpočetní služby** |   | | |
| Správa kvót napříč pracovními prostory                                         | GA                   | ANO                | ANO         |
| **Data pro strojové učení** |   | | |
| Vytvoření, zobrazení nebo úprava datových sad a úložišť dat ze sady SDK                  | GA                   | ANO                | ANO         |
| Vytvoření, zobrazení nebo úprava datových sad a úložišť dat z uživatelského rozhraní                   | GA                   | ANO                | ANO         |
| Zobrazení, úpravy nebo odstranění sledování posunu datové sady ze sady SDK                   | Public Preview       | ANO                | ANO         |
| Zobrazení, úpravy nebo odstranění sledování posunu datové sady z uživatelského rozhraní                    | Public Preview       | ANO                | ANO         |
| **Životní cyklus machine learningu** |   | | |
| Profilace modelu                                                            | GA                   | ANO                | ČÁSTEČNÉ     |
| Rozšíření Azure DevOps pro Machine Learning & Azure ML CLI         | GA                   | ANO                | ANO         |
| Modely s hardwarovou akcelerací založené na FPGA                                     | GA                   | NO                 | NO          |
| Integrace Visual Studio Code                                             | Public Preview       | NO                 | NO          |
| Integrace Event Gridu                                                     | Public Preview       | NO                 | NO          |
| Integrace Azure Stream Analytics s Azure Machine Learning               | Public Preview       | NO                 | NO          |
| **Označování** |   | | |
| Označování Portál pro správu projektu                                        | GA                   | ANO                | ANO         |
| Portál popisků                                                            | GA                   | ANO                | ANO         |
| Označování pomocí privátních zaměstnanců                                          | GA                   | ANO                | ANO         |
| Popis s asistencí ML (klasifikace obrázků a detekce objektů)           | Public Preview       | ANO                | ANO         |
| **Zodpovědné strojové učení** |   | | |
| Vysvětlení v uživatelském rozhraní                                                       | Public Preview       | NO                 | NO          |
| Rozdílová ochrana osobních údajů WhiteNoise Toolkit                                    | OSS                  | NO                 | NO          |
| vlastní značky v Azure Machine Learning k implementaci datových listů              | GA                   | NO                 | NO          |
| Spravedlivá integrace AzureML                                               | Public Preview       | NO                 | NO          |
| Sada SDK pro vyhodnocení                                                      | GA                   | ANO                | ANO         |
| **Školení** |   | | |
| Streamování protokolů experimentování                                              | GA                   | ANO                | ANO         |
| Posílení učení                                                     | Public Preview       | NO                 | NO          |
| Uživatelské rozhraní experimentování                                                         | GA                   | ANO                | ANO         |
| .NET Integration ML.NET 1,0                                                | GA                   | ANO                | ANO         |
| **Odvození** |   | | |
| Batch Inferencing                                                          | GA                   | ANO                | ANO         |
| Data Box Edge s FPGA                                                    | Public Preview       | NO                 | NO          |
| **Další** |   | | |
| Open Datasets                                                              | Public Preview       | ANO                | ANO         |
| Vlastní Kognitivní hledání                                                    | Public Preview       | ANO                | ANO         |
| Mnoho modelů                                                                | Public Preview       | NO                 | NO          |


### <a name="azure-government-scenarios"></a>Azure Government scénáře

| Scénář                                                    | US-Virginia | US-Arizona| Omezení  |
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|-------------|
| **Obecné nastavení zabezpečení** |   | | |
| Komunikace privátní sítě mezi službami                                     | NO | NO | Momentálně není k dispozici žádný privátní odkaz. | 
| Zakázání/řízení přístupu k Internetu (příchozích a odchozích) a konkrétní virtuální sítě | ČÁSTEČNÉ| ČÁSTEČNÉ   | ACR za virtuální síť není k dispozici ve Azure Government-dvojité kontrole na ACI. | 
| Umístění všech přidružených prostředků a služeb  | ANO | ANO |  |
| Šifrování v klidovém režimu a při přenosu.                                                 | ANO | ANO |  |
| Přístup k výpočetním prostředkům rootem a SSH.                                          | ANO | ANO |  |
| Udržování zabezpečení nasazených systémů (instancí, koncových bodů atd.), včetně Endpoint Protection, opravování a protokolování |  ČÁSTEČNÉ|  ČÁSTEČNÉ |ACI za virtuální sítí a soukromým koncovým bodem není momentálně k dispozici. |                                  
| Řízení (zakázání/omezení/omezení) používání integrace ACI/AKS                    | ČÁSTEČNÉ| ČÁSTEČNÉ |ACI za virtuální sítí a soukromým koncovým bodem není momentálně k dispozici.|
| Role-Based Access Control (RBAC) – vytváření vlastních rolí                           | ANO | ANO |  |
| Řízení přístupu k ACR imagí, které používá služba ML (poskytnuté/udržované v Azure versus vlastní)  |ČÁSTEČNÉ|  ČÁSTEČNÉ | ACR za privátním koncovým bodem a virtuální síť nejsou podporované v Azure Government |
| **Obecné použití Machine Learning služby** |  | | |
| Možnost mít vývojové prostředí pro sestavování modelu, výuku modelu, jeho hostování jako koncového bodu a jeho využití prostřednictvím WebApp     | ANO | ANO |  |
| Možnost vyžádat data z ADLS (Data Lake Storage)                                 |ANO | ANO |  |
| Možnost vyžádat si data z Azure Blob Storage                                       |ANO | ANO |  |



### <a name="additional-azure-government-limitations"></a>Další omezení Azure Government

* V případě Azure Machine Learning výpočetních instancí není v Azure Government dostupná možnost aktualizace tokenu trvajícího více než 24 hodin.
* Profilace modelů nepodporuje 4 procesory v US-Arizona oblasti.   
* Ukázkové poznámkové bloky nemusí v Azure Government fungovat, pokud potřebují přístup k veřejným datům.
* IP adresy: příkaz CLI použitý ve virtuální síti [a pokyny pro vynucené tunelování](how-to-secure-training-vnet.md#forced-tunneling) nevrací ROZSAHy IP adres. Místo toho použijte [rozsahy IP adres Azure a značky služeb pro Azure Government](https://www.microsoft.com/download/details.aspx?id=57063) .
* U naplánovaných kanálů poskytujeme i mechanismus triggeru založený na objektech blob. Tento mechanismus není podporován pro CMK pracovní prostory. Pro povolení triggeru založeného na objektu BLOB pro pracovní prostory CMK je nutné provést další nastavení. Další informace najdete v tématu spuštění [kanálu strojového učení z aplikace logiky](how-to-trigger-published-pipeline.md).
* Brány firewall: při použití Azure Government oblasti přidejte do nastavení brány firewall následující další hostitele:

    * Pro Arizona použití: `usgovarizona.api.ml.azure.us`
    * Pro) – Virginia použití: `usgovvirginia.api.ml.azure.us`
    * Pro obě: `graph.windows.net` 


## <a name="azure-china-21vianet"></a>Azure (Čína) 21Vianet 

| Příznak                                       | Stav veřejného cloudu | CH – východ – 2 | CH-sever-3 |
|----------------------------------------------------------------------------|:------------------:|:--------------------:|:-------------:|
| **Automatizované strojové učení** |    | | |
| Vytváření a spouštění experimentů v poznámkových blocích                                    | GA               | ANO       | Není k dispozici        |
| Vytváření a spouštění experimentů ve webovém prostředí studia                        | Public Preview   | ANO       | Není k dispozici        |
| Špičkové možnosti prognózování v oboru                                  | GA               | ANO       | Není k dispozici        |
| Podpora hloubkového učení a dalších pokročilých učících                      | GA               | ANO       | Není k dispozici        |
| Podpora velkých objemů dat (až 100 GB)                                          | Public Preview   | ANO       | Není k dispozici        |
| Integrace Azure Databricks                                              | GA               | NO        | Není k dispozici        |
| Integrace SQL, CosmosDB a HDInsight                                   | GA               | ANO       | Není k dispozici        |
| **Machine Learning kanály** |    | | |
| Vytváření, spouštění a publikování kanálů pomocí Azure ML SDK                   | GA               | ANO       | Není k dispozici        |
| Vytváření koncových bodů kanálu pomocí sady Azure ML SDK                           | GA               | ANO       | Není k dispozici        |
| Vytváření, úpravy a odstraňování naplánovaných běhů kanálů pomocí sady Azure ML SDK | GA               | ANO       | Není k dispozici        |
| Zobrazení podrobností o spuštění kanálu v studiu                                        | GA               | ANO       | Není k dispozici        |
| Vytváření, spouštění, vizualizace a publikování kanálů v Azure ML designeru          | GA  | ANO       | Není k dispozici        |
| Azure Databricks integrace s kanálem ML                             | GA               | NO        | Není k dispozici        |
| Vytváření koncových bodů kanálu v Návrháři Azure ML                             | GA   | ANO       | Není k dispozici        |
| **Integrované poznámkové bloky** |   | | |
| Notebook a sdílení souborů v pracovním prostoru                                        | GA               | ANO       | Není k dispozici        |
| Podpora R a Pythonu                                                       | GA               | ANO       | Není k dispozici        |
| Podpora virtuální sítě                                                    | Public Preview   | NO        | Není k dispozici        |
| **Instance služby Compute** |    | | |
| Spravované výpočetní instance pro integrované poznámkové bloky                         | GA               | NO        | Není k dispozici        |
| Integrace Jupyter, JupyterLab                                            | GA               | ANO       | Není k dispozici        |
| Podpora Virtual Network (VNet)                                             | Public Preview   | ANO       | Není k dispozici        |
| **Podpora SDK** |    | | |
| Podpora sady R SDK                                                              | Public Preview   | ANO       | Není k dispozici        |
| Podpora sady Python SDK                                                         | GA               | ANO       | Není k dispozici        |
| **Zabezpečení** |   | | |
| Podpora Virtual Network (VNet) pro školení                                | GA               | ANO       | Není k dispozici        |
| Podpora Virtual Network (VNet) pro odvození                               | GA               | ANO       | Není k dispozici        |
| Bodování ověřování koncového bodu                                            | Public Preview   | ANO       | Není k dispozici        |
| Privátní odkaz na pracoviště                                                     | Public Preview   | NO        | Není k dispozici        |
| ACI za virtuální sítí                                                            | Public Preview   | NO        | Není k dispozici        |
| ACR za virtuální sítí                                                            | Public Preview   | NO        | Není k dispozici        |
| Privátní IP adresa clusteru AKS                                                  | Public Preview   | NO        | Není k dispozici        |
| **Výpočetní služby** |   | | |
| Správa kvót napříč pracovními prostory                                         | GA               | ANO       | Není k dispozici        |
| **Data pro strojové učení** | | | |
| Vytvoření, zobrazení nebo úprava datových sad a úložišť dat ze sady SDK                  | GA               | ANO       | Není k dispozici        |
| Vytvoření, zobrazení nebo úprava datových sad a úložišť dat z uživatelského rozhraní                   | GA               | ANO       | Není k dispozici        |
| Zobrazení, úpravy nebo odstranění sledování posunu datové sady ze sady SDK                   | Public Preview   | ANO       | Není k dispozici        |
| Zobrazení, úpravy nebo odstranění sledování posunu datové sady z uživatelského rozhraní                    | Public Preview   | ANO       | Není k dispozici        |
| **Životní cyklus machine learningu** |    | | |
| Profilace modelu                                                            | GA               | ČÁSTEČNÉ   | Není k dispozici        |
| Rozšíření Azure DevOps pro Machine Learning & Azure ML CLI         | GA               | ANO       | Není k dispozici        |
| Modely s hardwarovou akcelerací založené na FPGA                                     | GA               | NO        | Není k dispozici        |
| Integrace Visual Studio Code                                             | Public Preview   | NO        | Není k dispozici        |
| Integrace Event Gridu                                                     | Public Preview   | ANO       | Není k dispozici        |
| Integrace Azure Stream Analytics s Azure Machine Learning               | Public Preview   | NO        | Není k dispozici        |
| **Označování** |    | | |
| Označování Portál pro správu projektu                                        | GA               | ANO       | Není k dispozici        |
| Portál popisků                                                            | GA               | ANO       | Není k dispozici        |
| Označování pomocí privátních zaměstnanců                                          | GA               | ANO       | Není k dispozici        |
| Popis s asistencí ML (klasifikace obrázků a detekce objektů)           | Public Preview   | ANO       | Není k dispozici        |
| **Zodpovědné strojové učení** |    | | |
| Vysvětlení v uživatelském rozhraní                                                       | Public Preview   | NO        | Není k dispozici        |
| Rozdílová ochrana osobních údajů WhiteNoise Toolkit                                    | OSS              | NO        | Není k dispozici        |
| vlastní značky v Azure Machine Learning k implementaci datových listů              | GA               | NO        | Není k dispozici        |
| Spravedlivá integrace AzureML                                               | Public Preview   | NO        | Není k dispozici        |
| Sada SDK pro vyhodnocení                                                      | GA               | ANO       | Není k dispozici        |
| **Školení** |    | | |
| Streamování protokolů experimentování                                              | GA               | ANO       | Není k dispozici        |
| Posílení učení                                                     | Public Preview   | NO        | Není k dispozici        |
| Uživatelské rozhraní experimentování                                                         | GA               | ANO       | Není k dispozici        |
| .NET Integration ML.NET 1,0                                                | GA               | ANO       | Není k dispozici        |
| **Odvození** |   | | |
| Batch Inferencing                                                          | GA               | ANO       | Není k dispozici        |
| Data Box Edge s FPGA                                                    | Public Preview   | NO        | Není k dispozici        |
| **Další** |    | | |
| Open Datasets                                                              | Public Preview   | ANO       | Není k dispozici        |
| Vlastní Kognitivní hledání                                                    | Public Preview   | ANO       | Není k dispozici        |
| Mnoho modelů                                                                | Public Preview   | NO        | Není k dispozici        |



### <a name="additional-azure-china-limitations"></a>Další omezení pro Azure Čína

* Azure Čína má omezené SKU virtuálních počítačů, zejména pro SKU GPU. Má pouze rodinu NCv3 (V100).
* Koncové body REST API se liší od globální služby Azure. Následující tabulku použijte k nalezení REST APIho koncového bodu pro oblasti Azure Čína:

    | Koncový bod REST                 | Globální Azure                                 | China-Government                           |
    |------------------|--------------------------------------------|--------------------------------------------|
    | Rovina správy | `https://management.azure.com/`              | `https://management.chinacloudapi.cn/`       |
    | Rovina dat       | `https://{location}.experiments.azureml.net` | `https://{location}.experiments.ml.azure.cn` |
    | Azure Active Directory              | `https://login.microsoftonline.com`          | `https://login.chinacloudapi.cn`             |

* Ukázkový Poznámkový blok nemusí fungovat, pokud potřebuje přístup k veřejným datům.
* Rozsahy IP adres: příkaz CLI použitý v pokynech pro [vynucené tunelování virtuální](how-to-secure-training-vnet.md#forced-tunneling) sítě nevrací rozsahy IP adres. Místo toho použijte [rozsahy IP adres Azure a značky služeb pro Azure Čína](https://www.microsoft.com//download/details.aspx?id=57062) .
* Služba Azure Machine Learning COMPUTE Instances Preview není podporovaná v pracovním prostoru, kde je aktuálně povolený privátní odkaz, ale CI se bude podporovat v dalším nasazení pro rozšíření služby pro všechny oblasti AML.

## <a name="next-steps"></a>Další kroky

Další informace o oblastech, ve kterých je Azure Machine Learning k dispozici, najdete v tématu [produkty podle oblasti](https://azure.microsoft.com/global-infrastructure/services/).
