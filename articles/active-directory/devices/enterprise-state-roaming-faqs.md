---
title: Nejčastější dotazy k Enterprise State Roaming – Azure Active Directory
description: Nejčastější dotazy k ESR
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
ms.openlocfilehash: cb889298a09c30a629c69442ebf31bc735af31d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96173120"
---
# <a name="settings-and-data-roaming-faq"></a>Nejčastější dotazy k nastavení a datovému roamingu

Tento článek obsahuje odpovědi na některé otázky, které správci IT můžou narazit na nastavení a synchronizaci dat aplikace.

## <a name="what-data-roams"></a>K čemu se data roamingují?

**Nastavení systému Windows**: nastavení počítače, která jsou součástí operačního systému Windows. Obecně platí, že se jedná o nastavení, která přizpůsobují počítač a obsahují následující široké kategorie:

* *Motiv* obsahující funkce jako motiv plochy a nastavení hlavního panelu
* *Nastavení Internet Exploreru* včetně nedávno otevřených karet a oblíbených položek
* *Nastavení prohlížeče Microsoft Edge*, například oblíbené položky a seznam pro čtení.
* *Hesla*, včetně internetových hesel, profilů Wi-Fi a dalších.
* *Jazykové předvolby* obsahující nastavení pro rozložení klávesnice, jazyk systému, datum a čas a další
* *Funkce usnadnění přístupu*, například motiv s vysokým kontrastem, Předčítání a Lupa
* *Další nastavení Windows*, například nastavení myši

> [!NOTE]
> Tento článek se týká starší verze prohlížeče založeného na HTML v Microsoft Edge, který se spouští s Windows 10 v červenci 2015. Článek neplatí pro nový prohlížeč založený na Microsoft Edge chrom vydaný 15. ledna 2020. Další informace o chování synchronizace pro nové Microsoft Edge najdete v článku o [synchronizaci Microsoft Edge](/deployedge/microsoft-edge-enterprise-sync).

**Data aplikací**: univerzální aplikace pro Windows můžou zapisovat data nastavení do složky roamingu a veškerá data zapsaná do této složky se automaticky synchronizují. Pro návrh aplikace, aby tuto možnost využila, je k dispozici vývojářům jednotlivých aplikací. Další informace o tom, jak vyvíjet univerzální aplikaci pro Windows, která používá roaming, najdete na blogu věnovaném [rozhraní API úložiště aplikačních](/windows/uwp/design/app-settings/store-and-retrieve-app-data) údajů a [blogu vývojářům pro roaming pro Windows 8](https://blogs.windows.com/windowsdeveloper/2016/05/04/roaming-app-data-and-the-user-experience/).

## <a name="what-account-is-used-for-settings-sync"></a>Jaký účet se má použít k synchronizaci nastavení?

V Windows 8.1 nastavení synchronizace vždycky používala uživatelské účty Microsoft. Podnikoví uživatelé mají možnost připojit účet Microsoft k účtu domény služby Active Directory a získat tak přístup k nastavení synchronizace. V systému Windows 10 je tato funkce připojená účet Microsoft nahrazena primárním a sekundárním účetním rozhraním.

Primární účet je definován jako účet použitý k přihlášení do systému Windows. Může to být účet Microsoft, účet Azure Active Directory (Azure AD), místní účet Active Directory nebo místní účet. Kromě primárního účtu můžou uživatelé s Windows 10 přidávat do zařízení jeden nebo víc sekundárních cloudových účtů. Sekundárním účtem je obecně účet Microsoft, účet služby Azure AD nebo jiný účet, například Gmail nebo Facebook. Tyto sekundární účty poskytují přístup k dalším službám, jako je jednotné přihlašování a Windows Store, ale nejsou schopné synchronizovat nastavení.

Ve Windows 10 se dá použít jenom primární účet pro zařízení k synchronizaci nastavení (viz téma [návody upgrade z účet Microsoft synchronizace nastavení ve Windows 8 s Azure AD ve Windows 10?](enterprise-state-roaming-faqs.md#how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10)).

Data nejsou nikdy smíšená mezi různými uživatelskými účty v zařízení. Existují dvě pravidla synchronizace nastavení:

* Nastavení systému Windows bude vždy cestovní s primárním účtem.
* Data aplikace budou označená pomocí účtu, který se používá k získání aplikace. Synchronizovat se budou jenom aplikace označené primárním účtem. Označování vlastnictví aplikací se určuje, když se aplikace načte do Windows Storu nebo ze správy mobilních zařízení (MDM).

Pokud vlastníka aplikace nelze identifikovat, bude přecházet do roamingu s primárním účtem. Pokud je zařízení upgradované z Windows 8 nebo Windows 8.1 na Windows 10, budou všechny aplikace označené účet Microsoft, které získá. Důvodem je to, že většina uživatelů získává aplikace prostřednictvím Windows Storu a pro účty Azure AD starší než Windows 10 ještě nepodporovala Windows Store. Pokud je aplikace nainstalovaná prostřednictvím offline licence, aplikace se označí pomocí primárního účtu v zařízení.

> [!NOTE]
> Zařízení s Windows 10, která jsou vlastněná podnikem a jsou připojená ke službě Azure AD, už nemohou připojit své účty Microsoft k účtu domény. Možnost připojit účet Microsoft k účtu domény a nechat synchronizaci dat všech uživatelů s účet Microsoft (to znamená, že účet Microsoft roaming prostřednictvím připojených účet Microsoft a funkce Active Directory) se odebere ze zařízení s Windows 10, která jsou připojená k připojenému prostředí Active Directory nebo Azure AD.

## <a name="how-do-i-upgrade-from-microsoft-account-settings-sync-in-windows-8-to-azure-ad-settings-sync-in-windows-10"></a>Návody upgradovat z nastavení účet Microsoft synchronizace ve Windows 8 do Azure AD nastavení synchronizace ve Windows 10?

Pokud jste připojeni k doméně služby Active Directory běžící Windows 8.1 s připojeným účet Microsoft, budete pomocí účet Microsoft synchronizovat nastavení. Po upgradu na Windows 10 budete pokračovat v synchronizaci uživatelských nastavení prostřednictvím účet Microsoft, pokud jste uživatelem připojeným k doméně a doména služby Active Directory se nepřipojí k Azure AD.

Pokud se místní doména služby Active Directory připojí k Azure AD, zařízení se pokusí synchronizovat nastavení pomocí připojeného účtu služby Azure AD. Pokud správce Azure AD nepovolí Enterprise State Roaming, připojený účet služby Azure AD zastaví synchronizaci nastavení. Pokud jste uživatel s Windows 10 a přihlásíte se pomocí identity Azure AD, začnete synchronizovat nastavení Windows, jakmile správce povolí synchronizaci nastavení přes Azure AD.

Pokud jste v podnikovém zařízení uložili nějaké osobní údaje, měli byste si uvědomit, že se operační systém Windows a data aplikací začnou synchronizovat do Azure AD. To má následující důsledky:

* Vaše osobní nastavení účet Microsoft přijde od nastavení vašich pracovních nebo školních účtů Azure AD. Důvodem je to, že synchronizace nastavení účet Microsoft a Azure AD teď používá samostatné účty.
* Osobní údaje, například Wi-Fi hesla, Webová pověření a oblíbené položky aplikace Internet Explorer, které byly dříve synchronizovány prostřednictvím připojeného účet Microsoft, budou synchronizovány prostřednictvím služby Azure AD.

## <a name="how-do-microsoft-account-and-azure-ad-enterprise-state-roaming-interoperability-work"></a>Jak účet Microsoft a spolupráce Enterprise State Roaming služby Azure AD funguje?

V listopadu 2015 nebo novějších verzích Windows 10 se Enterprise State Roaming podporuje jenom pro jediný účet. Pokud se k Windows přihlašujete pomocí pracovního nebo školního účtu Azure AD, budou se všechna data synchronizovat přes Azure AD. Pokud se přihlásíte k systému Windows pomocí osobního účet Microsoft, budou všechna data synchronizována prostřednictvím účet Microsoft. Univerzální data budou roamingem jenom pomocí primárního přihlašovacího účtu v zařízení a přenese se do roamingu jenom v případě, že licence aplikace patří k primárnímu účtu. Univerzální služby pro aplikace vlastněné libovolnými sekundárními účty nebudou synchronizovány.

## <a name="do-settings-sync-for-azure-ad-accounts-from-multiple-tenants"></a>Synchronizují se nastavení pro účty Azure AD z více tenantů?

Pokud jsou na stejném zařízení víc účtů Azure AD z různých tenantů Azure AD, musíte aktualizovat registr zařízení, aby komunikoval se službou Azure Rights Management pro každého tenanta Azure AD.  

1. Vyhledejte identifikátor GUID pro každého tenanta Azure AD. Otevřete Azure Portal a vyberte tenanta Azure AD. Identifikátor GUID pro tenanta se nachází na stránce vlastností vybraného tenanta ( https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties) s popiskem **ID adresáře**. 
2. Po dokončení identifikátoru GUID budete muset přidat klíč registru **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\SettingSync\WinMSIPC\<tenant ID GUID>**.
   Z klíče **GUID ID tenanta** vytvořte novou hodnotu s více řetězci (reg-multi-SZ) s názvem **AllowedRMSServerUrls**. Pro svá data zadejte adresy URL distribučního bodu pro licencování ostatních tenantů Azure, ke kterým zařízení přistupuje.
3. Adresy URL distribučního bodu licencování můžete najít spuštěním rutiny **Get-AadrmConfiguration** z modulu aadrm. Pokud se hodnoty pro **LicensingIntranetDistributionPointUrl** a **LicensingExtranetDistributionPointUrl** liší, zadejte obě hodnoty. Pokud jsou hodnoty stejné, zadejte hodnotu pouze jednou.

## <a name="what-are-the-roaming-settings-options-for-existing-windows-desktop-applications"></a>Jaké jsou možnosti nastavení roamingu pro existující desktopové aplikace Windows?

Roaming funguje jenom pro univerzální aplikace pro Windows. K dispozici jsou dvě možnosti, jak povolit roaming ve stávající desktopové aplikaci pro Windows:

* [Most pro stolní počítače](/windows/msix/desktop/source-code-overview) vám pomůže přenést stávající desktopové aplikace Windows do Univerzální platforma Windows. Z tohoto místa se budou vyžadovat minimální změny kódu, aby bylo možné využívat roaming dat aplikací Azure AD. Most pro stolní počítače poskytuje vašim aplikacím identitu aplikace, která je nutná k povolení roamingu dat aplikací pro existující aplikace klasické pracovní plochy.
* [Virtualizace uživatelského prostředí (UE-V)](/previous-versions//dn458947(v=vs.85)) vám pomůže vytvořit vlastní šablonu nastavení pro stávající desktopové aplikace pro Windows a povolit roaming pro aplikace Win32. Tato možnost nevyžaduje, aby vývojář aplikace změnil kód aplikace. Pro zákazníky, kteří si zakoupili sadu Microsoft Desktop Optimization Pack, je UE-V omezený na místní roaming služby Active Directory.

Správci můžou nakonfigurovat vícefaktorové technologie pro roaming dat aplikací klasické pracovní plochy Windows změnou roamingu nastavení operačního systému Windows a univerzálních aplikačních dat pomocí [zásad skupiny s podporou UE-v](/microsoft-desktop-optimization-pack/uev-v2/configuring-ue-v-2x-with-group-policy-objects-both-uevv2), včetně těchto:

* Nastavení roamingu zásady skupiny pro Windows
* Nesynchronizovat zásady skupiny aplikací pro Windows
* Roaming aplikace Internet Explorer v části aplikace

V budoucnu může Microsoft prozkoumat způsoby, jak provést integraci s jednotným UE-V hluboko do Windows a jak v cloudu Azure AD rozšíří nastavení do roamingu.

## <a name="can-i-store-synced-settings-and-data-on-premises"></a>Můžu ukládat synchronizovaná nastavení a data místně?

Enterprise State Roaming ukládá všechna synchronizovaná data v cloudu Microsoftu. UE-V nabízí místní řešení roamingu.

## <a name="who-owns-the-data-thats-being-roamed"></a>Kdo je vlastníkem dat, která jsou roamingu?

Podniky vlastní data roamingovaná prostřednictvím Enterprise State Roaming. Data jsou uložena v datovém centru Azure. Všechna uživatelská data jsou šifrovaná i v klidovém prostředí v cloudu pomocí služby Azure Rights Management z Azure Information Protection. Jedná se o vylepšení v porovnání s účet Microsoft synchronizace nastavení, která šifruje jenom určitá citlivá data, jako jsou třeba přihlašovací údaje uživatele dřív, než zařízení opustí.

Společnost Microsoft se zavazuje chránit zákaznická data. Data nastavení podnikového uživatele se před tím, než opustí zařízení s Windows 10, automaticky šifrují službou Azure Rights Management, takže tato data nikdo jiný uživatel nedokáže číst. Pokud má vaše organizace placené předplatné služby Azure Rights Management, můžete použít další funkce ochrany, například sledovat a odvolat dokumenty, automaticky chránit e-maily, které obsahují citlivé údaje, a spravovat vlastní klíče (tzv. "Přineste si vlastní klíč", označované také jako BYOK). Další informace o těchto funkcích a o tom, jak tato služba ochrany funguje, najdete v tématu [co je Azure Rights Management](/azure/information-protection/what-is-information-protection).

## <a name="can-i-manage-sync-for-a-specific-app-or-setting"></a>Můžu spravovat synchronizaci pro konkrétní aplikaci nebo nastavení?

Ve Windows 10 neexistuje nastavení MDM ani Zásady skupiny pro zákaz roamingu pro jednotlivé aplikace. Správci klientů můžou zakázat synchronizaci aplikačních kódů pro všechny aplikace na spravovaném zařízení, ale neexistují žádné jemnější řízení na úrovni jednotlivých aplikací nebo v rámci aplikace.

## <a name="how-can-i-enable-or-disable-roaming"></a>Jak můžu povolit nebo zakázat roaming?

V aplikaci **Nastavení** pokračujte na **účty**  >  **synchronizovat nastavení**. Na této stránce můžete zjistit, který účet se používá k nastavení roamingu, a můžete povolit nebo zakázat jednotlivé skupiny nastavení, které se mají přenášet do roamingu.

## <a name="what-is-microsofts-recommendation-for-enabling-roaming-in-windows-10"></a>Co je doporučení Microsoftu pro povolení roamingu ve Windows 10?

Microsoft má několik různých nastavení, která jsou k dispozici pro roaming, včetně cestovních profilů uživatelů, UE-V a Enterprise State Roaming.  Společnost Microsoft se zavazuje, že v budoucích verzích Windows investice do Enterprise State Roaming. Pokud vaše organizace není připravena ani se nemůžete přemísťovat data do cloudu, doporučujeme, abyste jako primární cestovní technologii používali r-V. Pokud vaše organizace vyžaduje podporu roamingu pro stávající desktopové aplikace Windows, ale Eager se přesune do cloudu, doporučujeme použít jak Enterprise State Roaming, tak i UE-V. I když jsou V Enterprise State Roaming velmi podobné technologie, jsou i V stejné jako technologie, se vzájemně nevylučují. Vzájemně doplňují informace, které vám pomůžou zajistit, aby vaše organizace poskytovala cestovní služby, které potřebují vaši uživatelé.  

Při použití Enterprise State Roaming a UE-V platí následující pravidla:

* Enterprise State Roaming je primárním agentem roamingu v zařízení. UE-V se používá k doplnění "mezery Win32".
* Při použití zásad skupiny UE-V by měl být zakázán roaming UE-V pro nastavení systému Windows a moderní data aplikace UWP. Tyto jsou již pokryty Enterprise State Roaming.

## <a name="how-does-enterprise-state-roaming-support-virtual-desktop-infrastructure-vdi"></a>Jak Enterprise State Roaming podporuje infrastrukturu virtuálních klientských počítačů (VDI)?

Služba Enterprise State Roaming je podporovaná na SKU klientů Windows 10, ale ne na SKU serveru. Pokud je klientský virtuální počítač hostovaný na počítači hypervisoru a vzdáleně se přihlašujete k virtuálnímu počítači, vaše data se budou cestovat. Pokud má více uživatelů stejný operační systém a uživatelé se vzdáleně přihlásí k serveru, aby mohli používat kompletní desktopové prostředí, nemusí roaming fungovat. Druhý scénář založený na relaci není oficiálně podporován.

## <a name="what-happens-when-my-organization-purchases-a-subscription-that-includes-azure-rights-management-after-using-roaming"></a>Co se stane, když moje organizace koupí předplatné, které zahrnuje Azure Rights Management po použití roamingu?

Pokud vaše organizace už používá roaming ve Windows 10 s bezplatným předplatným Azure Rights Management s omezeným použitím, zakoupení [placeného předplatného](https://azure.microsoft.com/pricing/details/information-protection/) , které zahrnuje službu Azure Rights Management Protection, nebude mít žádný vliv na funkčnost funkce roamingu a správce IT nebude vyžadovat žádné změny konfigurace.

## <a name="known-issues"></a>Známé problémy

Seznam známých problémů naleznete v dokumentaci v části [věnované řešení potíží](enterprise-state-roaming-troubleshooting.md) . 

## <a name="next-steps"></a>Další kroky 

Přehled najdete v tématu [Přehled služby Enterprise State Roaming](enterprise-state-roaming-overview.md) .