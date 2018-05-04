---
title: Koncepční přehled Azure Machine Learning Model správy | Microsoft Docs
description: Tento dokument vysvětluje koncepty Model správy pro Azure Machine Learning.
services: machine-learning
author: nk773
ms.author: padou
manager: mwinkle
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 89f95753248f74c7f6cb9ca1f680a01b07dd43d1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="azure-machine-learning-model-management"></a>Správa modelů ve službě Azure Machine Learning

Azure Machine Learning Management modelu umožňuje spravovat a nasazovat pracovní postupy a modely strojové učení. 

Model správy poskytuje možnosti pro:
- Model správy verzí
- Sledování modely v produkčním prostředí
- Modely nasazení do produkčního prostředí prostřednictvím AzureML výpočetní prostředí s [Azure Container Service](https://azure.microsoft.com/services/container-service/) a [Kubernetes](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Vytvoření kontejnerů Docker s modely a místní testování
- Retraining automatizované modelu
- Zaznamenání modelu telemetrie prakticky využitelné informace. 

Azure Machine Learning modelu Management poskytuje registru verze modelu. Také poskytuje automatizované pracovní postupy pro balení a nasazení kontejnerů Machine Learning jako rozhraní REST API. Modely a jejich závislosti runtime jsou součástí kontejner Docker systémem Linux s předpovědi rozhraní API. 

Azure Machine Learning výpočetní prostředí pomůžou nastavit a spravovat škálovatelné clustery pro hostování modely. Výpočetní prostředí je založena na kontejneru služby Azure. Azure služby kontejneru poskytuje automatické úniku Machine Learning API jako koncové body REST API s následujícími vlastnostmi:

- Authentication
- Vyrovnávání zatížení
- Automatické škálování
- Šifrování

Azure Machine Learning modelu Management poskytuje tyto možnosti prostřednictvím rozhraní příkazového řádku, rozhraní API a portálu Azure. 

Azure Machine Learning model správy používá následující informace:

 - Model soubor nebo adresář se soubory modelu
 - Uživatele vytvořit soubor Python implementace model vyhodnocování – funkce
 - Conda závislostí souboru se seznamem závislostí modulu runtime
 - Volba prostředí runtime, a 
 - Soubor schématu pro parametry rozhraní API 

Tyto informace se používají při provádění následujících akcí:

- Registrace modelu
- Vytvoření manifestu, který se používá při vytváření kontejneru
- Vytváření kontejneru image Docker
- Kontejner nasazení do Azure Container Service
 
Následující obrázek ukazuje přehled o tom, jak jsou modely registrován a nasazen do clusteru. 

![](media/model-management-overview/modelmanagement.png)

## <a name="create-and-manage-models"></a>Vytváření a správě modelů 
Modely můžete zaregistrovat pomocí Azure Machine Learning Model správy pro sledování verze modelu v produkčním prostředí. Pro usnadnění reprodukovatelnosti a zásady správného řízení služba zaznamená všechny závislosti a související informace. Pro podrobnější přehled o výkonu můžete zachytit telemetrie modelu pomocí zadaného sady SDK. Model telemetrie je archivovat v úložišti zadaný uživatelem. Telemetrie modelu lze později pro analýzu výkonu v modelu, přeučení a získání statistiky pro vaši organizaci.

## <a name="create-and-manage-manifests"></a>Vytvoření a správa manifestů 
Modely vyžadovat další artefakty k nasazení do produkčního prostředí. Systém poskytuje možnost pro vytvoření manifestu, který zahrnuje modelu, závislostí, odvození skriptu (neboli vyhodnocování skript), ukázková data, schéma atd. Tento manifest funguje jako tajný recept na vytvoření bitové kopie kontejner Docker. Podniky můžete automaticky generovat manifest, vytvořit různé verze a spravovat jejich manifesty. 

## <a name="create-and-manage-docker-container-images"></a>Vytvoření a správa bitových kopií kontejner Docker 
Manifest z předchozího kroku můžete použít k vytvoření bitové kopie založené na Docker kontejneru v rámci příslušných prostředí. Obrázky kontejnerizované, na základě Docker poskytnout podnikům a flexibilně spouštět tyto bitové kopie na následujících výpočetních prostředích:

- [Kubernetes na základě Azure Container Service](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
- Služby kontejneru na místě
- Vývojové prostředí
- Zařízení IoT

Tyto Image kontejnerizované na základě Docker jsou samostatné s všechny potřebné závislosti, které jsou požadované pro generování předpovědi. 

## <a name="deploy-docker-container-images"></a>Nasazení bitových kopií kontejner Docker 
Pomocí služby Azure Machine Learning modelu Management můžete nasadit bitové kopie založené na Docker kontejneru jedním příkazem Azure Container Service spravuje ML výpočetní prostředí. Tato nasazení jsou vytvořeny pomocí serveru front-end, který poskytuje následující funkce:

- Předpovědi s nízkou latencí ve velkém měřítku
- Vyrovnávání zatížení
- Automatické škálování koncových bodů ML
- Autorizace pro klíč rozhraní API
- Dokumentu swagger rozhraní API

Můžete řídit rozsah nasazení a telemetrie prostřednictvím následující nastavení:

- Protokolování systému a telemetrie modelu pro každou úroveň webové služby. Pokud je povoleno, všechny protokoly stdout streamovaných k [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Model telemetrie je archivovat v úložišti, který zadáte. 
- Automatické škálování a souběžnost omezení. Tato nastavení automaticky zvýšit počet nasazených kontejnery podle zatížení v rámci stávajícího clusteru. Také řídit propustnosti a latence předpovědi konzistence.

## <a name="consumption"></a>Využití 
Azure Machine Learning modelu Management vytvoří REST API pro nasazené model společně s dokumentu swagger. Nasazené modely můžete využívat ve volání rozhraní REST API s rozhraním API klíče a model vstupy získat jsou předpovědi jako součást-obchodní aplikace. Ukázkový kód je k dispozici na webu GitHub pro jazyky Java, [Python](https://github.com/CortanaAnalyticsGallery-Int/digit-recognition-cnn-tf/blob/master/client.py)a C# pro volání rozhraní REST API. Azure Machine Learning Model správy CLI poskytuje snadný způsob, jak pracovat s Tato rozhraní API REST. Můžete využívat rozhraní API pomocí jednoduchého příkazu rozhraní příkazového řádku, aplikací s povolenými swagger, nebo pomocí curl. 

## <a name="retraining"></a>Retraining 
Azure Machine Learning modelu Management poskytuje rozhraní API, která vám pomůže přeučit modely. Rozhraní API můžete použít také k aktualizaci stávajícího nasazení aktualizované verze modelu. V rámci pracovního postupu vědecké účely dat se je znovu vytvořit model ve vašem prostředí experimenty. Pak zaregistrujte modelu s Model správy a existující nasazení aktualizací. Aktualizace se provádí pomocí jednoduchého příkazu UPDATE rozhraní příkazového řádku. Příkaz UPDATE aktualizuje existující nasazení beze změny adresy URL rozhraní API nebo klíč. Aplikací, které využívají model nadále fungovat bez jakékoli změny kódu a spusťte získání lepší předpovědi pomocí nového modelu.

Na následujícím obrázku je zachytit popisující tyto koncepty dokončení pracovního postupu:

![](media/model-management-overview/modelmanagementworkflow.png)

## <a name="frequently-asked-questions-faq"></a>Nejčastější dotazy 
- **Jaké datové typy jsou podporovány? Můžete předat pole NumPy přímo jako vstup pro webovou službu?**

   Pokud zadáte soubor schématu, která byla vytvořena pomocí generate_schema SDK, pak můžete předat NumPy nebo Pandas DF. Také můžete předat všechny vstupy serializovatelný JSON. Bitové kopie můžete předat jako binární řetězec s kódováním také.

- **Podporuje webovou službu více vstupů nebo analyzovat různé vstupy?**

   Ano, může trvat více vstupů zabalené v jedné žádosti JSON jako slovník. Každý vstup by odpovídat jedné slovník jedinečný klíč.

- **Je volání aktivuje požadavkem na web služby blokování volání nebo asynchronní volání?**

   Pokud služba byla vytvořena pomocí možnosti v reálném čase v rámci rozhraní CLI nebo API, je blokování synchronní volání. Očekává se jako rychlou v reálném čase. I když na straně klienta, můžete ji pomocí knihovny asynchronní HTTP, aby se zabránilo volat blokování vláken klienta.

- **Počet požadavků může webová služba současně zpracovávat?**

   To závisí na škálování clusteru a webové služby. Můžete škálovat služby do 100 x repliky a pak ji může zpracovat mnoho požadavků současně. Můžete taky nakonfigurovat maximální počtu souběžných požadavků na repliku, pokud chcete zvýšit propustnost služby.

- **Počet požadavků můžete webovou službu fronty?**

   Je konfigurovatelné. Ve výchozím nastavení je nastaven na hodnotu 10 ~ za jednu repliku, ale můžete můžete zvětšit nebo zmenšit jeho vašim požadavkům aplikace. Obvykle zvyšuje počet požadavků ve frontě zvyšuje propustnost služby ale dává nejhorší latence na vyšší percentily. Aby latence konzistentní, můžete chtít nastavit řazení do fronty na nízkou hodnotu (1-5) a zvýšit počet replik pro zpracování propustnost. Můžete také zapnout automatické škálování, chcete-li počet replik úpravě automaticky v závislosti na zatížení. 

- **Lze do počítače nebo clusteru použít pro více koncových bodů webové služby?**

   Jistě. 100 x služby nebo koncových bodů můžete spustit ve stejném clusteru. 

## <a name="next-steps"></a>Další postup
Začínáme se správou modelu, najdete v části [konfigurace Model správy](deployment-setup-configuration.md).
