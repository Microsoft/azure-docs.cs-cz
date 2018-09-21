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
ms.component: report-monitor
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 08e4525e7dcf8edafc8819416b9d77e76fd78a65
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46363424"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account-preview"></a>Kurz: Archivace protokolů Azure AD do účtu úložiště Azure (Preview)

V tomto kurzu se dozvíte, jak nastavit diagnostiku ve službě Azure Monitor na směrování protokolů Azure Active Directory (Azure AD) do účtu úložiště Azure.

## <a name="prerequisites"></a>Požadavky 

Pokud chcete používat tuto funkci, potřebujete tyto položky:

* Předplatné Azure s účtem úložiště Azure. Pokud předplatné Azure nemáte, můžete si [zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
* Tenanta Azure AD.
* Uživatele, který je *globálním správcem* nebo *správcem zabezpečení* pro tohoto tenanta Azure AD.

## <a name="archive-logs-to-an-azure-storage-account"></a>Archivace protokolů do účtu úložiště Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

2. Vyberte **Azure Active Directory** > **Aktivita** > **Protokoly auditu**. 

3. Vyberte **Exportovat nastavení**. 

4. V podokně **Nastavení diagnostiky** proveďte jednu z následujících akcí:
    * Pokud chcete změnit stávající nastavení, vyberte **Upravit nastavení**.
    * Pokud chcete přidat nové nastavení, vyberte **Přidat nastavení diagnostiky**.  
      Můžete mít až tři nastavení. 

    ![Export nastavení](./media/quickstart-azure-monitor-route-logs-to-storage-account/ExportSettings.png)

5. Zadejte pro nastavení popisný název, který vám bude připomínat jeho účel (například *Odeslání do účtu úložiště Azure*). 

6. Zaškrtněte políčko **Archivovat v účtu úložiště** a pak vyberte **Účet úložiště**. 

7. Vyberte předplatné Azure a účet úložiště, kam chcete protokoly směrovat.
 
8. Výběrem **OK** ukončete konfiguraci.

9. Proveďte jednu nebo obě z následujících akcí:
    * Pokud chcete do účtu úložiště odesílat protokoly auditu, zaškrtněte políčko **Protokoly auditu**. 
    * Pokud chcete do účtu úložiště odesílat protokoly přihlášení, zaškrtněte políčko **Protokoly přihlášení**.

10. Pomocí posuvníku nastavte dobu uchovávání dat protokolu. Ve výchozím nastavení je tato hodnota *0*, což znamená, že se protokoly v účtu úložiště uchovávají po neomezenou dobu. Pokud nastavíte jinou hodnotu, události starší než vybraný počet dnů se automaticky odstraní.

11. Vyberte **Uložit** a nastavení se uloží.

    ![Nastavení diagnostiky](./media/quickstart-azure-monitor-route-logs-to-storage-account/DiagnosticSettings.png)

12. Přibližně po 15 minutách zkontrolujte, jestli se protokoly odesílají do vašeho účtu úložiště. Přejděte na web [Azure Portal](https://portal.azure.com), vyberte **Účty úložiště**, vyberte účet úložiště, který jste použili dříve, a pak vyberte **Objekty blob**. 

13. U položky **Protokoly auditu** vyberte **insights-log-audit**. U položky **Protokoly přihlášení** vyberte **insights-logs-signin**.
    ![Účet úložiště](./media/quickstart-azure-monitor-route-logs-to-storage-account/StorageAccount.png)

## <a name="next-steps"></a>Další kroky

* [Interpretace schématu protokolů auditu ve službě Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpretace schématu protokolů přihlášení ve službě Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Nejčastější dotazy a známé problémy](concept-activity-logs-in-azure-monitor.md#frequently-asked-questions)
