---
title: "Konfigurace sestav pro zálohování Azure"
description: "V tomto článku bude zmíněn konfigurace sestavy Power BI pro Azure Backup pomocí trezoru služeb zotavení."
services: backup
documentationcenter: 
author: JPallavi
manager: vijayts
editor: 
ms.assetid: 86e465f1-8996-4a40-b582-ccf75c58ab87
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/10/2017
ms.author: pajosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f51d85da1bda0d9483883301b71c20e405724b9b
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="configure-azure-backup-reports"></a>Konfigurace sestav Azure Backup
V tomto článku bude zmíněn kroky konfigurace sestav pro Azure Backup pomocí trezoru služeb zotavení a chcete dostat k sestavám pomocí Power BI. Po provedení těchto kroků, můžete přímo přejít k Power BI. Chcete-li zobrazit všechny sestavy, přizpůsobení a vytváření sestav. 

## <a name="supported-scenarios"></a>Podporované scénáře
1. Azure Backup sestavy nejsou podporovány pro virtuální počítač Azure zálohování a zálohování soubor nebo složku, do cloudu pomocí agenta služeb zotavení Azure.
2. V tuto chvíli nepodporuje sestav pro Azure SQL, aplikace DPM a serveru Azure Backup.
3. Sestavy můžete zobrazit různé trezory a odběry, pokud stejný účet úložiště je nakonfigurovaný pro každou trezorů. Vybraný účet úložiště musí být ve stejné oblasti jako trezor služeb zotavení.
4. Četnost plánované aktualizace pro sestavy je 24 hodin v Power BI. Můžete také provést aktualizaci ad-hoc sestavy v Power BI, ve kterém případu nejnovější data v účtu úložiště zákazníka slouží pro vykreslení sestavy. 
5. Azure Backup sestavy nejsou aktuálně podporovány v národních cloudů.

## <a name="prerequisites"></a>Požadavky
1. Vytvoření [účtu úložiště Azure](../storage/common/storage-create-storage-account.md#create-a-storage-account) ho nakonfigurovat pro sestavy. Tento účet úložiště se používá pro ukládání souvisejících dat sestavy.
2. [Vytvořit účet Power BI](https://powerbi.microsoft.com/landing/signin/) zobrazení, přizpůsobení a vytváření vlastních sestav pomocí portálu Power BI.
3. Registrace poskytovatele prostředků **Microsoft.insights** Pokud není již zaregistrován, s předplatným účtu úložiště a také s předplatným trezor služeb zotavení povolit reporting tok úložiště dat do účet. O stejnou akci, je nutné přejít na portálu Azure > předplatné > Zprostředkovatelé prostředků a zkontrolujte u tohoto zprostředkovatele a zaregistrujte ho. 

## <a name="configure-storage-account-for-reports"></a>Konfigurace účtu úložiště pro sestavy
Použijte následující postup ke konfiguraci účtu úložiště pro trezor služeb zotavení pomocí portálu Azure. Jedná se o jednorázovou konfiguraci a jakmile je nakonfigurovaný účet úložiště, můžete přejít na Power BI přímo k zobrazení balíček obsahu a využívejte sestavy.
1. Pokud již máte otevřete trezoru služeb zotavení, pokračujte dalším krokem. Pokud nemáte otevřený trezor služeb zotavení, ale jsou na portálu Azure, klikněte na tlačítko **všechny služby**.

   * V seznamu prostředků zadejte **Recovery Services**.
   * Seznam se průběžně filtruje podle zadávaného textu. Až uvidíte **Trezory Recovery Services**, klikněte na ně.

      ![Vytvoření trezoru Recovery Services – krok 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     Objeví se seznam trezorů Služeb zotavení. Ze seznamu trezorů Služeb zotavení vyberte trezor.

     Otevře se řídicí panel vybraného trezoru.
2. Ze seznamu položek, která se zobrazí pod trezoru, klikněte na tlačítko **zálohování sestavy** části monitorování a sestavy ke konfiguraci účtu úložiště pro sestavy.

      ![Vyberte zálohu sestavy nabídky položky krok 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. V okně Zálohování sestavy klikněte na **nastavení diagnostiky** odkaz. Otevře se nastavení diagnostiky uživatelského rozhraní, která se používá k předání dat do účtu úložiště zákazníka.

      ![Povolíte diagnostiku krok 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Klikněte na odkaz **zapněte diagnostiku**. Otevře se uživatelské rozhraní pro konfiguraci účtu úložiště. 

      ![Zapněte diagnostiku krok 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. Do pole zadejte název nastavení **název** a vyberte **archivu do účtu úložiště** políčko tak, aby vytváření sestav dat můžete spustit v toku k účtu úložiště.

      ![Povolíte diagnostiku krok 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Klikněte na výběr účtu úložiště a vyberte odpovídající předplatné a úložiště účet ze seznamu pro uložení sestavy dat a klikněte na tlačítko **OK**.

      ![Vyberte úložiště účet krok 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Vyberte **AzureBackupReport** políčko části protokolu a přesuňte jezdec na dobu uchování vyberte data pro vytváření sestav. Data v účtu úložiště pro vytváření sestav je udržováno dobu vybraných pomocí této posuvníku.

      ![Uložit úložiště účet krok 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Zkontrolujte všechny změny a klikněte na **Uložit** tlačítko nahoře, jak je znázorněno na obrázku výše. Tato akce zajistí, aby se ukládají všechny změny a účet úložiště je nyní nakonfigurována pro ukládání dat, vytváření sestav.

9. Nastavení diagnostiky tabulky by měl nyní zobrazit nové nastavení povolena pro trezor. Pokud se nezobrazuje, aktualizuje v tabulce najdete aktualizované nastavení.

      ![Nastavení diagnostiky zobrazení kroku 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Jakmile nakonfigurujete sestavy uložením účet úložiště, měli byste **počkejte 24 hodin** dokončit počáteční data nabízená instalace. Balíček obsahu Azure Backup v Power BI importujte pouze po uplynutí této doby. Odkazovat [v části Nejčastější dotazy](#frequently-asked-questions) další podrobnosti. 
>
>

## <a name="view-reports-in-power-bi"></a>Zobrazit sestavy v Power BI 
Po konfiguraci účtu úložiště pro sestavy na základě trezoru služeb zotavení, trvá přibližně 24 hodin pro vytváření sestav dat spuštění toku v. Po 24 hodinách nastavení účtu úložiště pro zobrazení sestavy v Power BI použijte následující kroky:
1. [Přihlaste se](https://powerbi.microsoft.com/landing/signin/) do Power BI.
2. Klikněte na tlačítko **načíst Data** a klikněte na tlačítko **získat** pod **služby** v knihovně obsahu Pack. Použijte postup uvedený v [dokumentace Power BI přístup k balíčku obsahu](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-packs-services/).

     ![Importovat balíček obsahu](./media/backup-azure-configure-reports/content-pack-import.png)
3. Typ **Azure Backup** v panelu vyhledávání a klikněte na **ho získat**.

      ![Získat balíček obsahu](./media/backup-azure-configure-reports/content-pack-get.png)
4. Zadejte název účtu úložiště nakonfigurovali v kroku 5 výše a klikněte na **Další** tlačítko.

    ![Zadejte název účtu úložiště.](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Zadejte klíč účtu úložiště pro tento účet úložiště. Můžete [zobrazení a zkopírování přístupových klíčů k úložišti](../storage/common/storage-create-storage-account.md#manage-your-storage-account) přechodem na účtu úložiště na portálu Azure. 

     ![Zadejte účet úložiště](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. Klikněte na tlačítko **přihlášení** tlačítko. Po přihlášení úspěšné, můžete získat **import dat** oznámení.

    ![Import balíčku obsahu](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    Po určité době získáte **úspěch** oznámení po dokončení importu. Může trvat déle importovat balíček obsahu, pokud je velké množství dat v účtu úložiště.
    
    ![Importovat balíček obsahu úspěch](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>
    
7. Po úspěšném importu **Azure Backup** balíček obsahu je zobrazen v **aplikace** v navigačním podokně. V seznamu jsou nyní řídicí panel Azure Backup, sestavy a datové sady se žlutou hvězdičku označující nově importované sestavy. 

     ![Balíček obsahu Azure Backup](./media/backup-azure-configure-reports/content-pack-azure-backup.png) <br/>
     
8. Klikněte na tlačítko **Azure Backup** pod řídicí panely, které zobrazuje sadu definovaného klíče sestavy.

      ![Řídicí panel Azure Backup](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Kompletní sadu sestavy zobrazíte kliknutím na žádnou sestavu v řídicím panelu.

      ![Azure stavu úlohy zálohování](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Klikněte na každé kartě v sestavách zobrazovat sestavy v této oblasti.

      ![Azure Backup sestavy karty](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Nejčastější dotazy
1. **Jak zkontrolovat, zda bylo zahájeno vytváření sestav dat předávaných v účtu úložiště?**
    
    Můžete přejít do nakonfigurovaný účet úložiště a vyberte kontejnery. Pokud kontejner má záznam pro insights-logs-azurebackupreport, znamená to, že data pro vytváření sestav byla spuštěna toku.

2. **Co je četnost datová oznámení k účtu úložiště a balíček obsahu Azure Backup v Power BI?**

   Pro uživatele, dne 0 to by trvat přibližně 24 hodin pro zápis dat do účtu úložiště. Po dokončení této počáteční nabízené data se aktualizují s následující frekvence uvedený na následujícím obrázku. 
      * Data související s **úlohy, výstrahy, zálohování položek, trezory, chráněných serverů a zásady** vložena do účtu úložiště zákazníka jako a při protokolování.
      * Data související s **úložiště** vložena do účtu úložiště zákazníka každých 24 hodin.
   
    ![Četnost nabízené Azure sestavy zálohování dat](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  Má Power BI [plánovaná aktualizace jednou denně](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Manuální aktualizace dat můžete provádět v Power BI pro balíček obsahu.

3. **Jak dlouho může zachovat sestavy?** 

   Při konfiguraci účtu úložiště, můžete vybrat dobu uchování generování sestav dat v účtu úložiště (s použitím kroku 6 v účtu úložiště konfigurace pro oddíl sestavy výše). Kromě toho, že můžete [sestavy doplňku analyzovat v aplikaci excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) a uložit je delší dobu uchovávání dat, podle potřeb. 

4. **Zobrazí všechna data v sestavách po dokončení konfigurace účtu úložiště?**

   Všechna data, které jsou generovány po **"účet úložiště konfigurace"** se vloží do účtu úložiště a bude k dispozici v sestavách. Ale **nejsou v průběhu úlohy nabídnutých** pro vytváření sestav. Jakmile úloha neskončí nebo neselže, odesláním do sestavy.

5. **Pokud účet úložiště, který chcete zobrazit sestavy I již nakonfigurována, lze změnit konfiguraci použít jiný účet úložiště?** 

   Ano, můžete změnit konfiguraci tak, aby odkazoval na jiný účet úložiště. Účet nově nakonfigurovaná úložiště by měl používat při připojování k balíček obsahu Azure Backup. Kromě toho po nakonfigurování jiný účet úložiště by toku nová data v rámci tohoto účtu úložiště. Ale starší data (před změnou konfigurace) by stále na starší účet úložiště.

6. **Můžete zobrazit různé trezory a odběry sestav?** 

   Ano, můžete konfigurovat stejný účet úložiště v rámci různých trezory k zobrazení sestav mezi trezoru. Můžete také konfigurovat stejný účet úložiště pro trezorů v rámci předplatných. Pak můžete tento účet úložiště při připojování k balíček obsahu Azure Backup v Power BI k zobrazení sestavy. Vybraný účet úložiště by však být ve stejné oblasti jako trezor služeb zotavení.
   
## <a name="troubleshooting-errors"></a>Řešení potíží s chybami
| Detaily chyby | Řešení |
| --- | --- |
| Po nastavení účtu úložiště pro zálohování sestavy **účet úložiště** stále zobrazuje **není nakonfigurováno**. | Pokud jste úspěšně nakonfigurovaný účet úložiště, generování sestav dat bude procházet v i přes tento problém. Chcete-li vyřešit tento problém, přejděte na portálu Azure > všechny služby > Nastavení pro diagnostiku > RS trezoru > Upravit nastavení. Odstranit dřív nakonfigurovaná nastavení a vytvořit nové nastavení v okně stejné. Tentokrát nastavte hodnotu pole **název** k **služby**. To by měl zobrazit účet úložiště. |
|Po importu Azure Backup obsahu pack v Power BI, chyba **404 kontejneru nebyla nalezena** se zobrazí. | Dle pokynů v tomto dokumentu je nutné počkat 24 hodin po konfiguraci sestavy v trezoru služeb zotavení zobrazovat správně v Power BI. Pokud se pokusíte mít přístup k sestavám než 24 hodin, zobrazí se tato chyba, vzhledem k tomu, že dokončení dat ještě není přítomen a zobrazit sestavy platný. |

## <a name="next-steps"></a>Další postup
Teď, když jste nakonfigurovali účet úložiště a importované balíček obsahu Azure Backup, dalším krokem je pomocí vytváření sestav datového modelu vytvářet sestavy a přizpůsobit tyto sestavy. Další podrobnosti naleznete v následujících článcích.

* [Pomocí služby Azure Backup reporting datový model](backup-azure-reports-data-model.md)
* [Filtrování sestavy v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Vytváření sestav v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)

