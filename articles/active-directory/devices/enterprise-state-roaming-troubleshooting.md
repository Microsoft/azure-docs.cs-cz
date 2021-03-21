---
title: Řešení potíží s Enterprise State Roaming v Azure Active Directory
description: Poskytuje odpovědi na některé otázky, které správci IT můžou narazit na nastavení a synchronizaci dat aplikací.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: tanning
ms.collection: M365-identity-device-management
ms.openlocfilehash: df70891ef090d44769aadbc235273e3193bc780e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94837205"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Řešení potíží s nastavením Enterprise State Roaming v Azure Active Directory

Toto téma poskytuje informace o tom, jak řešit a diagnostikovat problémy s Enterprise State Roaming a poskytuje seznam známých problémů.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> Tento článek se týká starší verze prohlížeče založeného na HTML v Microsoft Edge, který se spouští s Windows 10 v červenci 2015. Článek neplatí pro nový prohlížeč založený na Microsoft Edge chrom vydaný 15. ledna 2020. Další informace o chování synchronizace pro nové Microsoft Edge najdete v článku o [synchronizaci Microsoft Edge](/deployedge/microsoft-edge-enterprise-sync).

## <a name="preliminary-steps-for-troubleshooting"></a>Předběžné kroky pro řešení potíží 

Před zahájením odstraňování potíží ověřte, zda byl uživatel a zařízení správně nakonfigurován a zda zařízení a uživatel mají splněné všechny požadavky Enterprise State Roaming. 

1. V zařízení je nainstalován systém Windows 10 s nejnovějšími aktualizacemi a minimální verzí 1511 (Build operačního systému 10586 nebo novější). 
1. Zařízení je připojené k Azure AD nebo je připojené k hybridní službě Azure AD. Další informace najdete v tématu [Jak získat zařízení pod kontrolou služby Azure AD](overview.md).
1. Zajistěte, aby byl pro tenanta ve službě Azure AD povolený **Enterprise State Roaming** , jak je popsáno v tématu [povolení Enterprise State Roaming](enterprise-state-roaming-enable.md). Můžete povolit roaming pro všechny uživatele nebo jenom pro vybranou skupinu uživatelů.
1. Uživateli je přiřazena licence Azure Active Directory Premium.  
1. Zařízení se musí restartovat a uživatel se musí znovu přihlásit, aby mohl získat přístup k funkcím Enterprise State Roaming.

## <a name="information-to-include-when-you-need-help"></a>Informace, které byste měli zahrnout při žádosti o pomoc
Pokud problém nemůžete vyřešit pomocí následujících pokynů, můžete se obrátit na naše pracovníky podpory. Při kontaktování těchto informací zahrňte následující informace:

* **Obecný popis chyby**: jsou k dispozici chybové zprávy, které uživatel uvidí? Pokud se neobjevila žádná chybová zpráva, popište neočekávané chování, které jste si poznamenali podrobněji. Jaké funkce jsou povolené pro synchronizaci a jaký je uživatel, který očekává synchronizaci? Je více funkcí nesynchronizovaných nebo se nejedná o izolaci?
* **Ovlivnění uživatelé** – jedná se o synchronizaci práce/neúspěch pro jednoho uživatele nebo více uživatelů? Kolik zařízení je součástí jednotlivých uživatelů? Nesynchronizují se ani některá z nich se synchronizují a některé z nich se nesynchronizují?
* **Informace o uživateli** – jakou identitu uživatel používá k přihlášení k zařízení? Jak se uživatel přihlašuje k zařízení? Jsou součástí vybrané skupiny zabezpečení, která je povolena k synchronizaci? 
* **Informace o zařízení** – je připojené ke službě Azure AD nebo připojen k doméně? K jakému sestavení je zařízení? Jaké jsou nejnovější aktualizace?
* **Datum/čas/časové pásmo** – jaké bylo přesné datum a čas, kdy jste si chybu viděli (včetně časového pásma)?

Včetně těchto informací nám pomáhá co nejrychleji vyřešit váš problém.

## <a name="troubleshooting-and-diagnosing-issues"></a>Řešení potíží a diagnostika problémů

V této části jsou podrobnější informace o tom, jak řešit problémy související s Enterprise State Roaming a diagnostikovat problémy.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Ověřte synchronizaci a stránku nastavení synchronizovat nastavení. 

1. Po připojení počítače s Windows 10 k doméně, která je nakonfigurovaná tak, aby povolovala Enterprise State Roaming, se přihlaste pomocí svého pracovního účtu. V části **Nastavení**  >  **účty**  >  **synchronizujte nastavení** a potvrďte, že je zapnutá synchronizace a individuální nastavení. v horní části stránky nastavení znamená, že synchronizujete se svým pracovním účtem. Ověřte, že se stejný účet používá taky jako přihlašovací účet v **Nastavení**  >  **účty**  >  **vaše informace**. 
1. Ověřte, že synchronizace funguje na několika počítačích, a to provedením některých změn v původním počítači, jako je například přesunutí hlavního panelu na pravou nebo horní stranu obrazovky. Sledujte změnu, která se šíří na druhý počítač během pěti minut. 

   * Uzamčení a odemknutí obrazovky (Win + L) může přispět k aktivaci synchronizace.
   * Aby bylo možné synchronizovat práci, musíte se přihlásit pomocí stejného účtu na obou počítačích, protože Enterprise State Roaming je svázán s uživatelským účtem, a ne s účtem počítače.

**Potenciální problém**: Pokud ovládací prvky na stránce **Nastavení** nejsou k dispozici a zobrazí se zpráva "některé funkce systému Windows jsou k dispozici pouze v případě, že používáte účet Microsoft nebo pracovní účet". K tomuto problému může dojít u zařízení, která jsou nastavená tak, aby byla připojená k doméně a zaregistrovaná ve službě Azure AD, ale zařízení se ještě neúspěšně neověřilo do Azure AD. Možnou příčinou je, že je nutné použít zásady zařízení, ale tato aplikace proběhne asynchronně a může být odložena o několik hodin. 

### <a name="verify-the-device-registration-status"></a>Ověření stavu registrace zařízení

Enterprise State Roaming vyžaduje, aby zařízení bylo zaregistrované ve službě Azure AD. I když nejsou specifické pro Enterprise State Roaming, podle pokynů níže může pomáhat ověřit, jestli je klient Windows 10 zaregistrovaný, a potvrdit jeho kryptografický otisk, adresu URL nastavení Azure AD, stav NGC a další informace.

1. Otevřete příkazový řádek se zvýšenými oprávněními. Chcete-li to provést v systému Windows, otevřete spouštěcí program (Win + R) a zadejte příkaz "cmd", který chcete otevřít.
1. Po otevření příkazového řádku zadejte "*dsregcmd.exe/status*".
1. Pro očekávaný výstup by měla být hodnota pole **AzureAdJoined** "Yes", hodnota pole **WamDefaultSet** by měla být "Yes" a hodnota pole **WamDefaultGUID** by měla být identifikátorem GUID s "(AzureAd)" na konci.

**Možný problém**: **WamDefaultSet** a **AzureAdJoined** mají v hodnotě pole hodnotu Ne, zařízení bylo připojeno k doméně a zaregistrováno ve službě Azure AD a zařízení se nesynchronizuje. Pokud se to zobrazuje, může být nutné, aby zařízení čekalo na použití zásad nebo selhalo ověřování pro zařízení při připojování ke službě Azure AD. Aby bylo možné použít zásady, může uživatel počkat několik hodin. Další kroky při řešení potíží můžou zahrnovat opakování autoregistrace tím, že se odhlásí a znovu odhlásí nebo spustí úkol v Plánovač úloh. V některých případech se při použití příkazu "*dsregcmd.exe/Leave*" v okně příkazového řádku se zvýšenými oprávněními, restartování a pokus o registraci může pomáhat s tímto problémem.

**Potenciální problém**: pole pro **SettingsUrl** je prázdné a zařízení se nesynchronizuje. Uživatel se možná naposledy přihlásil k zařízení před tím, než se Enterprise State Roaming povolil na portálu Azure Active Directory. Restartujte zařízení a přihlaste se uživateli. Volitelně můžete na portálu povolit, aby správce IT přešel na **Azure Active Directory**  >  **zařízení**  >  **Enterprise State Roaming** zakažte a znovu povolit **uživatele můžou synchronizovat nastavení a data aplikací napříč zařízeními**. Po opětovném povolení restartujte zařízení a přihlaste se uživateli. Pokud se tím problém nevyřeší, **SettingsUrl** může být prázdný, pokud je k dispozici špatný certifikát zařízení. V takovém případě se v okně příkazového řádku se zvýšenými oprávněními spustí "*dsregcmd.exe/Leave*", restartování a pokus o registraci může pomáhat s tímto problémem.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Enterprise State Roaming a Multi-Factor Authentication 

Za určitých podmínek může Enterprise State Roaming selhat při synchronizaci dat, pokud je nakonfigurovaná Multi-Factor Authentication Azure AD. Další informace o těchto symptomech najdete v dokumentu podpory [KB3193683](https://support.microsoft.com/kb/3193683). 

**Potenciální problém**: Pokud je vaše zařízení nakonfigurované tak, aby vyžadovalo Multi-Factor Authentication na portále Azure Active Directory, nemůžete při přihlašování k zařízení s Windows 10 pomocí hesla nakonfigurovat jeho nastavení. Tento typ konfigurace Multi-Factor Authentication slouží k ochraně účtu správce Azure. Uživatelé s rolí správce můžou pořád synchronizovat přihlášením ke svým zařízením s Windows 10 a jejich Microsoft Passport for Work kódem PIN nebo doplněním Multi-Factor Authentication při přístupu k jiným službám Azure, jako je Microsoft 365.

**Možný problém**: synchronizace může selhat, pokud správce nakonfiguruje Active Directory Federation Services (AD FS) zásady podmíněného přístupu Multi-Factor Authentication a platnost přístupového tokenu v zařízení vyprší. Ujistěte se, že jste se přihlásili a odhlásili pomocí Microsoft Passport for Work PIN nebo dokončete Multi-Factor Authentication při přístupu k jiným službám Azure, jako je Microsoft 365.

### <a name="event-viewer"></a>Prohlížeč událostí

Pro řešení potíží s pokročilými chybami se Prohlížeč událostí dá použít k vyhledání konkrétních chyb. Ty jsou popsány v následující tabulce. Události najdete v části Prohlížeč událostí > **aplikace a služby protokoly**  >  **Microsoft**  >  **Windows**  >  **SettingSync – Azure** a problémy související s identitou s **protokoly synchronizovat aplikace a služby**  >  **Microsoft**  >  **Windows**  >  **AAD**.

## <a name="known-issues"></a>Známé problémy

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>Synchronizace nefunguje na zařízeních, která mají na straně aplikace nahraná pomocí softwaru MDM.

Má vliv na zařízení s aktualizací Windows 10 pro výročí (verze 1607). V Prohlížeč událostí v protokolech SettingSync-Azure se často zobrazuje ID události 6013 s chybou 80070259.

**Doporučená akce**  
Ujistěte se, že klient Windows 10 v1607 má 23. srpna 2016 kumulativní aktualizaci ([KB3176934](https://support.microsoft.com/kb/3176934) OS Build 14393,82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Oblíbené položky aplikace Internet Explorer se nesynchronizují

Má vliv na zařízení s Windows 10 listopad Update (verze 1511).

**Doporučená akce**  
Ujistěte se, že má klient Windows 10 v1511 kumulativní aktualizaci z července 2016 ([KB3172985](https://support.microsoft.com/kb/3172985) OS build 10586,494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Motiv se nesynchronizuje a data chráněná pomocí Windows Information Protection 

Aby nedošlo k úniku dat, data, která jsou chráněná pomocí [windows Information Protection](/windows/security/information-protection/windows-information-protection/protect-enterprise-data-using-wip) , se nesynchronizují prostřednictvím Enterprise State Roaming pro zařízení s aktualizací Windows 10 pro výročí.

**Doporučená akce**  
Žádné Tyto potíže mohou vyřešit budoucí aktualizace systému Windows.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>Nastavení data a času a oblasti se na zařízení připojeném k doméně nesynchronizují 
  
Zařízení, která jsou připojená k doméně, nebudou mít k synchronizaci pro datum a čas nastavení a oblast: Automatický čas. Automatické použití času může přepsat jiné nastavení data, času a oblasti a způsobit, že se tato nastavení nesynchronizují. 

**Doporučená akce**  
Žádné 

---

### <a name="uac-prompts-when-syncing-passwords"></a>Nástroj Řízení uživatelských účtů vyzve při synchronizaci hesel

Ovlivňuje zařízení s Windows 10 listopad Update (verze 1511) pomocí bezdrátové síťové karty, která je nakonfigurovaná pro synchronizaci hesel.

**Doporučená akce**  
Ujistěte se, že klient Windows 10 v1511 má kumulativní aktualizaci ([KB3140743](https://support.microsoft.com/kb/3140743) OS build 10586,494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>Synchronizace nefunguje na zařízeních, která používají čipovou kartu pro přihlášení.

Pokud se pokusíte přihlásit k zařízení s Windows pomocí čipové karty nebo virtuální čipové karty, bude synchronizace nastavení přestala fungovat.     

**Doporučená akce**  
Žádné Tyto potíže mohou vyřešit budoucí aktualizace systému Windows.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>Zařízení připojené k doméně se po ukončení podnikové sítě nesynchronizuje.     

Zařízení připojená k doméně zaregistrovaná ve službě Azure AD se můžou setkat s selháním synchronizace, pokud je zařízení v delší době mimo pracoviště a ověřování domény nejde dokončit.

**Doporučená akce**  
Připojte zařízení k podnikové síti, aby synchronizace mohla pokračovat.

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Zařízení připojené k Azure AD se nesynchronizuje a uživatel má smíšený hlavní název uživatele případu.

Pokud má uživatel smíšený hlavní název uživatele (například uživatelské jméno namísto uživatelského jména) a uživatel se nachází na zařízení připojeném k Azure AD, které se upgraduje z Windows 10 build 10586 na 14393, může se stát, že se zařízení uživatele nepodaří synchronizovat. 

**Doporučená akce**  
Uživatel bude muset zařízení odpojovat a znovu připojit ke cloudu. Provedete to tak, že se přihlásíte jako uživatel místního správce a odpojíte zařízení tak, že v části **Nastavení**  >  **systému**  >   zvolíte možnost spravovat nebo odpojit od práce nebo školy. Níže tyto soubory vyčistěte a pak Azure AD připojte zařízení znovu v systému **Nastavení**  >    >   a vyberte připojit k práci nebo školu. Pokračujte tím, že se připojíte k zařízení Azure Active Directory a dokončíte tok.

V kroku vyčištění vyčistěte následující soubory:
- Settings. dat v `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Všechny soubory ve složce `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>ID události 6065:80070533 tento uživatel se nemůže přihlásit, protože tento účet je aktuálně zakázaný.  

V Prohlížeč událostí v protokolech SettingSync/Debug se tato chyba může zobrazit, když vypršela platnost přihlašovacích údajů uživatele. Navíc k tomu může dojít, když tenant neAzureRMSo automaticky zřízené. 

**Doporučená akce**  
V prvním případě uživatel aktualizuje své přihlašovací údaje a přihlaste se k zařízení pomocí nových přihlašovacích údajů. Chcete-li vyřešit problém AzureRMS, postupujte podle kroků uvedených v části [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>ID události 1098: Chyba: operace zprostředkovatele tokenů 0xCAA5001C se nezdařila.  

V Prohlížeč událostí v protokolech AAD/provozní se tato chyba může zobrazit v události 1104: volání metody Get tokenu plug-in cloudového bodu AAD vrátilo chybu: 0xC000005F. K tomuto problému dochází, pokud chybí oprávnění nebo atributy vlastnictví.  

**Doporučená akce**  
Pokračujte podle kroků uvedených v [KB3196528](https://support.microsoft.com/kb/3196528).  

## <a name="next-steps"></a>Další kroky

Přehled najdete v tématu [Přehled služby Enterprise State Roaming](enterprise-state-roaming-overview.md).