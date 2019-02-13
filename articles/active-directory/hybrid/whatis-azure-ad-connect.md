---
title: Co je Azure AD Connect a Connect Health. | Dokumenty Microsoft
description: Popisuje nástroje pro synchronizaci a monitorovat místní prostředí s využitím Azure AD.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/30/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d364421794452f8ccf95a60ec86a161934c87528
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167979"
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

Azure AD Connect Health pro službu AD FS podporuje službu AD FS 2.0 v systémech Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows Server 2016. Podporuje také monitorování proxy serveru služby AD FS a proxy serverů webových aplikací, které poskytují ověřování pro přístup z extranetu. Díky snadné a rychlé instalaci agenta služby Health poskytuje Azure AD Connect Health pro službu AD FS sadu klíčových funkcí.

Mezi klíčové výhody a osvědčené postupy:

|Klíčové výhody|Osvědčené postupy|
|-----|-----|
|Vylepšené zabezpečení|[Trendy uzamčení extranetu](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)</br>[Sestava neúspěšných přihlášení](how-to-connect-health-adfs.md#risky-ip-report-public-preview)</br>[V kompatibilní ochrany osobních údajů](reference-connect-health-user-privacy.md)|
|Nechte se upozornit na [všechny kritické problémy systému služby AD FS](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)|Konfigurace serveru a dostupnost</br>[Výkon a možnosti připojení](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs)</br>Pravidelná údržba|
|Snadné nasazení a správa|[Instalace agenta rychlé](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)</br>Automatický upgrade agenta na nejnovější verzi</br>Dostupnost dat na portálu během několika minut|
Bohaté [metriky využití](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)|Využití nejčastějších aplikací</br>Umístění v síti a připojení TCP</br>Žádosti o tokeny pro každý server|
|Skvělé uživatelské prostředí|Styl řídicího panelu z webu Azure Portal</br>[Upozornění prostřednictvím e-mailů](how-to-connect-health-adfs.md#alerts-for-ad-fs)|




## <a name="next-steps"></a>Další postup

- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Expresní nastavení](how-to-connect-install-express.md)
- [Vlastní nastavení](how-to-connect-install-custom.md)
- [Instalace agentů Azure AD Connect Health](how-to-connect-health-agent-install.md) 
