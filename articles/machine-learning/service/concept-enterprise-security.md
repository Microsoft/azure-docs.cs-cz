---
title: Podnikové zabezpečení
titleSuffix: Azure Machine Learning service
description: 'Bezpečně používat službu Azure Machine Learning: ověřování, autorizace, zabezpečení sítě, šifrování dat a monitorování.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/10/2019
ms.openlocfilehash: b950e7d38235d089c6236c76136d8ec2fc7a1f74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60821335"
---
# <a name="enterprise-security-for-azure-machine-learning-service"></a>Zabezpečení podnikové třídy pro službu Azure Machine Learning

V tomto článku se dozvíte o funkcích zabezpečení, které jsou dostupné s Azure Machine learning service.

Pokud používáte cloudovou službu, je osvědčeným postupem je omezit přístup jenom na uživatele, kteří ji potřebují. Tím se spustí Pochopením model ověřování a autorizace služba používá. Můžete také chtít omezit přístup k síti, nebo bezpečně připojit k prostředkům ve vaší místní síti s těmi v cloudu. Šifrování dat je také důležité, v klidu i při přesunu dat mezi službami. Nakonec musíte monitorovat službu a vytvoření protokolu auditu všech aktivit.

## <a name="authentication"></a>Authentication
Multi-Factor authentication je podporováno, pokud je Azure Active Directory (Azure AD) nakonfigurovaný pro stejné.
* Klient se přihlásí do služby Azure AD a získá token Azure Resource Manageru.  Uživatelé a instanční objekty jsou plně podporované.
* Klient prezentuje token na Azure Resource Manager a všechny služby Azure Machine Learning
* Služba Azure Machine Learning poskytuje token Azure Machine Learning compute uživatele. Například, Machine Learning Compute. Tato Azure Machine Learning, které uživatel používá token compute, zavolá zpět do služby Azure Machine Learning (omezuje obor do pracovního prostoru), po spuštění je dokončena.

![Snímek obrazovky ukazující, jak funguje ověřování ve službě Azure Machine Learning](./media/enterprise-readiness/authentication.png)

### <a name="authentication-keys-for-web-service-deployment"></a>Ověření klíče pro nasazení webové služby

Když povolíte ověřování pro nasazení, automaticky se vytvoří ověřovací klíče.

* Ve výchozím nastavení je povoleno ověřování při nasazování do služby Azure Kubernetes Service.
* Ve výchozím nastavení je zakázáno ověřování při nasazování do služby Azure Container Instances.

Pokud chcete nastavit ověřování, použijte `auth_enabled` parametr při vytváření nebo aktualizaci nasazení.

Pokud je ověřování zapnuté, můžete použít `get_keys` metody k získání primární a sekundární ověřovací klíč:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Pokud je potřeba znovu vygenerovat klíč, použijte [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py)


## <a name="authorization"></a>Autorizace

Můžete vytvořit víc pracovních prostorů a každý pracovní prostor může být sdílen více lidí. Když sdílíte s pracovním prostorem, můžete řídit přístup k němu tak, že uživatelům přiřadíte následující role:
* Vlastník
* Přispěvatel
* Čtenář
    
V následující tabulce jsou uvedeny některé hlavní operace služby Azure Machine Learning a rolí, které můžete provést:

| Služba Azure Machine Learning operace | Vlastník | Přispěvatel | Čtenář |
| ---- |:----:|:----:|:----:|
| Vytvořit pracovní prostor | ✓ | ✓ | |
| Sdílení pracovního prostoru | ✓ | |  |
| Vytvořte výpočetní prostředky | ✓ | ✓ | |
| Připojit výpočetní prostředky | ✓ | ✓ | |
| Připojení úložišť | ✓ | ✓ | |
| Spusťte Experiment | ✓ | ✓ | |
| Zobrazení spuštění a metrik | ✓ | ✓ | ✓ |
| Registrace modelu | ✓ | ✓ | |
| Vytvořit image | ✓ | ✓ | |
| Nasazení webové služby | ✓ | ✓ | |
| Zobrazení modelů nebo imagí | ✓ | ✓ | ✓ |
| Volání webové služby | ✓ | ✓ | ✓ |

Pokud předdefinované role nejsou dostatečná k vašim potřebám, můžete také vytvořit vlastní role. Všimněte si, že pouze vlastní role, které jsme podporu pro operace v pracovním prostoru a Machine Learning Compute. Může mít vlastní role číst, zapsat nebo odstranit oprávnění v pracovním prostoru a výpočetních prostředků v daném pracovním prostoru. Roli můžete zpřístupnit na úrovni konkrétní pracovní prostor, úrovni skupiny prostředků konkrétní nebo úroveň konkrétní předplatné. Další informace najdete v tématu [správu uživatelů a rolí v pracovním prostoru Azure Machine Learning](how-to-assign-roles.md)

### <a name="securing-compute-and-data"></a>Zabezpečení výpočetních a dat
Vlastníci a přispěvatelé můžete použít všechny výpočetní prostředky cíle a úložišti dat, které jsou připojené k pracovnímu prostoru.  
Každý pracovní prostor obsahuje také související systém přiřadil spravovaná identita (se stejným názvem jako pracovní prostor) s následujícími oprávněními na připojené zdroje použité v pracovním prostoru:

Další informace o spravovaných identit najdete v tématu [spravovaných identit pro prostředky Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

| Prostředek | Oprávnění |
| ----- | ----- |
| Pracovní prostor | Přispěvatel | 
| Účet úložiště | Přispěvatel dat v objektech blob služby Storage | 
| Key Vault | Přístup ke všem certifikátům klíče, tajné kódy, | 
| Azure Container Registry | Přispěvatel | 
| Skupiny prostředků, která obsahuje tento pracovní prostor | Přispěvatel | 
| Skupina prostředků obsahující trezor klíčů (Pokud je jiný než ten, který obsahuje pracovní prostor) | Přispěvatel | 

Doporučuje se, že správci neodebírejte přístup spravovaná identita na odkazech uvedených výše. Přístup je možné obnovit pomocí operace resynchronizovat klíče.

Služba Azure Machine Learning se vytvoří další aplikace (název začíná aml-) s přístupem na úrovni Přispěvatel ve vašem předplatném pro každou oblast pracovního prostoru. Pro příklad. Pokud máte pracovní prostor v oblasti východní USA a jiným pracovním prostorem v oblasti Severní Evropa ve stejném předplatném se zobrazí 2 těchto aplikací. To je potřeba, aby Azure Machine Learning service může pomoct spravovat výpočetní prostředky.


## <a name="network-security"></a>Zabezpečení sítě

Služba Azure Machine Learning spoléhá na ostatní služby Azure pro výpočetní prostředky. Výpočetní prostředky (cílových výpočetních prostředí) se používají k trénování a nasazování modelů. Tyto výpočetní cíle lze vytvořit ve virtuální síti. Například můžete použít virtuální počítač Microsoft datové vědy pro trénování modelu a poté model nasaďte do Azure Kubernetes Service (AKS).  

Další informace najdete v tématu [spuštění experimentů a odvozování ve virtuální síti](how-to-enable-virtual-network.md).

## <a name="data-encryption"></a>Šifrování dat

### <a name="encryption-at-rest"></a>Šifrování v klidovém stavu
#### <a name="azure-blob-storage"></a>Azure Blob Storage
Služba Azure Machine Learning uchovává snímky, výstupy a protokoly v účtu úložiště objektů Blob v Azure, kudy do pracovního prostoru služby Azure Machine Learning, která se nachází v předplatném uživatele. Všechna data uložená ve službě Azure Blob Storage zašifrovaná přinejmenším pomocí Microsoft-Managed klíčů.

Další informace o tom, jak uvést vaše vlastní klíče pro data uložená ve službě Azure Blob Storage najdete v tématu [šifrování služby Storage pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault](https://docs.microsoft.com/azure/storage/common/storage-service-encryption-customer-managed-keys).

Cvičná data jsou obvykle také uložená v úložišti objektů Blob v Azure tak, aby byly přístupné pro výpočetní školení. Toto úložiště není spravované službou Azure Machine Learning, ale připojené k výpočtu jako vzdálený systém souborů.

#### <a name="cosmos-db"></a>Cosmos DB
Služba Azure Machine Learning ukládá metriky a metadata pro Cosmos DB, který se nachází v předplatném Microsoft spravovaném službou Azure Machine Learning. Všechna data uložená ve službě Cosmos DB zašifrovaná přinejmenším pomocí Microsoft Managed Keys.

#### <a name="azure-container-registry-acr"></a>Azure Container Registry (ACR)
Všechny Image kontejneru v registru (ACR) jsou v klidovém stavu zašifrovaná. Azure automaticky šifruje image před uložením a dešifruje ji v běhu při služby Azure Machine Learning, stáhne bitovou kopii.

#### <a name="machine-learning-compute"></a>Výpočetní prostředky služby Machine Learning
Disk s operačním systémem pro každý výpočetní uzel je uložený ve službě Azure Storage se šifrují pomocí klíčů spravovaných Microsoft v účtech úložiště služby Azure Machine Learning. Tato výpočetní je dočasný a clustery jsou obvykle kapacitu vertikálně snížit při nic se nespustilo. ve frontě. Základní virtuální počítač se zruší a odstranit disk s operačním systémem. Azure disk encryption se nepodporuje pro disk s operačním systémem.
Každý virtuální počítač má také místní dočasný disk pro operační systém operace. Tento disk Volitelně lze také na fázi trénovací data. Tento disk není šifrovaný. Další informace o tom, jak funguje šifrování v klidovém stavu v Azure najdete v tématu [Azure Data šifrování neaktivních](https://docs.microsoft.com/azure/security/azure-security-encryption-atrest). 

### <a name="encryption-in-transit"></a>Šifrování během přenosu
Obě interní komunikaci mezi různými mikroslužby Azure Machine Learning a externí komunikace volání bodovací koncový bod jsou podporovány při použití protokolu SSL. Veškerý přístup služby Azure Storage je také přes zabezpečený kanál. Další informace najdete v tématu [webových služeb zabezpečení Azure Machine Learning pomocí protokolu SSL](https://docs.microsoft.com/azure/machine-learning/service/how-to-secure-web-service).

### <a name="using-azure-key-vault"></a>Použití Azure Key Vault
Instance služby Key Vault přidružený k pracovnímu prostoru se používá služba Azure Machine Learning k ukládání přihlašovacích údajů různých druhů:
* Připojovací řetězec pro účet úložiště přidružený
* Hesla k úložišti Azure Container instances
* Připojovací řetězce k datům úložiště. 

Hesla SSH byla zakázaná a klíče k výpočtu cíle, jako je HDInsight HDI a virtuálních počítačů jsou uložené v samostatné služby Key Vault, který je přidružený k předplatnému Microsoft. Služba Azure Machine Learning ukládá všechna hesla nebo klíče uživatelem zadané místo generuje, autorizuje a ukládá své vlastní klíče SSH pro připojení k virtuálnímu počítači/HDInsight spouštět experimenty. Každý pracovní prostor má přidružený systém přiřadil spravovat identitu (se stejným názvem jako pracovní prostor), který má přístup ke všem klíče, tajné kódy a certifikáty ve službě Key Vault.

 
## <a name="monitoring"></a>Monitorování

Uživatelé mohou zobrazit protokol aktivit v pracovním prostoru a prohlédněte si různé operace prováděné v pracovním prostoru přitom základní informace, jako je název operace, událost inicioval(a), časové razítko atd.

Následující snímek obrazovky ukazuje protokolu aktivit pro pracovní prostor:

![Snímek obrazovky znázorňující protokolu aktivit v pracovním prostoru](./media/enterprise-readiness/workspace-activity-log.png)


Ve službě AppInsights, který se vytvoří v předplatném uživatele při vytváření pracovního prostoru jsou uloženy podrobnosti o vyhodnocení požadavku. To zahrnuje pole, jako jsou HTTPMethod, UserAgent, ComputeType, RequestUrl, StatusCode, RequestId, doba trvání atd.


## <a name="data-flow-diagram"></a>Diagram toku dat

### <a name="create-workspace"></a>Vytvořit pracovní prostor
Následující diagram znázorňuje pracovní postup vytvoření pracovního prostoru.
Uživatel přihlásí do Azure AD z kteréhokoli z podporovaných klientů služby Azure Machine Learning (rozhraní příkazového řádku, Python SDK, Azure portal) a požádá o příslušného tokenu Azure Resource Manageru.  Uživatel pak zavolá Azure Resource Manageru vytvořit pracovní prostor.  Azure Resource Manageru kontakty Azure Machine Learning služby poskytovatele prostředků se zřídit pracovní prostor.  Další prostředky se vytvoří v předplatném zákazníka během vytváření pracovního prostoru:
* Trezor klíčů (k ukládání tajných klíčů)
* Účet služby Azure Storage (včetně objektů Blob a sdílení souborů)
* Služba Azure Container Registry (k ukládání imagí dockeru pro odvozování a experimentování ve službě)
* Application Insights (pro ukládání telemetrických dat)

Další výpočetní prostředí, připojený k pracovnímu prostoru (služby Azure Kubernetes Service, virtuálních počítačů atd.) můžete také zřídí zákazníci podle potřeby. 

![Snímek obrazovky znázorňující vytvoření pracovního prostoru pracovního postupu](./media/enterprise-readiness/create-workspace.png)

### <a name="save-source-code-training-scripts"></a>Uložit zdrojový kód (trénovací skripty)
Následující diagram znázorňuje pracovní postup snímku kódu.
Přidružené Azure Machine Learning pracovního prostoru služby jsou adresáře (experimenty), který obsahuje zdrojový kód (trénovací skripty).  Tyto jsou uložené na místním počítači zákazníka i v cloudu (v Azure Blob Storage v rámci předplatného zákazníka). Tyto snímky kódu slouží k provádění nebo kontroly pro historické auditování.

![Snímek obrazovky znázorňující vytvoření pracovního prostoru pracovního postupu](./media/enterprise-readiness/code-snapshot.png)

### <a name="training"></a>Školení
Následující diagram znázorňuje školení pracovního postupu.
* Služba Azure Machine Learning se volá s ID snímku pro kód snímku uloženy výše
* Azure Machine Learning, které vytvoří službu spustit ID (volitelné) a token služby Azure Machine Learning, který později používá cílových výpočetních prostředí jako Machine Learning výpočetní/VM ke komunikaci zpět do služby Azure Machine Learning
* Můžete použít buď spravované výpočetní prostředí (např.) Machine Learning Compute) nebo nespravované výpočetní výkon (např.) Virtuální počítač) ke spuštění trénovací úlohy. Tok dat je vysvětleno pro oba scénáře níže:
* (Virtuální počítač/HDInsight/místní funkce – přistupovat pomocí přihlašovací údaje pro SSH ve službě Key Vault v předplatném Microsoft) Služba Azure Machine Learning běží kód pro správu na cílové výpočetní prostředí, které:
    1.  Připraví prostředí (Poznámka: Docker je možnost i pro virtuální počítač/místní funkce. Viz kroky pro Machine Learning Compute pochopit, jak spuštění experimentu na funguje kontejneru dockeru níže)
    2.  Soubory ke stažení kódu
    3.  Nastaví proměnné prostředí nebo konfigurace
    4.  Spustí skript uživatelem (snímku kódu uvedeného výše)
* (Machine Learning Compute – přistupovat pomocí identity spravované v pracovním prostoru) Všimněte si, že od Machine Learning Compute je spravovaná výpočetní tedy spravovaného společností Microsoft v důsledku spuštění v rámci předplatného Microsoft.
    1.  Vzdálené vytváření Docker spustí se v případě potřeby
    2.  Zapíše kód pro správu pro uživatele sdílené složky Azure
    3.  Spuštění kontejneru s počátečním příkaz tedy kód pro správu v předchozím kroku


#### <a name="querying-runs--metrics"></a>Dotazování na spuštění a metrik
Tento krok se zobrazí ve službě flow, kde školení compute zápisy *spustit metriky* zpět do služby Azure Machine Learning z kde jsou uloženy v databázi Cosmos DB. Klienti mohou volat službu Azure Machine Learning, který bude postupně načítat metriky služby Cosmos DB a vrátit zpět do klienta.

![Snímek obrazovky znázorňující vytvoření pracovního prostoru pracovního postupu](./media/enterprise-readiness/training-and-metrics.png)

### <a name="creating-web-services"></a>Vytváření webových služeb
Následující diagram znázorňuje odvozování pracovního postupu, ve kterém je model nasadit jako webovou službu.
Viz podrobnosti níže:
* Uživatel zaregistruje model s použitím klienta, jako jsou sady SDK Azure ML
* Uživatel vytvoří image pomocí modelu, skóre souboru a další závislosti modelu
* Image Dockeru se vytvoří a uloží do služby ACR
* Webová služba je nasazený do cílového výpočetního prostředí (ACI a AKS) pomocí bitové kopie vytvořené výše
* Hodnoticí podrobnosti o žádosti jsou uloženy ve službě AppInsights, což je v předplatném uživatele
* Telemetrie se také vloží do předplatného Microsoft Azure

![Snímek obrazovky znázorňující vytvoření pracovního prostoru pracovního postupu](./media/enterprise-readiness/inferencing.png)

## <a name="next-steps"></a>Další postup

* [Zabezpečení webových služeb Azure Machine Learning s protokolem SSL](how-to-secure-web-service.md)
* [Používání modelu ML nasadit jako webovou službu](how-to-consume-web-service.md)
* [Jak spustit predikcí služby batch](how-to-run-batch-predictions.md)
* [Monitorování vašich modelů Azure Machine Learning s využitím Application Insights](how-to-enable-app-insights.md)
* [Shromažďování dat modelů v produkčním prostředí](how-to-enable-data-collection.md)
* [Sady SDK služby Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)
* [Použití služby Azure Machine Learning s Azure Virtual Network](how-to-enable-virtual-network.md)
* [Osvědčené postupy pro vytváření doporučovacích systémů](https://github.com/Microsoft/Recommenders)
* [Sestavení v reálném čase doporučení rozhraní API v Azure](https://docs.microsoft.com/azure/architecture/reference-architectures/ai/real-time-recommendation)
