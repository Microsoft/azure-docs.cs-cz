---
title: Parita mezi veřejnými a svrchovanými regiony
titleSuffix: Azure Machine Learning
description: Tento článek obsahuje seznam parity funkcí mezi veřejným cloudem a oblastmi Azure Government, Azure Německo a Azure Čína 21Vianet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: andzha
author: Anurzeuii
ms.date: 12/21/2020
ms.custom: references_regions
ms.openlocfilehash: 88240f9b46997d11f1e7c2d93fa880b004615a11
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97725016"
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

| Funkce | Stav veřejného cloudu  | US-Virginia | US-Arizona| 
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
| **Instance COMPUTE** |   |  | | 
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
| Rozdílová ochrana osobních údajů SmartNoise Toolkit                                    | OSS                  | NO                 | NO          |
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

| Scenario                                                    | US-Virginia | US-Arizona| Omezení  |
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|-------------|
| **Obecné nastavení zabezpečení** |   | | |
| Komunikace privátní sítě mezi službami                                     | NO | NO | Momentálně není k dispozici žádný privátní odkaz. | 
| Zakázání/řízení přístupu k Internetu (příchozích a odchozích) a konkrétní virtuální sítě | ČÁSTEČNÉ| ČÁSTEČNÉ   | ACR za virtuální síť není k dispozici ve Azure Government-dvojité kontrole na ACI. | 
| Umístění všech přidružených prostředků a služeb  | ANO | ANO |  |
| Šifrování v klidovém režimu a při přenosu.                                                 | ANO | ANO |  |
| Přístup k výpočetním prostředkům rootem a SSH.                                          | ANO | ANO |  |
| Udržování zabezpečení nasazených systémů (instancí, koncových bodů atd.), včetně Endpoint Protection, opravování a protokolování |  ČÁSTEČNÉ|  ČÁSTEČNÉ |ACI za virtuální sítí a soukromým koncovým bodem není momentálně k dispozici. |                                  
| Řízení (zakázání/omezení/omezení) používání integrace ACI/AKS                    | ČÁSTEČNÉ| ČÁSTEČNÉ |ACI za virtuální sítí a soukromým koncovým bodem není momentálně k dispozici.|
| Řízení přístupu na základě role v Azure (Azure RBAC) – vytváření vlastních rolí                           | ANO | ANO |  |
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

| Funkce                                       | Stav veřejného cloudu | CH – východ – 2 | CH-sever-3 |
|----------------------------------------------------------------------------|:------------------:|:--------------------:|:-------------:|
| **Automatizované strojové učení** |    | | |
| Vytváření a spouštění experimentů v poznámkových blocích                                    | GA               | ANO       | –        |
| Vytváření a spouštění experimentů ve webovém prostředí studia                        | Public Preview   | ANO       | –        |
| Špičkové možnosti prognózování v oboru                                  | GA               | ANO       | –        |
| Podpora hloubkového učení a dalších pokročilých učících                      | GA               | ANO       | –        |
| Podpora velkých objemů dat (až 100 GB)                                          | Public Preview   | ANO       | –        |
| Integrace Azure Databricks                                              | GA               | NO        | –        |
| Integrace SQL, CosmosDB a HDInsight                                   | GA               | ANO       | –        |
| **Machine Learning kanály** |    | | |
| Vytváření, spouštění a publikování kanálů pomocí Azure ML SDK                   | GA               | ANO       | –        |
| Vytváření koncových bodů kanálu pomocí sady Azure ML SDK                           | GA               | ANO       | –        |
| Vytváření, úpravy a odstraňování naplánovaných běhů kanálů pomocí sady Azure ML SDK | GA               | ANO       | –        |
| Zobrazení podrobností o spuštění kanálu v studiu                                        | GA               | ANO       | –        |
| Vytváření, spouštění, vizualizace a publikování kanálů v Azure ML designeru          | GA  | ANO       | –        |
| Azure Databricks integrace s kanálem ML                             | GA               | NO        | –        |
| Vytváření koncových bodů kanálu v Návrháři Azure ML                             | GA   | ANO       | –        |
| **Integrované poznámkové bloky** |   | | |
| Notebook a sdílení souborů v pracovním prostoru                                        | GA               | ANO       | –        |
| Podpora R a Pythonu                                                       | GA               | ANO       | –        |
| Podpora virtuální sítě                                                    | Public Preview   | NO        | –        |
| **Instance COMPUTE** |    | | |
| Spravované výpočetní instance pro integrované poznámkové bloky                         | GA               | NO        | –        |
| Integrace Jupyter, JupyterLab                                            | GA               | ANO       | –        |
| Podpora Virtual Network (VNet)                                             | Public Preview   | ANO       | –        |
| **Podpora SDK** |    | | |
| Podpora sady R SDK                                                              | Public Preview   | ANO       | –        |
| Podpora sady Python SDK                                                         | GA               | ANO       | –        |
| **Zabezpečení** |   | | |
| Podpora Virtual Network (VNet) pro školení                                | GA               | ANO       | –        |
| Podpora Virtual Network (VNet) pro odvození                               | GA               | ANO       | –        |
| Bodování ověřování koncového bodu                                            | Public Preview   | ANO       | –        |
| Privátní odkaz na pracoviště                                                     | Public Preview   | NO        | –        |
| ACI za virtuální sítí                                                            | Public Preview   | NO        | –        |
| ACR za virtuální sítí                                                            | Public Preview   | NO        | –        |
| Privátní IP adresa clusteru AKS                                                  | Public Preview   | NO        | –        |
| **Výpočetní služby** |   | | |
| Správa kvót napříč pracovními prostory                                         | GA               | ANO       | –        |
| **Data pro strojové učení** | | | |
| Vytvoření, zobrazení nebo úprava datových sad a úložišť dat ze sady SDK                  | GA               | ANO       | –        |
| Vytvoření, zobrazení nebo úprava datových sad a úložišť dat z uživatelského rozhraní                   | GA               | ANO       | –        |
| Zobrazení, úpravy nebo odstranění sledování posunu datové sady ze sady SDK                   | Public Preview   | ANO       | –        |
| Zobrazení, úpravy nebo odstranění sledování posunu datové sady z uživatelského rozhraní                    | Public Preview   | ANO       | –        |
| **Životní cyklus machine learningu** |    | | |
| Profilace modelu                                                            | GA               | ČÁSTEČNÉ   | –        |
| Rozšíření Azure DevOps pro Machine Learning & Azure ML CLI         | GA               | ANO       | –        |
| Modely s hardwarovou akcelerací založené na FPGA                                     | GA               | NO        | –        |
| Integrace Visual Studio Code                                             | Public Preview   | NO        | –        |
| Integrace Event Gridu                                                     | Public Preview   | ANO       | –        |
| Integrace Azure Stream Analytics s Azure Machine Learning               | Public Preview   | NO        | –        |
| **Označování** |    | | |
| Označování Portál pro správu projektu                                        | GA               | ANO       | –        |
| Portál popisků                                                            | GA               | ANO       | –        |
| Označování pomocí privátních zaměstnanců                                          | GA               | ANO       | –        |
| Popis s asistencí ML (klasifikace obrázků a detekce objektů)           | Public Preview   | ANO       | –        |
| **Zodpovědné strojové učení** |    | | |
| Vysvětlení v uživatelském rozhraní                                                       | Public Preview   | NO        | –        |
| Rozdílová ochrana osobních údajů SmartNoise Toolkit                                    | OSS              | NO        | –        |
| vlastní značky v Azure Machine Learning k implementaci datových listů              | GA               | NO        | –        |
| Spravedlivá integrace AzureML                                               | Public Preview   | NO        | –        |
| Sada SDK pro vyhodnocení                                                      | GA               | ANO       | –        |
| **Školení** |    | | |
| Streamování protokolů experimentování                                              | GA               | ANO       | –        |
| Posílení učení                                                     | Public Preview   | NO        | –        |
| Uživatelské rozhraní experimentování                                                         | GA               | ANO       | –        |
| .NET Integration ML.NET 1,0                                                | GA               | ANO       | –        |
| **Odvození** |   | | |
| Batch Inferencing                                                          | GA               | ANO       | –        |
| Data Box Edge s FPGA                                                    | Public Preview   | NO        | –        |
| **Další** |    | | |
| Open Datasets                                                              | Public Preview   | ANO       | –        |
| Vlastní Kognitivní hledání                                                    | Public Preview   | ANO       | –        |
| Mnoho modelů                                                                | Public Preview   | NO        | –        |



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
