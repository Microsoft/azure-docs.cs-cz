---
title: Připojení účtu Google Cloud Platform ke službě Cloudyn v Azure
description: Připojte účet Google Cloud Platform, abyste mohli v sestavách Cloudyn prohlížet data o nákladech a využití.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 8d549a8fd7c8b03f98fe2b11c94531ed323e0e3a
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2020
ms.locfileid: "92526411"
---
# <a name="connect-a-google-cloud-platform-account"></a>Připojení účtu Google Cloud Platform

Svůj stávající účet Google Cloud Platform můžete připojit ke službě Cloudyn. Po připojení účtu ke službě Cloudyn budou data o nákladech a využití k dispozici v sestavách Cloudyn. Tento článek vám pomůže nakonfigurovat a připojit váš účet Google ke Cloudynu.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="collect-project-information"></a>Shromáždění informací o projektu

Nejprve shromážděte informace o projektu.

1. Přihlaste se ke konzole Google Cloud Platform na adrese [https://console.cloud.google.com](https://console.cloud.google.com).
2. Zkontrolujte informace o projektu, který chcete nasadit do Cloudynu, a poznamenejte si **název projektu** a **ID projektu** . Informace si nechce po ruce pro další kroky.  
    ![Název a ID projektu zobrazené na konzole Google Cloud Platform](./media/connect-google-account/gcp-console01.png)
3. Pokud nemáte v projektu povolenou a propojenou fakturaci, vytvořte fakturační účet. Další informace najdete v tématu o [vytvoření nového fakturačního účtu](https://cloud.google.com/billing/docs/how-to/manage-billing-account#create/_a/_new/_billing/_account).

## <a name="enable-storage-bucket-billing-export"></a>Povolení exportu fakturace kbelíku úložiště

Cloudyn načítá vaše fakturační data Google z kbelíku úložiště. **Název kbelíku** a **předponu sestavy** si nechte po ruce, abyste je mohli později použít při registraci do Cloudynu.

Za použití cloudového úložiště Googlu k ukládání sestav o využití se účtují minimální poplatky. Další informace najdete v tématu o [cenách cloudového úložiště](https://cloud.google.com/storage/pricing).

1. Pokud jste nepovolili export fakturace do souboru, postupujte podle pokynů v tématu o [povolení exportu fakturace do souboru](https://cloud.google.com/billing/docs/how-to/export-data-file#how_to_enable_billing_export_to_a_file). K exportu fakturace můžete použít formát JSON nebo CSV.
2. Na konzole Google Cloud Platform přejděte na **Billing** > **Billing export** (Fakturace > Export fakturace). Poznamenejte si **Bucket name** (Název kbelíku) a **Report prefix** (Předponu sestavy) pro fakturaci.  
    ![Informace o exportu fakturace zobrazené na stránce exportu fakturace](./media/connect-google-account/billing-export.png)

## <a name="enable-google-cloud-platform-apis"></a>Povolení rozhraní API služeb Google Cloud Platform

Ke shromažďování informací o využití a prostředcích potřebuje Cloudyn povolit následující rozhraní API služeb Google Cloud Platform:

- Rozhraní API pro BigQuery
- Google Cloud SQL
- Rozhraní API pro Google Cloud Datastore
- Cloudové úložiště Googlu
- Rozhraní API jazyka JSON pro Google Cloud Storage
- Rozhraní API pro Google Compute Engine

### <a name="enable-or-verify-apis"></a>Povolení nebo ověření rozhraní API

1. Na konzole Google Cloud Platform vyberte projekt, který chcete zaregistrovat do Cloudynu.
2. Přejděte na **APIs & Services** > **Library** (Rozhraní API a služby > Knihovna).
3. K vyhledání předchozích uvedených rozhraní API použijte vyhledávání.
4. U každého rozhraní API ověřte, že se zobrazuje **povolené rozhraní API** . Pokud tomu tak není, klikněte na **ENABLE** (POVOLIT).

## <a name="add-a-google-cloud-account-to-cloudyn"></a>Přidání účtu Google Cloud do služby Cloudyn

1. Portál Cloudyn můžete otevřít z webu Azure Portal nebo můžete přejít na adresu [https://azure.cloudyn.com](https://azure.cloudyn.com/) a přihlásit se.
2. Klikněte na **Nastavení** (symbol ozubeného kola) a vyberte **Cloud Accounts** (Cloudové účty).
3. Ve **Správě účtů** vyberte **Účty Google** a klikněte na **Přidat nový +** .
4. Do pole **Google Account Name** (Název účtu Google) zadejte e-mailovou adresu fakturačního účtu a klikněte na **Next** (Další).
5. V dialogovém okně ověřování Google vyberte nebo zadejte účet Google a potom vyberte **ENABLE** (POVOLIT) webu cloudyn.com přístup ke svému účtu.
6. Přidejte informace o požadovaném projektu, které jste si předtím poznamenali. Obsahují **ID projektu** , název **projektu** , název **fakturačního** kbelíku a předponu sestavy **fakturačního souboru** . Pak klikněte na **Uložit** .  
    ![Přidání projektu Google do účtu Cloudyn](./media/connect-google-account/add-project.png)

Váš účet Google se zobrazí v seznamu účtů s označením **Authenticated** (Ověřený). Pod ním by měl být název a ID projektu Google, které by u sebe měly mít zelené zaškrtnutí. Stav účtu by měl být **Completed** (Dokončeno).

Za pár hodin se budou v sestavách Cloudynu zobrazovat informace o nákladech a využití projektu Google.

## <a name="next-steps"></a>Další kroky

- Pokud se chcete dozvědět více o Cloudynu, pokračujte kurzem o [kontrole využití a nákladů](tutorial-review-usage.md) v Cloudynu.
