---
title: "Přehled funkcí Azure Machine Learning preview | Microsoft Docs"
description: "Koncepční přehled funkce preview Azure Machine Learning, jako je například odběry, účty, pracovní prostory, projekty, atd."
services: machine-learning
author: serinakaye
ms.author: serinak
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 09/06/2017
ms.openlocfilehash: ea9da6f23fd08c09f9e805519487648480816f35
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2017
---
# <a name="azure-machine-learning---concepts"></a>Azure Machine Learning – koncepty

Tento článek definuje a popisuje koncepty, které potřebujete vědět, abyste mohli používat Azure Machine Learning. 

![Hierarchie koncepty](media/overview-general-concepts/hierarchy.png)

- **Předplatné:** předplatné Azure uděluje přístup k prostředkům v Azure. Protože Azure Machine Learning se úzce integruje se výpočty, úložiště a mnoho dalších prostředků Azure a služby, Workbench vyžaduje, aby každý uživatel má přístup k platné předplatné Azure. Uživatelé musí také mít dostatečná oprávnění v rámci tohoto předplatného vytvořit prostředky.


- **Účet experimentování:** experimentování účet je prostředek Azure vyžadují Azure ML a fakturace vehicle. Obsahuje pracovní prostory, která obsahují projekty. Můžete přidat více uživatelů, označuje jako _licencí_, k účtu experimenty. Aby bylo možné použít ke spuštění experimenty Azure ML Workbench musí mít přístup k účtu experimenty. 


- **Model správy účtu** účet modelu správy je také prostředek služby Azure vyžaduje Azure ML pro správu modelů. Můžete ho registrovat manifestů a modely, sestavení kontejnerizované webové služby a nasadit místně nebo v cloudu. Je také další fakturace vehicle Azure ML.


- **Pracovní prostor:** pracovního prostoru je primární součásti pro sdílení a spolupráce v Azure ML. Projekty jsou seskupené v rámci pracovního prostoru. Pracovního prostoru je pak možné sdílet s více uživatelů, které byly přidány k experimentování účtu.


- **Projekt:** v Azure Machine Learning projektu je logický kontejner pro všechny pracovní prováděná k vyřešení problému. Mapuje do jednoho souboru složky na váš místní disk, a můžete přidat soubory nebo sub složek na ni. Volitelně lze přidružit úložiště Git pro správy zdrojového kódu a spolupráci projektu.  

- **Experiment:** v Azure ML experimentu je jeden nebo více soubory zdrojového kódu, které mohou být provedeny z jeden vstupní bod. Může obsahovat úlohy, jako je přijímání dat, funkce technikům, školení modelu nebo vyhodnocení modelu. V současné době podporuje Azure ML Python nebo pouze experimenty PySpark.


- **Model:** v Azure Machine Learning, označují modely produkt experimentu strojového učení. Jsou recepty, když správně použitá k datům, generovat předpovězené hodnoty. Modely lze nasadit do testovacího nebo produkční prostředí a použít pro výpočet skóre nová data. Jednou v produkčním prostředí, modely mohou být monitorovat data výkonu a odlišily a retrained podle potřeby. 

- **Cíl výpočetní:** výpočetní cíl se nachází výpočetní prostředek, který jste nakonfigurovali pro provádění experimentů. Může být místním počítači (Windows nebo systému macOS), kontejner Docker spuštěn v místním počítači nebo v virtuálního počítače s Linuxem v Azure nebo clusteru HDInsight Spark.


- **Spustit:** službu experimentování definuje spustit jako životnost spuštění experimentu v výpočetní cíl. Azure ML zaznamená informace o každé spuštění automaticky a uvede celou historii konkrétní experimentu ve formě historie spouštění.

- **Prostředí:** v Azure Machine Learning, prostředí označuje konkrétní výpočetní prostředek, který se používá pro nasazení a správě modely. Může to být místního počítače, virtuální počítač s Linuxem v Azure nebo Kubernetes clusteru se systémem v Azure Container Service, v závislosti na kontextu a konfigurace. Váš model je hostovaná v kontejner Docker spuštěna v těchto prostředích a zveřejněné jako koncový bod REST API.


- **Spravovaný model:** Model správy umožňuje nasazovat modely jako webové služby, spravovat různé verze vaší modelů a monitorovat jejich výkonu a metriky. Spravované modely je zaregistrovaná pomocí účtu Azure Machine Learning modelu správy.

- **Manifesty:** systému modelu správy model nasadí do produkčního prostředí, obsahuje manifestu, který může zahrnovat modelu, závislosti, vyhodnocování skriptu, ukázkových dat a schéma. Manifest je recepturách použít k vytvoření bitové kopie kontejner Docker. Pomocí modelu správy můžete automaticky generovat manifesty, vytvořit různé verze a spravovat tyto manifesty. 


- **Obrázky:** manifesty můžete použít k vygenerování (a znovu je obnovovat) imagí Dockeru. Kontejnerizované imagí Dockeru vytvořit flexibilitu spustit v cloudu, na místním počítači nebo na zařízení IoT. Bitové kopie jsou samostatné a zahrnují všechny závislosti, které jsou nutné pro vyhodnocování nová data s modely. 

- **Služba:** Model správy umožňuje nasadit modely jako webové služby. Webová služba logiku a závislosti jsou zapouzdřeny do bitové kopie. Každý webová služba je sada kontejnery podle image připraveno k žádosti o službu na dané adrese URL. Webové služby se počítá jako jedno nasazení.
