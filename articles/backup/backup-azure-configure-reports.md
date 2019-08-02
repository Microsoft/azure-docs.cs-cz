---
title: Konfigurace sestav pro Azure Backup
description: Nakonfigurujte sestavy Power BI pro Azure Backup pomocí trezoru Recovery Services.
ms.reviewer: adigan
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: dacurwin
ms.openlocfilehash: 169ce73ead52d6a275f13f084c681e14c89ab606
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689357"
---
# <a name="configure-azure-backup-reports"></a>Konfigurace sestav Azure Backup
V tomto článku se dozvíte, jak postupovat při konfiguraci sestav pro Azure Backup pomocí trezoru Recovery Services. Také ukazuje, jak přistupovat k sestavám pomocí Power BI. Po dokončení těchto kroků můžete přejít přímo na Power BI a zobrazit, přizpůsobit a vytvořit sestavy.

> [!IMPORTANT]
> Od 1. listopadu 2018 můžou někteří zákazníci pozorovat problémy při načítání dat z aplikace Azure Backup v Power BI, při kterých se zobrazí tato zpráva: „Na konci vstupu JSON jsme našli nějaké znaky navíc. Výjimku vyvolalo rozhraní IDataReader.“
Příčinou je změna formátu dat načítaných do účtu úložiště.
Pokud se chcete tomuto problému vyhnout, Stáhněte si prosím nejnovější aplikaci (verze 1,8).
>
>

## <a name="supported-scenarios"></a>Podporované scénáře
- Sestavy Azure Backup se podporují pro zálohování virtuálních počítačů Azure a zálohování souborů a složek do cloudu pomocí agenta Azure Recovery Services.
- Sestavy pro Azure SQL Database, sdílené složky Azure, Data Protection Manager a Azure Backup Server nejsou v tuto chvíli podporované.
- Pokud je pro každý trezor nakonfigurovaný stejný účet úložiště, můžete zobrazit sestavy napříč trezory a předplatnými. Vybraný účet úložiště musí být ve stejné oblasti jako trezor Recovery Services.
- Frekvence plánovaných aktualizací sestav je 24 hodin v Power BI. Můžete také provést aktualizaci ad hoc sestav v Power BI. V tomto případě se k vykreslování sestav použijí nejnovější data v účtu úložiště zákazníka.

## <a name="prerequisites"></a>Požadavky
- Vytvořte [účet úložiště Azure](../storage/common/storage-quickstart-create-account.md) pro konfiguraci pro sestavy. Tento účet úložiště se používá k ukládání dat souvisejících se sestavami.
- [Vytvořte účet Power BI](https://powerbi.microsoft.com/landing/signin/) pro zobrazení, přizpůsobení a vytváření vlastních sestav pomocí portálu Power BI.
- Zaregistrujte poskytovatele prostředků **Microsoft. Insights**, pokud už není zaregistrovaný. Použijte odběry pro účet úložiště a Recovery Services trezor, aby data vytváření sestav mohla být v účtu úložiště. Tento krok provedete tak, že přejdete na > Azure Portal, vyberete**poskytovatele prostředků**předplatného a zkontrolujete tohoto poskytovatele a zaregistrujete ho.

## <a name="configure-storage-account-for-reports"></a>Konfigurace účtu úložiště pro sestavy
Postupujte podle těchto kroků a nakonfigurujte účet úložiště pro Recovery Services trezor pomocí Azure Portal. Jedná se o jednorázovou konfiguraci. Po nakonfigurování účtu úložiště můžete přejít přímo na Power BI a zobrazit balíček obsahu a použít sestavy.

1. Pokud už máte otevřený trezor Recovery Services, pokračujte na další krok. Pokud nemáte otevřený trezor Recovery Services, vyberte v Azure Portal možnost **všechny služby**.

   * V seznamu prostředků zadejte **Recovery Services**.
   * Seznam se průběžně filtruje podle zadávaného textu. Když vidíte **Recovery Services trezory**, vyberte ji.
   * Objeví se seznam trezorů Služeb zotavení. Ze seznamu trezorů Služeb zotavení vyberte trezor.

     Otevře se řídicí panel vybraného trezoru.
2. V seznamu položek, které se zobrazí v trezoru, vyberte v části **monitorování a sestavy** možnost **sestavy zálohování** a nakonfigurujte účet úložiště pro sestavy.

      ![Vyberte položku nabídky sestavy zálohování krok 2.](./media/backup-azure-configure-reports/backup-reports-settings.PNG)
3. V okně **sestavy zálohování** vyberte odkaz **nastavení diagnostiky** . Tento odkaz otevře uživatelské rozhraní pro **nastavení diagnostiky** , které se používá k odesílání dat do účtu úložiště zákazníka.

      ![Povolit diagnostiku – krok 3](./media/backup-azure-configure-reports/backup-azure-configure-reports.png)
4. Výběrem **zapnout diagnostiku** otevřete uživatelské rozhraní, které chcete použít ke konfiguraci účtu úložiště.

      ![Zapnout diagnostiku – krok 4](./media/backup-azure-configure-reports/enable-diagnostics.png)
5. Do pole **název** zadejte název nastavení. Zaškrtněte políčko **archivovat do účtu úložiště** , aby data vytváření sestav mohla začít přesměrovat do účtu úložiště.

      ![Povolit diagnostiku – krok 5](./media/backup-azure-configure-reports/select-setting-name.png)
6. Vyberte možnost **účet úložiště**, vyberte příslušné předplatné a účet úložiště ze seznamu pro uložení dat vytváření sestav a vyberte **OK**.

      ![Výběr účtu úložiště – krok 6](./media/backup-azure-configure-reports/select-subscription-sa.png)
7. V části **protokol** zaškrtněte políčko **AzureBackupReport** . Přesunutím posuvníku vyberte dobu uchovávání dat pro sestavy. Data vytváření sestav v účtu úložiště se uchovávají po dobu vybranou pomocí tohoto posuvníku.

      ![Uložení účtu úložiště – krok 7](./media/backup-azure-configure-reports/save-diagnostic-settings.png)
8. Zkontrolujte všechny změny a vyberte **Uložit**. Tato akce zajistí, že se uloží všechny změny a účet úložiště je teď nakonfigurovaný tak, aby ukládal data sestav.

9. Tabulka **nastavení diagnostiky** nyní zobrazuje nové nastavení povolené pro trezor. Pokud se nezobrazí, aktualizujte tabulku, aby se zobrazilo aktualizované nastavení.

      ![Zobrazit nastavení diagnostiky krok 9](./media/backup-azure-configure-reports/diagnostic-setting-row.png)

> [!NOTE]
> Po nakonfigurování sestav uložením účtu úložiště *Počkejte 24 hodin* na dokončení počátečního vkládání dat. Importujte aplikaci Azure Backup v Power BI až po uplynutí této doby. Další informace najdete v [části Nejčastější dotazy](backup-azure-monitor-alert-faq.md).
>
>

## <a name="view-reports-in-power-bi"></a>Zobrazení sestav v Power BI
Po nakonfigurování účtu úložiště pro sestavy pomocí Recovery Servicesho trezoru zabere přibližně 24 hodin, aby se data sestav spouštěla v. Po 24 hodinách nastavování účtu úložiště postupujte podle těchto kroků a zobrazte sestavy v Power BI.
Pokud chcete sestavu přizpůsobit a sdílet, vytvořte pracovní prostor a proveďte následující kroky.

1. [](https://powerbi.microsoft.com/landing/signin/) Přihlaste se k Power BI.
2. Vyberte **Načíst data**. Ve **více způsobech vytvoření vlastního obsahu**vyberte **balíčky obsahu služby**. Postupujte podle kroků v [dokumentaci Power BI pro připojení ke službě](https://powerbi.microsoft.com/documentation/powerbi-content-packs-services/).

3. Na panelu **hledání** zadejte **Azure Backup** a vyberte **získat**.

      ![Získat balíček obsahu](./media/backup-azure-configure-reports/content-pack-get.png)
4. Zadejte název účtu úložiště, který jste nakonfigurovali v předchozím kroku 5, a vyberte **Další**.

    ![Zadejte název účtu úložiště.](./media/backup-azure-configure-reports/content-pack-storage-account-name.png)    
5. Pomocí metody Authentication Key (klíč) zadejte klíč účtu úložiště pro tento účet úložiště. Pokud chcete [Zobrazit a zkopírovat přístupové klíče úložiště](../storage/common/storage-account-manage.md#access-keys), přejděte do účtu úložiště v Azure Portal.

     ![Zadejte účet úložiště.](./media/backup-azure-configure-reports/content-pack-storage-account-key.png) <br/>

6. Vyberte **Přihlásit se**. Po úspěšném přihlášení se zobrazí oznámení o importu dat.

    ![Import balíčku obsahu](./media/backup-azure-configure-reports/content-pack-importing-data.png) <br/>

    Po dokončení importu se zobrazí oznámení o **úspěchu** . Pokud je objem dat v účtu úložiště velký, může Import balíčku obsahu trvat trochu déle.

    ![Balíček obsahu úspěšného importu](./media/backup-azure-configure-reports/content-pack-import-success.png) <br/>

7. Po úspěšném importu dat se **Azure Backup** balíček obsahu zobrazí v **aplikacích** v navigačním podokně. V části **řídicí panely**, **sestavy**a **datové sady**se teď v seznamu zobrazuje Azure Backup.

8. V části **řídicí panely**vyberte **Azure Backup**, která zobrazuje sadu připnutých sestav klíčů.

      ![Řídicí panel Azure Backup](./media/backup-azure-configure-reports/azure-backup-dashboard.png) <br/>
9. Chcete-li zobrazit kompletní sadu sestav, vyberte jakoukoli sestavu na řídicím panelu.

      ![Stav úlohy Azure Backup](./media/backup-azure-configure-reports/azure-backup-job-health.png) <br/>
10. Vyberte každou kartu v sestavách k zobrazení sestav v této oblasti.

      ![Karty sestav Azure Backup](./media/backup-azure-configure-reports/reports-tab-view.png)


## <a name="troubleshooting-errors"></a>Řešení chyb
| Podrobnosti chyby | Řešení |
| --- | --- |
| Po nastavení účtu úložiště pro sestavy záloh se pořád zobrazuje, že **účet úložiště** **není nakonfigurovaný**. | Pokud jste úspěšně nakonfigurovali účet úložiště, data sestav natékají navzdory tomuto problému. Tento problém vyřešíte tak, že přejdete do Azure Portal a vyberete**Nastavení** > diagnostiky **všechny služby** > **Recovery Services** > **Upravit nastavení**trezoru. Odstraňte dříve nakonfigurované nastavení a vytvořte nové nastavení pro stejné okno. Tentokrát v poli **název** vyberte **Služba**. Nyní se zobrazí nakonfigurovaný účet úložiště. |
|Po importu balíčku obsahu Azure Backup v Power BI se zobrazí chybová zpráva "404-Container se nenašlo". | Jak už bylo uvedeno výše, musíte po dokončení konfigurace sestav v Recovery Servicesovém trezoru počkat 24 hodin, aby byly správně zobrazeny v Power BI. Pokud se pokusíte o přístup k sestavám před 24 hodinami, zobrazí se tato chybová zpráva, protože pro zobrazení platných sestav ještě nejsou k dispozici úplná data. |

## <a name="next-steps"></a>Další postup
Po nakonfigurování účtu úložiště a importu balíčku Azure Backup obsahu jsou další kroky k přizpůsobení sestav a použití datového modelu vytváření sestav k vytváření sestav. Další informace najdete v následujících článcích.

* [Použití datového modelu Azure Backup sestavy](backup-azure-reports-data-model.md)
* [Filtrovat sestavy v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-about-filters-and-highlighting-in-reports/)
* [Vytváření sestav v Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-create-a-new-report/)
