---
title: Přenos předplatných Azure mezi předplatiteli a CSP
description: Přečtěte si, jak můžete přenést předplatná Azure mezi předplatiteli a CSP.
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: banders
ms.openlocfilehash: 4e7e9ea61d74eb38f3c225d66a39906bc18fe6d2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77200516"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>Přenos předplatných Azure mezi předplatiteli a CSP

Tento článek popisuje hlavní kroky, které slouží k přenosu předplatných Azure mezi partnery CSP (Cloud Solution Provider) a jejich zákazníky.

## <a name="transfer-ea-subscriptions"></a>Přenos předplatných EA

Partneři poskytovatelů CSP s přímým účtováním, kteří jsou certifikovaní jako [Poskytovatelé spravovaných služeb Azure Expert (MSP)](https://partner.microsoft.com/membership/azure-expert-msp), můžou požádat o přenos předplatných Azure pro své zákazníky, kteří mají přímou smlouvu Enterprise (EA). Přenosy předplatného jsou povolené jenom pro zákazníky, kteří přijali Smlouvu se zákazníkem Microsoftu (MCA) a koupili si plán Azure.

Po schválení žádosti může CSP poskytnout zákazníkům kombinovanou fakturu. Další informace o přenosu předplatných poskytovateli CSP najdete v tématu [Získání vlastnictví fakturace předplatných Azure pro váš účet MPA](mpa-request-ownership.md).

## <a name="other-subscription-transfers-to-a-csp-partner"></a>Přenosy dalších předplatných na partnera CSP

Aby bylo možné přenést jakékoli jiné předplatné Azure na partnera CSP, musí předplatitel přesunout prostředky ze zdrojových předplatných do předplatných poskytovatele CSP. K přesunutí prostředků mezi předplatnými postupujte podle následujících pokynů.

1. Spolupracujte se svým partnerem CSP a vytvořte cílová předplatná Azure CSP.
1. Ujistěte se, že předplatná zdrojového a cílového poskytovatele CSP jsou ve stejném tenantovi Azure Active Directory (Azure AD).  
    Pro předplatné Azure CSP nemůžete změnit tenanta Azure AD. Místo toho je nutné přidat nebo přidružit zdrojové předplatné k tenantovi služby Azure AD poskytovatele CSP. Další informace najdete v tématu [Přiřazení nebo přidání předplatného Azure do tenanta Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    > [!IMPORTANT]
    > - Když přidružíte předplatné k jinému adresáři služby Azure AD, ztratí přístup uživatelé, kteří mají přiřazené role pomocí [řízení přístupu na základě role (RBAC)](../../role-based-access-control/role-assignments-portal.md). Klasičtí správci předplatných, včetně správců služeb a spolusprávců, také ztratí přístup.
    > - Z předplatného se také odstraní přiřazení zásad, jakmile se předplatné přidruží k jinému adresáři.
1. Uživatelský účet, který použijete k provedení přenosu, musí mít u obou předplatných přístup vlastníka [RBAC](add-change-subscription-administrator.md).
1. Než začnete, [ověřte](/rest/api/resources/resources/validatemoveresources), že se všechny prostředky Azure můžou přesunout ze zdrojového předplatného do cílového předplatného.  
    Některé prostředky Azure nejde přesunout mezi předplatnými. Úplný seznam prostředků Azure, které se dají přesunout, najdete v tématu [Podpora operací přesunutí pro prostředky](../../azure-resource-manager/management/move-support-resources.md).
    > [!IMPORTANT]
    >  - Azure CSP podporuje jenom prostředky služby Azure Resource Manager. Pokud byly nějaké prostředky Azure ve zdrojovém předplatném vytvořeny pomocí modelu nasazení Azure Classic, musíte je migrovat do služby [Azure Resource Manager](https://docs.microsoft.com/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm) před migrací. Aby bylo možné zobrazit webovou stránku, musíte být partnerem.

1. Ověřte, že všechny služby zdrojového předplatného používají model Azure Resource Manager. Potom přeneste prostředky ze zdrojového předplatného do cílového předplatného pomocí [přesunu prostředků Azure](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Přesunutí prostředků Azure mezi předplatnými může vést k výpadkům služby na základě prostředků v předplatných.

## <a name="all-subscription-transfers-from-a-csp-partner"></a>Přenosy všech předplatných na partnera CSP

Aby bylo možné přenést jakékoli další odběry od partnera CSP do jakékoli jiné nabídky Azure, musí předplatitel přesunout prostředky mezi zdrojovými předplatnými a cílovými předplatnými CSP.

1. Vytvořte cílová předplatná Azure.
1. Ujistěte se, že zdrojová a cílová předplatná jsou ve stejném tenantovi Azure Active Directory (Azure AD). Další informace o změnách tenanta Azure AD najdete v tématu [Přiřazení nebo přidání předplatného Azure do tenanta Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

    > [!IMPORTANT]
    >  - Když přidružíte předplatné k jinému adresáři, ztratí přístup uživatelé, kteří mají přiřazené role pomocí [RBAC](../../role-based-access-control/role-assignments-portal.md). Klasičtí správci předplatných, včetně správců služeb a spolusprávců, také ztratí přístup.
    >  - Z předplatného se také odstraní přiřazení zásad, jakmile se předplatné přidruží k jinému adresáři.

1. Uživatelský účet, který použijete k provedení přenosu, musí mít u obou předplatných přístup vlastníka [RBAC](add-change-subscription-administrator.md).
1. Než začnete, [ověřte](/rest/api/resources/resources/validatemoveresources), že se všechny prostředky Azure můžou přesunout ze zdrojového předplatného do cílového předplatného.
    > [!IMPORTANT]
    >  - Některé prostředky Azure nejde přesunout mezi předplatnými. Úplný seznam prostředků Azure, které se dají přesunout, najdete v tématu [Podpora operací přesunutí pro prostředky](../../azure-resource-manager/management/move-support-resources.md).

1. Přeneste prostředky ze zdrojového předplatného do cílového předplatného pomocí [přesunu prostředků Azure](../../azure-resource-manager/management/move-resource-group-and-subscription.md).
    > [!IMPORTANT]
    >  - Přesunutí prostředků Azure mezi předplatnými může vést k výpadkům služby na základě prostředků v předplatných.

## <a name="next-steps"></a>Další kroky
- [Získejte vlastnictví fakturace předplatných Azure pro vlastní účet se smlouvou MPA](mpa-request-ownership.md).
- Přečtěte si o tom, jak [spravovat účty a předplatná pomocí fakturace Azure](index.yml).
