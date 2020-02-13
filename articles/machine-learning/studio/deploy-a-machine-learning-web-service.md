---
title: Nasazení webové služby
titleSuffix: ML Studio (classic) - Azure
description: Jak převést školicí experiment na prediktivní experiment, připravit ho pro nasazení a pak ho nasadit jako webovou službu Azure Machine Learning Studio (Classic).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 482dc0ab549ec6b9f05bc9f74a3d31ac13646ce5
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169209"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Nasazení webové služby Azure Machine Learning Studio (Classic)

Azure Machine Learning Studio (Classic) umožňuje sestavit a otestovat prediktivní analytické řešení. Pak můžete řešení nasadit jako webovou službu.

Webové služby Machine Learning Studio (Classic) poskytují rozhraní mezi aplikací a modelem vyhodnocování pracovního postupu pro Machine Learning Studio (Classic). Externí aplikace může komunikovat s modelem vyhodnocování pracovního postupu Machine Learning Studio (Classic) v reálném čase. Volání webové služby Machine Learning Studio (Classic) vrátí výsledky předpovědi do externí aplikace. Webovou službu můžete volat předáním klíče rozhraní API, který se vytvořil při nasazení této webové služby. Webová služba Machine Learning Studio (Classic) je založená na REST, na základě oblíbené architektury pro projekty webového programování.

Azure Machine Learning Studio (Classic) má dva typy webových služeb:

* Služba Request-response (RR): nízká latence, vysoce škálovatelná služba, která odpovídá jednomu datovému záznamu.
* Služba batch execution (BES): asynchronní služba, která vyrovnává dávku datových záznamů.

Vstup pro BES je podobný datové vstupu, který využívá RRS. Hlavním rozdílem je to, že BES čte blok záznamů z několika různých zdrojů, jako je třeba Azure Blob Storage, Azure Table Storage, Azure SQL Database, HDInsight (dotaz Hive) a zdroje HTTP.

Z hlediska vysoké úrovně si model nasadíte ve třech krocích:

* **[Vytvoření školicí experimentu]** – v studiu (Classic) můžete vytvářet a testovat model prediktivní analýzy pomocí školicích dat, která zadáte, a to pomocí rozsáhlé sady integrovaných algoritmů strojového učení.
* **[Převést na prediktivní experiment]** – Jakmile se model vyškole pomocí stávajících dat a jste připraveni ho použít k vyhodnocení nových dat, připravte a Zjednodušte svůj experiment pro předpovědi.
* **Nasazení** jako **[Nová webová služba]** nebo **[Klasická webová služba]** – při nasazení prediktivního experimentu jako webové služby Azure mohou uživatelé odesílat data do modelu a přijímat předpovědi modelu.

## <a name="create-a-training-experiment"></a>Vytvořit výukový experiment

K výuce modelu prediktivní analýzy použijte Azure Machine Learning Studio (Classic) k vytvoření školicího experimentu, který obsahuje různé moduly pro načtení školicích dat, přípravu dat podle potřeby, použití algoritmů strojového učení a vyhodnocení důsledk. Můžete iterovat na experiment a vyzkoušet různé algoritmy strojového učení pro porovnání a vyhodnocení výsledků.

Proces vytváření a správy pokusů o školení se podrobněji pokryje jinde. Další informace najdete v těchto článcích:

* [Vytvoření jednoduchého experimentu v Azure Machine Learning Studio (Classic)](create-experiment.md)
* [Vývoj prediktivního řešení s využitím Azure Machine Learning Studio (Classic)](tutorial-part1-credit-risk.md)
* [Import školicích dat do Azure Machine Learning Studio (Classic)](import-data.md)
* [Správa iterací experimentů v Azure Machine Learning Studio (Classic)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Převod výukového experimentu na prediktivní experiment

Jakmile svůj model provedete, budete připraveni převést svůj školicí experiment na prediktivní experiment, který bude vyhodnocovat skóre nových dat.

Převodem na prediktivní experiment získáte vyškolený model připravený k nasazení jako webovou službu bodování. Uživatelé webové služby mohou odesílat vstupní data do modelu a váš model vrátí zpět výsledky předpovědi. Při převodu na prediktivní experiment mějte na paměti, jak očekáváte, že váš model budou používat jiní uživatelé.

Chcete-li převést školicí experiment na prediktivní experiment, klikněte na tlačítko **Spustit** v dolní části plátna experiment, klikněte na možnost **nastavit webovou službu**a pak vyberte možnost **prediktivní webová služba**.

![Převést na experiment bodování](./media/publish-a-machine-learning-web-service/figure-1.png)

Další informace o tom, jak tento převod provést, najdete [v tématu Příprava modelu pro nasazení v Azure Machine Learning Studio (Classic)](convert-training-experiment-to-scoring-experiment.md).

Následující kroky popisují Nasazení prediktivního experimentu jako nové webové služby. Experiment můžete nasadit také jako klasickou webovou službu.

## <a name="deploy-it-as-a-new-web-service"></a>Nasadit jako novou webovou službu

Teď, když je připravený experiment připraven, můžete ho nasadit jako novou (Správce prostředků) webovou službu Azure. Pomocí webové služby, uživatelé můžou posílat data do modelu a vrátí jeho předpovědi modelu.

Pokud chcete nasadit prediktivní experiment, klikněte na **Spustit** v dolní části plátna experimentu. Po skončení běhu experimentu klikněte na **nasadit webovou službu** a vyberte **nasadit webovou službu [New]** .  Otevře se stránka nasazení portálu webové služby Machine Learning Studio (Classic).

> [!NOTE] 
> K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, ke kterému, můžete nasazení webové služby. Další informace najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md). 

### <a name="web-service-portal-deploy-experiment-page"></a>Stránka experimentu nasazení portálu webové služby

Na stránce nasazení experimentu zadejte název webové služby.
Vyberte Cenový tarif. Pokud máte existující Cenový tarif, můžete ho vybrat, jinak musíte pro službu vytvořit nový cenový plán.

1. V rozevíracím seznamu **cenový plán** vyberte existující plán nebo vyberte možnost **vybrat nový plán** .
2. Do pole **název plánu**zadejte název, který bude identifikovat plán na faktuře.
3. Vyberte jednu z **úrovní měsíčního plánu**. Výchozí plán úrovně plány vaší výchozí oblasti a webová služba je nasazený na danou oblast.

Klikněte na **nasadit** a otevře se stránka pro **rychlý Start** pro webovou službu.

Stránka pro rychlý Start webové služby vám poskytne přístup a doprovodné materiály k nejběžnějším úlohám, které budete provádět po vytvoření webové služby. Odtud můžete snadno přistupovat ke stránce test a k využití stránky.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Otestování nové webové služby

Pokud chcete otestovat novou webovou službu, klikněte v části běžné úlohy na **Testovat webovou službu** . Na stránce test můžete otestovat webovou službu jako službu požadavek-odpověď (RR) nebo službu Batch Execution (BES).

Na stránce testování záznamů se zobrazí vstupy, výstupy a všechny globální parametry, které jste pro experiment definovali. K otestování webové služby můžete ručně zadat příslušné hodnoty pro vstupy nebo zadat formátovaný soubor s hodnotami oddělenými čárkou (CSV) obsahující testovací hodnoty.

Chcete-li testovat pomocí prostředku, v režimu zobrazení seznamu zadejte příslušné hodnoty pro vstupy a klikněte na **testovat požadavek-odpověď**. Výsledky předpovědi se zobrazí ve sloupci Output vlevo.

![Zadejte odpovídající hodnoty pro otestování webové služby.](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

Pokud chcete otestovat BES, klikněte na **Batch**. Na stránce test dávky klikněte na Procházet pod vaším vstupem a vyberte soubor CSV obsahující příslušné vzorové hodnoty. Pokud nemáte soubor CSV a vytvořili jste prediktivní experiment pomocí Machine Learning Studio (Classic), můžete si stáhnout datovou sadu pro prediktivní experiment a použít ji.

Chcete-li stáhnout datovou sadu, otevřete Machine Learning Studio (Classic). Otevřete prediktivní experiment a klikněte pravým tlačítkem na vstup pro váš experiment. V místní nabídce vyberte **datová sada** a pak vyberte **Stáhnout**.

![Stáhněte si datovou sadu z plátna studia (Classic).](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Klikněte na tlačítko **test**. Stav úlohy spuštění dávky se zobrazí vpravo v části **testovací úlohy dávky**.

![Otestování úlohy provádění dávky na portálu webové služby](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Na stránce **Konfigurace** můžete změnit popis, název, aktualizovat klíč účtu úložiště a povolit ukázková data pro webovou službu.

![Konfigurace webové služby](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Přístup k nové webové službě

Po nasazení webové služby z Machine Learning Studio (Classic) můžete odesílat data do služby a programově přijímat odpovědi.

Stránka **využití** poskytuje všechny informace, které potřebujete pro přístup k webové službě. Například klíč rozhraní API je k dispozici pro povolení oprávněného přístupu ke službě.

Další informace o přístupu k webové službě Machine Learning Studio (Classic) najdete v tématu [Jak používat webové služby Azure Machine Learning Studio (Classic)](consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Správa nové webové služby

Nové webové služby můžete spravovat pomocí portálu Web Services Machine Learning Studio (Classic). Na [hlavní stránce portálu](https://services.azureml.net/)klikněte na **webové služby**. Na stránce webové služby můžete odstranit nebo zkopírovat službu. Chcete-li monitorovat konkrétní službu, klikněte na službu a pak klikněte na **řídicí panel**. Chcete-li monitorovat dávkové úlohy přidružené k webové službě, klikněte na položku **protokol žádosti o dávku**.

### <a id="multi-region"></a>Nasazení nové webové služby do více oblastí

Novou webovou službu můžete snadno nasadit do několika oblastí, aniž byste potřebovali víc předplatných nebo pracovních prostorů.

Ceny jsou specifické pro oblast, takže je potřeba definovat plán fakturace pro každou oblast, ve které budete webovou službu nasazovat.

#### <a name="create-a-plan-in-another-region"></a>Vytvoření plánu v jiné oblasti

1. Přihlaste se k [Microsoft Azure Machine Learning webové služby](https://services.azureml.net/).
2. Klikněte na možnost nabídky **plány** .
3. Na stránce plány pro zobrazení klikněte na **Nový**.
4. V rozevíracím seznamu **předplatné** vyberte předplatné, ve kterém se nový plán bude nacházet.
5. V rozevíracím seznamu **oblast** vyberte oblast pro nový plán. Možnosti plánu pro vybranou oblast se zobrazí v části **Možnosti plánu** stránky.
6. V rozevíracím seznamu **Skupina prostředků** vyberte skupinu prostředků pro daný plán. Další informace o skupinách prostředků najdete v tématu [přehled Azure Resource Manager](../../azure-resource-manager/management/overview.md).
7. V části **plán název** zadejte název plánu.
8. V části **Možnosti plánu**klikněte na úroveň fakturace nového plánu.
9. Klikněte na možnost **Vytvořit**.

#### <a name="deploy-the-web-service-to-another-region"></a>Nasazení webové služby do jiné oblasti

1. Na stránce Microsoft Azure Machine Learning webové služby klikněte na možnost nabídky **webové služby** .
2. Vyberte webovou službu, kterou nasazujete do nové oblasti.
3. Klikněte na **Kopírovat**.
4. Do pole **název webové služby**zadejte nový název webové služby.
5. Do pole **Popis webové služby**zadejte popis webové služby.
6. V rozevíracím seznamu **předplatné** vyberte předplatné, ve kterém bude nová webová služba umístěna.
7. V rozevíracím seznamu **Skupina prostředků** vyberte skupinu prostředků pro webovou službu. Další informace o skupinách prostředků najdete v tématu [přehled Azure Resource Manager](../../azure-resource-manager/management/overview.md).
8. V rozevíracím seznamu **oblast** vyberte oblast, do které chcete nasadit webovou službu.
9. V rozevíracím seznamu **účet úložiště** vyberte účet úložiště, do kterého chcete uložit webovou službu.
10. V rozevíracím seznamu **cenový plán** vyberte plán v oblasti, kterou jste vybrali v kroku 8.
11. Klikněte na **Kopírovat**.

## <a name="deploy-it-as-a-classic-web-service"></a>Nasaďte ji jako klasickou webovou službu.

Teď, když je prediktivní experiment dostatečně připravený, můžete ho nasadit jako klasickou webovou službu Azure. Pomocí webové služby, uživatelé můžou posílat data do modelu a vrátí jeho předpovědi modelu.

Pokud chcete nasadit prediktivní experiment, klikněte na **Spustit** v dolní části plátna experimentu a potom klikněte na **nasadit webovou službu**. Webová služba je nastavená a nachází se na řídicím panelu webové služby.

![Nasazení webové služby z studia (Classic)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>Testování klasické webové služby

Webovou službu můžete otestovat buď na portálu Machine Learning Studio (Classic) webových služeb, nebo v Machine Learning Studio (Classic).

Webovou službu Request response otestujete kliknutím na tlačítko **test** na řídicím panelu webové služby. Zobrazí se dialogové okno s žádostí o zadání vstupních dat pro službu. Toto jsou sloupce očekávané experimentem bodování. Zadejte sadu dat a pak klikněte na **OK**. Výsledky vygenerované webovou službou se zobrazí v dolní části řídicího panelu.

Kliknutím na odkaz **test** Preview můžete službu otestovat na portálu Azure Machine Learning Studio (Classic) Web Services, jak je uvedeno dříve v části Nová webová služba.

Chcete-li otestovat službu spuštění dávky, klikněte na odkaz **test** Preview. Na stránce test dávky klikněte na Procházet pod vaším vstupem a vyberte soubor CSV obsahující příslušné vzorové hodnoty. Pokud nemáte soubor CSV a vytvořili jste prediktivní experiment pomocí Machine Learning Studio (Classic), můžete si stáhnout datovou sadu pro prediktivní experiment a použít ji.

![Test webové služby](./media/publish-a-machine-learning-web-service/figure-3.png)

Na stránce **Konfigurace** můžete změnit zobrazovaný název služby a zadat pro něj popis. Název a popis se zobrazí v [Azure Portal](https://portal.azure.com/) , kde spravujete své webové služby.

Zadáním řetězce pro každý sloupec v části **vstupní schéma**, **výstupní schéma**a **parametr webové služby**můžete zadat popis pro vstupní data, výstupní data a parametry webové služby. Tyto popisy se používají v dokumentaci k ukázkovému kódu, která je k dispozici pro webovou službu.

Protokolování můžete povolit, chcete-li diagnostikovat všechny chyby, které vidíte, když máte k dispozici webovou službu. Další informace najdete v tématu [Povolení protokolování pro webové služby Machine Learning Studio (Classic)](web-services-logging.md).

![Povolit protokolování na portálu Web Services](./media/publish-a-machine-learning-web-service/figure-4.png)

Koncovým bodům webové služby můžete také nakonfigurovat na portálu Azure Machine Learning Web Services podobným postupem, který jste použili dříve v části Nová webová služba. Možnosti se liší, můžete přidat nebo změnit popis služby, povolit protokolování a povolit ukázková data pro testování.

### <a name="access-your-classic-web-service"></a>Přístup k klasické webové službě

Po nasazení webové služby z Azure Machine Learning Studio (Classic) můžete odesílat data do služby a programově přijímat odpovědi.

Řídicí panel poskytuje všechny informace, které potřebujete pro přístup k webové službě. Například klíč rozhraní API je k dispozici pro povolení oprávněného přístupu ke službě a k dispozici jsou stránky s nápovědami k rozhraní API, které vám pomohou začít psát kód.

Další informace o přístupu k webové službě Machine Learning Studio (Classic) najdete v tématu [Jak používat webové služby Azure Machine Learning Studio (Classic)](consume-web-services.md).

### <a name="manage-your-classic-web-service"></a>Správa klasické webové služby

Existují různé akce, které můžete provést k monitorování webové služby. Můžete ho aktualizovat a odstranit. Kromě výchozího koncového bodu, který je vytvořen při jeho nasazení, můžete také přidat další koncové body do klasické webové služby.

Další informace najdete v tématech [Správa pracovního prostoru Azure Machine Learning Studio (klasický)](manage-workspace.md) a [Správa webové služby pomocí portálu Web Services Azure Machine Learning Studio (Classic)](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Aktualizovat webovou službu
Můžete provádět změny webové služby, jako je třeba aktualizace modelu pomocí dalších školicích dat, a znovu ho nasadit a přepsat původní webovou službu.

Chcete-li aktualizovat webovou službu, otevřete původní prediktivní experiment, který jste použili k nasazení webové služby, a proveďte upravitelnou kopii kliknutím na možnost **Uložit jako**. Proveďte změny a potom klikněte na **nasadit webovou službu**.

Vzhledem k tomu, že jste tento experiment nasadili dřív, zobrazí se dotaz, jestli chcete přepsat existující službu (klasickou webovou službu) nebo aktualizovat (novou webovou službu). Kliknutím na **Ano** nebo **aktualizovat** zastavíte existující webovou službu a nasadíme nový prediktivní experiment, který se nasadí na místo.

> [!NOTE]
> Pokud jste v původní webové službě provedli změny konfigurace, například zadáním nového zobrazovaného názvu nebo popisu, budete muset tyto hodnoty zadat znovu.

Jednou z možností aktualizace webové služby je přeučení modelu prostřednictvím kódu programu. Další informace najdete v tématu [přeučení modelů Machine Learning Studio (Classic) prostřednictvím kódu programu](/azure/machine-learning/studio/retrain-machine-learning-model).

## <a name="next-steps"></a>Další kroky

* Další technické informace o tom, jak funguje nasazení, najdete v tématu [jak model Machine Learning Studio (klasický) postupuje z experimentu na provozní webovou službu](model-progression-experiment-to-web-service.md).

* Podrobnosti o tom, jak model připravit k nasazení, najdete v tématu [Příprava modelu pro nasazení v Azure Machine Learning Studio (Classic)](convert-training-experiment-to-scoring-experiment.md).

* Existuje několik způsobů, jak využít REST API a komunikovat s webovou službou. Podívejte [se, jak využívat webovou službu Azure Machine Learning Studio (Classic)](consume-web-services.md).

<!-- internal links -->
[Vytvoření školicí experimentu]: #create-a-training-experiment
[Převést na prediktivní experiment]: #convert-the-training-experiment-to-a-predictive-experiment
[Nová webová služba]: #deploy-it-as-a-new-web-service
[Klasická webová služba]: #deploy-it-as-a-classic-web-service
[New]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
