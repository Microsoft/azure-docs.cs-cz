---
title: Běžné scénáře správy nároků ve službě Azure AD (Preview) – Azure Active Directory
description: Seznamte se se základními kroky, které byste měli dodržovat v části běžné scénáře v Azure Active Directory Správa nároků (Preview).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81382ebecdff6c7b146386b3ae2b0768a7c834bf
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389069"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Běžné scénáře správy nároků ve službě Azure AD (Preview)

> [!IMPORTANT]
> Správa opravňujících k Azure Active Directory (Azure AD) je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Existuje několik způsobů, jak můžete nakonfigurovat správu nároků pro vaši organizaci. Pokud však teprve začínáte, je vhodné porozumět běžným scénářům pro správce, schvalovatele a žadatele.

## <a name="administrators"></a>Správci

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>Jsem novým řízením nároků a chci pomáhat s zahájením práce

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | [Postup vytvoření prvního balíčku pro přístup podle kurzu](entitlement-management-access-package-first.md) | [@no__t – ikona portálu 1Azure](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Chci uživatelům v mém adresáři dovolit požádat o přístup ke skupinám, aplikacím nebo webům SharePointu

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | **1.** [Vytvoření nového přístupového balíčku v katalogu](entitlement-management-access-package-create.md#start-new-access-package) | ![Vytvoření balíčku pro přístup](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [přidejte role prostředků pro přístup k balíčku](entitlement-management-access-package-resources.md#add-resource-roles)<ul><li>Skupiny</li><li>Aplikace</li><li>Weby SharePoint</li></ul> | ![Přidat role prostředků](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [přidejte zásadu](entitlement-management-access-package-request-policy.md#for-users-in-your-directory) .<ul><li>Pro uživatele ve vašem adresáři</li><li>Vyžadovat schválení</li><li>Nastavení životního cyklu</li></ul> | ![Přidání zásad](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Chci uživatelům v adresáři Moje obchodní partneři (včetně uživatelů, kteří ještě nejsou v adresáři adresáře) dovolit přístup k přístupu ke skupinám, aplikacím nebo webům SharePointu.

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | **1.** [Vytvoření nového přístupového balíčku v katalogu](entitlement-management-access-package-create.md#start-new-access-package) | ![Vytvoření balíčku pro přístup](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [přidejte role prostředků pro přístup k balíčku](entitlement-management-access-package-resources.md#add-resource-roles) | ![Přidat role prostředků](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [přidejte zásadu pro externí uživatele](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory) .<ul><li>Pro uživatele, kteří nejsou ve vašem adresáři</li><li>Vyžadovat schválení</li><li>Nastavení životního cyklu</li></ul> | ![Přidat zásady pro externí uživatele](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [odeslání odkazu pro vyžádání balíčku pro přístup vašemu obchodnímu partnerovi](entitlement-management-access-package-settings.md)<ul><li>Obchodní partner může sdílet propojení se svými uživateli.</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>Chci změnit skupiny, aplikace nebo weby SharePointu v balíčku pro přístup

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | **1.** otevřete balíček pro přístup. | ![Přidat role prostředků](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Přidání nebo odebrání rolí prostředků](entitlement-management-access-package-resources.md#add-resource-roles) | ![Přidat role prostředků](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>Chci zobrazit, kdo má přiřazení ke skupinám, aplikacím nebo webům SharePointu

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | **1.** otevřete balíček pro přístup. | ![Přidat role prostředků](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [zobrazení přiřazení](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)<ul><li>Zobrazit, kteří uživatelé mají přístup k balíčku přístupu</li><li>Zobrazit platnost přístupu uživatele</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>Chci zobrazit skupiny, aplikace nebo weby SharePointu, ke kterým má uživatel přístup

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | [Zobrazit sestavu přiřazení uživatelů](entitlement-management-reports.md)<ul><li>Zobrazit, kdy požadoval a kdo schválil</li></ul> |  |

## <a name="approvers"></a>Schvalovatelé

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>Chci schvalovat žádosti o přístup ke skupinám, aplikacím nebo webům SharePointu

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | **1.** [otevření žádosti na portálu pro přístup](entitlement-management-request-approve.md#open-request) | [@no__t – ikona portálu pro přístup k 1My](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [schvalte žádost o přístup](entitlement-management-request-approve.md#approve-or-deny-request) . | ![Schválit přístup](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>Žadatelé

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>Chci zobrazit skupiny, aplikace nebo weby služby SharePoint, které jsou pro mě dostupné, a požádat o přístup

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | **1.** [Přihlaste se na portál My Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [@no__t – ikona portálu pro přístup k 1My](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** vyhledání balíčku pro přístup |  |
> | **3.** [požádat o přístup](entitlement-management-request-access.md#request-an-access-package) | ![Vyžádání přístup](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>Jsem externí uživatel a chci požádat o přístup ke skupinám, aplikacím nebo webům SharePointu s přímým odkazem

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | **1.** [Najděte přijatý odkaz na balíček přístupu](entitlement-management-access-package-settings.md) . |  |
> | **2.** [Přihlaste se na portál My Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [@no__t – ikona portálu pro přístup k 1My](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [požádat o přístup](entitlement-management-request-access.md#request-an-access-package) | ![Požádat o přístup k externímu uživateli](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>Chci zobrazit skupiny, aplikace nebo weby SharePointu, ke kterým už mám přístup

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | **1.** [Přihlaste se na portál My Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [@no__t – ikona portálu pro přístup k 1My](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** zobrazení balíčků aktivních přístupů |  |

## <a name="next-steps"></a>Další kroky

- [Kurz: vytvoření prvního balíčku pro přístup](entitlement-management-access-package-first.md)
- [Delegování a role](entitlement-management-delegate.md)
