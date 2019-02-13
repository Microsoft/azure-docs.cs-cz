---
title: Azure Active Directory nejčastější dotazy ke správě zařízení | Dokumentace Microsoftu
description: Azure Active Directory zařízení nejčastější dotazy ke správě.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2019
ms.author: markvi
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9517ccac4006edec473e25c5e6524ce62d4e1259
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56210098"
---
# <a name="azure-active-directory-device-management-faq"></a>Azure Active Directory nejčastější dotazy ke správě zařízení

**Otázka: Nedávno jsem registraci zařízení. Proč nelze zobrazit zařízení v části Moje informace o uživateli na webu Azure Portal? Nebo proč je vlastník zařízení označeno jako zařízení připojená k není k dispozici pro hybridní služby Azure Active Directory (Azure AD)?**

**ODPOVĚĎ:** Zařízení s Windows 10, která jsou připojená k hybridní Azure AD se nezobrazují v části **zařízení uživatelů**.
Použití **všechna zařízení** zobrazení na webu Azure Portal. Můžete také použít Powershellu [Get-MsolDevice](https://docs.microsoft.com/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) rutiny.

Následující zařízení jsou uvedeny v části **zařízení uživatelů**:

- Připojené k všechna osobní zařízení, které nejsou hybridní službě Azure AD. 
- Windows 10 nebo Windows Server 2016 zařízení.
- Všechna zařízení bez Windows. 

--- 

**Otázka: Jak zjistím, co je stav registrace zařízení klienta?**

**ODPOVĚĎ:** Na webu Azure Portal, přejděte na **všechna zařízení**. Hledat zařízení s použitím ID zařízení. Zkontrolujte hodnotu ve sloupci Typ spojení. V některých případech zařízení může resetovat nebo obnoví z Image. Proto je nutné také zkontrolovat stav registrace zařízení na zařízení:

- Pro Windows 10 a Windows Server 2016 nebo novějším, spusťte `dsregcmd.exe /status`.
- Verze operačního systému nižší úrovně, spusťte `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`.

---

**Otázka: Zobrazí záznam zařízení v části informace o uživateli na webu Azure Portal. A protože zaregistrované na zařízení se zobrazuje stav. Teď můžu nastavit správně použití podmíněného přístupu?**

**ODPOVĚĎ:** Stav připojení k zařízení, se zobrazí podle **deviceID**, musí odpovídat stavu na Azure AD a splnění libovolného kritéria hodnocení pro podmíněný přístup. Další informace najdete v tématu [vyžadují spravovaná zařízení pro přístup k aplikaci cloud s podmíněným přístupem](../conditional-access/require-managed-devices.md).

---

**Otázka: Mohu odstranit zařízení na portálu Azure portal nebo pomocí prostředí Windows PowerShell. Ale místní stavu v zařízení říká, že je pořád zaregistrovaný.**

**ODPOVĚĎ:** Tato operace je záměrné. Zařízení nemá přístup k prostředkům v cloudu. 

Pokud chcete znovu zaregistrovat, je nutné provést ruční akcí na zařízení. 

Vymazat stav spojení z Windows 10 a Windows Server 2016, která jsou připojená k doméně služby Active Directory v místním, proveďte následující kroky:

1.  Otevřete příkazový řádek jako správce.

2.  Zadejte `dsregcmd.exe /debug /leave`.

3.  Odhlaste se a přihlaste se k aktivaci naplánované úlohy, který zaregistruje zařízení s Azure AD. 

Verze operačního systému Windows nižší úrovně, které jsou přidaní do domény služby Active Directory v místním proveďte následující kroky:

1.  Otevřete příkazový řádek jako správce.
2.  Zadejte `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"`.
3.  Zadejte `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"`.

---

**Otázka: Proč se zobrazují položky duplicitní zařízení na portálu Azure portal?**

**ODPOVĚĎ:**

-   Pro Windows 10 a Windows Server 2016 opakované pokusy na zrušení služby a znovu se připojit stejném zařízení může způsobit duplicitní položky. 

-   Každý uživatel Windows, který používá **přidat pracovní nebo školní účet** vytváří záznam o novém zařízení se stejným názvem zařízení.

-   Automatické registrace pro verzí operačního systému Windows nižší úrovně, které jsou přidaní do domény v místním adresáři Azure vytvoří nový záznam zařízení se stejným názvem zařízení pro každého uživatele domény, který se přihlásí k zařízení. 

-   Počítače připojeného k Azure AD, který vymaže, přeinstalovat a znovu vstoupit se stejným názvem, zobrazí se jako jiný záznam se stejným názvem zařízení.

---

**Otázka: Proč může uživatel dál přístup k prostředkům ze zařízení zakázáno, na webu Azure Portal?**

**ODPOVĚĎ:** Trvá za hodinu revoke uplatňovat.

>[!NOTE] 
>Pro zaregistrovaná zařízení doporučujeme vám, vymazat zařízení, abyste měli jistotu, že uživatelé nemají přístup k prostředkům. Další informace najdete v tématu [co je registrace zařízení?](https://docs.microsoft.com/intune/deploy-use/enroll-devices-in-microsoft-intune). 

---

## <a name="azure-ad-join-faq"></a>Připojení ke službě Azure AD – nejčastější dotazy

**Otázka: Jak můžu zrušení služby zařízení připojené k doméně Azure AD místně na zařízení?**

**ODPOVĚĎ:** 
- K hybridní službě Azure AD. zařízení připojené k doméně, ujistěte se, že chcete vypnout automatické registrace. Pak bude úloha nebude zařízení znovu zaregistrovat. Dále otevřete příkazový řádek jako správce a zadejte `dsregcmd.exe /debug /leave`. Nebo spusťte tento příkaz jako skript napříč několika zařízeními na zrušení služby hromadně.

- Pro službu Azure AD čistě zařízení připojené k doméně, ujistěte se, že máte účet místního správce v režimu offline nebo ho vytvořit. Nemůžete se přihlásit pomocí libovolné přihlašovací údaje uživatele Azure AD. Dále přejděte na **nastavení** > **účty** > **přístup do práce nebo do školy**. Vyberte svůj účet a vyberte **odpojit**. Postupujte podle zobrazených výzev a zadejte přihlašovací údaje místního správce, po zobrazení výzvy. Restartujte zařízení pro dokončení procesu odpojení.

---

**Otázka: Můžete svým uživatelům přihlásit k zařízení připojená k Azure AD, které jsou odstraněny nebo zakázaný ve službě Azure AD?**

**ODPOVĚĎ:** Ano. Windows je uložené v mezipaměti uživatelské jméno a heslo schopností, který umožňuje uživatelům, kteří už přihlásili pro přístup k ploše rychle i bez připojení k síti. 

Pokud zařízení se odstraní nebo je zakázaný ve službě Azure AD, není znám do zařízení Windows. Tak uživatelům, kteří dříve přihlásili i nadále přístup k ploše v mezipaměti uživatelské jméno a heslo. Ale jak zařízení se odstraní nebo zakázaná, uživatelé nemají přístup k prostředkům nechrání podmíněným přístupem na základě zařízení. 

Uživatelé, kteří se nepovedlo přihlásit dříve nelze přistupovat k zařízení. Neexistuje žádné uložené v mezipaměti uživatelského jména a hesla je povoleno. 

---

**Otázka: Můžete zakázané nebo odstraněných uživatelů přihlásit k zařízení připojených k Azure AD?**

**ODPOVĚĎ:** Ano, ale pouze po omezenou dobu. Když uživatel se odstranil nebo zakázaný ve službě Azure AD, je známo, nikoliv okamžitě do zařízení Windows. Uživatelům, kteří dříve přihlásili tak můžete přístup k ploše v mezipaměti uživatelské jméno a heslo. 

Zařízení je obvykle přehled o stavu uživatele v méně než čtyři hodiny. Potom Windows zablokuje tito uživatelé přístup k ploše. Jako uživatel se odstraní nebo je zakázaný ve službě Azure AD, byly odvolány, jejich tokenů. Proto nemají přístup k žádným prostředkům. 

Odstraněné nebo zakázané uživatelé, kteří nepovedlo přihlásit dříve nemají přístup k zařízení. Neexistuje žádné uložené v mezipaměti uživatelského jména a hesla je povoleno. 

---

**Otázka: Proč uživatelé mají problémy na zařízeních připojených k Azure AD po změně jejich hlavní název uživatele?**

**ODPOVĚĎ:** Změny (UPN) v současné době nejsou plně podporovány na zařízeních připojených k Azure AD. Tak jejich ověřování pomocí Azure AD nepodaří po jejich změně hlavní název uživatele. V důsledku toho uživatelé mají jednotného přihlašování a podmíněný přístup problémy na svých zařízeních. V tuto chvíli uživatelé potřebují k přihlášení k Windows pomocí dlaždice "Jiný uživatel" pomocí jejich nový název UPN k vyřešení tohoto problému. Na vyřešení tohoto problému pracujeme. Uživatelé přihlášení pomocí Windows Hello pro firmy se však není potýkají tento problém. 

---

**Otázka: Moji uživatelé nemůže vyhledat tiskárny ze zařízení připojených k Azure AD. Jak můžu povolit tisk z těchto zařízení?**

**ODPOVĚĎ:** Nasaďte tiskárny pro službu Azure AD zařízení připojená k naleznete v tématu [nasazení systému Windows Server hybridního cloudu tisk pomocí předběžné ověření](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy). Budete potřebovat Windows serveru v místním nasazení hybridního cloudu tisk. V současné době není k dispozici tiskové služby založené na cloudu. 

---

**Otázka: Jak se připojit k vzdálené službě Azure AD na zařízení?**

**ODPOVĚĎ:** Zobrazit [připojit ke vzdálenému počítači připojeném k Azure Active Directory](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc).

---

**Otázka: Proč Moji uživatelé vidí *se tam nelze dostat z tohoto*?**

**ODPOVĚĎ:** Konfigurace určitá pravidla podmíněného přístupu tak, aby vyžadovala stavu konkrétní zařízení? Pokud zařízení nesplňuje kritéria, jsou zablokované uživatele a zobrazí tuto zprávu. Vyhodnocení pravidla zásad podmíněného přístupu. Ujistěte se, že zařízení splňuje kritéria, aby zprávy.

---

**Otázka: Proč nejsou některé Moji uživatelé získat výzvy ověřování Azure Multi-Factor Authentication na zařízení připojených k Azure AD?**

**ODPOVĚĎ:** Uživatel může připojit k nebo zaregistrovat zařízení v Azure AD pomocí služby Multi-Factor Authentication. Samotné zařízení se pak stane důvěryhodným druhý faktor pro tohoto uživatele. Pokaždé, když se stejný uživatel přihlásí k zařízení a přistupuje k aplikaci, službě Azure AD jako druhý faktor považuje za zařízení. Umožňuje daného uživatele a bezproblémově přistupovat k aplikacím bez dalších výzev k ověření službou Multi-Factor Authentication. 

Toto chování nelze použít s žádným uživatelem, který se přihlásí na toto zařízení. Proto všechny ostatní, kteří přistupují k zařízení získat výzvu ověřování službou Multi-Factor Authentication. Pak bude moct aplikace, které vyžadují ověřování službou Multi-Factor Authentication.

---

**Otázka: Proč se zobrazí zpráva *uživatelské jméno nebo heslo není správné* zprávu pro zařízení jsem právě připojená k Azure AD?**

**ODPOVĚĎ:** Běžné důvody pro tento scénář jsou následující:

- Vaše přihlašovací údaje uživatele už nejsou platné.

- Počítač nemůže komunikovat se službou Azure Active Directory. Vyhledejte všechny problémy se síťovým připojením.

- Federované přihlášení vyžadovat federačním serveru pro podporu WS-Trust koncové body, které jsou povolené a přístupné. 

- Můžete povolit předávací ověřování. Proto musí dočasné heslo změnit při přihlášení.

---

**Otázka: Proč se zobrazuje *zobrazí... došlo k chybě!* dialogové okno při pokusu o službě Azure AD join počítač?**

**ODPOVĚĎ:** K této chybě dochází při nastavování registrace Azure Active Directory s Intune. Ujistěte se, že uživatel, který se pokusí o připojení ke službě Azure AD má správné přiřazenou licenci Intune. Další informace najdete v tématu [nastavení registrace pro zařízení s Windows](https://docs.microsoft.com/intune/windows-enroll).  

---

**Otázka: Proč mého pokusu o do služby Azure AD připojte se k počítači nezdaří, i když mi nepřišel. jakékoli informace o chybách?**

**ODPOVĚĎ:** Pravděpodobnou příčinou je, že jste přihlášení k zařízení pomocí účtu místního správce. Před použitím služby Azure Active Directory join k dokončení instalace, vytvořte jiný místní účet. 

---

**Q: co jsou certifikáty MS organizace P2P přístupu k dispozici na naše zařízení s Windows 10?**

**ODPOVĚĎ:** Vystavení certifikátů MS organizace P2P přístupu Azure AD na obě, připojená k Azure AD a zařízení připojená k hybridní službě Azure AD. Tyto certifikáty slouží k povolení vztahu důvěryhodnosti mezi zařízeními ve stejném tenantovi pro scénáře vzdálené plochy. Jeden certifikát je vydaný pro zařízení a jiné se vydá uživateli. Certifikát zařízení nachází v `Local Computer\Personal\Certificates` a je platný jeden den. Tento certifikát se obnovuje (ve vydání nového certifikátu) Pokud je zařízení stále aktivní ve službě Azure AD. Uživatelský certifikát je k dispozici v `Current User\Personal\Certificates` a tento certifikát je platný také za jeden den, ale je vystaven na vyžádání, když se uživatel pokusí relace vzdálené plochy na jiné zařízení připojené k doméně Azure AD. Se platnost neprodlouží na vypršení platnosti. Oba tyto certifikáty jsou vystavené v MS organizace P2P přístupu certifikát `Local Computer\AAD Token Issuer\Certificates`. Tento certifikát je vydaný službou Azure AD při registraci zařízení. 

---

**Q:Why zobrazit více Prošlé certifikáty vydané MS organizace P2P přístupu na naše zařízení s Windows 10? Jak je možné je odstranit?**

**ODPOVĚĎ:** Došlo k nějakému problému lze na Windows 10 verze 1709 a nižší, kde certifikáty s vypršenou platností MS organizaci P2P přístup i nadále existovat v úložišti počítače z důvodu problémů s kryptografických. Uživatelé můžou mít potíže s s připojením k síti, pokud používáte žádné klienty VPN (například Cisco AnyConnect), které nelze zpracovat velký počet certifikáty s vypršenou platností. Tento problém byl vyřešen ve verzi Windows 10 1803 automaticky odstranit všechny takové MS organizace P2P přístupu certifikáty s vypršenou platností. Tento problém lze vyřešit aktualizací zařízení na Windows 10 1803. Pokud se nemůžete aktualizovat, můžete odstranit tyto certifikáty žádný nepříznivý vliv.  

---


## <a name="hybrid-azure-ad-join-faq"></a>Připojení k hybridní službě Azure AD – nejčastější dotazy

**Otázka: Kde můžu najít Poradce při potížích s informací k diagnostice chyb připojení k hybridní službě Azure AD?**

**ODPOVĚĎ:** Informace o odstraňování potíží, najdete v těchto článcích:

- [Zařízení s Windows 10 a Windows serveru 2016 připojená k řešení potíží s hybridní služby Azure Active Directory](troubleshoot-hybrid-join-windows-current.md)

- [Zařízení s nižší úrovně připojená k řešení potíží s hybridní služby Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md)
 
**Otázka: Proč vidím duplicitní Azure AD registrované záznam pro moje Windows 10 hybridní službě Azure AD připojené zařízení v seznamu zařízení služby Azure AD?**

**ODPOVĚĎ:** Pokud vaši uživatelé přidat své účty k aplikacím na zařízení připojeném k doméně, se může zobrazit výzva s **přidat účet do Windows?** Po zadání **Ano** na příkazovém řádku, zaregistruje zařízení s Azure AD. Typ vztahu důvěryhodnosti je označen jako registrováno v Azure AD. Po povolení připojení k hybridní službě Azure AD ve vaší organizaci zařízení také získá hybridní připojená k Azure AD. Pak dvou stavů – stav zařízení zobrazí pro stejné zařízení. 

Připojení k hybridní službě Azure AD má přednost před stavu registrováno v Azure AD. Takže zařízení se považuje za hybridní připojená k Azure AD pro všechny ověřovací a vyhodnocení podmíněného přístupu. Můžete bezpečně odstranit záznam zařízení registrováno v Azure AD z portálu Azure AD. Zjistěte, jak [vyhnout nebo je odstraňte tento duální stav na počítači s Windows 10](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan#review-things-you-should-know). 


---

**Otázka: Proč uživatelé mají problémy na zařízeních připojených k Azure AD s Windows 10 hybridní po změně jejich hlavní název uživatele?**

**ODPOVĚĎ:** Změny hlavního názvu uživatele nyní nejsou plně podporovány s hybridních zařízení připojených k Azure AD. Zatímco uživatelé přihlásit k zařízení a přístupu k místním aplikacím, ověřování pomocí Azure AD nepodaří po změně UPN. V důsledku toho uživatelé mají jednotného přihlašování a podmíněný přístup problémy na svých zařízeních. V tuto chvíli je potřeba odebrat zařízení ze služby Azure AD (spusťte s vyššími oprávněními "dsregcmd /leave") a znovu připojí k (automaticky se stane) k vyřešení daného problému. Na vyřešení tohoto problému pracujeme. Uživatelé přihlášení pomocí Windows Hello pro firmy se však není potýkají tento problém. 

---

**Otázka: Vyžadují zařízení s Windows 10 hybridní připojená k Azure AD na dohled řadič domény získat přístup ke cloudovým prostředkům?**

**ODPOVĚĎ:** Ne. Dokončení hřívací zařízení Windows 10 hybridní služby Azure AD join a uživatel alespoň jednou přihlásí, zařízení nevyžaduje dohlednost služby k řadiči domény pro přístup k prostředkům cloudu. Windows 10 můžete získat jednotné přihlašování k aplikacím Azure AD z libovolného místa s internetovým připojením, s výjimkou případů, kdy se změnil heslo. Pokud heslo je změněno mimo podnikovou síť (například pomocí samoobslužného resetování HESLA Azure AD), uživatel musí mít dohled řadič domény, než budou moct přihlásit k zařízení pomocí nového hesla. V opačném případě se můžete přihlásit pouze pomocí staré heslo, které zneplatněna službou Azure AD a brání jednotného přihlašování na. Tento problém však nedojde, při použití Windows Hello pro firmy. Uživatelé, kteří se přihlašují se pomocí Windows Hello pro firmy i nadále získat jednotné přihlášení do aplikace Azure AD po změně hesla i v případě, že nebudou mít dohled jejich řadič domény. 

---


## <a name="azure-ad-register-faq"></a>Nejčastější dotazy k registrace Azure AD

**Otázka: Můžete zaregistrovat zařízení s Androidem nebo iOS BYOD?**

**ODPOVĚĎ:** Ano, ale pouze službě Azure device registration service a pro zákazníky využívající hybridní řešení. Nepodporuje se se službou registrace zařízení v místním v Active Directory Federation Services (AD FS).

**Otázka: Jak se můžete zaregistrovat zařízení s macOS?**

**ODPOVĚĎ:** Proveďte následující kroky:

1.  [Vytvoření zásad dodržování předpisů](https://docs.microsoft.com/intune/compliance-policy-create-mac-os)
2.  [Definovat zásady podmíněného přístupu pro zařízení s macOS](../active-directory-conditional-access-azure-portal.md) 

**Poznámky:**

- Uživatelé zahrnutých ve své zásady podmíněného přístupu, musí [podporovanou verzi sady Office pro macOS](../conditional-access/technical-reference.md#client-apps-condition) pro přístup k prostředkům. 

- Během prvního pokusu přístup uživatelům výzva k registraci zařízení pomocí portálu společnosti.

