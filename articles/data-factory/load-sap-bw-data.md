---
title: Načtení dat z obchodního skladu SAP
description: Kopírování dat z obchodního skladu SAP (BW) pomocí Azure Data Factory
services: data-factory
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/22/2019
ms.openlocfilehash: 971871c28bd1b38b134c04b0334fbe99d1d655c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440151"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Kopírování dat z obchodního skladu SAP pomocí Azure Data Factory

Tento článek ukazuje, jak pomocí Azure Data Factory kopírovat data z SAP Business Warehouse (BW) přes Open Hub do Azure Data Lake Storage Gen2. Podobný proces můžete použít ke kopírování dat do jiných [podporovaných úložišť dat jímky](copy-activity-overview.md#supported-data-stores-and-formats).

> [!TIP]
> Obecné informace o kopírování dat z SAP BW, včetně integrace SAP BW Open Hub a toku delta extrakce, najdete [v tématu Kopírování dat z OBCHODNÍHO SKLADU SAP přes Open Hub pomocí Azure Data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Požadavky

- **Azure Data Factory**: Pokud ho nemáte, vytvořte [datovou továrnu](quickstart-create-data-factory-portal.md#create-a-data-factory)podle pokynů .

- **SAP BW Open Hub Destination (OHD) s cílovým typem "Tabulka databáze"**: Chcete-li vytvořit OHD nebo zkontrolovat, zda je váš OHD správně nakonfigurován pro integraci data factory, naleznete v tomto článku v části [Konfigurace SAP BW Open Hub Destination.](#sap-bw-open-hub-destination-configurations)

- **Uživatel SAP BW potřebuje následující oprávnění**:

  - Autorizace pro volání vzdálených funkcí (RFC) a SAP BW.
  - Oprávnění k aktivitě "Spustit" **objektu autorizace S_SDSAUTH.**

- **Runtime [integrace s vlastním hostitelem (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) s konektorem SAP .NET 3.0**. Postupujte podle následujících kroků nastavení:

  1. Nainstalujte a zaregistrujte runtime integrace s vlastním hostitelem verze 3.13 nebo novější. (Tento proces je popsán dále v tomto článku.)

  2. Stáhněte si [64bitový konektor SAP pro microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) z webu SAP a nainstalujte jej do stejného počítače jako infračervený přenos hostovaný samostatně. Během instalace se ujistěte, že jste v dialogovém okně **Volitelné kroky nastavení** vybrali možnost Instalovat sestavení do **gac,** jak ukazuje následující obrázek:

     ![Nastavení dialogového okna Konektor SAP .NET](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Proveďte úplnou kopii z otevřeného rozbočovače SAP BW

Na webu Azure Portal přejděte k vaší datové továrně. Vyberte **Author & Monitor,** chcete-li otevřít ui datové továrny na samostatné kartě.

1. Na stránce **Začínáme** vyberte **Kopírovat data,** abyste otevřeli nástroj Kopírovat data.

2. Na stránce **Vlastnosti** zadejte **název úkolu**a pak vyberte **Další**.

3. Na stránce **Zdrojové úložiště dat** vyberte **+Vytvořit nové připojení**. V galerii konektorů vyberte **SAP BW Open Hub** a pak vyberte **Pokračovat**. Chcete-li filtrovat konektory, můžete do vyhledávacího pole zadat **SAP.**

4. Na stránce **Určení připojení otevřeného rozbočovače SAP BW** vytvořte nové připojení následujícím postupem.

   ![Vytvořit stránku služby propojené s otevřeným centrem SAP BW](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Ze seznamu **Připojit prostřednictvím integračního běhu** vyberte existující infračervený přenos s vlastním hostitelem. Nebo si ho vytvořte, pokud ho ještě nemáte.

      Chcete-li vytvořit novou infračervené infračervené odpovězenou infračervené vybavenou infračerveným přenosem, vyberte **možnost +Nový**a pak vyberte **možnost Vlastní hostované**. Zadejte **název**a pak vyberte **Další**. Vyberte **Expresní nastavení,** které chcete nainstalovat do aktuálního počítače, nebo postupujte podle pokynů **pro ruční nastavení,** které jsou k dispozici.

      Jak je uvedeno v [části Požadavky](#prerequisites), ujistěte se, že máte sap konektor pro Microsoft .NET 3.0 nainstalován ve stejném počítači, kde je spuštěna samoobslužná infračervená zkušená infračervená.

   2. Vyplňte **název serveru**SAP BW , **systémové číslo**, **ID klienta,** **jazyk** (pokud je jiný než **EN),** **uživatelské jméno**a **heslo**.

   3. Chcete-li ověřit nastavení, vyberte **Testovat připojení** a pak vyberte **Dokončit**.

   4. Je vytvořeno nové připojení. Vyberte **další**.

5. Na stránce **Vybrat cíle otevření rozbočovače** projděte cíle otevřených rozbočovačů, které jsou dostupné ve vašem SAP BW. Vyberte OHD, ze kterých chcete kopírovat data, a pak vyberte **Další**.

   ![Vybrat cílovou tabulku otevřeného rozbočovače SAP BW](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Zadejte filtr, pokud ho potřebujete. Pokud váš OHD obsahuje pouze data z jednoho procesu přenosu dat (DTP) spuštění s jedním ID požadavku, nebo jste si jisti, že vaše DTP je dokončena a chcete zkopírovat data, zrušte zaškrtnutí políčka **Vyloučit poslední požadavek.**

   Další informace o těchto nastaveních najdete v tomto článku v části [Konfigurace sap BW Open Hub Destination.](#sap-bw-open-hub-destination-configurations) Chcete-li zkontrolovat, jaká data budou vrácena, vyberte **možnost Ověřit.** Pak vyberte **Další**.

   ![Konfigurace filtru otevřeného rozbočovače SAP BW](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Na stránce **Cílové úložiště dat** vyberte **+Vytvořit nové připojení** > **Azure Data Lake Storage Gen2** > **Continue**.

8. Na stránce **Určení připojení úložiště datových jezer Azure** vytvořte připojení následujícím postupem.

   ![Vytvoření stránky propojené služby ADLS Gen2](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. V rozevíracím seznamu **Název** vyberte účet podporující úložiště datového jezera Gen2.
   2. Vytvořte připojení výběrem možnosti **Dokončit**. Pak vyberte **Další**.

9. Na stránce **Zvolte výstupní soubor nebo složku** zadejte **copyfromopenhub** jako název výstupní složky. Pak vyberte **Další**.

   ![Zvolte stránku výstupní složky](media/load-sap-bw-data/choose-output-folder.png)

10. Na stránce **Nastavení formátu souboru** vyberte **Další,** chcete-li použít výchozí nastavení.

    ![Určení stránky formátu jímky](media/load-sap-bw-data/specify-sink-format.png)

11. Na stránce **Nastavení** rozbalte **položku Nastavení výkonu**. Zadejte hodnotu **pro Stupeň paralelismu kopírování, například** 5 pro paralelní načítání z SAP BW. Pak vyberte **Další**.

    ![Konfigurace nastavení kopírování](media/load-sap-bw-data/configure-copy-settings.png)

12. Na stránce **Souhrn** zkontrolujte nastavení. Pak vyberte **Další**.

13. Na stránce **Nasazení** vyberte **Sledovat,** chcete-li sledovat kanál.

    ![Stránka Nasazení](media/load-sap-bw-data/deployment.png)

14. Všimněte si, že je automaticky vybrána karta **Sledování** na levé straně stránky. Sloupec **Akce** obsahuje odkazy na zobrazení podrobností o spuštění aktivity a opětovné spuštění kanálu.

    ![Zobrazení monitorování kanálu](media/load-sap-bw-data/pipeline-monitoring.png)

15. Chcete-li zobrazit spuštění aktivity, která jsou přidružena ke spuštění kanálu, vyberte **možnost Zobrazit spuštění aktivity** ve sloupci **Akce.** Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Chcete-li přepnout zpět do zobrazení potrubí, vyberte odkaz **Potrubí** nahoře. Seznam můžete aktualizovat kliknutím na **Aktualizovat**.

    ![Obrazovka sledování aktivity](media/load-sap-bw-data/activity-monitoring.png)

16. Chcete-li sledovat podrobnosti spuštění pro každou aktivitu kopírování, vyberte odkaz **Podrobnosti,** což je ikona brýlí pod **položkou Akce** v zobrazení sledování aktivity. Mezi dostupné podrobnosti patří svazek dat zkopírovaný ze zdroje do jímky, propustnost dat, kroky spuštění a doba trvání a použité konfigurace.

    ![Podrobnosti sledování aktivity](media/load-sap-bw-data/activity-monitoring-details.png)

17. Chcete-li zobrazit **maximální ID požadavku**, vraťte se do zobrazení sledování aktivity a vyberte **výstup** v části **Akce**.

    ![Obrazovka výstupu aktivity](media/load-sap-bw-data/activity-output.png)

    ![Zobrazení podrobností výstupu aktivity](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Přírůstková kopie z otevřeného rozbočovače SAP BW

> [!TIP]
> Viz [SAP BW Open Hub konektor delta extrakce toku](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) se dozvíte, jak SAP BW Open Hub konektor v Datové factory kopíruje přírůstková data z SAP BW. Tento článek vám také může pomoci pochopit základní konfiguraci konektoru.

Nyní pokračujme v konfiguraci přírůstkové kopie z otevřeného rozbočovače SAP BW.

Přírůstková kopie používá mechanismus "high-vodoznak", který je založen na **ID požadavku**. Toto ID je automaticky generováno v SAP BW Open Hub Destination dtp. Následující diagram znázorňuje tento pracovní postup:

![Vývojový diagram pracovního postupu přírůstkové](media/load-sap-bw-data/incremental-copy-workflow.png)

Na stránce Data Factory **Let's Get's Get's Started** page vyberte **Create pipeline from template,** aby se použila předdefinovaná šablona.

1. Vyhledejte **SAP BW** a vyhledejte a vyberte **přírůstkovou kopii z SAP BW do šablony Azure Data Lake Storage Gen2.** Tato šablona zkopíruje data do Azure Data Lake Storage Gen2. Podobný pracovní postup můžete použít ke kopírování do jiných typů jímky.

2. Na hlavní stránce šablony vyberte nebo vytvořte následující tři připojení a pak vyberte **Použít tuto šablonu** v pravém dolním rohu okna.

   - **Úložiště objektů blob Azure**: V tomto návodu používáme úložiště objektů Blob Azure k uložení vysokého vodoznaku, což je *maximální zkopírované ID požadavku*.
   - **SAP BW Open Hub:** Toto je zdroj pro kopírování dat z. Podrobné informace o konfiguraci naleznete v předchozím úplném návodu.
   - **Azure Data Lake Storage Gen2**: Toto je jímka ke kopírování dat. Podrobné informace o konfiguraci naleznete v předchozím úplném návodu.

   ![Přírůstková kopie ze šablony SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Tato šablona generuje kanál s následujícími třemi aktivitami a umožňuje jejich zřetězení na úspěch: *Vyhledávání*, *Kopírovat data*a *Web*.

   Přejděte na kartu **Parametry** kanálu. Zobrazí se všechny konfigurace, které je třeba zadat.

   ![Přírůstková kopie z konfigurace SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Zadejte název tabulky Otevřít hub, ze které chcete kopírovat data.

   - **Data_Destination_Container**: Zadejte cílový kontejner Azure Data Lake Storage Gen2, do kterých chcete kopírovat data. Pokud kontejner neexistuje, aktivita kopírování data factory vytvoří jeden během provádění.
  
   - **Data_Destination_Directory**: Zadejte cestu ke složce pod kontejnerem Azure Data Lake Storage Gen2, do které chcete kopírovat data. Pokud cesta neexistuje, aktivita kopírování Data Factory vytvoří cestu během provádění.
  
   - **HighWatermarkBlobContainer**: Zadejte kontejner pro uložení hodnoty vysokého vodoznaku.

   - **HighWatermarkBlobDirectory**: Zadejte cestu ke složce pod kontejnerem pro uložení hodnoty vysokého vodoznaku.

   - **HighWatermarkBlobName**: Zadejte název objektu blob pro `requestIdCache.txt`uložení hodnoty vysokého vodoznaku, například . V úložišti objektů Blob přejděte na odpovídající cestu HighWatermarkBlobContainer+HighWatermarkBlobDirectory+HighWatermarkBlobName, například *container/path/requestIdCache.txt*. Vytvořte objekt blob s obsahem 0.

      ![Obsah objektu blob](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: V této šabloně používáme WebActivity k volání Azure Logic Apps nastavit hodnotu vysokého vodoznaku v úložišti objektů Blob. Nebo můžete použít Azure SQL Database k jeho uložení. K aktualizaci hodnoty použijte aktivitu uložené procedury.

      Musíte nejprve vytvořit aplikaci logiky, jak ukazuje následující obrázek. Potom vložte do **adresy URL HTTP POST**.

      ![Konfigurace aplikace Logika](media/load-sap-bw-data/logic-app-config.png)

      1. Přejděte na web Azure Portal. Vyberte novou službu **Logic Apps.** Výběrem **možnosti +Prázdná aplikace logiky** přejdete do **Návrháře logických aplikací**.

      2. Vytvořte aktivační událost **Při přijetí požadavku HTTP**. Zadejte tělo požadavku HTTP takto:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. Přidejte akci **Vytvořit objekt blob.** Pro **cestu složky** a **název objektu blob**použijte stejné hodnoty, které jste nakonfigurovali dříve v *highwatermarkblobcontainer+HighWatermarkBlobDirectory* a *HighWatermarkBlobName*.

      4. Vyberte **Uložit**. Potom zkopírujte hodnotu **adresy URL HTTP POST** pro použití v kanálu Data Factory.

4. Po zadání parametrů kanálu datové továrny vyberte **ladění** > **dokončit** vyvolat spustit k ověření konfigurace. Nebo vyberte **Publikovat,** chcete-li publikovat všechny změny, a pak vyberte **Přidat aktivační událost,** chcete-li spustit.

## <a name="sap-bw-open-hub-destination-configurations"></a>Konfigurace sap BW Open Hub Cíl

Tato část představuje konfiguraci na straně SAP BW pro použití konektoru SAP BW Open Hub v datové továrně ke kopírování dat.

### <a name="configure-delta-extraction-in-sap-bw"></a>Konfigurace delta extrakce v SAP BW

Pokud potřebujete historické kopie a přírůstkové kopie nebo pouze přírůstkové kopie, nakonfigurujte delta extrakce v SAP BW.

1. Vytvořte cíl otevřeného centra. OhD můžete vytvořit v SAP Transaction RSA1, který automaticky vytvoří požadovaný proces transformace a přenosu dat. Použijte následující nastavení:

   - **ObjectType**: Můžete použít libovolný typ objektu. Zde používáme **InfoCube** jako příklad.
   - **Typ cíle**: Vyberte **databázovou tabulku**.
   - **Klíč tabulky**: Vyberte **technický klíč**.
   - **Extrakce**: Vyberte **zachovat data a vložit záznamy do tabulky**.

   ![Dialogové okno Vytvořit sap BW OHD delta extrakce](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Dialogové okno Vytvořit SAP BW OHD delta2 extrakce](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Můžete zvýšit počet paralelních spuštěných pracovních procesů SAP pro DTP:

   ![vytvořit-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Naplánujte DTP v procesních řetězcích.

   Rozdílový DTP pro datovou krychli funguje pouze v případě, že potřebné řádky nebyly komprimovány. Ujistěte se, že komprese krychle BW není spuštěna před DTP do tabulky Otevřít hub. Nejjednodušší způsob, jak to udělat, je integrovat DTP do stávajících procesních řetězců. V následujícím příkladu dtp (na OHD) je vložen do řetězce procesu mezi *Adjust* (souhrnně kumulativní) a *Sbalit* (komprese krychle) kroky.

   ![Vytvoření vývojového diagramu procesního řetězce SAP BW](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Konfigurace úplného vytažení v SAP BW

Kromě delta extrakce můžete chtít úplnou extrakci stejného SAP BW InfoProvider. To obvykle platí, pokud chcete provést úplnou kopii, ale ne přírůstkovou, nebo chcete [resync delta extrakce](#resync-delta-extraction).

Nemůžete mít více než jeden DTP pro stejné OHD. Takže musíte vytvořit další OHD před delta extrakce.

![Vytvořit SAP BW OHD plné](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Pro plné zatížení OHD zvolte jiné možnosti než pro delta extrakci:

- V OHD: Nastavte možnost **Extrakce** na **odstranění dat a vložení záznamů**. V opačném případě budou data extrahována mnohokrát při opakování DTP v řetězci procesu BW.

- V DTP: Nastavte **režim extrakce** na **plnou**. Automaticky vytvořený DTP je nutné změnit z **delta** na **plný** ihned po vytvoření OHD, jak ukazuje tento obrázek:

   ![Vytvořit dialogové okno SAP BW OHD nakonfigurované pro úplné extrakci](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- V konektoru BW Open Hub v Factory dat: Vypněte **vyloučit poslední požadavek**. V opačném případě nebude nic extrahováno.

Obvykle spustit úplné DTP ručně. Nebo můžete vytvořit řetězec procesu pro úplné DTP. Obvykle se jedná o samostatný řetězec, který je nezávislý na stávajících procesních řetězcích. V obou případech *se ujistěte, že dtp je dokončena před zahájením extrakce pomocí kopírování Data Factory*. V opačném případě budou zkopírována pouze částečná data.

### <a name="run-delta-extraction-the-first-time"></a>Spustit delta extrakce poprvé

První delta extrakce je technicky *úplná extrakce*. Ve výchozím nastavení konektor SAP BW Open Hub vyloučí poslední požadavek při kopírování dat. Pro první delta extrakce žádná data jsou extrahovány aktivity kopírování Data Factory, dokud následující DTP generuje rozdílová data v tabulce se samostatným ID požadavku. Existují dva způsoby, jak se tomuto scénáři vyhnout:

- Vypněte možnost **Vyloučit poslední požadavek** pro první extrakci delta. Ujistěte se, že první delta DTP je dokončena před zahájením extrakce delta poprvé.
-  Použijte postup pro resynchronizaci extrakce delta, jak je popsáno v další části.

### <a name="resync-delta-extraction"></a>Resynchronizace delta extrakce

Následující scénáře změnit data v datové krychle SAP BW, ale nejsou považovány za delta DTP:

- Selektivní odstranění SAP BW (řádků pomocí jakékoli podmínky filtru)
- Smazání požadavku SAP BW (chybných požadavků)

Sap Open Hub Cíl není cíl dat řízený data mart (ve všech balíčcích podpory SAP BW od 2015). Takže můžete odstranit data z datové krychle bez změny dat v OHD. Potom je nutné znovu synchronizovat data datové krychle s datovou továrně:

1. Spusťte úplnou extrakci v datové továrně (pomocí úplného DTP v SAP).
2. Odstraňte všechny řádky v tabulce Otevřít rozbočovač pro rozdílový DTP.
3. Nastavte stav rozdílového dtp na **načteno**.

Poté všechny následné delta DTPs a data factory delta extrakce fungovat podle očekávání.

Chcete-li nastavit stav rozdílového dtp na **načtený**, můžete použít následující možnost pro ruční spuštění delta DTP:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>Další kroky

Další informace o podpoře konektoru SAP BW Open Hub:

> [!div class="nextstepaction"]
>[Konektor otevřeného rozbočovače SAP Business Warehouse](connector-sap-business-warehouse-open-hub.md)
