---
title: Monitorujte místní infrastrukturu identity v cloudu.
description: Toto je stránka o službě Azure AD Connect Health. Najdete tu popis této služby a důvody, proč ji používat.
services: active-directory
documentationcenter: ''
author: zhiweiw
manager: mtillman
editor: curtand
ms.assetid: 82798ea6-5cd3-4f30-93ae-d56536f8d8e3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 006914b279f2d762bf0dee6f83d6866235c062bc
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887500"
---
# <a name="monitor-your-on-premises-identity-infrastructure-and-synchronization-services-in-the-cloud"></a>Monitorujte místní infrastrukturu identity a synchronizačních služeb v cloudu.
Služba Azure Active Directory (Azure AD) Connect Health pomáhá monitorovat místní infrastrukturu identity a synchronizační služby a lépe proniknout do jejich funkce. Umožňuje udržovat spolehlivé propojení s Office 365 a službami Microsoft Online Services tím, že zajišťuje monitorování klíčových komponent identity, jako jsou servery služby Active Directory Federation Services (AD FS), servery služby Azure AD Connect (neboli synchronizační stroj), řadiče domény služby Active Directory atd. Také udržuje klíčové datové body těchto komponent dobře přístupné, takže můžete snadno získat přehled o jejich používání a dalších důležitých statistikách, abyste se mohli kvalifikovaně rozhodnout.

Další informace najdete v článku [Portál služby Azure AD Connect Health](https://aka.ms/aadconnecthealth). Na portálu služby Azure AD Connect Health můžete zobrazovat upozornění, monitorování výkonu, analýzy využívání a další informace. Azure AD Connect Health umožňuje mít na jednom místě a v jediném přehledu informace o stavu klíčových komponent identity.

![Co je Azure AD Connect Health](./media/active-directory-aadconnect-health/aadconnecthealth2.png)


Se zvyšujícím se počtem funkcí ve službě Azure AD Connect Health poskytuje portál jediný řídicí panel přehledu identity. Získáte ještě rozsáhlejší, kvalitnější a propojenější prostředí, které vaši uživatelé budou moci využít ke zvýšení efektivity své práce.
## <a name="why-use-azure-ad-connect-health"></a>Proč používat službu Azure AD Connect Health?
Když integrujete místní adresáře se službou Azure AD, zvýší se produktivita vašich uživatelů skrze společnou identitu pro přístup ke cloudovým i místním prostředkům. Spolu s touto integrací ale přicházejí i další výzvy. Je třeba zajistit, aby toto prostředí bylo v pořádku a uživatelé měli spolehlivý přístup k místním i cloudovým prostředkům z jakéhokoli zařízení. Azure AD Connect Health pomáhá s monitorováním a získáváním přehledu o místní infrastruktuře identity, která se používá pro přístup k Office 365 nebo k jiným aplikacím služby Azure AD. Stačí jednoduše nainstalovat agenta na každý z vašich místních serverů identity.

## <a name="azure-ad-connect-health-for-ad-fsactive-directory-aadconnect-health-adfsmd"></a>[Azure AD Connect Health pro službu AD FS](active-directory-aadconnect-health-adfs.md)
Azure AD Connect Health pro službu AD FS podporuje službu AD FS 2.0 v systémech Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows Server 2016. Podporuje také monitorování proxy serveru služby AD FS a proxy serverů webových aplikací, které poskytují ověřování pro přístup z extranetu. Díky snadné a ekonomické instalaci agenta služby Health poskytuje Azure AD Connect Health pro službu AD FS následující sadu klíčových funkcí:

* Monitorování s upozorněními, která oznamují, pokud služba AD FS nebo proxy servery služby AD FS nejsou v pořádku
* E-mailová oznámení pro kritické výstrahy
* Trendy v datech o výkonu, které se hodí pro plánování kapacity služby AD FS
* Analytické funkce pro analýzy využití, které monitorují různá přihlášení do služby AD FS (aplikace, uživatelé, umístění v síti atd.), umožňující snadno pochopit, jak se služba AD FS využívá
* Sestavy pro službu AD FS, například 50 uživatelů s největším počtem chybně zadaných kombinací uživatelského jména a hesla z poslední IP adresy
* Sestava rizikových IP adres pro neúspěšná přihlášení ke službě AD FS
  
Tady najdete další informace o [Používání služby Azure AD Connect Health se službou AD FS](active-directory-aadconnect-health-adfs.md).

Následující video poskytuje přehled služby Azure AD Connect Health pro službu AD FS.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health--Monitor-you-identity-bridge/player]
>
>

## <a name="azure-ad-connect-health-for-syncactive-directory-aadconnect-health-syncmd"></a>[Azure AD Connect Health pro synchronizaci](active-directory-aadconnect-health-sync.md)
Služba Azure AD Connect Health pro synchronizaci monitoruje a poskytuje informace o synchronizacích, ke kterým dochází mezi místní službou Active Directory a službou Azure AD. Služba Azure AD Connect Health pro synchronizaci poskytuje následující sadu klíčových funkcí:

* Monitorování s upozorněními v případě, že server Azure AD Connect neboli synchronizační stroj není v pořádku
* E-mailová oznámení pro kritické výstrahy
* Synchronizace statistik provozu včetně přehledů latencí pro operace synchronizace a trendy v různých operacích, jako jsou přidání, aktualizace nebo odstranění
* Rychlý přehled informací o vlastnostech synchronizace a posledním úspěšném exportu do služby Azure AD
* Sestavy chyb synchronizace na úrovni objektu \(nevyžadují službu Azure AD Premium\)

Tady najdete další informace o [Používání služby Azure AD Connect Health k synchronizaci](active-directory-aadconnect-health-sync.md).

Následující video poskytuje přehled služby Azure AD Connect Health pro synchronizaci.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-Active-Directory-Connect-Health-Monitoring-the-sync-engine/player]
>
>

## <a name="azure-ad-connect-health-for-ad-dsactive-directory-aadconnect-health-addsmd"></a>[Azure AD Connect Health pro službu AD DS](active-directory-aadconnect-health-adds.md)
Azure AD Connect Health pro službu Active Directory Domain Services (AD DS) podporuje monitorování řadičů domény nainstalovaných v systémech Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows Server 2016. Instalace agenta služby Health umožňuje monitorování místního prostředí AD DS z cloudu. Služba Azure AD Connect Health pro AD DS poskytuje následující sadu klíčových funkcí:

* Monitorování upozornění, aby se zjistilo, kdy řadiče domény nejsou v pořádku, a e-mailová oznámeními pro kritická upozornění
* Řídicí panel Řadiče domény, který poskytuje rychlý přehled kondice a provozního stavu řadičů domény
* Řídicí panel Stav replikace s nejnovějšími informacemi o replikaci a s odkazy na průvodce odstraňováním potíží při zjištění chyb
* Rychlý přístup odkudkoli ke grafům s daty o výkonu oblíbených čítačů výkonu, které jsou nezbytné pro účely monitorování a řešení potíží

Tady najdete další informace o [Používání služby Azure AD Connect Health se službou AD DS](active-directory-aadconnect-health-adds.md).

Následující video poskytuje přehled služby Azure AD Connect Health pro službu AD DS.

> [!VIDEO https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Azure-AD-Connect-Health-monitors-on-premises-AD-Domain-Services/player]
>
>

## <a name="get-started-with-azure-ad-connect-health"></a>Začínáme se službou Azure AD Connect Health
Pokud chcete začít se službou Azure AD Connect Health, použijte následující postup:

1. [Získejte předplatné služby Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) nebo [začněte se zkušební verzí](https://azure.microsoft.com/trial/get-started-active-directory/).
2. [Stáhněte a nainstalujte agenty služby Azure AD Connect Health](#download-and-install-azure-ad-connect-health-agent) na servery identity.
3. Prohlédněte si řídicí panel služby Azure AD Connect Health na adrese [https://aka.ms/aadconnecthealth](https://aka.ms/aadconnecthealth).

> [!NOTE]
> Nezapomeňte, že než na řídicím panelu služby Azure AD Connect Health uvidíte nějaká data, je třeba na cílové servery nainstalovat agenty služby Azure AD Connect Health.
>
>

## <a name="download-and-install-azure-ad-connect-health-agent"></a>Stažení a instalace agenta služby Azure AD Connect Health
* Ověřte, že [splňujete požadavky](active-directory-aadconnect-health-agent-install.md#requirements) pro službu Azure AD Connect Health.
* Začínáme s využitím Azure AD Connect Health pro službu AD FS
    * [Stažení agenta Azure AD Connect Health pro službu AD FS](http://go.microsoft.com/fwlink/?LinkID=518973)
    * [Pokyny k instalaci](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-fs)
* Začínáme s využitím Azure AD Connect Health pro synchronizaci
    * [Stažení a instalace nejnovější verze služby Azure AD Connect](http://go.microsoft.com/fwlink/?linkid=615771) Agent služby Health pro synchronizaci se nainstaluje jako součást instalace služby Azure AD Connect (verze 1.0.9125.0 nebo vyšší).
* Začínáme s využitím Azure AD Connect Health pro službu AD DS
    * [Stažení agenta služby Azure AD Connect Health pro službu AD DS](http://go.microsoft.com/fwlink/?LinkID=820540)
    * [Pokyny k instalaci](active-directory-aadconnect-health-agent-install.md#installing-the-azure-ad-connect-health-agent-for-ad-ds)

## <a name="azure-ad-connect-health-portal"></a>Portál služby Azure AD Connect Health
Portál služby Azure AD Connect Health umožňuje zobrazovat upozornění, monitorování výkonu a analýzy využívání. Hlavní okno služby Azure AD Connect Health najdete na následující adrese URL: https://aka.ms/aadconnecthealth. Je to v podstatě hlavní obrazovka této služby. V hlavním okně uvidíte možnost **Rychlý start**, služby v rámci Azure AD Connect Health a další možnosti konfigurace. Prohlédněte si následující snímek obrazovky a stručné vysvětlení uvedené pod ním. Jakmile nasadíte agenty, služba Health automaticky identifikuje služby, které Azure AD Connect Health monitoruje.

> [!NOTE]
> Informace o licencích najdete v [nejčastějších dotazech ke službě Azure AD Connect Health](active-directory-aadconnect-health-faq.md) nebo na [stránce s cenami služby Azure AD](https://aka.ms/aadpricing).
    
![Portál služby Azure AD Connect Health](./media/active-directory-aadconnect-health/portalsidebar.png)

* **Rychlý start:** Po výběru této možnosti se otevře okno **Rychlý start**. Výběrem možnosti **Získat nástroje** si můžete stáhnout agenta služby Azure AD Connect Health. Máte také přístup k dokumentaci a můžete nám poskytnout zpětnou vazbu.
* **Azure Active Directory Connect (synchronizace):** Tato možnost ukazuje vaše servery Azure AD Connect, které služba Azure AD Connect Health aktuálně monitoruje. Položka **Chyby synchronizace** ukazuje základní chyby synchronizace vaší první zaregistrované služby synchronizace podle kategorií. Po výběru položky **Služby synchronizace** se otevře okno s informacemi o vašich serverech Azure AD Connect. Další informace o těchto možnostech najdete v tématu [Používání služby Azure AD Connect Health pro synchronizaci](active-directory-aadconnect-health-sync.md).
* **Active Directory Federation Services:** Tato možnost ukazuje všechny služby AD FS, které služba Azure AD Connect Health aktuálně monitoruje. Po výběru některé instance se otevře okno s informacemi o příslušné instanci služby. Tyto informace zahrnují přehled, vlastnosti, výstrahy, monitorování a analýzu využití. Další informace o těchto možnostech najdete v tématu [Používání služby Azure AD Connect Health se službou AD FS](active-directory-aadconnect-health-adfs.md).
* **Active Directory Domain Services:** Tato možnost ukazuje všechny doménové struktury AD DS, které služba Azure AD Connect Health aktuálně monitoruje. Po výběru některé doménové struktury se otevře okno s informacemi o příslušné doménové struktuře. Tyto informace zahrnují přehled základních informací, řídicí panel Řadiče domény, řídicí panel Stav replikace, upozornění a monitorování. Další informace o těchto možnostech najdete v tématu [Používání služby Azure AD Connect Health se službou AD DS](active-directory-aadconnect-health-adds.md).
* **Konfigurace:** Tato část umožňuje zapnout nebo vypnout následující možnosti:

  - Položka **Nastavení** obsahuje základní konfigurace vašich agentů. Nastavení automatického upgradu umožňuje automaticky aktualizovat agenta služby Azure AD Connect Health na nejnovější verzi: aktualizace na nejnovější verzi agenta služby Azure AD Connect Health proběhne automaticky pokaždé, když bude taková verze k dispozici. Tato možnost je ve výchozím nastavení zapnutá. Umožnit Microsoftu přístup k datům o stavu adresáře Azure AD výhradně pro účely řešení problémů: Pokud je tato možnost povolená, Microsoft bude moci zobrazit stejná data, která vidíte vy. Tyto informace můžou pomoci při řešení potíží a problémů. Tato možnost je ve výchozím nastavení zakázána.
* **Řízení přístupu na základě role (IAM):** V této části se spravuje přístup k datům služby Connect Health na základě role. 

## <a name="related-links"></a>Související odkazy
* [Instalace agenta služby Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Operace služby Azure AD Connect Health](active-directory-aadconnect-health-operations.md)
* [Používání služby Azure AD Connect Health se službou AD FS](active-directory-aadconnect-health-adfs.md)
* [Používání služby Azure AD Connect Health pro synchronizaci](active-directory-aadconnect-health-sync.md)
* [Používání služby Azure AD Connect Health se službou AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health – nejčastější dotazy](active-directory-aadconnect-health-faq.md)
* [Historie verzí služby Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
