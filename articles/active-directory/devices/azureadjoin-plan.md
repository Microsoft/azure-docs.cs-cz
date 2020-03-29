---
title: Jak naplánovat implementaci připojení ke službě Azure Active Directory
description: Vysvětluje kroky, které jsou nutné k implementaci zařízení spojených s Azure AD ve vašem prostředí.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6bbecf0e365ba7a8424da775245181fa64c21f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672701"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Postup: Plánování implementace připojení k Azure AD

Připojení k Azure AD umožňuje připojit zařízení přímo k Azure AD bez nutnosti připojení k místní službě Active Directory při zachování vaší uživatele produktivní a zabezpečené. Připojení Azure AD je připravené pro podniky pro nasazení ve velkém měřítku i v rozsahu.   

Tento článek obsahuje informace, které potřebujete k plánování implementace připojení k Azure AD.
 
## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste obeznámeni s [úvodem ke správě zařízení ve službě Azure Active Directory](../device-management-introduction.md).

## <a name="plan-your-implementation"></a>Naplánujte si implementaci

Chcete-li naplánovat implementaci připojení k Azure AD, měli byste se seznámit s:

|   |   |
|---|---|
|![Zaškrtnout][1]|Zkontrolujte své scénáře|
|![Zaškrtnout][1]|Kontrola infrastruktury identit|
|![Zaškrtnout][1]|Posouzení správy zařízení|
|![Zaškrtnout][1]|Vysvětlení aspektů aplikací a prostředků|
|![Zaškrtnout][1]|Vysvětlení možností zřizování|
|![Zaškrtnout][1]|Konfigurace roamingu ve stavu organizace|
|![Zaškrtnout][1]|Konfigurace podmíněného přístupu|

## <a name="review-your-scenarios"></a>Zkontrolujte své scénáře 

Zatímco hybridní připojení Azure AD může být upřednostňováno pro určité scénáře, připojení Azure AD umožňuje přechod na model první v cloudu s Windows. Pokud plánujete modernizovat správu zařízení a snížit náklady na IT související se zařízeními, připojení k Azure AD poskytuje skvělý základ pro dosažení těchto cílů.  
 
Měli byste zvážit připojení Azure AD, pokud vaše cíle odpovídají následujícím kritériím:

- Přijímáte Microsoft 365 jako sadu produktivity pro vaše uživatele.
- Chcete spravovat zařízení pomocí cloudového řešení pro správu zařízení.
- Chcete zjednodušit zřizování zařízení pro geograficky distribuované uživatele.
- Plánujete modernizovat infrastrukturu aplikací.

## <a name="review-your-identity-infrastructure"></a>Kontrola infrastruktury identit  

Připojení Azure AD funguje s oběma, spravovaných i federovaných prostředích.  

### <a name="managed-environment"></a>Spravované prostředí

Spravované prostředí lze nasadit buď prostřednictvím [synchronizace hash hesel,](/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) nebo [předat ověřování](/azure/active-directory/hybrid/how-to-connect-pta-quick-start) pomocí bezproblémového jednotného přihlášení.

Tyto scénáře nevyžadují konfiguraci federačního serveru pro ověřování.

### <a name="federated-environment"></a>Federované prostředí

Federované prostředí by mělo mít zprostředkovatele identity, který podporuje protokoly WS-Trust i WS-Fed:

- **WS-Fed:** Tento protokol je nutné připojit zařízení k Azure AD.
- **WS-Trust:** Tento protokol je nutné k přihlášení k zařízení připojil Azure AD.

Pokud používáte službu AD FS, musíte povolit následující koncové body WS-Trust:`/adfs/services/trust/2005/usernamemixed`
 `/adfs/services/trust/13/usernamemixed`
 `/adfs/services/trust/2005/certificatemixed`
 `/adfs/services/trust/13/certificatemixed`

Pokud váš poskytovatel identity nepodporuje tyto protokoly, připojení Azure AD nefunguje nativně. 

>[!NOTE]
> V současné době připojení Azure AD nefunguje s [AD FS 2019 nakonfigurované s externími poskytovateli ověřování jako primární metoda ověřování](/windows-server/identity/ad-fs/operations/additional-authentication-methods-ad-fs#enable-external-authentication-methods-as-primary). Azure AD připojení výchozí ověřování hesla jako primární metoda, která má za následek selhání ověřování v tomto scénáři


### <a name="smartcards-and-certificate-based-authentication"></a>Čipové karty a ověřování založené na certifikátech

K připojení zařízení k Azure AD nelze použít čipové karty nebo ověřování založené na certifikátech. Čipové karty se však dá použít k přihlášení k zařízením s připojenou k Azure AD, pokud máte nakonfigurovanou službu AD FS.

**Doporučení:** Implementujte Windows Hello pro firmy pro silné ověřování bez hesla pro zařízení s Windows 10.

### <a name="user-configuration"></a>Konfigurace uživatele

Pokud vytváříte uživatele v:

- **Místní služba Active Directory**, je třeba je synchronizovat s Azure AD pomocí [služby Azure AD Connect](/azure/active-directory/hybrid/how-to-connect-sync-whatis). 
- **Azure AD**, není vyžadováno žádné další nastavení.

Místní upny, které se liší od upnů Azure AD nejsou podporovány na zařízeních spojených s Azure AD. Pokud vaši uživatelé používají místní hlavní hlupávací úhlavní ho disponiál, měli byste naplánovat přechod na používání jejich primárního hlavního uživatelského zdroje uživatelů ve službě Azure AD.

## <a name="assess-your-device-management"></a>Posouzení správy zařízení

### <a name="supported-devices"></a>Podporovaná zařízení

Připojení k Azure AD:

- Platí jenom pro zařízení s Windows 10. 
- Nevztahuje se na předchozí verze systému Windows nebo jiných operačních systémů. Pokud máte zařízení s Windows 7/8.1, musíte upgradovat na Windows 10, abyste mohli nasadit připojení azure ad.
- Není podporována na zařízeních s čipem TPM v režimu FIPS.
 
**Doporučení:** Vždy používejte nejnovější verzi Windows 10, abyste využili aktualizované funkce.

### <a name="management-platform"></a>Platforma pro správu

Správa zařízení pro zařízení připojená k Azure AD je založená na platformě MDM, jako je Intune a MDM CSP. Windows 10 má integrovaného agenta MDM, který pracuje se všemi kompatibilními řešeními MDM.

> [!NOTE]
> Zásady skupiny nejsou podporovány v zařízeních připojených k Azure AD, protože nejsou připojeny k místní službě Active Directory. Správa zařízení spojených s Azure AD je možná jenom prostřednictvím MDM

Existují dva přístupy pro správu zařízení spojených s Azure AD:

- **MDM-only** - Zařízení je výhradně spravované poskytovatelem MDM, jako je Intune. Všechny zásady jsou dodávány jako součást procesu registrace MDM. Pro zákazníky Azure AD Premium nebo EMS je registrace MDM automatizovaný krok, který je součástí spojení Azure AD.
- **Spoluspráva** – zařízení je spravováno poskytovatelem MDM a SCCM. V tomto přístupu je agent SCCM nainstalován na zařízení spravovaném MDM pro správu určitých aspektů.

Pokud používáte zásady skupiny, vyhodnoťte paritu zásad MDM pomocí [nástroje MDM Migration Analysis Tool (MMAT).](https://github.com/WindowsDeviceManagement/MMAT) 

Zkontrolujte podporované a nepodporované zásady a zjistěte, zda můžete místo zásad skupiny použít řešení MDM. Nepodporované zásady zvažte následující:

- Jsou nepodporované zásady nezbytné pro zařízení nebo uživatele spojené s Azure AD?
- Jsou nepodporované zásady použitelné v nasazení řízeném cloudem?

Pokud vaše řešení MDM není k dispozici prostřednictvím galerie aplikací Azure AD, můžete ho přidat podle procesu popsaného v [integraci Služby Azure Active Directory s MDM](/windows/client-management/mdm/azure-active-directory-integration-with-mdm). 

Prostřednictvím spolusprávy můžete pomocí SCCM spravovat určité aspekty vašich zařízení, zatímco zásady jsou doručovány prostřednictvím platformy MDM. Microsoft Intune umožňuje spolusprávu s SCCM. Další informace o spolusprávě zařízení s Windows 10 najdete v [tématu Co je spoluspráva?](/configmgr/core/clients/manage/co-management-overview). Pokud používáte jiný produkt MDM než Intune, obraťte se na svého poskytovatele MDM na příslušné scénáře společné správy.

**Doporučení:** Zvažte jenom mdm správu pro zařízení připojená k Azure AD.

## <a name="understand-considerations-for-applications-and-resources"></a>Vysvětlení aspektů aplikací a prostředků

Doporučujeme migrovat aplikace z místního do cloudu pro lepší uživatelské prostředí a řízení přístupu. Zařízení připojená k Azure AD však můžete bez problémů poskytnout přístup k oběma, místní a cloudové aplikace. Další informace najdete v tématu [Jak funguje přisuzovací služby k místním prostředkům na zařízeních spojených s Azure AD](azuread-join-sso.md).

V následujících částech jsou uvedeny aspekty pro různé typy aplikací a prostředků.

### <a name="cloud-based-applications"></a>Cloudové aplikace

Pokud se aplikace přidá do galerie aplikací Azure AD, uživatelé získají služby SSO prostřednictvím zařízení spojených s Azure AD. Není vyžadována žádná další konfigurace. Uživatelé získají semanové přihánění v prohlížečích Microsoft Edge i Chrome. V chromu musíte nasadit [rozšíření účtů Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). 

Všechny Aplikace Win32, které:

- Spolehněte se na správce webových účtů (WAM) pro požadavky na tokeny také získat přistovašené zabezpečení na zařízeních spojených s Azure AD. 
- Nespoléhejte na WAM může vyzvat uživatele k ověření. 

### <a name="on-premises-web-applications"></a>Místní webové aplikace

Pokud jsou vaše aplikace vlastní vytvořené a/nebo hostované místně, musíte je přidat na důvěryhodné weby prohlížeče na:

- Povolení integrovaného ověřování systému Windows 
- Poskytněte uživatelům žádné rychlé nastavení spřipoňka. 

Pokud používáte ad fs, přečtěte si informace [o ověření a správě jednotného přihlašování pomocí funkce AD FS](/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Doporučení:** Zvažte hostování v cloudu (například Azure) a integraci s Azure AD pro lepší prostředí.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Místní aplikace závislé na starších protokolech

Uživatelé získají přihlašovací služby z zařízení připojená k Azure AD, pokud má zařízení přístup k řadiči domény. 

**Doporučení:** Nasazení [proxy aplikace Azure AD pro](/azure/active-directory/manage-apps/application-proxy) povolení zabezpečeného přístupu pro tyto aplikace.

### <a name="on-premises-network-shares"></a>Místní síťové sdílené složky

Vaši uživatelé mají služby SSO z zařízení připojená k Azure AD, když má zařízení přístup k místnímu řadiči domény.

### <a name="printers"></a>Tiskárny

Pro tiskárny je potřeba nasadit [hybridní cloudový tisk](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) pro zjišťování tiskáren na zařízeních spojených s Azure AD. 

Zatímco tiskárny nelze automaticky zjistit v prostředí pouze v cloudu, uživatelé mohou také použít cestu UNC tiskárny přímo přidat. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>Místní aplikace spoléhající na ověřování počítače

Zařízení připojená k Azure AD nepodporují místní aplikace, které spoléhají na ověřování počítače. 

**Doporučení:** Zvažte odchod do důchodu tyto aplikace a přechod na jejich moderní alternativy.

### <a name="remote-desktop-services"></a>Vzdálená plocha

Připojení ke vzdálené ploše k zařízením s připojením azure ad vyžaduje, aby byl hostitelský počítač připojen buď k Azure AD, nebo hybridní Azure AD. Vzdálená plocha z nepřilehlého zařízení nebo zařízení, které není se systémem Windows, není podporována. Další informace najdete v tématu [Připojení ke vzdálenému počítači Azure AD](/windows/client-management/connect-to-remote-aadj-pc)

## <a name="understand-your-provisioning-options"></a>Vysvětlení možností zřizování

Spojení Azure AD můžete zřídit pomocí následujících přístupů:

- **Samoobslužné služby v OOBE/Nastavení** – v režimu samoobslužné služby uživatelé projít procesu připojení Azure AD buď během Windows Out of Box Experience (OOBE) nebo z nastavení systému Windows. Další informace naleznete v [tématu Připojení pracovního zařízení k síti vaší organizace](/azure/active-directory/user-help/user-help-join-device-on-network). 
- **Windows Autopilot** – Windows Autopilot umožňuje předkonfiguraci zařízení pro hladší zážitek v OOBE provádět připojení Azure AD. Další informace naleznete v [přehledu systému Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot). 
- **Hromadná registrace** – hromadná registrace umožňuje připojení Azure AD řízené ho disponivola pomocí nástroje hromadného zřizování ke konfiguraci zařízení. Další informace naleznete v [tématu Hromadná registrace pro zařízení s Windows](/intune/windows-bulk-enroll).
 
Zde je srovnání těchto tří přístupů 
 
|   | Samoobslužné nastavení | Windows Autopilot | Hromadný zápis |
| --- | --- | --- | --- |
| Vyžadovat interakci uživatele k nastavení | Ano | Ano | Ne |
| Vyžadovat úsilí V IT | Ne | Ano | Ano |
| Použitelné toky | Nastavení & OOBE | Pouze OOBE | Pouze OOBE |
| Práva místních správců primárního uživatele | Ano, ve výchozím nastavení | Konfigurovatelné | Ne |
| Vyžadovat podporu oem zařízení | Ne | Ano | Ne |
| Podporované verze | 1511+ | 1709+ | 1703+ |
 
Zvolte přístup nebo přístupy k nasazení tak, že si projmete výše uvedenou tabulku a prozkoumáte následující aspekty pro přijetí obou přístupů:  

- Jsou vaši uživatelé tech důvtipný projít nastavení sami? 
   - Samoobslužná služba může pro tyto uživatele fungovat nejlépe. Zvažte Windows Autopilot pro zlepšení uživatelského prostředí.  
- Jsou vaši uživatelé vzdálení nebo v podnikových prostorách? 
   - Samoobslužné nebo autopilota fungují nejlépe pro vzdálené uživatele pro bezproblémové nastavení. 
- Dáváte přednost řízené uživateli nebo konfiguraci spravované správcem? 
   - Hromadná registrace funguje lépe pro nasazení řízené správcem nastavit zařízení před předáním uživatelům.     
- Nakupujete zařízení od 1-2 OEMS, nebo máte širokou distribuci zařízení OEM?  
   - Pokud nakupujete od omezených oemů, kteří také podporují autopilota, můžete těžit z užší integrace s autopilotem. 

## <a name="configure-your-device-settings"></a>Konfigurace nastavení zařízení

Portál Azure umožňuje řídit nasazení zařízení spojených s Azure AD ve vaší organizaci. Chcete-li nakonfigurovat související **Azure Active Directory page**nastavení, vyberte `Devices > Device settings`na stránce Azure Active Directory položku .

### <a name="users-may-join-devices-to-azure-ad"></a>Uživatelé mohou připojit zařízení k Azure AD

Nastavte tuto možnost na **všechny** nebo **Vybrané** na základě rozsahu vašeho nasazení a kdo chcete povolit nastavení zařízení spojené s Azure AD. 

![Uživatelé mohou připojit zařízení k Azure AD](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Další místní správci na zařízeních azure ad připojen

Zvolte **Vybrané** a vyberete uživatele, které chcete přidat do skupiny místních správců na všech zařízeních spojených s Azure AD. 

![Další místní správci na zařízeních azure ad připojen](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>Vyžadovat vícefaktorové utucha pro připojení zařízení

Vyberte **"Ano,** pokud požadujete, aby uživatelé prováděli vícefaktorové zabezpečení při připojovat zařízení k Azure AD. Pro uživatele, kteří připojují zařízení k Azure AD pomocí vícefaktorového přístupu, se samotné zařízení stane druhým faktorem.

![Vyžadovat vícefaktorové utucha pro připojení zařízení](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>Konfigurace nastavení mobility

Před konfigurací nastavení mobility bude pravděpodobně nutné nejprve přidat zprostředkovatele MDM.

**Přidání zprostředkovatele MDM**:

1. Na **stránce Azure Active Directory**klikněte `Mobility (MDM and MAM)`v části **Správa** na položku . 
1. Klepněte na tlačítko **Přidat aplikaci**.
1. Ze seznamu vyberte zprostředkovatele MDM.

   ![Přidání aplikace](./media/azureadjoin-plan/04.png)

Vyberte poskytovatele MDM a nakonfigurujte související nastavení. 

### <a name="mdm-user-scope"></a>Obor uživatele MDM

Vyberte **Některé** nebo **Všechny** na základě rozsahu vašeho nasazení. 

![Obor uživatele MDM](./media/azureadjoin-plan/05.png)

Na základě vašeho oboru se stane jedna z následujících věcí: 

- **Uživatel je v oboru MDM:** Pokud máte předplatné Azure AD Premium, registrace MDM je automatizovaná spolu s připojením Azure AD. Všichni uživatelé s rozsahem musí mít příslušnou licenci pro váš MDM. Pokud se v tomto scénáři nezdaří registrace MDM, připojení Azure AD bude také vrácena zpět.
- **Uživatel není v oboru MDM**: Pokud uživatelé nejsou v oboru MDM, připojení Azure AD dokončí bez jakékoli registrace MDM. Výsledkem je nespravované zařízení.

### <a name="mdm-urls"></a>Adresy URL MDM

Existují tři adresy URL, které souvisejí s konfigurací MDM:

- Adresa URL podmínek použití MDM
- Adresa URL zjišťování MDM 
- Adresa URL s předpisy služby MDM

![Přidání aplikace](./media/azureadjoin-plan/06.png)

Každá adresa URL má předdefinovanou výchozí hodnotu. Pokud jsou tato pole prázdná, obraťte se na poskytovatele MDM, který vám poskytne další informace.

### <a name="mam-settings"></a>Nastavení MAM

MAM se nevztahuje na připojení Azure AD. 

## <a name="configure-enterprise-state-roaming"></a>Konfigurace roamingu ve stavu organizace

Pokud chcete povolit státní roaming ve službě Azure AD, aby uživatelé mohli synchronizovat svá nastavení mezi zařízeními, přečtěte si informace o [povolení roamingu stavu Enterprise ve službě Azure Active Directory](enterprise-state-roaming-enable.md). 

**Doporučení:** Povolit toto nastavení i pro hybridní zařízení připojená k Azure AD.

## <a name="configure-conditional-access"></a>Konfigurace podmíněného přístupu

Pokud máte zprostředkovatele MDM nakonfigurovanépro vaše zařízení připojená k Azure AD, zprostředkovatel označí zařízení jako kompatibilní, jakmile je zařízení pod správou. 

![Odpovídající zařízení](./media/azureadjoin-plan/46.png)

Pomocí této implementace můžete [vyžadovat spravovaná zařízení pro přístup ke cloudovým aplikacím pomocí podmíněného přístupu](../conditional-access/require-managed-devices.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Připojení k novému zařízení s Windows 10 pomocí Azure AD během prvního spuštění](azuread-joined-devices-frx.md)
> [Připojení k pracovnímu zařízení do sítě vaší organizace](/azure/active-directory/user-help/user-help-join-device-on-network)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
