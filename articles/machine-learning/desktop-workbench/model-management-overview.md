---
title: Koncepční přehled správy modelů Azure Machine Learning | Dokumentace Microsoftu
description: Tento dokument popisuje koncepty správy modelů pro Azure Machine Learning.
services: machine-learning
author: hjerezmsft
ms.author: hjerez
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ROBOTS: NOINDEX
ms.openlocfilehash: d3f7e206e7f4aa61a8ec1272ff2670d81bb7a33e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974676"
---
# <a name="azure-machine-learning-model-management"></a>Správa modelů ve službě Azure Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]


Azure Machine Learning Správa modelů umožňuje spravovat a nasazovat pracovní postupy a modely strojového učení. 

Správa modelů ve službě poskytuje možnosti pro:
- Model správy verzí
- Sledování modelů v produkčním prostředí
- Nasazení modelů do produkčního prostředí pomocí Azure ml prostředí Compute s [Azure Container Service](https://azure.microsoft.com/services/container-service/) a [Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Vytvoření kontejnerů Dockeru s modely a místní testování
- Přetrénování modelů automatizované
- Sběr telemetrických dat model pro užitečné přehledy. 

Správa modelů Azure Machine Learning poskytuje registru verze modelu. Také poskytuje automatizované pracovní postupy pro balení a nasazování kontejnerů Machine Learning jako rozhraní REST API. Modely a jejich závislosti modulu runtime jsou zabaleny v kontejneru Dockeru založených na Linuxu pomocí prediktivního rozhraní API. 

Prostředí Azure Machine Learning Compute pomohou nastavit a spravovat škálovatelné clustery pro hostování modely. Výpočetní prostředí je založena na služby Azure Container Service. Azure Container Service poskytuje automatické zpřístupňování rozhraní API pro Machine Learning jako koncových bodů rozhraní REST API s následujícími funkcemi:

- Authentication
- Vyrovnávání zatížení
- Automatické horizontální navýšení kapacity
- Šifrování

Správa modelů Azure Machine Learning nabízí tyto možnosti prostřednictvím rozhraní příkazového řádku, rozhraní API a webu Azure portal. 

Správa modelů Azure Machine Learning používá následující informace:

 - Model souboru nebo adresáři se soubory modelu
 - Uživatel vytvořil soubor Pythonu implementace modelu vyhodnocování – funkce
 - Souboru závislostí systému Conda se seznamem závislosti modulu runtime
 - Výběr prostředí modulu runtime, a 
 - Soubor schématu pro parametry rozhraní API 

Tyto informace se používají při provádění následujících akcí:

- Registrace modelu
- Vytvoření manifestu, který se používá při vytváření kontejneru
- Vytvoření image kontejneru Dockeru
- Nasazení kontejneru do služby Azure Container Service
 
Následující obrázek znázorňuje přehled o tom, jak jsou modely registrován a nasazen do clusteru. 

![](media/model-management-overview/modelmanagement.png)

## <a name="create-and-manage-models"></a>Vytváření a správě modelů 
Modely můžete zaregistrovat pomocí správy modelů Azure Machine Learning pro sledování verzí modelů v produkčním prostředí. Z důvodu snadnějšího reprodukovatelnost a zásad správného řízení služba zachycuje všechny závislosti a související informace. Pro hlubší přehled o výkonu můžete zachytit telemetrických dat modelu pomocí zadaného sady SDK. Model telemetrie se archivuje do uživatelem zadaného úložiště. Telemetrická data model můžete použít později pro analýzu výkonu v modelu, přetrénování a získávání informací pro vaši firmu.

## <a name="create-and-manage-manifests"></a>Vytváření a správě manifestů 
Modely vyžadovat další artefakty pro nasazení do produkčního prostředí. Systém umožňuje vytvoření manifestu, která zahrnuje model, závislosti, skript odvození (neboli hodnoticí skript), ukázková data, schéma atd. Tento manifest funguje jako recept na vytvoření image kontejneru Dockeru. Podniky můžete automaticky generovat manifest, vytvořit různé verze a spravovat jejich manifesty. 

## <a name="create-and-manage-docker-container-images"></a>Vytvoření a správa imagí kontejnerů Dockeru 
V manifestu z předchozího kroku můžete použít k sestavení Image kontejneru dockeru v rámci příslušné prostředí. Image kontejnerizované, založené na Dockeru poskytují podnikům a flexibilně spouštět v následujících výpočetních prostředích tyto Image:

- [Kubernetes na základě služby Azure Container Service](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Místní kontejner služby
- Vývojová prostředí
- Zařízení IoT

Všechny potřebné závislosti vyžadované pro generování předpovědi jsou samostatné tyto kontejnerové Image dockeru. 

## <a name="deploy-docker-container-images"></a>Nasazení Image kontejneru Dockeru 
Pomocí správy modelů Azure Machine Learning můžete nasadit Image kontejnerů dockeru pomocí jediného příkazu spravuje ML výpočetní prostředí Azure Container Service. Tato nasazení jsou vytvořeny pomocí front-endového serveru, který poskytuje následující funkce:

- Predikce s nízkou latencí ve velkém měřítku
- Vyrovnávání zatížení
- Automatické škálování koncových bodů ML
- Autorizace pro klíč k rozhraní API
- Dokument swagger rozhraní API

Můžete řídit škálování nasazení a telemetrická data prostřednictvím následující nastavení:

- Protokolování systému a modelu telemetrie pro každou úroveň webové služby. Pokud je povoleno, všechny protokoly stdout se streamují do [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Model telemetrie je archivovat v úložišti, které poskytujete. 
- Limity pro automatické škálování a souběžnosti. Tato nastavení automaticky zvýšit počet nasazených kontejnerů na základě zatížení v rámci stávajícího clusteru. Také určují propustnosti a konzistence, latence předpovědi.

## <a name="consumption"></a>Využití 
Správa modelů Azure Machine Learning vytvoří rozhraní REST API pro nasazený model spolu s dokument swagger. Můžete využívat nasazených modelů pomocí volání rozhraní REST API s rozhraním API key a model vstupy pro získání předpovědi jako součást-obchodních aplikací. Ukázkový kód je k dispozici na Githubu pro jazyky Java, [Python](https://github.com/CortanaAnalyticsGallery-Int/digit-recognition-cnn-tf/blob/master/client.py)a C# pro volání rozhraní REST API. Příkazového řádku Azure Machine Learning Model správy poskytuje snadný způsob, jak pracovat s těmito rozhraními REST API. Můžete využívat rozhraní API pomocí jediného příkazu rozhraní příkazového řádku aplikace podporou swaggeru nebo pomocí příkazu curl. 

## <a name="retraining"></a>Přeškolení 
Správa modelů Azure Machine Learning poskytuje rozhraní API, která vám umožní programovém přeučení modelů. Rozhraní API můžete použít také k aktualizaci stávajících nasazení aktualizované verze modelu. Jako součást pracovní postup datových věd můžete znovu vytvořit model ve vašem prostředí služby experimentování ve službě. Pak zaregistrujte model u správy modelů a existující nasazení aktualizací. Aktualizace se provádí pomocí jediného příkazu aktualizace rozhraní příkazového řádku. Příkaz UPDATE aktualizuje existující nasazení beze změny adresy URL rozhraní API nebo klíče. Aplikace využívající model i nadále fungovat bez jakékoli změny kódu a získávat na zajištění lepších predikcí pomocí nového modelu.

Kompletní pracovní postup, který popisuje tyto koncepty je zachycena na následujícím obrázku:

![](media/model-management-overview/modelmanagementworkflow.png)

## <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy 
- **Jaké datové typy jsou podporovány? Můžete předat pole NumPy přímo jako vstup pro webovou službu?**

   Pokud zadáte soubor schématu, který byl vytvořen pomocí generate_schema SDK, pak můžete předat NumPy a/nebo Pandas DF. Můžete také předat žádné vstupy serializovat JSON. Obrázek lze předat jako binární také kódovaný řetězec.

- **Webová služba podporu více vstupů nebo parsovat různé vstupy?**

   Ano, může trvat více vstupů zabalené v jedné žádosti JSON jako slovník. Každá vstupní by odpovídat do jednoho slovníku jedinečný klíč.

- **Je aktivována požadavkem na webu volání služby blokovacího hovoru nebo asynchronní volání?**

   Pokud služba byla vytvořena pomocí možnosti v reálném čase v rámci rozhraní příkazového řádku nebo rozhraní API, je blokování synchronní volání. Očekává se bude v reálném čase rychlé. I když na straně klienta při volání pomocí knihovny asynchronní HTTP, aby blokování vlákna klienta.

- **Kolik požadavků může webová služba současně zpracovávat?**

   To závisí na škálování clusteru a webové služby. Můžete škálovat svoji službu pro 100 x repliky a pak je můžete zpracovat velký počet požadavků současně. Můžete taky nakonfigurovat maximální souběžný požadavek na repliku, pokud chcete zvýšit propustnost služby.

- **Kolik požadavků může webová služba fronty?**

   To je možné konfigurovat. Ve výchozím nastavení je nastaven na hodnotu 10 přibližně za jednu repliku, ale je můžete zvětšit nebo zmenšit jeho požadavkům vaší aplikace. Obvykle zvyšuje počet požadavků zařazených do fronty zvyšuje propustnost služby, ale provede ještě hůř latenci v vyšší percentil. Pokud chcete zachovat latence konzistentní vzhledem k aplikacím, může chtít nastavit řazení do fronty na nízkou hodnotu (1-5) a zvýšit počet replik pro zpracování propustnost. Můžete také zapnout automatické škálování, aby se počet replik nastavení automaticky v závislosti na zatížení. 

- **Na počítači nebo cluster slouží pro několik koncových bodů webové služby?**

   Jistě. 100 x služby/koncových bodů můžete spustit ve stejném clusteru. 

## <a name="next-steps"></a>Další postup
Začínáme se správou Model, najdete v části [konfigurace správy modelů](deployment-setup-configuration.md).
