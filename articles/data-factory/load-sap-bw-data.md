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
ms.date: 05/22/2019
ms.author: jingwang
ms.openlocfilehash: 4cd61db3ec0e8d88c9b1c6d6ba427b120b3f1af1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66152417"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Kopírování dat z řešení SAP Business Warehouse s využitím Azure Data Factory

Tento článek ukazuje, jak pomocí Azure Data Factory ke zkopírování dat z SAP Business Warehouse (BW) prostřednictvím Open centra do Azure Data Lake Storage Gen2. Podobný proces můžete použít ke zkopírování dat do jiných [podporovaná úložiště dat jímky](copy-activity-overview.md#supported-data-stores-and-formats).

> [!TIP]
> Obecné informace o kopírování dat z SAP BW, včetně integrace SAP BW Open centra a rozdílové extrakce toku, najdete v části [kopírování dat z řešení SAP Business Warehouse prostřednictvím Open centra pomocí služby Azure Data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Požadavky

- **Azure Data Factory**: Pokud ho nemáte, postupujte podle kroků pro [vytvoření datové továrny](quickstart-create-data-factory-portal.md#create-a-data-factory).

- **SAP BW Open Centrum cílový (OHD) s typem cílového "Databázové tabulky"**: Vytvoření OHD zkontrolujte, jestli je vaše OHD správně nakonfigurovaný pro integraci služby Data Factory najdete v článku [konfigurace SAP BW otevřít Centrum cílový](#sap-bw-open-hub-destination-configurations) části tohoto článku.

- **SAP BW uživatel potřebuje následující oprávnění**:

  - Autorizace pro vzdálená volání funkcí (RFC) a SAP BW.
  - Oprávnění k "Spustit" aktivitu **S_SDSAUTH** objekt autorizací.

- **A [v místním prostředí integration runtime (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) s konektorem SAP .NET 3.0**. Postupujte podle těchto kroků instalace:

  1. Instalace a registrace místního prostředí integration runtime, verze 3,13 nebo novější. (Tento proces je popsán dále v tomto článku).

  2. Stáhněte si [64-bit konektoru SAP pro rozhraní Microsoft .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) z webu SAP a nainstalujte ho na stejném počítači jako v místním prostředí IR. Během instalace, ujistěte se, že jste vybrali **nainstalovat sestavení do GAC** v **pokynů k instalaci** dialogové okno, jak ukazuje následující obrázek:

     ![Nastavení dialogové okno konektoru SAP .NET](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Proveďte úplné kopie z SAP BW Open centra

Na webu Azure Portal přejděte do služby data factory. Vyberte **vytvořit a monitorovat** otevřete na samostatné kartě uživatelské rozhraní služby Data Factory.

1. Na **pusťme se do práce** stránce **kopírování dat** otevřete nástroj pro kopírování dat.

2. Na **vlastnosti** určete, **název úkolu**a pak vyberte **Další**.

3. Na **zdrojového úložiště dat** stránce **+ vytvořit nové připojení**. Vyberte **SAP BW Open centra** z Galerie konektoru a pak vyberte **pokračovat**. Filtrovat konektory, můžete zadat **SAP** do vyhledávacího pole.

4. Na **připojení zadejte SAP BW Open rozbočovače** stránce, postupujte podle těchto kroků a vytvořte nové připojení.

   ![Vytvoření stránky otevřete Centrum SAP BW propojená služba](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Z **připojit prostřednictvím prostředí integration runtime** vyberte existující v místním prostředí IR. Nebo můžete vytvořit jednu, pokud ještě nemáte.

      Chcete-li vytvořit nové prostředí IR v místním prostředí, vyberte **+ nová**a pak vyberte **v místním prostředí**. Zadejte **název**a pak vyberte **Další**. Vyberte **Expresní instalace** nainstalovat na počítač ani postupovat podle pokynů **ruční instalaci** kroky, které jsou k dispozici.

      Jak je uvedeno v [požadavky](#prerequisites), ujistěte se, že máte pro Microsoft .NET 3.0 nainstalovaný na stejném počítači, kde je spuštěná místní prostředí IR konektoru SAP.

   2. Vyplňte SAP BW **název serveru**, **číslo systému**, **ID klienta** **jazyk** (Pokud jiný než **EN**) , **Uživatelské jméno**, a **heslo**.

   3. Vyberte **Test připojení** ověřte nastavení a potom vyberte **Dokončit**.

   4. Vytvoření nového připojení. Vyberte **Další**.

5. Na **vyberte Otevřít Centrum cíle** stránky, vyhledejte otevřít centra cílů, které jsou k dispozici v SAP BW. Vyberte OHD kopírování dat z, a potom vyberte **Další**.

   ![Vyberte SAP BW Open centra cílové tabulky](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Zadejte filtr, pokud budete potřebovat. Pokud vaše OHD obsahuje pouze data od spuštění procesu (DTP) jeden přenos dat s ID jedné žádosti, nebo si jisti, že dokončení vaší DTP a vy chcete zkopírovat data, zrušte **vyloučení posledního požadavku** zaškrtávací políčko.

   Další informace o těchto nastaveních [konfigurace SAP BW otevřít Centrum cílový](#sap-bw-open-hub-destination-configurations) části tohoto článku. Vyberte **ověřit** znovu zkontrolovat, jaká data bude vrácen. Pak vyberte **Další**.

   ![Otevřete Centrum SAP BW filtru](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Na **cílového úložiště dat** stránce **+ vytvořit nové připojení** > **Azure Data Lake Storage Gen2**  >   **Pokračovat**.

8. Na **připojení zadejte Azure Data Lake Storage** stránce, postupujte podle těchto kroků a vytvořte připojení.

   ![Vytvoření stránky propojené služby ADLS Gen2](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Vyberte svůj účet Data Lake Storage Gen2 umožňující z **název** rozevíracího seznamu.
   2. Vyberte **Dokončit** k vytvoření připojení. Pak vyberte **Další**.

9. Na **zvolte výstupní soubor nebo složku** zadejte **copyfromopenhub** jako název složky výstupu. Pak vyberte **Další**.

   ![Vyberte stránku výstupní složka](media/load-sap-bw-data/choose-output-folder.png)

10. Na **nastavení formátu souboru** stránce **Další** použít výchozí nastavení.

    ![Určení stránky formátu jímky](media/load-sap-bw-data/specify-sink-format.png)

11. Na **nastavení** stránce, rozbalte **nastavení výkonu**. Zadejte hodnotu pro **stupeň paralelismu kopírování** například 5 pro načtení ze SAP BW paralelně. Pak vyberte **Další**.

    ![Konfigurace nastavení kopírování](media/load-sap-bw-data/configure-copy-settings.png)

12. Na **Souhrn** stránky, zkontrolujte nastavení. Pak vyberte **Další**.

13. Na **nasazení** stránce **monitorování** a začněte monitorovat kanál.

    ![Stránka Nasazení](media/load-sap-bw-data/deployment.png)

14. Všimněte si, **monitorování** automaticky vybraná karta na levé straně stránky. **Akce** sloupec obsahuje odkazy, chcete-li zobrazit podrobnosti o spuštění aktivit a spustit kanál znovu.

    ![Zobrazení monitorování kanálu](media/load-sap-bw-data/pipeline-monitoring.png)

15. Pokud chcete zobrazit spuštění aktivit, které jsou spojeny se spuštěním kanálu, vyberte **zobrazit spuštění aktivit** v **akce** sloupce. Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Pokud chcete přepnout zpět na zobrazení spuštění kanálu, vyberte **kanály** odkazu v horní části. Seznam můžete aktualizovat výběrem možnosti **Aktualizovat**.

    ![Monitorování aktivit obrazovky](media/load-sap-bw-data/activity-monitoring.png)

16. Pokud chcete monitorovat spuštění podrobnosti o každé aktivitě kopírování, vyberte **podrobnosti** odkaz, což je ikona brýlí níže **akce** v zobrazení monitorování aktivit. Dostupné podrobnosti zahrnují objem dat zkopírovanou ze zdroje do jímky, propustnost dat, provádění kroků a dobu trvání a konfigurace použité.

    ![Podrobnosti monitorování aktivit](media/load-sap-bw-data/activity-monitoring-details.png)

17. Chcete-li zobrazit **maximální ID žádosti**, přejděte zpět na zobrazení a vyberte monitorování aktivit **výstup** pod **akce**.

    ![Obrazovka výstup aktivity](media/load-sap-bw-data/activity-output.png)

    ![Zobrazení podrobností o výstup aktivity](media/load-sap-bw-data/activity-output-details.png)

## <a name="do-an-incremental-copy-from-sap-bw-open-hub"></a>Provést přírůstková kopie ze SAP BW Open centra

> [!TIP]
> Zobrazit [SAP BW Open centra konektorem delta extrakce](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) se dozvíte, jak konektor SAP BW Open Hub ve službě Data Factory kopíruje Přírůstková data z SAP BW. Tento článek může taky vám pomůžou pochopit základní konektor configuration.

Teď můžeme pokračovat v konfiguraci přírůstkového kopírování z SAP BW Open centra.

Přírůstkové kopírování používá mechanismus "meze", který je založen na **ID požadavku**. Toto ID není automaticky vygenerován v SAP BW otevřít Centrum cílový DTP. Následující diagram znázorňuje tento pracovní postup:

![Přírůstkové kopírování pracovního postupu vývojového diagramu](media/load-sap-bw-data/incremental-copy-workflow.png)

V datové továrně **pusťme se do práce** stránce **vytvořit kanál ze šablony** použít předdefinované šablony.

1. Vyhledejte **SAP BW** najděte a vyberte **přírůstkové kopírování ze SAP BW do Azure Data Lake Storage Gen2** šablony. Tato šablona zkopíruje data do Azure Data Lake Storage Gen2. Podobně jako pracovní postup můžete použít ke zkopírování do jiných typů jímky.

2. Na hlavní stránce šablony vyberte nebo vytvořte následující tři připojení a pak vyberte **pomocí této šablony** v pravém dolním rohu okna.

   - **Azure Blob storage**: V tomto názorném postupu používáme Azure Blob storage k ukládání meze, která je *max zkopírovat ID požadavku*.
   - **SAP BW Open Hub**: Toto je zdroje ke zkopírování dat z. Přečtěte si k předchozímu návodu celou Kopírovat pro podrobnou konfiguraci.
   - **Azure Data Lake Storage Gen2**: Toto je jímky kopírování dat do. Přečtěte si k předchozímu návodu celou Kopírovat pro podrobnou konfiguraci.

   ![Přírůstkové kopírování ze šablony SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Tato šablona vytvoří kanál s následujícími aktivitami tři a je mezi nimi vlastně zřetězené v případě úspěchu: *Vyhledávání*, *kopírování dat*, a *webové*.

   Přejděte do kanálu **parametry** kartu. Zobrazí všechny konfigurace, které je potřeba zadat.

   ![Přírůstkové kopírování z konfigurace SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Zadejte název tabulky otevřete Centrum pro kopírování dat z.

   - **ADLSGen2SinkPath**: Zadejte cílovou cestu, Azure Data Lake Storage Gen2 zkopírovat data do. Pokud cesta neexistuje, aktivita kopírování objektu pro vytváření dat vytváří cestu během provádění.

   - **HighWatermarkBlobPath**: Zadejte cestu pro ukládání hodnoty horní meze, jako například `container/path`.

   - **HighWatermarkBlobName**: Zadejte název objektu blob pro ukládání hodnoty horní meze, jako například `requestIdCache.txt`. V úložišti objektů Blob, přejděte na odpovídající cesta HighWatermarkBlobPath + HighWatermarkBlobName, jako například *container/path/requestIdCache.txt*. Vytvoření objektu blob s obsahem 0.

      ![Obsah objektu blob](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: V této šabloně používáme WebActivity volání Azure Logic Apps se nastavit hodnotu meze v úložišti objektů Blob. Nebo můžete použít Azure SQL Database a uložit. Aktualizujte hodnotu pomocí aktivity uložených procedur.

      Nejprve musíte vytvořit aplikaci logiky, jak ukazuje následující obrázek. Nakonec vložte **adresa URL operace HTTP POST**.

      ![Konfigurace aplikací logiky](media/load-sap-bw-data/logic-app-config.png)

      1. Přejděte na web Azure Portal. Vyberte nový **Logic Apps** služby. Vyberte **+ prázdná aplikace logiky** přejdete na **návrhář pro Logic Apps**.

      2. Vytvořit aktivační událost s **přijetí požadavku HTTP při**. Určení těla požadavku protokolu HTTP následujícím způsobem:

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

      3. Přidat **vytvořit objekt blob** akce. Pro **cesta ke složce** a **název objektu Blob**, použijte stejné hodnoty, které jste dříve nakonfigurovali v **HighWatermarkBlobPath** a **HighWatermarkBlobName**.

      4. Vyberte **Uložit**. Potom zkopírujte hodnotu **adresa URL operace HTTP POST** pro použití v kanálech Data Factory.

4. Po zadání parametrů kanálu služby Data Factory, vyberte **ladění** > **Dokončit** chcete vyvolat běh k ověření konfigurace. Nebo vyberte **Publikovat vše** publikovat změny a pak vyberte **aktivační událost** provést spuštění.

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW otevřít Centrum cílový konfigurace

Tato část představuje konfigurace na straně SAP BW pro použití konektoru SAP BW Open centra ve službě Data Factory pro kopírování dat.

### <a name="configure-delta-extraction-in-sap-bw"></a>Konfigurace extrakce rozdílů v SAP BW

Pokud potřebujete historické kopie a přírůstkového kopírování nebo pouze přírůstkové kopie, nakonfigurujte extrakce rozdílů v SAP BW.

1. Vytvořte cílový otevřete Centrum. Můžete vytvořit OHD v RSA1 transakce SAP, tím se automaticky vytvoří požadované transformaci a přenos dat procesu. Použijte následující nastavení:

   - **ObjectType**: Můžete použít libovolný typ objektu. Tady používáme **InfoCube** jako příklad.
   - **Cílový typ**: Vyberte **tabulky databáze**.
   - **Klíče tabulky**: Vyberte **technické klíč**.
   - **Extrakce**: Vyberte **zachovat Data a záznamy vložit do tabulky**.

   ![SAP BW OHD delta extrakce dialogové okno vytvořit](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![SAP BW OHD delta2 extrakce dialogové okno vytvořit](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Může zvýšit počet paralelních spuštění SAP pracovní procesy pro DTP:

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Naplánujte DTP v procesu řetězců.

   Rozdílové hodnoty DTP datovou krychli funguje jenom v případě potřeby řádků nebyly komprimovány. Ujistěte se, že BW komprese datové krychle není spuštěna před DTP do tabulky otevřete Centrum. Nejjednodušší způsob je integrovat vaše existující proces řetězy DTP. V následujícím příkladu je vložen DTP (Chcete-li OHD) do řetězce procesu mezi *upravit* (souhrnného) a *sbalit* kroky (komprese datové krychle).

   ![Vytvoření SAP BW procesu řetězu vývojový diagram](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Konfigurace úplné extrakce v SAP BW

Kromě delta extrakce může být vhodné úplné extrakce stejné InfoProvider SAP BW. To obvykle platí v případě, že chcete provést úplné kopie, ale ne přírůstkové, nebo chcete provést [nové synchronizace delta extrakce](#resync-delta-extraction).

Pro stejnou OHD nemůže mít více než jeden DTP. Proto je nutné vytvořit další OHD před delta extrakce.

![Vytvoření SAP BW OHD úplné](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Úplné načtení OHD zvolte různé možnosti než pro extrakci rozdílů:

- V OHD: Nastavte **extrakce** umožňuje **Data odstranit a vložit záznamy**. V opačném případě budou extrahována data v mnoha případech při opakování DTP v řetězci BW procesu.

- V DTP: Nastavte **režimu extrakce** k **úplné**. Musíte změníte automaticky vytvořený DTP z **rozdílové** k **úplné** okamžitě po OHD, jak ukazuje tento obrázek:

   ![Vytvoření SAP BW OHD nakonfigurované pro extrakci "Úplné" dialogové okno](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- V konektoru BW Open centra služby Data Factory: Vypnout **vyloučení posledního požadavku**. V opačném případě nic se budou extrahovat.

Obvykle spustíte úplné DTP ručně. Nebo můžete vytvořit řetězec proces pro úplné DTP. Je to obvykle samostatné řetězec, který je nezávislý na vaší stávající řetězy procesu. V obou případech *Ujistěte se, že je DTP dokončena před zahájením extrakce pomocí služby Data Factory kopie*. V opačném případě budou zkopírovány pouze částečná data.

### <a name="run-delta-extraction-the-first-time"></a>Extrakce delta při prvním spuštění

Technicky je první extrakci delta *úplné extrakce*. Ve výchozím nastavení vyloučí konektoru SAP BW Open centra poslední žádosti při kopíruje data. Pro první delta extrakce aktivita kopírování objektu pro vytváření dat extrahují žádná data dokud následné DTP generuje rozdílová data v tabulce s ID samostatnou žádost. Existují dva způsoby, jak se vyhnout této situaci:

- Vypnout **vyloučení posledního požadavku** možnost pro první extrakci delta. Ujistěte se, že první delta DTP dokončení před zahájením extrakce delta poprvé.
-  Pomocí postupu pro resynchronizaci delta extrakce, jak je popsáno v další části.

### <a name="resync-delta-extraction"></a>Nové synchronizace delta extrakce

Následující scénáře změnu dat v datové krychle SAP BW, ale nejsou považovány za položkou delta DTP:

- SAP BW selektivní mazání (řádky s použitím libovolné podmínky filtru)
- SAP BW žádosti o odstranění (chybný počet požadavků)

Otevřít Centrum cílový SAP není cíl s daty regulovanými Tržiště dat (ve všech balíčků podpora SAP BW od 2015). Data můžete odstranit tak, z datové krychle beze změny dat v OHD. Potom musí znovu synchronizovat data z datové krychle pomocí služby Data Factory:

1. Spustíte úplné extrakce ve službě Data Factory (s použitím úplné DTP v SAP).
2. Odstraňte všechny řádky v tabulce otevřete Centrum pro rozdílové DTP.
3. Nastavit stav delta DTP k **počet získaných**.

Po této, všechny následné delta DTPs a extrakce rozdílová Data Factory fungovat podle očekávání.

Nastavit stav delta DTP k **počet získaných**, následující možnost můžete ručně spustit rozdílový DTP:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>Další postup

Další informace o podpoře konektoru SAP BW Open Hub:

> [!div class="nextstepaction"]
>[Konektor SAP Business Warehouse otevřete Centrum](connector-sap-business-warehouse-open-hub.md)
