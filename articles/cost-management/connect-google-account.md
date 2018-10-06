---
title: Připojení účtu Google Cloud Platform do Cloudyn v Azure | Dokumentace Microsoftu
description: Připojení účtu Google Cloud Platform a zobrazit data o využití a nákladů v sestavách Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/05/2018
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: ''
ms.openlocfilehash: 48f1afcbef873e1af4346199c00ee2fadb1ad858
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2018
ms.locfileid: "48830238"
---
# <a name="connect-a-google-cloud-platform-account"></a>Připojení účtu Google Cloud Platform

Můžete připojit váš existující účet Google Cloud Platform do Cloudyn. Po propojení účtu do Cloudyn, nákladů a využití dat je k dispozici v sestavách Cloudyn. Tento článek pomůže vám pomůže nakonfigurovat a připojte si účet Google s Cloudyn.

> [!NOTE]
> Google byl změněn účet zabezpečení, což zabrání z zavedeno mezi Cloudyn a Google nová připojení. Cloudyn pokračuje ve shromažďování dat Google pro uživatele, kteří už mají Cloudyn připojené ke Googlu. Ale nemůže přidat nové účty Google do Cloudyn aktuálně. Cloudyn tým nebude vědět, kdy bude pokračovat, podpora pro přidávání nových účtů Google do Cloudyn. Po návratu podporu Odebereme tuto poznámku.

## <a name="collect-project-information"></a>Shromažďovat informace o projektu

Začnete tím, že shromažďování informací o projektu.

1. Přihlaste se ke konzole Google Cloud Platform na [ https://console.cloud.google.com ](https://console.cloud.google.com).
2. Projděte si informace o projektu, který chcete, aby se zapojit do Cloudyn a Všimněte si, **název projektu** a **ID projektu**. Zachovejte informace po ruce pro pozdější kroky.  
    ![Konzoly Google Cloud Platform](./media/connect-google-account/gcp-console01.png)
3. V případě fakturace není povolen a propojeny s projektem, vytvořte fakturační účet. Další informace najdete v tématu [vytvořit nové fakturační účet](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create\_a\_new\_billing\_account).

## <a name="enable-storage-bucket-billing-export"></a>Povolit export fakturační kontejneru úložiště

Cloudyn načte vašich fakturačních dat Google z kontejneru úložiště. Zachovat **název sektoru** a **předpona sestavy** informace po ruce pro pozdější použití. při registraci do Cloudyn.

Použití Google Cloud Storage pro uložení sestavy o využití se účtují minimální poplatky. Další informace najdete v tématu [ceny za úložiště cloudu](https://cloud.google.com/storage/pricing).

1. Pokud jste ještě nepovolili fakturační export do souboru, postupujte podle pokynů na adrese [povolení fakturace export do souboru](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). Můžete použít JSON nebo CSV fakturační formát exportu.
2. V opačném případě se v konzole Google Cloud Platform přejděte do **fakturace** > **fakturace export**. Všimněte si fakturace **název sektoru** a **předpona sestavy**.  
    ![Export fakturace](./media/connect-google-account/billing-export.png)

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
2. Přejděte do **rozhraní API a služby** > **knihovny**.
3. Pomocí hledání můžete najít jednotlivé dříve uvedené rozhraní API.
4. Pro každé rozhraní API, ověřte, že **rozhraní API povoleno** se zobrazí. V opačném případě klikněte na tlačítko **povolit**.

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Přidání účtu Google Cloud Cloudyn

1. Otevřete portál Cloudyn z portálu Azure portal nebo přejděte na [ https://azure.cloudyn.com ](https://azure.cloudyn.com/) a přihlaste se.
2. Klikněte na tlačítko **nastavení** (symbol ozubeného kola) a pak vyberte **cloudové účty**.
3. V **Správa účtů**, vyberte **účty Google** kartu a potom klikněte na tlačítko **přidat nový +**.
4. V **název účtu služby Google**, zadejte e-mailovou adresu pro fakturační účet a pak klikněte na tlačítko **Další**.
5. V dialogovém okně ověřování Google, vyberte nebo zadejte účet Google a následně **povolit** cloudyn.com přístup k vašemu účtu.
6. Přidat žádost o informace o projektu, že jste měli předchozí jste si poznamenali. Patří mezi ně **ID projektu**, **projektu** název, **fakturační** název kontejneru a **fakturační souboru** předpona sestavy a potom klikněte na  **Uložit**.  
    ![Přidání projektu Google](./media/connect-google-account/add-project.png)

Váš účet Google se zobrazí v seznamu účtů a by mělo být uvedeno **ověřený**. V něm by měl název projektu Google a ID se zobrazí a mít symbol zelená značka zaškrtnutí. Stav účtu by mělo být uvedeno **dokončeno**.

Během pár hodin sestavách Cloudyn zobrazit Google nákladů a využití informací.

## <a name="next-steps"></a>Další postup

- Chcete-li další informace o Cloudyn, pokračujte [kontrola využití a nákladů](./tutorial-review-usage.md) kurz pro Cloudyn.
