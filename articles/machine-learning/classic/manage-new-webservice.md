---
title: 'ML Studio (Classic): Správa webových služeb – Azure'
description: Spravujte své Machine Learning nové a klasické webové služby pomocí portálu Microsoft Azure Machine Learning Web Services. Vzhledem k tomu, že klasické webové služby a nové webové služby jsou založené na různých základních technologiích, máte u každého z nich poněkud různé možnosti správy.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: d40d67b40d9e269f9ed6552467918cc878ffd197
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342504"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-classic-web-services-portal"></a>Správa webové služby pomocí portálu Web Services Azure Machine Learning Studio (Classic)

**platí pro:** ![ Ano ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (Classic) ![ bez](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  


Pomocí portálu Microsoft Azure Machine Learning Web Services můžete spravovat Machine Learning nové a klasické webové služby. Vzhledem k tomu, že klasické webové služby a nové webové služby jsou založené na různých základních technologiích, máte u každého z nich poněkud různé možnosti správy.

Na portálu Machine Learning Web Services můžete:

* Sledujte, jak se webová služba používá.
* Nakonfigurujte popis, aktualizujte klíče webové služby (jenom nové), aktualizujte svůj klíč účtu úložiště (jenom nové), povolte protokolování a povolte nebo zakažte ukázková data.
* Odstraňte webovou službu.
* Vytváření, odstraňování nebo Aktualizace fakturačních plánů (jenom nové)
* Přidat a odstranit koncové body (jenom klasické)

>[!NOTE]
>Klasické webové služby můžete spravovat také v [Machine Learning Studio (Classic)](https://studio.azureml.net) na kartě **webové služby** .

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Oprávnění ke správě nových webových služeb založených na správci prostředků

Nové webové služby se nasazují jako prostředky Azure. V takovém případě musíte mít správná oprávnění k nasazení a správě nových webových služeb.  Chcete-li nasadit nebo spravovat nové webové služby, musíte mít přiřazenou roli přispěvatel nebo správce v předplatném, ve kterém je webová služba nasazená. Pokud k pracovnímu prostoru Machine Learning Pozvěte jiného uživatele, musíte je před tím, než bude moct nasadit nebo spravovat webové služby, přiřadit k roli přispěvatel nebo správce v předplatném. 

Pokud uživatel nemá správná oprávnění pro přístup k prostředkům na portálu Azure Machine Learning Web Services, při pokusu o nasazení webové služby se zobrazí následující chyba:

*Nasazení webové služby se nezdařilo. Tento účet nemá dostatečný přístup k předplatnému Azure, které obsahuje tento pracovní prostor. Aby bylo možné nasadit webovou službu do Azure, musí být stejný účet pozván do pracovního prostoru a mít přístup k předplatnému Azure, které obsahuje tento pracovní prostor.*

Další informace o vytváření pracovního prostoru najdete v tématu [Vytvoření a sdílení pracovního prostoru Azure Machine Learning Studio (Classic)](create-workspace.md).

Další informace o nastavení přístupových oprávnění najdete v tématu [Správa přístupu pomocí RBAC a Azure Portal](../../role-based-access-control/role-assignments-portal.md).


## <a name="manage-new-web-services"></a>Správa nových webových služeb
Správa nových webových služeb:

1. Přihlaste se k portálu [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) pomocí účtu Microsoft Azure – použijte účet, který je přidružený k předplatnému Azure.
2. V nabídce klikněte na položku **webové služby**.

Zobrazí se seznam nasazených webových služeb pro vaše předplatné. 

Chcete-li spravovat webovou službu, klikněte na položku webové služby. Na stránce webové služby můžete:

* Klikněte na webovou službu, kterou chcete spravovat.
* Kliknutím na plán fakturace webové služby ho aktualizujte.
* Odstraní webovou službu.
* Zkopírujte webovou službu a nasaďte ji do jiné oblasti.

Když kliknete na webovou službu, otevře se stránka pro rychlý Start webové služby. Stránka pro rychlý Start webové služby má dvě možnosti nabídky, které vám umožní spravovat webovou službu:

* **Řídicí panel** – umožňuje zobrazit použití webové služby.
* **Konfigurovat** – umožňuje přidat popisný text, aktualizovat klíč pro účet úložiště přidružený k webové službě a povolit nebo zakázat ukázková data.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorování způsobu použití webové služby
Klikněte na kartu **řídicí panel** .

Z řídicího panelu můžete zobrazit celkové využití webové služby v časovém intervalu. V rozevírací nabídce tečka v pravém horním rohu grafu využití můžete vybrat období, které chcete zobrazit. Řídicí panel zobrazuje následující informace:

* **Žádosti v průběhu času** zobrazují krokový graf počtu požadavků v průběhu vybraného časového období. Může vám to usnadnit určení, jestli máte špičky využití.
* **Požadavek – požadavky na odpověď** zobrazí celkový počet volání požadavků a odpovědí, které služba přijala během vybraného časového období, a počet selhání.
* **Průměrná doba výpočtu odezvy požadavku** zobrazuje průměrnou dobu potřebnou k provedení přijatých požadavků.
* **Dávková žádost** zobrazuje celkový počet požadavků na dávky, které služba přijala během vybraného časového období, a počet selhání.
* **Průměrná latence úlohy** zobrazuje průměrnou dobu potřebnou k provedení přijatých požadavků.
* **Chyba** zobrazuje agregovaný počet chyb, ke kterým došlo při volání webové služby.
* **Náklady na služby** zobrazují poplatky za fakturační plán přidružený ke službě.

### <a name="configuring-the-web-service"></a>Konfigurace webové služby
Klikněte na možnost **Konfigurace** nabídky.

Můžete aktualizovat tyto vlastnosti:

* **Popis** umožňuje zadat popis webové služby.
* **Název** umožňuje zadat název webové služby.
* **Klíče** umožňují otočit primární a sekundární klíče rozhraní API.
* **Klíč účtu úložiště** vám umožní aktualizovat klíč pro účet úložiště přidružený k změnám webové služby. 
* **Možnost Povolit ukázková data** vám umožní poskytnout ukázková data, která můžete použít k otestování služby Request-Response. Pokud jste vytvořili webovou službu v Machine Learning Studio (Classic), budou se ukázková data pořízena z dat, která jste použili pro výuku modelu. Pokud jste službu vytvořili programově, data jsou přijímána z ukázkových dat, která jste zadali jako součást balíčku JSON.

### <a name="managing-billing-plans"></a>Správa fakturačních plánů
Na stránce rychlý Start webových služeb klikněte na možnost nabídky **plány** . Můžete také kliknout na plán přidružený ke konkrétní webové službě a spravovat tento plán.

* **Novinka** vám umožní vytvořit nový plán.
* Možnost **Přidat nebo odebrat instanci plánu** umožňuje "horizontální navýšení kapacity" k přidání kapacity.
* **Upgrade/downgrade** vám umožní škálovat nahoru stávající plán pro přidání kapacity.
* **Odstranění** umožňuje odstranit plán.

Kliknutím na plán zobrazíte jeho řídicí panel. Řídicí panel poskytuje snímek nebo plánování využití během vybraného časového období. Chcete-li vybrat časové období, které chcete zobrazit, klikněte na rozevírací nabídku **tečka** v pravém horním rohu řídicího panelu. 

Řídicí panel plán poskytuje následující informace:

* **Popis plánu** zobrazuje informace o nákladech a kapacitě spojené s plánem.
* **Využití plánu** zobrazuje počet transakcí a výpočetních hodin, které se účtují na základě plánu.
* **Webové služby** zobrazí počet webových služeb, které používají tento plán.
* **Hlavní webová služba podle volání** zobrazuje horní čtyři webové služby, které provádějí volání, která se účtují na základě plánu.
* **Hlavní webové služby podle COMPUTE – hodiny** zobrazují horní čtyři webové služby, které používají výpočetní prostředky, které se účtují na základě plánu.

## <a name="manage-classic-web-services"></a>Správa klasických webových služeb
> [!NOTE]
> Postupy v této části jsou relevantní pro správu klasických webových služeb prostřednictvím portálu Azure Machine Learning Web Services. Informace o správě klasických webových služeb prostřednictvím Machine Learning Studio (Classic) a Azure Portal najdete v tématu [Správa pracovního prostoru Azure Machine Learning Studio (Classic)](manage-workspace.md).
> 
> 

Správa klasických webových služeb:

1. Přihlaste se k portálu [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) pomocí účtu Microsoft Azure – použijte účet, který je přidružený k předplatnému Azure.
2. V nabídce klikněte na **klasické webové služby**.

Pokud chcete spravovat klasickou webovou službu, klikněte na **klasické webové služby**. Na stránce klasické webové služby můžete:

* Kliknutím na webovou službu zobrazíte přidružené koncové body.
* Odstraní webovou službu.

Když spravujete klasickou webovou službu, spravujete jednotlivé koncové body samostatně. Po kliknutí na webovou službu na stránce webové služby se otevře seznam koncových bodů přidružených k této službě. 

Na stránce koncového bodu klasické webové služby můžete přidat a odstranit koncové body služby. Další informace o přidávání koncových bodů najdete v tématu [vytváření koncových](create-endpoint.md)bodů.

Kliknutím na jeden z koncových bodů otevřete stránku pro rychlý Start webové služby. Na stránce rychlý Start jsou k dispozici dvě možnosti nabídky, které vám umožní spravovat webovou službu:

* **Řídicí panel** – umožňuje zobrazit použití webové služby.
* **Konfigurovat** – umožňuje přidat popisný text, zapnout a vypnout protokolování chyb, aktualizovat klíč pro účet úložiště přidružený k webové službě a povolit a zakázat ukázková data.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Monitorování způsobu použití webové služby
Klikněte na kartu **řídicí panel** .

Z řídicího panelu můžete zobrazit celkové využití webové služby v časovém intervalu. V rozevírací nabídce tečka v pravém horním rohu grafu využití můžete vybrat období, které chcete zobrazit. Řídicí panel zobrazuje následující informace:

* **Žádosti v průběhu času** zobrazují krokový graf počtu požadavků v průběhu vybraného časového období. Může vám to usnadnit určení, jestli máte špičky využití.
* **Požadavek – požadavky na odpověď** zobrazí celkový počet volání požadavků a odpovědí, které služba přijala během vybraného časového období, a počet selhání.
* **Průměrná doba výpočtu odezvy požadavku** zobrazuje průměrnou dobu potřebnou k provedení přijatých požadavků.
* **Dávková žádost** zobrazuje celkový počet požadavků na dávky, které služba přijala během vybraného časového období, a počet selhání.
* **Průměrná latence úlohy** zobrazuje průměrnou dobu potřebnou k provedení přijatých požadavků.
* **Chyba** zobrazuje agregovaný počet chyb, ke kterým došlo při volání webové služby.
* **Náklady na služby** zobrazují poplatky za fakturační plán přidružený ke službě.

### <a name="configuring-the-web-service"></a>Konfigurace webové služby
Klikněte na možnost **Konfigurace** nabídky.

Můžete aktualizovat tyto vlastnosti:

* **Popis** umožňuje zadat popis webové služby. Popis je povinné pole.
* **Protokolování** umožňuje povolit nebo zakázat protokolování chyb u koncového bodu. Další informace o protokolování najdete v tématu Povolení [protokolování pro Machine Learning webové služby](web-services-logging.md).
* **Možnost Povolit ukázková data** vám umožní poskytnout ukázková data, která můžete použít k otestování služby Request-Response. Pokud jste vytvořili webovou službu v Machine Learning Studio (Classic), budou se ukázková data pořízena z dat, která jste použili pro výuku modelu. Pokud jste službu vytvořili programově, data jsou přijímána z ukázkových dat, která jste zadali jako součást balíčku JSON.


