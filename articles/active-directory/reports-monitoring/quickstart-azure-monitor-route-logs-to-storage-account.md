---
title: Kurz – archivace protokolů adresářů do účtu úložiště | Microsoft Docs
description: Přečtěte si, jak nastavit Azure Diagnostics pro odesílání Azure Active Directory protokolů do účtu úložiště.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 045f94b3-6f12-407a-8e9c-ed13ae7b43a3
ms.service: active-directory
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa2cac7c89a2cb1bd99675727bcd51e82c4a18ef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74007859"
---
# <a name="tutorial-archive-azure-ad-logs-to-an-azure-storage-account"></a>Kurz: archivace protokolů služby Azure AD do účtu služby Azure Storage

V tomto kurzu se dozvíte, jak nastavit diagnostiku ve službě Azure Monitor na směrování protokolů Azure Active Directory (Azure AD) do účtu úložiště Azure.

## <a name="prerequisites"></a>Požadavky 

Pokud chcete používat tuto funkci, potřebujete tyto položky:

* Předplatné Azure s účtem úložiště Azure. Pokud předplatné Azure nemáte, můžete si [zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
* Tenanta Azure AD.
* Uživatele, který je *globálním správcem* nebo *správcem zabezpečení* pro tohoto tenanta Azure AD.

## <a name="archive-logs-to-an-azure-storage-account"></a>Archivace protokolů do účtu úložiště Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

2. Vyberte **Azure Active Directory**  >  **Activity**  >  **protokoly auditu**aktivit. 

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

12. Přibližně po 15 minutách zkontrolujte, jestli se protokoly odesílají do vašeho účtu úložiště. Přejděte na web [Azure Portal](https://portal.azure.com), vyberte **Účty úložiště**, vyberte účet úložiště, který jste použili dříve, a pak vyberte **Objekty blob**. U položky **Protokoly auditu** vyberte **insights-log-audit**. U položky **Protokoly přihlášení** vyberte **insights-logs-signin**.

    ![Účet úložiště](./media/quickstart-azure-monitor-route-logs-to-storage-account/StorageAccount.png)

## <a name="next-steps"></a>Další kroky

* [Interpretace schématu protokolů auditu v Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpretace schématu protokolů přihlášení ve službě Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Nejčastější dotazy a známé problémy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)