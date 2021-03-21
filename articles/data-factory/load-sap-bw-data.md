---
title: Načtení dat z SAP Business Warehouse
description: Použití Azure Data Factory ke kopírování dat z SAP Business Warehouse (ČERNOBÍLý)
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/22/2019
ms.openlocfilehash: 3dabb6d5df0a74cc7ae2fb8b381ad9e0dfe04e63
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370695"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Kopírování dat z SAP Business Warehouse pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V tomto článku se dozvíte, jak pomocí Azure Data Factory kopírovat data z SAP Business Warehouse (ČERNOBÍLý) prostřednictvím otevřeného centra pro Azure Data Lake Storage Gen2. Podobný proces můžete použít ke kopírování dat do jiných [podporovaných úložišť dat jímky](copy-activity-overview.md#supported-data-stores-and-formats).

> [!TIP]
> Obecné informace o kopírování dat z SAP BW, včetně SAP BW otevřené integrace centra a postupu extrakce Delta, najdete v tématu [kopírování dat ze SAP Business Warehouse přes Open hub pomocí Azure Data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Předpoklady

- **Azure Data Factory**: Pokud ho ještě nemáte, postupujte podle pokynů k [Vytvoření datové továrny](quickstart-create-data-factory-portal.md#create-a-data-factory).

- **SAP BW OHD (cílové umístění centra) s cílovým typem "databázová tabulka"**: Pokud chcete vytvořit OHD nebo ověřit, že je váš OHD správně nakonfigurovaný pro Data Factory integraci, přečtěte si část [SAP BW otevřít cílovou konfiguraci centra](#sap-bw-open-hub-destination-configurations) v tomto článku.

- **SAP BW uživatel potřebuje následující oprávnění**:

  - Autorizace pro volání funkce Remote Functions (RFC) a SAP BW.
  - Oprávnění k aktivitě Execute objektu autorizace **S_SDSAUTH** .

- **Místní [prostředí Integration runtime (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) s konektorem SAP .NET Connector 3,0**. Postupujte podle těchto kroků instalace:

  1. Nainstalujte a zaregistrujte místní prostředí Integration runtime, verze 3,13 nebo novější. (Tento postup je popsán dále v tomto článku.)

  2. Stáhněte si z webu SAP [konektor 64-bit SAP pro Microsoft .net 3,0](https://support.sap.com/en/product/connectors/msnet.html) a nainstalujte ho do stejného počítače jako prostředí IR v místním prostředí. Během instalace se ujistěte, že jste v dialogovém okně **volitelné instalační kroky** vybrali možnost **instalovat sestavení do mezipaměti GAC** , jak ukazuje následující obrázek:

     ![Dialogové okno nastavení konektoru SAP .NET](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Provedení úplné kopie z SAP BW otevřeném centru

Na webu Azure Portal přejděte k vaší datové továrně. Vyberte **vytvořit & monitorování** a otevřete tak Data Factory uživatelské rozhraní na samostatné kartě.

1. Na stránce **Začínáme** vyberte **kopírování dat** pro otevření nástroje kopírování dat.

2. Na stránce **vlastnosti** zadejte **název úlohy** a pak vyberte **Další**.

3. Na stránce **zdrojové úložiště dat** vyberte **+ vytvořit nové připojení**. Vyberte **SAP BW otevřít rozbočovač** z Galerie konektorů a pak vyberte **pokračovat**. Chcete-li filtrovat konektory, můžete do vyhledávacího pole zadat **SAP** .

4. Na stránce **zadat SAP BW otevřít připojení centra** postupujte podle těchto kroků a vytvořte nové připojení.

   ![Vytvořit SAP BW otevřít stránku propojených služeb na střed](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. V seznamu **připojit prostřednictvím prostředí Integration runtime** vyberte existující prostředí IR v místním prostředí. Nebo ho můžete vytvořit, pokud ho ještě nemáte.

      Pokud chcete vytvořit nový prostředí IR v místním prostředí, vyberte **+ Nový** a pak vyberte v místním **prostředí.** Zadejte **název** a potom vyberte **Další**. Vyberte **expresní instalaci** , kterou chcete nainstalovat na aktuální počítač, nebo postupujte podle uvedených kroků **Ruční instalace** .

      Jak je uvedeno v části [požadavky](#prerequisites), ujistěte se, že máte konektor SAP pro Microsoft .NET 3,0 nainstalovaný na stejném počítači, na kterém běží prostředí IR pro místní hostování.

   2. Zadejte **název SAP BW serveru**, **číslo systému**, **ID klienta,** **jazyk** (Pokud je jiný než **EN**), **uživatelské jméno** a **heslo**.

   3. Vyberte **Test připojení** , aby se ověřilo nastavení, a pak vyberte **Dokončit**.

   4. Vytvoří se nové připojení. Vyberte **Další**.

5. Na stránce **Vyberte umístění centra** , která jsou k dispozici ve vašem SAP BW, přejděte na otevřená umístění centra. Vyberte OHD, ze kterých se mají kopírovat data, a pak vyberte **Další**.

   ![Vybrat SAP BW otevřít cílovou tabulku centra](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Určete filtr, pokud ho potřebujete. Pokud vaše OHD obsahuje jenom data z jednoho procesu přenosu dat (DTP) s jediným ID žádosti, nebo jste si jisti, že je váš DTP dokončený a že chcete data zkopírovat, zrušte zaškrtnutí políčka **vyloučit poslední požadavek** .

   Další informace o těchto nastaveních najdete v části [SAP BW otevřít cílové konfigurace centra](#sap-bw-open-hub-destination-configurations) v tomto článku. Výběrem možnosti **ověřit** dvakrát zkontrolujete, jaká data budou vrácena. Pak vyberte **Další**.

   ![Nakonfigurovat SAP BW otevřít filtr centra](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. Na stránce **cílové úložiště dat** vyberte **+ vytvořit nové připojení**  >  **Azure Data Lake Storage Gen2**  >  **pokračovat**.

8. Na stránce **zadejte Azure Data Lake Storage připojení** postupujte podle těchto kroků a vytvořte připojení.

   ![Vytvoření stránky propojené služby ADLS Gen2](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. V rozevíracím seznamu **název** vyberte účet s podporou Data Lake Storage Gen2.
   2. Vytvořte připojení výběrem možnosti **Dokončit**. Pak vyberte **Další**.

9. Na stránce **zvolit výstupní soubor nebo složku** zadejte **copyfromopenhub** jako název výstupní složky. Pak vyberte **Další**.

   ![Vybrat stránku výstupní složky](media/load-sap-bw-data/choose-output-folder.png)

10. Na stránce **Nastavení formátu souboru** vyberte možnost **Další** a použijte výchozí nastavení.

    ![Zadat stránku formátu jímky](media/load-sap-bw-data/specify-sink-format.png)

11. Na stránce **Nastavení** rozbalte položku **Nastavení výkonu**. Zadejte hodnotu pro **úroveň kopírování paralelismus** , jako je například 5 pro načtení z SAP BW paralelně. Pak vyberte **Další**.

    ![Konfigurovat nastavení kopírování](media/load-sap-bw-data/configure-copy-settings.png)

12. Na stránce **Souhrn** zkontrolujte nastavení. Pak vyberte **Další**.

13. Na stránce **nasazení** vyberte **monitorovat** a monitorujte kanál.

    ![Stránka Nasazení](media/load-sap-bw-data/deployment.png)

14. Všimněte si, že se automaticky vybere karta **monitorování** na levé straně stránky. Sloupec **Actions (akce** ) obsahuje odkazy na zobrazení podrobností o spuštění aktivit a opětovném spuštění kanálu.

    ![Zobrazení monitorování kanálu](media/load-sap-bw-data/pipeline-monitoring.png)

15. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte **Zobrazit spuštění aktivit** ve sloupci **Akce** . Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Pokud chcete přejít zpátky k zobrazení spuštění kanálu, vyberte odkaz **kanály** v horní části. Seznam můžete aktualizovat kliknutím na **Aktualizovat**.

    ![Obrazovka monitorování aktivity](media/load-sap-bw-data/activity-monitoring.png)

16. Chcete-li monitorovat podrobnosti o spuštění každé aktivity kopírování, vyberte odkaz **Podrobnosti** , což je ikona brýlí pod **akcemi** v zobrazení monitorování aktivity. Dostupné podrobnosti zahrnují datový svazek zkopírovaný ze zdroje do jímky, propustnosti dat, postup provedení a dobu trvání a použité konfigurace.

    ![Podrobnosti monitorování aktivity](media/load-sap-bw-data/activity-monitoring-details.png)

17. Chcete-li zobrazit **maximální ID žádosti**, vraťte se zpět do zobrazení monitorování aktivit a vyberte **výstup** v části **Akce**.

    ![Obrazovka výstup aktivity](media/load-sap-bw-data/activity-output.png)

    ![Zobrazení podrobností výstupu aktivity](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Přírůstková kopie z SAP BW otevřeném centru

> [!TIP]
> Další informace o tom, jak SAP BW Open hub Connector v Data Factory kopíruje přírůstková data z SAP BW, najdete v tématu [SAP BW Open hub Connector – průběh extrakce](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) . Tento článek vám může také porozumět základní konfiguraci konektoru.

Teď budeme pokračovat v konfiguraci přírůstkové kopie z SAP BW otevřeném centru.

Přírůstkové kopírování používá mechanismus vysoké meze, který je založen na **ID žádosti**. Toto ID se automaticky vygeneruje v SAP BW otevřeném cíli centra DTP. Tento pracovní postup znázorňuje následující diagram:

![Diagram toku pracovního postupu přírůstkového kopírování](media/load-sap-bw-data/incremental-copy-workflow.png)

Na stránce **Začínáme** pro datovou továrnu vyberte **vytvořit kanál ze šablony** , aby se použila předdefinovaná šablona.

1. Vyhledejte **SAP BW** , kde můžete najít a vybrat **přírůstkovou kopii z SAP BW na šablonu Azure Data Lake Storage Gen2** . Tato šablona kopíruje data do Azure Data Lake Storage Gen2. Podobný pracovní postup můžete použít ke kopírování na jiné typy jímky.

2. Na hlavní stránce šablony vyberte nebo vytvořte následující tři připojení a pak vyberte **použít tuto šablonu** v pravém dolním rohu okna.

   - **Azure Blob Storage**: v tomto návodu používáme úložiště objektů BLOB v Azure k uložení horní meze, což je *maximální kopírovaný identifikátor žádosti*.
   - **SAP BW otevřené centrum**: Jedná se o zdroj, ze kterého se mají kopírovat data. Podrobnou konfiguraci najdete v předchozím podrobném návodu k úplnému kopírování.
   - **Azure Data Lake Storage Gen2**: Toto je jímka, do které se mají kopírovat data. Podrobnou konfiguraci najdete v předchozím podrobném návodu k úplnému kopírování.

   ![Přírůstková kopírování ze šablony SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Tato šablona vygeneruje kanál s následujícími třemi aktivitami a provede jejich zřetězení při úspěchu: *vyhledávání*, *kopírování dat* a *webu*.

   Přejít na kartu **parametry** kanálu. Zobrazí se všechny konfigurace, které je třeba zadat.

   ![Přírůstková kopie z konfigurace SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: zadejte název otevřené tabulky centra pro kopírování dat.

   - **Data_Destination_Container**: zadejte cílovou Azure Data Lake Storage Gen2 kontejner, do kterého se mají kopírovat data. Pokud kontejner neexistuje, aktivita Data Factory kopírování ho vytvoří během provádění.
  
   - **Data_Destination_Directory**: zadejte cestu ke složce v kontejneru Azure Data Lake Storage Gen2, do které se mají kopírovat data. Pokud cesta neexistuje, aktivita Data Factory kopírování vytvoří během provádění cestu.
  
   - **HighWatermarkBlobContainer**: Určete kontejner, do kterého se má uložit hodnota s horním limitem.

   - **HighWatermarkBlobDirectory**: zadejte cestu ke složce v kontejneru, do které se uloží hodnota s horním limitem.

   - **HighWatermarkBlobName**: zadejte název objektu blob, do kterého se uloží hodnota horní meze, třeba `requestIdCache.txt` . V části úložiště objektů blob, přejít na odpovídající cestu HighWatermarkBlobContainer + HighWatermarkBlobDirectory + HighWatermarkBlobName, jako je například *Container/cesta/requestIdCache.txt*. Vytvoří objekt BLOB s obsahem 0.

      ![Obsah objektu blob](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: v této šabloně používáme funkci webactivity k volání Azure Logic Apps k nastavení hodnoty horní meze v úložišti objektů BLOB. Případně můžete použít Azure SQL Database k uložení. K aktualizaci hodnoty použijte aktivitu uložené procedury.

      Nejprve musíte vytvořit aplikaci logiky, jak ukazuje následující obrázek. Pak vložte **adresu URL post protokolu HTTP**.

      ![Konfigurace aplikace logiky](media/load-sap-bw-data/logic-app-config.png)

      1. Přejděte na Azure Portal. Vyberte novou **Logic Apps** službu. Vyberte **+ prázdná aplikace logiky** , abyste přešli na **Logic Apps Designer**.

      2. Vytvoří Trigger, **když se přijme požadavek HTTP**. Text požadavku HTTP určete následujícím způsobem:

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

      3. Přidejte akci **vytvořit objekt BLOB** . Pro **cestu ke složce** a **název objektu BLOB** použijte stejné hodnoty, které jste nakonfigurovali dříve v *HighWatermarkBlobContainer + HighWatermarkBlobDirectory* a *HighWatermarkBlobName*.

      4. Vyberte **Uložit**. Pak zkopírujte hodnotu **http post URL** , která se použije v kanálu Data Factory.

4. Po zadání parametrů kanálu Data Factory vyberte **ladit**  >  **Dokončit** , aby se spustila spuštění pro ověření konfigurace. Nebo vyberte **publikovat** a publikujte všechny změny a pak vyberte **Přidat Trigger** pro spuštění běhu.

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW otevřít cílové konfigurace centra

V této části se seznámíte s konfigurací SAP BW straně pro použití konektoru SAP BW Open hub v Data Factory ke kopírování dat.

### <a name="configure-delta-extraction-in-sap-bw"></a>Konfigurace extrakce rozdílů v SAP BW

Pokud potřebujete jak historické kopírování, tak přírůstkové kopírování, nebo jenom přírůstkové kopírování, nakonfigurujte extrakci Delta v SAP BW.

1. Vytvořte umístění otevřeného centra. OHD můžete vytvořit v RSA1 transakce SAP, který automaticky vytvoří požadovanou transformaci a proces přenosu dat. Použijte následující nastavení:

   - **ObjectType**: můžete použít libovolný typ objektu. Tady jako příklad používáme **InfoCube** .
   - **Cílový typ**: vyberte **Databázová tabulka**.
   - **Klíč tabulky**: vyberte **technický klíč**.
   - **Extrakce**: vyberte **zachovat data a vložte záznamy do tabulky**.

   ![Dialogová okna pro extrakci rozdílových SAP BW OHD](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Dialogové okno vytvořit SAP BW OHD Delta2 pro extrakci](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Můžete zvýšit počet paralelně spuštěných pracovních procesů SAP pro DTP:

   ![Snímek obrazovky ukazuje nastavení pro paralelní zpracování, kde můžete vybrat počet paralelních procesů pro D T P.](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Naplánujte DTP v řetězcích procesu.

   Rozdílová DTP pro datovou krychli funguje pouze v případě, že nejsou potřebné řádky komprimovány. Ujistěte se, že komprese datové krychle není spuštěná před DTP k otevřené tabulce hub. Nejjednodušší způsob, jak to provést, je integrovat DTP do stávajících zřetězení procesů. V následujícím příkladu je DTP (do OHD) vložen do řetězce procesu mezi kroky *Úpravy* (souhrn souhrnu) a *sbalení* (komprese datové krychle).

   ![Vytvořit diagram toku SAP BW procesu zpracování](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Konfigurace úplné extrakce v SAP BW

Kromě extrakce rozdílů můžete chtít mít úplnou extrakci stejného SAP BW InfoProvider. To se obvykle používá v případě, že chcete provést úplné kopírování, ale ne přírůstkové, nebo chcete [extrakci Delta znovu synchronizovat](#resync-delta-extraction).

Pro stejný OHD nemůžete mít více než jeden DTP. Takže je třeba vytvořit další OHD před extrakcí Delta.

![Vytvořit úplnou SAP BW OHD](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Pro úplný OHD zatížení vyberte jiné možnosti než pro extrakci rozdílů:

- V OHD: Nastavte možnost **extrakce** k **odstranění dat a vložení záznamů**. V opačném případě budou data extrahována mnohokrát při opakování DTP v nestupném procesu.

- V DTP: nastavte **režim extrakce** na **Full**. Je nutné změnit automaticky vytvořené DTP z **rozdílové** na hodnotu **Full** hned po vytvoření OHD, jak ukazuje tento obrázek:

   ![Dialog vytvořit SAP BW OHD nakonfigurovaný pro "úplnou" extrakci](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- V neData Factoryovém konektoru s otevřeným rozbočovačem: vypnout **vyloučení posledního požadavku**. V opačném případě se nic neextrahuje.

Obvykle se celý DTP spouští ručně. Nebo můžete vytvořit řetězec procesu pro úplné DTP. Obvykle je to samostatný řetězec, který je nezávislý na vašich existujících řetězcích procesů. V obou případech se *ujistěte, že je DTP dokončený, než začnete s extrakcí pomocí Data Factory kopie*. V opačném případě se zkopírují jenom částečná data.

### <a name="run-delta-extraction-the-first-time"></a>Při prvním spuštění extrakce rozdílů

První extrakce Delta je technicky *kompletní extrakce*. Ve výchozím nastavení vyloučí konektor SAP BW Open hub poslední požadavek při kopírování dat. Pro první extrakci rozdílů neextrahují žádná data Data Factory aktivity kopírování, dokud následná DTP negeneruje rozdílová data v tabulce s ID samostatného požadavku. Neexistují dva způsoby, jak se vyhnout tomuto scénáři:

- Vypněte možnost **vyloučit poslední požadavek** pro první extrakci rozdílů. Ujistěte se, že první rozdílová DTP je dokončená před prvním spuštěním extrakce Delta.
-  Použijte postup pro opětovné synchronizaci extrakce Delta, jak je popsáno v následující části.

### <a name="resync-delta-extraction"></a>Opětovná synchronizace Delta pro extrakci

Následující scénáře mění data v SAP BW datové krychle, ale nepovažují se za rozdílové DTP:

- SAP BW selektivního odstranění (řádků pomocí libovolné podmínky filtru)
- Odstranění žádosti o SAP BW (chybné požadavky)

Cílové umístění centra pro SAP není cílem dat řízených datovými tržiště (ve všech SAP BW podporujících balíčky od 2015). Takže můžete data z datové krychle odstranit, aniž byste museli měnit data v OHD. Pak je nutné znovu synchronizovat data datové krychle s Data Factory:

1. Spusťte úplnou extrakci v Data Factory (pomocí úplného DTPu v SAP).
2. Odstraní všechny řádky v otevřené tabulce hub pro rozdílový DTP.
3. Nastavte stav rozdílového DTP, který se **načte**.

Po tomto případě budou všechna další rozdílová DTPs a Data Factory rozdílové extrakce fungovat podle očekávání.

Chcete-li nastavit stav rozdílového **DTP, můžete** použít následující možnost k ručnímu spuštění rozdílového DTP:

*Žádné Přenos dat; Stav rozdílu ve zdroji: načteno*

## <a name="next-steps"></a>Další kroky

Další informace o podpoře SAP BW Open hub Connector:

> [!div class="nextstepaction"]
>[Konektor Open hub pro SAP Business Warehouse](connector-sap-business-warehouse-open-hub.md)
