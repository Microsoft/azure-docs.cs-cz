---
title: Nasazení webové služby Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Postup převedení školicího experimentu na prediktivní experiment, její přípravu na nasazení a pak ji nasadit jako webovou službu Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: ericlicoding
ms.author: amlstudiodocs
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 67d0ef88072985141c05d9da77377e5d4228a669
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56270313"
---
# <a name="deploy-an-azure-machine-learning-studio-web-service"></a>Nasazení webové služby Azure Machine Learning Studio

Azure Machine Learning Studio umožňuje sestavení a otestování prediktivních analytických řešení. Pak můžete nasadit řešení jako webovou službu.

Webové služby Machine Learning Studio poskytují rozhraní mezi aplikací a modelem Machine Learning Studio bodovací pracovního postupu. Externí aplikace komunikovat s hodnoticí modelem Machine Learning Studio pracovních postupů v reálném čase. Volání webové služby Machine Learning Studio vrací do externí aplikace predikované výsledky. Webovou službu můžete volat předáním klíče rozhraní API, který se vytvořil při nasazení této webové služby. Webová služba Machine Learning Studio je založen na REST, možnost Oblíbené architektuře programátorských projektů na webu.

Azure Machine Learning Studio nabízí dva typy webových služeb:

* Služba Request-Response (RRS): Nízká latence, vysoce škálovatelná služba, která stanoví skóre jeden datový záznam.
* Služba batch Execution (BES): Asynchronní služba pro vyhodnocování dávek datových záznamů.

Vstup pro BES je podobný datové vstupu, který využívá RRS. Hlavním rozdílem je to, že BES čte blok záznamů z několika různých zdrojů, jako je třeba Azure Blob Storage, Azure Table Storage, Azure SQL Database, HDInsight (dotaz Hive) a zdroje HTTP.


Z vysoké úrovni bodu kolegů nasazení modelu ve třech krocích:

* **[Vytvořit výukový experiment]**  – v sadě Studio můžete trénování a testování modelu prediktivní analýzy pomocí trénovacích dat, který zadáte, pomocí rozsáhlé sady algoritmů integrované strojového učení.
* **[Převeďte jej na prediktivní experiment]**  – Jakmile vyškolila modelu s existujícími daty a teď můžete použít ke stanovení skóre pro nová data, Příprava a zefektivnit experimentu pro předpovědi.
* **[Ho nasadit jako webovou službu]**  – můžete nasadit jako prediktivní experiment *Classic* nebo *nový* služby Azure web (využívající Resource Manager). Uživatelé můžou posílat data do modelu a obdrží předpovědi modelu.



## <a name="create-a-training-experiment"></a>Vytvořit výukový experiment
K natrénování modelu prediktivní analýzy, použijete Azure Machine Learning Studio k vytvoření výukového experimentu vložíte různých modulů zatížení trénovací data, připravit data podle potřeby, používá algoritmy strojového učení a vyhodnoťte výsledky. Můžete iterovat experiment a zkuste to algoritmů různých strojového učení můžete porovnat a vyhodnoťte výsledky.

Postup vytváření a správa experimentů školení najdete více důkladně jinde. Další informace najdete v těchto článcích:

* [Vytvoření jednoduchého experimentu v nástroji Azure Machine Learning Studio](create-experiment.md)
* [Vývoj prediktivního řešení pomocí Azure Machine Learning Studio](tutorial-part1-credit-risk.md)
* [Import cvičných dat do Azure Machine Learning Studio](import-data.md)
* [Správa iterací experimentů v nástroji Azure Machine Learning Studio](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>Převod výukového experimentu na prediktivní experiment
Když jsme natrénovali model, jste připraveni převést výukový experiment prediktivní experiment ke stanovení skóre pro nová data.

Převedením na prediktivní experiment získáváte trénovaného modelu připravená k nasazení jako hodnoticí webové služby. Uživatelé webové služby, můžou posílat vstupní data do modelu a modelu odešle zpět výsledků předpovědí. Při převodu na prediktivní experiment mít na paměti, jak očekáváte, že váš model, chcete-li využívat další uživatelé.

Převod výukového experimentu na prediktivní experiment, klikněte na tlačítko **spustit** dole na plátno experimentu klikněte na tlačítko **nastavení webové služby**a pak vyberte **prediktivní webová služba**.

![Převod na bodování experimentu](./media/publish-a-machine-learning-web-service/figure-1.png)

Další informace o tom, jak provést tento převod najdete v tématu [přípravu modelu pro nasazení v Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

Následující kroky popisují nasazení prediktivního experimentu jako nové webové služby. Můžete taky nasadit experiment jako webové služby Classic.

## <a name="deploy-it-as-a-web-service"></a>Ho nasadit jako webovou službu

Prediktivní experiment můžete nasadit jako novou webovou službu nebo jako webové služby Classic.

### <a name="deploy-the-predictive-experiment-as-a-new-web-service"></a>Prediktivní experiment nasadit jako novou webovou službu
Teď, když byla připravena prediktivní experiment, ji můžete nasadit jako novou službu Azure web. Pomocí webové služby, uživatelé můžou posílat data do modelu a vrátí jeho předpovědi modelu.

Prediktivní experiment nasadit, klikněte na tlačítko **spustit** v dolní části na plátno experimentu. Po dokončení spuštění experimentu klikněte na tlačítko **nasadit webovou službu** a vyberte **nasadit webovou službu [nový]**.  Otevře se stránka nasazení portálu webové služby Machine Learning Studio.

> [!NOTE] 
> K nasazení nové webové služby musí mít dostatečná oprávnění v rámci předplatného, ke kterému, můžete nasazení webové služby. Další informace najdete v tématu [Správa webové služby pomocí portálu Azure Machine Learning Web Services](manage-new-webservice.md). 

#### <a name="machine-learning-studio-web-service-portal-deploy-experiment-page"></a>Nasazení experimentu stránce portálu pro Machine Learning Studio webové služby
Na stránce experimentu nasazení zadejte název pro webovou službu.
Vyberte cenový plán. Pokud máte stávajícím cenovým tarifem, že můžete ji vybrat, v opačném případě musíte vytvořit nový cenový plán služby.

1. V **cenový plán** rozevírací seznam, vyberte existující plán nebo **vyberte nový plán** možnost.
2. V **název plánu**, zadejte název, který bude identifikovat plán na faktuře.
3. Vyberte jednu z **měsíční plán úrovně**. Výchozí plán úrovně plány vaší výchozí oblasti a webová služba je nasazený na danou oblast.

Klikněte na tlačítko **nasadit** a **rychlý Start** se otevře stránka pro webovou službu.

Rychlý start stránku webové služby obsahuje přístup a pokyny o zvládnout běžné úkoly, které provedete po vytvoření webové služby. Z tohoto místa můžete snadno přístup k zkušební stránku i využívání stránky.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

#### <a name="test-your-new-web-service"></a>Otestovat novou webovou službu
Otestovat novou webovou službu, klikněte na tlačítko **testovat webovou službu** v rámci běžných úloh. Na stránce Test můžete otestovat webovou službu jako Request Response Service (RRS) nebo službu Batch Execution (BES).

Na stránce RRS testu zobrazí globální parametry, které jste definovali pro experiment, vstupy a výstupy. K otestování webové služby, můžete ručně zadejte příslušné hodnoty pro vstupy nebo zadat soubor formátovaný hodnotu (CSV) oddělený čárkami obsahující testovací hodnoty.

Pokud chcete otestovat, pomocí RRS, v režimu zobrazení seznamu, zadejte odpovídající hodnoty pro vstupy a klikněte na **testování Request-Response**. Ve sloupci výstup na levé straně se zobrazí predikované výsledky.

![Nasazení webové služby](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

K otestování vaší BES, klikněte na tlačítko **Batch**. Na stránce testovací služby Batch v rámci váš vstup, klikněte na tlačítko Procházet a vyberte soubor CSV obsahující hodnoty odpovídající vzorku. Pokud nemáte soubor CSV a vytvoření prediktivního experimentu pomocí Machine Learning Studio, můžete stáhnout sady dat pro prediktivní experiment a používat ho.

Chcete-li stáhnout sadu dat, otevřete Machine Learning Studio. Otevřete prediktivní experiment a vstup pro experimentu klikněte pravým tlačítkem myši. V místní nabídce vyberte **datovou sadu** a pak vyberte **Stáhnout**.

![Nasazení webové služby](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Klikněte na tlačítko **Test**. Stav vaší úlohy provedení dávky služby se zobrazí vpravo pod **Test dávkových úloh**.

![Nasazení webové služby](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

Na **konfigurace** stránky, můžete změnit popis, title, aktualizujte klíč účtu úložiště a povolit ukázková data pro webovou službu.

![Konfigurovat webovou službu](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

Po nasazení webové služby, můžete:

* **Přístup** prostřednictvím rozhraní API webové služby.
* **Správa** prostřednictvím portálu Azure Machine Learning Studio webové služby.
* **Aktualizace** je-li změny modelu.

#### <a name="access-your-new-web-service"></a>Přístup k vaší nové webové služby
Po nasazení webové služby v Machine Learning Studio, můžete posílat data do služby a přijímat odpovědi prostřednictvím kódu programu.

**Využívání** stránka obsahuje všechny informace potřebné pro přístup k webové službě. Například klíč rozhraní API zajišťuje autorizovaný přístup ke službě.

Další informace o přístupu k webové službě Machine Learning Studio najdete v tématu [jak využívání služby Azure Machine Learning Studio Web](consume-web-services.md).

#### <a name="manage-your-new-web-service"></a>Správa nové webové služby
Můžete spravovat nového webovém portálu služby webových služeb Machine Learning Studio. Z [hlavní stránky portálu](https://services.azureml-test.net/), klikněte na tlačítko **webových služeb**. Z webové stránky služby můžete odstranit nebo zkopírovat služby. Chcete-li monitorovat konkrétní službu, klikněte na službu a pak klikněte na **řídicí panel**. Monitorování úloh služby batch přidružené k webové službě, klikněte na tlačítko **protokol žádostí služby Batch**.

### <a name="deploy-the-predictive-experiment-as-a-classic-web-service"></a>Prediktivní experiment nasadit jako webovou službu Classic

Teď, když prediktivní experiment dostatečně připravený, ho můžete nasadit jako webovou službu Classic Azure. Pomocí webové služby, uživatelé můžou posílat data do modelu a vrátí jeho předpovědi modelu.

Prediktivní experiment nasadit, klikněte na tlačítko **spustit** v dolní části experiment plátno a potom klikněte na tlačítko **nasadit webovou službu**. Webová služba je nastavena a jsou umístěny v řídicím panelu webové služby.

![Nasazení webové služby](./media/publish-a-machine-learning-web-service/figure-2.png)

#### <a name="test-your-classic-web-service"></a>Test webové služby Classic

Testování webové služby v portálu webových služeb Machine Learning Studio nebo Machine Learning Studio.

Chcete-li otestovat webovou službu Request Response, klikněte na tlačítko **testování** tlačítko v řídicím panelu webové služby. Dialogové okno se zobrazí na se vás zeptáme na vstupní data pro službu. Jedná se o sloupce, očekává bodovací experimentu. Zadejte sadu dat a pak klikněte na tlačítko **OK**. Výsledky generovaných webové služby se zobrazí v dolní části řídicího panelu.

Můžete kliknout **testování** náhledu odkazu k otestování služby na portálu webových služeb Azure Machine Learning Studio, jak bylo uvedeno výše v části nové webové služby.

Služba Batch Execution otestovat, kliknutím na **testování** náhledu odkazu. Na stránce testovací služby Batch v rámci váš vstup, klikněte na tlačítko Procházet a vyberte soubor CSV obsahující hodnoty odpovídající vzorku. Pokud nemáte soubor CSV a vytvoření prediktivního experimentu pomocí Machine Learning Studio, můžete stáhnout sady dat pro prediktivní experiment a používat ho.

![Test webové služby](./media/publish-a-machine-learning-web-service/figure-3.png)

Na **konfigurace** stránky, můžete změnit zobrazovaný název služby a zadejte jeho popis. Název a popis se zobrazí v [webu Azure portal](https://portal.azure.com/) kde spravujete webové služby.

Můžete zadat popis pro vstupní data, výstupních dat a webové služby parametry tak, že zadáte řetězec pro každý sloupec v části **vstupní schéma**, **schéma výstupu**, a **webové služby Parametr**. Tyto popisy se používají ve vzorku kódu dokumentaci pro webovou službu.

Můžete povolit protokolování diagnostikovat všechny chyby, které zobrazuje se vám při přístupu k webové službě. Další informace najdete v tématu [povolení protokolování pro webové služby Machine Learning Studio](web-services-logging.md).

![Konfigurovat webovou službu](./media/publish-a-machine-learning-web-service/figure-4.png)

Můžete také nakonfigurovat koncové body pro webovou službu na portálu Azure Machine Learning Web Services podobný postup uvedenému výše v části nové webové služby. Možnosti se liší, můžete přidat nebo změnit popis služby, povolte protokolování a povolit ukázková data pro účely testování.

#### <a name="access-your-classic-web-service"></a>Přístup k webové služby Classic
Po nasazení webové služby v Machine Learning Studio, můžete posílat data do služby a přijímat odpovědi prostřednictvím kódu programu.

Řídicí panel obsahuje všechny informace, které potřebujete pro přístup k webové službě. Například klíč rozhraní API zajišťuje autorizovaný přístup ke službě a jsou k dispozici stránek nápovědy rozhraní API pomáhají začít psát kód.

Další informace o přístupu k webové službě Machine Learning Studio najdete v tématu [jak využívání služby Azure Machine Learning Studio Web](consume-web-services.md).

#### <a name="manage-your-classic-web-service"></a>Správa webové služby Classic
Existují různé akce, které můžete provádět monitorování webové služby. Můžete ho aktualizovat a odstranit. Můžete také přidat další koncové body pro webové služby Classic kromě výchozí koncový bod, který je vytvořen při nasazování.

Další informace najdete v tématu [Správa pracovního prostoru Azure Machine Learning Studio](manage-workspace.md) a [Správa webové služby pomocí portálu webových služeb Azure Machine Learning Studio](manage-new-webservice.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning Studio web service endpoints using the REST API, see **Azure Machine Learning Studio web service endpoints**.
-->

## <a name="update-the-web-service"></a>Aktualizovat webovou službu
Můžete provést změny webové služby, jako je aktualizace modelu s další trénovacích dat a nasadit ho znovu, přepíše původní webové služby.

Pokud chcete aktualizovat webovou službu, otevřete původní prediktivní experiment jste použili k nasazení webové služby a vytvořili upravit kopii kliknutím **uložit jako**. Proveďte požadované změny a pak klikněte na tlačítko **nasadit webovou službu**.

Vzhledem k tomu, že jste nasadili před tento experiment, zobrazí se výzva, pokud chcete přepsat (klasická webová služba) nebo aktualizovat existující službu (nové webové služby). Kliknutím na **Ano** nebo **aktualizace** zastaví existující webovou službu a nasadí nový prediktivní experiment je nasazený na příslušné místo.

> [!NOTE]
> Pokud jste provedli změny konfigurace v původní webové služby, například zadáte nový zobrazovaný název nebo popis, je potřeba znovu zadat tyto hodnoty.

Jednou z možností pro aktualizaci webové služby je přeučování modelu. Další informace najdete v tématu [modelů Přeučování Machine Learning Studio](retrain-models-programmatically.md).

## <a name="next-steps"></a>Další postup

* Další technické podrobnosti o tom, jak nasazení funguje, najdete v části [jak Machine Learning Studio modelu postupuje od experimentu zprovozněné webové službě](model-progression-experiment-to-web-service.md).

* Podrobnosti o tom, jak připravit nasazení modelu najdete v tématu [přípravu modelu pro nasazení v Azure Machine Learning Studio](convert-training-experiment-to-scoring-experiment.md).

* Existuje několik způsobů, jak využít REST API a komunikovat s webovou službou. Zobrazit [jak využívání webové služby Azure Machine Learning Studio](consume-web-services.md).


<!-- internal links -->
[Vytvořit výukový experiment]: #create-a-training-experiment
[Převeďte jej na prediktivní experiment]: #convert-the-training-experiment-to-a-predictive-experiment
[Ho nasadit jako webovou službu]: #deploy-it-as-a-web-service
[Nový]: #deploy-the-predictive-experiment-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service
