---
title: "Připojení k Azure náklady na správu účtu Google Cloud Platform | Microsoft Docs"
description: "Připojení k účtu Google Cloud Platform zobrazíte náklady a repots data o využití v náklady na správu."
services: cost-management
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 02/05/2018
ms.topic: article
ms.service: cost-management
manager: carmonm
ms.custom: 
ms.openlocfilehash: 8f8c157be0a369817099afa211015ba7587017e3
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="connect-a-google-cloud-platform-account"></a>Připojit k účtu Google Cloud Platform

Vaše stávající účet Google Cloud Platform může připojit k Azure náklady na správu. Po připojení k náklady na správu účtu, je k dispozici v sestavách náklady na správu data o využití a náklady. Tento článek vám pomůže nakonfigurovat a připojit svůj účet Google s náklady na správu.

## <a name="collect-project-information"></a>Shromažďovat informace o projektu

Můžete začít shromažďují se informace o projektu.

1. Přihlaste se ke konzole Google Cloud Platform v [https://console.cloud.google.com](https://console.cloud.google.com).
2. Zkontrolujte informace projektu, které chcete, aby se budou registrovat náklady na správu a Poznámka **název projektu** a **ID projektu**. Zachovat informace o dalších krocích užitečné.  
    ![Google Cloud Platform konzoly](./media/connect-google-account/gcp-console01.png)
3. Pokud fakturace není povolená a propojené do projektu, vytvořte účet fakturace. Další informace najdete v tématu [vytvořit nový účet fakturace](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create\_a\_new\_billing\_account).

## <a name="enable-storage-bucket-billing-export"></a>Povolit export fakturace sady úložiště

Náklady správy načte vaše Google fakturace data z úložiště sady. Zachovat **název intervalu** a **sestavy předponu** informace užitečné pro pozdější použití při registraci náklady na správu.

Pomocí služby Google cloudového úložiště pro uložení sestavy využití způsobuje minimální poplatky. Další informace najdete v tématu [Cloud Storage – ceny](https://cloud.google.com/storage/pricing).

1. Pokud jste nepovolili fakturace export do souboru, postupujte podle pokynů v [povolení fakturace export do souboru](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Můžete použít buď JSON nebo CSV fakturace formát exportu.
2. Jinak, v konzole pro Google Cloud Platform, přejděte na **fakturace** > **fakturace export**. Poznámka: vaše fakturace **název intervalu** a **sestavy předponu**.  
    ![Export fakturace](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Povolte rozhraní API Google Cloud Platform

Pokud chcete shromažďovat informace o využití a asset, potřebuje náklady na správu následující Google Cloud Platform rozhraní API povoleno:

- BigQuery rozhraní API
- Google Cloud SQL
- Google Cloud Datastore rozhraní API
- Úložiště v cloudu Google
- Úložiště v cloudu Google JSON API
- Rozhraní API Google výpočetní modulu

### <a name="enable-or-verify-apis"></a>Povolení nebo ověřte rozhraní API

1. V konzole Google Cloud Platform vyberte projekt, který chcete zaregistrovat s náklady na správu.
2. Přejděte na **rozhraní API a službám** > **knihovny**.
3. Pomocí vyhledávání a zjistit, že každý dříve uvedený rozhraní API.
4. Každé rozhraní API, ověřte, že **rozhraní API povoleno** se zobrazí. Jinak, klikněte na tlačítko **povolit**.

## <a name="add-a-google-cloud-account-to-cost-management"></a>Přidání účtu Google Cloud náklady na správu

1. Otevřete portál Cloudyn z portálu Azure nebo přejděte na [https://azure.cloudyn.com](https://azure.cloudyn.com/) a přihlaste se.
2. Klikněte na tlačítko **nastavení** (symbol ozubené kolo) a potom vyberte **cloudové účty**.
3. V **správy účtů**, vyberte **účty Google** a pak klikněte **přidat nové +**.
4. V **název účtu Google**, zadejte e-mailovou adresu pro platebního účtu, potom klikněte na **Další**.
5. V dialogovém okně ověřování Google, vyberte nebo zadejte účet Google a potom **povolit** cloudyn.com přístup k vašemu účtu.
6. Přidejte požadované informace o projektu poznamenat, že jste měli předchozí. Patří mezi ně **ID projektu**, **projektu** název, **fakturace** název intervalu a **fakturace souboru** sestavy předponu a pak klikněte na  **Uložit**.  
    ![Přidání projektu Google](./media/connect-google-account/add-project.png)

Účet služby Google se zobrazí v seznamu účtů a by mělo být uvedeno **ověřený**. V části, by měl název projektu Google a ID zobrazí a mít symbol zelená značka zaškrtnutí. Stav účtu by mělo být uvedeno **dokončeno**.

V rámci několik hodin náklady na správu sestavy zobrazit informace náklady a využití Google.

## <a name="next-steps"></a>Další postup

- Další informace o Azure náklady na správu, pokračujte [zkontrolujte využití a náklady](./tutorial-review-usage.md) kurzu náklady na správu.
