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
ms.openlocfilehash: 8d1e44eae7e87a450ac5f36e621d559fca92ca74
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016150"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory nejčastější dotazy ke správě zařízení

**DOTAZ: Nedávno jsem registraci zařízení. Proč nelze zobrazit zařízení v části Moje informace o uživateli na webu Azure Portal? Nebo proč je vlastník zařízení označeno jako není k dispozici pro hybridních zařízení připojených k Azure AD? ** 
 **A:** Zařízení s Windows 10, která jsou připojená k hybridní Azure AD nezobrazí v zařízení uživatele.
Budete muset použít zobrazení všech zařízení na webu Azure portal. Můžete také použít PowerShell [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) rutiny.

Následující zařízení jsou uvedeny v zařízení uživatele:

- Připojené k všechna osobní zařízení, které nejsou hybridní službě Azure AD. 
- Všechny bez Windows 10 a zařízení Windows serveru 2016.
- Všechna zařízení bez Windows 

--- 

**DOTAZ: Jak zjistím, co je stav registrace zařízení klienta?**

**ODPOVĚĎ:** Můžete pomocí webu Azure portal, přejděte na všech zařízeních a vyhledat zařízení pomocí ID zařízení. Zkontrolujte hodnotu ve sloupci Typ spojení. V některých případech zařízení by byly resetovat nebo obnoví z Image. Proto je nutné také zkontrolovat stav registrace zařízení na zařízení příliš:

- Pro Windows 10 a Windows Server 2016 nebo novějším spusťte dsregcmd.exe/status.
- Verze operačního systému nižší úrovně spusťte "%programFiles%\Microsoft pracoviště Join\autoworkplace.exe"

---

**DOTAZ: Můžu zobrazit záznam zařízení v části informace o uživateli na webu Azure Portal a můžete prohlédnout stav, jak na zařízení zaregistrovaná. Teď můžu nastavit správně pro používání podmíněného přístupu?**

**ODPOVĚĎ:** Stav připojení k zařízení, projeví podle ID zařízení, musí dodržovat, které v Azure AD a splnění libovolného kritéria hodnocení pro podmíněný přístup. Další informace najdete v tématu [vyžadují spravovaná zařízení pro přístup k aplikaci cloud s podmíněným přístupem](../conditional-access/require-managed-devices.md).

---

**DOTAZ: Odstraněný na webu Azure Portal nebo pomocí Windows Powershellu, ale místní stavu v zařízení říká, že je stále zaregistrovaná?**

**ODPOVĚĎ:** Toto chování je úmyslné. Zařízení nebude mít přístup k prostředkům v cloudu. 

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

**DOTAZ: Proč se zobrazují položky duplicitní zařízení na webu Azure portal?**

**ODPOVĚĎ:**

-   Pro Windows 10 a Windows Server 2016 Pokud jsou opakované pokusy o zrušení služby a znovu vstoupit stejného zařízení, může být duplicitní položky. 

-   Pokud jste použili přidat pracovní nebo školní účet, každý uživatel systému windows, který používá přidat pracovní nebo školní účet vytvoří nový záznam zařízení s tímto názvem zařízení.

-   Pro verze operačního systému Windows nižší úrovně, které jsou v místním AD připojených k doméně pomocí automatické registrace vytvoří nový záznam zařízení se stejným názvem zařízení pro každého uživatele domény, který se přihlásí k zařízení. 

-   Připojené k počítači s Azure AD, který má vymazáno, přeinstalovat a znovu vstoupit se stejným názvem, se zobrazí jako jiný záznam se stejným názvem zařízení.

---

**DOTAZ: Proč může uživatel dál přístup k prostředkům ze zařízení, které můžu zakázali na webu Azure Portal?**

**ODPOVĚĎ:** Může trvat až hodinu revoke uplatňovat.

>[!Note] 
>Pro zaregistrovaná zařízení doporučujeme, abyste vymazání obsahu zařízení k zajištění, že uživatelé nemají přístup k prostředkům. Další informace najdete v tématu [registrovat zařízení pro správu v Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

## <a name="azure-ad-join-faq"></a>Nejčastější dotazy k Azure AD Join

**DOTAZ: Jak můžu zrušení služby zařízení připojeno k Azure AD s místně na zařízení?**

**ODPOVĚĎ:** 
- Hybridní zařízení připojeno k Azure AD Ujistěte se, že chcete vypnout automatické registrace, aby se naplánovaná úloha nezaregistroval zařízení znovu. Dále otevřete příkazový řádek jako správce a zadejte `dsregcmd.exe /debug /leave`. Alternativně můžete tento příkaz Spustit jako skript v různých zařízeních na zrušení služby hromadně.

- Pro čistě připojeno k Azure AD zařízení, ujistěte se, že jste správcem offline místní účet nebo vytvořit novou, nebudou moct přihlásit pomocí libovolné přihlašovací údaje uživatele Azure AD. Dále přejděte na **nastavení** > **účty** > **přístup do práce nebo do školy**. Vyberte svůj účet a klikněte na **odpojit**. Postupujte podle zobrazených výzev a zadejte přihlašovací údaje místního správce, po zobrazení výzvy. Restartujte zařízení provést odpojení.

---

**DOTAZ: Můžete svým uživatelům přihlásit k zařízení připojená k Azure AD, která byla odstraněna, nebo zakázaný ve službě Azure AD? ** 
 **A:** Ano. Windows uložil do mezipaměti přihlašovací možnost povolit, dříve přihlášení uživatelé pro přístup k ploše rychle i bez připojení k síti. Pokud zařízení se odstraní nebo je zakázaný ve službě Azure AD, není znám do zařízení Windows. Takže dříve přihlášení uživatelé můžou dál přístup k ploše s přihlášení uložené v mezipaměti. Ale jak zařízení se odstraní nebo zakázán, uživatelé nemají přístup k prostředkům nechrání podmíněným přístupem na základě zařízení. 

Uživatelé, kteří se ještě nepřihlásili nelze přístupu k zařízení, protože není k dispozici žádné uložené v mezipaměti přihlášení povolený pro ně. 

---

**DOTAZ: Můžete zakázané nebo odstraněných uživatelů přihlásit k zařízení připojených k Azure AD? ** 
 **A:** Ano, ale pouze po omezenou dobu. Když uživatel se odstranil nebo zakázaný ve službě Azure AD, není okamžitě známé zařízení Windows. Takže dříve přihlášení uživatelé mají přístup k ploše s přihlášení uložené v mezipaměti. Jakmile je zařízení přehled o stavu uživatele (obvykle v méně než 4 hodinami), Windows zablokuje uživatelům přístup k ploše. Jako uživatel se odstraní nebo je zakázaný ve službě Azure AD, se jejich tokeny odvolá, nemají přístup k žádným prostředkům. 

Odstraněné nebo zakázaných uživatelů, kteří dříve nepřihlásili nelze přístup k zařízení, protože není k dispozici žádné uložené v mezipaměti přihlášení povolený pro ně. 

---

**DOTAZ: Moji uživatelé nemůže vyhledat tiskárny ze zařízení připojeno k Azure AD. Jak můžu povolit tisk ze zařízení připojeno k Azure AD?**

**ODPOVĚĎ:** Nasazení tiskáren pro zařízení připojeno k Azure AD, najdete v části [hybridní cloud print](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Budete potřebovat Windows serveru v místním nasazení hybridního cloudu tisk. V současné době není k dispozici tiskové služby založené na cloudu. 

---

**DOTAZ: Jak se připojit k vzdálené službě Azure AD na zařízení? ** 
 **A:** Najdete v článku https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc podrobnosti.

---

**DOTAZ: Proč Moji uživatelé vidí "Se tam nelze dostat z tohoto"?**

**ODPOVĚĎ:** Pokud jste nakonfigurovali určitá pravidla podmíněného přístupu tak, aby vyžadovala stavu konkrétní zařízení a zařízení nesplňuje kritéria, jsou blokovány, uživatele a zobrazí tato zpráva. Vyhodnocení pravidla zásad podmíněného přístupu a ujistěte se, že zařízení je možné ke splnění kritérií, aby se tato zpráva.

---

**DOTAZ: Proč některá Moje uživatelů není MFA výzev v Azure AD získáte připojené zařízení?**

**ODPOVĚĎ:** Pokud se uživatel připojí nebo zaregistruje zařízení s Azure AD pomocí služby Multi-Factor Authentication, bude samotné zařízení důvěryhodné druhý faktor pro konkrétního uživatele. Následně pokaždé, když se stejný uživatel přihlásí k zařízení a přistupuje k aplikaci, bude považovat za zařízení, jako druhý faktor Azure AD a umožňuje daného uživatele a bezproblémově přistupovat k jejich aplikacím bez dalších výzev vícefaktorové ověřování. Toto chování neplatí pro žádný jiný uživatel, přihlášení k zařízení, aby všichni uživatelé přístup k tomuto zařízení by stále výzva s výzvu MFA před přístupem k aplikacím, které vyžadují vícefaktorové ověřování.

---

**DOTAZ: Proč zobrazí se zpráva "uživatelské jméno nebo heslo je chybné." pro zařízení, které jsem právě připojená k Azure AD?**

**ODPOVĚĎ:** Běžné důvody pro tento scénář jsou:

- Vaše přihlašovací údaje uživatele už nejsou platné.

- Počítač je schopen komunikovat se službou Azure Active Directory. Vyhledejte všechny problémy se síťovým připojením.

- Federované přihlášení už budou vyžadovat federačním serveru pro podporu koncových bodů WS-Trust povolené a přístupné. 

- Povolíte předávací ověřování a uživatel má dočasné heslo, které je potřeba změnit přihlášení.

---

**DOTAZ: Proč se zobrazuje "zobrazí... došlo k chybě!" dialogové okno, když se snažím se Azure AD join počítač?**

**ODPOVĚĎ:** To je výsledkem nastavení registrace Azure Active Directory s Intune. Ujistěte se, že uživatel pokouší provést připojení k Azure AD má správné přiřazenou licenci Intune. Další informace najdete v tématu [nastavení správy pro zařízení Windows](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment).  

---

**DOTAZ: Proč mého pokusu o do služby Azure AD join selhání PC i když mi nepřišel. jakékoli informace o chybách?**

**ODPOVĚĎ:** Pravděpodobnou příčinou je, že je uživatel přihlášen k zařízení pomocí místní předdefinovaný účet správce. Než začnete používat Azure Active Directory Join k dokončení instalace vytvořte jiného místního účtu. 

---

## <a name="hybrid-azure-ad-join-faq"></a>Nejčastější dotazy týkající se hybridní připojení k Azure AD

**DOTAZ: Kde můžu najít Poradce při potížích s informace pro diagnostikování selhání připojení k hybridní službě Azure AD?**

**ODPOVĚĎ:** Informace o odstraňování potíží naleznete v tématu:

- [Řešení potíží s Automatická registrace domény připojené počítače do služby Azure AD – Windows 10 a Windows serveru 2016](troubleshoot-hybrid-join-windows-current.md)

- [Řešení potíží s Automatická registrace domény počítačů připojená k Azure AD pro klienty Windows nižší úrovně](troubleshoot-hybrid-join-windows-legacy.md)
 
**DOTAZ: Proč vidím duplicitní Azure AD registrované záznam pro moje Windows 10 hybridní službě Azure AD připojené zařízení v seznamu zařízení služby Azure AD?**

**ODPOVĚĎ:** Když uživatelé přidat svůj účet do aplikace na zařízení připojené k doméně, může být vyzve k "Přidat účet do Windows?". Kliknutím na "Ano" na příkazovém řádku by mohlo dojít k zařízení zaregistrovat u služby Azure AD a typ důvěryhodnosti označen jako registrováno v Azure AD. Jakmile povolíte připojení k hybridní službě Azure AD ve vaší organizaci, získají zařízení připojená k hybridní Azure AD. V důsledku toho budou existovat dva stavy zařízení zobrazovat pro stejné zařízení. Připojení k hybridní službě Azure AD, ale má přednost před stavu registrováno v Azure AD. Takže zařízení se budou považovat za hybridní připojení k Azure AD pro všechny ověřovací a vyhodnocení podmíněného přístupu. Ano můžete bezpečně odstranit záznam zařízení registrováno v Azure AD z portálu Azure AD. Přečtěte si prosím [v této části](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know) v hybridním Azure AD připojit článek pochopit, jak se vyhnout nebo čištění tohoto duální stavu na počítači s Windows 10. 

---

## <a name="azure-ad-register-faq"></a>Nejčastější dotazy týkající se registrace Azure AD

**DOTAZ: Můžete zaregistrovat zařízení s Androidem nebo iOS BYOD?**

**ODPOVĚĎ:** Ano, ale pouze službě Azure device registration service a pro zákazníky využívající hybridní řešení. Nepodporuje se místní službu device registration service ve službě AD FS.

**DOTAZ: Jak se můžete zaregistrovat zařízení s macOS?**

**ODPOVĚĎ:** Postup pro registraci zařízení s macOS:

1.  [Vytvoření zásad dodržování předpisů](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [Definovat zásady podmíněného přístupu pro zařízení s macOS](../active-directory-conditional-access-azure-portal.md) 

**Poznámky:**

- Uživatelé, které jsou součástí své zásady podmíněného přístupu, musí [podporovanou verzi sady Office pro macOS](../conditional-access/technical-reference.md#client-apps-condition) pro přístup k prostředkům. 

- Při prvním pokusu o přístup vaši uživatelé vyzváni k registraci zařízení pomocí portálu společnosti.

---
