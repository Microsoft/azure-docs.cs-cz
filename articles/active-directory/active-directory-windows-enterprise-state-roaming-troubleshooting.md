---
title: Řešení potíží s Enterprise State Roaming nastavení ve službě Azure Active Directory | Microsoft Docs
description: Poskytuje odpovědi na dotazy, které správci IT můžou mít o nastavení a synchronizaci dat aplikací.
services: active-directory
keywords: Enterprise stavu nastavení roamingu windows cloudu, nejčastější dotazy na enterprise stavu roaming
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: f45d0515-99f7-42ad-94d8-307bc0d07be5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: tanning
ms.custom: it-pro
ms.openlocfilehash: 25660eef50a0a18d4f404944daeb443133424897
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35261147"
---
# <a name="troubleshooting-enterprise-state-roaming-settings-in-azure-active-directory"></a>Řešení potíží s Enterprise State Roaming nastavení ve službě Azure Active Directory

Toto téma obsahuje informace o tom, jak odstraňovat a diagnostikovat problémy s Enterprise State Roaming a poskytuje seznam známých problémů.

## <a name="preliminary-steps-for-troubleshooting"></a>Přípravné kroky pro řešení potíží 
Než začnete, řešení potíží, ověřte, že uživatele a zařízení správně nakonfigurovány a splnění všech požadavků Enterprise State Roaming zařízení a uživatele. 

1. Windows 10 s nejnovější aktualizace a minimální verzi 1511 (sestavení operačního systému 10586 nebo novější) je nainstalována na zařízení. 
2. Azure AD je zařízení připojené k nebo připojené k hybridní Azure AD. Další informace najdete v tématu [jak získat zařízení pod kontrolou Azure AD](device-management-introduction.md).
3. Ujistěte se, že **Enterprise State Roaming** je povolený pro klienta ve službě Azure AD jak je popsáno v [povolit Enterprise State Roaming](active-directory-windows-enterprise-state-roaming-enable.md). Můžete povolit roaming pro všechny uživatele nebo pouze vybrané skupiny uživatelů.
4. Uživatel musí mít přiřazen již licenci Azure Active Directory Premium.  
25. Po restartování zařízení a uživatel musí znovu přihlásit k přístupu k funkcím Enterprise State Roaming.

## <a name="information-to-include-when-you-need-help"></a>Informace, které chcete zahrnout, pokud potřebujete pomoc
Pokud se vám nepovede vyřešit problém s níže uvedené pokyny, můžete kontaktovat naši pracovníci technické podpory. Při kontaktování je, zahrňte následující informace:

* **Obecný popis chyby**: jsou zprávy o chybách pohledu uživatele? Pokud se žádná chybová zpráva, popisují neočekávanému chování, které jste si všimli, podrobně. Jaké funkce jsou povolené pro synchronizaci a co je uživatel očekává synchronizaci? Nejsou synchronizuje více funkcí nebo je izolovaná na jednu?
* **Ovlivněných uživatelů** – pracovní nebo selhání pro jeden nebo více uživateli je synchronizace? Kolik zařízení se podílejí na uživatele? Jsou všechny z nich nesynchronizuje nebo jsou některé z nich, synchronizaci a některé nesynchronizuje?
* **Informace o uživateli** – co je identita uživatele s využitím se přihlásit k zařízení? Jak je uživateli přihlášení k zařízení? Že jsou součástí vybrané skupiny zabezpečení oprávnění k synchronizaci? 
* **Informace o zařízení** – je toto zařízení Azure připojené k AD nebo připojený k doméně? Jaké sestavení je na zařízení? Jaké jsou nejnovější aktualizace?
- **Datum a čas / časové pásmo** – jak se přesné datum a čas, které jste viděli chyba (včetně časové pásmo)?

Tyto informace včetně pomáhá nám co nejdříve vyřešit problém.

## <a name="troubleshooting-and-diagnosing-issues"></a>Řešení potíží a diagnostika problémů
Tato část nabízí návrhy o tom, jak odstraňovat a diagnostikovat problémy související s Enterprise State Roaming.

## <a name="verify-sync-and-the-sync-your-settings-settings-page"></a>Ověření synchronizace a nastavení stránky "Synchronizace nastavení" 

1. Po připojení k doméně, která jsou nakonfigurována ke spouštění Enterprise State Roaming počítače s Windows 10, přihlaste se pomocí svého pracovního účtu. Přejděte na **nastavení** > **účty** > **vaše nastavení synchronizace** a ověřte, zda jsou jednotlivé nastavení a synchronizace a že horní části Stránka nastavení určuje, že se synchronizuje pomocí svého pracovního účtu. Potvrďte stejný účet se používá jako váš účet pro přihlášení v **nastavení** > **účty** > **vaše údaje**. 
2. Ověřte, že synchronizace funguje napříč více počítačů tím, že některé změny na původní počítač, například přesun na hlavním panelu na stranu pravé nebo horní části obrazovky. Sledování změn se rozšíří na druhý počítač do pěti minut. 
  * Zamknutí a odemknutí obrazovky (Win + L) může pomoct aktivaci synchronizace.
  * Musí být přihlašujete pomocí stejného účtu na obou počítačích pro synchronizaci pracovat – jako Enterprise State Roaming je vázaný na uživatelský účet a účet počítače.

**Potenciální problém**: Pokud ovládacích prvků v **nastavení** stránky nejsou k dispozici, a zobrazí se zpráva "některými funkcemi Windows, jsou k dispozici pouze pokud používáte účet Microsoft nebo pracovní účet." Tento problém může vzniknout pro zařízení, která jsou nastavena na být připojený k doméně a zaregistrované u Azure AD, ale zařízení se ještě úspěšně neověřené do služby Azure AD. Možnou příčinou je, že zásady zařízení se musí použít, ale tato aplikace probíhá asynchronně a může být zpožděn několik hodin. 

### <a name="verify-the-device-registration-status"></a>Ověření stavu registrace zařízení
Enterprise State Roaming vyžaduje, aby zařízení zaregistrovat u služby Azure AD. I když nejsou specifické pro Enterprise State Roaming, postupujte podle pokynů níže může pomoct potvrdit, že klient systému Windows 10 je zaregistrován a potvrdit stav kryptografický otisk, nastavení adresy URL služby Azure AD, NGC a další informace.

1.  Otevřete příkazový řádek, který je pozastavené. Chcete-li to provést v systému Windows, otevřete Spouštěče spustit (Win + R) a zadejte "cmd" otevřete.
2.  Jakmile příkazovém řádku je otevřený, zadejte "*/dsregcmd.exe status*".
3.  Pro očekávaný výstup **AzureAdJoined** pole hodnota by měla být "Ano", **WamDefaultSet** pole hodnota by měla být "Ano" a **WamDefaultGUID** pole hodnota by měla být identifikátor GUID s "(AzureAd)" na konci.

**Potenciální problém**: **WamDefaultSet** a **AzureAdJoined** mějte hodnota pole "Ne", zařízení je připojené k doméně a zaregistrovaná v Azure AD, i zařízení není synchronizovaná. Pokud je to zobrazeno, zařízení může potřebovat čekání na dokončení použít nebo ověřování pro zařízení se nezdařilo při připojování ke službě Azure AD. Uživatel může mít až několik hodin, než zásady, které se má použít. Při řešení potíží může zahrnovat automatickou registraci opakování odhlaste se a zpět v nebo spouštění úloh v Plánovači úloh. V některých případech systémem "*dsregcmd.exe /leave*" v okně příkazového řádku se zvýšenými oprávněními, restartování a zkusit to znovu registrace mohou pomoci s tímto problémem.


**Potenciální problém**: pole pro **AzureAdSettingsUrl** je prázdný a není synchronizovaná zařízení. Uživatel může mít poslední přihlášení k zařízení před Enterprise State Roaming bylo povoleno na portálu Azure Active Directory. Zařízení restartovat a mít přihlašovací údaje uživatele. Volitelně můžete na portálu, zkuste s IT správce, zakažte a znovu povolte nastavení synchronizace mohou uživatelé a podniková Data aplikace. Jednou znovu povolené, zařízení restartovat a mít přihlašovací údaje uživatele. Pokud problém nevyřešíte **AzureAdSettingsUrl** může být prázdný, v případě certifikát chybný zařízení. V takovém případě systémem "*dsregcmd.exe /leave*" v okně příkazového řádku se zvýšenými oprávněními, restartování a zkusit to znovu registrace mohou pomoci s tímto problémem.

## <a name="enterprise-state-roaming-and-multi-factor-authentication"></a>Enterprise State Roaming a vícefaktorového ověřování 
Za určitých podmínek Enterprise State Roaming může selhat synchronizaci dat, pokud je nakonfigurované ověřování Azure Multi-Factor Authentication. Další informace o těchto příznaků, najdete v dokumentu podporu [KB3193683](https://support.microsoft.com/kb/3193683). 

**Potenciální problém**: Pokud je nakonfigurováno zařízení vyžadovat Vícefaktorové ověřování na portálu Azure Active Directory, se pravděpodobně nezdaří synchronizaci nastavení při přihlášení k zařízením s Windows 10 pomocí hesla. Tento typ konfigurace služby Multi-Factor Authentication je určená pro ochranu účet správce služby Azure. Správci mohou stále moci synchronizovat po přihlášení k jejich zařízení s Windows 10 s jejich Microsoft Passport pro pracovní PIN kód nebo pomocí služby Multi-Factor Authentication při přístupu k jiným službám Azure, jako je Office 365.

**Potenciální problém**: synchronizace může selhat, pokud správce nakonfiguruje zásady podmíněného přístupu Active Directory Federation služby Multi-Factor Authentication a vyprší platnost přístupového tokenu v zařízení. Zajistěte, aby přihlášení a odhlášení pomocí Microsoft Passport pro pracovní PIN kód nebo dokončení služby Multi-Factor Authentication při přístupu k jiným službám Azure, jako je Office 365.

### <a name="event-viewer"></a>Prohlížeč událostí
Pro pokročilé řešení problémů s prohlížeči událostí můžete použít k vyhledání konkrétní chyby. Ty jsou popsané v následující tabulce. Události lze nalézt v prohlížeči událostí > protokoly aplikací a služeb > **Microsoft** > **Windows** > **SettingSync** a pro problémy související s identity se synchronizací **Microsoft** > **Windows** > **AAD**.


## <a name="known-issues"></a>Známé problémy

### <a name="sync-does-not-work-on-devices-that-have-apps-side-loaded-using-mdm-software"></a>Synchronizace nefunguje na zařízeních s nainstalovanými aplikacemi zkušebně načtených pomocí softwaru MDM software

Ovlivňuje zařízení se systémem Windows 10 Anniversary Update (verze 1607). V prohlížeči událostí pod protokoly SettingSync Azure je často vidět 6013 ID události s chybou 80070259.

**Doporučená akce**  
Ověřte, zda má klient Windows 10 v1607 23 srpna 2016 kumulativní aktualizace ([KB3176934](https://support.microsoft.com/kb/3176934) 14393.82 sestavení operačního systému). 

---

### <a name="internet-explorer-favorites-do-not-sync"></a>Není synchronizována Oblíbené aplikace Internet Explorer

Ovlivňuje zařízení se systémem Windows 10 listopadu Update (verze 1511).

**Doporučená akce**  
Ověřte, zda má klient v1511 Windows 10. července 2016 kumulativní aktualizace ([KB3172985](https://support.microsoft.com/kb/3172985) 10586.494 sestavení operačního systému).

---

### <a name="theme-is-not-syncing-as-well-as-data-protected-with-windows-information-protection"></a>Motiv nesynchronizuje, a také data chráněná pomocí Windows Information Protection 

Aby se zabránilo úniku dat, data, která je chráněná pomocí [Windows Information Protection](https://technet.microsoft.com/itpro/windows/keep-secure/protect-enterprise-data-using-wip) nebude pro používání aktualizace Windows 10 Anniversary zařízení synchronizovat prostřednictvím Enterprise State Roaming.



**Doporučená akce**  
Žádné. Tento problém může vyřešit budoucí aktualizace systému Windows.

---

### <a name="date-time-and-region-settings-do-not-sync-on-domain-joined-device"></a>Datum, čas a oblast nastavení není synchronizována na zařízení připojeném k doméně 
  
Zařízení, které jsou připojené k doméně už nebude synchronizace pro nastavení datum, čas a oblast: automatické čas. Pomocí automatické času mohou přepsat jiné datum, čas a oblasti nastavení a tato nastavení nejsou pro synchronizaci. 

**Doporučená akce**  
Žádné. 

---

### <a name="uac-prompts-when-syncing-passwords"></a>Řízení uživatelských účtů vyzve při synchronizaci hesel

Ovlivňuje zařízení se systémem Windows 10 listopadu Update (verze 1511) s bezdrátový síťový adaptér nakonfigurovaný tak, aby synchronizaci hesel.

**Doporučená akce**  
Ověřte, zda má kumulativní aktualizaci klienta v1511 Windows 10 ([KB3140743](https://support.microsoft.com/kb/3140743) 10586.494 sestavení operačního systému).

---

### <a name="sync-does-not-work-on-devices-that-use-smart-card-for-login"></a>Synchronizace nefunguje na zařízení, která používají čipové karty pro přihlášení
Pokud se pokusíte se přihlásit k zařízení s Windows pomocí čipové karty a virtuální čipové karty, nastavení synchronizace přestanou fungovat.     

**Doporučená akce**  
Žádné. Tento problém může vyřešit budoucí aktualizace systému Windows.

---

### <a name="domain-joined-device-is-not-syncing-after-leaving-corporate-network"></a>Po opuštění podnikové síti není synchronizaci zařízení připojených k doméně     
Zařízení připojených k doméně zaregistrován ke službě Azure AD může zaznamenat selhání synchronizace, pokud je zařízení mimo pracoviště pro dlouhou dobu a nemůže dokončit ověřování v doméně.

**Doporučená akce**  
Připojte zařízení k podnikové síti, aby mohli obnovit synchronizace.

---

 ### <a name="azure-ad-joined-device-is-not-syncing-and-the-user-has-a-mixed-case-user-principal-name"></a>Azure AD připojení k zařízení není synchronizaci a má uživatel smíšený případu hlavní název uživatele.
 Pokud má uživatel smíšený případu hlavní název uživatele (například uživatelské jméno místo uživatelské jméno) a má uživatel na zařízení s připojení k Azure AD, kterou má upgradovat z 10586 sestavení Windows 10 na 14393, zařízení uživatele se pravděpodobně nezdaří k synchronizaci. 

**Doporučená akce**  
Uživatel bude muset zrušení služby a znovu připojit zařízení do cloudu. Uděláte to přihlášení jako uživatel místního správce a zrušení služby zařízení tak, že přejdete do **nastavení** > **systému** > **o** a vyberte možnost "Manage nebo odpojení od práci nebo ve škole ". Vyčištění zařízení znovu na následující soubory a pak Azure AD Join **nastavení** > **systému** > **o** a výběrem "připojit se k práci nebo Školní". Nadále připojit zařízení k Azure Active Directory a dokončete postup.

V kroku čištění čištění následující soubory:
- Settings.dat v `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\Settings\`
- Všechny soubory ve složce `C:\Users\<Username>\AppData\Local\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy\AC\TokenBroker\Account`

---

### <a name="event-id-6065-80070533-this-user-cant-sign-in-because-this-account-is-currently-disabled"></a>ID události 6065:80070533, které tento uživatel nemůže přihlásit, protože tento účet je aktuálně zakázáno.  
V prohlížeči událostí pod protokoly SettingSync/Debug může tato chyba zobrazí, pokud vypršela platnost přihlašovacích údajů uživatele. Kromě toho může dojít, když klient automaticky neměl AzureRMS zřízený. 

**Doporučená akce**  
V prvním případě máte aktualizovat své přihlašovací údaje a přihlaste se na zařízení s novými pověřeními uživatele. K vyřešení problému AzureRMS, postupujte podle kroků uvedených v [KB3193791](https://support.microsoft.com/kb/3193791). 

---

### <a name="event-id-1098-error-0xcaa5001c-token-broker-operation-failed"></a>ID události. 1098: Chyba: 0xCAA5001C Token zprostředkovatele operace se nezdařila  
V prohlížeči událostí pod AAD nebo provozní protokoly, tato chyba může vidět s událostí 1104: AAD cloudu Asie modulu plug-in volání Get token vrátil chybu: 0xC000005F. K tomuto problému dochází, pokud jsou chybí oprávnění nebo vlastnictví atributy.  

**Doporučená akce**  
Postupujte podle kroků uvedených [KB3196528](https://support.microsoft.com/kb/3196528).  



## <a name="next-steps"></a>Další postup

- Použití [User Voice fórum](https://feedback.azure.com/forums/169401-azure-active-directory/category/158658-enterprise-state-roaming) poskytnout zpětnou vazbu a zaslat návrhy pro zlepšení Enterprise State Roaming.

- Další informace najdete v tématu [Enterprise State Roaming přehled](active-directory-windows-enterprise-state-roaming-overview.md). 

## <a name="related-topics"></a>Související témata
* [Přehled roamingu stavu Enterprise](active-directory-windows-enterprise-state-roaming-overview.md)
* [Povolit stav enterprise roaming v Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
* [Nastavení a datový roaming – nejčastější dotazy](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Nastavení MDM pro synchronizaci nastavení zásad skupiny a](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Odkaz nastavení roamingu Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
