---
title: Postup plánování implementace služby JOIN pro Azure Active Directory (Azure AD) | Microsoft Docs
description: Popisuje kroky, které jsou potřeba k implementaci zařízení připojených k Azure AD ve vašem prostředí.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 906ab4da941c6a0e1bc98f2f724141c719d04b89
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68879427"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Postup: Plánování implementace připojení ke službě Azure AD

Služba Azure AD JOIN umožňuje připojit zařízení přímo k Azure AD bez nutnosti připojení k místní službě Active Directory a udržování produktivity uživatelů a jejich zabezpečení. Připojení k Azure AD je připravené pro podnikové nasazení v rámci škálování i v oboru nasazení.   

Tento článek poskytuje informace, které potřebujete k plánování vaší implementace služby Azure AD JOIN.
 
## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že jste obeznámeni se [správou zařízení v Azure Active Directory](../device-management-introduction.md).

## <a name="plan-your-implementation"></a>Plánování implementace

K naplánování vaší implementace služby Azure AD JOIN byste se měli seznámit s těmito kroky:

|   |   |
|---|---|
|![Kontrola][1]|Kontrola scénářů|
|![Kontrola][1]|Kontrola infrastruktury identity|
|![Kontrola][1]|Posouzení správy zařízení|
|![Kontrola][1]|Pochopení důležitých informací o aplikacích a prostředcích|
|![Kontrola][1]|Informace o možnostech zřizování|
|![Kontrola][1]|Konfigurace roamingu podnikového stavu|
|![Kontrola][1]|Konfigurace podmíněného přístupu|

## <a name="review-your-scenarios"></a>Kontrola scénářů 

I když se hybridní připojení ke službě Azure AD může pro určité scénáře upřednostnit, Azure AD JOIN vám umožní přejít na model cloudového modelu v systému Windows. Pokud plánujete modernizovat správu zařízení a omezit náklady na IT související s zařízení, Azure AD JOIN poskytuje skvělou základnu pro dosažení těchto cílů.  
 
Připojení k Azure AD byste měli zvážit, pokud vaše cíle odpovídají následujícím kritériím:

- Přijímáte Microsoft 365 jako sadu produktivity pro vaše uživatele.
- Chcete spravovat zařízení pomocí řešení pro správu cloudových zařízení.
- Chcete zjednodušit zřizování zařízení pro geograficky distribuované uživatele.
- Plánujete modernizovat své aplikační infrastruktury.

## <a name="review-your-identity-infrastructure"></a>Kontrola infrastruktury identity  

Připojení k Azure AD funguje s oběma spravovanými i federovaným prostředími.  

### <a name="managed-environment"></a>Spravované prostředí

Spravované prostředí se dá nasadit buď pomocí [synchronizace hodnot hash hesel](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) , nebo [přes ověřování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) pomocí bezproblémového jednotného přihlašování.

Tyto scénáře nevyžadují konfiguraci federačního serveru pro ověřování.

### <a name="federated-environment"></a>Federované prostředí

Federované prostředí by mělo mít poskytovatele identity, který podporuje protokoly WS-Trust i WS-dodávání:

- **WS-nakrmený:** Tento protokol je nutný k připojení zařízení k Azure AD.
- **WS-Trust:** Tento protokol se vyžaduje pro přihlášení k zařízení připojenému k Azure AD.
Pokud používáte AD FS, je nutné povolit následující koncové body WS-Trust:`/adfs/services/trust/2005/usernamemixed`
 `/adfs/services/trust/13/usernamemixed`
 `/adfs/services/trust/2005/certificatemixed`
 `/adfs/services/trust/13/certificatemixed`

Pokud Váš zprostředkovatel identity tyto protokoly nepodporuje, připojení k Azure AD nefunguje nativně. Počínaje systémem Windows 10 1809 se uživatelé můžou přihlásit k zařízení připojenému k Azure AD pomocí zprostředkovatele identity založeného na SAML prostřednictvím [webového přihlášení ve Windows 10](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1809#web-sign-in-to-windows-10). V současné době je webové přihlašování funkcí verze Preview a nedoporučuje se pro produkční nasazení.

>[!NOTE]
> Připojení k Azure AD v současné době nefunguje s [AD FS 2019 nakonfigurovanými externími zprostředkovateli ověřování jako primární metodou ověřování](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/operations/additional-authentication-methods-ad-fs#enable-external-authentication-methods-as-primary). Služba Azure AD JOIN ve výchozím nastavení používá ověřování hesla jako primární metodu, což má za následek selhání ověřování v tomto scénáři.


### <a name="smartcards-and-certificate-based-authentication"></a>Čipové karty a ověřování na základě certifikátu

K připojení zařízení do Azure AD nemůžete použít čipové karty ani ověřování založené na certifikátech. Čipové karty se ale dají použít k přihlášení k zařízením připojeným k Azure AD, pokud jste AD FS nakonfigurovaní.

**Základě** Implementujte Windows Hello pro firmy pro silné ověřování bez hesla u zařízení s Windows 10.

### <a name="user-configuration"></a>Konfigurace uživatele

Pokud vytvoříte uživatele v:

- **Místní služba Active Directory**, je třeba je synchronizovat s Azure AD pomocí [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis). 
- **Azure AD**, nevyžaduje se žádné další nastavení.

Místní hlavní názvy služby (UPN), které se liší od Azure AD UPN, se na zařízeních připojených k Azure AD nepodporují. Pokud uživatelé používají místní hlavní název uživatele (UPN), měli byste naplánovat přechod na použití primárního hlavního názvu uživatele (UPN) ve službě Azure AD.

## <a name="assess-your-device-management"></a>Posouzení správy zařízení

### <a name="supported-devices"></a>Podporovaná zařízení

Připojení k Azure AD:

- Platí jenom pro zařízení s Windows 10. 
- Neplatí pro předchozí verze Windows nebo jiné operační systémy. Pokud máte zařízení s Windows 7/8.1, musíte upgradovat na Windows 10 a nasadit službu Azure AD JOIN.
- Není podporován na zařízeních s čipem TPM v režimu FIPS.
 
**Základě** Vždy používejte nejnovější verzi Windows 10, abyste mohli využít výhod aktualizovaných funkcí.

### <a name="management-platform"></a>Platforma pro správu

Správa zařízení pro zařízení připojená k Azure AD je založená na platformě MDM, jako je třeba Intune, a na CSP pro cloudy MDM. Windows 10 obsahuje integrovaného agenta MDM, který funguje se všemi kompatibilními řešeními MDM.

> [!NOTE]
> Zásady skupiny se na zařízeních připojených k Azure AD nepodporují, protože nejsou připojené k místní službě Active Directory. Správa zařízení připojených k Azure AD je možná jenom přes MDM.

Existují dva způsoby, jak spravovat zařízení připojená k Azure AD:

- **Jenom MDM** – zařízení se spravuje výhradně přes poskytovatele MDM, jako je Intune. Všechny zásady se doručují jako součást procesu registrace MDM. Pro zákazníky Azure AD Premium nebo EMS je automatický krok, který je součástí služby Azure AD JOIN.
- **Společná správa** – zařízení spravuje poskytovatel MDM a SCCM. V tomto postupu se agent SCCM nainstaluje na zařízení spravované MDM za účelem správy určitých aspektů.

Pokud používáte zásady skupiny, vyhodnoťte paritu zásad MDM pomocí [Nástroje pro analýzu migrace MDM (mmat)](https://github.com/WindowsDeviceManagement/MMAT). 

Zkontrolujte podporované a nepodporované zásady, abyste zjistili, jestli můžete místo zásad skupiny použít řešení MDM. U nepodporovaných zásad Vezměte v úvahu následující skutečnosti:

- Jsou nutné zásady nepodporované pro zařízení nebo uživatele připojená k Azure AD?
- Platí nepodporované zásady pro nasazení na základě cloudu?

Pokud vaše řešení pro správu mobilních zařízení není dostupné prostřednictvím Galerie aplikací Azure AD, můžete ho přidat za proces popsaný v [Azure Active Directory integraci s MDM](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm). 

Prostřednictvím spolusprávy můžete pomocí nástroje SCCM spravovat určité aspekty vašich zařízení, zatímco zásady se doručují prostřednictvím platformy MDM. Microsoft Intune umožňuje spolusprávu pomocí nástroje SCCM. Další informace najdete v tématu [společná správa zařízení s Windows 10](https://docs.microsoft.com/sccm/core/clients/manage/co-management-overview). Pokud používáte jiný produkt MDM než Intune, obraťte se prosím na svého poskytovatele MDM v příslušných scénářích spolusprávy.

**Základě** Zvažte správu jenom MDM pro zařízení připojená k Azure AD.

## <a name="understand-considerations-for-applications-and-resources"></a>Pochopení důležitých informací o aplikacích a prostředcích

Pro lepší činnost koncového uživatele a řízení přístupu doporučujeme migrovat aplikace z místního prostředí do cloudu. Zařízení připojená k Azure AD ale můžou bezproblémově poskytovat přístup k místním i cloudovým aplikacím. Další informace najdete v tématu [Jak funguje jednotné přihlašování k místním prostředkům na zařízeních připojených k Azure AD](azuread-join-sso.md).

V následujících částech jsou uvedeny informace o různých typech aplikací a prostředků.

### <a name="cloud-based-applications"></a>Cloudové aplikace

Pokud se aplikace přidá do Galerie aplikací Azure AD, uživatelé získají jednotné přihlašování přes zařízení připojená k Azure AD. Není nutná žádná další konfigurace. Uživatelé získávají jednotné přihlašování v prohlížečích Microsoft Edge i Chrome. Pro Chrome musíte nasadit [rozšíření účtů Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). 

Všechny aplikace Win32, které:

- Spoléhání se na žádosti o tokeny na správce webového účtu (WAM) taky přihlašování na zařízeních připojených k Azure AD. 
- Nespoléhání se na WAM může vyzvat uživatele k ověření. 

### <a name="on-premises-web-applications"></a>Místní webové aplikace

Pokud jsou vaše aplikace vlastní sestavené a/nebo hostované místně, musíte je přidat do důvěryhodných lokalit prohlížeče:

- Povolit integrované ověřování Windows pro práci 
- Poskytněte uživatelům možnosti pro jednotné přihlašování. 

Pokud používáte AD FS, přečtěte si téma [ověření a správa jednotného přihlašování pomocí AD FS](https://docs.microsoft.com/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Základě** Pro lepší prostředí zvažte hostování v cloudu (například Azure) a integraci se službou Azure AD.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Místní aplikace spoléhající se na starší protokoly

Uživatelé získávají přihlašování ze zařízení připojených k Azure AD, pokud má zařízení přístup k řadiči domény. 

**Základě** Nasaďte [aplikace Azure AD proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) , abyste pro tyto aplikace povolili zabezpečený přístup.

### <a name="on-premises-network-shares"></a>Místní síťové sdílené složky

Uživatelé mají k dispozici jednotné přihlašování ze zařízení připojených k Azure AD, když má zařízení přístup k místnímu řadiči domény.

### <a name="printers"></a>Tiskárny

Pro tiskárny potřebujete nasadit [hybridní cloudový tisk](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) pro zjišťování tiskáren na zařízeních připojených k Azure AD. 

I když se tiskárny nedají automaticky zjistit v prostředí jenom pro Cloud, můžou uživatelé použít cestu UNC tiskárny, aby je mohli přímo přidat. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>Místní aplikace, které se spoléhají na ověřování počítače

Zařízení připojená k Azure AD nepodporují místní aplikace, které se spoléhají na ověřování počítače. 

**Základě** Zvažte vyřazení těchto aplikací a přechod na jejich moderní alternativy.

### <a name="remote-desktop-services"></a>Vzdálená plocha

Připojení ke vzdálené ploše k zařízením připojeným k Azure AD vyžaduje, aby byl hostitelský počítač buď připojený k Azure AD, nebo připojený k hybridní službě Azure AD. Vzdálená plocha z nepřipojeného zařízení nebo jiného zařízení než Windows není podporovaná. Další informace najdete v tématu [připojení ke vzdálenému počítači připojenému k Azure AD](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc) .

## <a name="understand-your-provisioning-options"></a>Informace o možnostech zřizování

Službu Azure AD JOIN můžete zřídit pomocí následujících přístupů:

- **Samoobslužná služba v nastavení OOBE/nastavení** – v samoobslužném režimu se uživatelé procházejí procesem připojení služby Azure AD buď během procesu OOBE (Windows out of box), nebo z nastavení systému Windows. Další informace najdete v tématu [připojení pracovního zařízení k síti vaší organizace](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network). 
- **Windows autopilot** – Windows autopilot – Windows autopilot umožňuje předběžnou konfiguraci zařízení pro bezproblémové prostředí v OOBE k provedení služby Azure AD JOIN. Další informace najdete v tématu [Přehled Windows](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)autopilotu. 
- **Hromadná** registrace – hromadný zápis umožňuje správcům využít službu Azure AD pro připojení pomocí nástroje pro hromadné zřizování ke konfiguraci zařízení. Další informace najdete v tématu [Hromadná registrace pro zařízení s Windows](https://docs.microsoft.com/intune/windows-bulk-enroll).
 
Toto je srovnání těchto tří přístupů. 
 
|   | Instalace samoobslužných služeb | Windows autopilot | Hromadná registrace |
| --- | --- | --- | --- |
| Vyžadovat nastavení interakce s uživatelem | Ano | Ano | Ne |
| Vyžadovat úsilí IT | Ne | Ano | Ano |
| Použitelné toky | Nastavení & OOBE | Pouze OOBE | Pouze OOBE |
| Práva místního správce k primárnímu uživateli | Ano, ve výchozím nastavení | Konfigurovatelné | Ne |
| Vyžadovat podporu OEM zařízení | Ne | Ano | Ne |
| Podporované verze | 1511+ | 1709+ | 1703+ |
 
Vyberte si v tabulce výše svůj přístup k nasazení nebo přístupy a Projděte si následující skutečnosti, které vám pomají při přijímání obou přístupů:  

- Chtějí vaši uživatelé, aby procházeli samotným nastavením? 
   - Samoobslužná služba může fungovat nejlépe pro tyto uživatele. Zvažte možnost použití funkce Windows autopilot pro vylepšení uživatelského prostředí.  
- Jsou vaši uživatelé vzdálenými nebo v podnikových prostorách? 
   - Samoobslužná služba nebo funkce autopilotu fungují nejlépe pro vzdálené uživatele, aby bylo zajištěno bezplatné nastavení. 
- Dáváte přednost řízenému uživateli nebo konfiguraci spravovanou správcem? 
   - Hromadný zápis funguje lepším řešením pro nasazení řízené správcem, aby bylo možné nastavit zařízení před tím, než se uživatelům dopustí.     
- Koupíte zařízení od výrobců OEM 1-2 nebo máte rozsáhlou distribuci zařízení s výrobcem OEM?  
   - Pokud si koupíte od omezených výrobců OEM, kteří také podporují autopilot, můžete využít úzkou integraci s autopilotem. 

## <a name="configure-your-device-settings"></a>Konfigurace nastavení zařízení

Azure Portal vám umožní řídit nasazení zařízení připojených k Azure AD ve vaší organizaci. Pokud chcete nakonfigurovat související nastavení, na **stránce Azure Active Directory**vyberte `Devices > Device settings`.

### <a name="users-may-join-devices-to-azure-ad"></a>Uživatelé můžou připojovat zařízení do Azure AD

Tuto možnost nastavte na **všechny** nebo **vybrané** v závislosti na rozsahu nasazení a na tom, který chcete nastavit pro nastavení zařízení připojeného k Azure AD. 

![Uživatelé můžou připojovat zařízení do Azure AD](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Další místní správci na zařízeních připojených do Azure AD

Vyberte vybrat a vyberte uživatele, které chcete přidat do skupiny místních správců na všech zařízeních připojených k Azure AD. 

![Další místní správci na zařízeních připojených do Azure AD](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>Vyžadovat službu Multi-Factor auth k připojení zařízení

Pokud požadujete, aby uživatelé prováděli MFA během připojování zařízení do Azure AD, vyberte **Ano** . Pro uživatele, kteří se připojují k Azure AD pomocí MFA, se samotné zařízení stala za druhý faktor.

![Vyžadovat službu Multi-Factor auth k připojení zařízení](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>Konfigurace nastavení mobility

Než budete moct nakonfigurovat nastavení mobility, možná budete muset nejdřív přidat poskytovatele MDM.

**Postup přidání poskytovatele MDM**:

1. Na **stránce Azure Active Directory**v části **Spravovat** klikněte na `Mobility (MDM and MAM)`. 
1. Klikněte na **Přidat aplikaci**.
1. Ze seznamu vyberte poskytovatele MDM.

   ![Přidat aplikaci](./media/azureadjoin-plan/04.png)

Vyberte poskytovatele MDM a nakonfigurujte související nastavení. 

### <a name="mdm-user-scope"></a>Obor uživatele MDM

V závislosti na rozsahu nasazení vyberte možnost **vše nebo vše** . 

![Obor uživatele MDM](./media/azureadjoin-plan/05.png)

Na základě vašeho oboru nastane jedna z následujících možností: 

- **Uživatel je v oboru MDM**: Pokud máte předplatné Azure AD Premium, je registrace MDM automatizovaná spolu se službou Azure AD JOIN. Všichni uživatelé s vymezeným oborem musí mít příslušnou licenci pro MDM. Pokud v tomto scénáři registrace MDM selže, vrátí se i služba Azure AD JOIN.
- **Uživatel není v oboru MDM**: Pokud uživatelé nejsou v oboru MDM, připojení k Azure AD se dokončí bez registrace MDM. Výsledkem je nespravované zařízení.

### <a name="mdm-urls"></a>Adresy URL MDM

Existují tři adresy URL, které souvisejí s konfigurací MDM:

- Adresa URL podmínek použití MDM
- Adresa URL zjišťování MDM 
- Adresa URL s předpisy služby MDM

![Přidat aplikaci](./media/azureadjoin-plan/06.png)

Každá adresa URL má předdefinovanou výchozí hodnotu. Pokud jsou tato pole prázdná, požádejte prosím o další informace svého poskytovatele MDM.

### <a name="mam-settings"></a>Nastavení MAM

MAM se nevztahuje na službu Azure AD JOIN. 

## <a name="configure-enterprise-state-roaming"></a>Konfigurace roamingu podnikového stavu

Pokud chcete povolit roaming stavu do služby Azure AD, aby uživatelé mohli synchronizovat nastavení napříč zařízeními, přečtěte si téma [povolení Enterprise State Roaming v Azure Active Directory](enterprise-state-roaming-enable.md). 

**Doporučení**: Toto nastavení povolte i pro zařízení připojená k hybridní službě Azure AD.

## <a name="configure-conditional-access"></a>Konfigurace podmíněného přístupu

Pokud máte pro zařízení připojená k Azure AD nakonfigurovaný poskytovatele MDM, poskytovatel zařízení označí jako kompatibilní, jakmile bude zařízení pod správou. 

![Vyhovující zařízení](./media/azureadjoin-plan/46.png)

Tuto implementaci můžete použít k [vyžadování spravovaných zařízení pro přístup k cloudovým aplikacím pomocí podmíněného přístupu](../conditional-access/require-managed-devices.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Připojení nového zařízení s Windows 10 k Azure AD během prvního spuštění](azuread-joined-devices-frx.md)
> [připojit své pracovní zařízení k síti vaší organizace](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
