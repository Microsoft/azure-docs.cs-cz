---
title: Připojení účtu Google Cloud Platform do Cloudyn v Azure | Dokumentace Microsoftu
description: Připojení účtu Google Cloud Platform a zobrazit data o využití a nákladů v sestavách Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 937d1b6e0bc9ece0507821538fafb0f5d8c0ef99
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230137"
---
# <a name="connect-a-google-cloud-platform-account"></a>Připojení účtu Google Cloud Platform

Můžete připojit váš existující účet Google Cloud Platform do Cloudyn. Po propojení účtu do Cloudyn, nákladů a využití dat je k dispozici v sestavách Cloudyn. Tento článek pomůže vám pomůže nakonfigurovat a připojte si účet Google s Cloudyn.


## <a name="collect-project-information"></a>Shromažďovat informace o projektu

Začnete tím, že shromažďování informací o projektu.

1. Přihlaste se ke konzole Google Cloud Platform v [https://console.cloud.google.com](https://console.cloud.google.com).
2. Zkontrolujte informace o projektu, které chcete připojit k Cloudyn, a poznamenejte si **název projektu** a **ID projektu**. Zachovejte informace po ruce pro pozdější kroky.  
    název projektu ![a ID projektu zobrazené v konzole Google Cloud Platform](./media/connect-google-account/gcp-console01.png)
3. V případě fakturace není povolen a propojeny s projektem, vytvořte fakturační účet. Další informace najdete v tématu [Vytvoření nového fakturačního účtu](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account).

## <a name="enable-storage-bucket-billing-export"></a>Povolit export fakturační kontejneru úložiště

Cloudyn načte vašich fakturačních dat Google z kontejneru úložiště. Ponechte **název** sady a informace **předpony sestavy** užitečné pro pozdější použití při registraci Cloudyn.

Použití Google Cloud Storage pro uložení sestavy o využití se účtují minimální poplatky. Další informace najdete v tématu [ceny cloudového úložiště](https://cloud.google.com/storage/pricing).

1. Pokud jste nepovolili export fakturace do souboru, postupujte podle pokynů v tématu [Jak povolit export fakturace do souboru](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Můžete použít JSON nebo CSV fakturační formát exportu.
2. V opačném případě v konzole Google Cloud Platform přejděte na **fakturace** > **fakturace export**. Poznamenejte si **název** fakturačního intervalu a **předponu sestavy**.  
    ![informace o exportu fakturace zobrazené na stránce Export fakturace](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Povolte rozhraní API Google Cloud Platform

Cloudyn shromažďovat údaje o využití a prostředků, potřebuje následující Google Cloud Platform rozhraní API povoleno:

- BigQuery rozhraní API
- Google Cloud SQL
- Google Cloud Datastore. rozhraní API
- Google Cloud Storage
- Google Cloud Storage JSON API
- Google Compute Engine rozhraní API

### <a name="enable-or-verify-apis"></a>Povolení nebo ověření rozhraní API

1. V konzole Google Cloud Platform vyberte projekt, který chcete použít k registraci ve službě Cloudyn.
2. Přejděte na **rozhraní api & Services** > **Library**.
3. Pomocí hledání můžete najít jednotlivé dříve uvedené rozhraní API.
4. U každého rozhraní API ověřte, že se zobrazuje **rozhraní API povoleno** . V opačném případě klikněte na tlačítko **Povolit**.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Přidání účtu Google Cloud Cloudyn

1. Otevřete portál Cloudyn z Azure Portal nebo přejděte na [https://azure.cloudyn.com](https://azure.cloudyn.com/) a přihlaste se.
2. Klikněte na **Nastavení** (ozubeného kola symbol) a pak vyberte **cloudové účty**.
3. V části **Správa účtů**vyberte kartu **účty Google** a pak klikněte na **Přidat nové +** .
4. Do pole **název účtu Google**zadejte e-mailovou adresu fakturačního účtu a pak klikněte na **Další**.
5. V dialogu ověřování Google vyberte nebo zadejte účet Google a pak **Povolte** přístup Cloudyn.com k vašemu účtu.
6. Přidat žádost o informace o projektu, že jste měli předchozí jste si poznamenali. Patří mezi ně **ID projektu**, název **projektu** , název **fakturačního** souboru a předpona sestavy **soubor** a potom klikněte na **Uložit**.  
    ![přidat projekt Google do účtu Cloudyn](./media/connect-google-account/add-project.png)

Váš účet Google se zobrazí v seznamu účtů a měl by se jednat o **ověřený**příkaz. V něm by měl název projektu Google a ID se zobrazí a mít symbol zelená značka zaškrtnutí. Stav účtu by měl být **dokončen**.

Během pár hodin sestavách Cloudyn zobrazit Google nákladů a využití informací.

## <a name="next-steps"></a>Další kroky

- Další informace o Cloudyn najdete v kurzu [Kontrola využití a nákladů](./tutorial-review-usage.md) pro Cloudyn.
