---
title: Nejčastější dotazy týkající se správy zařízení Azure Active Directory | Microsoft Docs
description: Azure Active Directory časté otázky týkající se správy zařízení.
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
ms.openlocfilehash: fbba3f1b753738de57aa311387e522bae1b7b523
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499802"
---
# <a name="azure-active-directory-device-management-faq"></a>Nejčastější dotazy ke správě zařízení Azure Active Directory

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>Otázka: Nedávno jsem zaregistroval zařízení. Proč se v Azure Portal v části informace o uživateli nezobrazují zařízení? Nebo proč je pro zařízení připojená k hybridnímu Azure Active Directory (Azure AD) vlastník zařízení označený jako neurčený jako N/A.

**Odpověď:** Zařízení s Windows 10, která jsou připojená k hybridní službě Azure AD, se nezobrazí v části **uživatelská zařízení**.
V Azure Portal použijte zobrazení **všechna zařízení** . Můžete také použít rutinu PowerShellu [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) .

V části **uživatelská zařízení**jsou uvedena pouze následující zařízení:

- Všechna osobní zařízení, která nejsou připojená k hybridní službě Azure AD. 
- Všechna zařízení s jiným systémem než Windows 10 nebo Windows Server 2016.
- Všechna zařízení s jiným systémem než Windows. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>Otázka: Návody víte, co je stav registrace zařízení klienta?

**Odpověď:** V Azure Portal přejdete na **všechna zařízení**. Vyhledejte zařízení pomocí ID zařízení. Ověřte hodnotu ve sloupci Typ spojení. V některých případech je možné zařízení resetovat nebo obnovit z image. Je proto nutné také na zařízení ověřit stav registrace zařízení:

- U zařízení s Windows 10 a Windows Server 2016 nebo novějším `dsregcmd.exe /status`spusťte příkaz.
- Pro verze operačního systému nižší úrovně spusťte `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>Otázka: V Azure Portal se v informacích o uživateli zobrazuje záznam o zařízení. Zobrazuje se stav registrovaný na zařízení. Je správně nastavené používání podmíněného přístupu?

**Odpověď:** Stav připojení zařízení zobrazený v **deviceID**se musí shodovat se stavem ve službě Azure AD a splňovat všechna kritéria hodnocení pro podmíněný přístup. Další informace najdete v tématu [vyžadování spravovaných zařízení pro cloudovou aplikaci přístup s podmíněným přístupem](../conditional-access/require-managed-devices.md).

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices-"></a>Otázka: Proč se uživatelům zobrazí chybová zpráva s oznámením, že vaše organizace odstranila zařízení nebo že vaše organizace zařízení zakázala na svých zařízeních s Windows 10?

**Odpověď:** V zařízeních s Windows 10 připojených nebo registrovaných ve službě Azure AD se uživatelům vydává [primární obnovovací token (PRT)](concept-primary-refresh-token.md) , který umožňuje jednotné přihlašování. Platnost PRT vychází z validaity samotného zařízení. Tato zpráva se zobrazí, pokud je zařízení buď odstraněno, nebo zakázáno ve službě Azure AD, aniž by bylo potřeba zahájit akci ze samotného zařízení. Zařízení se dá v Azure AD odstranit nebo zakázat jedním z následujících scénářů: 

- Uživatel zařízení zakáže z portálu moje aplikace. 
- Správce (nebo uživatel) odstraní nebo zakáže zařízení v Azure Portal nebo pomocí PowerShellu.
- Jenom služba Hybrid Azure AD připojená: Správce odebere organizační jednotku zařízení z oboru synchronizace, což vede k odstranění zařízení z Azure AD.

Níže najdete informace o tom, jak mohou být tyto akce odstraněny.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>Otázka: Zařízení zakázal nebo odstranil (a) v Azure Portal nebo pomocí Windows PowerShellu. Ale místní stav zařízení říká, že je pořád zaregistrovaný. Co bych měl/a dělat?

**Odpověď:** Tato operace je záměrné. V takovém případě zařízení nemá přístup k prostředkům v cloudu. Správci můžou tuto akci provést u zastaralých, ztracených nebo odcizených zařízení, aby se zabránilo neoprávněnému přístupu. Pokud se tato akce prováděla omylem, budete muset zařízení znovu povolit nebo znovu zaregistrovat, jak je popsáno níže.

- Pokud je zařízení ve službě Azure AD zakázané, správce s dostatečnými oprávněními může povolit z portálu Azure AD.  

 - Pokud se zařízení ve službě Azure AD odstraní, musíte ho znovu zaregistrovat. Pokud se chcete znovu zaregistrovat, musíte na zařízení provést ruční akci. Pokyny k opětovné registraci na základě stavu zařízení najdete níže. 

      Pokud chcete znovu zaregistrovat zařízení Windows 10 a Windows Server 2016/2019 připojená k hybridní službě Azure AD, proveďte následující kroky:

      1. Otevřete příkazový řádek jako správce.
      1. Zadejte `dsregcmd.exe /debug /leave`.
      1. Odhlaste se a přihlaste se a aktivujte naplánovanou úlohu, která zařízení znovu zaregistruje ve službě Azure AD. 

      U verzí operačního systému Windows nižší úrovně, které jsou připojené k hybridní službě Azure AD, proveďte následující kroky:

      1. Otevřete příkazový řádek jako správce.
      1. Zadejte `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
      1. Zadejte `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

      Pro zařízení s Windows 10 připojená k Azure AD proveďte následující kroky:

      1. Otevřete příkazový řádek jako správce.
      1. Zadejte `dsregcmd /forcerecovery` (Poznámka: Chcete-li provést tuto akci, musíte být správcem.
      1. V dialogu, který se otevře, klikněte na Přihlásit se a pokračujte v procesu přihlašování.
      1. Odhlaste se a přihlaste se k zařízení a dokončete obnovení.

      U registrovaných zařízení s Windows 10 v Azure AD proveďte následující kroky:

      1. Přejděte na **Nastavení** > **účty** > **přístup do práce nebo do školy**. 
      1. Vyberte účet a vyberte **Odpojit**.
      1. Klikněte na + připojit a zaregistrujte zařízení znovu prostřednictvím procesu přihlášení.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>Otázka: Proč se v Azure Portal zobrazí duplicitní položky zařízení?

**Odpověď:**

- U systémů Windows 10 a Windows Server 2016 můžou opakované pokusy o odpojení a opětovné připojení ke stejnému zařízení způsobit duplicitní položky. 
- Každý uživatel systému Windows, který používá **Přidat pracovní nebo školní účet** , vytvoří nový záznam zařízení se stejným názvem zařízení.
- U verzí operačního systému Windows, které jsou připojené k místní službě Azure Directory Domain, vytvoří Automatická registrace nový záznam zařízení se stejným názvem zařízení pro každého uživatele domény, který se přihlásí k zařízení. 
- Počítač připojený k Azure AD, který se vymaže, znovu nainstaluje a znovu se připojí se stejným názvem, se zobrazí jako jiný záznam se stejným názvem zařízení.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>Otázka: Podporuje registrace zařízení s Windows 10 ve službě Azure AD čipy TPM v režimu FIPS?

**Odpověď:** Ne, aktuálně registrace zařízení ve Windows 10 pro všechny stavy zařízení – hybridní připojení ke službě Azure AD, připojení k Azure AD a zaregistrovanou službu Azure AD – nepodporuje čipy TPM v režimu FIPS. K úspěšnému připojení ke službě Azure AD a její registraci je potřeba, aby byl pro čipy TPM na těchto zařízeních vypnutý režim FIPS.

---

**Otázka: Proč může uživatel i nadále přistupovat k prostředkům ze zařízení, které je v Azure Portal zakázané?**

**Odpověď:** Použití odvolání může trvat až hodinu.

>[!NOTE] 
>U zaregistrovaných zařízení doporučujeme zařízení vymazat, abyste se ujistili, že uživatelé nebudou mít přístup k prostředkům. Další informace najdete v tématu [co je registrace zařízení?](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

## <a name="azure-ad-join-faq"></a>Nejčastější dotazy ke službě Azure AD JOIN

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>Otázka: Návody odpojte zařízení připojené k Azure AD místně na zařízení?

**Odpověď:** 
- Pro zařízení připojená k hybridní službě Azure AD se ujistěte, že jste automatickou registraci vypnuli. Naplánovaná úloha pak zařízení znovu neregistruje. Pak otevřete příkazový řádek jako správce a zadejte `dsregcmd.exe /debug /leave`. Nebo spusťte tento příkaz jako skript u několika zařízení, aby se hromadně odpojování nepřipojilo.
- U zařízení připojených k čisté službě Azure AD se ujistěte, že máte offline účet místního správce nebo si ho vytvořte. Nemůžete se přihlásit pomocí přihlašovacích údajů uživatele Azure AD. Potom přejděte na **Nastavení** > **účty** > **přístup do práce nebo do školy**. Vyberte svůj účet a vyberte **Odpojit**. Postupujte podle pokynů a po zobrazení výzvy zadejte přihlašovací údaje místního správce. Dokončete proces odpojování restartováním zařízení.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>Otázka: Můžou moji uživatelé přihlašovat se k zařízením připojeným k Azure AD, která jsou ve službě Azure AD Odstraněná nebo zakázaná?

**Odpověď:** Ano. Systém Windows má funkci uživatelského jména a hesla v mezipaměti, která umožňuje uživatelům, kteří se předtím přihlásili, získat přístup k ploše rychle i bez připojení k síti. 

Pokud je zařízení v Azure AD odstraněné nebo zakázané, není známé pro zařízení s Windows. Takže uživatelé, kteří se předtím přihlásili, budou mít přístup k ploše s uživatelským jménem a heslem v mezipaměti. I když je zařízení odstraněné nebo zakázané, uživatelé nemají přístup k žádným prostředkům chráněným pomocí podmíněného přístupu na základě zařízení. 

Uživatelé, kteří se předtím nedostali, nemají přístup k zařízení. Není k dispozici žádné uživatelské jméno a heslo pro ukládání do mezipaměti. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>Otázka: Může být zakázaný nebo odstraněný uživatel přihlášený k zařízením připojeným k Azure AD.

**Odpověď:** Ano, ale pouze po dobu omezeného času. Pokud je uživatel v Azure AD odstraněný nebo zakázaný, není hned známý na zařízení s Windows. Uživatelé, kteří si dříve přihlásili, mají přístup k ploše s uživatelským jménem a heslem v mezipaměti. 

Zařízení obvykle ví o stavu uživatele za méně než čtyři hodiny. Pak Windows zablokuje přístup uživatelů k ploše. Když je uživatel ve službě Azure AD odstraněný nebo zakázaný, všechny jejich tokeny se odvolají. Takže nemají přístup k žádným prostředkům. 

Odstraněné nebo zakázané uživatele, kteří se předtím nemohli přihlásit, nemůžou získat přístup k zařízení. Není k dispozici žádné uživatelské jméno a heslo pro ukládání do mezipaměti. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>Otázka: Proč mají moji uživatelé problémy na zařízeních připojených k Azure AD po změně svého hlavního názvu uživatele?

**Odpověď:** V současné době nejsou na zařízeních připojených k Azure AD plně podporované změny hlavního názvu uživatele (UPN). Ověřování pomocí služby Azure AD tak nebude po změně hlavního názvu uživatele (UPN) úspěšné. Výsledkem je, že uživatelé mají na svých zařízeních problémy jednotného přihlašování a podmíněného přístupu. V tuto chvíli se uživatelé potřebují přihlašovat k Windows prostřednictvím dlaždice "jiný uživatel" pomocí nového hlavního názvu uživatele (UPN) a tento problém vyřešit. V současnosti pracujeme na řešení tohoto problému. Uživatelé, kteří se přihlásí pomocí Windows Hello pro firmy, ale tento problém nečelí. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>Otázka: Moji uživatelé nemůžou hledat tiskárny ze zařízení připojených k Azure AD. Jak můžu povolit tisk z těchto zařízení?

**Odpověď:** Pokud chcete nasadit tiskárny pro zařízení připojená k Azure AD, přečtěte si téma [Nasazení hybridního cloudového tisku Windows serveru pomocí](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy)předběžného ověření. K nasazení hybridního cloudového tisku potřebujete místní Windows Server. V současné době není k dispozici cloudová služba tisku. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>Otázka: Návody se připojit ke vzdálenému zařízení připojenému k Azure AD?

**Odpověď:** Viz [připojení ke VZDÁLENÉMU počítači připojenému Azure Active Directory](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>Otázka: Proč uvidí moji uživatelé *, tady se tam nemůžete dostat*?

**Odpověď:** Nakonfigurovali jste určitá pravidla podmíněného přístupu tak, aby vyžadovala určitý stav zařízení? Pokud zařízení nesplňuje kritéria, uživatelé se zablokují a zobrazí se jim zpráva. Vyhodnoťte pravidla zásad podmíněného přístupu. Ujistěte se, že zařízení splňuje kritéria, abyste se vyhnuli této zprávě.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>Otázka: Proč někteří moji uživatelé neobdrželi výzvy k ověření Azure Multi-Factor Authentication na zařízeních připojených k Azure AD?

**Odpověď:** Uživatel se může ke službě Azure AD připojit nebo zaregistrovat zařízení pomocí služby Multi-Factor Authentication. Pak se samotné zařízení bude pro daného uživatele jednat o důvěryhodný druhý faktor. Když se stejný uživatel přihlásí k zařízení a přistupuje k aplikaci, služba Azure AD považuje zařízení za druhý faktor. Umožňuje tomuto uživateli bezproblémově přistupovat k aplikacím bez dalších výzev k Multi-Factor Authentication. 

Toto chování:

- Platí pro připojené služby Azure AD a zařízení registrovaná v Azure AD, ale ne pro zařízení připojená k hybridní službě Azure AD.
- Neplatí pro žádného jiného uživatele, který se přihlásí k tomuto zařízení. Všichni ostatní uživatelé, kteří přistupují k tomuto zařízení, získají výzvu k Multi-Factor Authentication. Pak mají přístup k aplikacím, které vyžadují službu Multi-Factor Authentication.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>Otázka: Proč se mi zobrazuje *uživatelské jméno nebo heslo je* nesprávná zpráva pro zařízení, které jste právě připojili ke službě Azure AD?

**Odpověď:** Mezi běžné důvody pro tento scénář patří následující:

- Vaše přihlašovací údaje uživatele již nejsou platné.
- Počítač nemůže komunikovat s Azure Active Directory. Vyhledejte všechny problémy s připojením k síti.
- Federované přihlašování vyžaduje federační server pro podporu koncových bodů WS-Trust, které jsou povolené a přístupné. 
- Povolili jste předávací ověřování. Takže vaše dočasné heslo se musí po přihlášení změnit.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>Otázka: Proč se mi zobrazuje *... došlo k chybě!* Dialogové okno při pokusu o připojení k počítači do Azure AD?

**Odpověď:** K této chybě dochází, když nastavíte registraci Azure Active Directory v Intune. Ujistěte se, že uživatel, který se pokouší o připojení k Azure AD, má přiřazenou správnou licenci Intune. Další informace najdete v tématu [Nastavení registrace pro zařízení s Windows](https://docs.microsoft.com/intune/windows-enroll).  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>Otázka: Proč se mi nepovedlo provést pokus o připojení k Azure AD, ale nedostali jsme žádné informace o chybě?

**Odpověď:** Pravděpodobnou příčinou je, že jste se k zařízení přihlásili pomocí místního předdefinovaného účtu správce. Než použijete Azure Active Directory připojit k dokončení instalace, vytvořte jiný místní účet. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>Q:What jsou certifikáty MS-Organization-P2P-Access, které jsou k dispozici na našich zařízeních s Windows 10?

**Odpověď:** Certifikáty MS-Organization-P2P-Access jsou vydávány službou Azure AD pro zařízení připojená k Azure AD a k hybridním zařízením připojeným k Azure AD. Tyto certifikáty se používají k umožnění vztahu důvěryhodnosti mezi zařízeními ve stejném tenantovi pro scénáře vzdálené plochy. K zařízení se vydá jeden certifikát a druhý uživatel ho vydá. Certifikát zařízení je přítomen v `Local Computer\Personal\Certificates` nástroji a je platný po dobu jednoho dne. Tento certifikát se obnoví (vydáním nového certifikátu), pokud je zařízení stále aktivní ve službě Azure AD. Uživatelský certifikát je přítomen v `Current User\Personal\Certificates` nástroji a tento certifikát je platný i po jednom dni, ale je vydaný na vyžádání, když se uživatel pokusí relaci vzdálené plochy k jinému zařízení připojenému k Azure AD. Po vypršení platnosti se neobnovuje. Oba tyto certifikáty jsou vydávány pomocí certifikátu MS-Organization-P2P-Access, který `Local Computer\AAD Token Issuer\Certificates`je k dispozici v části. Tento certifikát je vydaný službou Azure AD během registrace zařízení. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>Q:Why jsem na našich zařízeních s Windows 10 vystavilo více certifikátů s vypršenou platností, které vydala MS-Organization-P2P – přístup? Jak je můžu odstranit?

**Odpověď:** Došlo k chybě zjištěné ve Windows 10 verze 1709 a nižší, kde prošly platnost certifikátů zabezpečení MS-Organization-P2P-Access v úložišti počítače z důvodu kryptografických problémů. Vaši uživatelé mohou čelit problémům s připojením k síti, pokud používáte klienty VPN (například Cisco AnyConnect), který nedokáže zpracovat velký počet certifikátů s vypršenou platností. Tento problém byl opravený v systému Windows 10 1803, aby automaticky odstranil všechny tyto certifikáty s vypršenou platností v MS-Organization-P2P-Access. Tento problém můžete vyřešit tak, že své zařízení aktualizujete na Windows 10 1803. Pokud nemůžete aktualizovat, můžete tyto certifikáty odstranit bez jakýchkoli negativních dopadů.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Nejčastější dotazy k hybridní službě Azure AD

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>Otázka: Kde najdu informace o řešení potíží pro diagnostiku selhání připojení k hybridní službě Azure AD?

**Odpověď:** Informace o řešení potíží najdete v těchto článcích:

- [Řešení potíží se zařízeními s Windows 10 a Windows serverem 2016 s připojením k hybridním Azure Active Directory](troubleshoot-hybrid-join-windows-current.md)
- [Řešení potíží s modulem hybridní Azure Active Directory připojená zařízení nižší úrovně](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>Otázka: Proč se v seznamu zařízení Azure AD zobrazuje duplicitní záznam registrované služby Azure AD pro zařízení připojené k hybridní službě Azure AD ve Windows 10?

**Odpověď:** Když uživatelé přidají své účty do aplikací na zařízení připojeném k doméně, může se zobrazit výzva k **Přidání účtu do Windows?** Pokud na příkazovém řádku zadají **Ano** , zařízení se zaregistruje do služby Azure AD. Typ vztahu důvěryhodnosti je označený jako registrovaný pro Azure AD. Když ve vaší organizaci povolíte hybridní připojení k Azure AD, zařízení taky získá připojenou hybridní službu Azure AD. Pak se dvě stavy zařízení zobrazí pro stejné zařízení. 

Připojení k hybridní službě Azure AD má přednost před stavem zaregistrovánm službou Azure AD. Takže se vaše zařízení považuje za hybridní službu Azure AD připojené k ověřování a vyhodnocení podmíněného přístupu. Záznam zařízení registrovaných v Azure AD můžete bezpečně odstranit z portálu Azure AD. Naučte se [Tento duální stav na počítači s Windows 10 vyhnout nebo ho vyčistit](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know). 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>Otázka: Proč mají moji uživatelé problémy s hybridními zařízeními s Windows 10 připojenými k Azure AD po změně svého hlavního názvu uživatele?

**Odpověď:** Aktuálně se změny hlavního názvu uživatele (UPN) nejsou u hybridních zařízení připojených k Azure AD plně podporovány. I když se uživatelé můžou přihlásit k zařízení a přistupovat k místním aplikacím, ověřování pomocí služby Azure AD se po změně názvu UPN nezdařilo. Výsledkem je, že uživatelé mají na svých zařízeních problémy jednotného přihlašování a podmíněného přístupu. V tuto chvíli je potřeba odpojte zařízení ze služby Azure AD (spusťte dsregcmd/Leave se zvýšenými oprávněními) a znovu se připojte (automaticky se stane) a problém vyřešit. V současnosti pracujeme na řešení tohoto problému. Uživatelé, kteří se přihlásí pomocí Windows Hello pro firmy, ale tento problém nečelí. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>Otázka: Vyžadují zařízení připojená k hybridní službě Azure AD s Windows 10 pro přístup k prostředkům cloudu dohled nad řadičem domény?

**Odpověď:** Ne, s výjimkou případů, kdy došlo ke změně hesla uživatele Po dokončení připojení hybridní služby Azure AD pro Windows 10 a uživatel se aspoň jednou přihlásil, zařízení nevyžaduje pro přístup k prostředkům cloudu dohled nad řadičem domény. Windows 10 může získat jednotné přihlašování k aplikacím Azure AD odkudkoli prostřednictvím připojení k Internetu, s výjimkou změny hesla. Uživatelé, kteří se přihlásí pomocí Windows Hello pro firmy, pokračují v získání jednotného přihlašování k aplikacím Azure AD i po změně hesla, a to i v případě, že nemají dohled nad svým řadičem domény. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>Otázka: Co se stane, když uživatel změní heslo a pokusí se přihlásit ke svému zařízení připojenému k hybridní službě Azure AD s Windows 10 mimo podnikovou síť?

**Odpověď:** Pokud se heslo změní mimo podnikovou síť (například pomocí Azure AD SSPR), přihlášení uživatele pomocí nového hesla se nezdaří. Pro zařízení připojená k hybridní službě Azure AD je místní služba Active Directory primární autoritou. Pokud zařízení nemá k řadiči domény dohled, nemůže nové heslo ověřit. Proto musí uživatel navázat připojení s řadičem domény (buď prostřednictvím sítě VPN, nebo v podnikové síti) předtím, než se bude moci přihlásit k zařízení pomocí nového hesla. V opačném případě se můžou přihlásit jenom se starým heslem z důvodu funkce přihlašování v mezipaměti ve Windows. Ve službě Azure AD se ale během požadavků na tokeny zruší původní heslo, a proto se zabrání jednotnému přihlašování a nezdařily se všechny zásady podmíněného přístupu na základě zařízení. K tomuto problému dochází, pokud používáte Windows Hello pro firmy. 

---

## <a name="azure-ad-register-faq"></a>Nejčastější dotazy k registru Azure AD

### <a name="q-can-i-register-android-or-ios-byod-devices"></a>Otázka: Můžu zaregistrovat zařízení s Androidem nebo iOS BYOD?

**Odpověď:** Ano, ale jenom se službou Azure Device Registration Service a pro hybridní zákazníky. U místní služby Device Registration Service v Active Directory Federation Services (AD FS) (AD FS) se nepodporuje.

### <a name="q-how-can-i-register-a-macos-device"></a>Otázka: Jak můžu zaregistrovat zařízení macOS?

**Odpověď:** Proveďte následující kroky:

1.  [Vytvoření zásady dodržování předpisů](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
1.  [Definování zásad podmíněného přístupu pro zařízení macOS](../active-directory-conditional-access-azure-portal.md) 

**Mark**

- Uživatelé zahrnutí do zásad podmíněného přístupu potřebují pro přístup k prostředkům [podporovanou verzi Office pro MacOS](../conditional-access/technical-reference.md#client-apps-condition) . 
- Při prvním pokusu o přístup se uživatelům zobrazí výzva k registraci zařízení pomocí portálu společnosti.

## <a name="next-steps"></a>Další postup

- Další informace o [zařízeních registrovaných v Azure AD](concept-azure-ad-register.md)
- Další informace o [zařízeních připojených k Azure AD](concept-azure-ad-join.md)
- Další informace o [zařízeních připojených k hybridní službě Azure AD](concept-azure-ad-join-hybrid.md)
