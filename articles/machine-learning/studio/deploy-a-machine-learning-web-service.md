---
title: Nasazení webové služby
titleSuffix: ML Studio (classic) - Azure
description: Jak převést trénovací experiment na prediktivní experiment, připravit ho na nasazení a pak ho nasadit jako webovou službu Azure Machine Learning Studio (klasická).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 6c81e50560de69f7702e852d4602680fde7f01f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218130"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Nasazení webové služby Azure Machine Learning Studio (klasické)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio (klasické) umožňuje vytvářet a testovat prediktivní analytické řešení. Potom můžete nasadit řešení jako webovou službu.

Machine Learning Studio (klasické) webové služby poskytují rozhraní mezi aplikací a Machine Learning Studio (klasické) workflow bodování modelu. Externí aplikace může komunikovat s machine learning studio (klasické) workflow bodování modelu v reálném čase. Volání machine learningstudio (klasické) webové služby vrátí výsledky předpovědi externí aplikace. Webovou službu můžete volat předáním klíče rozhraní API, který se vytvořil při nasazení této webové služby. Webová služba Machine Learning Studio (classic) je založena na REST, oblíbené architektuře pro projekty webového programování.

Azure Machine Learning Studio (klasické) má dva typy webových služeb:

* Služba odezvy na vyžádání (RRS): Služba s nízkou latencí a vysokou škálovatelnou hodnotou, která získá jeden datový záznam.
* Služba dávkového spuštění (BES): Asynchronní služba, která získá skóre dávky datových záznamů.

Vstup pro BES je podobný datové vstupu, který využívá RRS. Hlavním rozdílem je to, že BES čte blok záznamů z několika různých zdrojů, jako je třeba Azure Blob Storage, Azure Table Storage, Azure SQL Database, HDInsight (dotaz Hive) a zdroje HTTP.

Z hlediska vysoké úrovně nasadíte model ve třech krocích:

* **[Vytvořte trénovací experiment]** – ve studiu (klasické) můžete trénovat a testovat model prediktivní analýzy pomocí trénovacích dat, která zadáte, pomocí velké sady integrovaných algoritmů strojového učení.
* **[Převeďte jej na prediktivní experiment]** – jakmile bude váš model trénovaný s existujícími daty a budete připraveni jej použít k naskórování nových dat, připravíte a zjednodušíte experiment pro předpovědi.
* **Nasazení** jako **[nová webová služba]** nebo **[klasická webová služba]** – když nasadíte prediktivní experiment jako webovou službu Azure, uživatelé můžou odesílat data do vašeho modelu a přijímat předpovědi modelu.

## <a name="create-a-training-experiment"></a>Vytvoření trénovacího experimentu

Chcete-li trénovat model prediktivní analýzy, použijte Azure Machine Learning Studio (classic) k vytvoření trénovacího experimentu, kde zahrnete různé moduly pro načítání trénovacích dat, připravujete data podle potřeby, aplikujete algoritmy strojového učení a vyhodnobíte Výsledky. Můžete iterate na experiment a vyzkoušet různé algoritmy strojového učení porovnat a vyhodnotit výsledky.

Proces vytváření a řízení vzdělávacích experimentů je podrobněji popsán jinde. Další informace najdete v těchto článcích:

* [Vytvoření jednoduchého experimentu v Azure Machine Learning Studio (klasické)](create-experiment.md)
* [Vývoj prediktivního řešení s Azure Machine Learning Studio (klasický)](tutorial-part1-credit-risk.md)
* [Import trénovacích dat do Azure Machine Learning Studio (klasický)](import-data.md)
* [Správa iterací experimentů v Azure Machine Learning Studio (klasické)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Převedení tréninkového experimentu na prediktivní experiment

Po vyškolení modelu jste připraveni převést tréninkový experiment na prediktivní experiment, abyste získali nová data.

Převedením na prediktivní experiment získáte připravený trénovaný model k nasazení jako webová služba s hodnocením. Uživatelé webové služby můžete odeslat vstupní data do modelu a váš model odešle zpět výsledky předpovědi. Při převodu na prediktivní experiment mějte na paměti, jak očekáváte, že váš model budou používat ostatní uživatelé.

Chcete-li vzdělávací experiment převést na prediktivní experiment, klepněte v dolní části plátna experimentu na **tlačítko Spustit,** klepněte na tlačítko **Nastavit webovou službu**a vyberte **možnost Prediktivní webová služba**.

![Převést na experiment hodnocení](./media/publish-a-machine-learning-web-service/figure-1.png)

Další informace o tom, jak provést tuto konverzi, najdete v [tématu Jak připravit model pro nasazení v Azure Machine Learning Studio (klasické)](convert-training-experiment-to-scoring-experiment.md).

Následující kroky popisují nasazení prediktivního experimentu jako nové webové služby. Experiment můžete také nasadit jako klasickou webovou službu.

## <a name="deploy-it-as-a-new-web-service"></a>Nasazení jako nové webové služby

Teď, když byl připravený prediktivní experiment, můžete ho nasadit jako novou webovou službu Azure (založenou na Resource Manageru). Pomocí webové služby mohou uživatelé odesílat data do modelu a model vrátí své předpovědi.

Pokud chcete nasadit prediktivní experiment, klikněte v dolní části plátna experimentu na **Spustit.** Po dokončení experimentu klepněte na tlačítko **Nasadit webovou službu** a vyberte **možnost Nasadit webovou službu [Nová]**.  Otevře se stránka nasazení portálu webové služby Machine Learning Studio (classic).

> [!NOTE] 
> Chcete-li nasadit novou webovou službu, musíte mít dostatečná oprávnění v předplatném, do kterého nasadíte webovou službu. Další informace najdete [v tématu Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md). 

### <a name="web-service-portal-deploy-experiment-page"></a>Stránka nasazení experimentu portálu webové služby

Na stránce Deploy Experiment zadejte název webové služby.
Vyberte cenový plán. Pokud máte existující cenový plán, můžete jej vybrat, jinak musíte vytvořit nový cenový plán pro službu.

1. V rozevíracím přehledu **Cenový plán** vyberte existující plán nebo vyberte možnost **Vybrat nový plán.**
2. Do **pole Název plánu**zadejte název, který na vaší vyúčtování identifikuje plán.
3. Vyberte jednu z **úrovní měsíčního plánu**. Úrovně plánu výchozí plány pro výchozí oblast a vaše webová služba je nasazena do této oblasti.

Klikněte na **Nasadit** a otevře se stránka **Rychlý start** webové služby.

Webová služba QuickStart stránka vám přístup a pokyny k nejběžnější úkoly, které budete provádět po vytvoření webové služby. Odtud můžete snadno přistupovat jak na stránku Test, tak na stránku Spotřebovávat.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Otestujte novou webovou službu

Chcete-li otestovat novou webovou službu, klepněte na tlačítko **Testovat webovou službu** v rámci běžných úkolů. Na stránce Test můžete otestovat webovou službu jako službu odezvy (RRS) nebo službu dávkového spuštění (BES).

Testovací stránka RRS zobrazuje vstupy, výstupy a všechny globální parametry, které jste definovali pro experiment. Chcete-li webovou službu otestovat, můžete ručně zadat příslušné hodnoty pro vstupy nebo zadat soubor ve formátu CSV (comma separated) obsahující testovací hodnoty.

Chcete-li otestovat pomocí služby RRS, zadejte v režimu zobrazení seznamu příslušné hodnoty vstupů a klepněte na tlačítko **Testovat odpověď na požadavek**. Výsledky předpovědi se zobrazí ve výstupním sloupci vlevo.

![Zadejte příslušné hodnoty pro testování webové služby.](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Chcete-li bes otestovat, klepněte na tlačítko **Dávka**. Na stránce Dávkový test klikněte pod vstupem na Procházet a vyberte soubor CSV obsahující příslušné ukázkové hodnoty. Pokud nemáte soubor CSV a vytvořili jste prediktivní experiment pomocí Machine Learning Studio (klasické), můžete stáhnout sadu dat pro prediktivní experiment a použít ji.

Chcete-li stáhnout sadu dat, otevřete Machine Learning Studio (classic). Otevřete prediktivní experiment a klikněte pravým tlačítkem myši na vstup experimentu. V místní nabídce vyberte **datovou sadu** a pak vyberte **Stáhnout**.

![Stažení datové sady ze studiového (klasického) plátna](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Klepněte na tlačítko **Testovat**. Stav úlohy dávkového spuštění se zobrazí vpravo v části **Testovat dávkové úlohy**.

![Otestujte úlohu dávkového spuštění na portálu webové služby](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Na stránce **KONFIGURACE** můžete změnit popis, název, aktualizovat klíč účtu úložiště a povolit ukázková data pro webovou službu.

![Konfigurace webové služby](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Přístup k nové webové službě

Po nasazení webové služby z Machine Learning Studio (klasické), můžete odesílat data do služby a přijímat odpovědi programově.

Stránka **Spotřebovávat** obsahuje všechny informace, které potřebujete pro přístup k webové službě. Klíč rozhraní API je například k dispozici k povolení autorizovaného přístupu ke službě.

Další informace o přístupu k webové službě Machine Learning Studio (klasické) najdete v [tématu Jak využívat webovou službu Azure Machine Learning Studio (klasická).](consume-web-services.md)

### <a name="manage-your-new-web-service"></a>Správa nové webové služby

Nové webové služby můžete spravovat pomocí portálu Machine Learning Studio (klasický) portál webových služeb. Na [stránce hlavního portálu](https://services.azureml.net/)klepněte na **položku Webové služby**. Na stránce webových služeb můžete službu odstranit nebo zkopírovat. Chcete-li sledovat konkrétní službu, klepněte na službu a potom klepněte na **příkaz Řídicí panel**. Chcete-li sledovat dávkové úlohy přidružené k webové službě, klepněte na **položku Protokol dávkových požadavků**.

### <a name="deploy-your-new-web-service-to-multiple-regions"></a><a id="multi-region"></a>Nasazení nové webové služby do více oblastí

Novou webovou službu můžete snadno nasadit do více oblastí, aniž byste potřebovali více předplatných nebo pracovních prostorů.

Ceny jsou specifické pro oblast, takže je třeba definovat fakturační plán pro každou oblast, ve které budete nasazovat webovou službu.

#### <a name="create-a-plan-in-another-region"></a>Vytvoření plánu v jiné oblasti

1. Přihlaste se k [webovým službám Microsoft Azure Machine Learning .](https://services.azureml.net/)
2. Klikněte na možnost nabídky **Plány.**
3. Na stránce Plány nad zobrazením klikněte na **Nový**.
4. V rozevíracím plánu **Odběr** vyberte předplatné, ve kterém bude nový plán umístěn.
5. V rozevíracím souboru **Oblast** vyberte oblast pro nový plán. Možnosti plánu pro vybranou oblast se zobrazí v části **Možnosti plánu** na stránce.
6. V rozevíracím seznamu **Skupina prostředků** vyberte skupinu prostředků pro plán. Další informace o skupinách prostředků najdete v [tématu Přehled Správce prostředků Azure](../../azure-resource-manager/management/overview.md).
7. Do **pole Název plánu** zadejte název plánu.
8. V části **Možnosti plánu**klikněte na fakturační úroveň nového plánu.
9. Klikněte na **Vytvořit**.

#### <a name="deploy-the-web-service-to-another-region"></a>Nasazení webové služby do jiné oblasti

1. Na stránce Microsoft Azure Machine Learning Web Services klikněte na možnost nabídky **Webové služby.**
2. Vyberte webovou službu, kterou nasazujete do nové oblasti.
3. Klepněte na tlačítko **Kopírovat**.
4. Do **pole Název webové služby**zadejte nový název webové služby.
5. Do **popisu webové služby**zadejte popis webové služby.
6. V rozevíracím souboru **Předplatným** vyberte předplatné, ve kterém bude umístěna nová webová služba.
7. V rozevíracím seznamu **Skupina prostředků** vyberte skupinu prostředků pro webovou službu. Další informace o skupinách prostředků najdete v [tématu Přehled Správce prostředků Azure](../../azure-resource-manager/management/overview.md).
8. V rozevíracím souboru **Oblast** vyberte oblast, ve které chcete nasadit webovou službu.
9. V rozevíracím souboru **Účet úložiště** vyberte účet úložiště, ve kterém chcete webovou službu uložit.
10. V rozevíracím souboru **Cenový plán** vyberte plán v oblasti, kterou jste vybrali v kroku 8.
11. Klepněte na tlačítko **Kopírovat**.

## <a name="deploy-it-as-a-classic-web-service"></a>Nasazení jako klasické webové služby

Teď, když byl prediktivní experiment dostatečně připravený, můžete ho nasadit jako webovou službu Classic Azure. Pomocí webové služby mohou uživatelé odesílat data do modelu a model vrátí své předpovědi.

Chcete-li nasadit prediktivní experiment, klepněte v dolní části plátna experimentu na **tlačítko Spustit** a potom klepněte na tlačítko **Nasadit webovou službu**. Webová služba je nastavena a vy jste umístěni na řídicím panelu webové služby.

![Nasazení webové služby ze studia (klasické)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Otestujte svou klasickou webovou službu

Webovou službu můžete otestovat buď na portálu webových služeb Machine Learning Studio (klasické) nebo v Machine Learning Studiu (klasické).

Chcete-li otestovat webovou službu Požadavek na odpověď, klepněte na tlačítko **Testovat** na řídicím panelu webové služby. Objeví se dialogové okno, ve které se zobrazí vstupní data pro službu. Jedná se o sloupce očekávané bodovacím experimentem. Zadejte sadu dat a klepněte na tlačítko **OK**. Výsledky generované webovou službou jsou zobrazeny v dolní části řídicího panelu.

Kliknutím na odkaz **Náhled testu** můžete otestovat svou službu na portálu webových služeb Azure Machine Learning Studio (klasické), jak je znázorněno dříve v části Nová webová služba.

Chcete-li otestovat službu dávkového spuštění, klepněte na tlačítko **Testovat** odkaz náhled . Na stránce Dávkový test klikněte pod vstupem na Procházet a vyberte soubor CSV obsahující příslušné ukázkové hodnoty. Pokud nemáte soubor CSV a vytvořili jste prediktivní experiment pomocí Machine Learning Studio (klasické), můžete stáhnout sadu dat pro prediktivní experiment a použít ji.

![Otestování webové služby](./media/publish-a-machine-learning-web-service/figure-3.png)

Na stránce **KONFIGURACE** můžete změnit zobrazovaný název služby a poskytnout jí popis. Název a popis se zobrazí na [webu Azure Portal,](https://portal.azure.com/) kde spravujete webové služby.

Popis vstupních dat, výstupních dat a parametrů webové služby můžete zadat zadáním řetězce pro každý sloupec v části **INPUT SCHEMA**, **OUTPUT SCHEMA**a **Parametr webové služby**. Tyto popisy se používají v dokumentaci ukázkového kódu poskytnuté pro webovou službu.

Protokolování můžete povolit a diagnostikovat všechny chyby, které se zobrazují při přístupu k webové službě. Další informace naleznete v [tématu Enable logging for Machine Learning Studio (classic) web services](web-services-logging.md).

![Povolení protokolování na portálu webových služeb](./media/publish-a-machine-learning-web-service/figure-4.png)

Koncové body pro webovou službu můžete také nakonfigurovat na portálu Azure Machine Learning Web Services podobně jako postup, který byl uveden dříve v části Nová webová služba. Možnosti se liší, můžete přidat nebo změnit popis služby, povolit protokolování a povolit ukázková data pro testování.

### <a name="access-your-classic-web-service"></a>Přístup k klasické webové službě

Po nasazení webové služby z Azure Machine Learning Studio (klasické), můžete odesílat data do služby a přijímat odpovědi programově.

Řídicí panel poskytuje všechny informace, které potřebujete pro přístup k webové službě. Klíč rozhraní API je například k dispozici k povolení autorizovaného přístupu ke službě a stránky nápovědy rozhraní API jsou k dispozici, které vám pomohou začít psát kód.

Další informace o přístupu k webové službě Machine Learning Studio (klasické) najdete v [tématu Jak využívat webovou službu Azure Machine Learning Studio (klasická).](consume-web-services.md)

### <a name="manage-your-classic-web-service"></a>Správa klasické webové služby

Existují různé akce, které můžete provádět ke sledování webové služby. Můžete jej aktualizovat a odstranit. Můžete také přidat další koncové body klasické webové služby kromě výchozí koncový bod, který je vytvořen při nasazení.

Další informace najdete [v tématu Správa pracovního prostoru Azure Machine Learning Studio (klasické)](manage-workspace.md) a [Správa webové služby pomocí portálu webových služeb Azure Machine Learning Studio (klasické)](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Aktualizace webové služby
Můžete provést změny webové služby, jako je například aktualizace modelu s dalšími trénovacími daty, a znovu jej nasadit a přepsat původní webovou službu.

Chcete-li aktualizovat webovou službu, otevřete původní prediktivní experiment, který jste použili k nasazení webové služby, a vytvořte upravitelnou kopii klepnutím na tlačítko **ULOŽIT JAKO**. Proveďte změny a klepněte na tlačítko **Nasadit webovou službu**.

Vzhledem k tomu, že jste tento experiment nasadili již dříve, budete dotázáni, zda chcete přepsat (Klasická webová služba) nebo aktualizovat (Nová webová služba) existující službu. Klepnutím na **tlačítko ANO** nebo **aktualizace** zastaví stávající webové služby a nasadí nový prediktivní experiment je nasazen na jeho místo.

> [!NOTE]
> Pokud jste v původní webové službě provedli změny konfigurace, například zadání nového zobrazovacího názvu nebo popisu, budete muset tyto hodnoty zadat znovu.

Jednou z možností aktualizace webové služby je programové přeškolit model. Další informace naleznete v [tématu Retrain Machine Learning Studio (klasické) modely programově](/azure/machine-learning/studio/retrain-machine-learning-model).

## <a name="next-steps"></a>Další kroky

* Další technické podrobnosti o tom, jak nasazení funguje, naleznete [v tématu Jak model Machine Learning Studio (klasický) postupuje z experimentu na zprovozněnou webovou službu](model-progression-experiment-to-web-service.md).

* Podrobnosti o tom, jak připravit model k nasazení, najdete v [tématu Jak připravit model pro nasazení v Azure Machine Learning Studio (klasické)](convert-training-experiment-to-scoring-experiment.md).

* Existuje několik způsobů, jak využít REST API a komunikovat s webovou službou. Informace o [tom, jak využívat webovou službu Azure Machine Learning Studio (klasické)](consume-web-services.md).

<!-- internal links -->
[Vytvoření trénovacího experimentu]: #create-a-training-experiment
[Převést na prediktivní experiment]: #convert-the-training-experiment-to-a-predictive-experiment
[Nová webová služba]: #deploy-it-as-a-new-web-service
[Klasická webová služba]: #deploy-it-as-a-classic-web-service
[Nwe]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
