---
title: Kurz – Archivace protokolů Azure Active Directory do účtu úložiště Azure (Preview) | Microsoft Docs
description: Zjistěte, jak nastavit službu Azure Diagnostics, aby předávala protokoly Azure Active Directory do účtu úložiště (Preview)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: ea26da91e2a0b88e8afa54a4210b7a2365e4949b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39240184"
---
# <a name="tutorial-archive-azure-active-directory-logs-to-an-azure-storage-account-preview"></a>Kurz: Archivace protokolů Azure Active Directory do účtu úložiště Azure (Preview)

V tomto kurzu se dozvíte, jak nastavit diagnostiku v Azure Monitoru na směrování protokolů Azure Active Directory do účtu úložiště Azure.

## <a name="prerequisites"></a>Požadavky 

Budete potřebovat:

* Předplatné Azure s účtem úložiště Azure. Pokud předplatné Azure nemáte, můžete si [zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
* Tenanta Azure Active Directory.
* Uživatele, který je globálním správcem nebo správcem zabezpečení pro tohoto tenanta.

## <a name="archive-logs-to-an-azure-storage-account"></a>Archivace protokolů do účtu úložiště Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 
2. Klikněte na **Azure Active Directory** -> **Aktivita** -> **Protokoly auditu**. 
3. Kliknutím na **Exportovat nastavení** otevřete okno Nastavení diagnostiky. Pokud chcete změnit stávající nastavení, klikněte na **Upravit nastavení**, a pokud chcete přidat nové nastavení, klikněte na **Přidat nastavení diagnostiky**. Můžete mít maximálně tři nastavení. 
    ![Exportovat nastavení](./media/reporting-azure-monitor-diagnostics-azure-storage-account/ExportSettings.png "Exportovat nastavení")

4. Přidejte k nastavení popisný název, který vám bude připomínat jeho účel. Může to být třeba název „Odeslání do účtu úložiště Azure“. 
5. Zaškrtněte políčko **Archivovat v účtu úložiště** a klikněte na **Účet úložiště**, abyste mohli zvolit účet úložiště Azure. 
6. Vyberte předplatné a účet úložiště Azure, kam chcete směrovat protokoly, a zavřete konfiguraci kliknutím na **OK**.
7. Zaškrtnutím políčka **Protokoly auditu** určíte, že se mají do účtu úložiště odesílat protokoly auditu. 
8. Zaškrtnutím políčka **Protokoly přihlášení** určíte, že se mají do účtu úložiště odesílat protokoly přihlášení.
9. Pomocí posuvníku nastavte dobu uchovávání dat protokolu. Ve výchozím nastavení je tato hodnota „0“ a protokoly se budou v účtu úložiště uchovávat po neomezenou dobu. Můžete ale nastavit požadovanou hodnotu, aby se události starší než vybraný počet dní automaticky mazaly.
10. Uložte nastavení kliknutím na **Uložit**.
    ![Nastavení diagnostiky](./media/reporting-azure-monitor-diagnostics-azure-storage-account/DiagnosticSettings.png "Nastavení diagnostiky")

11. Přibližně po 15 minutách zkontrolujte, jestli se protokoly předávají do vašeho účtu úložiště. Přejděte na web Azure Portal, klikněte na **Účty úložiště**, zvolte účet úložiště, který jste použili předtím, a klikněte na **Objekty blob**. 
12. U položky **Protokoly auditu** klikněte na **insights-log-audit**. U položky **Protokoly přihlášení** klikněte na **insights-logs-signin**.
    ![Účet úložiště](./media/reporting-azure-monitor-diagnostics-azure-storage-account/StorageAccount.png "Účet úložiště")

## <a name="next-steps"></a>Další kroky

* [Interpretace schématu protokolů auditu ve službě Azure Monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpretace schématu protokolů přihlášení ve službě Azure Monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Nejčastější dotazy a známé problémy](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)