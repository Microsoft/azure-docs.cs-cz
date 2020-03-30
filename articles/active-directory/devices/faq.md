---
title: Nejčastější dotazy ke správě zařízení služby Azure Active Directory | Dokumenty společnosti Microsoft
description: Nejčastější dotazy ke správě zařízení služby Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: c238600d412e53ad665214492e292aa395655b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497518"
---
# <a name="azure-active-directory-device-management-faq"></a>Nejčastější dotazy ke správě zařízení služby Azure Active Directory

## <a name="general-faq"></a>Obecné nejčastější dotazy

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>Otázka: Nedávno jsem zařízení zaregistroval. Proč nevidím zařízení pod informacemi o uživateli na webu Azure Portal? Nebo proč je vlastník zařízení označen jako Není k zapisovaná zařízení pro hybridní Azure Active Directory (Azure AD) připojená zařízení?

**A:** Zařízení s Windows 10, která jsou hybridní mj. **USER devices**
Použijte zobrazení **Všechna zařízení** na webu Azure Portal. Můžete také použít rutinu PowerShell [Get-MsolDevice.](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0)

V seznamu **zařízení USER**jsou uvedena pouze následující zařízení :

- Všechna osobní zařízení, která nejsou hybridní Azure AD připojen. 
- Všechna zařízení, která nejsou windows 10 nebo Windows Server 2016.
- Všechna zařízení, která nejsou windows. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>Otázka: Jak poznám, jaký je stav registrace zařízení klienta?

**A:** Na webu Azure Portal přejděte na **Všechna zařízení**. Vyhledejte zařízení pomocí ID zařízení. Zkontrolujte hodnotu ve sloupci typu spojení. Někdy může být zařízení resetováno nebo reimaged. Proto je také důležité zkontrolovat stav registrace zařízení v zařízení:

- U zařízení s Windows 10 a Windows Server `dsregcmd.exe /status`2016 nebo novějším spusťte .
- Pro verze operačního systému `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`nižší úrovně spusťte .

**A:** Informace o řešení potíží naleznete v těchto článcích:
- [Řešení potíží se zařízeními pomocí příkazu dsregcmd](troubleshoot-device-dsregcmd.md)
- [Řešení potíží s hybridní službou Azure Active Directory, která se připojila k zařízením s Windows 10 a Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)
- [Řešení potíží s hybridními zařízeními Azure Active Directory, která se připojili k zařízením nižší úrovně](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>Otázka: Záznam zařízení se zobrazuje pod informacemi o uživateli na webu Azure Portal. A vidím stát, jak je registrován na zařízení. Mám správně nastaveno použití podmíněného přístupu?

**A:** Stav připojení zařízení, zobrazený **id zařízení**, musí odpovídat stavu ve službě Azure AD a splňovat všechna kritéria hodnocení pro podmíněný přístup. Další informace najdete [v tématu Vyžadovat spravovaná zařízení pro přístup ke cloudovým aplikacím pomocí podmíněného přístupu](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices"></a>Otázka: Proč se mým uživatelům zobrazuje chybová zpráva "Vaše organizace odstranila zařízení" nebo "Vaše organizace zakázala zařízení" na svých zařízeních s Windows 10?

**A:** Na zařízeních s Windows 10, která jsou připojena nebo registrována ve službě Azure AD, se uživatelům vydává [primární obnovovací token (PRT),](concept-primary-refresh-token.md) který umožňuje jednotné přihlašování. Platnost PRT je založena na platnosti samotného zařízení. Uživatelé se zobrazí tato zpráva, pokud je zařízení odstraněné nebo zakázané ve službě Azure AD bez zahájení akce ze samotného zařízení. Zařízení lze odstranit nebo zakázat ve službě Azure AD jeden z následujících scénářů: 

- Uživatel zařízení zakáže z portálu Moje aplikace. 
- Správce (nebo uživatel) odstraní nebo zakáže zařízení na webu Azure Portal nebo pomocí PowerShellu
- Hybridní Azure AD připojen jenom: Správce odebere zařízení OU mimo rozsah synchronizace výsledkem zařízení odstraněných z Azure AD
- Upgrade Azure AD připojení k verzi 1.4.xx.x. [Principy Azure AD Connect 1.4.xx.x a zmizení zařízení](/azure/active-directory/hybrid/reference-connect-device-disappearance).


Viz níže, jak lze tyto akce napravit.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>Otázka: Zakázal jsem nebo odstranil své zařízení na webu Azure Portal nebo pomocí Windows PowerShellu. Ale místní stát na zařízení říká, že je stále registrován. Co bych měl/a dělat?

**A:** Tato operace je záměrná. V takovém případě zařízení nemá přístup k prostředkům v cloudu. Správci mohou tuto akci provést pro zastaralá, ztracená nebo odcizená zařízení, aby zabránili neoprávněnému přístupu. Pokud byla tato akce provedena neúmyslně, budete muset zařízení znovu povolit nebo znovu zaregistrovat, jak je popsáno níže.

- Pokud bylo zařízení ve službě Azure AD zakázáno, správce s dostatečnými oprávněními ho může povolit z portálu Azure AD.  
  > [!NOTE]
  > Pokud synchronizujete zařízení pomocí Azure AD Connect, hybridní zařízení připojená k Azure AD se automaticky znovu povolit během dalšího cyklu synchronizace. Takže pokud potřebujete zakázat hybridní zařízení spojené s Azure AD, musíte ho zakázat z místní služby AD.

 - Pokud se zařízení odstraní ve službě Azure AD, je potřeba zařízení znovu zaregistrovat. Chcete-li se znovu zaregistrovat, musíte v zařízení provést ruční akci. Pokyny pro opětovnou registraci na základě stavu zařízení naleznete níže. 

      Pokud chcete znovu zaregistrovat hybridní Azure AD, které se připojilo k zařízením s Windows 10 a Windows Server 2016/2019, postupujte takto:

      1. Otevřete příkazový řádek jako správce.
      1. Zadejte `dsregcmd.exe /debug /leave`.
      1. Odhlaste se a přihlaste se k aktivaci naplánované úlohy, která znovu zaregistruje zařízení pomocí Azure AD. 

      Pro verze operačního systému Windows nižší úrovně, které jsou hybridní Azure AD připojil, postupujte takto:

      1. Otevřete příkazový řádek jako správce.
      1. Zadejte `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
      1. Zadejte `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

      Pro zařízení Azure AD připojená k zařízením s Windows 10 postupujte takto:

      1. Otevření příkazového řádku jako správce
      1. Zadejte `dsregcmd /forcerecovery` (Poznámka: K provedení této akce musíte být správcem).
      1. Klikněte na "Přihlásit se" v dialogovém okně, které se otevře a pokračovat v procesu přihlášení.
      1. Chcete-li obnovení dokončit, odhlaste se a přihlaste se zpět k zařízení.

      Pro zařízení s Windows 10 registrovanými službou Azure AD postupujte takto:

      1. Přejděte na **nastavení** > **účtů** > **přístup k práci nebo škole**. 
      1. Vyberte účet a vyberte **Odpojit**.
      1. Klikněte na "+ Connect" a zaregistrujte zařízení znovu procházením přihlašovacího procesu.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>Otázka: Proč se na webu Azure Portal zobrazují duplicitní položky zařízení?

**A:**

- U Windows 10 a Windows Server 2016 může opakované pokusy o odpojení a opětovné připojení ke stejnému zařízení způsobit duplicitní položky. 
- Každý uživatel systému Windows, který používá **účet Přidat práci nebo školu,** vytvoří nový záznam zařízení se stejným názvem zařízení.
- Pro verze operačního systému Windows nižší úrovně, které jsou připojeny k místní doméně služby Azure Directory, automatická registrace vytvoří nový záznam zařízení se stejným názvem zařízení pro každého uživatele domény, který se k zařízení přihlásí. 
- Počítač spojený s Azure AD, který se vymazal, přeinstaloval a znovu připojil se stejným názvem, se zobrazí jako jiný záznam se stejným názvem zařízení.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>Otázka: Podporuje registrace zařízení s Windows 10 ve službě Azure AD tpms v režimu FIPS?

**A:** Registrace zařízení s Windows 10 je podporovaná jenom pro čip TPM 2.0 kompatibilní se standardem FIPS a není podporována pro čip TPM 1.2. Pokud vaše zařízení mají Čip TPM 1.2 kompatibilní s FIPS, musíte je před pokračováním v připojení k Azure AD nebo hybridním připojení azure ad zakázat. Společnost Microsoft neposkytuje žádné nástroje pro zakázání režimu FIPS pro tpms, protože je závislá na výrobci čipu TPM. Obraťte se na svého hardwarového oem pro podporu. 

---

**Otázka: Proč může uživatel stále přistupovat k prostředkům ze zařízení, které jsem na webu Azure Portal zakázal?**

**A:** Trvá až hodinu, než se odvolání použije od okamžiku, kdy je zařízení Azure AD označeno jako zakázané.

>[!NOTE] 
>U zaregistrovaných zařízení doporučujeme zařízení vymazat, abyste měli jistotu, že uživatelé nemají přístup k prostředkům. Další informace najdete v tématu [Co je registrace zařízení?](/mem/intune/user-help/use-managed-devices-to-get-work-done). 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>Otázka: Proč jsou zařízení označená jako "Čekající" pod registrovaným sloupcem na webu Azure Portal?

**A**: Čeká na vyřízení označuje, že zařízení není registrováno. Tento stav označuje, že zařízení bylo synchronizováno pomocí připojení Azure AD z místního služby AD a je připraveno k registraci zařízení. Tato zařízení mají typ join nastavena na "Hybridní Azure AD připojen". Přečtěte si další informace o [tom, jak naplánovat hybridní implementaci připojení k azure active directory](hybrid-azuread-join-plan.md).

>[!NOTE]
>Zařízení může také změnit z registrovaného stavu na "Čeká na vyřízení"
>* Pokud se zařízení nejprve odstraní ze služby Azure AD a znovu se synchronizuje z místního služby AD.
>* Pokud se zařízení odebere z oboru synchronizace ve službě Azure AD Connect a přidá zpět.
>
>V obou případech je nutné znovu zaregistrovat zařízení ručně na každém z těchto zařízení. Chcete-li zkontrolovat, zda bylo zařízení dříve zaregistrováno, můžete [řešit potíže se zařízeními pomocí příkazu dsregcmd](troubleshoot-device-dsregcmd.md).

---
## <a name="azure-ad-join-faq"></a>Nejčastější dotazy týkající se připojení k Azure AD

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>Otázka: Jak se můžu připojit k zařízení Azure AD připojenmístně na zařízení?

**A:** Pro čistá zařízení připojená k Azure AD se ujistěte, že máte účet místního správce offline nebo ho vytvořte. Nemůžete se přihlásit pomocí žádné přihlašovací údaje uživatele Azure AD. Dále přejděte na **nastavení** > **účtů** > **přístup k práci nebo škole**. Vyberte svůj účet a vyberte **Odpojit**. Postupujte podle pokynů a po zobrazení výzvy zadejte pověření místního správce. Restartujte zařízení a dokončete proces odpojení.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>Otázka: Můžou se moji uživatelé přihlásit k zařízením Azure AD, která jsou odstraněná nebo zakázaná ve službě Azure AD?

**Odpověď:** Ano. Systém Windows má možnost uživatelského jména a hesla uložené v mezipaměti, které uživatelům, kteří se dříve přihlásili, umožňuje rychlý přístup k ploše i bez připojení k síti. 

Když je zařízení odstraněné nebo zakázané ve službě Azure AD, není známo zařízení s Windows. Takže uživatelé, kteří se dříve přihlásili, nadále přistupují k ploše pomocí uživatelského jména a hesla uloženého v mezipaměti. Ale jak je zařízení odstraněno nebo zakázáno, uživatelé nemají přístup k žádným prostředkům chráněným podmíněným přístupem založeným na zařízení. 

Uživatelé, kteří se dříve nepřihlásili, nemají k zařízení přístup. Není pro ně povoleno žádné uživatelské jméno a heslo uložené v mezipaměti. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>Otázka: Může se zakázaný nebo odstraněný uživatel přihlásit k zařízením s připojenou k Azure AD.

**A:** Ano, ale jen na omezenou dobu. Když je uživatel odstraněnnebo zakázán ve službě Azure AD, není okamžitě známo zařízení s Windows. Uživatelé, kteří se dříve přihlásili, tak mohou přistupovat k ploše pomocí uživatelského jména a hesla uloženého v mezipaměti. 

Zařízení si obvykle uvědomuje stav uživatele za méně než čtyři hodiny. Systém Windows pak blokuje přístup těchto uživatelů k ploše. Jako uživatel je odstraněn nebo zakázán ve službě Azure AD, všechny jeho tokeny jsou odvolány. Takže nemají přístup k žádným prostředkům. 

Odstranění nebo deaktivování uživatelé, kteří se dříve nepřihlásili, nemají přístup k zařízení. Není pro ně povoleno žádné uživatelské jméno a heslo uložené v mezipaměti. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>Otázka: Proč mají moji uživatelé problémy se zařízeními Azure AD po změně svého upn?

**A:** V současné době nejsou plně podporované změny UPN na zařízeních spojených s Azure AD. Takže jejich ověřování pomocí Azure AD selže po jejich změny UPN. V důsledku toho mají uživatelé problémy s přistupujícím systémem a podmíněným přístupem na svých zařízeních. V tuto chvíli se uživatelé musí přihlásit k systému Windows prostřednictvím dlaždice "Jiný uživatel" pomocí nového hlavního názvového on-n., aby tento problém vyřešili. V současné době pracujeme na řešení tohoto problému. Uživatelé, kteří se přihlašují pomocí Windows Hello pro firmy, však tomuto problému nečelí. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>Otázka: Moji uživatelé nemůžou prohledávat tiskárny ze zařízení s azure ad. Jak mohu povolit tisk z těchto zařízení?

**A:** Informace o nasazení tiskáren pro zařízení připojená k Azure AD najdete [v tématu Nasazení hybridního cloudového tisku windows serveru s předběžným ověřováním](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). K nasazení hybridního cloudového tisku potřebujete místní Windows Server. V současné době cloudová tisková služba není k dispozici. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>Otázka: Jak se připojím ke vzdálenému zařízení s připojením k Azure AD?

**A:** Viz [Připojení ke vzdálenému počítači azure active directory .](/windows/client-management/connect-to-remote-aadj-pc)

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>Otázka: Proč moji uživatelé vidí *Nemůžete se tam dostat odtud?*

**A:** Nakonfigurovali jste určitá pravidla podmíněného přístupu tak, aby vyžadovala určitý stav zařízení? Pokud zařízení nesplňuje kritéria, uživatelé jsou blokováni a tato zpráva se zobrazí. Vyhodnoťte pravidla zásad podmíněného přístupu. Ujistěte se, že zařízení splňuje kritéria, abyste se vyhnuli zprávě.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>Otázka: Proč někteří z mých uživatelů nedostávají výzvy azure multi-factor authentication na zařízeních spojených s Azure AD?

**A:** Uživatel se může připojit nebo zaregistrovat zařízení pomocí Azure AD pomocí vícefaktorového ověřování. Pak se samotné zařízení stane důvěryhodným druhým faktorem pro tohoto uživatele. Kdykoli se stejný uživatel přihlásí k zařízení a přistupuje k aplikaci, Azure AD považuje zařízení za druhý faktor. Umožňuje tomuto uživateli bezproblémový přístup k aplikacím bez dalších výzev k vícefaktorovému ověřování. 

Toto chování:

- Platí pro připojená zařízení Azure AD a registrovaná zařízení Azure AD – ale ne pro hybridní zařízení připojená k Azure AD.
- Nevztahuje se na žádného jiného uživatele, který se k danému zařízení přihlásí. Takže všichni ostatní uživatelé, kteří mají přístup k tomuto zařízení, získají výzvu vícefaktorového ověřování. Pak mohou přistupovat k aplikacím, které vyžadují vícefaktorové ověřování.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>Otázka: Proč dostanu *uživatelské jméno nebo heslo je nesprávná* zpráva pro zařízení, které jsem právě připojil k Azure AD?

**A:** Běžné důvody pro tento scénář jsou následující:

- Vaše pověření uživatele již nejsou platná.
- Počítač nemůže komunikovat se službou Azure Active Directory. Zkontrolujte, zda nenajdete problémy s připojením k síti.
- Federovaná přihlášení vyžadují, aby federační server podporoval koncové body WS-Trust, které jsou povolené a přístupné. 
- Povolili jste předávací ověřování. Takže vaše dočasné heslo je třeba změnit při přihlášení.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>Otázka: Proč vidím *Jejda ... došlo k chybě!* když se pokusím připojit Azure AD k počítači?

**A:** K této chybě dojde, když nastavíte registraci služby Azure Active Directory pomocí Intune. Ujistěte se, že uživateli, který se pokusí připojit k Azure AD, je přiřazena správná licence Intune. Další informace naleznete v tématu [Nastavení registrace pro zařízení s Windows](/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>Otázka: Proč se můj pokus o připojení Azure AD připojit k počítači nezdaří, i když jsem nedostal žádné informace o chybě?

**A:** Pravděpodobnou příčinou je, že jste se k zařízení přihlásili pomocí místního předdefinovaného účtu správce. Před dokončením instalace vytvořte jiný místní účet. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>Otázka: Jaké jsou certifikáty MS-Organization-P2P-Access, které jsou k dispozici na našich zařízeních s Windows 10?

**A:** Certifikáty MS-Organization-P2P-Access vydávají Azure AD oběma zařízením Azure AD a hybridním zařízením azure ad. Tyto certifikáty se používají k povolení důvěryhodnosti mezi zařízeními ve stejném tenantovi pro scénáře vzdálené plochy. Jeden certifikát je vydán zařízení a jiný je vydán uživateli. Certifikát zařízení je `Local Computer\Personal\Certificates` přítomen a je platný po dobu jednoho dne. Tento certifikát se obnoví (vydáním nového certifikátu), pokud je zařízení stále aktivní ve službě Azure AD. Uživatelský certifikát je `Current User\Personal\Certificates` k dispozici v a tento certifikát je také platný pro jeden den, ale je vydán na vyžádání, když se uživatel pokusí o relaci vzdálené plochy na jiné zařízení spojené s Azure AD. Po uplynutí doby platnosti není obnovena. Oba tyto certifikáty jsou vydávány pomocí certifikátu MS-Organization-P2P-Access, který je k dispozici v oblasti `Local Computer\AAD Token Issuer\Certificates`. Tento certifikát je vydán Službou Azure AD během registrace zařízení. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Otázka: Proč se na našich zařízeních s Windows 10 zobrazí více certifikátů s prošlou platností vydaných ms-organization-p2p-access? Jak je mohu odstranit?

**A:** V systému Windows 10 verze 1709 a nižším, kde v úložišti počítače z důvodu kryptografických problémů nadále existoval y certifikáty MS-Organization-P2P-Access, byl zjištěn problém. Uživatelé mohou čelit problémům s připojením k síti, pokud používáte všechny klienty VPN (například Cisco AnyConnect), kteří nemohou zpracovat velký počet certifikátů, jejichž platnost vypršela. Tento problém byl opraven v systému Windows 10 1803 vydání automaticky odstranit všechny takové vypršela MS-Organization-P2P-Access certifikáty. Tento problém můžete vyřešit aktualizací zařízení na Windows 10 1803. Pokud nelze aktualizovat, můžete tyto certifikáty odstranit bez jakéhokoli nepříznivého dopadu.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Nejčastější dotazy týkající se hybridního připojení k Azure AD

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>Otázka: Jak se můžu připojit k hybridnímu zařízení Azure AD připojenou místně na zařízení?

**A:** U hybridních zařízení spojených s Azure AD nezapomeňte vypnout automatickou registraci. Pak naplánovaná úloha zařízení znovu nezaregistruje. Dále otevřete příkazový řádek jako `dsregcmd.exe /debug /leave`správce a zadejte . Nebo spusťte tento příkaz jako skript napříč několika zařízeními pro hromadné odpojení.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>Otázka: Kde najdu informace o řešení potíží pro diagnostiku selhání hybridního připojení Azure AD?

**A:** Informace o řešení potíží naleznete v těchto článcích:

- [Řešení potíží s hybridní službou Azure Active Directory, která se připojila k zařízením s Windows 10 a Windows Server 2016](troubleshoot-hybrid-join-windows-current.md)
- [Řešení potíží s hybridními zařízeními Azure Active Directory, která se připojili k zařízením nižší úrovně](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>Otázka: Proč se mi v seznamu zařízení Azure AD zobrazuje duplicitní registrovaný záznam Azure AD pro moje hybridní zařízení Azure AD s Windows 10?

**A:** Když uživatelé přidají své účty do aplikací na zařízení, které je připojeno k doméně, může se na ně zobrazit výzva **s příkazem Přidat účet do Windows?** Pokud na výzvu zadají **Ano,** zařízení se zaregistruje ve službě Azure AD. Typ důvěryhodnosti je označen jako registrovaný azure ad. Po povolení hybridní připojení Azure AD ve vaší organizaci, zařízení také získá hybridní Azure AD připojil. Pak se pro stejné zařízení zobrazí dva stavy zařízení. 

Hybridní připojení Azure AD má přednost před stavem registrovaného služby Azure AD. Takže vaše zařízení je považováno za hybridní Azure AD připojen pro jakékoli ověřování a vyhodnocení podmíněného přístupu. Záznam registrovaného zařízení Azure AD můžete bezpečně odstranit z portálu Azure AD. Naučte [se vyhnout nebo vyčistit tento dvojí stav na počítači s Windows 10](hybrid-azuread-join-plan.md#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>Otázka: Proč mají moji uživatelé problémy s hybridními zařízeními Azure AD s Windows 10 po změně svého upn?

**A:** V současné době nejsou změny UPN plně podporovány s hybridními zařízeními spojenými s Azure AD. Zatímco uživatelé se můžou přihlásit k zařízení a přistupovat ke svým místním aplikacím, ověřování pomocí Azure AD se po změně hlavního uživatele nezdaří. V důsledku toho mají uživatelé problémy s přistupujícím systémem a podmíněným přístupem na svých zařízeních. V tuto chvíli je třeba zrušit připojení k zařízení z Azure AD (spustit "dsregcmd /leave" se zvýšenými oprávněními) a znovu se připojit (stane automaticky) k vyřešení problému. V současné době pracujeme na řešení tohoto problému. Uživatelé, kteří se přihlašují pomocí Windows Hello pro firmy, však tomuto problému nečelí. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>Otázka: Vyžadují hybridní zařízení Azure AD s Windows 10 k řadiči domény zorné pole, aby získali přístup ke cloudovým prostředkům?

**A:** Ne, s výjimkou případů, kdy je změněno heslo uživatele. Po dokončení připojení hybridního azure ad windows 10 a uživatel se přihlásil alespoň jednou, zařízení nevyžaduje zorné pole řadiče domény pro přístup ke cloudovým prostředkům. Windows 10 můžete získat jednotné přihlášení k aplikacím Azure AD odkudkoli s připojením k internetu, s výjimkou při změně hesla. Uživatelé, kteří se přihlašují pomocí Windows Hello pro firmy, i po změně hesla nadále přihlašují k aplikacím Azure AD, a to i v případě, že nemají zorné pole do svého řadiče domény. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>Otázka: Co se stane, když uživatel změní své heslo a pokusí se přihlásit ke svému hybridnímu zařízení Azure AD s Windows 10 mimo podnikovou síť?

**A:** Pokud se heslo změní mimo podnikovou síť (například pomocí azure ad sspr), pak se uživatel přihlásit pomocí nového hesla se nezdaří. Pro hybridní zařízení připojená k Azure AD je primární autoritou místní služba Active Directory. Pokud zařízení nemá zorné pole řadiče domény, nemůže ověřit nové heslo. Takže uživatel musí navázat spojení s řadičem domény (buď prostřednictvím sítě VPN nebo v podnikové síti), než se bude moci přihlásit k zařízení pomocí nového hesla. V opačném případě se mohou přihlásit pouze pomocí svého starého hesla z důvodu možnosti přihlášení v mezipaměti v systému Windows. Staré heslo je však zneplatněn Azure AD během požadavků na tokeny a proto zabraňuje jednotné přihlášení a selže všechny zásady podmíněného přístupu založené na zařízení. K tomuto problému nedochází, pokud používáte Windows Hello pro firmy. 

---

## <a name="azure-ad-register-faq"></a>Nejčastější dotazy k registru Azure AD

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>Otázka: Jak odebrat azure ad registrovaný stav pro zařízení místně?

**A:** 
- Pro zařízení registrovaná pro Windows 10 Azure AD přejděte na **Nastavení** > **účtů** > **Přístup k práci nebo škole**. Vyberte svůj účet a vyberte **Odpojit**. Registrace zařízení se ve Windows 10 provádí podle uživatelského profilu.
- Pro iOS a Android můžete použít**aplikaci** Microsoft Authenticator **Application Application Device** > Registration a vybrat **Možnost Zrušit registraci zařízení**.
- Pro macOS můžete použít aplikaci Portál společnosti Microsoft Intune k odhlášení zařízení ze správy a odebrání jakékoli registrace. 

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>Otázka: Jak můžu uživatelům zablokovat přidávání dalších pracovních účtů (registrovaných azure ad) na firemních zařízeních s Windows 10?

**A:** Povolte následující registr a zablokujte uživatelům přidání dalších pracovních účtů do vaší podnikové domény, připojeno k ní, k ad azure ad připojená k zařízením s Windows 10. Tuto zásadu lze také blokovat počítače spojené s doménou neúmyslně získání Azure AD registrované se stejným uživatelským účtem. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>Otázka: Mohu zaregistrovat zařízení Android nebo iOS BYOD?

**A:** Ano, ale jenom se službou registrace zařízení Azure a pro hybridní zákazníky. Není podporována místní službou registrace zařízení ve službě AD FS (AD FS).

---
### <a name="q-how-can-i-register-a-macos-device"></a>Otázka: Jak můžu zaregistrovat zařízení s macOS?

**A:** Postupujte takto:

1.    [Tvorba zásady dodržování předpisů](/intune/compliance-policy-create-mac-os)
1.    [Definování zásad podmíněného přístupu pro zařízení s macOS](../active-directory-conditional-access-azure-portal.md) 

**Poznámky:**

- Uživatelé zahrnutí do zásad podmíněného přístupu potřebují pro přístup k prostředkům [podporovanou verzi Office pro macOS.](../conditional-access/concept-conditional-access-conditions.md) 
- Během prvního pokusu o přístup budou uživatelé vyzváni k registraci zařízení pomocí portálu společnosti.

---
## <a name="next-steps"></a>Další kroky

- Další informace o [zařízeních registrovaných na Azure AD](concept-azure-ad-register.md)
- Další informace o [zařízeních s azure ad připojená](concept-azure-ad-join.md)
- Další informace o [hybridních zařízeních s Azure AD](concept-azure-ad-join-hybrid.md)
