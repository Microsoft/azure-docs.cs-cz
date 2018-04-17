---
title: Použití portálu webové služby Azure Machine Learning | Microsoft Docs
description: Správa přístupu k Azure Machine Learning pracovní prostory a nasadit a spravovat rozhraní API pro ML webové služby
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: b62cf2ca-dd2a-4a83-bb54-469f948fb026
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.openlocfilehash: c02ded6605b6374e0e37462205f8390f54b24b58
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-web-services-portal"></a>Správa webové služby pomocí portálu Azure Machine Learning webové služby
Můžete spravovat vaše nové Machine Learning a Classic webové služby pomocí portálu Microsoft Azure Machine Learning webové služby. Vzhledem k tomu, že Classic webové služby a nové webové služby jsou založená na různých základní technologií, máte možnosti mírně lišit správy pro každý z nich.

Na portálu webové služby Machine Learning můžete:

* Sledujte, jak je používán webovou službu.
* Konfigurovat popis, aktualizujte klíče pro webové služby (pouze nové), aktualizace vašeho úložiště účet klíče (pouze nové), povolit protokolování, zakázání nebo povolení ukázková data.
* Odstraňte webovou službu.
* Vytvoření, odstranění nebo aktualizaci fakturace plány (pouze nové).
* Přidání a odstranění koncových bodů (pouze klasické)

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Oprávnění ke správě nového správce prostředků na základě webové služby

Nové webové služby jsou nasazeny jako prostředky Azure. Jako takový musí mít správná oprávnění k nasazení a správě nových webových služeb.  Nasadit nebo spravovat nové webové služby musí mít přiřazenou roli Přispěvatel nebo správce na předplatné, která je nasazena webová služba. Pokud můžete pozvat jiného uživatele na pracovní prostor machine learning, musíte je přiřadit k roli Přispěvatel nebo správce na předplatné, než můžete nasadit nebo spravovat webové služby. 

Pokud uživatel nemá správná oprávnění pro přístup k prostředkům v portálu webové služby Azure Machine Learning, obdrží při pokusu o nasazení webové služby k následující chybě:

*Nasazení webové služby se nezdařilo. Tento účet nemá dostatečný přístup k předplatnému Azure, který obsahuje pracovním prostoru. Abyste mohli nasadit webovou službu Azure, stejný účet musí být do pracovního prostoru pozvat a být poskytnut přístup k předplatnému Azure, který obsahuje pracovním prostoru.*

Další informace o vytváření pracovního prostoru najdete v tématu [vytvoření a sdílení pracovní prostor služby Azure Machine Learning](create-workspace.md).

Další informace o nastavení oprávnění přístupu najdete v tématu [zobrazení přiřazení přístupu pro uživatele a skupiny na portálu Azure – ve verzi Public preview](../../role-based-access-control/role-assignments-users.md).


## <a name="manage-new-web-services"></a>Správa nové webové služby
Správa nové webové služby:

1. Přihlaste se k [webové služby aplikace Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) portálu Microsoft Azure pomocí účtu – použijte účet, který je spojen s předplatným služby Azure.
2. V nabídce klikněte na tlačítko **webové služby**.

Zobrazí se seznam nasazených webových služeb pro vaše předplatné. 

Chcete-li spravovat webové služby, klikněte na tlačítko webové služby. Na stránce webové služby můžete:

* Klikněte na webovou službu k její správě.
* Klikněte na tlačítko fakturační plán pro webovou službu jej aktualizovat.
* Odstraňte webovou službu.
* Zkopírujte webové služby a nasadíte ho do jiné oblasti.

Klepnutím na webové služby, otevřete stránku rychlý start webové služby. Stránku rychlý start webové služby má dvě možnosti nabídky, které vám umožní spravovat webové služby:

* **Řídicí panel** -vám umožní zobrazit webovou službu využití.
* **KONFIGURACE** – umožňuje přidat popisný text aktualizovat klíč pro účet úložiště, který je přidružený k webové službě a povolit nebo zakázat ukázková data.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorování, jak je používán webovou službu
Klikněte **řídicí panel** kartě.

Na řídicím panelu můžete zobrazit celkové využití webové služby v časovém intervalu. Můžete vybrat období zobrazení období rozevírací nabídce v pravé horní části grafy využití. Řídicí panel zobrazuje následující informace:

* **Požadavky v čase** zobrazí graf krok počet požadavků za vybrané časové období. Může pomoct určit, zda dochází k špičky využití.
* **Požadavky požadavků a odpovědí** zobrazí celkový počet požadavků a odpovědí volání, které Služba obdržela přes zvolené časové období a kolik z nich se nezdařilo.
* **Průměrná doba výpočetní požadavků a odpovědí** Průměrná doba potřebná k provedení přijatých požadavků.
* **Požadavky služby batch** zobrazí celkový počet dávkových žádostí, které Služba obdržela přes zvolené časové období a kolik z nich se nezdařilo.
* **Průměrná latence úlohy** Průměrná doba potřebná k provedení přijatých požadavků.
* **Chyby** zobrazí souhrnný počet chyb, k nimž došlo v volání webové služby.
* **Služby náklady** zobrazí poplatky za fakturační plán spojené s touto službou.

### <a name="configuring-the-web-service"></a>Konfigurace webové služby
Klikněte **konfigurace** možnost nabídky.

Můžete aktualizovat následující vlastnosti:

* **Popis** můžete zadat popis pro webovou službu.
* **Název** umožňuje zadat název pro webovou službu
* **Klíče** umožňuje otočit primární a sekundární klíče rozhraní API.
* **Klíč účtu úložiště** umožňuje aktualizovat klíč pro účet úložiště, které jsou přidružené k změny webové služby. 
* **Povolit ukázková data** umožňuje poskytovat ukázková data, která můžete použít k testování služby požadavků a odpovědí. Pokud jste vytvořili webovou službu v nástroji Machine Learning Studio, ukázkových dat je převzat ze data vaší použité k natrénování modelu. Pokud jste vytvořili službu prostřednictvím kódu programu, data je převzat ze příklad dat, které jste zadali jako součást balíčku JSON.

### <a name="managing-billing-plans"></a>Správa fakturace plány
Klikněte **plány** nabídky ze stránky rychlé spuštění webové služby. Můžete také kliknout na plán přidružený specifické webové služby ke správě tohoto plánu.

* **Nové** vám umožní vytvořit nový plán.
* **Přidat nebo odebrat plán instance** umožňuje "škálovat" existujícího plánu přidat kapacitu.
* **Upgrade nebo přechod na starší verzi** umožňuje "škálovat" existujícího plánu přidat kapacitu.
* **Odstranit** umožňuje odstranit plán.

Klikněte na tlačítko plán k zobrazení jeho řídicího panelu. Řídicí panel poskytuje použití snímků nebo se chystáte nad vybraném časovém období. Chcete-li vybrat časové období zobrazení, klikněte na tlačítko **období** rozevírací nabídce v pravé horní části řídicího panelu. 

Řídicí panel plán poskytuje následující informace:

* **Plánování popis** zobrazí informace o nákladech a kapacity přidružený k plánu.
* **Plánování použití** zobrazí počet transakcí a výpočetní hodiny, které byly účtovat proti plánu.
* **Webové služby** zobrazí počet webové služby, které používají tento plán.
* **TOP webové služby pomocí volání** zobrazí horní čtyři webové služby, které jsou volání které připsány plánu.
* **Nejlepších webové služby podle hodin výpočetní** zobrazí horní čtyři webové služby, které používají výpočetní prostředky, které budou účtovat proti plánu.

## <a name="manage-classic-web-services"></a>Spravovat Classic webové služby
> [!NOTE]
> Postupy v této části jsou relevantní pro správy Classic webových služeb prostřednictvím portálu webové služby Azure Machine Learning. Informace o správě Classic webové služby Machine Learning Studio a portálu Azure najdete v tématu [spravovat pracovní prostor služby Azure Machine Learning](manage-workspace.md).
> 
> 

Správa Classic webové služby:

1. Přihlaste se k [webové služby aplikace Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) portálu Microsoft Azure pomocí účtu – použijte účet, který je spojen s předplatným služby Azure.
2. V nabídce klikněte na tlačítko **Classic webové služby**.

Chcete-li spravovat Classic webové služby, klikněte na tlačítko **Classic webové služby**. Na stránce Classic webové služby můžete:

* Klikněte na webovou službu, zobrazí se koncové body přidružené.
* Odstraňte webovou službu.

Když spravujete Classic webové služby, můžete spravovat všechny koncové body samostatně. Když kliknete na webové stránce webové služby, otevře se seznam koncových bodů, které jsou spojené s touto službou. 

Na stránce koncový bod Classic webové služby můžete přidat a odstranit koncové body služby. Další informace o přidání koncové body, najdete v části [vytváření koncových bodů](create-endpoint.md).

Klikněte na jednu z koncových bodů otevřete stránku rychlý start webové služby. Na stránce Rychlý start existují dvě možnosti nabídky, které vám umožní spravovat webové služby:

* **Řídicí panel** -vám umožní zobrazit webovou službu využití.
* **KONFIGURACE** – umožňuje přidat popisný text, zapnout protokolování chyb a vypnout, aktualizovat klíč pro účet úložiště, který je přidružený k webové službě a povolení a zákaz ukázková data.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorování, jak je používán webovou službu
Klikněte **řídicí panel** kartě.

Na řídicím panelu můžete zobrazit celkové využití webové služby v časovém intervalu. Můžete vybrat období zobrazení období rozevírací nabídce v pravé horní části grafy využití. Řídicí panel zobrazuje následující informace:

* **Požadavky v čase** zobrazí graf krok počet požadavků za vybrané časové období. Může pomoct určit, zda dochází k špičky využití.
* **Požadavky požadavků a odpovědí** zobrazí celkový počet požadavků a odpovědí volání, které Služba obdržela přes zvolené časové období a kolik z nich se nezdařilo.
* **Průměrná doba výpočetní požadavků a odpovědí** Průměrná doba potřebná k provedení přijatých požadavků.
* **Požadavky služby batch** zobrazí celkový počet dávkových žádostí, které Služba obdržela přes zvolené časové období a kolik z nich se nezdařilo.
* **Průměrná latence úlohy** Průměrná doba potřebná k provedení přijatých požadavků.
* **Chyby** zobrazí souhrnný počet chyb, k nimž došlo v volání webové služby.
* **Služby náklady** zobrazí poplatky za fakturační plán spojené s touto službou.

### <a name="configuring-the-web-service"></a>Konfigurace webové služby
Klikněte **konfigurace** možnost nabídky.

Můžete aktualizovat následující vlastnosti:

* **Popis** můžete zadat popis pro webovou službu. Popis je povinné pole.
* **Protokolování** umožňuje povolit nebo zakázat protokolování na koncovém bodu. Další informace o protokolování naleznete v tématu Povolení [protokolování pro webové služby Machine Learning](web-services-logging.md).
* **Povolit ukázková data** umožňuje poskytovat ukázková data, která můžete použít k testování služby požadavků a odpovědí. Pokud jste vytvořili webovou službu v nástroji Machine Learning Studio, ukázkových dat je převzat ze data vaší použité k natrénování modelu. Pokud jste vytvořili službu prostřednictvím kódu programu, data je převzat ze příklad dat, které jste zadali jako součást balíčku JSON.


