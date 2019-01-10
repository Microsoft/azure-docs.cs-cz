---
title: Nastavení a datovému roamingu – nejčastější dotazy | Dokumentace Microsoftu
description: Poskytuje odpovědi na některé dotazy, které správce IT může mít informace o nastavení a synchronizace dat aplikace.
services: active-directory
keywords: Enterprise stavu cestovní nastavení cloudu systému windows, nejčastější dotazy k enterprise stav roamingu
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: curtand
ms.component: devices
ms.assetid: c0824f5c-129b-4240-969f-921f6a64eae7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: markvi
ms.openlocfilehash: 9f3d171a8f0e353d5860f410a8c32149f8872338
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189384"
---
# <a name="settings-and-data-roaming-faq"></a>Nejčastější dotazy k nastavení a datovému roamingu
Tento článek obsahuje odpovědi na některé otázky, které správce IT může mít informace o nastavení a synchronizace dat aplikace.

## <a name="what-data-roams"></a>Jaká data při roamingu?
**Nastavení Windows**: nastavení počítače, které jsou součástí operačního systému Windows. Obecně platí jedná se o nastavení, které přizpůsobení počítače a zahrnují následujících kategorií:

* *Motiv*, což zahrnuje funkce, jako je nastavení motivu a na hlavním panelu na ploše.
* *Nastavení aplikace Internet Explorer*, včetně naposledy otevřených karet a Oblíbené položky.
* *Nastavení prohlížeče Microsoft Edge*, jako je například oblíbené položky a čtení seznamu.
* *Hesla*, včetně Internet hesla, profilů sítě Wi-Fi a další.
* *Jazykové předvolby*, což zahrnuje nastavení pro rozložení klávesnice, jazyk systému, datum a čas a další.
* *Snadné získat přístup k funkcím*, jako je například motiv s vysokým kontrastem a program Předčítání, Lupa.
* *Další nastavení Windows*, jako je například nastavení myši.

**Data aplikací**: Univerzální aplikace pro Windows můžete zapisovat do složky cestovního nastavení data a všechny data zapsaná do této složky se budou automaticky synchronizovat. Záleží jen na jednotlivé aplikace pro vývojáře k návrhu aplikace, abyste mohli využít tuto funkci. Další podrobnosti o tom, jak vývoj aplikace pro Universal Windows, který používá roaming, najdete v článku [API úložiště appdata](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) a [roaming blog pro vývojáře aplikací Windows 8](https://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## <a name="what-account-is-used-for-settings-sync"></a>Který účet se používá pro synchronizaci nastavení?
Synchronizace nastavení ve Windows 8.1, vždy používá účty zákazníků Microsoftu. Podnikoví uživatelé měli možnost připojení k účtu v doméně služby Active Directory k získání přístupu k synchronizaci nastavení účtu Microsoft. Ve Windows 10 připojené to účet Microsoft, že funkce je nahrazen účtu primárního/sekundárního framework.

Primární účet je definován jako účet použitý k přihlášení k Windows. To může být účet Microsoft, účet služby Azure Active Directory (Azure AD), účet místní služby Active Directory nebo místní účet. Kromě primárního účtu uživatele Windows 10 přidat jeden nebo více účtů sekundárního cloudu do svého zařízení. Sekundární účet je obvykle účet Microsoft, účet Azure AD nebo jiného účtu třeba z Gmailu nebo Facebook. Tyto sekundární účty poskytují přístup k další služby, jako je jednotné přihlašování a Windows Store, ale nejsou schopná běží na ní takové nastavení synchronizace.

Ve Windows 10, je možné jenom primární účet zařízení pro synchronizaci nastavení (viz [Jak můžu upgradovat z synchronizaci nastavení účtu Microsoft ve Windows 8 do služby Azure AD sync nastavení ve Windows 10?](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

Data se nikdy směšovat mezi různé uživatelské účty na zařízení. Existují dvě pravidla pro synchronizaci nastavení:

* Nastavení Windows se vždy zpřístupní primárního účtu.
* Data aplikací budou označené účet použitý k získání aplikace. Synchronizuje jenom aplikace, které jsou označené primárního účtu. Označování vlastnictví aplikace je určena při aplikace se zkušebně načtených prostřednictvím Windows Store nebo správu mobilních zařízení (MDM).

Pokud vlastník aplikace nelze identifikovat, zpřístupní se primární účet. Pokud zařízení je upgradovat z Windows 8 nebo Windows 8.1 na Windows 10, protože získat účet Microsoft se označí všechny aplikace. Je to proto, že většina uživatelů získat aplikace přes Windows Store a došlo k dispozici žádná podpora pro Windows Store pro účty Azure AD před Windows 10. Pokud je aplikace nainstalovaná prostřednictvím offline licence, aplikace označené pomocí primárního účtu v zařízení.

> [!NOTE]
> Zařízení s Windows 10, které jsou ve vlastnictví organizace a jsou připojené ke službě Azure AD už můžete připojit své účty Microsoft na účet domény. Možnost připojení k účtu domény s účtem Microsoft a mít synchronizace dat všech uživatelů k účtu Microsoft (to znamená, účet Microsoft roaming přes připojený účet Microsoft a funkce služby Active Directory) je odstraněná z Windows 10 zařízení, která jsou připojená k propojené prostředí služby Active Directory nebo Azure AD.
>
>

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Jak můžu upgradovat z synchronizaci nastavení účtu Microsoft ve Windows 8 do služby Azure AD sync nastavení ve Windows 10?
Pokud jsou připojeny k doméně služby Active Directory s Windows 8.1 připojený účet Microsoft, budou synchronizovat nastavení prostřednictvím účtu Microsoft. Po upgradu na Windows 10, budete i nadále synchronizovat nastavení uživatele přes účet Microsoft, jako je uživatel připojený k doméně a doméně služby Active Directory s Azure AD nepřipojuje.

Pokud místní domény služby Active Directory s Azure AD connect, zařízení se pokusí o synchronizaci nastavení pomocí připojeném účtu služby Azure AD. Pokud správce Azure AD neumožňuje Enterprise State Roaming, vaše připojení účtu služby Azure AD se zastaví, synchronizovat nastavení. Pokud jste uživatelem systému Windows 10 a přihlaste se pomocí identity Azure AD, se spustí, synchronizovat nastavení systému windows, poté, co váš správce povolí synchronizaci nastavení přes Azure AD.

Pokud jste si uložili všechny osobní údaje na firemní zařízení, byste měli vědět, že operační systém Windows a data aplikací se začnou synchronizují do služby Azure AD. Má to následující důsledky:

* Osobní nastavení účtu Microsoft bude odchylek kromě nastavení na váš pracovní nebo školní účty Azure AD. Důvodem je, že účet Microsoft a nastavení služby Azure AD synchronizovat nyní používají samostatné účty.
* Osobní údaje, jako jsou hesla Wi-Fi, webových přihlašovacích údajů a Oblíbené položky aplikace Internet Explorer, které byly dříve synchronizovaná přes propojeném účtu Microsoft se budou synchronizovat prostřednictvím služby Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Jak vytvořit účet Microsoft a pracovní vzájemná funkční spolupráce Azure AD podnikový Roaming stavu?
V listopadu 2015 nebo novější verze Windows 10 Enterprise State Roaming je podporována pouze pro jeden účet v čase. Pokud se přihlášení k Windows pomocí svého pracovního nebo školního účtu služby Azure AD, všechna data se budou synchronizovat prostřednictvím služby Azure AD. Pokud se přihlásíte se k Windows pomocí osobního účtu Microsoft, všechna data se budou synchronizovat prostřednictvím účtu Microsoft. Univerzální appdata se zpřístupní účtem jenom primární přihlášení na zařízení a zpřístupní pouze v případě, že licence aplikace je vlastníkem primárního účtu. Univerzálních aplikací pro aplikace pro vlastní sekundární účtů nebudou synchronizovány.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Nastavení synchronizace pro účty Azure AD z více tenantů?
Pokud několik služeb Azure AD pro účty z různých tenantů Azure AD jsou na stejném zařízení, je nutné aktualizovat registr zařízení komunikovat se službou Azure Rights Management pro jednotlivé tenanty Azure AD.  

1. Najdete identifikátor GUID pro jednotlivé tenanty Azure AD. Otevřete na webu Azure portal a vyberte tenanta Azure AD. Na stránce vlastnosti vybraného tenanta je identifikátor GUID pro klienta (https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties), s popiskem **ID adresáře**. 
2. Jakmile budete mít identifikátor GUID, budete muset přidat klíč registru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<tenanta Identifikátor GUID >**.
   Z **tenanta Identifikátor GUID** klíče, vytvořte novou víceřetězcovou hodnotu (REG-MULTI-SZ) s názvem **AllowedRMSServerUrls**. Její data určete distribuční bod adresy URL licencování Azure tenantů, které má přístup k zařízení.
3. Licenční adresy URL distribučního bodu můžete najít spuštěním **Get-AadrmConfiguration** rutiny z modulu AADRM. Pokud hodnoty **LicensingIntranetDistributionPointUrl** a **LicensingExtranetDistributionPointUrl** se liší, zadat obě hodnoty. Pokud jsou hodnoty stejné, zadejte hodnotu pouze jednou.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Jaké jsou možnosti cestovního nastavení pro existující aplikace klasické pracovní plochy Windows?
Roaming funguje jenom pro Universal Windows apps. Pro povolení roamingu v existující aplikace klasické pracovní plochy Windows k dispozici jsou dvě možnosti:

* [Přemostění na Desktop](https://aka.ms/desktopbridge) umožňuje přenést existující desktopové aplikace Windows do univerzální platformy Windows. Tady bude minimálními změnami kódu vyžaduje, abyste mohli využívat roaming dat aplikací Azure AD. Přemostění na Desktop poskytuje vaše aplikace s identitou aplikace, která je potřebná k povolení pro existující aplikace klasické pracovní plochy roaming dat aplikací.
* [Virtualizace uživatelského prostředí (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) vám pomůže vytvořit vlastní nastavení šablony pro existující aplikace klasické pracovní plochy Windows a povolit roaming pro aplikace Win32. Tato možnost nevyžaduje, aby vývojáři aplikace Změna kódu aplikace. UE-V je omezený na místní služby Active Directory roamingu pro zákazníky, kteří si zakoupili Microsoft Desktop Optimization Pack.

Správci mohou nakonfigurovat UE-V do roaming dat aplikací klasické pracovní plochy Windows tak, že změníte cestovní nastavení operačního systému Windows a univerzální aplikace pro data prostřednictvím [UE-V zásady skupiny](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), včetně:

* Zásady skupiny Windows nastavení roamingu
* Nesynchronizovat zásad skupiny aplikací Windows
* V části aplikace Internet Explorer

Microsoft může v budoucnu, prozkoumat způsoby, jak provést UE-V, které jsou hluboce integrovány do Windows a rozšiřují UE-V do roaming nastavení prostřednictvím cloudové služby Azure AD.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Můžete ukládat synchronizovaná nastavení a data v místním prostředí?
Enterprise State Roaming ukládá všechna synchronizovaná data v cloudu Microsoftu. UE-V nabízí místní cestovní řešení.

## <a name="who-owns-the-data-thats-being-roamed"></a>Kdo vlastní data, která je přenosné?
Podniky vlastní data s roamingem prostřednictvím Enterprise State Roaming. Data se ukládají v datovém centru Azure. Všechna data se šifrují přenášená i neaktivní uložená v cloudu pomocí služby Azure Rights Management z Azure Information Protection. Toto je vylepšení v porovnání s synchronizace nastavení založené na účtu Microsoft, který šifruje jenom určité citlivých dat jako jsou přihlašovací údaje uživatele, před opuštěním zařízení.

Společnosti Microsoft záleží na ochranu dat zákazníků. Organizace uživatele nastavení data budou automaticky šifrována pomocí služby Azure Rights Management před opuštěním zařízení s Windows 10, aby žádný jiný uživatel může číst data. Pokud vaše organizace má předplatné pro službu Azure Rights Management, můžete využívat jiné funkce ochrany, jako je například sledovat a odvolat dokumenty, automaticky chránit e-mailů, které obsahují citlivé informace a správu vlastních klíčů ("přineste your own key"řešení, označované také jako BYOK). Další informace o těchto funkcích a jak tuto službu ochrany funguje, najdete v části [co je Azure Rights Management](/azure/information-protection/what-is-information-protection).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Můžete spravovat synchronizace pro konkrétní aplikaci nebo nastavení?
Ve Windows 10 neexistuje žádné MDM nebo zásady skupiny nastavení zakázat roamingu pro jednotlivé aplikace. Správci klientů mohou zakázat synchronizaci appdata u všech aplikací na spravovaná zařízení, ale neexistuje žádná lepší kontrolu na úrovni pro aplikaci nebo v rámci aplikace.

## <a name="how-can-i-enable-or-disable-roaming"></a>Jak můžete povolit nebo zakázat roaming?
V **nastavení** aplikaci, přejděte na **účty** > **synchronizovat nastavení**. Na této stránce můžete zobrazit, který účet se používá k nastavení roamingu a můžete povolit nebo zakázat jednotlivé skupiny nastavení, chcete-li být s roamingem.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Co je doporučením společnosti Microsoft pro povolení roaming ve Windows 10?
Microsoft má několik různých nastavení roamingu řešení, které jsou k dispozici, včetně cestovních profilů uživatelů, UE-V a Enterprise stav roamingu.  Společnost Microsoft se zaměřuje na poskytování investice do organizace stav roamingu v budoucích verzích Windows. Pokud vaše organizace není připravena nebo pohodlné s přesunem dat do cloudu, pak doporučujeme použít UE-V jako váš primární technologii ve roamingu. Pokud vaše organizace vyžaduje podporu pro existující aplikace klasické pracovní plochy Windows roamingu, ale nemůžou dočkat, až cloud mohli přejít, doporučujeme použít Enterprise stav roamingu a UE-V. I když UE-V a Enterprise State Roaming jsou velmi podobné technologie, nejsou vzájemně vylučují. Doplňují ostatní pomáhá zajistit, že vaše organizace poskytuje roamingu služby, které uživatelé potřebují.  

Pokud používáte Enterprise State Roaming a UE-V, platí následující pravidla:

* Enterprise State Roaming je agent, kterého primární roamingu v zařízení. UE-V se používá k doplnění "Win32 mezery."
* Roaming nastavení Windows a dat moderních aplikací UPW UE-V by měl být zakázáno, pokud skupině UE-V pomocí zásady. Tyto jsou již zahrnuté ve Enterprise State Roaming.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Jak Enterprise State Roaming podporuje infrastruktury virtuálních klientských (počítačů VDI)?
Enterprise State Roaming je podporována v klientovi Windows 10 skladové položky, ale není na serveru skladové položky. Pokud klientského virtuálního počítače je hostované na hypervisoru počítači a vzdáleně přihlaste k virtuálnímu počítači, zpřístupní se vaše data. Pokud více mohou uživatelé sdílet stejný operační systém a uživatelé vzdáleně přihlašují k serveru pro úplné desktopové prostředí, roaming, nemusí fungovat. Druhý scénář na základě relace není oficiálně podporován.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Jakmile používáte roaming, co se stane, když Moje organizace koupí předplatné, které zahrnuje Azure Rights Management?
Pokud vaše organizace už používá roaming v systému Windows 10 s omezeným použitím bezplatné předplatné Azure Rights Management, nákupu [placené předplatné](https://azure.microsoft.com/pricing/details/information-protection/) , které zahrnuje Azure Rights Management nebudete mít službu ochrany žádný vliv na funkčnost funkci cestovní a bez změny konfigurace se vyžaduje správce IT.

## <a name="known-issues"></a>Známé problémy
Podrobnosti najdete v dokumentaci [řešení potíží s](enterprise-state-roaming-troubleshooting.md) najdete seznam známých problémů. 

## <a name="next-steps"></a>Další postup 

Přehled najdete v tématu [roaming přehled stavu enterprise](enterprise-state-roaming-overview.md)
