---
title: Jak naplánovat vaši implementaci připojení k Azure Active Directory (Azure AD) | Dokumentace Microsoftu
description: Vysvětluje kroky, které jsou nutné k implementaci služby Azure AD připojené zařízení ve vašem prostředí.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.subservice: devices
ms.assetid: 81d4461e-21c8-4fdd-9076-0e4991979f62
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/21/2018
ms.author: markvi
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a299c5cf59a3c5b7a994303c9d85783ecfbb424
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201886"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Postup: Plánování implementace připojení ke službě Azure AD


Připojení k Azure AD umožňuje připojit přímo k Azure AD bez nutnosti připojení k místní službě Active Directory a zajistit přitom ochranu uživatelům zajistit produktivitu a zabezpečení zařízení. Připojení k Azure AD je připravené pro podniky ve velkém měřítku a s vymezeným oborem nasazení.   

Tento článek obsahuje informace potřebné k naplánování vaší implementace připojení k Azure AD.

 
## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte zkušenosti s [Úvod ke správě zařízení ve službě Azure Active Directory](../device-management-introduction.md).



## <a name="plan-your-implementation"></a>Plánování implementace

K naplánování vaší implementace hybridní služby Azure AD, které byste se seznámit s:

|   |   |
|---|---|
|![Zaškrtnout][1]|Zkontrolujte vaše scénáře|
|![Zaškrtnout][1]|Kontrola vaší infrastruktury identit|
|![Zaškrtnout][1]|Vyhodnocení správy zařízení|
|![Zaškrtnout][1]|Pochopte předpoklady pro aplikace a prostředky|
|![Zaškrtnout][1]|Popis možností zřizování|
|![Zaškrtnout][1]|Konfigurace organizace stav roamingu|
|![Zaškrtnout][1]|Konfigurace podmíněného přístupu|







## <a name="review-your-scenarios"></a>Zkontrolujte vaše scénáře 

Zatímco hybridní připojení ke službě Azure AD může být upřednostňované pro určité scénáře, připojení ke službě Azure AD vám umožní přechod k modelu upřednostněním cloudu s Windows. Pokud máte v úmyslu modernizovat správu zařízení a zařízení: náklady na IT, připojení ke službě Azure AD poskytuje skvělé základ k dosažení těchto cílů.  

 
Připojení ke službě Azure AD je třeba zvážit, pokud vaše cíle bylo v souladu s následujícími kritérii:

- Microsoft 365 se zavést jako sada produktivity pro vaše uživatele.

- Chcete spravovat zařízení s cloudovým řešením správy zařízení.

- Chcete zjednodušit zřizování zařízení pro geograficky distribuovanou uživatele.

- Máte v plánu modernizujte svou infrastrukturu aplikace.
 

 

## <a name="review-your-identity-infrastructure"></a>Kontrola vaší infrastruktury identit  

Připojení k Azure AD funguje s prostředím, spravované i federované.  


### <a name="managed-environment"></a>Spravované prostředí

Spravované prostředí můžou být nasazené prostřednictvím [synchronizace hodnot Hash hesel](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) nebo [předat prostřednictvím ověřování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) pomocí bezproblémového jednotného přihlašování.

Tyto scénáře nevyžadují konfigurace federačního serveru pro ověřování.


### <a name="federated-environment"></a>Federovaném prostředí

Federovaném prostředí by měl mít zprostředkovatele identity, která podporuje WS-Trust a WS-Fed protokoly:

- **WS-Fed:** Tento protokol je potřeba připojit zařízení k Azure AD.

- **WS-Trust:** Tento protokol se vyžaduje pro přihlášení k zařízení připojené k doméně Azure AD. 

Pokud váš zprostředkovatel identity nepodporuje tyto protokoly, nefunguje připojení k Azure AD nativně. Od verze Windows 10 1809, vaši uživatelé můžete přihlásit k zařízení připojené k doméně Azure AD pomocí zprostředkovatele identity založené na SAML prostřednictvím [webové přihlášení ve Windows 10](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1809#web-sign-in-to-windows-10). V současné době webové přihlášení je pouze ve verzi preview funkce.


### <a name="smartcards-and-certificate-based-authentication"></a>Čipové karty a ověřování na základě certifikátu

Čipové karty nebo ověřování pomocí certifikátu nelze použít k připojení zařízení k Azure AD. Čipové karty však lze použít pro přihlášení k zařízení připojených k Azure AD, pokud máte službu AD FS nakonfigurovaný.

**Doporučení:** Implementace Windows Hello pro firmy bez hesla, silné ověřování pro zařízení s Windows 10.


### <a name="user-configuration"></a>Konfigurace uživatele

Pokud vytvoříte uživatele ve vaší:

- **Místní služby Active Directory**, budete muset synchronizovat do Azure AD pomocí [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis). 

- **Azure AD**, není zapotřebí žádné další nastavení.

Pro místní názvy UPN, které se liší od Azure AD UPN nejsou podporované na zařízeních připojených k Azure AD. Pokud budou uživatelé používat hlavní název uživatele v místním, měli byste naplánovat přejít na jejich primárních hlavní název uživatele v Azure AD.



## <a name="assess-your-device-management"></a>Vyhodnocení správy zařízení

### <a name="supported-devices"></a>Podporovaná zařízení

Připojení k Azure AD:

- Platí pouze pro zařízení s Windows 10. 

- Se nevztahuje na předchozích verzích Windows nebo jinými operačními systémy. Pokud máte zařízení s Windows 7/8.1, musíte upgradovat na Windows 10 k nasazení připojení k Azure AD.

- Není podporované na zařízeních s TPM v režimu FIPS.
 
**Doporučení:** Vždy používejte nejnovější verzi Windows 10 využívat aktualizované funkce.


### <a name="management-platform"></a>Platforma pro správu

Správu zařízení pro zařízení připojená k Azure AD je založená na platformě jako je například Intune MDM) a MDM CSP. Windows 10 obsahuje integrovaného agenta MDM, který funguje u všech řešení MDM kompatibilní.

Pro zařízení připojená k správě služby Azure AD existují dvě metody:

- **Jen pro MDM** – zařízení se spravují výhradně podle poskytovatele MDM, jako je Intune. Všechny zásady se dodávají jako součást procesu registrace MDM. Pro zákazníky Azure AD Premium nebo EMS registrace MDM je automatizované krok, který je součástí Azure AD join.

- **Společná správa** – zařízení se spravuje pomocí poskytovatele MDM a nástroj SCCM. V takovém případě je SCCM agent nainstalovaný na zařízení spravovaná pomocí MDM spravovat některé aspekty.

Protože je připojená k Azure AD zařízení nejsou připojená k místní službě Active Directory, zásady skupiny se nepodporují.


Pokud používáte zásady skupiny, vyhodnocovat vaše parity zásady MDM pomocí [MDM migrace analýzy nástroj (MMAT)](https://github.com/WindowsDeviceManagement/MMAT). 

Zkontrolujte podporované a nepodporované zásady k určení, zda můžete použít řešení MDM místo zásad skupiny. Nepodporovaná zásady zvažte následující:

- Jsou nepodporované zásady potřebné pro zařízení nebo uživatelů připojená k Azure AD?

- Nepodporovaná zásady platí v cloudu řízené nasazení?

Pokud vaše řešení MDM není k dispozici prostřednictvím Galerie aplikací Azure AD, můžete ho vysvětleného v [integrace služby Azure Active Directory s MDM](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm). 

Prostřednictvím spolusprávy můžete spravovat některé aspekty svých zařízení, když zásady jsou poskytovány pomocí vaši platformu MDM SCCM. Microsoft Intune umožňuje společná správa s nástrojem SCCM. Další informace najdete v tématu [zařízení s Windows 10 pro spolusprávu](https://docs.microsoft.com/sccm/core/clients/manage/co-management-overview). Pokud používáte produktu MDM než Intune, zkontrolujte u svého poskytovatele MDM na příslušné spolusprávy scénáře.

**Doporučení:** Zvažte MDM jen pro zařízení připojená k správy pro službu Azure AD.



## <a name="understand-considerations-for-applications-and-resources"></a>Pochopte předpoklady pro aplikace a prostředky

Doporučujeme migrovat aplikace do cloudu pro lepší uživatele z místního prostředí a řízení přístupu. Nicméně připojená k Azure AD zařízení můžete bez problémů poskytují přístup k, v místním i cloudovým aplikacím. Další informace najdete v tématu [jak jednotné přihlašování funguje s místními prostředky v Azure AD zařízení připojená k](azuread-join-sso.md).

Důležité informace týkající se různých typů aplikací a prostředků v následujících částech.

### <a name="cloud-based-applications"></a>Cloudové aplikace

Pokud je aplikace přidána do Galerie aplikací Azure AD, uživatelé získají jednotné přihlašování prostřednictvím zařízení připojených k Azure AD. Není nutná žádná další konfigurace. Uživatelé získat jednotné přihlašování, jak Microsoft Edge a Chrome prohlížečů. Pro Chrome, budete muset nasadit [účty systému Windows 10 rozšíření](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). 

Všechny aplikace systému Win32, který:

- Spolehněte se na Web účtu správce (WAM) pro žádosti o tokeny také získat jednotné přihlašování na zařízeních připojených k Azure AD. 

- Nemusíte spoléhat na WAM může vyzvat uživatele k ověřování. 


### <a name="on-premises-web-applications"></a>Místní webové aplikace

Pokud jsou vaše aplikace vlastní vytvořené a hostované místně, musíte je přidat do důvěryhodných lokalit prohlížeče na:

- Povolit integrované ověřování Windows pro práci 
- Poskytovat prostředí jednotného přihlašování bez výzvy pro uživatele. 

Pokud používáte službu AD FS, přečtěte si téma [ověřte a Správa jednotného přihlašování se službou AD FS](https://docs.microsoft.com/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Doporučení:** Vezměte v úvahu hostování v cloudu (například Azure) a integrace s Azure AD pro vyšší výkon.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Spoléhání se na protokoly starší verze místních aplikací

Uživatelé získat jednotné přihlašování z Azure AD připojených k zařízení, pokud má zařízení přístup k řadiči domény. 

**Doporučení:** Nasazení [proxy aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) zajistit zabezpečený přístup k těmto aplikacím.


### <a name="on-premises-network-shares"></a>Místní síťové složky

Pokud má zařízení přístup k řadiči domény s místními mají vaši uživatelé jednotného přihlašování ze zařízení připojených k Azure AD.

### <a name="printers"></a>Tiskárny

Tiskárny, musíte nasadit [hybridního cloudu tisku](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) pro zařízení připojená k zjišťování tiskáren na Azure AD. 

Zatímco v cloudovém prostředí pouze nelze automaticky zjišťovat tiskárny, vaši uživatelé můžete také cestu UNC k tiskárnám je přímo přidat. 


### <a name="on-premises-applications-relying-on-machine-authentication"></a>U místních aplikací spoléhá na ověřování počítače

Podpora k zařízením Azure AD, které jsou připojené k nepodporují u místních aplikací spoléhá na počítači ověřování. 

**Doporučení:** Zvažte vyřazení těchto aplikací a přesunutí do svých moderních alternativy.

### <a name="remote-desktop-services"></a>Vzdálená plocha

Připojení ke vzdálené ploše zařízení připojená k Azure AD vyžaduje na hostitelském počítači být buď připojené k Azure AD nebo hybridní připojená k Azure AD. Vzdálená plocha ze zařízení s odpojit nebo jiných Windows se nepodporuje. Další informace najdete v tématu [počítač připojený k připojení ke vzdálené službě Azure AD](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc)


## <a name="understand-your-provisioning-options"></a>Popis možností zřizování

Můžete vytvořit připojení ke službě Azure AD pomocí následujících postupů:

- **Samoobslužná služba v OOBE/nastavení** – v režimu samoobslužného uživatele go prostřednictvím připojení k Azure AD buď během Windows mimo prostředí (OOBE) nebo z Windows nastavení procesu. Další informace najdete v tématu [připojení pracovních zařízení k síti vaší organizace](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network). 

- **Windows Autopilot** – Windows Autopilot umožňuje před konfigurací zařízení pro plynulost v OOBE k provedení připojení ke službě Azure AD. Další informace najdete v tématu [přehledu Windows Autopilotu](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot). 

- **Hromadná registrace** -hromadný zápis umožňuje na základě správce Azure AD join pomocí hromadného zřizování nástroje pro konfiguraci zařízení. Další informace najdete v tématu [hromadnou registraci pro zařízení s Windows](https://docs.microsoft.com/intune/windows-bulk-enroll).
 
 


Tady je porovnání těchto tří metod 

 
||Nastavení samoobslužných služeb|Windows Autopilot|Hromadná registrace|
|---|---|---|---|
|Vyžadovat interakci uživatele k nastavení|Ano|Ano|Ne|
|Vyžadovat IT úsilí|Ne|Ano|Ano|
|Použitelné toky|Nastavení & OOBE|Pouze OOBE|Pouze OOBE|
|Práva místního správce na primárním uživatelem|Ano, ve výchozím nastavení|Konfigurovatelné|Ne|
|Nepožadujete podporu zařízení OEM|Ne|Ano|Ne|
|Podporované verze|1511+|1709+|1703+|
 
Zvolte postup nasazení nebo přístupy revize výše uvedené tabulce a kontrolou následující důležité informace pro přijetí kterýkoliv přístup:  

- Jsou vaši uživatelé Odborný koumák projděte si instalační program sami? 

    - Samoobslužné služby mohou nejvhodnější pro tyto uživatele. Vezměte v úvahu Windows Autopilotu a zlepšit uživatelské prostředí.  

- Uživatelé jsou vzdálené nebo v rámci podnikové místní? 

    - Samoobslužné nebo Autopilot práce pro vzdálené uživatele pro nastavení přehledného nejvhodnější. 
 
- Upřednostňujete řízené uživatelem nebo konfiguraci služby spravované správce? 

    - Hromadná registrace fungovala lépe pro správce řízené nasazení si nejdřív vytvořit zařízení předání uživatelům.     

- Zakoupit zařízení od výrobců OEM 1-2, nebo máte široké distribuci OEM zařízení?  

    - Pokud Nákup z omezené výrobce OEM, kteří podporují také Autopilot, vám může přinést užší integraci s Autopilot. 
 

## <a name="configure-your-device-settings"></a>Konfigurace nastavení zařízení

Na webu Azure portal umožňuje že řídit nasazení služby Azure AD připojené zařízení ve vaší organizaci. Nakonfigurovat související nastavení na **stránce služby Azure Active Directory**vyberte `Devices > Device settings`.

### <a name="users-may-join-devices-to-azure-ad"></a>Uživatelé můžou připojovat zařízení do Azure AD

Tuto možnost nastavte na **všechny** nebo **vybrané** založené na rozsah nasazení a který se má povolit nastavení Azure AD připojené zařízení. 

![Uživatelé můžou připojovat zařízení do Azure AD](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Další místní správci na zařízeních připojených do Azure AD

Zvolte **vybrané** a vybere uživatelů, které chcete přidat do skupiny místních správců na zařízeních připojených k všechny Azure AD. 

![Další místní správci na zařízeních připojených do Azure AD](./media/azureadjoin-plan/02.png)


### <a name="require-multi-factor-auth-to-join-devices"></a>Vyžadovat vícefaktorové ověřování pro připojení zařízení

Vyberte **"Ano** Pokud vyžadujete, aby uživatelům provádět vícefaktorové ověřování při připojení zařízení k Azure AD. Pro uživatele, připojení zařízení k Azure AD pomocí vícefaktorového ověřování se stane 2nd factor samotné zařízení.

![Vyžadovat vícefaktorové ověřování pro připojení zařízení](./media/azureadjoin-plan/03.png)




## <a name="configure-your-mobility-settings"></a>Konfigurovat nastavení mobility

Než budete moct nakonfigurovat nastavení mobility, budete muset nejdřív přidat poskytovatele MDM.

**Přidání poskytovatele MDM**:

1. Na **stránce služby Azure Active Directory**v **spravovat** klikněte na tlačítko `Mobility (MDM and MAM)`. 

2. Klikněte na tlačítko **přidat aplikaci**.

3. Vyberte poskytovatele MDM ze seznamu.

    ![Přidání aplikace](./media/azureadjoin-plan/04.png)

Vyberte poskytovatele MDM a nakonfigurujte související nastavení. 

### <a name="mdm-user-scope"></a>Obor uživatele MDM

Vyberte **některé** nebo **všechny** založené na rozsah nasazení. 

![Obor uživatele MDM](./media/azureadjoin-plan/05.png)

Podle oboru, jednu z následujících se stane toto: 

- **Uživatel je v oboru MDM**: Pokud máte předplatné Azure AD Premium, je automatické registrace MDM, spolu s Azure AD join. Všichni uživatelé s vymezeným oborem musí mít příslušnou licenci pro vaše MDM. Pokud se registrace MDM se nezdaří v tomto scénáři, připojení ke službě Azure AD bude vrácena zpět.
    
- **Uživatel není v oboru MDM**: Pokud uživatelé nejsou v oboru MDM, bez jakékoli registrace MDM dokončení připojení ke službě Azure AD. Výsledkem nespravovaném zařízení.


### <a name="mdm-urls"></a>Adresy URL MDM

Existují tři adresy URL, které se vztahují k vaší konfigurace MDM:

- Adresa URL podmínek použití MDM

- Adresa URL zjišťování MDM 

- Adresa URL s předpisy služby MDM


![Přidání aplikace](./media/azureadjoin-plan/06.png)


Každá adresa URL má předdefinovaná výchozí hodnotu. Pokud tato pole jsou prázdné, kontaktujte prosím svého poskytovatele MDM pro další informace.

### <a name="mam-settings"></a>Nastavení MAM

MAM se nevztahují na připojení ke službě Azure AD. 


## <a name="configure-enterprise-state-roaming"></a>Konfigurace organizace stav roamingu

Pokud chcete povolit stav roamingu do služby Azure AD tak, aby uživatelé můžete synchronizovat svoje nastavení mezi zařízeními, přečtěte si téma [povolit Enterprise stav roamingu v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/devices/enterprise-state-roaming-enable). 

**Doporučení**: Povolte toto nastavení i pro hybridních zařízení připojených k Azure AD.


## <a name="configure-conditional-access"></a>Konfigurace podmíněného přístupu

Pokud máte zařízení připojená k poskytovatele MDM, který je nakonfigurovaný pro službu Azure AD, poskytovatele příznaky zařízení jako vyhovující, jakmile je zařízení pod správou. 

![Odpovídající zařízení](./media/azureadjoin-plan/46.png)

Můžete použít tuto implementaci, aby [spravovaných zařízení vyžadovat pro přístup k aplikaci cloud s podmíněným přístupem](../conditional-access/require-managed-devices.md).




## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Připojte se k nové zařízení s Windows 10 s využitím Azure AD během prvního spuštění](azuread-joined-devices-frx.md)
> [připojení pracovních zařízení k síti vaší organizace](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network)


<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
