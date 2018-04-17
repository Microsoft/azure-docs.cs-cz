---
title: Azure Active Directory nejčastější dotazy ke správě zařízení | Microsoft Docs
description: Azure Active Directory zařízení nejčastější dotazy ke správě.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 4358b57284721642957d56ad8cfeea2b0f53fd89
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory nejčastější dotazy ke správě zařízení



**Otázka: jak můžete zaregistrovat zařízení systému macOS?**

**Odpověď:** k registraci zařízení v systému macOS:

1.  [Vytvoření zásady dodržování předpisů](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [Definovat zásady podmíněného přístupu pro zařízení v systému macOS](active-directory-conditional-access-azure-portal.md) 

**Poznámky:**

- Uživatelé, které jsou součástí zásady podmíněného přístupu musí [podporovaná verze systému Office pro systému macOS](active-directory-conditional-access-technical-reference.md#client-apps-condition) přístup k prostředkům. 

- Při první pokus o přístup uživatelům se zobrazí výzva k registraci zařízení pomocí portálu společnosti.

---

**Otázka: I nedávno zaregistrovaná zařízení. Proč nevidím zařízení v části Moje informace o uživateli na portálu Azure?**

**Odpověď:** zařízení s Windows 10, které jsou připojené k Azure AD hybridní není zobrazí na zařízení uživatelů.
Budete muset použít PowerShell zobrazíte všechna zařízení. 

Následující zařízení jsou uvedené v části zařízení uživatelů:

- Připojené všechny osobní zařízení, která nejsou hybridní Azure AD. 
- Všechny bez – Windows 10 nebo Windows Server 2016 zařízení.
- Všechna zařízení jiný systém než Windows 

---

**Otázka: Proč nevidím všechna zařízení zaregistrované v Azure Active Directory na portálu Azure?** 

**Odpověď:** se nyní zobrazí je v části adresář Azure AD -> všechny nabídky zařízení. Prostředí Azure PowerShell můžete taky najít všechna zařízení. Další podrobnosti najdete v tématu [Get-MsolDevice](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) rutiny.

--- 

**Otázka: Jak poznám, jaký je stav registrace zařízení klienta?**

**Odpověď:** pro Windows 10 a Windows Server 2016 nebo novější zařízení, spusťte/dsregcmd.exe status.

Verze operačního systému nižší úrovně spusťte "%programFiles%\Microsoft síti na pracovišti Join\autoworkplace.exe"

---

**Otázka: Proč je zařízení odstraněný na portálu Azure nebo pomocí prostředí Windows PowerShell pořád objevuje as registrované?**

**Odpověď:** toto chování je záměrné. Zařízení nebude mít přístup k prostředkům v cloudu. Pokud chcete znovu zaregistrovat znovu, musí být manuální akce mají být provedeny v zařízení. 

Vymazat stav spojení z Windows 10 a Windows Server 2016, které jsou místní AD připojený k doméně:

1.  Otevřete příkazový řádek jako správce.

2.  Typ `dsregcmd.exe /debug /leave`

3.  Odhlásit se a přihlaste se k aktivaci naplánovanou úlohu, která registruje zařízení s Azure AD znovu. 

Verze operačního systému Windows nižší úrovně, které jsou místní AD připojené k doméně:

1.  Otevřete příkazový řádek jako správce.
2.  Zadejte `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Zadejte `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---

**Otázka: Proč vidí položky duplicitní zařízení na portálu Azure?**

**ODPOVĚĎ:**

-   Pro Windows 10 a Windows Server 2016 Pokud existují opakovaných pokusech o zrušení služby a znova připojit do stejného zařízení, může být duplicitní položky. 

-   Pokud jste použili přidat pracovní nebo školní účet, bude každý uživatel systému windows, který používá přidat pracovní nebo školní účet vytvořit nový záznam zařízení se stejným názvem zařízení.

-   Verze operačního systému Windows nižší úrovně, které jsou místní AD připojené k doméně pomocí automatické registrace vytvoří nový záznam zařízení s stejný název zařízení pro každého uživatele domény, který se přihlásí do zařízení. 

-   Počítači připojeném k Azure AD, který vymaže, přeinstalovat a znovu připojený se stejným názvem, se zobrazí jako jiný záznam se stejným názvem zařízení.

---

**Otázka: Proč může uživatel i nadále přístup k prostředkům ze zařízení, které I zakázali na portálu Azure?**

**Odpověď:** může trvat až jednu hodinu pro odvolání má být použita.

>[!Note] 
>Pro zaregistrovaná zařízení doporučujeme vymazání obsahu zařízení k zajištění, aby uživatelé nemohli přistupovat k prostředkům. Další podrobnosti najdete v tématu [registrovat zařízení pro správu v Intune](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 


---

**Otázka: Proč Moji uživatelé vidí "Tam nedostanete odsud"?**

**Odpověď:** Pokud jste nakonfigurovali určitá pravidla podmíněného přístupu tak, aby vyžadovala stavu konkrétní zařízení a zařízení nesplňuje kritéria, uživatelé se zablokuje a zobrazí tato zpráva. Vyhodnocení pravidla zásad podmíněného přístupu a zajistěte, aby byla zařízení moct splňují kritéria, zobrazí se zpráva.

---


**Otázka: I najdete v části zařízení záznam zařízení v části informace o uživateli na webu Azure portal a můžete zobrazit stav as registrované na straně klienta. Se, že správně nastavit pro použití podmíněného přístupu?**

**Odpověď:** stavu připojení k zařízení, projeví podle ID zařízení, musí shodovat s třídou na Azure AD a musí splňovat všechny vyhodnocení kritérií pro podmíněný přístup. Další podrobnosti najdete v tématu [Začínáme s Azure Active Directory Device Registration](active-directory-device-registration.md).

---

**Otázka: Proč se zobrazila zpráva "uživatelské jméno nebo heslo je chybné." pro zařízení, které I právě připojené ke službě Azure AD?**

**Odpověď:** běžných příčin pro tento scénář jsou:

- Pověření uživatele již není platný.

- Počítač je schopen komunikovat s Azure Active Directory. Zkontrolujte všechny problémy se síťovým připojením.

- Azure AD Join požadavky nebyly splněny. Zkontrolujte, že jste provedli kroky pro [rozšíření možností cloudu k zařízení s Windows 10 prostřednictvím Azure Active Directory Join](active-directory-azureadjoin-overview.md).  

- Federované přihlášení vyžaduje federačním serveru pro podporu WS-Trust aktivní koncový bod. 

---

**Otázka: Proč se zobrazuje "Oops... došlo k chybě!" dialogové okno, když se Pokouším se Azure AD join Můj počítač?**

**Odpověď:** jedná o výsledek nastavení registrace Azure Active Directory s Intune. Přesvědčte se, zda má uživatel pokouší provést připojení k Azure AD přiřazenou správnou licenci pro Intune. Další podrobnosti najdete v tématu [nastavení správy pro zařízení Windows](https://docs.microsoft.com/intune/deploy-use/set-up-windows-device-management-with-microsoft-intune#azure-active-directory-enrollment).  

---

**Otázka: Proč moje pokus o připojení k počítači nezdaří i když se mi nepřišel. veškeré informace o chybě?**

**Odpověď:** pravděpodobnou příčinou je, že je uživatel přihlášen do zařízení pomocí předdefinovaného účtu správce. Před použitím Azure Active Directory Join k dokončení instalace vytvořte jiný místní účet. 

---

**Otázka: kde najdete pokyny pro nastavení automatické registrace zařízení?**

**Odpověď:** podrobné pokyny najdete v tématu [postup konfigurace automatické registrace zařízení se systémem Windows připojených k doméně se službou Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md)

---

**Otázka: kde můžete najít řešení potíží s informace o registraci automatického zařízení?**

**Odpověď:** informace o odstraňování potíží, najdete v tématu:

- [Řešení potíží s automatickou registraci domény k počítačům připojeným k Azure AD – Windows 10 a Windows Server 2016](device-management-troubleshoot-hybrid-join-windows-current.md)

- [Řešení potíží s automatickou registraci domény k počítačům připojeným k Azure AD pro klienty nižší úrovně systému Windows](device-management-troubleshoot-hybrid-join-windows-legacy.md)
 
---

