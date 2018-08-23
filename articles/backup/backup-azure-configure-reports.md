---
title: Konfigurace sestav Azure Backup
description: Konfigurace sestav Power BI pro Azure Backup pomocí trezoru služby Recovery Services.
services: backup
author: adiganmsft
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 07/26/2018
ms.author: adigan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4839b1aaa56be1ad93fa1dd685ca3176d1cc8a27
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "42057220"
---
# <a name="configure-azure-backup-reports"></a>Konfigurace sestav Azure Backup
Tento článek se hovoří o krocích, které chcete konfigurovat sestavy Azure Backup pomocí trezoru služby Recovery Services a chcete dostat k sestavám pomocí Power BI. Po provedení těchto kroků, můžete přejít přímo do Power BI k zobrazení všech sestav, přizpůsobení a vytváření sestav. 

## <a name="supported-scenarios"></a>Podporované scénáře
1. Azure Backup reports jsou podporované pro zálohování virtuálních počítačů Azure a zálohování souborů a složek do cloudu pomocí agenta Azure Recovery Services.
2. Sestavy pro Azure SQL, aplikace DPM a Azure Backup serveru nejsou v tuto chvíli nepodporuje.
3. Sestavy můžete zobrazit napříč trezory a napříč předplatnými, pokud se stejný účet úložiště je nakonfigurovaný pro všechny tyto trezory. Vybraný účet úložiště by měl být ve stejné oblasti jako trezor služby recovery services.
4. Četnost naplánované aktualizace pro sestavy je 24 hodin v Power BI. Aktualizace ad-hoc sestav můžete provést také v Power BI, ve kterém případu nejnovější data v účtu úložiště zákazníka používané k vykreslování sestav. 

## <a name="prerequisites"></a>Požadavky
1. Vytvoření [účtu služby Azure storage](../storage/common/storage-quickstart-create-account.md) ho nakonfigurovat pro sestavy. Tento účet úložiště se používá pro ukládání souvisejících dat sestavy.
2. [Vytvoření účtu Power BI](https://powerbi.microsoft.com/landing/signin/) k zobrazení, přizpůsobení a vytváření vlastních sestav pomocí portálu Power BI.
3. Registrace poskytovatele prostředků **Microsoft.insights** Pokud není zaregistrován již, s předplatné účtu úložiště a také předplatné trezoru služby Recovery Services umožňuje vykazování tok do úložiště dat do účet. Stejný postup provést, musíte přejít na web Azure portal > předplatné > poskytovatelů prostředků a kontroly pro tohoto zprostředkovatele k zaregistrování. 

## <a name="configure-storage-account-for-reports"></a>Nakonfigurujte účet úložiště pro sestavy
Pomocí následujících kroků a nakonfigurujte účet úložiště pro trezor služby recovery services pomocí webu Azure portal. Jedná se o jednorázovou konfiguraci a jakmile je nakonfigurovaný účet úložiště, můžete přejít do Power BI přímo do zobrazit balíček obsahu a využít sestavy.
1. Pokud už máte trezor služby Recovery Services, otevřít, pokračujte dalším krokem. Pokud nemáte otevřený trezor služeb zotavení, ale jsou na webu Azure Portal, klikněte na tlačítko **všechny služby**.

   * V seznamu prostředků zadejte **Recovery Services**.
   * Seznam se průběžně filtruje podle zadávaného textu. Až uvidíte **Trezory Recovery Services**, klikněte na ně.

      ![Vytvoření trezoru Recovery Services – krok 1](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

     Objeví se seznam trezorů Služeb zotavení. Ze seznamu trezorů Služeb zotavení vyberte trezor.

     Otevře se řídicí panel vybraného trezoru.
2. Ze seznamu položek, které se zobrazí v části trezor, klikněte na tlačítko **Backup Reports** části monitorování a sestavy můžete nakonfigurovat účet úložiště pro sestavy.

      ![Vyberte zálohu sestavy nabídky položky krok 2](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. V okně sestavy zálohování klikněte na tlačítko **nastavení diagnostiky** odkaz. Otevře se nastavení diagnostiky uživatelského rozhraní, který se používá k odesílání dat do účtu úložiště zákazníka.

      ![Povolení diagnostiky krok 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Klikněte na odkaz **zapnout diagnostiku**. Otevře se uživatelské rozhraní pro konfiguraci účtu úložiště. 

      ![Zapnout diagnostiku kroku 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. Do pole zadejte název nastavení **název** a vyberte **archivovat do účtu úložiště** zaškrtněte políčko, aby vytváření sestav dat můžete spustit tok v účtu úložiště.

      ![5. kroku povolení diagnostiky](./media/backup-azure-configure-reports/select-setting-name.png)
6. Klikněte na tlačítko pro výběr účtu úložiště a vyberte ze seznamu pro generování sestav dat a kliknutím příslušné předplatné a účet úložiště **OK**.

      ![Vyberte úložiště účtu krok 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. Vyberte **AzureBackupReport** zaškrtněte políčko v části protokolu a nastavte posuvník na dobu uchování vyberte data pro generování sestav. Za období vybrané pomocí tohoto posuvníku se ukládají data v účtu úložiště pro generování sestav.

      ![Uložit úložiště účtu krok 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Zkontrolujte všechny změny a klikněte na tlačítko **Uložit** tlačítko v horní části, jak je znázorněno na obrázku výše. Tato akce zajistí, že všechny změny se uložily a účet úložiště je teď nakonfigurovaný pro ukládání dat, vytváření sestav.

9. Nastavení diagnostiky tabulky by měly vykazovat nyní nové nastavení povolena pro trezor. Pokud se nezobrazí, aktualizujte tabulky, které chcete zobrazit aktualizované nastavení.

      ![Zobrazení nastavení diagnostiky kroku 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Jakmile nakonfigurujete sestavy ukládání účtu úložiště, měli byste **počkejte po dobu 24 hodin** pro počáteční datová oznámení k dokončení. Balíček obsahu Azure Backup v Power BI importujte pouze po uplynutí této doby. Přečtěte si [oddílu Nejčastější dotazy](#frequently-asked-questions) další podrobnosti. 
>
>

## <a name="view-reports-in-power-bi"></a>Zobrazení sestav v Power BI 
Po konfiguraci účtu úložiště pro sestavy pomocí trezoru služby recovery services, trvá přibližně 24 hodin pro generování sestav dat spuštění toku. Po 24 hodinách nastavení účtu úložiště postupujte následovně Chcete-li zobrazit sestavy v Power BI:
1. [Přihlaste se](https://powerbi.microsoft.com/landing/signin/) do Power BI.
2. Klikněte na tlačítko **získat Data** a klikněte na tlačítko **získat** pod **služby** v knihovně obsahu balíčku. Pomocí kroků uvedených v [dokumentaci k Power BI pro přístup k balíčku obsahu](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/).

     ![Importovat balíček obsahu](./media/backup-azure-configure-reports/content-pack-import.png)
3. Typ **Azure Backup** panel hledání a klikněte na **získat**.

      ![Získat balíček obsahu](./media/backup-azure-configure-reports/content-pack-get.png)
4. Zadejte název účtu úložiště, který je nakonfigurovaný v kroku 5 výše a klikněte na tlačítko **Další** tlačítko.

    ![Zadejte název účtu úložiště.](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Zadejte klíč účtu úložiště pro tento účet úložiště. Je možné [zobrazení a zkopírování přístupových klíčů k úložišti](../storage/common/storage-create-storage-account.md#manage-your-storage-account) tak, že přejdete do účtu úložiště na webu Azure portal. 

     ![Zadejte účet úložiště](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>
     
6. Klikněte na tlačítko **přihlášení** tlačítko. Po přihlášení úspěšné, dostanete **importu dat** oznámení.

    ![Import balíčku obsahu](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>
    
    Po nějaké době získáte **úspěch** oznámení po dokončení importu. Může trvat déle k importu balíčku obsahu, pokud existuje velké množství dat v účtu úložiště.
    
    ![Importovat balíček obsahu úspěch](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>
    
7. Po úspěšném importu dat **Azure Backup** balíček obsahu je zobrazená v **aplikace** v navigačním podokně. Seznam nyní zobrazuje řídicí panel Azure Backup, sestavy a datové sady s černým symbolem x označující nově importovaných sestav. 

     ![Balíček obsahu Azure Backup](./media/backup-azure-configure-reports/content-pack-azure-backup.png) <br/>
     
8. Klikněte na tlačítko **Azure Backup** v rámci řídicích panelů, zobrazí sadu sestav připnuté klíče.

      ![Řídicí panel služby Azure Backup](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Chcete-li zobrazit úplnou sadu sestav, kliknutím na jakoukoli sestavu na řídicím panelu.

      ![Stav úloh Azure Backup](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Kliknutí na jednotlivé karty v sestavách zobrazovat sestavy v této oblasti.

      ![Azure Backup Reports karty](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="frequently-asked-questions"></a>Nejčastější dotazy
1. **Jak můžu ověřit, zda bylo zahájeno generování sestav dat přitékající do účtu úložiště?**
    
    Můžete přejít na účet úložiště, které jsou nakonfigurované a vybrat kontejnery. Pokud kontejner má záznam pro přehledy. protokoly azurebackupreport, znamená to, data pro vytváření sestav byla spuštěna tok.

2. **Co je frekvence datová oznámení k účtu úložiště a balíčku obsahu Azure Backup v Power BI?**

   Den 0 uživatelů by trvat přibližně 24 hodin k zápisu dat do účtu úložiště. Po dokončení tohoto počátečního nabízená data se aktualizují s následující frekvencí je znázorněno na následujícím obrázku. 
      * Data související s **úlohy, výstrahy, zálohování položek, trezory, chráněné servery a zásady** se vloží do účtu úložiště zákazníka jako a při protokolování.
      * Data související s **úložiště** se vloží do účtu úložiště zákazníka každých 24 hodin.
   
    ![Azure Backup Reports data nabízená frekvence](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  Má Power BI [plánovanou aktualizaci jednou denně](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Můžete provést ruční aktualizaci dat v Power BI pro balíček obsahu.

3. **Jak dlouho může uchovávat sestavy?** 

   Při konfiguraci účtu úložiště, můžete vybrat dobu uchování dat, vytváření sestav v účtu úložiště (použití kroku 6 v účtu úložiště konfigurace pro výše uvedené části sestavy). Kromě toho můžete [sestavy analyzovat v aplikaci excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) a uloží pro delší doba uchovávání dat podle vašich potřeb. 

4. **Se zobrazí všechna data v sestavách po dokončení konfigurace účtu úložiště?**

   Všechna data generován poté **"účet úložiště konfigurace"** se vloží do účtu úložiště a bude k dispozici v sestavách. Ale **probíhající úlohy, se nepřenášejí** pro vytváření sestav. Jakmile úloha neskončí nebo neselže, odesílat sestavy.

5. **Pokud mám jste už nakonfigurovali účet úložiště, chcete-li zobrazit sestavy, můžu změnit konfiguraci na jiný účet úložiště?** 

   Ano, můžete změnit konfiguraci tak, aby odkazoval na jiný účet úložiště. Nově nakonfigurovaný účet úložiště by měl používat při připojování k balíčku obsahu Azure Backup. Navíc po nakonfigurování jiný účet úložiště je nová data by tok v tomto účtu úložiště. Ale starší data (před změnou konfigurace) by stále zůstanou ve starší účtu úložiště.

6. **Můžete zobrazit sestavy, napříč trezory a napříč předplatnými?** 

   Ano, můžete konfigurovat stejného účtu úložiště v rámci různých úložišť zobrazit trezor různé sestavy. Kromě toho můžete nakonfigurovat stejného účtu úložiště pro trezory napříč předplatnými. Potom můžete tento účet úložiště při připojení k balíčku obsahu Azure Backup v Power BI k zobrazení sestavy. Vybraný účet úložiště by však být ve stejné oblasti jako trezor služby recovery services.
   
## <a name="troubleshooting-errors"></a>Řešení chyb
| Podrobnosti o chybě | Řešení |
| --- | --- |
| Po nastavení účtu úložiště pro zálohování sestavy **účtu úložiště** stále hlásí **Nenakonfigurováno**. | Pokud jste úspěšně nakonfigurovali účet úložiště, vaše data sestav budou směrovat v i přes tento problém. Chcete-li tento problém vyřešit, přejděte na webu Azure portal > všechny služby > nastavení diagnostiky > RS trezor > Upravit nastavení. Odstranit dřív nakonfigurovaná nastavení a vytvořte nové nastavení v okně stejné. Nyní nastavte pole **název** k **služby**. To by měly vykazovat nakonfigurovaný účet úložiště. |
|Po importu Azure Backup balíčku obsahu v Power BI, chyba **404-container nenajde** se zobrazí. | Jak je navrženo v tomto dokumentu musíte počkat po dobu 24 hodin po dokončení konfigurace sestavy v trezoru služby Recovery Services je správně zobrazit v Power BI. Při pokusu o přístup k hlášení před 24 hodin bude získat tuto chybu, protože úplná data ještě není k dispozici k zobrazení sestav platná. |

## <a name="next-steps"></a>Další postup
Teď, když nakonfigurujete účet úložiště a importovaný balíček obsahu Azure Backup, dalším krokem je Přizpůsobte si sestavy a použít vytváření sestav datový model pro vytváření sestav. Další podrobnosti najdete v následujících článcích.

* [Pomocí služby Azure Backup, vytváření sestav datového modelu](backup-azure-reports-data-model.md)
* [Filtrování sestavy v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Vytváření sestav v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)

