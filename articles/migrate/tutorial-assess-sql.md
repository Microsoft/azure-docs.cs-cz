---
title: Kurz pro vyhodnocení instancí SQL pro migraci do spravované instance Azure SQL a Azure SQL Database
description: Naučte se, jak vytvořit vyhodnocení pro Azure SQL v Azure Migrate
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 02/07/2021
ms.openlocfilehash: 9b33890d53f67eee870b42462a65b4a0b7ba9981
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102055420"
---
# <a name="tutorial-assess-sql-instances-for-migration-to-azure-sql"></a>Kurz: vyhodnocení instancí SQL pro migraci do Azure SQL

Jako součást cesty k migraci do Azure vyhodnocujete vaše místní úlohy a měříte připravenost k cloudu, identifikaci rizik a odhadu nákladů a složitosti.
V tomto článku se dozvíte, jak vyhodnotit zjištěné databáze instancí SQL Server v přípravě na migraci do Azure SQL pomocí nástroje Azure Migrate: Discovery and Assessment Tool.

> [!Note]
> Zjišťování a hodnocení instancí SQL Server a databází spuštěných ve vašem prostředí VMware je teď ve verzi Preview. Pokud chcete tuto funkci vyzkoušet, použijte [**tento odkaz**](https://aka.ms/AzureMigrate/SQL) a vytvořte projekt v oblasti **Austrálie – východ**. Pokud již máte projekt v oblasti Austrálie – východ a chcete tuto funkci vyzkoušet, na portálu se ujistěte, že jste splnili tyto [**požadavky**](how-to-discover-sql-existing-project.md).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Spusťte posouzení na základě konfigurace serveru a dat o výkonu.
> * Kontrola posouzení Azure SQL 

> [!NOTE]
> Kurzy ukazují nejrychlejší cestu k vyzkoušení scénáře a používají výchozí možnosti, pokud je to možné. 


## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.

- Než budete postupovat podle tohoto kurzu, abyste vyhodnotili SQL Server instance pro migraci do Azure SQL, ujistěte se, že jste zjistili instance SQL, které chcete vyhodnotit pomocí Azure Migrate zařízení, [postupujte podle tohoto kurzu](tutorial-discover-vmware.md) .

## <a name="run-an-assessment"></a>Spuštění posouzení
Proveďte posouzení následujícím způsobem:
1. Na stránce **přehled** > **Windows, Linux a SQL Server** klikněte na možnost **zhodnotit a migrovat servery**.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Stránka s přehledem pro Azure Migrate":::
2. Na **Azure Migrate: zjišťování a hodnocení**, klikněte na **zhodnotit** a vyberte typ posouzení jako **Azure SQL**.
    :::image type="content" source="./media/tutorial-assess-sql/assess.png" alt-text="Rozevírací seznam pro výběr typu posouzení jako Azure SQL":::
3. V tématu **vyhodnocování serverů** > bude možné zobrazit typ posouzení, který je předem vybraný jako **Azure SQL** , a zdroj zjišťování byl ve výchozím nastavení pro **servery zjištěné ze Azure Migrate zařízení**.

4. Klikněte na **Upravit** a zkontrolujte vlastnosti posouzení.
     :::image type="content" source="./media/tutorial-assess-sql/assess-servers-sql.png" alt-text="Tlačítko Upravit, ze kterého lze přizpůsobit vlastnosti posouzení":::
5. Ve vlastnostech posouzení > **cílové vlastnosti**:
    - V části **cílové umístění** zadejte oblast Azure, do které chcete migrovat. 
        - Doporučení pro konfiguraci a náklady Azure SQL jsou založená na umístění, které zadáte. 
    - V **cílovém typu nasazení**
        - Vyberte **doporučeno**, pokud chcete Azure Migrate vyhodnotit připravenost instancí SQL pro migraci na Azure SQL mi a Azure SQL DB a doporučte si možnost optimálního cíle pro nasazení, cílovou úroveň, konfiguraci Azure SQL a měsíční odhady. [Další informace](concepts-azure-sql-assessment-calculation.md)
        - Vyberte **Azure SQL DB**, pokud chcete vyhodnotit připravenost instancí SQL pro migraci jenom na Azure SQL Database, a Projděte si cílovou úroveň, konfiguraci Azure SQL a měsíční odhady.
        - Vyberte **Azure SQL mi**, pokud chcete vyhodnotit připravenost instancí SQL pro migraci jenom na spravovanou instanci Azure SQL, a Projděte si cílovou úroveň, konfiguraci Azure SQL a měsíční odhady.
    - V části **Rezervovaná kapacita** určete, jestli chcete po migraci použít rezervovanou kapacitu pro SQL Server.
        - Pokud vyberete možnost rezervované kapacity, nemůžete zadat "slevu" (%).

6. Ve vlastnostech posouzení > **kritéria hodnocení**:
    - Kritéria změny velikosti jsou nastavená jako výchozí na **základě výkonu** , což znamená, že Azure migruje shromáždí metriky výkonu související s instancemi SQL a databázemi, které spravuje, a doporučí optimální Azure SQL Database nebo konfiguraci spravované instance Azure SQL. Můžete zadat:
        - **Historie výkonu** k označení doby trvání dat, na které chcete vyhodnotit základ posouzení. (Výchozí hodnota je jeden den)
        - **Percentil – využití** k označení hodnoty percentilu, kterou chcete použít pro ukázku výkonu. (Výchozí hodnota je 95. percentil)
    - V části **faktor komfortu** určete vyrovnávací paměť, kterou chcete použít při posuzování. Tyto účty jsou důležité pro problémy, jako je sezónní využití, historie krátkého výkonu a pravděpodobný nárůst využití v budoucnu. Pokud například použijete faktor komfortu 2: 
        
        **Komponenta** | **Efektivní využití** | **Přidat faktor pohodlí (2,0)**
        --- | --- | ---
        Cores | 2  | 4
        Memory (Paměť) | 8 GB | 16 GB
   
7. V **ceně**:
    - V **nabídce nebo licenční program** zadejte nabídku Azure, pokud jste zaregistrovaní. V současné době můžete vybírat jenom z průběžných plateb a průběžných plateb podle aktuálního využití pro vývoj/testování. 
        - Můžete využít další zlevněnou kapacitu, a to použitím rezervované kapacity a Zvýhodněné hybridní využití Azure nad nabídkou průběžných plateb. 
        - Můžete použít Zvýhodněné hybridní využití Azure nad průběžnými platbami podle aktuálního využití pro vývoj/testování. Posouzení aktuálně nepodporuje použití rezervované kapacity na základě nabídky pro vývoj/testování s průběžnými platbami.
    - V části **úroveň služby** vyberte nejvhodnější možnost úrovně služby, která bude vyhovovat vašim potřebám vaší firmy pro migraci do Azure SQL Database nebo spravované instance Azure SQL: 
        - Vyberte možnost **doporučeno** , pokud chcete, Azure Migrate doporučit pro vaše servery nejlepší sadu služeb. To může být pro obecné účely nebo pro podnikání důležité. Další informace
        - Vyberte možnost **pro obecné účely** , pokud chcete konfiguraci Azure SQL navrženou pro úlohy orientované na rozpočet.
        - Vyberte **pro důležité obchodní informace** , pokud chcete, aby konfigurace Azure SQL určená pro úlohy s nízkou latencí a vysokou odolností vůči chybám a rychlému převzetí služeb při selhání.
    - V části **sleva (%)** přidejte do nabídky Azure všechny slevy specifické pro předplatné, které obdržíte. Výchozí nastavení je 0 %.
    - V části **Měna** vyberte fakturační měnu vašeho účtu.
    - V **zvýhodněné hybridní využití Azure** určete, zda již máte licenci SQL Server. Pokud to uděláte a jsou pokryté s aktivními softwarovými zárukami SQL Server předplatných, můžete požádat o Zvýhodněné hybridní využití Azure při přenesení licencí do Azure.
    - Pokud provedete změny, klikněte na Uložit.
     :::image type="content" source="./media/tutorial-assess-sql/view-all.png" alt-text="Tlačítko Uložit ve vlastnostech posouzení":::
8. V **vyhodnocování serverů** > klikněte na další.
9.  V části **Vybrat servery pro vyhodnocení**  >  **názvu vyhodnocení** > zadejte název posouzení.
10. V **Vyberte nebo vytvořte skupinu** > vyberte **vytvořit novou** a zadejte název skupiny.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-add-servers.png" alt-text="Tlačítko umístění nové skupiny":::
11. Vyberte zařízení a vyberte servery, které chcete přidat do skupiny. Potom klikněte na Další.
12. V části **Revize + vytvořit posouzení** zkontrolujte podrobnosti posouzení a kliknutím na vytvořit posouzení vytvořte skupinu a spusťte posouzení.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-create.png" alt-text="Umístění tlačítka revize a vytvořit posouzení.":::
13. Po vytvoření posouzení přejděte na **Windows, Linux a SQL Server**  >  **Azure Migrate: dlaždici zjišťování a hodnocení** > klikněte na číslo vedle SQL vyhodnocení pro Azure.
     :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-navigation.png" alt-text="Navigace k vytvořenému posouzení":::
15. Klikněte na název posouzení, který chcete zobrazit.

> [!NOTE]
> Protože posuzování Azure SQL jsou posouzení na základě výkonu, doporučujeme, abyste před vytvořením posouzení počkali aspoň jeden den od spuštění zjišťování. To poskytuje čas ke shromažďování dat o výkonu s větší jistotou. Pokud vaše zjišťování stále probíhá, připravenost vašich instancí SQL bude označena jako **neznámá**. V ideálním případě po zahájení zjišťování **počkejte na dobu trvání výkonu, kterou zadáte (den/týden/měsíc)** , a vytvořte nebo přepočítejte hodnocení pro hodnocení s vysokou mírou jistoty. 

## <a name="review-an-assessment"></a>Kontrola posouzení

**Zobrazení posouzení**:

1. **Windows, Linux a SQL Server**  >  **Azure Migrate: zjišťování a hodnocení** > klikněte na číslo vedle posouzení Azure SQL.
2. Klikněte na název posouzení, který chcete zobrazit. Příklad (odhad a náklady pouze pro příklad): :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-summary.png" alt-text="Přehled vyhodnocení SQL":::
3. Přečtěte si Souhrn posouzení. Můžete také upravit vlastnosti posouzení nebo přepočítat posouzení.

#### <a name="discovered-items"></a>Zjištěné položky

Označuje počet serverů SQL, instancí a databází, které byly posouzeny v tomto posouzení.
    
#### <a name="azure-readiness"></a>Připravenost pro Azure

To indikuje distribuci vyhodnocených instancí SQL: 
    
**Cílový typ nasazení (ve vlastnostech posouzení)** | **Připravenosti**   
--- | --- |
**Doporučeno** |  Připraveno k Azure SQL Database připravené pro Azure SQL Managed instance, která může být připravená pro virtuální počítač Azure, neznámou připravenost (pro případ, že zjišťování probíhá nebo že se opravily některé problémy zjišťování)
**Azure SQL DB** nebo **Azure SQL mi** | Připraveno pro Azure SQL Database nebo Azure SQL Managed instance, není připravená pro Azure SQL Database nebo Azure SQL Managed instance, neznámá připravenost (pro případ, že zjišťování probíhá, nebo když jsou vyřešené některé problémy zjišťování)
     
Můžete přejít k podrobnostem a pochopit podrobnosti o problémech s migrací a upozorněních, která můžete opravit před migrací do Azure SQL. [Další informace](concepts-azure-sql-assessment-calculation.md) Můžete si také projít Doporučené konfigurace Azure SQL pro migraci na databáze SQL Azure a/nebo spravované instance.
    
#### <a name="azure-sql-database-and-managed-instance-cost-details"></a>Podrobnosti o Azure SQL Database a náklady na spravované instance

Odhad měsíčních nákladů zahrnuje náklady na výpočetní prostředky a úložiště pro konfigurace Azure SQL, které odpovídají doporučeným Azure SQL Database a/nebo typu nasazení spravované instance SQL Azure. [Další informace](concepts-azure-sql-assessment-calculation.md#calculate-monthly-costs)


### <a name="review-readiness"></a>Kontrola připravenosti

1. Klikněte na **připravenost Azure SQL**.
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-readiness.png" alt-text="Podrobnosti připravenosti na Azure SQL":::
1. V Azure SQL Readiness si prostudujte připravenost **Azure SQL DB** a **připravenost Azure SQL mi** pro hodnocené instance SQL:
    - **Připraveno**: instance je připravená k migraci do Azure SQL DB/mi bez jakýchkoli potíží s migrací nebo upozornění. 
        - Připraveno (ikona s hypertextovými odkazy a modrými informacemi): instance je připravena k migraci do Azure SQL DB/MI bez jakýchkoli potíží s migrací, ale obsahuje několik upozornění migrace, která potřebujete zkontrolovat. Kliknutím na hypertextový odkaz můžete zkontrolovat upozornění na migraci a doporučené pokyny k nápravě: :::image type="content" source="./media/tutorial-assess-sql/assess-ready.png" alt-text="posouzení se stavem připravenosti"::: .       
    - **Nepřipraveno**: instance má jeden nebo více problémů s migrací pro migraci na Azure SQL DB/mi. Můžete kliknout na hypertextový odkaz a zkontrolovat problémy s migrací a doporučené pokyny k nápravě.
    - **Neznámé**: Azure Migrate nemůže vyhodnotit připravenost, protože zjišťování probíhá nebo došlo k problémům během zjišťování, které je potřeba opravit z okna oznámení. Pokud problém přetrvá, obraťte se na podporu Microsoftu. 
1. Prohlédněte si doporučený typ nasazení pro instanci SQL, která je určena dle matice níže:

    - **Cílový typ nasazení** (jak je vybraný ve vlastnostech posouzení): **Doporučené**

        **Připravenost Azure SQL DB** | **Připravenost Azure SQL MI** | **Doporučený typ nasazení** | **Počítá se konfigurace Azure SQL a odhad nákladů?**
         --- | --- | --- | --- |
        Připraveno | Připraveno | Azure SQL DB nebo Azure SQL MI další [informace](concepts-azure-sql-assessment-calculation.md#recommended-deployment-type) | Yes
        Připraveno | Nepřipraveno nebo neznámo | Azure SQL DB | Yes
        Nepřipraveno nebo neznámo | Připraveno | Azure SQL MI | Yes
        Nepřipraveno | Nepřipraveno | Možná připravené na virtuální počítač Azure další [informace](concepts-azure-sql-assessment-calculation.md#potentially-ready-for-azure-vm) | No
        Nepřipraveno nebo neznámo | Nepřipraveno nebo neznámo | Neznámý | No
    
    - **Cílový typ nasazení** (jak je vybraný ve vlastnostech posouzení): **Azure SQL DB**
    
        **Připravenost Azure SQL DB** | **Počítá se konfigurace Azure SQL a odhad nákladů?**
        --- | --- |
        Připraveno | Yes
        Nepřipraveno | Ne
        Není známo | No
    
    - **Cílový typ nasazení** (jak je vybraný ve vlastnostech posouzení): **Azure SQL mi**
    
        **Připravenost Azure SQL MI** | **Počítá se konfigurace Azure SQL a odhad nákladů?**
         --- | --- |
        Připraveno | Yes
        Nepřipraveno | Ne
        Není známo | No

4. Kliknutím na název instance přejdete k podrobnostem o počtu uživatelských databází, podrobnostech instancí, včetně vlastností instance, výpočetním (vymezeném na instanci) a podrobnostech úložiště zdrojové databáze.
5. Kliknutím na počet uživatelských databází zkontrolujte seznam databází a jejich podrobnosti. Příklad (odhad a náklady pouze pro příklad): :::image type="content" source="./media/tutorial-assess-sql/assessment-db.png" alt-text="Podrobnosti instance SQL":::
5. Pokud chcete zkontrolovat problémy a upozornění migrace konkrétního cílového typu nasazení, klikněte na zkontrolovat podrobnosti ve sloupci problémy s migrací. 
    :::image type="content" source="./media/tutorial-assess-sql/assessment-db-issues.png" alt-text="Problémy a upozornění migrace databáze":::

### <a name="review-cost-estimates"></a>Přehled odhadovaných nákladů
V souhrnu posouzení se zobrazuje Odhadované měsíční náklady na výpočetní prostředky a úložiště pro konfigurace Azure SQL, které odpovídají doporučeným typům nasazení Azure SQL Database nebo Managed Instances.

1. Zkontrolujte měsíční celkové náklady. Náklady jsou shrnuté pro všechny instance SQL v hodnocené skupině.
    - Odhad nákladů vychází z Doporučené konfigurace Azure SQL pro instanci. 
    - Zobrazí se odhadované měsíční náklady na výpočetní prostředky a úložiště. Příklad (odhad a náklady pouze pro příklad):
    
    :::image type="content" source="./media/tutorial-assess-sql/assessment-sql-cost.png" alt-text="Podrobnosti o nákladech":::

1. Můžete přejít k podrobnostem na úrovni instance, abyste viděli konfiguraci a odhad nákladů Azure SQL na úrovni instance.  
1. Můžete také přejít k podrobnostem v seznamu databáze a zkontrolovat konfiguraci Azure SQL a odhad nákladů na databázi, když se doporučuje konfigurace Azure SQL Database.

### <a name="review-confidence-rating"></a>Kontrola hodnocení spolehlivosti
Azure Migrate přiřadí hodnocení spolehlivosti všem odhadům SQL Azure na základě dostupnosti datových bodů výkonu a využití potřebných k výpočtu posouzení pro všechny hodnocené instance a databáze SQL. Hodnocení je od jedné hvězdičky (nejnižší) až pěti hvězdiček (nejvyšší).
Hodnocení spolehlivosti vám pomůže odhadnout spolehlivost doporučení velikosti v posouzení. Hodnocení spolehlivosti je následující:

**Dostupnost datového bodu** | **Hodnocení spolehlivosti**
--- | ---
0 až 20 % | 1 hvězdička
21 až 40 % | 2 hvězdičky
41 až 60 % | 3 hvězdičky
61 až 80 % | 4 hvězdičky
81 až 100 % | 5 hvězdiček

[Přečtěte si další informace](concepts-azure-sql-assessment-calculation.md#confidence-ratings) o hodnocení spolehlivosti.


## <a name="next-steps"></a>Další kroky

- [Přečtěte si další informace](concepts-azure-sql-assessment-calculation.md) o výpočtu odhadů Azure SQL.
- Začněte migrovat instance a databáze SQL pomocí [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview).
