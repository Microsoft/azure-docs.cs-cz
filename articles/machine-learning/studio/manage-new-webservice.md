---
title: Správa webových služeb
titleSuffix: ML Studio (classic) - Azure
description: Spravujte své nové a klasické webové služby Machine Learning pomocí portálu Microsoft Azure Machine Learning Web Services. Vzhledem k tomu, že klasické webové služby a nové webové služby jsou založeny na různých základních technologiích, máte pro každou z nich mírně odlišné možnosti správy.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: 2277aa3de5955efe5a3e4cb938fa557352f89006
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217965"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-classic-web-services-portal"></a>Správa webové služby pomocí portálu webových služeb Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Své webové služby Machine Learning New and Classic můžete spravovat pomocí portálu Microsoft Azure Machine Learning Web Services. Vzhledem k tomu, že klasické webové služby a nové webové služby jsou založeny na různých základních technologiích, máte pro každou z nich mírně odlišné možnosti správy.

Na portálu Machine Learning Web Services můžete:

* Sledujte, jak je webová služba používána.
* Nakonfigurujte popis, aktualizujte klíče pro webovou službu (pouze nové), aktualizujte klíč účtu úložiště (pouze nový), povolte protokolování a povolte nebo zakažte ukázková data.
* Odstraňte webovou službu.
* Vytvořit, odstranit nebo aktualizovat fakturační plány (pouze nové).
* Přidání a odstranění koncových bodů (pouze klasické)

>[!NOTE]
>Klasické webové služby můžete také spravovat v [Machine Learning Studio (klasické)](https://studio.azureml.net) na kartě **Webové služby.**

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Oprávnění ke správě webových služeb založených na Novém správci prostředků

Nové webové služby se nasazují jako prostředky Azure. V důsledku toho musíte mít správná oprávnění k nasazení a správě nových webových služeb.  Chcete-li nasadit nebo spravovat nové webové služby, musíte být přiřazena role přispěvatele nebo správce v předplatném, do kterého je webová služba nasazena. Pokud pozvete jiného uživatele do pracovního prostoru strojového učení, musíte ho přiřadit k roli přispěvatele nebo správce v předplatném, aby mohl nasadit nebo spravovat webové služby. 

Pokud uživatel nemá správná oprávnění pro přístup k prostředkům na portálu Azure Machine Learning Web Services, obdrží při pokusu o nasazení webové služby následující chybu:

*Nasazení webové služby se nezdařilo. Tento účet nemá dostatečný přístup k předplatnému Azure, který obsahuje pracovní prostor. Aby bylo možné nasadit webovou službu do Azure, musí být stejný účet pozván do pracovního prostoru a musí mít přístup k předplatnému Azure, které obsahuje pracovní prostor.*

Další informace o vytváření pracovního prostoru najdete v [tématu Vytvoření a sdílení pracovního prostoru Azure Machine Learning Studio (klasické).](create-workspace.md)

Další informace o nastavení přístupových oprávnění najdete v [tématu Správa přístupu pomocí RBAC a portálu Azure](../../role-based-access-control/role-assignments-portal.md).


## <a name="manage-new-web-services"></a>Správa nových webových služeb
Správa nových webových služeb:

1. Přihlaste se k portálu [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) pomocí svého účtu Microsoft Azure – použijte účet, který je přidružený k předplatnému Azure.
2. V nabídce klepněte na **položku Webové služby**.

Zobrazí se seznam nasazených webových služeb pro vaše předplatné. 

Chcete-li spravovat webovou službu, klepněte na položku Webové služby. Na stránce Webové služby můžete:

* Chcete-li webovou službu spravovat, klepněte na ni.
* Kliknutím na fakturační plán webové služby ji aktualizujte.
* Odstraňte webovou službu.
* Zkopírujte webovou službu a nasaďte ji do jiné oblasti.

Po klepnutí na webovou službu se otevře stránka Rychlého spuštění webové služby. Webová služba Úvodní stránka má dvě možnosti nabídky, které umožňují spravovat webovou službu:

* **DASHBOARD** – umožňuje zobrazit využití webové služby.
* **KONFIGUROVAT** – Umožňuje přidat popisný text, aktualizovat klíč pro účet úložiště přidružený k webové službě a povolit nebo zakázat ukázková data.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Sledování způsobu, jakým je webová služba používána
Klikněte na kartu **ŘÍDICÍ PANEL.**

Z řídicího panelu můžete zobrazit celkové využití webové služby po určitou dobu. Období, které chcete zobrazit, můžete vybrat z rozevírací nabídky Období v pravém horním horním části grafů využití. Řídicí panel zobrazuje následující informace:

* **Požadavky v průběhu času** zobrazí krokový graf počtu požadavků za vybrané časové období. To může pomoci zjistit, zda dochází špičky v používání.
* **Požadavky na požadavek na odpověď** zobrazuje celkový počet volání požadavku a odpovědi, které služba přijala za vybrané časové období, a počet z nich se nezdařil.
* **Průměrná doba výpočtu odezvy požadavku** zobrazí průměr času potřebného k provedení přijatých požadavků.
* **Dávkové požadavky** zobrazují celkový počet dávkových požadavků, které služba obdržela za vybrané časové období, a počet z nich se nezdařil.
* **Průměrná latence úlohy** zobrazuje průměr času potřebného k provedení přijatých požadavků.
* **Chyby** zobrazují souhrnný počet chyb, ke kterým došlo při volání webové služby.
* **Náklady na služby** zobrazují poplatky za fakturační plán přidružený ke službě.

### <a name="configuring-the-web-service"></a>Konfigurace webové služby
Klepněte na možnost **konfigurovat** nabídku.

Můžete aktualizovat následující vlastnosti:

* **Popis** umožňuje zadat popis webové služby.
* **Název** umožňuje zadat název webové služby.
* **Klíče** umožňují otočit primární a sekundární klíče rozhraní API.
* **Klíč účtu úložiště** umožňuje aktualizovat klíč pro účet úložiště přidružený ke změnám webové služby. 
* **Povolit ukázková data** umožňuje poskytnout ukázková data, která můžete použít k testování služby Požadavek-odpověď. Pokud jste vytvořili webovou službu v Machine Learning Studio (klasické), ukázková data se odebere z dat, které slouží k trénování modelu. Pokud jste službu vytvořili programově, data jsou převzata z ukázkových dat, která jste poskytli jako součást balíčku JSON.

### <a name="managing-billing-plans"></a>Správa fakturačních plánů
Klikněte na možnost nabídky **Plány** na stránce Rychlý start webových služeb. Můžete také kliknout na plán přidružený k určité webové službě a spravovat tento plán.

* **Nový** umožňuje vytvořit nový plán.
* **Přidat nebo odebrat plán instance** umožňuje "horizontální navýšení kapacity" existující plán přidat kapacitu.
* **Upgrade / DownGrade** umožňuje "vertikálně navýšit kapacitu" existující plán pro přidání kapacity.
* **Odstranit** umožňuje odstranit plán.

Kliknutím na plán zobrazíte jeho řídicí panel. Řídicí panel poskytuje snímek nebo plánování využití za vybrané časové období. Chcete-li vybrat časové období, které chcete zobrazit, klikněte na rozevírací možnost **Období** v pravém horním bodě řídicího panelu. 

Řídicí panel plánu poskytuje následující informace:

* **Popis plánu** zobrazuje informace o nákladech a kapacitě spojené s plánem.
* **Využití plánu** zobrazuje počet transakcí a výpočetních hodin, které byly účtovány proti plánu.
* **Webové služby** zobrazují počet webových služeb, které používají tento plán.
* **Nejlepší webová služba podle volání** zobrazuje čtyři nejvyšší webové služby, které uskutečňují volání, které jsou účtovány proti plánu.
* **Nejlepší webové služby podle výpočetních hodů** zobrazí čtyři nejlepší webové služby, které používají výpočetní prostředky, které jsou účtovány proti plánu.

## <a name="manage-classic-web-services"></a>Správa klasických webových služeb
> [!NOTE]
> Postupy v této části jsou relevantní pro správu klasických webových služeb prostřednictvím portálu Azure Machine Learning Web Services. Informace o správě klasických webových služeb prostřednictvím Machine Learning Studio (klasické) a portálu Azure najdete v [tématu Správa pracovního prostoru Azure Machine Learning Studio (klasické).](manage-workspace.md)
> 
> 

Správa klasických webových služeb:

1. Přihlaste se k portálu [Microsoft Azure Machine Learning Web Services](https://services.azureml.net/quickstart) pomocí svého účtu Microsoft Azure – použijte účet, který je přidružený k předplatnému Azure.
2. V nabídce klepněte na **položku Klasické webové služby**.

Chcete-li spravovat klasickou webovou službu, klepněte na **položku Klasické webové služby**. Na stránce Klasické webové služby můžete:

* Kliknutím na webovou službu zobrazíte přidružené koncové body.
* Odstraňte webovou službu.

Při správě klasické webové služby spravujete každý koncový bod samostatně. Po klepnutí na webovou službu na stránce Webové služby se otevře seznam koncových bodů přidružených ke službě. 

Na stránce koncového bodu klasické webové služby můžete přidat a odstranit koncové body ve službě. Další informace o přidávání koncových bodů naleznete v [tématu Vytváření koncových bodů](create-endpoint.md).

Kliknutím na jeden z koncových bodů otevřete stránku Rychlého spuštění webové služby. Na stránce Rychlý start jsou k dispozici dvě možnosti nabídky, které umožňují spravovat webovou službu:

* **DASHBOARD** – umožňuje zobrazit využití webové služby.
* **KONFIGUROVAT** – Umožňuje přidat popisný text, zapnout a vypnout přihlašování a vyhlazování chyb, aktualizovat klíč pro účet úložiště přidružený k webové službě a povolit a zakázat ukázková data.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Sledování způsobu, jakým je webová služba používána
Klikněte na kartu **ŘÍDICÍ PANEL.**

Z řídicího panelu můžete zobrazit celkové využití webové služby po určitou dobu. Období, které chcete zobrazit, můžete vybrat z rozevírací nabídky Období v pravém horním horním části grafů využití. Řídicí panel zobrazuje následující informace:

* **Požadavky v průběhu času** zobrazí krokový graf počtu požadavků za vybrané časové období. To může pomoci zjistit, zda dochází špičky v používání.
* **Požadavky na požadavek na odpověď** zobrazuje celkový počet volání požadavku a odpovědi, které služba přijala za vybrané časové období, a počet z nich se nezdařil.
* **Průměrná doba výpočtu odezvy požadavku** zobrazí průměr času potřebného k provedení přijatých požadavků.
* **Dávkové požadavky** zobrazují celkový počet dávkových požadavků, které služba obdržela za vybrané časové období, a počet z nich se nezdařil.
* **Průměrná latence úlohy** zobrazuje průměr času potřebného k provedení přijatých požadavků.
* **Chyby** zobrazují souhrnný počet chyb, ke kterým došlo při volání webové služby.
* **Náklady na služby** zobrazují poplatky za fakturační plán přidružený ke službě.

### <a name="configuring-the-web-service"></a>Konfigurace webové služby
Klepněte na možnost **konfigurovat** nabídku.

Můžete aktualizovat následující vlastnosti:

* **Popis** umožňuje zadat popis webové služby. Popis je povinné pole.
* **Protokolování** umožňuje povolit nebo zakázat protokolování chyb na koncovém bodu. Další informace o protokolování naleznete v tématu Enable [logging for Machine Learning web services](web-services-logging.md).
* **Povolit ukázková data** umožňuje poskytnout ukázková data, která můžete použít k testování služby Požadavek-odpověď. Pokud jste vytvořili webovou službu v Machine Learning Studio (klasické), ukázková data se odebere z dat, které slouží k trénování modelu. Pokud jste službu vytvořili programově, data jsou převzata z ukázkových dat, která jste poskytli jako součást balíčku JSON.


