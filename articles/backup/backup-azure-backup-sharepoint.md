---
title: Zálohování sharepointové farmy do Azure pomocí aplikace DPM
description: Tento článek obsahuje přehled ochrany serveru DPM/Azure Backup na sharepointové farmě do Azure
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: 0199495e3b0eb002e58c096ed9abf05d46f43f97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054117"
---
# <a name="back-up-a-sharepoint-farm-to-azure-with-dpm"></a>Zálohování sharepointové farmy do Azure pomocí aplikace DPM

Sdílenou farmu do Microsoft Azure můžete zálohovat pomocí Správce ochrany dat system center (DPM) v podstatě stejným způsobem jako zálohování jiných zdrojů dat. Azure Backup poskytuje flexibilitu v plánu zálohování k vytváření denních, týdenních, měsíčních nebo ročních bodů zálohování a poskytuje možnosti zásad uchovávání informací pro různé body zálohování. Aplikace DPM umožňuje ukládat kopie místních disků pro rychlé cíle obnovení (RTO) a ukládat kopie do Azure pro ekonomické a dlouhodobé uchovávání.

Zálohování SharePointu do Azure pomocí aplikace DPM je velmi podobný proces k místnímu zálohování SharePointu na DPM. Konkrétní důležité informace pro Azure budou uvedeny v tomto článku.

## <a name="sharepoint-supported-versions-and-related-protection-scenarios"></a>Podporované verze SharePointu a související scénáře ochrany

Seznam podporovaných verzí SharePointu a verzí DPM, které jsou nezbytné k jejich zálohování, najdete v článku [Co může aplikace DPM zálohovat?](https://docs.microsoft.com/system-center/dpm/dpm-protection-matrix?view=sc-dpm-2019#applications-backup).

## <a name="before-you-start"></a>Než začnete

Před zálohováním sharepointové farmy do Azure je potřeba potvrdit několik věcí.

### <a name="prerequisites"></a>Požadavky

Než budete pokračovat, ujistěte se, že jste splnili všechny [předpoklady pro použití microsoft azure backup](backup-azure-dpm-introduction.md#prerequisites-and-limitations) u ochrany úloh. Některé úkoly pro požadavky zahrnují: vytvoření trezoru zálohování, stažení přihlašovacích údajů trezoru, instalaci agenta zálohování Azure a registraci zálohovacího serveru DPM/Azure pomocí trezoru.

Další požadavky a omezení najdete v článku [Zálohovat SharePoint s aplikacemi DPM.](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#prerequisites-and-limitations)

## <a name="configure-backup"></a>Konfigurace zálohování

Abyste mohli zálohovat farmu SharePointu, nakonfigurujte ochranu SharePointu pomocí ConfigureSharePoint.exe a potom v DPM vytvořte skupinu ochrany. Pokyny naleznete v [tématu Konfigurace zálohování](https://docs.microsoft.com//system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#configure-backup) v dokumentaci aplikace DPM.

## <a name="monitoring"></a>Monitorování

Chcete-li úlohu zálohování sledovat, postupujte podle pokynů [v části Sledování zálohování aplikace DPM.](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#monitoring)

## <a name="restore-sharepoint-data"></a>Obnovení dat SharePointu

Informace o obnovení položky SharePointu z disku pomocí aplikace DPM naleznete v [tématu Obnovení dat služby SharePoint](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#restore-sharepoint-data).

## <a name="restore-a-sharepoint-database-from-azure-by-using-dpm"></a>Obnovení databáze SharePointu z Azure pomocí aplikace DPM

1. Chcete-li obnovit databázi obsahu služby SharePoint, procházejte různé body obnovení (jak je znázorněno výše) a vyberte bod obnovení, který chcete obnovit.

    ![Ochrana služby DPM pro službu SharePoint8](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection9.png)
2. Poklepáním na bod obnovení SharePointu zobrazte dostupné informace o katalogu SharePointu.

   > [!NOTE]
   > Vzhledem k tomu, že farma SharePoint je chráněná pro dlouhodobé uchovávání v Azure, žádné informace o katalogu (metadata) je k dispozici na serveru DPM. V důsledku toho vždy, když je potřeba obnovit databázi obsahu sharepointového bodu, je třeba znovu katalogizovat farmu služby SharePoint.
   >
   >
3. Klepněte na **tlačítko Znovu katalogizovat**.

    ![Ochrana služby DPM pro službu SharePoint10](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection12.png)

    Otevře se okno stavu **Překatalogu cloudu.**

    ![Ochrana služby DPM sharepointu11](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection13.png)

    Po dokončení katalogizování se stav změní na *Úspěch*. Klikněte na **Zavřít**.

    ![Ochrana služby DPM sharepointu12](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection14.png)
4. Kliknutím na objekt SharePoint ukázáte na kartě **Obnovení** aplikace DPM, abyste získali strukturu databáze obsahu. Klikněte pravým tlačítkem myši na položku a potom klikněte na **obnovit**.

    ![Ochrana sharepointu DPM13](./media/backup-azure-backup-sharepoint/dpm-sharepoint-protection15.png)
5. V tomto okamžiku postupujte podle kroků obnovení dříve v tomto článku obnovit databázi obsahu služby SharePoint z disku.

## <a name="switching-the-front-end-web-server"></a>Přepnutí webového serveru front-end

Pokud máte více než jeden webový server front-end a chcete přepnout server, který aplikace DPM používá k ochraně farmy, postupujte podle pokynů v [části Přepínání webového serveru front-end](https://docs.microsoft.com/system-center/dpm/back-up-sharepoint?view=sc-dpm-2019#switching-the-front-end-web-server).

## <a name="next-steps"></a>Další kroky

* [Azure Backup Server a DPM – nejčastější dotazy](backup-azure-dpm-azure-server-faq.md)
* [Řešení potíží se System Center Data Protection Managerem](backup-azure-scdpm-troubleshooting.md)
