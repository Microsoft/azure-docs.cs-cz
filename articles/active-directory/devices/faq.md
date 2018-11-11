---
title: Azure Active Directory nejčastější dotazy ke správě zařízení | Dokumentace Microsoftu
description: Azure Active Directory zařízení nejčastější dotazy ke správě.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 9402147e2dab7fbf52fc893f339f6f3b8e112377
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2018
ms.locfileid: "51515637"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory nejčastější dotazy ke správě zařízení

**Otázka: nemůžu nedávno registraci zařízení. Proč nelze zobrazit zařízení v části Moje informace o uživateli na webu Azure Portal? Nebo proč je vlastník zařízení označeno jako není k dispozici pro hybridních zařízení připojených k Azure AD? ** 
 **A:** zařízení s Windows 10, která jsou připojená k hybridní Azure AD není uveden v části zařízení uživatelů.
Budete muset použít zobrazení všech zařízení na webu Azure portal. Můžete také použít PowerShell [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) rutiny.

Následující zařízení jsou uvedeny v zařízení uživatele:

- Připojené k všechna osobní zařízení, které nejsou hybridní službě Azure AD. 
- Všechny bez Windows 10 a zařízení Windows serveru 2016.
- Všechna zařízení bez Windows 

--- 

**Otázka: Jak mohu vědět, co je stav registrace zařízení klienta?**

**Odpověď:** můžete pomocí webu Azure portal, přejděte na všech zařízeních a vyhledat zařízení pomocí ID zařízení. Zkontrolujte hodnotu ve sloupci Typ spojení. V některých případech zařízení by byly obnovit nebo znovu vytvoření bitové kopie. Proto je nutné také zkontrolovat stav registrace zařízení na zařízení příliš:

- Pro Windows 10 a Windows Server 2016 nebo novějším spusťte dsregcmd.exe/status.
- Verze operačního systému nižší úrovně spusťte "%programFiles%\Microsoft pracoviště Join\autoworkplace.exe"

---

**Otázka: Mohu zobrazit záznam zařízení v části informace o uživateli na webu Azure Portal a můžete prohlédnout stav, jak na zařízení zaregistrovaná. Jsem, že správně nastavit pro používání podmíněného přístupu?**

**Odpověď:** stavu připojení k pracovišti zařízení, projeví podle ID zařízení, musí dodržovat, které v Azure AD a splnění libovolného kritéria hodnocení pro podmíněný přístup. Další informace najdete v tématu [vyžadují spravovaná zařízení pro přístup k aplikaci cloud s podmíněným přístupem](../conditional-access/require-managed-devices.md).

---

**Otázka: nemůžu odstranily na webu Azure Portal nebo pomocí Windows Powershellu, ale místní stavu v zařízení říká, že je stále zaregistrovaná?**

**Odpověď:** toto chování je záměrné. Zařízení nebude mít přístup k prostředkům v cloudu. 

Pokud chcete znovu zaregistrovat znovu, manuální akce musí být přejdete na zařízení. 

Vymazat stav spojení z Windows 10 a Windows Server 2016, které jsou v místním AD připojených k doméně:

1.  Otevřete příkazový řádek jako správce.

2.  Typ `dsregcmd.exe /debug /leave`

3.  Odhlaste se a přihlaste se k aktivaci naplánované úlohy, který zaregistruje zařízení s Azure AD znovu. 

Pro verze operačního systému Windows nižší úrovně, které jsou v místním AD připojených k doméně:

1.  Otevřete příkazový řádek jako správce.
2.  Zadejte `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Zadejte `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---

**Otázka: Proč vidím zařízení duplicitní položky na webu Azure portal?**

**ODPOVĚĎ:**

-   Pro Windows 10 a Windows Server 2016 Pokud jsou opakované pokusy o zrušení služby a znovu vstoupit stejného zařízení, může být duplicitní položky. 

-   Pokud jste použili přidat pracovní nebo školní účet, každý uživatel systému windows, který používá přidat pracovní nebo školní účet vytvoří nový záznam zařízení s tímto názvem zařízení.

-   Pro verze operačního systému Windows nižší úrovně, které jsou v místním AD připojených k doméně pomocí automatické registrace vytvoří nový záznam zařízení se stejným názvem zařízení pro každého uživatele domény, který se přihlásí k zařízení. 

-   Připojené k počítači s Azure AD, který má vymazáno, přeinstalovat a znovu vstoupit se stejným názvem, se zobrazí jako jiný záznam se stejným názvem zařízení.

---

**Otázka: Proč může uživatel dál přístup k prostředkům ze zařízení, které můžu zakázali na webu Azure Portal?**

**Odpověď:** může trvat až hodinu revoke uplatňovat.

>[!Note] 
>Pro zaregistrovaná zařízení doporučujeme, abyste vymazání obsahu zařízení k zajištění, že uživatelé nemají přístup k prostředkům. Další informace najdete v tématu [registrovat zařízení pro správu v Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 
---

# <a name="azure-ad-join-faq"></a>Nejčastější dotazy k Azure AD Join

**Otázka: Jak můžu zrušení služby zařízení připojeno k Azure AD s místně na zařízení?**

**ODPOVĚĎ:** 
- Hybridní zařízení připojeno k Azure AD Ujistěte se, že chcete vypnout automatické registrace, aby se naplánovaná úloha nezaregistroval zařízení znovu. Dále otevřete příkazový řádek jako správce a zadejte `dsregcmd.exe /debug /leave`. Alternativně můžete tento příkaz Spustit jako skript v různých zařízeních na zrušení služby hromadně.

- Pro čistě připojeno k Azure AD zařízení, ujistěte se, že jste správcem offline místní účet nebo vytvořit novou, nebudou moct přihlásit pomocí libovolné přihlašovací údaje uživatele Azure AD. Dále přejděte na **nastavení** > **účty** > **přístup do práce nebo do školy**. Vyberte svůj účet a klikněte na **odpojit**. Postupujte podle zobrazených výzev a zadejte přihlašovací údaje místního správce, po zobrazení výzvy. Restartujte zařízení provést odpojení.

---

**Otázka: Moje uživatelů nemůže vyhledat tiskárny ze zařízení připojeno k Azure AD. Jak můžu povolit tisk ze zařízení připojeno k Azure AD?**

**Odpověď:** nasazení tiskáren pro zařízení připojeno k Azure AD, najdete v části [hybridní cloud print](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Budete potřebovat Windows serveru v místním nasazení hybridního cloudu tisk. V současné době není k dispozici tiskové služby založené na cloudu. 

---

**Otázka: jak se připojit k vzdálené službě Azure AD připojené zařízení? ** 
 **A:** najdete v článku https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc podrobnosti.

---

**Otázka: Proč Moji uživatelé vidí "Se tam nelze dostat z tohoto"?**

**Odpověď:** Pokud jste nakonfigurovali určitá pravidla podmíněného přístupu tak, aby vyžadovala stavu konkrétní zařízení a zařízení nesplňuje kritéria, jsou zablokované uživatele a zobrazí tato zpráva. Vyhodnocení pravidla zásad podmíněného přístupu a ujistěte se, že zařízení může ke splnění kritérií, aby se tato zpráva.

---

**Otázka: Proč některé Moji uživatelé dělat get výzvy MFA není na Azure AD připojené zařízení?**

**Odpověď:** Pokud se uživatel připojí nebo zaregistruje zařízení s Azure AD pomocí aplikace Multi-Factor auth, samotné zařízení se stane důvěryhodným druhý faktor pro konkrétního uživatele. Následně pokaždé, když se stejný uživatel přihlásí k zařízení a přistupuje k aplikaci, bude považovat za zařízení, jako druhý faktor Azure AD a umožňuje daného uživatele a bezproblémově přistupovat k jejich aplikacím bez dalších výzev vícefaktorové ověřování. Toto chování neplatí pro žádný jiný uživatel, přihlášení k zařízení, aby všichni uživatelé přístup k tomuto zařízení by stále výzva s výzvu MFA před přístupem k aplikacím, které vyžadují vícefaktorové ověřování.

---

**Otázka: Proč zobrazí se zpráva "uživatelské jméno nebo heslo je chybné." pro zařízení, které jsem právě připojená k Azure AD?**

**Odpověď:** běžné důvody pro tento scénář:

- Vaše přihlašovací údaje uživatele už nejsou platné.

- Počítač je schopen komunikovat se službou Azure Active Directory. Vyhledejte všechny problémy se síťovým připojením.

- Federované přihlášení vyžaduje federačním serveru pro podporu koncových bodů WS-Trust povolené a přístupné. 

- Povolíte předávací ověřování a uživatel má dočasné heslo, které je potřeba změnit přihlášení.

---

**Otázka: Proč se zobrazuje "zobrazí... došlo k chybě!" dialogové okno, když se snažím se Azure AD join počítač?**

**Odpověď:** to je výsledkem nastavení registrace Azure Active Directory s Intune. Ujistěte se prosím, že uživatel pokouší provést připojení k Azure AD má správné přiřazenou licenci Intune. Další informace najdete v tématu [nastavení správy pro zařízení Windows](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment).  

---

**Otázka: Proč mého pokusu o do služby Azure AD join selhání PC i když mi nepřišel. jakékoli informace o chybách?**

**Odpověď:** pravděpodobnou příčinou je, že je uživatel přihlášen k zařízení pomocí místní předdefinovaný účet správce. Než začnete používat Azure Active Directory Join k dokončení instalace vytvořte jiný místní účet. 

---

# <a name="hybrid-azure-ad-join-faq"></a>Nejčastější dotazy týkající se hybridní připojení k Azure AD

**D: kde lze najít Poradce při potížích s informace pro diagnostikování selhání připojení k hybridní službě Azure AD?**

**Odpověď:** informace o odstraňování potíží naleznete v tématu:

- [Řešení potíží s automatickou registrací domény připojené počítače do služby Azure AD – Windows 10 a Windows serveru 2016](troubleshoot-hybrid-join-windows-current.md)

- [Řešení potíží s automatickou registrací domény počítačů připojená k Azure AD pro klienty Windows nižší úrovně](troubleshoot-hybrid-join-windows-legacy.md)
 

---

# <a name="azure-ad-register-faq"></a>Nejčastější dotazy týkající se registrace Azure AD

**Dotaz: lze zaregistrovat vlastní zařízení s Androidem nebo iOS?**

**Odpověď:** Ano, ale pouze službě Azure device registration service a pro zákazníky využívající hybridní řešení. Nepodporuje se místní službu device registration service ve službě AD FS.

**Otázka: jak můžete zaregistrovat zařízení s macOS?**

**Odpověď:** k registraci zařízení s macOS:

1.  [Vytvoření zásad dodržování předpisů](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [Definovat zásady podmíněného přístupu pro zařízení s macOS](../active-directory-conditional-access-azure-portal.md) 

**Poznámky:**

- Uživatelé, které jsou součástí své zásady podmíněného přístupu, musí [podporovanou verzi sady Office pro macOS](../conditional-access/technical-reference.md#client-apps-condition) pro přístup k prostředkům. 

- Při prvním pokusu o přístup vaši uživatelé vyzváni k registraci zařízení pomocí portálu společnosti.

---
