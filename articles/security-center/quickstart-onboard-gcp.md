---
title: Připojte svůj účet GCP k Azure Security Center
description: Monitorování prostředků GCP z Azure Security Center
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 71f1de7b4ff265a5740181a2bb2032f33a83abe3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91448992"
---
#  <a name="connect-your-gcp-accounts-to-azure-security-center"></a>Připojení účtů GCP k Azure Security Center

Cloudové úlohy běžně pokrývá několik cloudových platforem, ale cloudové služby zabezpečení musí provádět stejné.

Azure Security Center chrání úlohy v Azure, Amazon Web Services (AWS) a Google Cloud Platform (GCP).

Při připojování účtu GCP do Security Center se integruje příkaz GCP Security a Azure Security Center. Security Center proto zajišťuje viditelnost a ochranu v obou těchto cloudových prostředích, aby poskytovala tyto informace:

- Zjišťování nezabezpečených konfigurací zabezpečení
- Jediné zobrazení, které zobrazuje doporučení Security Center GCP a Security Center – zjištění centra příkazů
- Začlenění prostředků GCP do výpočtů zabezpečeného skóre Security Center
- Integrace doporučení centra příkazů zabezpečení GCP na základě standardu CIS do řídicího panelu dodržování předpisů v Security Center

Na snímku obrazovky níže vidíte projekty GCP zobrazené na řídicím panelu přehled Security Center.

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="3 projekty GCP uvedené na řídicím panelu přehled Security Center" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>Dostupnost

|Aspekt|Podrobnosti|
|----|:----|
|Stav vydaných verzí:|Preview|
|Stanov|Vyžaduje [Azure Defender pro servery](defender-for-servers-introduction.md) .|
|Požadované role a oprávnění:|**Vlastník** nebo **Přispěvatel** v příslušném předplatném Azure|
|Cloud|![Ano](./media/icons/yes-icon.png) Komerční cloudy<br>![Ne](./media/icons/no-icon.png) National/svrchovaná (US Gov, Čína gov, ostatní gov)|
|||

## <a name="connect-your-gcp-account"></a>Připojení účtu GCP

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>Krok 1. Nastavení centra příkazů zabezpečení GCP s analýzou stavu zabezpečení

Pro všechny projekty GCP ve vaší organizaci musíte taky:

1. Pomocí [těchto pokynů v dokumentaci k GCP](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup)nastavte **GCP Security Command Center** .
1. Pomocí [těchto pokynů v dokumentaci k GCP](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics)povolte **analýzu stavu zabezpečení** .
1. Ověřte, že se v centru příkazů zabezpečení nachází tok dat.

Pokyny pro připojení prostředí GCP pro konfiguraci zabezpečení následují po doporučeních společnosti Google pro využívání doporučení pro konfiguraci zabezpečení. Integrace využívá centrum příkazů Google Security a bude využívat další zdroje, které by mohly mít dopad na vaši fakturaci.

Když poprvé povolíte analýzu stavu zabezpečení, může trvat několik hodin, než budou data k dispozici.


### <a name="step-2-enable-gcp-security-command-center-api"></a>Krok 2. Povolit rozhraní příkazového centra pro GCP zabezpečení

1. V **knihovně rozhraní API pro cloudovou konzolu**Google vyberte projekt, ke kterému se chcete připojit Azure Security Center.
1. V knihovně rozhraní API vyhledejte a vyberte **zabezpečení Security Center API**.
1. Na stránce rozhraní API vyberte **Povolit**.

Přečtěte si další informace o [rozhraní příkazového centra pro zabezpečení](https://cloud.google.com/security-command-center/docs/reference/rest/).


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>Krok 3. Vytvoření vyhrazeného účtu služby pro integraci konfigurace zabezpečení

1. V **konzole GCP**vyberte projekt, ke kterému se chcete připojit Security Center.
1. V **navigační nabídce**v části **IAM & možnosti správy** vyberte **účty služeb**.
1. Vyberte **vytvořit účet služby**.
1. Zadejte název účtu a vyberte **vytvořit**.
1. Jako **Security Center admin Viewer**zadejte **roli** a vyberte **pokračovat**.
1. Oddíl **udělení přístupu uživatelům k tomuto účtu služby** je nepovinný. Vyberte **Hotovo**.
1. Zkopírujte **hodnotu e-mailu** vytvořeného účtu služby a uložte ji pro pozdější použití.
1. V **navigační nabídce**v části **IAM & možnosti správy** vyberte **IAM**
    1. Přepněte na úroveň organizace.
    1. Vyberte **Přidat**.
    1. Do pole **noví členové** vložte **hodnotu e-mailu** , kterou jste zkopírovali dříve.
    1. Zadejte roli jako **Security Center admin Viewer** a pak vyberte Uložit.
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="3 projekty GCP uvedené na řídicím panelu přehled Security Center":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>Krok 4: Vytvořit privátní klíč pro vyhrazený účet služby
1. Přepněte na úroveň projektu.
1. V **navigační nabídce**v části **IAM & možnosti správy** vyberte **účty služeb**.
1. Otevřete vyhrazený účet služby a vyberte Upravit.
1. V části **klíče** vyberte **Přidat klíč** a pak **vytvořte nový klíč**.
1. Na obrazovce vytvořit privátní klíč vyberte **JSON**a pak vyberte **vytvořit**.
1. Uložte tento soubor JSON pro pozdější použití.


### <a name="step-5-connect-gcp-to-security-center"></a>Krok 5. Připojení GCP k Security Center 
1. V nabídce Security Center vyberte **cloudové konektory**.
1. Vyberte Přidat účet GCP.
1. Na stránce zprovoznění proveďte následující a potom vyberte **Další**.
    1. Ověří zvolený odběr.
    1. Do pole **Zobrazovaný název** zadejte zobrazovaný název konektoru.
    1. V poli **ID organizace** zadejte ID vaší organizace. Pokud ho neznáte, přečtěte si téma [vytváření a Správa organizací](https://cloud.google.com/resource-manager/docs/creating-managing-organization).
    1. V poli soubor **privátního klíče** vyhledejte soubor JSON, který jste stáhli v [kroku 4. Vytvořte privátní klíč pro vyhrazený účet služby](#step-4-create-a-private-key-for-the-dedicated-service-account).


### <a name="step-6-confirmation"></a>Krok 6. Confirmation (Potvrzení)

Po úspěšném vytvoření konektoru a GCP Security Center je správně nakonfigurovaný.

- Standard GCP CIS se zobrazí v řídicím panelu dodržování předpisů pro Security Center.
- Doporučení zabezpečení pro prostředky GCP se zobrazí na portálu Security Center a na řídicím panelu dodržování předpisů 5-10 minut po dokončení zprovoznění:   :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="3 projekty GCP uvedené na řídicím panelu přehled Security Center":::


## <a name="monitoring-your-gcp-resources"></a>Monitorování prostředků GCP

Jak vidíte výše, stránka doporučení pro zabezpečení Azure Security Center zobrazuje vaše prostředky GCP společně s prostředky Azure a AWS pro opravdové zobrazení s více cloudy.

Pokud chcete zobrazit všechna aktivní doporučení pro vaše prostředky podle typu prostředku, použijte stránku inventáře assetů Security Center a filtrujte na typ prostředku GCP, ve kterém máte zájem:

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="3 projekty GCP uvedené na řídicím panelu přehled Security Center"::: 


## <a name="next-steps"></a>Další kroky

Připojení účtu GCP je součástí prostředí s více cloudy, které je dostupné v Azure Security Center. Související informace najdete na následující stránce:

- [Připojení účtů AWS k Azure Security Center](quickstart-onboard-aws.md)
