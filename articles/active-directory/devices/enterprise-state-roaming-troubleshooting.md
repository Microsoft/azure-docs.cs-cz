---
title: Poradce při potížích s roamingem podnikového stavu ve službě Azure Active Directory
description: Obsahuje odpovědi na některé otázky, které mohou mít správci IT ohledně nastavení a synchronizace dat aplikací.
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
ms.openlocfilehash: ae8ce24aeb665a7f99326e83dbe18d020e1b6196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672350"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Poradce při potížích s nastavením podnikového státního roamingu ve službě Azure Active Directory

Toto téma obsahuje informace o řešení a diagnostice problémů se službou Enterprise State Roaming a obsahuje seznam známých problémů.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> Tento článek se týká prohlížeče microsoft edge legacy založeného na HTML spuštěném v systému Windows 10 v červenci 2015. Tento článek se nevztahuje na nový prohlížeč založený na microsoft edge chromu vydaném 15. Další informace o chování synchronizace pro nový microsoft edge naleznete v článku [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="preliminary-steps-for-troubleshooting"></a>Předběžné kroky pro řešení potíží 

Před zahájením odstraňování potíží ověřte, zda byl uživatel a zařízení správně nakonfigurováno a zda zařízení a uživatel splnili všechny požadavky na službu Enterprise State Roaming. 

1. Windows 10, s nejnovějšími aktualizacemi a minimální verze 1511 (OS Build 10586 nebo novější) je nainstalován na zařízení. 
1. Zařízení je Azure AD připojen nebo hybridní Azure AD připojen. Další informace najdete [v tématu jak získat zařízení pod kontrolou Azure AD](overview.md).
1. Ujistěte se, že **enterprise state roaming** je povolen pro klienta ve službě Azure AD, jak je popsáno v [Chcete-li povolit enterprise state roaming](enterprise-state-roaming-enable.md). Roaming můžete povolit pro všechny uživatele nebo pouze pro vybranou skupinu uživatelů.
1. Uživateli je přiřazena licence Služby Azure Active Directory Premium.  
1. Zařízení musí být restartováno a uživatel se musí znovu přihlásit, aby mohl přistupovat k funkcím podnikového státního roamingu.

## <a name="information-to-include-when-you-need-help"></a>Informace, které byste měli zahrnout při žádosti o pomoc
Pokud nemůžete vyřešit svůj problém pomocí níže uvedených pokynů, můžete se obrátit na naše technické poradce. Když je kontaktujete, uveďte následující informace:

* **Obecný popis chyby**: Existují chybové zprávy, které uživatel zobrazuje? Pokud nebyla žádná chybová zpráva, popište neočekávané chování, které jste si všimli, podrobně. Jaké funkce jsou povoleny pro synchronizaci a co uživatel očekává synchronizaci? Jsou více funkcí není synchronizováno, nebo je izolováno do jedné?
* **Ovlivnění uživatelé** – Funguje nebo selhává synchronizace pro jednoho uživatele nebo více uživatelů? Kolik zařízení se týká na uživatele? Jsou všechny z nich není synchronizace, nebo jsou některé z nich synchronizace a některé nejsou synchronizovány?
* **Informace o uživateli** – jakou identitu uživatel používá k přihlášení k zařízení? Jak se uživatel přihlašuje k zařízení? Je jim povoleno synchronizovat součást vybrané skupiny zabezpečení? 
* **Informace o zařízení** – Je toto zařízení Azure AD připojen nebo domény připojen? Na jakém sestavení je zařízení? Jaké jsou nejnovější aktualizace?
* **Datum / Čas / Časové pásmo** - Jaké bylo přesné datum a čas, kdy jste viděli chybu (včetně časového pásma)?

Zahrnutí těchto informací nám pomáhá vyřešit váš problém co nejrychleji.

## <a name="troubleshooting-and-diagnosing-issues"></a>Řešení potíží a diagnostika problémů

Tato část obsahuje návrhy, jak řešit a diagnostikovat problémy související se státním roamingem Enterprise.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Ověření synchronizace a stránka nastavení Synchronizace nastavení 

1. Po připojení k počítači s Windows 10 k doméně, která je nakonfigurovaná tak, aby umožňovala podnikový státní roaming, se přihlaste pomocí pracovního účtu. Přejděte na **nastavení** > **účty** > **Synchronizovat nastavení** a potvrďte, že synchronizace a jednotlivá nastavení jsou zapnutá a že v horní části stránky nastavení je uvedeno, že synchronizujete se svým pracovním účtem. Zkontrolujte, zda se stejný účet používá také jako váš přihlašovací účet v **nastavení** > **účtů** > Vaše**informace**. 
1. Ověřte, zda synchronizace funguje ve více počítačích, provedením některých změn v původním počítači, například přesunutím hlavního panelu na pravou nebo horní stranu obrazovky. Sledujte, jak se změna rozšíří do druhého počítače během pěti minut. 

   * Zamykání a odemykání obrazovky (Win + L) může pomoci spustit synchronizaci.
   * Chcete-li, aby synchronizace fungovala, musíte se přihlašovat pomocí stejného účtu na obou počítačích – protože službu Enterprise State Roaming je vázána na uživatelský účet a ne na účet počítače.

**Potenciální problém**: Pokud ovládací prvky na stránce **Nastavení** nejsou k dispozici a zobrazí se zpráva "Některé funkce systému Windows jsou k dispozici pouze v případě, že používáte účet Microsoft nebo pracovní účet." Tento problém může nastat pro zařízení, která jsou nastavena na připojení k doméně a registrované do Azure AD, ale zařízení ještě není úspěšně ověřeno do Azure AD. Možnou příčinou je, že zásady zařízení musí být použity, ale tato aplikace probíhá asynchronně a může být zpožděna o několik hodin. 

### <a name="verify-the-device-registration-status"></a>Ověření stavu registrace zařízení

Enterprise State Roaming vyžaduje, aby se zařízení zaregistrovalo ve službě Azure AD. I když není specifické pro enterprise state roaming, podle níže uvedených pokynů můžete potvrdit, že klient Windows 10 je registrovaný, a potvrdit kryptografický otisk, adresu URL nastavení Azure AD, stav ngc a další informace.

1. Otevřete příkazový řádek bez oprávnění. Chcete-li to provést v systému Windows, otevřete spustit spouštěč (Win + R) a zadejte "cmd" otevřít.
1. Po otevření příkazového řádku zadejte "*dsregcmd.exe /status*".
1. Pro očekávaný výstup **azureadJoined** hodnota pole by měla být "ANO", **WamDefaultSet** hodnota pole by měla být "ANO" a hodnota pole **WamDefaultGUID** by měla být GUID s "(AzureAd)" na konci.

**Potenciální problém**: **WamDefaultSet** a **AzureAdJoined** mají v hodnotě pole "NE", zařízení bylo připojeno k doméně a registrováno ve službě Azure AD a zařízení se nesynchronizuje. Pokud se zobrazuje toto, zařízení může vyžadovat čekání na zásady, které mají být použity nebo ověřování pro zařízení se nezdařilo při připojování k Azure AD. Uživatel může čekat několik hodin pro zásady, které mají být použity. Další kroky řešení potíží mohou zahrnovat opakování automatické registrace odhlášením a zpět nebo spuštěním úlohy v Plánovači úloh. V některých případech může s tímto problémem pomoci spuštění programu *"dsregcmd.exe /leave*" v okně příkazového řádku se zvýšenými oprávněními, restartování a opakování pokusu o registraci.

**Potenciální problém**: Pole **pro SettingsUrl** je prázdné a zařízení se nesynchronizuje. Uživatel se k zařízení mohl naposledy přihlásit před povolením roamingu Enterprise State Roaming na portálu Azure Active Directory Portal. Restartujte zařízení a přihlaste se k uživateli. Volitelně můžete na portálu zkusit, aby správce IT přecšlákem na **Azure Active Directory** > **Devices** > **Enterprise State Roaming** zakázal a **uživatelé mohou synchronizovat nastavení a data aplikací mezi zařízeními**. Po opětovném povolení restartujte zařízení a přihlaste se k uživateli. Pokud se tím problém nevyřeší, **SettingsUrl** může být prázdný, pokud je chybný certifikát zařízení. V tomto případě může s tímto problémem pomoci spuštění programu*dsregcmd.exe /leave*v okně příkazového řádku se zvýšenými oprávněními, restartování mandatáku a pokus o registraci znovu.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Roaming podnikového stavu a vícefaktorové ověřování 

Za určitých podmínek může enterprise state roaming ujít synchronizaci dat, pokud je nakonfigurované vícefaktorové ověřování Azure. Další informace o těchto příznaky naleznete v dokumentu podpory [KB3193683](https://support.microsoft.com/kb/3193683). 

**Potenciální problém**: Pokud je vaše zařízení nakonfigurované tak, aby vyžadovalo vícefaktorové ověřování na portálu Azure Active Directory, může se vám při přihlašování k zařízení s Windows 10 pomocí hesla nepodaří synchronizovat nastavení. Tento typ konfigurace vícefaktorového ověřování je určen k ochraně účtu správce Azure. Uživatelé správce můžou pořád moct synchronizovat po přihlášení ke svým zařízením s Windows 10 pomocí pinu Microsoft Passport for Work nebo dokončením vícefaktorového ověřování při přístupu k dalším službám Azure, jako je Office 365.

**Potenciální problém**: Synchronizace může selhat, pokud správce nakonfiguruje zásady podmíněného přístupu vícefaktorového ověřování služby ADF AD AD AD a platnost přístupového tokenu na zařízení vyprší. Ujistěte se, že se přihlásíte a odhlásíte pomocí PIN kódu Microsoft Passport for Work nebo dokončíte vícefaktorové ověřování při přístupu k dalším službám Azure, jako je Office 365.

### <a name="event-viewer"></a>Prohlížeč událostí

Pro pokročilé řešení potíží lze prohlížeč událostí použít k vyhledání konkrétních chyb. Ty jsou popsány v následující tabulce. Události najdete v části Prohlížeč událostí > protokoly aplikací a služeb > **Microsoft** > **Windows** > **SettingSync-Azure** a pro problémy související s identitou se synchronizací **Microsoft** > **Windows** > **AAD**.

## <a name="known-issues"></a>Známé problémy

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>Synchronizace nefunguje na zařízeních, která mají aplikace načtené na straně pomocí softwaru MDM

Ovlivňuje zařízení se systémem Windows 10 Anniversary Update (verze 1607). V Prohlížeči událostí v protokolech SettingSync-Azure je často vidět ID události 6013 s chybou 80070259.

**Doporučená akce**  
Ujistěte se, že klient Windows 10 v1607 má kumulativní aktualizaci ze srpna 23, 2016[(KB3176934](https://support.microsoft.com/kb/3176934) OS Build 14393.82). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Oblíbené položky aplikace Internet Explorer se nesynchronizují

Ovlivňuje zařízení se systémem Windows 10 Listopadová aktualizace (verze 1511).

**Doporučená akce**  
Ujistěte se, že klient Windows 10 v1511 má v červenci 2016 kumulativní aktualizace[(KB3172985](https://support.microsoft.com/kb/3172985) Sestavení operačního systému 10586.494).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Motiv není synchronizován, stejně jako data chráněná ochranou informací systému Windows 

Aby se zabránilo úniku dat, data chráněná [ochranou informací systému Windows](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) se nebudou synchronizovat prostřednictvím podnikového státního roamingu pro zařízení používající aktualizaci Windows 10 Anniversary Update.

**Doporučená akce**  
Žádné. Budoucí aktualizace systému Windows mohou tento problém vyřešit.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>Nastavení data, času a oblasti se nesynchronizují na zařízení připojení k doméně 
  
Zařízení, která jsou připojena k doméně, nebudou mít za problém synchronizovat pro nastavení Datum, Čas a Oblast: automatický čas. Použití automatického času může přepsat jiné nastavení data, času a oblasti a způsobit, že tato nastavení nebudou synchronizována. 

**Doporučená akce**  
Žádné. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>Výzvy nástroje UAC při synchronizaci hesel

Ovlivňuje zařízení se systémem Windows 10 Listopadová aktualizace (verze 1511) s bezdrátovou síťovou konkretou, která je nakonfigurovaná pro synchronizaci hesel.

**Doporučená akce**  
Ujistěte se, že klient Windows 10 v1511 má kumulativní aktualizaci ([KB3140743](https://support.microsoft.com/kb/3140743) Sestavení operačního systému 10586.494).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>Synchronizace nefunguje na zařízeních, která používají čipovou kartu pro přihlášení

Pokud se pokusíte přihlásit k zařízení se systémem Windows pomocí čipové karty nebo virtuální čipové karty, synchronizace nastavení přestane fungovat.     

**Doporučená akce**  
Žádné. Budoucí aktualizace systému Windows mohou tento problém vyřešit.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>Zařízení připojená k doméně se po opuštění podnikové sítě nesynchronizuje     

Zařízení připojená k doméně zaregistrovaná ve službě Azure AD může zaznamenat selhání synchronizace, pokud je zařízení mimo pracoviště po delší dobu a ověřování domény nelze dokončit.

**Doporučená akce**  
Připojte zařízení k podnikové síti, aby synchronizace mohla pokračovat.

---

### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Zařízení Azure AD Joined se nesynchronizuje a uživatel má smíšené uživatelské hlavní jméno.

Pokud má uživatel název UPN smíšeného případu (například Uživatelské jméno místo uživatelského jména) a uživatel je na zařízení Azure AD Joined, které upgradovalo z Windows 10 Build 10586 na 14393, zařízení uživatele se nemusí synchronizovat. 

**Doporučená akce**  
Uživatel se bude muset odpojit a znovu připojit zařízení ke cloudu. Chcete-li to provést, přihlaste se jako uživatel místního správce a odpojte se od zařízení tak, že přejdete do **nastavení** > **systému** > **A** vyberte možnost "Spravovat nebo odpojit od práce nebo školy". Vyčistěte soubory níže a pak Azure AD Připojit zařízení znovu v **nastavení** > **systému** > **o a** výběrem "Připojit k práci nebo škole". Pokračujte v připojení zařízení ke službě Azure Active Directory a dokončete tok.

V kroku vyčištění vyčistěte následující soubory:
- Nastavení.dat v`C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Všechny soubory pod složkou`C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>ID události 6065: 80070533 Tento uživatel se nemůže přihlásit, protože tento účet je momentálně zakázán.  

V Prohlížeči událostí v protokolech SettingSync/Debug lze tuto chybu vidět, když vypršela platnost pověření uživatele. Kromě toho může dojít, když klient neměl automaticky zřízena AzureRMS. 

**Doporučená akce**  
V prvním případě mají uživatel aktualizovat své přihlašovací údaje a přihlásit se k zařízení s novými pověřeními. Chcete-li vyřešit problém AzureRMS, pokračujte kroky uvedené v [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>ID události 1098: Chyba: Operace zprostředkovatele tokenu 0xCAA5001C se nezdařila.  

V Prohlížeči událostí v protokolech AAD/Operational se tato chyba může projevit u události 1104: Volání pluginu AAD Cloud AP Get token vrácena chyba: 0xC000005F. K tomuto problému dochází, pokud chybí oprávnění nebo atributy vlastnictví.  

**Doporučená akce**  
Pokračujte v uvedených krocích [KB3196528](https://support.microsoft.com/kb/3196528).  

## <a name="next-steps"></a>Další kroky

Přehled najdete v tématu [Přehled podnikového státního roamingu](enterprise-state-roaming-overview.md).
