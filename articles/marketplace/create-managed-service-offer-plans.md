---
title: Jak vytvořit plány pro nabídku spravované služby na Azure Marketplace
description: Naučte se vytvářet plány pro nabídku spravované služby na Azure Marketplace pomocí partnerského centra Microsoftu.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 9b5526af03bdbefeb54633c49bbd43743555f60b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383224"
---
# <a name="how-to-create-plans-for-your-managed-service-offer"></a>Jak vytvořit plány pro nabídku spravované služby

Nabídky spravované služby prodávané prostřednictvím komerčního tržiště Microsoftu musí mít aspoň jeden plán. V rámci jedné nabídky můžete vytvořit nejrůznější plány s různými možnostmi. Tyto plány (někdy označované jako SKU) se mohou lišit podle verze, finanční zhodnocení nebo vrstev služby. Podrobné pokyny k plánům najdete v tématu [plány a ceny pro komerční nabídky na webu Marketplace](./plans-pricing.md).

## <a name="create-a-plan"></a>Vytvoření plánu

1. Na kartě **Přehled plánu** vaší nabídky v partnerském centru vyberte **+ vytvořit nový plán**.
2. V dialogovém okně, které se zobrazí, zadejte do pole **ID plánu** jedinečný identifikátor ID plánu. Použijte až 50 malých alfanumerických znaků, pomlčky nebo podtržítka. Po zvolení **Create (vytvořit**) nemůžete změnit ID plánu. Toto ID uvidí vaši zákazníci.
3. Do pole **název plánu** zadejte jedinečný název pro tento plán. Použijte maximálně 50 znaků. Tento název uvidí vaši zákazníci.
4. Vyberte **Vytvořit**.

## <a name="define-the-plan-listing"></a>Definice výpisu plánu

Na kartě **seznam plánů** zadejte název a popis plánu, jak se mají zobrazit na komerčním webu Marketplace.

1. V poli **název plánu** se zobrazí název, který jste zadali dříve pro tento plán. Kdykoli ho můžete změnit. Tento název se zobrazí na komerčním webu Marketplace jako název plánu vaší nabídky.
2. V poli **Souhrn plánu** zadejte krátký popis plánu, který se dá použít ve výsledcích hledání na webu Marketplace.
3. V poli **Popis plánu** vysvětlete, co dělá tento plán jedinečný a odlišný od ostatních plánů v rámci vaší nabídky.
4. Než budete pokračovat na další kartu, vyberte **Uložit koncept** .

## <a name="define-pricing-and-availability"></a>Definování cen a dostupnosti

Jediným cenovým modelem dostupným pro nabídky spravované služby je **vlastní licence (BYOL)**. To znamená, že vaše zákazníky budete fakturovat přímo za náklady související s touto nabídkou a Microsoft vám nebude účtovat žádné poplatky.

Každý plán můžete nakonfigurovat tak, aby byl viditelný pro všechny (veřejné) nebo jenom pro konkrétní cílovou skupinu (soukromá).

> [!NOTE]
> U předplatných vytvořených prostřednictvím prodejce programu Cloud Solution Provider (CSP) se nepodporují privátní plány.

> [!IMPORTANT]
> Jakmile je plán publikovaný jako veřejný, nemůžete ho změnit na privátní. Chcete-li určit, kteří zákazníci mohou přijmout vaši nabídku a delegovat prostředky, použijte privátní plán. S veřejným plánem nemůžete omezit dostupnost na konkrétní zákazníky nebo dokonce na určitý počet zákazníků (i když se rozhodnete, že můžete plán úplně zastavit). Přístup k delegování můžete odebrat poté, co zákazník přijme nabídku, pouze pokud jste při publikování této nabídky zazahrnuli autorizaci s definicí role nastavenou na možnost přiřazení registrace spravovaných služeb. Můžete se také obrátit na zákazníka a požádat ho, aby váš přístup odebral.

## <a name="make-your-plan-public"></a>Nastavte svůj plán jako veřejný

1. V části **plán viditelnost** vyberte možnost **veřejné**.
2. Vyberte **Uložit koncept**. Pokud se chcete vrátit na kartu Přehled plánu, v levém horním rohu vyberte **plán – přehled** .
3. Pokud chcete pro tuto nabídku vytvořit další plán, vyberte **+ vytvořit nový plán** na kartě **Přehled plánu** .

## <a name="make-your-plan-private"></a>Nastavit váš plán jako soukromý

Přístup k privátnímu plánu udělíte pomocí ID předplatných Azure. Pomocí můžete přidat maximálně 10 ID předplatných nebo až 10 000 ID předplatných. Soubor CSV.

Ruční přidání až 10 ID předplatných:

1. V části **plán viditelnost** vyberte **soukromé**.
2. Zadejte ID předplatného Azure cílové skupiny, ke které chcete udělit přístup.
3. Volitelně můžete do pole **Popis** zadat popis této cílové skupiny.
4. Pokud chcete přidat další ID, vyberte **Přidat ID (max. 10)**.
5. Až skončíte s přidáváním ID, vyberte **Uložit koncept**.

Chcete-li přidat až 10 000 ID předplatných s. Soubor CSV:

1. V části **plán viditelnost** vyberte **soukromé**.
2. Vyberte odkaz **exportovat cílovou skupinu (CSV)** . Tím se stáhne. Soubor CSV.
3. Otevřete. Soubor CSV. Do sloupce **ID** zadejte ID předplatného Azure, ke kterým chcete udělit přístup.
4. Ve sloupci **Popis**   máte možnost přidat popis pro každou položku.
5. Do sloupce **typ**   přidejte **SubscriptionId**   do každého řádku, který má ID.
6. Uložte soubor jako. Soubor CSV.
7. V partnerském centru vyberte odkaz **importovat cílovou skupinu (CSV)** .
8. V ****   dialogovém okně Potvrdit vyberte **Ano** a potom nahrajte. Soubor CSV.
9. Vyberte **Uložit koncept**.

## <a name="technical-configuration"></a>Technická konfigurace

V této části se vytvoří manifest s autorizačními informacemi pro správu zákaznických prostředků. Tyto informace jsou nutné, aby bylo možné povolit [správu delegovaných prostředků Azure](../lighthouse/concepts/azure-delegated-resource-management.md).

Projděte si [klienty, role a uživatele ve scénářích Azure Lighthouse](../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) , abyste zjistili, které role jsou podporované, a osvědčené postupy pro definování autorizací.

> [!NOTE]
> Uživatelé a role v autorizačních položkách budou platit pro každého zákazníka, který plán aktivuje. Pokud chcete omezit přístup na konkrétního zákazníka, budete muset publikovat privátní plán pro výhradní použití.

### <a name="manifest"></a>Manifest

1. V části **manifest** zadejte **verzi** manifestu. Použijte formát n. n. n (například 1.2.5).
2. Zadejte **ID tenanta**. Toto je identifikátor GUID, který je přidružený k ID tenanta Azure Active Directory (Azure AD) vaší organizace. To znamená, že se jedná o tenanta správy, ze kterého budete přistupovat k prostředkům vašich zákazníků. Pokud tyto možnosti nemáte k dispozici, můžete ji najít přesunutím ukazatele myši na název účtu v pravém horním rohu Azure Portal, nebo výběrem **přepínače Adresář**.

Pokud publikujete novou verzi nabídky a potřebujete vytvořit aktualizovaný manifest, vyberte **+ nový manifest**. Ujistěte se, že jste nazvětšili číslo verze z předchozí verze manifestu.

### <a name="authorizations"></a>Autorizace

Autorizace definují entity ve vašem spravovaném tenantovi, kteří mají přístup k prostředkům a předplatným pro zákazníky, kteří si plán kupují. Každá z těchto entit má přiřazenou předdefinovanou roli, která uděluje konkrétní úroveň přístupu.

Pro každý plán můžete vytvořit až 20 autorizací.

> [!TIP]
> Ve většině případů budete chtít přiřadit role k skupině uživatelů nebo instančnímu objektu služby Azure AD, a ne k řadě jednotlivých uživatelských účtů. To vám umožní přidat nebo odebrat přístup pro jednotlivé uživatele bez nutnosti aktualizace a opětovného publikování plánu, když se změní vaše požadavky na přístup. Při přiřazování rolí do skupin Azure AD [by měl být typ skupiny zabezpečení a ne sada Office 365](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Další doporučení najdete v tématu [klienti, role a uživatelé ve scénářích Azure Lighthouse](../lighthouse/concepts/tenants-users-roles.md).

Pro každou autorizaci musíte zadat následující informace. Pak můžete vybrat **+ Přidat autorizaci** tolikrát, kolikrát je potřeba, a přidat další uživatele a definice rolí.

* **ID objektu AAD**: identifikátor Azure AD uživatele, skupiny uživatelů nebo aplikace, kterým budou udělena určitá oprávnění (definovaná v definici role) pro prostředky vašich zákazníků.
* **Zobrazovaný název objektu AAD**: popisný název, který zákazníkovi pomůže pochopit účel této autorizace. Zákazník uvidí tento název při delegování prostředků.
* **Definice role**: v seznamu vyberte jednu z dostupných předdefinovaných rolí Azure AD. Tato role určuje oprávnění, která bude mít uživatel v poli **ID objektu zabezpečení** u prostředků vašich zákazníků. Popisy těchto rolí najdete v tématu [předdefinované role](../role-based-access-control/built-in-roles.md) a [Podpora rolí pro Azure Lighthouse](../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse).

> [!NOTE]
> V případě, že jsou v Azure přidané nové předdefinované role, budou k dispozici zde. Před zobrazením může dojít ke zpoždění.

* **Přiřaditelné role**: Tato možnost se zobrazí jenom v případě, že jste pro tuto autorizaci vybrali správce přístupu uživatele v **definici role** . Pokud ano, musíte sem přidat jednu nebo více rolí, které lze přiřadit. Uživatel v poli **ID objektu Azure AD** bude moct přiřadit tyto role ke spravovaným identitám, které se vyžadují k [nasazení zásad, které se dají opravit](../lighthouse/how-to/deploy-policy-remediation.md). Pro tohoto uživatele se nebudou vztahovat žádná další oprávnění normálně přidružená k roli správce přístupu uživatele.

> [!TIP]
> Aby bylo možné v případě potřeby [Odebrat přístup k delegování](../lighthouse/how-to/remove-delegation.md) , zahrňte **autorizaci** s **definicí role** nastavenou na [přiřazení registrace spravovaných služeb odstranit roli](../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Pokud tato role není přiřazená, delegované prostředky může odebrat jenom uživatel v tenantovi zákazníka.

Po dokončení všech oddílů pro váš plán můžete vybrat **+ vytvořit nový plán** a vytvořit další plány. Až budete hotovi, vyberte **Uložit koncept** a teprve potom pokračujte.

## <a name="next-steps"></a>Další kroky

* [Kontrola a publikování](review-publish-offer.md)
