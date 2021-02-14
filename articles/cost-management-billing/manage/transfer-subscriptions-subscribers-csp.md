---
title: Přenos předplatných Azure mezi předplatiteli a CSP
description: Přečtěte si, jak můžete přenést předplatná Azure mezi předplatiteli a CSP.
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 02/11/2021
ms.author: banders
ms.openlocfilehash: 63fbf76b2211e530707f3598d176b646c317cc53
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363045"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>Přenos předplatných Azure mezi předplatiteli a CSP

Tento článek popisuje hlavní kroky, které slouží k přenosu předplatných Azure mezi partnery CSP (Cloud Solution Provider) a jejich zákazníky. Účelem zde uvedených informací je pomoct předplatiteli Azure domluvit se s partnerem. Informace o procesu převodu pro partnery Microsoftu jsou uvedené v tématu [Převod předplatných Azure zákazníka k jinému partnerovi](/partner-center/switch-azure-subscriptions-to-a-different-partner).

Před zahájením žádosti o přenos byste si měli stáhnout nebo vyexportovat všechny informace o nákladech a fakturační údaje, které chcete zachovat. Informace o fakturaci a využití se s předplatným nepřevádějí. Další informace o exportu údajů o správě nákladů najdete v tématu věnovaném [vytvoření a správě exportovaných dat](../costs/tutorial-export-acm-data.md). Další informace o stažení faktury a údajů o využití najdete v tématu [Stažení nebo zobrazení faktury za Azure a dat o denním využití](download-azure-invoice-daily-usage-date.md).

Pokud máte nějaké existující rezervace, po převodu předplatného se jejich použití zastaví. Před převodem předplatného nezapomeňte [zrušit všechny rezervace a refundovat je](../reservations/exchange-and-refund-azure-reservations.md).

## <a name="transfer-ea-subscriptions-to-a-csp-partner"></a>Převody předplatných EA na partnera CSP

Partneři poskytovatelů CSP s přímým účtováním, kteří jsou certifikovaní jako [Poskytovatelé spravovaných služeb Azure Expert (MSP)](https://partner.microsoft.com/membership/azure-expert-msp), můžou požádat o přenos předplatných Azure pro své zákazníky, kteří mají přímou smlouvu Enterprise (EA). Přenosy předplatných jsou povolené pouze pro zákazníky, kteří přijali Smlouvu se zákazníkem Microsoftu (MCA) a koupili si plán Azure v rámci programu CSP.

Po schválení žádosti může CSP poskytnout zákazníkům kombinovanou fakturu. Další informace o přenosu předplatných poskytovateli CSP najdete v tématu [Získání vlastnictví fakturace předplatných Azure pro váš účet MPA](mpa-request-ownership.md).

>[!IMPORTANT]
> Po přenosu předplatného na partnera CSP se veškerá zvýšení kvót dříve použitá u předplatného EA obnoví na výchozí hodnoty. Pokud je po přenosu předplatného vyžadována dodatečná kvóta, nechte poskytovatele CSP odeslat žádost o [zvýšení kvóty](../../azure-portal/supportability/regional-quota-requests.md). 

## <a name="other-subscription-transfers-to-a-csp-partner"></a>Přenosy dalších předplatných na partnera CSP

Aby bylo možné přenést jakékoli jiné předplatné Azure na partnera CSP, musí předplatitel přesunout prostředky ze zdrojových předplatných do předplatných poskytovatele CSP. K přesunutí prostředků mezi předplatnými postupujte podle následujících pokynů.

1. Navažte u zákazníka [vztah prodejce](/partner-center/request-a-relationship-with-a-customer) . Seznamte se s [přehledem regionálních autorizací CSP](/partner-center/regional-authorization-overview) a zajistěte, aby všichni klienti i partneři v rámci stejných autorizovaných oblastí.
1. Spolupracujte se svým partnerem CSP a vytvořte cílová předplatná Azure CSP.
1. Ujistěte se, že předplatná zdrojového a cílového poskytovatele CSP jsou ve stejném tenantovi Azure Active Directory (Azure AD).  
    Pro předplatné Azure CSP nemůžete změnit tenanta Azure AD. Místo toho je nutné přidat nebo přidružit zdrojové předplatné k tenantovi služby Azure AD poskytovatele CSP. Další informace najdete v tématu [Přiřazení nebo přidání předplatného Azure do tenanta Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    > [!IMPORTANT]
    > - Když přidružíte předplatné k jinému adresáři služby Azure AD, ztratí přístup uživatelé, kteří mají přiřazené role Azure pomocí [řízení přístupu na základě role (RBAC) v Azure](../../role-based-access-control/role-assignments-portal.md). Klasičtí správci předplatných, včetně správců služeb a spolusprávců, také ztratí přístup.
    > - Z předplatného se také odstraní přiřazení zásad, jakmile se předplatné přidruží k jinému adresáři.
1. Uživatelský účet, který použijete k provedení převodu, musí mít u obou předplatných přístup vlastníka [Azure RBAC](add-change-subscription-administrator.md).
1. Než začnete, [ověřte](/rest/api/resources/resources/validatemoveresources), že se všechny prostředky Azure můžou přesunout ze zdrojového předplatného do cílového předplatného.  
    Některé prostředky Azure nejde přesunout mezi předplatnými. Úplný seznam prostředků Azure, které se dají přesunout, najdete v tématu [Podpora operací přesunutí pro prostředky](../../azure-resource-manager/management/move-support-resources.md).
    > [!IMPORTANT]
    >  - Azure CSP podporuje jenom prostředky služby Azure Resource Manager. Pokud byly nějaké prostředky Azure ve zdrojovém předplatném vytvořeny pomocí modelu nasazení Azure Classic, musíte je migrovat do služby [Azure Resource Manager](/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm) před migrací. Aby bylo možné zobrazit webovou stránku, musíte být partnerem.

1. Ověřte, že všechny služby zdrojového předplatného používají model Azure Resource Manager. Potom přeneste prostředky ze zdrojového předplatného do cílového předplatného pomocí [přesunu prostředků Azure](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Přesunutí prostředků Azure mezi předplatnými může vést k výpadkům služby na základě prostředků v předplatných.

## <a name="transfer-csp-subscription-to-other-offer"></a>Převod předplatného CSP na jinou nabídku

Aby bylo možné přenést jakékoli další odběry od partnera CSP do jakékoli jiné nabídky Azure, musí předplatitel přesunout prostředky mezi zdrojovými předplatnými a cílovými předplatnými CSP.

1. Vytvořte cílová předplatná Azure.
1. Ujistěte se, že zdrojová a cílová předplatná jsou ve stejném tenantovi Azure Active Directory (Azure AD). Další informace o změnách tenanta Azure AD najdete v tématu [Přiřazení nebo přidání předplatného Azure do tenanta Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    Upozorňujeme, že možnost změny adresáře se pro předplatné CSP nepodporuje. Budete například přenášet předplatné z CSP na předplatné s průběžnými platbami. Je potřeba změnit adresář předplatného s průběžnými platbami tak, aby se s tímto shodoval.

    > [!IMPORTANT]
    >  - Když přidružíte předplatné k jinému adresáři, uživatelé, kteří mají přiřazené role pomocí [Azure RBAC](../../role-based-access-control/role-assignments-portal.md), k němu ztratí přístup. Klasičtí správci předplatných, včetně správců služeb a spolusprávců, také ztratí přístup.
    >  - Z předplatného se také odstraní přiřazení zásad, jakmile se předplatné přidruží k jinému adresáři.

1. Uživatelský účet, který použijete k provedení převodu, musí mít u obou předplatných přístup vlastníka [Azure RBAC](add-change-subscription-administrator.md).
1. Než začnete, [ověřte](/rest/api/resources/resources/validatemoveresources), že se všechny prostředky Azure můžou přesunout ze zdrojového předplatného do cílového předplatného.
    > [!IMPORTANT]
    >  - Některé prostředky Azure nejde přesunout mezi předplatnými. Úplný seznam prostředků Azure, které se dají přesunout, najdete v tématu [Podpora operací přesunutí pro prostředky](../../azure-resource-manager/management/move-support-resources.md).

1. Přeneste prostředky ze zdrojového předplatného do cílového předplatného pomocí [přesunu prostředků Azure](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Přesunutí prostředků Azure mezi předplatnými může vést k výpadkům služby na základě prostředků v předplatných.

## <a name="next-steps"></a>Další kroky
- [Získejte vlastnictví fakturace předplatných Azure pro vlastní účet se smlouvou MPA](mpa-request-ownership.md).
- Přečtěte si o tom, jak [spravovat účty a předplatná pomocí fakturace Azure](../index.yml).
