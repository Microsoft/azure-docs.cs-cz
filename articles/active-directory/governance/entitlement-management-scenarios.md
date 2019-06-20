---
title: Běžné scénáře v Azure AD oprávnění správy (Preview) – Azure Active Directory
description: Přečtěte si postup vysoké úrovně, které byste měli postupovat pro běžné scénáře v Azure Active Directory oprávnění správy (Preview).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 04/23/2019
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27d54d51a16f4ed94f24f9afa005c63b22778cf9
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67190352"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management-preview"></a>Běžné scénáře ve správě služby Azure AD oprávnění (Preview)

> [!IMPORTANT]
> Správa nároků Azure Active Directory (Azure AD) je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Existuje několik způsobů, abyste mohli konfigurovat správu oprávnění ve vaší organizaci. Nicméně pokud právě začínáte, je vhodné se seznámit s běžnými scénáři pro správce, schvalovatele a žadatele.

## <a name="administrators"></a>Správci

### <a name="im-new-to-entitlement-management-and-i-want-help-with-getting-started"></a>Jmenuji se nový pro správu oprávnění a chci nápovědu k zahájení práce

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | [Postupujte podle kurzu a vytvořit svůj první přístup balíček](entitlement-management-access-package-first.md) | [![Azure portal ikonu](./media/entitlement-management-scenarios/azure-portal.png)](./media/entitlement-management-scenarios/azure-portal-expanded.png#lightbox) |

### <a name="i-want-to-allow-users-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Chci umožnit uživatelům ve svém adresáři můžete požádat o přístup ke skupinám, aplikace nebo weby služby SharePoint

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | **1.** [Vytvořit nový balíček pro přístup do katalogu](entitlement-management-access-package-create.md#start-new-access-package) | ![Vytvořte si balíček pro přístup](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Přidání role prostředků pro přístup k balíčku](entitlement-management-access-package-edit.md#add-resource-roles)<ul><li>Skupiny</li><li>Aplikace</li><li>Weby SharePoint</li></ul> | ![Přidání role prostředků](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Přidat zásadu](entitlement-management-access-package-edit.md#policy-for-users-in-your-directory)<ul><li>Pro uživatele ve vašem adresáři</li><li>Vyžadovat schválení</li><li>Nastavení vypršení platnosti</li></ul> | ![Přidání zásad](./media/entitlement-management-scenarios/policy.png) |

### <a name="i-want-to-allow-users-from-my-business-partners-directory-including-users-not-yet-in-my-directory-to-request-access-to-groups-applications-or-sharepoint-sites"></a>Chcete uživatelům umožnit z adresáře obchodních partnerů (včetně uživatelů ještě není v mém adresáři) žádost o přístup ke skupinám, aplikace nebo weby služby SharePoint

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | **1.** [Vytvořit nový balíček pro přístup do katalogu](entitlement-management-access-package-create.md#start-new-access-package) | ![Vytvořte si balíček pro přístup](./media/entitlement-management-scenarios/access-package.png) |
> | **2.** [Přidání role prostředků pro přístup k balíčku](entitlement-management-access-package-edit.md#add-resource-roles) | ![Přidání role prostředků](./media/entitlement-management-scenarios/resource-roles.png) |
> | **3.** [Přidání zásad pro externí uživatele](entitlement-management-access-package-edit.md#policy-for-users-not-in-your-directory)<ul><li>Pro uživatele není ve vašem adresáři</li><li>Vyžadovat schválení</li><li>Nastavení vypršení platnosti</li></ul> | ![Přidání zásad pro externí uživatele](./media/entitlement-management-scenarios/policy-external.png) |
> | **4.** [Poslat odkaz portálu Můj přístup k vyžádání balíček přístup k vašemu obchodnímu partnerovi](entitlement-management-access-package-edit.md#copy-my-access-portal-link)<ul><li>Obchodní partner můžete odkaz sdílejte se svým uživatelům</li></ul> |  |

### <a name="i-want-to-change-the-groups-applications-or-sharepoint-sites-in-an-access-package"></a>Chci změnit skupiny, aplikace nebo weby služby SharePoint, přístup k balíčku

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | **1.** Otevřete balíček přístup | ![Přidání role prostředků](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Přidání nebo odebrání role prostředků](entitlement-management-access-package-edit.md#add-resource-roles) | ![Přidání role prostředků](./media/entitlement-management-scenarios/resource-roles-add.png) |

### <a name="i-want-to-view-who-has-an-assignment-to-groups-applications-or-sharepoint-sites"></a>Chci zobrazit, kdo má přiřazení ke skupinám, aplikace nebo weby služby SharePoint

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | **1.** Otevřít balíček přístup | ![Přidání role prostředků](./media/entitlement-management-scenarios/resource-roles.png) |
> | **2.** [Zobrazit přiřazení](entitlement-management-access-package-edit.md#view-who-has-an-assignment)<ul><li>Zobrazení, kteří mají přístup k balíčku přístup</li><li>Zobrazit uživatele, jejichž přístupu vypršela</li></ul> |  |

### <a name="i-want-to-view-groups-applications-or-sharepoint-sites-a-user-has-access-to"></a>Chci zobrazit skupiny, aplikace nebo weby služby SharePoint, které mají uživatelé přístup k

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | [Zobrazit sestavu o přiřazení uživatele](entitlement-management-reports.md)<ul><li>Zobrazit, když, o který žádali a kdo schválení</li></ul> |  |

## <a name="approvers"></a>Schvalovatele

### <a name="i-want-to-approve-requests-to-access-groups-applications-or-sharepoint-sites"></a>Chci, aby ke schválení žádosti o přístup ke skupinám, aplikace nebo weby služby SharePoint

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | **1.** [Otevřít požadavek na portálu Moje aplikace Access](entitlement-management-request-approve.md#open-request) | [![Ikona Tento přístup k portálu](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** [Schválit žádost o přístup](entitlement-management-request-approve.md#approve-or-deny-request) | ![Schválit přístup](./media/entitlement-management-scenarios/approve-access.png) |

## <a name="requestors"></a>Žadatele

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-available-to-me-and-request-access"></a>Chci zobrazit skupiny, aplikace nebo weby služby SharePoint v Microsoft Azure a žádat o přístup

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | **1.** [Přihlaste se k portálu Moje aplikace Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Ikona Tento přístup k portálu](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Nenašel se balíček přístup |  |
> | **3.** [Žádost o přístup](entitlement-management-request-access.md#request-an-access-package) | ![Vyžádat si přístup](./media/entitlement-management-scenarios/request-access.png) |

### <a name="im-an-external-user-and-i-want-to-request-access-to-groups-applications-or-sharepoint-sites-with-a-direct-link"></a>Já jsem externího uživatele a budu chtít požádat o přístup ke skupinám, aplikace nebo weby služby SharePoint s přímým odkazem

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | **1.** [Najít na portálu odkaz Mé přístup, který jste dostali](entitlement-management-access-package-edit.md#copy-my-access-portal-link) |  |
> | **2.** [Přihlaste se k portálu Moje aplikace Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Ikona Tento přístup k portálu](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **3.** [Žádost o přístup](entitlement-management-request-access.md#request-an-access-package) | ![Žádost o přístup k datům externí uživatele](./media/entitlement-management-scenarios/request-access-external.png) |

### <a name="i-want-to-view-the-groups-applications-or-sharepoint-sites-i-already-have-access-to"></a>Chci zobrazit skupiny, aplikace a weby služby SharePoint, které už mají přístup k

> [!div class="mx-tableFixed"]
> | Kroky | Příklad: |
> | --- | --- |
> | **1.** [Přihlaste se k portálu Moje aplikace Access](entitlement-management-request-access.md#sign-in-to-the-my-access-portal) | [![Ikona Tento přístup k portálu](./media/entitlement-management-scenarios/my-access-portal.png)](./media/entitlement-management-scenarios/my-access-portal-expanded.png#lightbox) |
> | **2.** Zobrazit aktivní přístup balíčky |  |

## <a name="next-steps"></a>Další postup

- [Kurz: Vytvoření vaší první přístup k balíčku](entitlement-management-access-package-first.md)
- [Delegovat úkoly](entitlement-management-delegate.md)
