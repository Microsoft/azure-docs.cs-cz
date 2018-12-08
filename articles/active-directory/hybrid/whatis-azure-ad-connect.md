---
title: Co je Azure AD Connect a Connect Health. | Dokumenty Microsoft
description: Popisuje nástroje pro synchronizaci a monitorovat místní prostředí s využitím Azure AD.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: get-started-article
ms.date: 11/28/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2fbd6edb02dfc4080d7692e43324a5b3981091f9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109449"
---
# <a name="what-is-azure-ad-connect"></a>Co je Azure AD Connect?

Azure AD Connect je nástroj od Microsoftu, jehož účelem je splnit a zajistit cíle hybridní identity.  Má následující funkce:
    
- [Synchronizace hodnot hash hesel](whatis-phs.md) – metoda přihlašování, který synchronizuje hodnoty hash uživatele místní heslo AD službou Azure AD.
- [Předávací ověřování](how-to-connect-pta.md) – metoda přihlašování, která umožňuje uživatelům používat stejné heslo místní i v cloudu, ale nevyžaduje další infrastrukturu federovaném prostředí.
- [Integrace federace](how-to-connect-fed-whatis.md) -federace je volitelná součást Azure AD Connect a slouží ke konfiguraci hybridního prostředí, pomocí místní infrastruktury služby AD FS. Také poskytuje možnosti správy služby AD FS, jako je obnovení certifikátu a další nasazení serveru služby AD FS.
- [Synchronizace](how-to-connect-sync-whatis.md) – zodpovědný za vytváření uživatelů, skupin a dalších objektů.  Stejně jako tak že informace o identitě pro místních uživatelů a skupin shodovaly s cloudem.  Tato synchronizace obsahuje také hodnot hash hesel.
-   [Monitorování stavu](whatis-hybrid-identity-health.md) – Azure AD Connect Health poskytuje robustní monitorování a centrální umístění na webu Azure Portal, kde je možné tuto aktivitu zobrazit. 


![Co je služba Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Co je Azure AD Connect Health?

Azure Active Directory (Azure AD) Connect Health poskytuje robustní monitorování vaší místní infrastruktury identit. Umožňuje udržovat spolehlivé propojení s Office 365 a Microsoft Online Services.  Tato spolehlivosti se dosahuje zajišťuje monitorování klíčových komponent identity. Navíc je klíčových datových bodů o tyto součásti snadno k dispozici.

Další informace najdete v článku [Portál služby Azure AD Connect Health](https://aka.ms/aadconnecthealth). Pomocí portálu Azure AD Connect Health pro zobrazení výstrah, sledování výkonu, analýza využití a dalších informací. Azure AD Connect Health umožňuje mít na jednom místě a v jediném přehledu informace o stavu klíčových komponent identity.

![Co je Azure AD Connect Health](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>Proč používat Azure AD Connect?
Integrace místních adresářů se službou Azure AD zvyšuje produktivitu uživatelů tím, že jim poskytuje společnou identitu pro přístup ke cloudovým i místním prostředkům. Uživatelé a organizace můžou využívat výhod:

* Uživatelé můžou používat jedinou identitu pro přístup k místním aplikacím i ke cloudovým službám, jako je například Office 365.
* Získáváte jeden nástroj, který umožňuje snadné nasazení pro synchronizaci a přihlašování.
* Přináší nejnovější schopnosti pro vaše scénáře. Azure AD Connect nahrazuje starší verze nástrojů pro integraci identity, jako jsou například DirSync nebo Azure AD Sync. Další informace najdete v článku o [orovnání nástrojů pro integraci adresáře hybridní identity](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Proč používat službu Azure AD Connect Health?
Při práci s Azure AD, jsou vaši uživatelé zvýšit produktivitu práce protože společnou identitu pro přístup ke cloudovým i místním prostředkům. Zajištění, že prostředí je spolehlivé, aby uživatelé měli přístup ke tyto prostředky se změní na výzvu.  Azure AD Connect Health pomáhá monitorovat a získávat přehledy o vaší místní infrastruktury identit zajistila spolehlivost tohoto prostředí. Stačí jednoduše nainstalovat agenta na každý z vašich místních serverů identity.


## <a name="next-steps"></a>Další postup

- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Expresní nastavení](how-to-connect-install-express.md)
- [Vlastní nastavení](how-to-connect-install-custom.md)
- [Instalace agentů Azure AD Connect Health](how-to-connect-health-agent-install.md) 
