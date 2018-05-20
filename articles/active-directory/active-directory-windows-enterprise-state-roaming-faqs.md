---
title: Nastavení a datový roaming – nejčastější dotazy | Microsoft Docs
description: Poskytuje odpovědi na dotazy, které správci IT můžou mít o nastavení a synchronizaci dat aplikací.
services: active-directory
keywords: Enterprise stavu nastavení roamingu windows cloudu, nejčastější dotazy na enterprise stavu roaming
documentationcenter: ''
author: tanning
manager: mtillman
editor: curtand
ms.assetid: c0824f5c-129b-4240-969f-921f6a64eae7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2018
ms.author: markvi
ms.openlocfilehash: f33376d5f68d64495a7a90e62870f3ec14f73246
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="settings-and-data-roaming-faq"></a>Nejčastější dotazy k nastavení a datovému roamingu
Tento článek obsahuje odpovědi na některé dotazy, které správci IT mohou mít o nastavení a synchronizaci dat aplikací.

## <a name="what-data-roams"></a>Jaká data přemístí?
**Nastavení systému Windows**: nastavení počítače, které jsou součástí operačního systému Windows. Obecně platí jsou to nastavení, které přizpůsobení počítače a obsahují následující rozsáhlé kategorie:

* *Motiv*, což zahrnuje funkce, jako je nastavení plochy motiv a na hlavním panelu.
* *Nastavení aplikace Internet Explorer*, včetně posledních otevřených karet a Oblíbené položky.
* *Okraj nastavení prohlížeče*, jako jsou například oblíbených položek a čtení seznamu.
* *Hesla*, včetně Internetu hesla, profily Wi-Fi a další.
* *Jazykové předvolby*, což zahrnuje nastavení pro rozložení klávesnice, jazykem, datum a čas a další.
* *Snadné získat přístup k funkcím*, jako jsou například motiv s vysokým kontrastem a Předčítání, Lupa.
* *Další nastavení Windows*, jako jsou nastavení příkazového řádku a seznam aplikací.

**Data aplikací**: univerzální aplikace pro Windows může zapsat do složky cestovního nastavení data a všechny data zapsaná do této složky se budou automaticky synchronizovat. Je to na vývojáře jednotlivých aplikací k návrhu aplikace využít tuto funkci. Další podrobnosti o tom, jak vyvíjet aplikace Universal Windows, který používá roaming najdete v tématu [úložiště data aplikací API](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) a [data aplikací Windows 8 roaming blogu pro vývojáře](http://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## <a name="what-account-is-used-for-settings-sync"></a>Který účet se používá pro nastavení synchronizace?
V systému Windows 8 a Windows 8.1 nastavení synchronizace vždycky používají účty Microsoft příjemce. Enterprise uživatelé měli možnost připojit se k účtu domény služby Active Directory k získání přístupu k synchronizaci nastavení účtu Microsoft. Ve Windows 10 toto připojení účtu Microsoft, že funkce je nahrazován framework primární a sekundární účet.

Primární účet je definován jako účet použitý k přihlášení k systému Windows. To může být účet Microsoft, účet služby Azure Active Directory (Azure AD), účet místní služby Active Directory nebo místní účet. Kromě primární účet uživatelé Windows 10, můžete přidat jeden nebo více účtů sekundární cloudu do svého zařízení. Sekundární účet je obvykle účet Microsoft, účet Azure AD nebo jiný účet třeba z Gmailu nebo Facebook. Tyto účty sekundární poskytují přístup k další služby, jako je například jednotné přihlašování a úložiště systému Windows, ale nejsou schopná pohánějící nastavení synchronizace.

V systému Windows 10, lze použít pouze primární účet pro zařízení pro synchronizaci nastavení (viz [jak se dá upgradovat z synchronizaci nastavení účtu Microsoft ve Windows 8 do služby Azure AD nastavení synchronizace ve Windows 10?](active-directory-windows-enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

Data se nikdy kombinované mezi různé uživatelské účty v zařízení. Existují dvě pravidla pro synchronizaci nastavení:

* Nastavení systému Windows bude vždy spolu s primární účet.
* Data aplikací budou označené účet použitý k získání aplikací. Jenom aplikace, které jsou označené primární účet bude synchronizovat. Označování vlastnictví aplikace je určen, když je aplikace zkušebně načtených prostřednictvím Windows Store nebo Správa mobilních zařízení (MDM).

Pokud aplikace vlastníka nelze identifikovat, bude spolu s primární účet. Pokud zařízení je upgrade z Windows 8 nebo Windows 8.1 na Windows 10, všechny aplikace označí jako získat účet Microsoft. Je to proto, že většina uživatelů získat aplikace z Windows Store a se žádná podpora pro Windows Store pro účty Azure AD před Windows 10. Pokud je aplikace nainstalována licenci offline, aplikace označené pomocí primární účtu na zařízení.

> [!NOTE]
> Zařízení s Windows 10, které jsou ve vlastnictví společnosti a jsou připojené ke službě Azure AD můžete připojit své účty Microsoft už na účet domény. Možnost připojit k účtu Microsoft na účet domény a mít všechny uživatele synchronizaci dat do účtu Microsoft (to znamená, že účet Microsoft roaming prostřednictvím připojeného účtu Microsoft a funkce služby Active Directory) je odstraněná z Windows 10 zařízení, které jsou připojeny k připojeném prostředí služby Active Directory nebo Azure AD.
>
>

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Jak se dá upgradovat z synchronizaci nastavení účtu Microsoft ve Windows 8 do služby Azure AD sync nastavení v systému Windows 10?
Pokud jsou připojena k doméně služby Active Directory, systémem Windows 8 nebo Windows 8.1 připojených účtem Microsoft, bude synchronizovat nastavení prostřednictvím účtu Microsoft. Po upgradu na Windows 10, bude pokračovat na synchronizaci nastavení uživatele prostřednictvím účtu Microsoft, dokud se uživatel připojený k doméně a názvu domény služby Active Directory se nemůže spojit se Azure AD.

Pokud se s Azure AD připojit místní doméně služby Active Directory, zařízení se pokusí o synchronizaci nastavení pomocí připojené účet Azure AD. Pokud správce Azure AD není povolen Enterprise State Roaming, vaše připojené účet Azure AD se zastaví synchronizaci nastavení. Pokud jste uživatelem systému Windows 10 a přihlásit Azure AD identity, se spustí, jakmile správce umožní synchronizace nastavení přes Azure AD synchronizovat nastavení systému windows.

Pokud jste uložili všechna osobní data v podnikových zařízení, byste měli vědět, že operačního systému Windows a data aplikací bude zahájena synchronizace Azure AD. To má následující důsledky:

* Nastavení svého osobního účtu Microsoft se soubor kromě nastavení na váš pracovní nebo školní účty Azure AD. Důvodem je, že účet Microsoft a nastavení služby Azure AD synchronizovat nyní používáte samostatné účty.
* Osobní data, jako jsou hesla Wi-Fi, webové pověření a Oblíbené položky aplikace Internet Explorer, dříve synchronizované prostřednictvím připojeného účtu Microsoft se budou synchronizovat přes Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Jak se účet Microsoft a pracovní vzájemná funkční spolupráce Azure AD Enterprise stavu Roaming?
V listopadu 2015 nebo novější verze Windows 10 Enterprise State Roaming je podporována pouze pro jeden účet najednou. Pokud se přihlaste do Windows pomocí pracovní nebo školní účet Azure AD, všechna data synchronizují přes Azure AD. Pokud se přihlásíte se k systému Windows pomocí osobního účtu Microsoft, všechna data se budou synchronizovat prostřednictvím účtu Microsoft. Univerzální data aplikací bude přenášet pomocí pouze primární přihlášení účtu na zařízení, a bude mohla používat pouze v případě, že primární účet vlastní licenci aplikace. Nebudou synchronizovat data univerzálních aplikací pro aplikace vlastníkem všechny sekundární účty.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Nastavení synchronizace pro účty Azure AD od víc klientů?
Když více Azure AD účty z různých klientů Azure AD jsou na stejném zařízení, musíte aktualizovat registru zařízení ke komunikaci se službou Azure Rights Management pro každého klienta Azure AD.  

1. Najdete identifikátor GUID pro každý klient Azure AD. Otevřete portál Azure a vyberte klienta Azure AD. Identifikátor GUID pro klienta je na stránce vlastnosti vybraného klienta (https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties), s popiskem **ID adresáře**. 
2. Až budete mít identifikátor GUID, budete muset přidat klíč registru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<klienta ID GUID >**.
   Z **klienta ID GUID** klíče, vytvořte novou víceřetězcovou hodnotu (REG SZ více) s názvem **AllowedRMSServerUrls**. Pro svá data zadejte distribuční bod adresy URL licencování Azure klientů, které má přístup k zařízení.
3. Licenční adresy URL distribučního bodu můžete najít spuštěním **Get-AadrmConfiguration** rutiny z modulu AADRM. Pokud hodnoty **LicensingIntranetDistributionPointUrl** a **LicensingExtranetDistributionPointUrl** se liší, zadat obě hodnoty. Pokud jsou hodnoty stejné, zadejte hodnotu pouze jednou.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Jaké jsou možnosti cestovní nastavení pro existující aplikace pracovní plochy Windows?
Roaming funguje pouze pro univerzální aplikace pro Windows. K dispozici pro povolení roamingu na existující aplikaci plochy Windows existují dvě možnosti:

* [Plochy most](https://aka.ms/desktopbridge) umožňuje uvést vaše stávající aplikace Windows desktop do univerzální platformy Windows. Z tohoto místa budou muset využít výhod roaming dat aplikací Azure AD minimálními změnami kódu. Most plocha poskytuje aplikace s identitou aplikace, která je nutná pro povolení existující aplikací klasické pracovní plochy roaming dat aplikací.
* [Virtualizace uživatelského prostředí (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) vám pomůže vytvořit šablonu vlastní nastavení pro existující aplikacích klasické pracovní plochy Windows a povolit roaming pro aplikace Win32. Tato možnost nevyžaduje, aby vývojáři aplikace změny kódu aplikace. UE-V je omezený na místní služby Active Directory roamingu pro zákazníky, kteří si koupili sady Microsoft Desktop Optimization Pack.

Správci mohou nakonfigurovat UE-V se bude používat roaming dat aplikace na ploše systému Windows, změnou cestovní nastavení operačního systému Windows a univerzální aplikace pro data prostřednictvím [UE-V zásady skupiny](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), včetně:

* Mohla používat zásady skupiny nastavení systému Windows
* Nesynchronizovat zásad skupiny aplikací pro Windows
* Roaming v části aplikace Internet Explorer

Microsoft může v budoucnu, prozkoumejte způsoby, jak provést UE-V, které úzce integruje do systému Windows a rozšířit UE-V se bude používat roaming nastavení prostřednictvím cloudu Azure AD.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Může ukládat synchronizovaná nastavení a data místně?
Enterprise State Roaming ukládá všechna synchronizovaná data v cloudu Azure. UE-V nabízí místní řešení roamingu.

## <a name="who-owns-the-data-thats-being-roamed"></a>Kdo je vlastníkem dat, který je právě roamované?
Podniky vlastní data roamované prostřednictvím Enterprise State Roaming. Data jsou uložena v datovém centru Azure. Všechna data se šifrují během přenosu i v klidovém stavu uložených v cloudu pomocí služby Azure Rights Management z Azure Information Protection. Toto je zlepšení ve srovnání s synchronizaci nastavení založeného na účet Microsoft, který šifruje jenom určité citlivých dat jako pověření uživatele před opuštěním zařízení.

Společnost Microsoft se zavazuje chránit data zákazníků. Uživatel s enterprise nastavení data se šifrují automaticky pomocí služby Azure Rights Management, než opustí zařízením s Windows 10, aby žádný jiný uživatel může číst tato data. Pokud má vaše organizace na placené předplatné pro službu Azure Rights Management, můžete využívat další funkce ochrany, jako je například sledovat a odvolat dokumenty, automaticky chránit e-mailů, které obsahují citlivé informace a spravovat vlastní klíče ("přineste vlastní klíč"řešení, také známé jako BYOK). Další informace o těchto funkcích a o tom, jak tuto službu ochrany funguje, najdete v části [co je Azure Rights Management](https://docs.microsoft.com/azure/information-protection/understand-explore/what-is-information-protection).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Můžete spravovat synchronizace pro konkrétní aplikaci nebo nastavení?
V systému Windows 10 neexistuje žádné MDM nebo zásady skupiny nastavení zakázat roamingu pro jednotlivé aplikace. Správci klienta můžete zakázat synchronizaci data aplikací pro všechny aplikace na spravované zařízení, ale neexistuje žádné jemnějšího ovládání na úrovni pro aplikaci nebo v rámci aplikace.

## <a name="how-can-i-enable-or-disable-roaming"></a>Jak můžete povolit nebo zakázat roaming?
V **nastavení** aplikace, přejděte na **účty** > **synchronizace nastavení**. Z této stránky se zobrazí, který účet se používá se bude používat roaming nastavení, a můžete povolit nebo zakázat jednotlivé skupiny nastavení, chcete-li být roamované.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Co je doporučením společnosti Microsoft pro povolení roaming v systému Windows 10?
Microsoft má několik různých nastavení roamingu řešení, které jsou k dispozici, včetně cestovních profilů uživatelů, UE-V a Enterprise stavu roamingu.  Společnost Microsoft se zaměřuje na poskytování investicemi v podniku stavu Roaming v budoucích verzích systému Windows. Pokud vaše organizace není připraveno nebo možnost s přesun dat do cloudu, pak doporučujeme použít UE-V jako primární roamingu technologie. Pokud vaše organizace vyžaduje roaming podporu pro existující aplikace pracovní plochy Windows, ale je přes přesunout do cloudu, doporučujeme, abyste použili Enterprise stavu roamingu a UE-V. I když jsou velmi podobné technologie UE-V a Enterprise State Roaming, nejsou vzájemně vylučují. Doplňují navzájem k zajištění, že vaše organizace poskytuje roamingu služby, které uživatelé potřebují.  

Pokud používáte Enterprise State Roaming a UE-V, platí následující pravidla:

* Enterprise State Roaming je primární roamingu agent na zařízení. UE-V se používá k doplnění "Win32 mezery."
* Cestovní nastavení systému Windows a moderní data aplikací UWP UE-V by mělo být zakázáno, když pomocí skupiny UE-V zásady. Tyto jsou již vztahuje Enterprise State Roaming.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Jak Enterprise State Roaming podporuje infrastruktury virtuálních klientských (počítačů VDI)?
Enterprise State Roaming je podporována v klientovi Windows 10 SKU, ale není na serveru SKU. Pokud klient virtuálního počítače je hostované na hypervisoru počítače a vzdáleně přihlaste k virtuálnímu počítači, bude přenášet data. Pokud se více uživateli sdílet stejný operační systém a uživatelé vzdáleně přihlašují k serveru pro úplné desktopové prostředí, roaming nemusí fungovat. V druhé situaci na bázi relace není oficiálně podporován.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Po použití roamingu co se stane, když Moje organizace zakoupí předplatné, které zahrnuje Azure Rights Management?
Pokud vaše organizace už používá roaming v systému Windows 10 s omezeným využitím bezplatné předplatné Azure Rights Management, nákup [placené předplatné](https://azure.microsoft.com/pricing/details/information-protection/) nebude mít služby ochrany služby Azure Rights Management, který obsahuje dopady na funkci funkci roamingu a žádné změny konfigurace, bude nutné váš správce IT.

## <a name="known-issues"></a>Známé problémy
Podrobnosti najdete v dokumentaci v [řešení potíží s](active-directory-windows-enterprise-state-roaming-troubleshooting.md) části Seznam známých problémů. 

## <a name="related-topics"></a>Související témata
* [Přehled roamingu stavu Enterprise](active-directory-windows-enterprise-state-roaming-overview.md)
* [Povolit stav enterprise roaming v Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
* [Nastavení MDM pro synchronizaci nastavení zásad skupiny a](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Odkaz nastavení roamingu Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Řešení potíží](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
