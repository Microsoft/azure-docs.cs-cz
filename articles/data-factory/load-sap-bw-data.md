---
title: Načtení dat ze SAP Business Warehouse s využitím Azure Data Factory | Dokumentace Microsoftu
description: Použití Azure Data Factory pro kopírování dat z SAP Business Warehouse (BW)
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: jingwang
ms.openlocfilehash: 9458903378576a50db9be92b9377987829e1ba41
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200153"
---
# <a name="load-data-from-sap-business-warehouse-bw-by-using-azure-data-factory"></a>Načtení dat ze SAP Business Warehouse (BW) pomocí služby Azure Data Factory

Tento článek popisuje návod na použití služby Data Factory _načtení dat ze SAP Business Warehouse (BW) prostřednictvím Open centra do Azure Data Lake Storage Gen2_. Můžete postupovat podle podobných kroků ke zkopírování dat do jiných [podporovaná úložiště dat jímky](copy-activity-overview.md#supported-data-stores-and-formats). 

> [!TIP]
> Odkazovat na [článku konektoru SAP BW Open centra](connector-sap-business-warehouse-open-hub.md) na kopírování dat ze SAP BW všeobecně, včetně Úvod k SAP BW Open centra integrace a rozdílové extrakce toku.

## <a name="prerequisites"></a>Požadavky

- **Azure Data Factory (ADF):** Pokud nemáte datovou továrnu, postupujte "[vytvoření datové továrny](quickstart-create-data-factory-portal.md#create-a-data-factory)" část k jejímu vytvoření. 

- **SAP BW Open Centrum cílový (OHD) s typem cílového jako "Tabulky databáze".** Postupujte podle [konfigurace SAP BW otevřít Centrum cílový](#sap-bw-open-hub-destination-configurations) části k jeho vytvoření, nebo k potvrzení, pokud vaše existující OHD správně nakonfigurována integrace pomocí ADF.

- **SAP BW uživatel používá musí mít následující oprávnění:**

  - Autorizace RFC a SAP BW.
  - Oprávnění "**provést**"Aktivita autorizace objektu"**S_SDSAUTH**".

- **[Hostování na vlastním prostředí Integration Runtime](concepts-integration-runtime.md#self-hosted-integration-runtime) s konektorem SAP .NET 3.0 jsou požadovány**. V následující tabulce jsou podrobné krokům, které je potřeba udělat:

  1. Instalaci a registraci místní prostředí IR v verze > = 3.13 (popsané v následující návod). 

  2. Stáhněte si [64-bit SAP .NET konektor 3.0](https://support.sap.com/en/product/connectors/msnet.html) z webu SAP a nainstalujte ho na místní prostředí IR počítače.  Při instalaci, v okně "pokynů k instalaci" Ujistěte se, že jste vybrali "**nainstalovat sestavení do GAC**" možnost, jak je znázorněno na následujícím obrázku.

     ![Nastavení konektoru SAP .NET](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="full-copy-from-sap-bw-open-hub"></a>Úplná kopie z SAP BW Open centra

Na portálu Azure portal, přejděte do služby data factory -> vyberte **vytvořit a monitorovat** spusťte samostatné kartě uživatelské rozhraní ADF. 

1. Na stránce **Začínáme** vyberte dlaždici **Kopírovat data**. Spustí se nástroj pro kopírování dat. 

2. Na **vlastnosti** stránky, zadejte název **název úkolu** pole a vyberte **Další**.

3. Na **zdrojového úložiště dat** klikněte na **+ vytvořit nové připojení** -> vyberte **SAP BW Open centra** z Galerie konektor -> vyberte **pokračovat**. Do vyhledávacího pole filtrovat konektory můžete zadat "SAP".

4. Na **připojení zadejte SAP BW Open rozbočovače** stránky, 

   ![Vytvoření SAP BW Open Centrum propojené služby](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Zvolte **připojit prostřednictvím prostředí Integration Runtime**: klikněte na rozevírací seznam a vyberte existující místní prostředí IR, nebo pokud nemáte místní prostředí IR nastavit ještě nevytvořili. 

      Chcete-li vytvořit nový, klikněte na tlačítko **+ nová** v rozevíracím seznamu-> vyberte typ **v místním prostředí** -> zadejte **název** a klikněte na tlačítko **Další** -> zvolte **Expresní instalace** nainstalovat na aktuálním počítači ani postupovat podle pokynů **ruční instalaci** kroky existuje.

      Jak je uvedeno v [požadavky](#prerequisites), ujistěte se, že budete mít taky **konektoru SAP .NET 3.0** na stejném počítači, kde je spuštěná místní prostředí IR nainstalovaný.

   2. Zadejte SAP BW **název serveru**, **číslo systému**, **ID klienta** **jazyk** (Pokud kromě EN), **uživatelské jméno**, a **heslo**.

   3. Klikněte na tlačítko **Test připojení** Pokud chcete ověřit nastavení, pak vyberte **Dokončit**.

   4. Uvidíte, že se vytvoří nové připojení. Vyberte **Další**.

5. Na **vyberte Otevřít Centrum cíle** stránce, přejděte k dispozici v SAP BW Open centra cíle a vyberte ten, který chcete zkopírovat data z a pak klikněte na **Další**.

   ![Vyberte tabulku, otevřít Centrum SAP BW](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. V případě potřeby zadejte filtr. Pokud otevřete Centrum cíl obsahuje pouze data z jednoho procesu přenosu dat (DTP) spuštění s ID jedné žádosti, nebo jste jisti, že dokončení vaší DTP a chcete všechna data, zrušte zaškrtnutí políčka **vyloučení posledního požadavku**. Přečtěte si více o tom, jak tato nastavení se týkají konfigurace SAP BW [konfigurace SAP BW otevřít Centrum cílový](#sap-bw-open-hub-destination-configurations) oddílu. Klikněte na tlačítko **ověřit** pečlivě zkontrolujte údaje vrátili, pak vyberte **Další**.

   ![Otevřete Centrum SAP BW filtru](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. V **cílového úložiště dat** klikněte na **+ vytvořit nové připojení**a pak vyberte **Azure Data Lake Storage Gen2**a vyberte **pokračovat**.

8. V **připojení zadejte Azure Data Lake Storage** stránky, 

   ![Vytvoření ADLS Gen2 propojené služby](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Vyberte svůj účet Data Lake Storage Gen2 podporující z rozevíracího seznamu "Název účtu úložiště".
   2. Vyberte **Dokončit** k vytvoření připojení. Pak vyberte **Další**.

9. V **zvolte výstupní soubor nebo složku** stránky, zadejte "copyfromopenhub" jako název složky výstupu a vyberte **Další**.

   ![Zvolte výstupní složka](media/load-sap-bw-data/choose-output-folder.png)

10. V **nastavení formátu souboru** stránce **Další** použít výchozí nastavení.

    ![Zadejte formát jímky](media/load-sap-bw-data/specify-sink-format.png)

11. V **nastavení** stránce, rozbalte **nastavení výkonu**a nastavte **stupeň paralelismu kopírování** například 5-li načíst z SAP BW paralelně. Klikněte na **Další**.

    ![Konfigurace nastavení kopírování](media/load-sap-bw-data/configure-copy-settings.png)

12. V **Souhrn** stránky, zkontrolujte nastavení a vyberte **Další**.

13. V **nasazení** stránce **monitorování** a začněte monitorovat kanál.

    ![Stránka Nasazení](media/load-sap-bw-data/deployment.png)

14. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. **Akce** sloupec obsahuje odkazy, chcete-li zobrazit podrobnosti o spuštění aktivit a opětovné spuštění kanálu:

    ![Monitorování kanálu](media/load-sap-bw-data/pipeline-monitoring.png)

15. Pokud chcete zobrazit spuštění aktivit, které jsou spojeny se spuštěním kanálu, vyberte **zobrazit spuštění aktivit** odkaz v **akce** sloupce. Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Pokud chcete přepnout zpět na zobrazení spuštění kanálu, vyberte **kanály** odkazu v horní části. Seznam můžete aktualizovat výběrem možnosti **Aktualizovat**.

    ![Monitorování aktivit](media/load-sap-bw-data/activity-monitoring.png)

16. Pokud chcete monitorovat spuštění podrobnosti o každé aktivitě kopírování, vyberte **podrobnosti** odkaz (obrázek brýlí) ve skupinovém rámečku **akce** v aktivitě zobrazení monitorování. Podobně jako objem dat zkopírovanou ze zdroje do jímky, propustnost dat, provádění kroků s určitou dobu a použít konfigurace, můžete sledovat informace:

    ![Podrobnosti monitorování aktivit](media/load-sap-bw-data/activity-monitoring-details.png)

17. Zkontrolujte **maximální ID žádosti** , který je zkopírován. Přejděte zpět na aktivitu zobrazení monitorování, klikněte na tlačítko **výstup** pod **akce**.

    ![Výstup aktivity](media/load-sap-bw-data/activity-output.png)

    ![Podrobnosti o aktivitě výstupu](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Přírůstkové kopírování z SAP BW Open centra

> [!TIP]
>
> Odkazovat na [SAP BW Open centra konektorem delta extrakce](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) Další informace o způsobu fungování konektoru ADF SAP BW Open centra pro kopírování dat z SAP BW a přečtěte si tento článek od začátku, aby pochopili základní informace týkající se konektoru konfigurace.

Teď můžeme pokračovat v konfiguraci přírůstkového kopírování z SAP BW Open centra. 

Přírůstkové kopírování používá mechanismus horní mez na základě **ID požadavku** automaticky generovat v SAP BW otevřít Centrum cílový DTP. Pracovní postup pro tento přístup je znázorněn v následujícím diagramu:

![Pracovní postup přírůstkového kopírování](media/load-sap-bw-data/incremental-copy-workflow.png)

V Uživatelském rozhraní ADF **pusťme se do práce** stránce **vytvořit kanál ze šablony** využít předdefinované šablony. 

1. Hledat "SAP BW" najít a vybrat šablonu s názvem **přírůstkové kopírování ze SAP BW do Azure Data Lake Storage Gen2**. Tato šablona zkopíruje data do ADLS Gen2, můžete později pomocí podobný postup ke zkopírování do jiných typů jímky.

2. Na hlavní stránce šablony vyberte nebo vytvořte následující tři připojení a potom vyberte **pomocí této šablony** v pravém dolním rohu.

   - **Azure Blob**: v tomto názorném postupu používáme objektů Blob v Azure k ukládání meze, což je maximální počet zkopírovaných žádosti.
   - **Otevřete Centrum SAP BW**: zdroje ke zkopírování dat z. Přečtěte si k předchozímu návodu úplné kopie na podrobná konfigurace.
   - **ADLS Gen2**: pro jímku zkopírovat data do. Přečtěte si k předchozímu návodu úplné kopie na podrobná konfigurace.

   ![Přírůstkové kopírování ze šablony SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Tato šablona vytvoří kanál s tři aktivity – **vyhledávání, kopírování dat a Web** – a je mezi nimi vlastně zřetězené v případě úspěchu. Přejděte do kanálu **parametry** kartu, se zobrazí všechny konfigurace, je potřeba zadat.

   ![Přírůstkové kopírování z konfigurace SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Zadejte název tabulky otevřete Centrum pro kopírování dat z.

   - **ADLSGen2SinkPath**: Zadejte cestu k cílovému ADLS Gen2 zkopírovat data do. Pokud cesta neexistuje, vytvoří při spuštění aktivity kopírování ADF.

   - **HighWatermarkBlobPath**: Zadejte cestu pro ukládání hodnoty horní meze třeba `container/path`. 

   - **HighWatermarkBlobName**: Zadejte název objektu blob pro ukládání hodnoty horní meze třeba `requestIdCache.txt`. V úložišti objektů blob v odpovídající cestě HighWatermarkBlobPath + HighWatermarkBlobName např "*container/path/requestIdCache.txt*", vytvoření objektu blob s obsahem 0. 

      ![Obsah objektu blob](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: v této šabloně používáme aktivity webu pro volání aplikací logiky k nastavení hodnoty horní meze v úložišti objektů Blob. Alternativně můžete také databáze SQL ukládat ho a aktualizujte hodnotu pomocí aktivity uložených procedur. 

      Zde je potřeba nejprve vytvořit aplikaci logiky jako následující příkaz, zkopírujte **adresa URL operace HTTP POST** do tohoto pole. 

      ![Konfigurace aplikace logiky](media/load-sap-bw-data/logic-app-config.png)

      1. Přejít na webu Azure portal -> Nový **Logic Apps** služby -> klikněte na tlačítko **+ prázdná aplikace logiky** přejdete na **návrhář pro Logic Apps**.

      2. Vytvořit aktivační událost s **přijetí požadavku HTTP když**. Určení těla požadavku protokolu HTTP následujícím způsobem:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               },
               "type": "object"
            }
         }
         ```

      3. Přidejte akci **vytvořit objekt blob**. "Cesta ke složce" a "Název objektu Blob" použijte stejnou hodnotu nakonfigurovanou v výše uvedeného HighWatermarkBlobPath a HighWatermarkBlobName.

      4. Klikněte na tlačítko **Uložit**a potom zkopírujte hodnotu **adresa URL operace HTTP POST** pro použití v kanálu ADF.

4. Po zadání všech hodnot pro parametry kanálu ADF, můžete kliknout na **ladění** -> **Dokončit** chcete vyvolat běh k ověření konfigurace. Nebo můžete vybrat **Publikovat vše** publikovat všechny změny, potom klikněte na tlačítko **aktivační událost** provést spuštění.

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW otevřít Centrum cílový konfigurace

Tato část představuje pro použití konektoru SAP BW Open centra ve službě ADF ke kopírování dat potřebných konfigurace na straně SAP BW.

### <a name="configure-delta-extraction-in-sap-bw"></a>Konfigurace extrakce rozdílů v SAP BW

Pokud potřebujete historické kopie a přírůstkového kopírování nebo pouze přírůstkové kopie, nakonfigurujte extrakce rozdílů v SAP BW.

1. Vytvořit cíl otevřete Centrum (OHD)

   Můžete vytvořit OHD v RSA1 transakce SAP, tím se automaticky vytvoří požadované transformaci a proces pro přenos dat (DTP). Použijte následující nastavení:

   - Typ objektu může být žádné. InfoCube zde použijeme jako příklad.
   - **Typ cíle:** *Databázové tabulky*
   - **Klíč v tabulce:** *Technické klíč*
   - **Extrakce:** *Zachovat Data a záznamy vložit do tabulky*

   ![Vytvoření extrakce delta SAP BW OHD](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![create-sap-bw-ohd-delta2](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Může zvýšit počet paralelních spuštění SAP pracovní procesy pro DTP:

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Plán DTP v procesu řetězců

   DTP rozdílů pro datovou krychli pouze funguje, když není potřebný řádky komprimované ještě. Proto musíte, BW komprese datové krychle není spuštěn před DTP do tabulky otevřete Centrum. Nejsnadnějším způsobem, jak to integruje do vaší stávající řetězy procesu tento DTP. V následujícím příkladu je vložen DTP (Chcete-li OHD) v řetězci procesu mezi kroku upravit (souhrnného) a sbalit (komprese datové krychle).

   ![create-sap-bw-process-chain](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Konfigurace úplné extrakce v SAP BW

Kromě delta extrakce můžete chtít mít úplnou extrakce stejné InfoProvider. Obvykle platí, pokud chcete provést úplné kopie bez nutnosti přírůstkové, nebo chcete provést [opětovné synchronizace delta extrakce](#re-sync-delta-extraction).

Pro stejnou OHD nesmí mít více než jeden DTP. Proto je potřeba vytvořit další OHD pak rozdílovou extrakce.

![create-sap-bw-ohd-full](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Úplné načtení OHD zvolte různé možnosti než extrakce rozdílů:

- V OHD: nastavte možnost "Extrakce" jako "*Data odstranit a vložit záznamy*". V opačném případě bude extrahována data v mnoha případech při opakování DTP v řetězci BW procesu.

- V DTP: nastavení "Režim extrakce" jako "*úplné*". Automaticky vytvořený DTP v rozdílovém musíte změnit na úplné pouze po vytvoření OHD:

   ![create-sap-bw-ohd-full2](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- V konektoru ADF SAP BW Open centra: Vypněte možnost "*vyloučení posledního požadavku*". Jinak by být extrahovat hodnotu nothing. 

Obvykle spustíte úplné DTP ručně. Nebo můžete také vytvořit řetěz proces pro úplné DTP – bude obvykle řetěz samostatný proces nezávisle na vaší stávající řetězy procesu. V obou případech musíte **Ujistěte se, že DTP dokončila před zahájením extrakce, pomocí kopie ADF**, v opačném případě budou zkopírovány částečná data.

### <a name="run-delta-extraction-the-first-time"></a>Extrakce delta při prvním spuštění

První extrakci rozdílů je technicky **úplné extrakce**. Poznámka: ve výchozí ADF SAP BW Open centra konektor vyloučí poslední žádosti při kopírování dat. V případě rozdílového extrakce pro první spuštění v aktivitě kopírování ADF, budou extrahovány žádná data, dokud nedojde k následné DTP generuje rozdílová data v tabulce s ID samostatnou žádost. I když existují dva možné způsoby, jak vyhnout této situaci:

1. Vypněte možnost "Vyloučit poslední žádosti" pro první Delta extrakce na tento případ, které potřebujete k Ujistěte se, že dokončení první DTP Delta před zahájením extrakce Delta poprvé
2. Pomocí postupu pro zpětné synchronizace Delta extrakce, jak je popsáno níže.

### <a name="re-sync-delta-extraction"></a>Opětovné synchronizace delta extrakce

Zde je několik scénářů, změnu dat v datové krychle SAP BW, které nejsou považované za podle DTP rozdílů:

- SAP BW selektivní mazání (řádky pomocí libovolné podmínky filtru)
- SAP BW žádosti o odstranění (Chybný požadavek)

Otevřít Centrum cílový SAP není cílem pracující s daty regulovanými Tržiště dat (ve všech SAP BW podporu balíčků od roku 2015). Proto je možné k odstranění dat z datové krychle beze změny dat v OHD. V takovém případě musí znovu synchronizovat data z datové krychle s daty ve službě ADF provedením následujících kroků:

1. Spustit úplné extrakce ve službě ADF (s použitím úplné DTP v SAP)
2. Odstranit všechny řádky v tabulce otevřete Centrum pro rozdílové DTP.
3. Nastavit stav Delta DTP na počet získaných

Za to všechny následné DTPs rozdílů a extrakce Delta ADF fungovat podle očekávání.

Můžete nastavit stav Delta DTP na počet získaných spuštěním DTP Delta ručně pomocí následujících možností: "*Nepřenášejí žádná Data; Stav delta ve zdroji: Načíst*".

## <a name="next-steps"></a>Další postup

Přejděte k další informace o podpoře konektoru SAP BW Open centra v následujícím článku: 

> [!div class="nextstepaction"]
>[Konektor SAP Business Warehouse otevřete Centrum](connector-sap-business-warehouse-open-hub.md)
