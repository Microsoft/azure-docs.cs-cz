---
title: Koncepční přehled funkcí Azure Machine Learning ve verzi preview | Dokumentace Microsoftu
description: Koncepční přehled funkce ve verzi preview služby Azure Machine Learning, jako je například předplatná, účty, pracovními prostory, projekty, atd.
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: f63b9c077e64b642adfd8c7eed5026563eb6319a
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "35642770"
---
# <a name="azure-machine-learning---concepts"></a>Azure Machine Learning – koncepce

Tento článek definuje a popisuje koncepty, které potřebujete vědět k používání Azure Machine Learning. 

![Hierarchie koncepty](media/overview-general-concepts/hierarchy.png)

- **Předplatné:** předplatné Azure uděluje přístup k prostředkům v Azure. Protože Azure Machine Learning se úzce integruje se výpočetní prostředky, úložiště a mnoho dalších prostředků Azure a služby, aplikace Workbench vyžaduje, že každý uživatel má přístup k platné předplatné Azure. Uživatelé musí také mít dostatečná oprávnění v rámci tohoto předplatného vytvářet prostředky.


- **Účet experimentování:** účet experimentování ve službě je prostředek Azure vyžaduje Azure ML a fakturační vozidlo. Obsahuje vaše pracovní prostory, které pak obsahovat projekty. Můžete přidat více uživatelů, označuje jako _licence_, k účtu experimentování. Chcete-li používat aplikaci Azure ML Workbench Pokud chcete spouštět experimenty musí mít přístup k účtu experimentování. 


- **Účet správy modelů** účet správy modelů je také nutné ke správě modely Azure ml prostředku Azure. Můžete ho zaregistrovat manifestů a modely, sestavovat kontejnerizované webové služby a nasazovat je místně nebo v cloudu. Je také další fakturační nástroj Azure ml.


- **Pracovní prostor:** pracovní prostor je primární součásti pro sdílení a spolupráce v Azure ML. Projekty jsou seskupena do pracovního prostoru. Pracovní prostor je pak sdílet s více uživateli, které byly přidány do experimentálního účtu.


- **Projekt:** ve službě Azure Machine Learning je projekt logickým kontejnerem pro veškerou práci prováděnou za účelem vyřešení problému. Mapuje se na jedinou složku souborů na místním disku, do které můžete přidat jakékoli soubory nebo podsložky. Projekt může být volitelně přidružena úložiště Git pro správu zdrojového kódu a spolupráci.  

- **Experiment:** experimentu v Azure ML je minimálně jeden zdrojový kód soubor, které mohou být provedeny z jednoho vstupního bodu. Může obsahovat úlohy, jako je příjem dat, vytváření funkcí, trénování modelu nebo vyhodnocení modelu. V současné době Azure ML podporuje Python nebo PySpark pouze experimenty.


- **Model:** v Azure Machine Learning, modely odkazovat na produkt experimentu strojového učení. Jsou recepty, které při použití správně na data, generovat předpovězeným hodnotám. Modely lze nasadit do testovacího nebo produkčního prostředí a použít pro vyhodnocování nová data. Jednou v produkčním prostředí, modelů může být monitorovat data o výkonu a posun a retrained podle potřeby. 

- **Cílové výpočetní prostředí:** cílové výpočetní prostředí je výpočetní prostředek, který nakonfigurujete pro provádění své experimenty. Může být místní (Windows nebo macOS), kontejner Dockeru spuštěný na místním počítači nebo v virtuálního počítače s Linuxem v Azure nebo cluster HDInsight Spark.


- **Spustit:** služba experimentování ve službě definuje spustit jako životnost spuštění experimentu v cílové výpočetní prostředí. Služba Azure ML automaticky zaznamenává informace o jednotlivých spuštění a představuje celou historii konkrétní experiment v podobě historie spuštění.

- **Prostředí:** prostředí ve službě Azure Machine Learning, označuje konkrétního výpočetního prostředku, který se používá pro nasazování a správu modelů. Může být místního počítače, virtuální počítač s Linuxem v Azure nebo clusterů Kubernetes spuštěných v Azure Container Service, v závislosti na kontextu a konfigurace. Váš model je hostované v kontejneru Dockeru spuštěném v těchto prostředích a vystavený jako koncový bod rozhraní REST API.


- **Spravovaný model:** Správa modelů umožňuje nasazovat modely jako webové služby, správa různých verzí vašich modelů a monitorujte jejich výkon a metriky. Spravované modely jsou registrované pomocí účtu správy modelů Azure Machine Learning.

- **Manifesty:** systému pro správu modelu model nasadí do produkčního prostředí, obsahuje manifest, který může zahrnovat model, závislosti, hodnoticí skript, ukázková data a schéma. Manifest je předpisu použitý k vytvoření image kontejneru Dockeru. Pomocí správy modelů ve službě, můžete automaticky generovat manifesty, vytvořit různé verze a spravovat tyto manifesty. 


- **Obrázky:** manifesty můžete generovat (a znovu generovat) Image Dockeru. Vytvoření kontejnerové Image Dockeru flexibilitu pro spouštění v cloudu, v místních počítačích nebo na zařízení IoT. Image jsou samostatné a zahrnout všechny závislosti, které vyžaduje pro vyhodnocování nová data s modely. 

- **Services:** Správa modelů umožňuje nasazovat modely jako webové služby. Webová služba logiky a závislosti jsou zapouzdřeny do bitové kopie. Každá webová služba je sada kontejnery založené na obrázku připraveno na dané adrese URL žádosti o služby. Webová služba se počítá jako jedno nasazení.
