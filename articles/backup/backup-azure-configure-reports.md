---
title: Konfigurace sestav Azure Backup
description: Konfigurace sestav Power BI pro službu Azure Backup pomocí trezoru služby Recovery Services.
services: backup
author: adigan
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: adigan
ms.openlocfilehash: e93c51365adfc867082e180d8e4db804d02003ca
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55297720"
---
# <a name="configure-azure-backup-reports"></a>Konfigurace sestav Azure Backup
Tento článek popisuje postup konfigurace sestav Azure Backup pomocí trezoru služby Recovery Services. Také ukazuje, jak získat přístup k sestavám pomocí Power BI. Po dokončení těchto kroků můžete přejít přímo do Power BI k zobrazení, přizpůsobení a vytváření sestav.

> [!IMPORTANT]
> Od 1. listopadu 2018 někteří zákazníci mohou podívejte se na problémy při načítání dat v aplikaci Azure Backup v Power BI, jak říkáte "jsme našli nějaké znaky navíc na konci vstupu JSON. Výjimku vyvolalo rozhraní IDataReader.“
Příčinou je změna formátu dat načítaných do účtu úložiště.
Stáhněte si prosím nejnovější aplikace (verze 1.8) k tomuto problému vyhnout.
>
>

## <a name="supported-scenarios"></a>Podporované scénáře
- Sestavy Azure Backup podporuje pro zálohování virtuálních počítačů Azure a souborům a složkám zálohování do cloudu pomocí agenta Azure Recovery Services.
- Sestavy pro Azure SQL Database, sdílených složek Azure, Data Protection Manager a Azure Backup serveru nejsou v tuto chvíli nepodporuje.
- Sestavy můžete zobrazit napříč trezory a předplatných, je-li stejný účet úložiště je nakonfigurovaný pro všechny tyto trezory. Vybraný účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.
- Četnost naplánované aktualizace pro sestavy je 24 hodin v Power BI. Můžete také provést aktualizaci ad-hoc sestav v Power BI. V takovém případě nejnovější data v účtu úložiště zákazníka slouží k vykreslení sestavy.

## <a name="prerequisites"></a>Požadavky
- Vytvoření [účtu služby Azure storage](../storage/common/storage-quickstart-create-account.md) ho nakonfigurovat pro sestavy. Tento účet úložiště se používá k ukládání dat týkajících se sestavy.
- [Vytvoření účtu Power BI](https://powerbi.microsoft.com/landing/signin/) Pokud chcete zobrazit, upravit a vytvořit vlastní sestavy s použitím portálu Power BI.
- Registrace poskytovatele prostředků **Microsoft.insights**, pokud není již zaregistrován. Předplatná pro účet úložiště a trezor služby Recovery Services tak, aby data sestavy můžete tok do účtu úložiště. Chcete-li provést tento krok, přejděte na webu Azure portal vyberte **předplatné** > **poskytovatelů prostředků**a vyhledat ho zaregistrovat tohoto poskytovatele.

## <a name="configure-storage-account-for-reports"></a>Nakonfigurujte účet úložiště pro sestavy
Postupujte podle těchto kroků a nakonfigurujte účet úložiště pro trezor služby Recovery Services pomocí webu Azure portal. Jedná se o jednorázovou konfiguraci. Po dokončení konfigurace účtu úložiště, můžete přejít přímo do Power BI a zobrazit balíček obsahu a pomocí sestav.
1. Pokud už máte trezor služby Recovery Services, otevřít, přejděte k dalšímu kroku. Pokud nemáte trezor služby Recovery Services, otevřete na webu Azure Portal, vyberte **všechny služby**.

   * V seznamu prostředků zadejte **služby Recovery Services**.
   * Seznam se průběžně filtruje podle zadávaného textu. Když se zobrazí **trezory služby Recovery Services**, vyberte ji.

      ![Vytvoření trezoru Recovery Services – krok 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

   * Objeví se seznam trezorů Služeb zotavení. Ze seznamu trezorů Služeb zotavení vyberte trezor.

     Otevře se řídicí panel vybraného trezoru.
2. Ze seznamu položek, které se zobrazí v části trezor, v části **monitorování a sestavy** vyberte **Backup Reports** a nakonfigurujte účet úložiště pro sestavy.

      ![Vyberte zálohu sestavy nabídky položky krok 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. Na **Backup Reports** okno, vyberte **nastavení diagnostiky** odkaz. Tento odkaz otevře **nastavení diagnostiky** uživatelského rozhraní, který se používá k zápisu dat do účtu úložiště zákazníka.

      ![Povolení diagnostiky krok 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Vyberte **zapnout diagnostiku** otevřete uživatelské rozhraní pomocí konfigurace účtu úložiště. 

      ![Zapnout diagnostiku kroku 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. V **název** zadejte název nastavení. Vyberte **archivovat do účtu úložiště** zaškrtněte políčko, aby vytváření sestav dat můžete spustit přenášejí do účtu úložiště.

      ![5. kroku povolení diagnostiky](./media/backup-azure-configure-reports/select-setting-name.png)
6. Vyberte **účtu úložiště**, vyberte příslušné předplatné a účet úložiště, ze seznamu pro ukládání dat, vytváření sestav a vyberte **OK**.

      ![Vyberte úložiště účtu krok 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. V části **protokolu** vyberte **AzureBackupReport** zaškrtávací políčko. Přesuňte posuvník vybrat takovou dobu uchování tohoto údaje o chybách. Data v účtu úložiště pro vytváření sestav, zůstane dobu vybrali pomocí tohoto posuvníku.

      ![Uložit úložiště účtu krok 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Projděte si všechny změny a vyberte **Uložit**. Tato akce zajistí, že všechny změny se uložily a účet úložiště je nyní nakonfigurováno pro ukládání dat generování sestav.

9. **Nastavení diagnostiky** tabulka teď uvádí nové nastavení povolena pro trezor. Pokud není zobrazená, aktualizujte tabulky, které chcete zobrazit aktualizované nastavení.

      ![Zobrazení nastavení diagnostiky kroku 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Po dokončení konfigurace sestav pomocí ukládání účtu úložiště *počkejte po dobu 24 hodin* pro počáteční datová oznámení na dokončení. Aplikace Azure Backup v Power BI importujte pouze po uplynutí této doby. Další informace najdete v tématu [oddílu Nejčastější dotazy](#frequently-asked-questions). 
>
>

## <a name="view-reports-in-power-bi"></a>Zobrazení sestav v Power BI 
Když nakonfigurujete účet úložiště pro sestavy pomocí trezoru služby Recovery Services, trvá přibližně 24 hodin pro generování sestav dat spustit tok v. Po 24 hodinách nastavení účtu úložiště použijte následující postup zobrazení sestav v Power BI.
Pokud chcete přizpůsobit a sdílet sestavy, vytvořte pracovní prostor a proveďte následující kroky

1. [Přihlaste se](https://powerbi.microsoft.com/landing/signin/) do Power BI.
2. Vyberte **Načíst data**. V **další způsoby, jak vytvořit vlastní obsah**vyberte **balíčky obsahu služby**. Postupujte podle pokynů [dokumentaci k Power BI připojit ke službě](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/).

3. V **hledání** panelu, zadejte **Azure Backup** a vyberte **získat**.

      ![Získat balíček obsahu](./media/backup-azure-configure-reports/content-pack-get.png)
4. Zadejte název účtu úložiště, který jste nakonfigurovali v předchozím kroku 5 a vyberte **Další**.

    ![Zadejte název účtu úložiště.](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Pomocí metody ověřování "Klíče", zadejte klíč účtu úložiště pro tento účet úložiště. K [zobrazení a zkopírování přístupových klíčů k úložišti](../storage/common/storage-account-manage.md#access-keys), přejděte na svůj účet úložiště na webu Azure Portal. 

     ![Zadejte účet úložiště](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. Vyberte **přihlášení**. Po přihlášení úspěšné, zobrazí se oznámení o importu dat.

    ![Import balíčku obsahu](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    Po dokončení importu se zobrazí **úspěch** oznámení. Pokud je velký objem dat v účtu úložiště, může trvat o něco déle, než k importu balíčku obsahu.
    
    ![Importovat balíček obsahu úspěch](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>
    
7. Jakmile úspěšně, naimportuje data **Azure Backup** balíček obsahu je zobrazená v **aplikace** v navigačním podokně. V části **řídicí panely**, **sestavy**, a **datových sad**, v seznamu se teď zobrazí Azure Backup.
     
8. V části **řídicí panely**vyberte **Azure Backup**, zobrazí sadu připnuté klíče sestavy.

      ![Řídicí panel služby Azure Backup](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Chcete-li zobrazit úplnou sadu sestav, vyberte všechny sestavy na řídicím panelu.

      ![Stav úloh Azure Backup](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Vyberte každou kartu v sestavách zobrazovat sestavy v této oblasti.

      ![Azure Backup sestavy karty](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Jak můžu ověřit, pokud vytváření sestav dat bylo zahájeno přenášejí do účtu úložiště?**
    
   Přejděte na účet úložiště, které jste nakonfigurovali a vyberte kontejnery. Pokud kontejner má záznam pro přehledy. protokoly azurebackupreport, znamená to, data pro vytváření sestav byla spuštěna tok.

**Co je frekvence datová oznámení k účtu úložiště a balíčku obsahu Azure Backup v Power BI?**

   Pro uživatele 0 dne trvá přibližně 24 hodin k zápisu dat do účtu úložiště. Po ukončení tohoto počátečního nabízená data se aktualizují s frekvencí je znázorněno na následujícím obrázku. 
      
* Data související s **úlohy**, **výstrahy**, **zálohování položek**, **trezory**, **chráněné servery**a  **Zásady** se vloží do účtu úložiště zákazníka, jako je, a když se zaznamená.
      
* Data související s **úložiště** se vloží do účtu úložiště zákazníka každých 24 hodin.
   
   ![Azure Backup Reports data nabízená frekvence](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

* Má Power BI [plánovanou aktualizaci jednou denně](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Můžete provést ruční aktualizaci dat v Power BI pro balíček obsahu.

**Jak dlouho může uchovávat sestavy?**

   Když konfigurujete účet úložiště, můžete vybrat takovou dobu uchování dat sestavy v účtu úložiště. Postupujte podle kroku 6 v předchozí části "Konfigurace účtu úložiště pro sestavy". Můžete také [analyzovat sestav v aplikaci Excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) a uloží pro delší doba uchovávání dat, na základě vašich potřeb.

**Se zobrazí všechna data v sestavách, po nakonfigurovat účet úložiště?**

   Všechna data vygeneruje, když nakonfigurujete účet úložiště se vloží do účtu úložiště a je k dispozici v sestavách. Pro vytváření sestav, se nepřenášejí probíhající úlohy. Po dokončení úlohy nebo se nezdaří, jsou odeslána do sestavy.

**Pokud už jsem nakonfiguroval účet úložiště, chcete-li zobrazit sestavy, můžu změnit konfiguraci na jiný účet úložiště?**

   Ano, můžete změnit konfiguraci tak, aby odkazoval na jiný účet úložiště. Při připojení k balíčku obsahu Azure Backup, použijte nově nakonfigurovaný účet úložiště. Po dokončení konfigurace jiný účet úložiště je nová data se přenášejí také v tomto účtu úložiště. Starší data (před změnou konfigurace) zůstává ve starší účtu úložiště.

**Můžete zobrazit sestavy úložišť a předplatná?**

   Ano, můžete konfigurovat stejného účtu úložiště v rámci různých úložišť zobrazit trezor různé sestavy. Kromě toho můžete nakonfigurovat stejného účtu úložiště pro trezory napříč předplatnými. Tento účet úložiště můžete použít při připojení k balíčku obsahu Azure Backup v Power BI k zobrazení sestavy. Vybraný účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.
   
## <a name="troubleshooting-errors"></a>Řešení chyb
| Podrobnosti o chybě | Řešení |
| --- | --- |
| Po nastavení účtu úložiště pro zálohování sestavy **účtu úložiště** stále hlásí **Nenakonfigurováno**. | Pokud jste úspěšně nakonfigurovali účet úložiště, generování sestav dat toků i přes tento problém. Chcete-li tento problém vyřešit, přejděte na Azure portal a vyberte **všechny služby** > **nastavení diagnostiky** > **trezor služby Recovery Services**  >  **Upravit nastavení**. Odstranit dřív nakonfigurovaná nastavení a vytvořte nové nastavení ve stejném okně. Tentokrát v **název** vyberte **služby**. Nyní se zobrazí nakonfigurovaný účet úložiště. |
|Po importování balíčku obsahu Azure Backup v Power BI, chybu "nebyl nalezen 404-container" se zobrazí zpráva. | Jak už jsme zmínili musíte počkat, 24 hodin po dokončení konfigurace sestavy v trezoru služby Recovery Services je správně zobrazit v Power BI. Při pokusu o přístup k hlášení před 24 hodin se zobrazí tato chybová zpráva protože kompletní data ještě není k dispozici k zobrazení sestav platná. |

## <a name="next-steps"></a>Další postup
Po konfiguraci účtu úložiště a importovat balíček obsahu Azure Backup, jsou tyto další kroky k přizpůsobení sestav a vytváření sestav dat model pro vytváření sestav. Další informace naleznete v následujících článcích.

* [Použití Azure Backup, vytváření sestav datového modelu](backup-azure-reports-data-model.md)
* [Filtr sestavy v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Vytvoření sestavy v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)

