---
title: Nejčastější dotazy k roamingu podnikového stavu – služba Azure Active Directory
description: Nejčastější dotazy týkající se ESR
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ad76835b0c72b691e1ef8810f2c58dedb8f597d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672381"
---
# <a name="settings-and-data-roaming-faq"></a>Nejčastější dotazy k nastavení a datovému roamingu

Tento článek odpovídá na některé otázky, které mohou správci IT mít ohledně nastavení a synchronizace dat aplikací.

## <a name="what-data-roams"></a>Jaká data se toulají?

**Nastavení systému Windows**: nastavení počítače, které je integrováno do operačního systému Windows. Obecně se jedná o nastavení, která personalizují váš počítač, a zahrnují následující široké kategorie:

* *Motiv*, který obsahuje funkce, jako je motiv plochy a nastavení hlavního panelu.
* *Nastavení aplikace Internet Explorer*, včetně naposledy otevřených karet a oblíbených položek.
* *Nastavení prohlížeče Microsoft Edge*, například oblíbené položky a seznam k přečtení.
* *Hesla*, včetně internetových hesel, profilů Wi-Fi a dalších.
* *Jazykové předvolby*, které zahrnují nastavení rozložení klávesnice, jazyk systému, datum a čas a další.
* *Snadné přístupové funkce*, například motiv s vysokým kontrastem, Předčítání a Lupa.
* *Další nastavení systému Windows*, například nastavení myši.

> [!NOTE]
> Tento článek se týká prohlížeče microsoft edge legacy založeného na HTML spuštěném v systému Windows 10 v červenci 2015. Tento článek se nevztahuje na nový prohlížeč založený na microsoft edge chromu vydaném 15. Další informace o chování synchronizace pro nový microsoft edge naleznete v článku [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

**Data aplikace**: Univerzální aplikace pro Windows mohou zapisovat data nastavení do roamingové složky a všechna data zapsaná do této složky budou automaticky synchronizována. Je na jednotlivých vývojářích aplikací, aby navrhl aplikaci, která tuto možnost využije. Další informace o vývoji univerzální aplikace pro Windows, která používá roaming, najdete v [rozhraní APPDATA Storage API](https://msdn.microsoft.com/library/windows/apps/mt299098.aspx) a blogu pro [vývojáře appdata roamingu ve Windows 8](https://blogs.msdn.com/b/windowsappdev/archive/2012/07/17/roaming-your-app-data.aspx).

## <a name="what-account-is-used-for-settings-sync"></a>Jaký účet se používá pro synchronizaci nastavení?

Ve Windows 8.1 synchronizace nastavení vždy používala spotřebitelské účty Microsoft. Podnikoví uživatelé měli možnost připojit účet Microsoft ke svému účtu domény služby Active Directory, aby získali přístup k synchronizaci nastavení. V systému Windows 10 je tato funkce propojeného účtu Microsoft nahrazována primárním nebo sekundárním rozhraním účtu.

Primární účet je definován jako účet používaný k přihlášení k systému Windows. Může se jedná o účet Microsoft, účet Azure Active Directory (Azure AD), místní účet služby Active Directory nebo místní účet. Kromě primárního účtu můžou uživatelé Windows 10 do svého zařízení přidat jeden nebo více sekundárních cloudových účtů. Sekundární účet je obecně účet Microsoft, účet Azure AD nebo nějaký jiný účet, například Gmail nebo Facebook. Tyto sekundární účty poskytují přístup k dalším službám, jako je jednotné přihlašování a Windows Store, ale nejsou schopny zapnout synchronizaci nastavení.

Ve Windows 10 se pro synchronizaci nastavení dá použít jenom primární účet pro zařízení (viz [Jak upgradovat z synchronizace nastavení účtu Microsoft v systému Windows 8 na synchronizaci nastavení Azure AD v systému Windows 10?](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

Data se nikdy nemíchají mezi různými uživatelskými účty v zařízení. Pro synchronizaci nastavení existují dvě pravidla:

* Nastavení systému Windows se bude vždy přecvakovat s primárním účtem.
* Data aplikace budou označena účtem použitým k získání aplikace. Synchronizovat se budou jenom aplikace označené primárním účtem. Označování vlastnictví aplikací se určuje, když je aplikace načtená z boku prostřednictvím windows storu nebo správy mobilních zařízení (MDM).

Pokud vlastníka aplikace nelze identifikovat, bude se přepínat s primárním účtem. Pokud je zařízení upgradováno z Windows 8 nebo Windows 8.1 na Windows 10, všechny aplikace se označí tak, jak je získal účet Microsoft. Důvodem je, že většina uživatelů získává aplikace prostřednictvím Windows Storu a před Windows 10 neexistovala žádná podpora Windows Storu pro účty Azure AD. Pokud je aplikace nainstalovaná prostřednictvím offline licence, bude označena pomocí primárního účtu na zařízení.

> [!NOTE]
> Zařízení s Windows 10, která vlastní podnik a jsou připojená k Azure AD, už nemůžou připojit své účty Microsoft k účtu domény. Možnost připojit účet Microsoft k účtu domény a synchronizovat všechna data uživatele s účtem Microsoft (to znamená účet Microsoft roaming prostřednictvím připojeného účtu Microsoft a funkce služby Active Directory) se odebere ze systému Windows 10. zařízení, která jsou připojena k připojenéslužbě Active Directory nebo prostředí Azure AD.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Jak můžu upgradovat z synchronizace nastavení účtu Microsoft ve Windows 8 na synchronizaci nastavení Azure AD ve Windows 10?

Pokud jste připojeni k doméně služby Active Directory se systémem Windows 8.1 s připojeným účtem Microsoft, synchronizujete nastavení prostřednictvím svého účtu Microsoft. Po upgradu na Windows 10 budete pokračovat v synchronizaci uživatelských nastavení prostřednictvím účtu Microsoft, pokud jste uživatelem napojeným na doménu a doména Služby Active Directory se nepřipojí k Azure AD.

Pokud se místní doména Služby Active Directory připojuje k Azure AD, vaše zařízení se pokusí synchronizovat nastavení pomocí připojeného účtu Azure AD. Pokud správce Azure AD nepovolí roaming Enterprise State Roaming, váš připojený účet Azure AD zastaví nastavení synchronizace. Pokud jste uživatel Windows 10 a přihlásíte se pomocí identity Azure AD, začnete synchronizovat nastavení windows, jakmile správce povolí synchronizaci nastavení prostřednictvím Azure AD.

Pokud jste na podnikovém zařízení uložili nějaká osobní data, měli byste si být vědomi toho, že data operačního systému Windows a aplikací se začnou synchronizovat se službou Azure AD. To má následující důsledky:

* Vaše osobní nastavení účtu Microsoft se bude lišit od nastavení na vašich pracovních nebo školních účtech Azure AD. Důvodem je, že synchronizace nastavení účtu Microsoft a Azure AD teď používá samostatné účty.
* Osobní údaje, jako jsou hesla Wi-Fi, přihlašovací údaje webu a oblíbené položky aplikace Internet Explorer, které byly dříve synchronizovány prostřednictvím připojeného účtu Microsoft, se budou synchronizovat prostřednictvím služby Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Jak funguje interoperabilita roamingu Microsoft account a Azure AD Enterprise State Roaming?

V listopadu 2015 nebo novějšíverze systému Windows 10, Enterprise State Roaming je podporována pouze pro jeden účet najednou. Pokud se k Windows přihlásíte pomocí pracovního nebo školního účtu Azure AD, všechna data se budou synchronizovat přes Azure AD. Pokud se k Windows přihlásíte pomocí osobního účtu Microsoft, budou všechna data synchronizována prostřednictvím účtu Microsoft. Univerzální appdata se budou přemístímovat pouze pomocí primárního přihlašovacího účtu v zařízení a budou se přepínat pouze v případě, že licence aplikace je vlastněna primárním účtem. Univerzální appdata pro aplikace vlastněné sekundárníúčty nebudou synchronizovány.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Synchronizuje se nastavení pro účty Azure AD od více klientů?

Když je na stejném zařízení více účtů Azure AD z různých klientů Azure AD, musíte aktualizovat registr zařízení, abyste komunikovali se službou Azure Rights Management pro každého klienta Azure AD.  

1. Najděte identifikátor GUID pro každého klienta Azure AD. Otevřete portál Azure a vyberte klienta Azure AD. Identifikátor GUID pro klienta je na stráncehttps://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)Vlastnosti vybraného klienta ( , označené **Directory ID**. 
2. Po vytvoření identifikátoru GUID bude nutné přidat klíč registru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<identifikátor GUID klienta>**.
   Z klíče **GUID ID klienta** vytvořte novou hodnotu Multi-String (REG-MULTI-SZ) s názvem **AllowedRMSServerUrls**. Pro jeho data zadejte adresy URL distribučního bodu licencování ostatních klientů Azure, ke kterým zařízení přistupuje.
3. Adresy URL distribučních bodů licencování můžete najít spuštěním rutiny **Get-AadrmConfiguration** z modulu AADRM. Pokud se hodnoty **pro LicensingIntranetDistributionPointUrl** a **LicensingExtranetDistributionPointUrl** liší, zadejte obě hodnoty. Pokud jsou hodnoty stejné, zadejte hodnotu pouze jednou.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Jaké jsou možnosti nastavení roamingu pro stávající desktopové aplikace systému Windows?

Roaming funguje pouze pro univerzální aplikace pro Windows. Pro povolení roamingu v existující desktopové aplikaci systému Windows jsou k dispozici dvě možnosti:

* [Most plochy](https://aka.ms/desktopbridge) vám pomůže přenést stávající desktopové aplikace windows na univerzální platformu Windows. Odtud budou nutné minimální změny kódu, aby bylo možné využít využití datového roamingu aplikací Azure AD. Desktop Bridge poskytuje vašim aplikacím identitu aplikace, která je potřebná k povolení datového roamingu aplikací pro stávající aplikace klasické pracovní plochy.
* [Virtualizace uživatelského prostředí (UE-V)](https://technet.microsoft.com/library/dn458947.aspx) vám pomůže vytvořit vlastní šablonu nastavení pro stávající desktopové aplikace windows a povolit roaming pro aplikace Win32. Tato možnost nevyžaduje, aby vývojář aplikace změnil kód aplikace. UE-V je omezen na místní roaming služby Active Directory pro zákazníky, kteří si zakoupili sadu Microsoft Desktop Optimization Pack.

Správci mohou nakonfigurovat UE-V tak, aby přecvakoval data aplikací klasické pracovní plochy systému Windows změnou roamingu nastavení operačního systému Windows a univerzálních dat aplikací prostřednictvím [zásad skupiny UE-V](https://technet.microsoft.com/itpro/mdop/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), včetně:

* Zásady skupiny nastavení systému Windows
* Nesynchronizovat zásady skupiny aplikací systému Windows
* Roaming v aplikaci Internet Explorer v části Aplikace

V budoucnu může Microsoft prozkoumat způsoby, jak ue-V hluboce integrovat do Windows a rozšířit UE-V přecházet nastavení prostřednictvím cloudu Azure AD.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Můžu synchronizovaná nastavení a data ukládat místně?

Enterprise State Roaming ukládá všechna synchronizovaná data do cloudu Microsoftu. UE-V nabízí místní roamingové řešení.

## <a name="who-owns-the-data-thats-being-roamed"></a>Kdo vlastní data, která se toulají?

Podniky vlastní data roamingu prostřednictvím podnikového státního roamingu. Data se ukládají v datovém centru Azure. Všechna uživatelská data se šifrují při přenosu i v klidovém stavu v cloudu pomocí služby Azure Rights Management z Azure Information Protection. Jedná se o zlepšení ve srovnání se synchronizací nastavení na základě účtu Microsoft, která šifruje pouze určitá citlivá data, jako jsou pověření uživatele před tím, než opustí zařízení.

Společnost Microsoft se zavazuje chránit zákaznická data. Data nastavení podnikového uživatele je automaticky zašifrována službou Azure Rights Management před tím, než opustí zařízení s Windows 10, takže žádný jiný uživatel nemůže tato data číst. Pokud má vaše organizace placené předplatné služby Azure Rights Management, můžete použít další funkce ochrany, jako je sledování a odvolání dokumentů, automatická ochrana e-mailů obsahujících citlivé informace a správa vlastních klíčů vlastní klíč", také známý jako BYOK). Další informace o těchto funkcích a fungování této služby ochrany naleznete v tématu [Co je správa práv Azure](/azure/information-protection/what-is-information-protection).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Můžu spravovat synchronizaci pro konkrétní aplikaci nebo nastavení?

V systému Windows 10 neexistuje žádné nastavení MDM nebo zásad skupiny, které by zakázalo roaming pro jednotlivé aplikace. Správci klienta můžou zakázat synchronizaci dat aplikací pro všechny aplikace na spravovaném zařízení, ale neexistuje lepší ovládací prvek na úrovni aplikace nebo v rámci aplikace.

## <a name="how-can-i-enable-or-disable-roaming"></a>Jak mohu povolit nebo zakázat roaming?

V aplikaci **Nastavení** přejděte na**Synchronizace nastavení** **účtů** > . Na této stránce můžete zjistit, který účet se používá k přemísťování nastavení a můžete povolit nebo zakázat jednotlivé skupiny nastavení, které mají být přemísťovány.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Jaké je doporučení společnosti Microsoft pro povolení roamingu v systému Windows 10?

Společnost Microsoft má k dispozici několik různých roamingových řešení nastavení, včetně cestovních profilů uživatelů, UE-V a podnikového státního roamingu.  Společnost Microsoft se zavazuje investovat do podnikového státního roamingu v budoucích verzích systému Windows. Pokud vaše organizace není připravená nebo pohodlná s přesouváním dat do cloudu, doporučujeme použít UE-V jako primární roamingovou technologii. Pokud vaše organizace vyžaduje podporu roamingu pro stávající desktopové aplikace windows, ale touží přejít do cloudu, doporučujeme použít službu Enterprise State Roaming i UE-V. Přestože UE-V a Enterprise State Roaming jsou velmi podobné technologie, vzájemně se nevylučují. Vzájemně se doplňují, aby zajistily, že vaše organizace poskytuje roamingové služby, které uživatelé potřebují.  

Při použití služby Enterprise State Roaming i UE-V platí následující pravidla:

* Enterprise State Roaming je primární roamingový agent v zařízení. UE-V se používá k doplnění "Win32 mezeru."
* UE-V roaming pro nastavení Windows a moderní data aplikací UPW by měly být zakázány při použití zásad skupiny UE-V. Ty jsou již zahrnuty v podnikový státní roaming.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Jak podnikový státní roaming podporuje infrastrukturu virtuálních desktopů (VDI)?

Roaming stavu organizace je podporován v klientských počítačích SKU klienta Windows 10, ale ne na serverových su. Pokud je virtuální počítač klienta hostovaný v počítači hypervisoru a vzdáleně se přihlásíte k virtuálnímu počítači, vaše data se budou přemítat. Pokud více uživatelů sdílí stejný operační systém a uživatelé se vzdáleně přihlašují k serveru, aby mohli získat úplné prostředí plochy, roaming nemusí fungovat. Scénář založený na relaci není oficiálně podporován.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Co se stane, když si moje organizace zakoupí předplatné, které zahrnuje Azure Rights Management po použití roamingu?

Pokud vaše organizace už používá roaming ve Windows 10 s bezplatným předplatným azure rights management s omezeným použitím, nákup [placeného předplatného,](https://azure.microsoft.com/pricing/details/information-protection/) které zahrnuje službu ochrany Azure Rights Management, nebude mít žádný vliv na funkčnost funkce roamingu a správce IT nebude vyžadovat žádné změny konfigurace.

## <a name="known-issues"></a>Známé problémy

Seznam známých problémů naleznete v dokumentaci v části [řešení potíží.](enterprise-state-roaming-troubleshooting.md) 

## <a name="next-steps"></a>Další kroky 

Přehled najdete v tématu [Přehled podnikového státního roamingu](enterprise-state-roaming-overview.md)
