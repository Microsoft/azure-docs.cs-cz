---
title: Nasazení webové služby Machine Learning | Microsoft Docs
description: Jak převést výukový experiment prediktivní experiment, příprava pro nasazení a pak ho nasadit jako webovou službu Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: YasinMSFT
ms.author: yahajiza
manager: hjerez
editor: cgronlun
ms.assetid: 73a3e9c6-00d0-41d4-8cf1-2ec87713867e
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2017
ms.openlocfilehash: 0f22f22d40410b258b3945d9e78938384ce64767
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128389"
---
# <a name="deploy-an-azure-machine-learning-web-service"></a>Nasazení webové služby Azure Machine Learning
Azure Machine Learning můžete vytvářet, testovat a nasazovat řešení prediktivní analýzy.

Ze souhrnné bodu z – zobrazení provádí se v tři kroky:

* **[Vytvoření experimentu školení]**  -Azure Machine Learning Studio je spolupráce vizuální vývojové prostředí, které používáte a natrénuje a otestuje model prediktivní analýzy pomocí Cvičná data, který zadáte.
* **[Převést na prediktivní experiment]**  -po modelu má cvičena s existujícími daty a jste připraveni použít jej k skóre pro nová data, můžete připravit a zjednodušit experimentu pro předpovědi.
* **[Nasadit jako webovou službu]**  – můžete nasadit jako vaše prediktivní experiment [Nový] nebo [classic] Azure webové služby. Uživatelé posílat data do modelu a přijímat váš model předpovědi.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="create-a-training-experiment"></a>Vytvoření experimentu školení
K natrénování modelu prediktivní analýzy, použijete Azure Machine Learning Studio k vytvoření výukový experiment vložíte různých modulů k načtení dat školení, přípravě data podle potřeby, použít algoritmy strojového učení a vyhodnoťte výsledky. Můžete iterovat experiment a opakujte algoritmů různých strojového učení porovnat a vyhodnoťte výsledky.

Proces vytváření a správa experimenty školení je zahrnutých víc důkladně jinde. Další informace najdete v těchto článcích:

* [Vytvoření jednoduchého experimentu v nástroji Azure Machine Learning Studio](create-experiment.md)
* [Vývoj prediktivního řešení pomocí Azure Machine Learning](walkthrough-develop-predictive-solution.md)
* [Importu trénovacích dat do Azure Machine Learning Studio](import-data.md)
* [Správa iterací experimentu v nástroji Azure Machine Learning Studio](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Převést výukový experiment prediktivní experiment
Když jsme natrénovali model, jste připravení převést výukový experiment prediktivní experiment skóre pro nová data.

Převedením na prediktivní experiment vám trained model připravená k nasazení jako vyhodnocování webovou službu. Uživatelé webové služby může odesílat vstupních dat modelu a modelu pošle zpět výsledky předpovědi. Při převodu na prediktivní experiment vzít v úvahu, jak očekáváte, že váš model používat ostatní uživatelé.

Chcete-li převést výukový experiment prediktivní experiment, klikněte na tlačítko **spustit** dole na plátno experimentu klikněte na **nastavit webové služby**, pak vyberte **webové služby prediktivní**.

![Převést na vyhodnocování experimentu](./media/publish-a-machine-learning-web-service/figure-1.png)

Další informace o tom, jak provést tento převod najdete v tématu [postup přípravy modelu pro nasazení v nástroji Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

Následující kroky popisují nasazení prediktivní experiment jako novou webovou službu. Experiment můžete taky nasadit jako webovou službu Classic.

## <a name="deploy-it-as-a-web-service"></a>Nasadit jako webovou službu

Prediktivní experiment můžete nasadit jako novou webovou službu nebo jako webovou službu Classic.

### <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>Nasadit prediktivní experiment jako novou webovou službu
Teď, když byl připraven prediktivní experiment, můžete jej nasadit jako novou Azure webovou službu. Pomocí webové služby, mohou uživatelé odesílat data do modelu a vrátí její předpovědi modelu.

Chcete-li nasadit prediktivní experiment, klikněte na tlačítko **spustit** v dolní části na plátno experimentu. Po dokončení běhu experimentu klikněte na **nasazení webové služby** a vyberte **nasazení webové služby [nový]**.  Otevře se stránka nasazení portálu webová služba Machine Learning.

> [!NOTE] 
> K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, do které, můžete nasazení webové služby. Další informace najdete v tématu [spravovat webové služby pomocí portálu webové služby Azure Machine Learning](manage-new-webservice.md). 

#### <a name="machine-learning-web-service-portal-deploy-experiment-page"></a>Nasadit experimentu stránce portálu pro Machine Learning webové služby
Na stránce experimentu nasazení zadejte název pro webovou službu.
Vyberte cenový plán. Pokud máte existující cenový plán, že můžete ji vybrat, v opačném případě musíte vytvořit nový plán ceny pro službu.

1. V **cena plán** rozevírací nabídky vyberte existujícího plánu nebo **vyberte nový plán** možnost.
2. V **název plánu**, zadejte název, který bude identifikovat plán ve vašem vyúčtování.
3. Vyberte jednu z **měsíční plánování vrstev**. Výchozí plán vrstvy do plánů pro vaši oblast výchozí a webové služby je nasazený na danou oblast.

Klikněte na tlačítko **nasadit** a **rychlý Start** otevře se stránka pro webovou službu.

Stránku webové služby rychlý start získáte přístup a pokyny na běžné úkoly, které provedete po vytvoření webové služby. Tady jsou snadno přístupné zkušební stránku i spotřebě stránky.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

#### <a name="test-your-new-web-service"></a>Otestovat novou webovou službu
Chcete-li otestovat novou webovou službu, klikněte na tlačítko **testování webové služby** v části Běžné úlohy. Na stránce Test můžete otestovat webové služby jako požadavků a odpovědí služby (RR) nebo služba Batch Execution (BES).

Na stránce test RRS zobrazuje vstupy, výstupy a globální parametry definované pro experimentu. K otestování webové služby, můžete ručně zadejte příslušné hodnoty pro vstupy nebo zadat formátovaný soubor oddělený čárkami hodnotu (CSV) obsahující testování hodnoty.

Chcete-li otestovat pomocí RRS, z režimu zobrazení seznamu, zadejte příslušné hodnoty pro vstupy a klikněte na **testování požadavků a odpovědí**. Výstupní sloupce na levé straně zobrazí výsledky předpovědi.

![Nasazení webové služby](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Chcete-li otestovat váš BES, klikněte na tlačítko **Batch**. Na stránce test Batch pod váš vstup, klikněte na tlačítko Procházet a vyberte soubor CSV obsahující hodnoty odpovídající vzorku. Pokud nemáte soubor CSV a vytvořili experimentu prediktivní pomocí Machine Learning Studio, můžete stáhnout sadu dat pro prediktivní experiment a používat ho.

Chcete-li stáhnout sadu dat, otevřete Machine Learning Studio. Otevřete prediktivní experiment a klikněte pravým tlačítkem na vstup pro experimentu. V místní nabídce vyberte **datovou sadu** a pak vyberte **Stáhnout**.

![Nasazení webové služby](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Klikněte na tlačítko **Test**. Stav úlohy Batch Execution se zobrazí vpravo pod **testovací úlohy Batch**.

![Nasazení webové služby](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Na **konfigurace** stránky, můžete změnit popis, název, aktualizovat klíč účtu úložiště a povolit ukázková data pro webovou službu.

![Konfigurovat webovou službu](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

Jakmile máte ukázku nasazenou webovou službu, můžete:

* **Přístup k** přes rozhraní API webové služby.
* **Spravovat** přes portál Azure Machine Learning webových služeb.
* **Aktualizace** je-li změny modelu.

#### <a name="access-your-new-web-service"></a>Přístup k vaší nové webové služby
Jakmile nasadíte webovou službu z Machine Learning Studio, můžete odesílat data do služby a příjem odpovědí prostřednictvím kódu programu.

**Spotřebě** stránka obsahuje všechny informace, které potřebujete pro přístup k webové služby. Klíč rozhraní API je třeba zadat umožňující autorizovaný přístup ke službě.

Další informace o přístup k webové službě Machine Learning najdete v tématu [využívání Azure Machine Learning webové služby](consume-web-services.md).

#### <a name="manage-your-new-web-service"></a>Spravovat novou webovou službu
Můžete spravovat nový webový portál služby webové služby Machine Learning. Z [hlavní stránky portálu](https://services.azureml-test.net/), klikněte na tlačítko **webové služby**. Z webové stránky služby můžete odstranit nebo zkopírujte služby. Chcete-li monitorovat konkrétní službu, klikněte na službu a potom klikněte na **řídicí panel**. Monitorování úlohy batch přidružený k webové službě, klikněte na tlačítko **dávkové žádosti protokolu**.

### <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>Nasadit prediktivní experiment jako webovou službu Classic

Teď, když dostatečně připravený prediktivní experiment, můžete ho nasadit jako webovou službu Classic Azure. Pomocí webové služby, mohou uživatelé odesílat data do modelu a vrátí její předpovědi modelu.

Chcete-li nasadit prediktivní experiment, klikněte na tlačítko **spustit** v dolní části experimentu plátno a potom klikněte na **nasazení webové služby**. Je-li nastavit webovou službu a jsou umístěny v řídicím panelu webové služby.

![Nasazení webové služby](./media/publish-a-machine-learning-web-service/figure-2.png)

#### <a name="test-your-classic-web-service"></a>Testování vaší Classic webové služby

V portálu webové služby Machine Learning nebo Machine Learning Studio můžete otestovat webovou službu.

Chcete-li otestovat webovou službu Request Response, klikněte na tlačítko **testování** tlačítka na řídicím panelu webové služby. Zobrazí se dialogové okno se zobrazí pro žádost o vstupní data pro službu. Jedná se o očekávanou vyhodnocování experimentu sloupce. Zadejte sadu dat a pak klikněte na tlačítko **OK**. V dolní části řídicího panelu se nezobrazí výsledky vygenerovaných webovou službu.

Můžete kliknout na **testování** preview odkaz na testovací služby na portálu Azure Machine Learning webové služby, jako je uvedený výše v části nové webové služby.

Chcete-li otestovat spuštění služby Batch, klikněte na tlačítko **testování** preview odkaz. Na stránce test Batch pod váš vstup, klikněte na tlačítko Procházet a vyberte soubor CSV obsahující hodnoty odpovídající vzorku. Pokud nemáte soubor CSV a vytvořili experimentu prediktivní pomocí Machine Learning Studio, můžete stáhnout sadu dat pro prediktivní experiment a používat ho.

![Test webové služby](./media/publish-a-machine-learning-web-service/figure-3.png)

Na **konfigurace** stránky, můžete změnit zobrazovaný název služby a zadejte jeho popis. Název a popis se zobrazí v [portál Azure](https://portal.azure.com/) kde budete spravovat webové služby.

Můžete zadat popis vstupních dat, výstupní data a webové služby parametry tak, že zadáte řetězec pro každý sloupec v části **vstupní schéma**, **výstupního schématu**, a **webové služby Parametr**. Tyto popisy se používají v dokumentaci ukázkový kód zadaný pro webovou službu.

Můžete povolit protokolování diagnostiky všechny chyby, které se zobrazuje při přístupu k webové služby. Další informace najdete v tématu [povolení protokolování pro webové služby Machine Learning](web-services-logging.md).

![Konfigurovat webovou službu](./media/publish-a-machine-learning-web-service/figure-4.png)

Můžete také nakonfigurovat koncové body pro webovou službu na portálu webové služby Azure Machine Learning podobný postup uvedený výše v části nové webové služby. Možnosti se liší, můžete přidat nebo změnit popis služby, povolte protokolování a povolení ukázkových dat pro testování.

#### <a name="access-your-classic-web-service"></a>Přístup ke službě web Classic
Jakmile nasadíte webovou službu z Machine Learning Studio, můžete odesílat data do služby a příjem odpovědí prostřednictvím kódu programu.

Řídicí panel poskytuje všechny informace, které potřebujete získat přístup k webové služby. Například klíč rozhraní API zajišťuje autorizovaný přístup ke službě a stránkám nápovědy rozhraní API poskytované pomohou začít pracovat, psaní kódu.

Další informace o přístup k webové službě Machine Learning najdete v tématu [využívání Azure Machine Learning webové služby](consume-web-services.md).

#### <a name="manage-your-classic-web-service"></a>Správa webové služby Classic
Existují různé akce, které můžete provádět ke sledování webové služby. Můžete jej aktualizovat a odstranit ji. K webové službě Classic kromě výchozí koncový bod, který se vytvoří při jejím nasazení můžete také přidat další koncové body.

Další informace najdete v tématu [spravovat pracovní prostor služby Azure Machine Learning](manage-workspace.md) a [spravovat webové služby pomocí portálu webové služby Azure Machine Learning](manage-new-webservice.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

## <a name="update-the-web-service"></a>Aktualizovat webovou službu
Můžete provést změny k webové službě, jako je například aktualizací modelu s další Cvičná data a nasadit ho znovu přepsal původní webové služby.

Chcete-li aktualizovat webovou službu, otevřete původní prediktivní experiment jste použili k nasazení webové služby a proveďte upravitelné kopie kliknutím **uložit jako**. Provedené změny a pak klikněte na **nasazení webové služby**.

Protože jste nasadili tohoto experimentu před, zobrazí se výzva, pokud chcete přepsat (Classic webová služba) nebo aktualizovat existující službu (novou webovou službu). Kliknutím na tlačítko **Ano** nebo **aktualizace** zastaví existující webovou službu a nasadí nový experiment prediktivní je nasazen na příslušné místo.

> [!NOTE]
> Pokud jste udělali změny konfigurace v původní webové služby, například zadáte nový zobrazovaný název nebo popis, musíte znovu zadejte tyto hodnoty.
> 
> 

Jednou z možností pro aktualizaci webové služby je programově přeučit modelu. Další informace najdete v tématu o [programovém přeučení modelů Machine Learning](retrain-models-programmatically.md).

<!-- internal links -->
[Vytvoření experimentu školení]: #create-a-training-experiment
[Převést na prediktivní experiment]: #convert-the-training-experiment-to-a-predictive-experiment
[Nasadit jako webovou službu]: #deploy-it-as-a-web-service
[Nový]: #deploy-the-predictive-experiment-as-a-new-web-service
[Classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
