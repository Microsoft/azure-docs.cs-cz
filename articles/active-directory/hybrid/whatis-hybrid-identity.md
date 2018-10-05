---
title: Připojení Active Directory s Azure Active Directory | Microsoft Docs
description: Azure AD Connect integruje vaše místní adresáře do služby Azure Active Directory. To umožní poskytovat společnou identitu pro aplikace Office 365, Azure a SaaS integrované s Azure AD.
keywords: Úvod k Azure AD Connect, přehled Azure AD Connect, co je Azure AD Connect, instalace active directory
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/18/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 40ac3ca92c65607df056b883608dde60d816143e
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47181769"
---
# <a name="hybrid-identity-and-microsofts-identity-solutions"></a>Hybridní identita a řešení pro správu identit od Microsoftu
Firmy a korporace v současné době stále více používají kombinaci místních a cloudových aplikací.  Když máte aplikace, které provozujete místně a v cloudu, a uživatele, kteří k těmto aplikacím potřebují přístup, začíná to být náročné.

Řešení pro správu identit od Microsoftu pokrývá místní i cloudové funkce a vytvářejí jedinou identitu uživatele pro ověřování a autorizaci u všech prostředků bez ohledu na umístění. Tomu se říká hybridní identita.

## <a name="what-is-azure-ad-connect"></a>Co je Azure AD Connect?

Azure AD Connect je nástroj od Microsoftu, jehož účelem je splnit a zajistit cíle hybridní identity.  To umožní poskytovat společnou identitu pro uživatele pro aplikace Office 365, Azure a SaaS integrované s Azure AD.  Má následující funkce:
    
- [Synchronizace](how-to-connect-sync-whatis.md) – tato komponenta odpovídá za vytváření uživatelů, skupin a dalších objektů. Také zajišťuje, aby se informace o identitě místních uživatelů a skupin shodovaly s cloudem.  Odpovídá za synchronizaci hodnot hash hesel se službou Azure AD.
-   [AD FS a integrace federace](how-to-connect-fed-whatis.md) – federace je volitelná součást Azure AD Connect, která se dá použít ke konfiguraci hybridního prostředí pomocí místní infrastruktury služby AD FS. Poskytuje také možnosti správy služby AD FS, jako je obnovení certifikátů a další nasazení serverů služby AD FS.
-   [Předávací ověřování](how-to-connect-pta.md) – další volitelná komponenta, která umožňuje uživatelům používat stejné heslo místně i v cloudu, ale nevyžaduje dodatečnou infrastrukturu federovaného prostředí.
-   [Monitorování stavu](whatis-hybrid-identity-health.md) – Azure AD Connect Health poskytuje robustní monitorování a centrální umístění na webu Azure Portal, kde je možné tuto aktivitu zobrazit. 


![Co je služba Azure AD Connect](./media/whatis-hybrid-identity/arch.png)



## <a name="what-is-azure-ad-connect-health"></a>Co je Azure AD Connect Health?

Služba Azure Active Directory (Azure AD) Connect Health pomáhá monitorovat místní infrastrukturu identity a synchronizační služby a lépe proniknout do jejich funkce. Umožňuje udržovat spolehlivé propojení s Office 365 a službami Microsoft Online Services tím, že zajišťuje monitorování klíčových komponent identity, jako jsou servery služby Active Directory Federation Services (AD FS), servery služby Azure AD Connect (neboli synchronizační stroj), řadiče domény služby Active Directory atd. Také udržuje klíčové datové body těchto komponent dobře přístupné, takže můžete snadno získat přehled o jejich používání a dalších důležitých statistikách, abyste se mohli kvalifikovaně rozhodnout.

Další informace najdete v článku [Portál služby Azure AD Connect Health](https://aka.ms/aadconnecthealth). Na portálu služby Azure AD Connect Health můžete zobrazovat upozornění, monitorování výkonu, analýzy využívání a další informace. Azure AD Connect Health umožňuje mít na jednom místě a v jediném přehledu informace o stavu klíčových komponent identity.

![Co je Azure AD Connect Health](./media/whatis-hybrid-identity-health/aadconnecthealth2.png)


Se zvyšujícím se počtem funkcí ve službě Azure AD Connect Health poskytuje portál jediný řídicí panel přehledu identity. Získáte ještě rozsáhlejší, kvalitnější a propojenější prostředí, které vaši uživatelé budou moci využít ke zvýšení efektivity své práce.


## <a name="why-use-azure-ad-connect"></a>Proč používat Azure AD Connect?
Integrace místních adresářů se službou Azure AD zvyšuje produktivitu uživatelů tím, že jim poskytuje společnou identitu pro přístup ke cloudovým i místním prostředkům. Uživatelé a organizace můžou využívat následujících výhod:

* Uživatelé můžou používat jedinou identitu pro přístup k místním aplikacím i ke cloudovým službám, jako je například Office 365.
* Získáváte jeden nástroj, který umožňuje snadné nasazení pro synchronizaci a přihlašování.
* Přináší nejnovější schopnosti pro vaše scénáře. Azure AD Connect nahrazuje starší verze nástrojů pro integraci identity, jako jsou například DirSync nebo Azure AD Sync. Další informace najdete v článku o [orovnání nástrojů pro integraci adresáře hybridní identity](plan-hybrid-identity-design-considerations-tools-comparison.md).

## <a name="why-use-azure-ad-connect-health"></a>Proč používat službu Azure AD Connect Health?
Když integrujete místní adresáře se službou Azure AD, zvýší se produktivita vašich uživatelů skrze společnou identitu pro přístup ke cloudovým i místním prostředkům. Spolu s touto integrací ale přicházejí i další výzvy. Je třeba zajistit, aby toto prostředí bylo v pořádku a uživatelé měli spolehlivý přístup k místním i cloudovým prostředkům z jakéhokoli zařízení. Azure AD Connect Health pomáhá s monitorováním a získáváním přehledu o místní infrastruktuře identity, která se používá pro přístup k Office 365 nebo k jiným aplikacím služby Azure AD. Stačí jednoduše nainstalovat agenta na každý z vašich místních serverů identity.

### <a name="azure-ad-connect-health-for-ad-fshow-to-connect-health-adfsmd"></a>[Azure AD Connect Health pro službu AD FS](how-to-connect-health-adfs.md)
Azure AD Connect Health pro službu AD FS podporuje službu AD FS 2.0 v systémech Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows Server 2016. Podporuje také monitorování proxy serveru služby AD FS a proxy serverů webových aplikací, které poskytují ověřování pro přístup z extranetu. Díky snadné a rychlé instalaci agenta služby Health poskytuje Azure AD Connect Health pro službu AD FS sadu klíčových funkcí.

#### <a name="key-benefits-and-best-practices"></a>Klíčové výhody a osvědčené postupy

- *Rozšířené zabezpečení*
  - [Trendy uzamčení extranetu](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)
  - [Sestava neúspěšných přihlášení](how-to-connect-health-adfs.md#risky-ip-report-public-preview) 
  - [Vyhovění ochraně osobních údajů](reference-connect-health-user-privacy.md)    
- *Upozornění na všechny [kritické systémové problémy služby AD FS](how-to-connect-health-alert-catalog.md#alerts-for-active-directory-federation-services)*
    - Konfigurace serveru a dostupnost 
    - [Výkon a možnosti připojení](how-to-connect-health-adfs.md#performance-monitoring-for-ad-fs) 
  - Pravidelná údržba    
- *Snadné nasazení a správa*
  - Rychlá [instalace agenta](how-to-connect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs) 
  - Automatický upgrade agenta na nejnovější verzi 
  - Dostupnost dat na portálu během několika minut    
- *Detailní [metriky využití](how-to-connect-health-adfs.md#usage-analytics-for-ad-fs)* 
  - Využití nejčastějších aplikací
  - Umístění v síti a připojení TCP
  - Žádosti o tokeny pro každý server    
- *Skvělé uživatelské prostředí* 
  - Styl řídicího panelu z webu Azure Portal
  - [Upozornění prostřednictvím e-mailů](how-to-connect-health-adfs.md#alerts-for-ad-fs)    

#### <a name="feature-highlight"></a>Nejvýznamnější funkce

*   Monitorování s upozorněními, která oznamují, pokud služba AD FS nebo proxy servery služby AD FS nejsou v pořádku
*   E-mailová oznámení pro kritické výstrahy
*   Trendy v datech o výkonu, které se hodí pro plánování kapacity služby AD FS
*   Analýza využití pro přihlášení ke službě AD FS s pivoty (aplikace, uživatelé, umístění v síti atd.)
*   Sestavy pro službu AD FS, například 50 uživatelů s největším počtem chybně zadaných kombinací uživatelského jména a hesla z poslední IP adresy
*   Sestava rizikových IP adres pro neúspěšná přihlášení ke službě AD FS

Tady najdete další informace o [Používání služby Azure AD Connect Health se službou AD FS](how-to-connect-health-adfs.md).

### <a name="azure-ad-connect-health-for-synchow-to-connect-health-syncmd"></a>[Azure AD Connect Health pro synchronizaci](how-to-connect-health-sync.md)
Služba Azure AD Connect Health pro synchronizaci monitoruje a poskytuje informace o synchronizacích, ke kterým dochází mezi místní službou Active Directory a službou Azure AD. Služba Azure AD Connect Health pro synchronizaci poskytuje následující sadu klíčových funkcí:

* Monitorování s upozorněními v případě, že server Azure AD Connect neboli synchronizační stroj není v pořádku
* E-mailová oznámení pro kritické výstrahy
* Synchronizace statistik provozu včetně přehledů latencí pro operace synchronizace a trendy v různých operacích, jako jsou přidání, aktualizace nebo odstranění
* Rychlý přehled informací o vlastnostech synchronizace a posledním úspěšném exportu do služby Azure AD
* Sestavy chyb synchronizace na úrovni objektu \(nevyžadují službu Azure AD Premium\)

Tady najdete další informace o [Používání služby Azure AD Connect Health k synchronizaci](how-to-connect-health-sync.md).

### <a name="azure-ad-connect-health-for-ad-dshow-to-connect-health-addsmd"></a>[Azure AD Connect Health pro službu AD DS](how-to-connect-health-adds.md)
Azure AD Connect Health pro službu Active Directory Domain Services (AD DS) podporuje monitorování řadičů domény nainstalovaných v systémech Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows Server 2016. Instalace agenta služby Health umožňuje monitorování místního prostředí AD DS z cloudu. Služba Azure AD Connect Health pro AD DS poskytuje následující sadu klíčových funkcí:

* Monitorování upozornění, aby se zjistilo, kdy řadiče domény nejsou v pořádku, a e-mailová oznámeními pro kritická upozornění
* Řídicí panel Řadiče domény, který poskytuje rychlý přehled kondice a provozního stavu řadičů domény
* Řídicí panel Stav replikace s nejnovějšími informacemi o replikaci a s odkazy na průvodce odstraňováním potíží při zjištění chyb
* Rychlý přístup odkudkoli ke grafům s daty o výkonu oblíbených čítačů výkonu, které jsou nezbytné pro účely monitorování a řešení potíží

Tady najdete další informace o [Používání služby Azure AD Connect Health se službou AD DS](how-to-connect-health-adds.md).

## <a name="next-steps"></a>Další kroky


- [Hardware a předpoklady](how-to-connect-install-prerequisites.md) 
- [Expresní nastavení](how-to-connect-install-express.md)
- [Vlastní nastavení](how-to-connect-install-custom.md)
- [Synchronizace hodnot hash hesel](how-to-connect-password-hash-synchronization.md)|
- [Předávací ověřování](how-to-connect-pta.md)
- [Azure AD Connect a federace](how-to-connect-fed-whatis.md)
- [Instalace agentů Azure AD Connect Health](how-to-connect-health-agent-install.md) 
- [Synchronizace služby Azure AD Connect](how-to-connect-sync-whatis.md)
- [Historie verzí](reference-connect-version-history.md)
- [Porovnání nástrojů pro integraci adresářů](plan-hybrid-identity-design-considerations-tools-comparison.md)
- [Azure AD Connect – nejčastější dotazy](reference-connect-faq.md)









