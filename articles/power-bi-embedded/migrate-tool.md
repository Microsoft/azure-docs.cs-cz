---
title: Použijte nástroj pro migraci Power BI Embedded | Microsoft Docs
description: Power BI Embedded nástroj pro migraci můžete použít pro kopírování sestavy z kolekce pracovních prostorů Power BI do Power BI Embedded.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: maghan
ms.openlocfilehash: 4f76b1efb509745653bfde0926f56032030f7d47
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31419897"
---
# <a name="use-the-power-bi-embedded-migration-tool"></a>Použijte nástroj pro migraci Power BI Embedded

Power BI Embedded nástroj pro migraci můžete použít pro kopírování sestavy z kolekce pracovních prostorů Power BI do Power BI Embedded.

Migraci obsahu z kolekce prostoru pro Power BI služba lze provést v paralelní do aktuální řešení a nevyžaduje žádné výpadky.

## <a name="limitations"></a>Omezení

* Stisknutí datové sady nelze stáhnout a bude nutné znovu vytvořit pomocí Power BI REST API pro službu Power BI.
* Než 26 od listopadu 2016 nebude ke stažení importovat soubor PBIX soubory.

## <a name="download"></a>Ke stažení

Si můžete stáhnout ukázkový nástroj pro migraci z [Githubu](https://github.com/Microsoft/powerbi-migration-sample). Můžete buď stáhnout zip úložiště, nebo ji můžete místně klonovat. Po stažení, můžete otevřít *powerbi. migrace sample.sln* v sadě Visual Studio sestavíte a spustíte nástroj pro migraci.

## <a name="migration-plans"></a>Plány migrace

Váš plán migrace je jenom metadata, která katalogizuje obsah v rámci Power BI prostoru kolekce a jak chcete publikovat je do Power BI Embedded.

### <a name="start-with-a-new-migration-plan"></a>Začněte nový plán migrace

Plánování migrace je metadata položky k dispozici v Power BI prostoru kolekce, kterou pak chcete přesunout do Power BI Embedded. Plánování migrace je uložený jako soubor XML.

Můžete začít tím, že vytváření nového plánu migrace. Pokud chcete vytvořit nový plán migrace, postupujte takto.

1. Vyberte **soubor** > **nový plán migrace**.

    ![Nový plán migrace](media/migrate-tool/migrate-tool-plan.png)

2. V **vybrat skupinu prostředků pro kolekce Power BI prostoru** dialogové okno, můžete vybrat **prostředí** rozevírací seznam a vyberte produkčního.

3. Zobrazí se výzva k přihlášení. Budete používat vaše předplatné Azure přihlášení.

    > [!IMPORTANT]
    > Toto je **není** organizace účtu služeb Office 365, který přihlášení do Power BI s.

4. Vyberte předplatné Azure, která ukládá prostředku kolekce pracovních prostorů Power BI.

    ![Vyberte předplatné Azure](media/migrate-tool/migrate-tool-select-resource-group.png)

5. Pod seznamem předplatné, vyberte **skupiny prostředků** obsahující pracovní prostor kolekcí a vyberte **vyberte**.

    ![Vyberte skupinu prostředků.](media/migrate-tool/migrate-tool-select-resource-group2.png)

6. Vyberte **analyzovat**. Tím získáte inventář položek v rámci vašeho předplatného Azure můžete zahájit plán.

    ![Kliknutím na tlačítko Analýza](media/migrate-tool/migrate-tool-analyze-group.png)

    > [!NOTE]
    > Analýza proces může trvat několik minut v závislosti na počtu prostoru kolekce a kolik obsah existuje v kolekci pracovních prostorů.

7. Když **analyzovat** je dokončena, vás vyzve k uložení plánu migrace.

V tomto okamžiku jste se připojili váš plán migrace vašeho předplatného Azure. Čtení níže na lépe pochopit tok jak pracovat s váš plán migrace. To zahrnuje analyzovat & plánování migrace, stahování, vytvořit skupiny a nahrávání.

### <a name="save-your-migration-plan"></a>Uložení plánu migrace

Můžete uložit váš plán migrace pro pozdější použití. Tím se vytvoří soubor XML, který všechny informace součástí plánu migrace.

Pokud chcete uložit váš plán migrace, postupujte takto.

1. Vyberte **soubor** > **uložit s plánem migrace**.

    ![Migrace plánu položku Uložit](media/migrate-tool/migrate-tool-save-plan.png)

2. Zadejte název souboru nebo použijte název vygenerovaný soubor a vyberte **Uložit**.

### <a name="open-an-existing-migration-plan"></a>Otevření existujícího plánu migrace

Můžete otevřít uložené plánu na pokračovat v práci na migraci.

Chcete-li otevřít váš stávající plán migrace, postupujte takto.

1. Vyberte **soubor** > **otevřete existující plán migrace**.

    ![Otevřete existující možnost nabídky plánu migrace](media/migrate-tool/migrate-tool-open-plan.png)

2. Vyberte souboru migrace a vyberte **otevřete**.

## <a name="step-1-analyze-and-plan-migration"></a>Krok 1: Analýza a plánování migrace

**Analyzovat & plánování migrace** karta nabízí zobrazení co je aktuálně ve skupině prostředků předplatného Azure.

![Analýza a plánování migrace karta](media/migrate-tool/migrate-tool-step1.png)

Se podíváme *SampleResourceGroup* jako příklad.

### <a name="paas-topology"></a>PaaS topologie

Toto je seznam vaše *skupina prostředků > kolekce prostoru > pracovní prostory*. Kolekce prostředků skupiny a pracovního prostoru se zobrazí popisný název. Pracovní prostory zobrazí identifikátor GUID.

Položky v seznamu se zobrazí také barvy a číslo ve formátu (# nebo #). To znamená počet sestav, které lze stáhnout.

Černé barvy znamená, že všechny sestavy si můžete stáhnout. Červená barva znamená, že některé sestavy nelze stáhnout. Číslo levém označí celkový počet sestav, které lze stáhnout. Číslo na pravé straně udává celkový počet sestav v rámci seskupení.

Můžete vybrat položky v rámci topologie PaaS k zobrazení sestavy v části sestavy.

### <a name="reports"></a>Reports

Části sestavy se zobrazí seznam si dostupné sestavy a určuje, jestli ho můžete stáhnout nebo ne.

![Seznam sestav v rámci kolekce pracovních prostorů Power BI](media/migrate-tool/migrate-tool-analyze-reports.png)

### <a name="target-structure"></a>Cílová struktura

**Cíle struktura** je, kde se zjistit nástroj co kde bude stažen do a jak je odeslat.

#### <a name="download-plan"></a>Stáhněte si plán

Cestu automaticky vytvoří za vás. Pokud chcete, můžete změnit tato cesta. Pokud změníte cestu, budete muset vybrat **aktualizovat cesty**.

**To není ve skutečnosti má stažení provést.** Toto je pouze určujete strukturu kde sestavy bude stažen do.

#### <a name="upload-plan"></a>Nahrát plán

Sem můžete zadat předponu, který se má použít pro aplikaci pracovní prostory, které budou vytvořeny v rámci služby Power BI. Po předponu bude identifikátor GUID pro pracovní prostor, které existovalo v Azure.

![Zadejte předponu názvu skupiny](media/migrate-tool/migrate-tool-upload-plan.png)

**To skutečně nevytváří skupiny v rámci služby Power BI.** Pouze definuje strukturu pojmenování pro skupiny.

Pokud změníte předponu, budete muset vybrat **generovat nahrát plánování**.

Můžete klikněte pravým tlačítkem na skupinu a vyberte přejmenujete skupinu v plánu nahrávání přímo, v případě potřeby.

![Přejmenování skupiny možnost místní nabídky](media/migrate-tool/migrate-tool-upload-report-rename-item.png)

> [!NOTE]
> Název *skupiny* nesmí obsahovat mezery nebo neplatné znaky.

## <a name="step-2-download"></a>Krok 2: stáhnout

Na **Stáhnout** karty, zobrazí se seznam sestav a přidružených metadat. Zobrazí stav exportu je spolu s předchozí stav exportu.

![Stáhněte si karta](media/migrate-tool/migrate-tool-download-tab.png)

Máte dvě možnosti.

* Vyberte konkrétní sestavy a vyberte **stáhnout vybrané**
* Vyberte **stáhnete všechny**.

![Stáhnout vybrané tlačítko](media/migrate-tool/migrate-tool-download-options.png)

Pro úspěšné stahování, zobrazí se stav *provádí* a zda existuje soubor PBIX bude odrážet.

Po dokončení stahování, vyberte **vytváření skupin** kartě.

## <a name="step-3-create-groups"></a>Krok 3: vytvoření skupiny

Po stažení sestavy, které jsou k dispozici, můžete přejít na **vytváření skupin** kartě. Na této kartě se vytvoří pracovní prostory aplikace v rámci služby Power BI na základě plánu migrace, kterou jste vytvořili. Vytvoří pracovní prostor aplikace s názvem, který jste zadali na **nahrát** kartě v rámci **analyzovat & plánování migrace**.

![Vytvoření skupiny](media/migrate-tool/migrate-tool-create-groups.png)

Chcete-li vytvořit pracovní prostory aplikace, můžete vybrat buď **vytvořit vybrané skupiny** nebo **vytvořit všechny chybějící skupiny**.

Když vyberete jednu z těchto možností, budete vyzváni k přihlášení. *Budete chtít použijte svoje přihlašovací údaje pro službu Power BI, který chcete vytvořit pracovní prostory aplikace na.*

![Přihlašovací obrazovka, Power BI](media/migrate-tool/migrate-tool-create-group-sign-in.png)

Tím se vytvoří pracovní prostor aplikace v rámci služby Power BI. Není to nahrajte sestavy do pracovního prostoru aplikace.

Můžete ověřit, zda byl vytvořen pracovní prostor aplikace přihlášení k Power BI a ověření, zda existuje pracovním prostoru. Si všimnete, že žádná data, je v pracovním prostoru.

![Pracovní prostor aplikace v rámci služby Power BI](media/migrate-tool/migrate-tool-app-workspace.png)

Po vytvoření pracovního prostoru můžete přesunout do **nahrát** kartě.

## <a name="step-4-upload"></a>Krok 4: nahrání

Na **nahrát** kartě, odešlete sestavy ve službě Power BI. Zobrazí se seznam sestav, které jsme stáhli na kartě stažení společně s Cílová skupina podle plánu migrace.

![Nahrát karta](media/migrate-tool/migrate-tool-upload-tab.png)

Můžete nahrát vybrané sestavy, nebo může odeslat všechny sestavy. Můžete taky resetovat stav nahrávání znovu odeslat položky.

Máte také možnost výběru co dělat, když sestava se stejným názvem existuje. Můžete si vybrat mezi **Abort**, **Ignorovat** a **přepsat**.

![Rozevíracího seznamu možnost pro co dělat, pokud existuje sestavy](media/migrate-tool/migrate-tool-upload-report-same-name.png)

![Nahrát vybraných výsledků](media/migrate-tool/migrate-tool-upload-selected.png)

### <a name="duplicate-report-names"></a>Duplicitní názvy sestavy

Pokud máte sestavu, která má stejný název, ale vy víte, že je různé sestavy, budete muset změnit **TargetName** sestavy. Můžete změnit název tak, že ručně upravíte plán migrace XML.

Musíte se zavřete Nástroj pro migraci na změnu provést a pak znovu otevřete nástroj a plán migrace.

Ve výše uvedeném příkladu jeden z klonovaného sestavy selhání oznamující, že existovaly sestavy se stejným názvem. Pokud jsme přejít, podívejte se na migraci plánu XML, jsme se zobrazí následující.

```
<ReportMigrationData>
    <PaaSWorkspaceCollectionName>SampleWorkspaceCollection</PaaSWorkspaceCollectionName>
    <PaaSWorkspaceId>4c04147b-d8fc-478b-8dcb-bcf687149823</PaaSWorkspaceId>
    <PaaSReportId>525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe</PaaSReportId>
    <PaaSReportLastImportTime>1/3/2017 2:10:19 PM</PaaSReportLastImportTime>
    <PaaSReportName>cloned</PaaSReportName>
    <IsPushDataset>false</IsPushDataset>
    <IsBoundToOldDataset>false</IsBoundToOldDataset>
    <PbixPath>C:\MigrationData\SampleResourceGroup\SampleWorkspaceCollection\4c04147b-d8fc-478b-8dcb-bcf687149823\cloned-525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe.pbix</PbixPath>
    <ExportState>Done</ExportState>
    <LastExportStatus>OK</LastExportStatus>
    <SaaSTargetGroupName>SampleMigrate</SaaSTargetGroupName>
    <SaaSTargetGroupId>6da6f072-0135-4e6c-bc92-0886d8aeb79d</SaaSTargetGroupId>
    <SaaSTargetReportName>cloned</SaaSTargetReportName>
    <SaaSImportState>Failed</SaaSImportState>
    <SaaSImportError>Report with the same name already exists</SaaSImportError>
</ReportMigrationData>
```

Pro položku se nezdařila jsme můžete změnit název SaaSTargetReportName.

```
<SaaSTargetReportName>cloned2</SaaSTargetReportName>
```

Pak můžeme znovu otevřít plán, nástroje pro migraci a nahrajte sestavy se nezdařilo.

Po návratu do Power BI, bychom vidět, že sestavy a datové sady byly odeslány v pracovním prostoru aplikace.

![Seznam sestav v rámci služby Power BI](media/migrate-tool/migrate-tool-upload-app-workspace.png)

<a name="upload-local-file"></a>
### <a name="upload-a-local-pbix-file"></a>Uložte místní soubor PBIX

Můžete nahrát místní verzi souboru Power BI Desktop. Budete muset ukončete nástroj, upravte soubor XML a uvést úplnou cestu k vaší místní soubor PBIX v **PbixPath** vlastnost.

```
<PbixPath>[Full Path to PBIX file]</PbixPath>
```

Jakmile upravíte soubor xml, znovu ho otevřete plánu v rámci nástroj pro migraci a nahrajte sestavy.

<a name="directquery-reports"></a>
### <a name="directquery-reports"></a>DirectQuery sestavy

Budete muset aktualizovat aktualizovat připojovací řetězec pro sestavy DirectQuery. To lze provést v rámci *powerbi.com*, nebo prostřednictvím kódu programu se můžete dotazovat připojovacího řetězce z Power BI Embedded (Paas). Příklad, naleznete v části [extrahovat DirectQuery připojovací řetězec ze sestavy PaaS](migrate-code-snippets.md#extract-directquery-connection-string-from-power-bi-workspace-collections).

Potom můžete aktualizovat připojovací řetězec pro datovou sadu v rámci služby Power BI a nastavte přihlašovací údaje pro zdroj dat. Můžete si prohlédnout následující příklady chcete zjistit, jak to udělat.

* [Aktualizovat DirectQuery připojovací řetězec v Power BI Embedded](migrate-code-snippets.md#update-directquery-connection-string-in-power-bi-embedded)
* [Nastavení přihlašovacích údajů DirectQuery v Power BI Embedded](migrate-code-snippets.md#set-directquery-credentials-in-power-bi-embedded)

## <a name="next-steps"></a>Další postup

Teď, když sestavy byly migrovány z kolekce pracovních prostorů Power BI pro Power BI Embedded, teď můžete aktualizovat aplikace a začít vložení sestavy do tohoto pracovního prostoru aplikace.

Další informace najdete v tématu [jak migrovat obsah kolekce pracovních prostorů Power BI pro Power BI Embedded](migrate-from-power-bi-workspace-collections.md).

Chcete se ještě na něco zeptat? [Zkuste požádat komunitě Power BI](http://community.powerbi.com/)