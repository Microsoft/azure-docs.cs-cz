---
title: Co je Azure AD Connect a Connect Health. | Dokumentace Microsoftu
description: Popisuje nástroje používané k synchronizaci a monitorování místního prostředí pomocí Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 01/08/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11c57065e21c48a23148cacd91a42737edc13538
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80049413"
---
# <a name="what-is-azure-ad-connect"></a>Co je Azure AD Connect?

Azure AD Connect je nástroj od Microsoftu, jehož účelem je splnit a zajistit cíle hybridní identity.  Má následující funkce:
     
- [Synchronizace hash hesla](whatis-phs.md) – metoda přihlášení, která synchronizuje hodnotu hash místního hesla služby AD uživatelů s Azure AD.
- [Předávací ověřování](how-to-connect-pta.md) – metoda přihlášení, která uživatelům umožňuje používat stejné heslo místně a v cloudu, ale nevyžaduje další infrastrukturu federovaného prostředí.
- [Integrace federace](how-to-connect-fed-whatis.md) – federace je volitelnou součástí Služby Azure AD Connect a lze ji použít ke konfiguraci hybridního prostředí pomocí místní infrastruktury služby AD FS. Poskytuje také možnosti správy služby AD FS, jako je obnovení certifikátu a další nasazení serveru AD FS.
- [Synchronizace](how-to-connect-sync-whatis.md) – odpovědnost za vytváření uživatelů, skupin a dalších objektů.  Stejně jako ujistěte se, že informace o identitě pro místní uživatele a skupiny se shoduje s cloudem.  Tato synchronizace zahrnuje také hodnotu hashe hesel.
-      [Monitorování stavu](whatis-hybrid-identity-health.md) – Azure AD Connect Health můžete poskytnout robustní monitorování a poskytují centrální umístění na portálu Azure pro zobrazení této aktivity. 


![Co je služba Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Co je Azure AD Connect Health?

Azure Active Directory (Azure AD) Connect Health poskytuje robustní monitorování vaší místní infrastruktury identit. Umožňuje udržovat spolehlivé připojení k službám Office 365 a Microsoft Online Services.  Této spolehlivosti je dosaženo poskytnutím možností monitorování pro klíčové součásti identity. Také umožňuje klíčové datové body o těchto komponentách snadno přístupné.

Další informace najdete v článku [Portál služby Azure AD Connect Health](https://aka.ms/aadconnecthealth). Portál Azure AD Connect Health slouží k zobrazení výstrah, monitorování výkonu, analýzy využití a dalších informací. Azure AD Connect Health umožňuje mít na jednom místě a v jediném přehledu informace o stavu klíčových komponent identity.

![Co je Azure AD Connect Health](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)

## <a name="why-use-azure-ad-connect"></a>Proč používat Azure AD Connect?
Integrace místních adresářů se službou Azure AD zvyšuje produktivitu uživatelů tím, že jim poskytuje společnou identitu pro přístup ke cloudovým i místním prostředkům. Uživatelé a organizace mohou využít:

* Uživatelé můžou používat jedinou identitu pro přístup k místním aplikacím i ke cloudovým službám, jako je například Office 365.
* Získáváte jeden nástroj, který umožňuje snadné nasazení pro synchronizaci a přihlašování.
* Přináší nejnovější schopnosti pro vaše scénáře. Azure AD Connect nahrazuje starší verze nástrojů pro integraci identit, jako je DirSync a Azure AD Sync. Další informace naleznete v [tématu Hybrid Identity directory integration tools comparison](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Proč používat službu Azure AD Connect Health?
Když s Azure AD, vaši uživatelé jsou produktivnější, protože existuje společná identita pro přístup ke cloudu i místní prostředky. Zajištění prostředí je spolehlivé, aby uživatelé měli přístup k těmto prostředkům, se stává výzvou.  Azure AD Connect Health pomáhá monitorovat a získávat přehledo v místní infrastruktuře identit a tím zajistit spolehlivost tohoto prostředí. Stačí jednoduše nainstalovat agenta na každý z vašich místních serverů identity.

Azure AD Connect Health pro službu AD FS podporuje službu AD FS 2.0 v systémech Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows Server 2016. Podporuje také monitorování proxy serveru služby AD FS a proxy serverů webových aplikací, které poskytují ověřování pro přístup z extranetu. Díky snadné a rychlé instalaci agenta služby Health poskytuje Azure AD Connect Health pro službu AD FS sadu klíčových funkcí.

Hlavní výhody a osvědčené postupy:

|Klíčové výhody|Osvědčené postupy|
|-----|-----|
|Rozšířené zabezpečení|[Trendy uzamčení extranetu](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)</br>[Sestava neúspěšných přihlášení](how-to-connect-health-adfs-risky-ip.md)</br>[V souladu s ochranou osobních údajů](reference-connect-health-user-privacy.md)|
|Upozornění na [všechny kritické problémy se systémem ADFS](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)|Konfigurace serveru a dostupnost</br>[Výkon a možnosti připojení](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs)</br>Pravidelná údržba|
|Snadné nasazení a správa|[Rychlá instalace agenta](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)</br>Automatický upgrade agenta na nejnovější verzi</br>Dostupnost dat na portálu během několika minut|
Detailní [metriky využití](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)|Využití nejčastějších aplikací</br>Umístění v síti a připojení TCP</br>Žádosti o tokeny pro každý server|
|Skvělé uživatelské prostředí|Styl řídicího panelu z webu Azure Portal</br>[Upozornění prostřednictvím e-mailů](how-to-connect-health-adfs.md#alerts-for-ad-fs)|


## <a name="license-requirements-for-using-azure-ad-connect"></a>Licenční požadavky pro používání služby Azure AD Connect

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="license-requirements-for-using-azure-ad-connect-health"></a>Licenční požadavky pro používání azure ad připojení stavu
[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Další kroky

- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Expresní nastavení](how-to-connect-install-express.md)
- [Vlastní nastavení](how-to-connect-install-custom.md)
- [Instalace agentů Azure AD Connect Health](how-to-connect-health-agent-install.md) 
