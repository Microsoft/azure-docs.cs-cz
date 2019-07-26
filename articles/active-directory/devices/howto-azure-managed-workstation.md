---
title: Nasazení pracovních stanic spravovaných Azure – Azure Active Directory
description: Naučte se, jak nasadit zabezpečené pracovní stanice spravované v Azure, abyste snížili riziko narušení způsobené chybnou konfigurací nebo ohrožením.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: be9e6374d92fbb7bb1c4b5a2a9e154119c5baf87
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377488"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Nasazení zabezpečené pracovní stanice spravované v Azure

Teď, když [rozumíte zabezpečeným pracovním stanicím](concept-azure-managed-workstation.md), je čas zahájit proces nasazení. Pomocí těchto pokynů vytvoříte pomocí definovaných profilů pracovní stanici, která je od začátku bezpečnější.

![Nasazení zabezpečené pracovní stanice](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Než budete moct nasadit řešení, musíte vybrat profil. V nasazení můžete použít více profilů současně a přiřadit je k klíčům nebo skupinám.
> [!NOTE]
> Použijte libovolný profil podle potřeby podle vašich požadavků. Přiřazením v Intune můžete přejít na jiný profil.

| Profil | Nízká | Rozšířené | Vysoká | Specializovaná | Zabezpečené | Isolated |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Uživatel v Azure AD | Ano | Ano | Ano | Ano | Ano | Ano |
| Spravovaná v Intune | Ano | Ano | Ano | Ano | Ano | Ano |
| Zařízení – zaregistrované v Azure AD | Ano |  |  |  |  | |   |
| Zařízení – připojeno k Azure AD |   | Ano | Ano | Ano | Ano | Ano |
| Použité standardní hodnoty zabezpečení Intune |   | Ano <br> Rozšíření | Ano <br> (HighSecurity) | Ano <br> (NCSC) | Ano <br> Psán |  Není k dispozici |
| Hardware splňuje zabezpečené standardy pro Windows 10. |   | Ano | Ano | Ano | Ano | Ano |
| Povolené ATP programu Microsoft Defender |   | Ano  | Ano | Ano | Ano | Ano |
| Odebrání oprávnění správce |   |   | Ano  | Ano | Ano | Ano |
| Nasazení pomocí programu Microsoft autopilot |   |   | Ano  | Ano | Ano | Ano |
| Aplikace nainstalované jenom pomocí Intune |   |   |   | Ano | Ano |Ano |
| Adresy URL omezené na seznam schválených |   |   |   | Ano | Ano |Ano |
| Blokované na internetu (příchozí/odchozí) |   |   |   |  |  |Ano |

## <a name="license-requirements"></a>Licenční požadavky

V konceptech popsaných v tomto průvodci se předpokládá, že máte Microsoft 365 Enterprise E5 nebo ekvivalentní SKU. Některá doporučení v tomto průvodci můžete implementovat s nižšími SKU. Další informace najdete v tématu [Microsoft 365 Enterprise licencování](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Pro automatizaci zřizování licencí zvažte [licencování na základě skupin](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) pro vaše uživatele.

## <a name="azure-active-directory-configuration"></a>Konfigurace Azure Active Directory

Azure Active Directory (Azure AD) spravují uživatele, skupiny a zařízení pro pracovní stanice vašich správců. Služby identit a funkce musíte povolit pomocí [účtu správce](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

Když vytvoříte zabezpečený účet správce pracovní stanice, vystavte si účet pro aktuální pracovní stanici. Ujistěte se, že k provedení této počáteční konfigurace a všech globálních konfigurací používáte známé bezpečné zařízení. Chcete-li snížit riziko útoku při prvním spuštění, zvažte následující [pokyny, abyste zabránili napadení malwarem](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection).

Měli byste také vyžadovat službu Multi-Factor Authentication, a to alespoň pro správce. Pokyny k implementaci najdete v tématu [nasazení cloudového vícefaktorového ověřování](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) .

### <a name="azure-ad-users-and-groups"></a>Uživatelé a skupiny Azure AD

1. V Azure Portal přejděte na **Azure Active Directory** > **Uživatelé** > **Nový uživatel**.
1. Postupujte podle kroků v [kurzu Vytvoření uživatele](https://docs.microsoft.com/Intune/quickstart-create-user)a vytvořte Správce zařízení.
1. Napište
   * **Název** – zabezpečený Správce pracovní stanice
   * **Uživatelské jméno** - `secure-ws-admin@identityitpro.com`
   * Role - adresáře s**omezeným oprávněním správce** a vyberte roli **správce Intune** .
1. Vyberte **Vytvořit**.

V dalším kroku vytvoříte dvě skupiny: uživatelé pracovní stanice a zařízení pracovní stanice.

Z Azure Portal přejděte na **Azure Active Directory** > **skupiny** > **Nová skupina**.

1. Pro skupinu uživatelé pracovní stanice možná budete chtít nakonfigurovat licencování [na základě skupin](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) pro automatizaci zřizování licencí pro uživatele.
1. Pro skupinu uživatelé pracovní stanice zadejte:
   * **Typ skupiny** – zabezpečení
   * **Název skupiny** – uživatelé zabezpečených pracovních stanic
   * Přiřazený **typ členství**
1. Přidejte uživatele správce zabezpečené pracovní stanice:`secure-ws-admin@identityitpro.com`
1. Můžete přidat všechny další uživatele, kteří budou spravovat zabezpečené pracovní stanice.
1. Vyberte **Vytvořit**.

1. Pro skupinu zařízení pracovní stanice zadejte:
   * **Typ skupiny** – zabezpečení
   * **Název skupiny** – zabezpečené pracovní stanice
   * Přiřazený **typ členství**
1. Vyberte **Vytvořit**.

### <a name="azure-ad-device-configuration"></a>Konfigurace zařízení Azure AD

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Určete, kdo může připojovat zařízení do Azure AD.

Nakonfigurujte nastavení zařízení ve službě Active Directory tak, aby skupina zabezpečení pro správu mohla připojovat zařízení k vaší doméně. Konfigurace tohoto nastavení z Azure Portal:

1. Přejít na **Azure Active Directory** > **zařízení** > **nastavení zařízení**.
1. Zvolte možnost **vybrané** v části **Uživatelé můžou připojovat zařízení do Azure AD**a pak vybrat skupinu "zabezpečená pracovní stanice uživatelů".

#### <a name="removal-of-local-admin-rights"></a>Odebrání oprávnění místního správce

Tato metoda vyžaduje, aby uživatelé virtuálních počítačů s VIP, DevOps a zabezpečenými pracovními stanicemi nemají na svých počítačích žádná práva správce. Konfigurace tohoto nastavení z Azure Portal:

1. Přejít na **Azure Active Directory** > **zařízení** > **nastavení zařízení**.
1. **V zařízeních připojených k Azure AD**vyberte **žádné** v rámci dalších místních správců.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Vyžadovat službu Multi-Factor Authentication pro připojení zařízení

Další posílení procesu připojení zařízení do služby Azure AD:

1. Přejít na **Azure Active Directory** > **zařízení** > **nastavení zařízení**.
1. Pokud chcete připojit zařízení **, vyberte v** části **vyžadovat vícefaktorové ověřování službu Multi-Factor auth**.
1. Vyberte **Uložit**.

#### <a name="configure-mdm"></a>Konfigurace MDM

Z Azure Portal:

1. Přejděte do**Microsoft Intune** **Azure Active Directory** > **mobility (MDM a mam)**  > .
1. Změňte nastavení **oboru uživatele MDM** na **vše**.
1. Vyberte **Uložit**.

Tyto kroky umožňují spravovat jakékoli zařízení v Intune. Další informace najdete v tématu [rychlý Start k Intune: Nastavte automatickou registraci zařízení](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment)s Windows 10. V dalším kroku vytvoříte konfiguraci Intune a zásady dodržování předpisů.

#### <a name="azure-ad-conditional-access"></a>Podmíněný přístup služby Azure AD

Podmíněný přístup Azure AD může přispět k omezení privilegované úlohy správy na vyhovující zařízení. Aby se při přihlašování ke cloudovým aplikacím provádělo ověřování Multi-Factor Authentication, musí být předdefinovaná členové skupiny **Uživatelé zabezpečených pracovních stanic** . Osvědčeným postupem je vyloučit z těchto zásad účty pro nouzový přístup. Další informace najdete v tématu [Správa účtů pro nouzový přístup ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

Postup konfigurace podmíněného přístupu z Azure Portal:

1. Přejděte na **Azure Active Directory** > **nové zásady** **podmíněného přístupu** > .
1. Napište
   * **Název** – zásada pro vyžadování zabezpečeného zařízení
   * Přiřazení
     * **Uživatelé a skupiny**
       * Zahrnutí – **Uživatelé a skupiny** – vyberte skupinu **uživatelů zabezpečených pracovních stanic** , kterou jste vytvořili dříve.
       * Vyloučení – **Uživatelé a skupiny** – vyberte účty pro nouzový přístup vaší organizace.
     * **Cloudové aplikace** – zahrnuje **všechny cloudové aplikace**.
    * Ovládací prvky přístupu
      * **Udělení** – vyberte přepínač **udělení přístupu** .
        * **Vyžadovat službu Multi-Factor Authentication**.
        * **Vyžadovat, aby zařízení bylo označené jako vyhovující**.
        * Pro více ovládacích prvků – **vyžadovat všechny vybrané ovládací prvky**.
    * Povolit **zásady.**

Máte možnost vytvořit zásady, které blokují země, kde uživatelé nebudou mít přístup k prostředkům společnosti. Další informace o zásadách podmíněného přístupu na základě umístění IP adres najdete v tématu [Podmínka umístění v Azure Active Directory podmíněný přístup](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition).

## <a name="intune-configuration"></a>Konfigurace Intune

### <a name="configure-enrollment-status"></a>Konfigurace stavu registrace

Je důležité zajistit, aby zabezpečená pracovní stanice byla důvěryhodného čistého zařízení. Při nákupu nových zařízení můžete nakoupit tovární nastavení na [Windows 10 pro v režimu S](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), což omezuje vystavení ohrožení zabezpečení při správě dodavatelských řetězců. Jakmile od dodavatele obdržíte zařízení, můžete ho pomocí nástroje autopilot změnit z režimu S. Následující doprovodné materiály obsahují podrobné informace o použití procesu transformace.

Aby se zajistilo, že zařízení jsou před použitím plně nakonfigurovaná, poskytne Intune způsob, jak **blokovat používání zařízení, dokud se nenainstalují všechny aplikace a profily**.

Z **Azure Portal**:
1. Přejít na **Microsoft Intune** >  > **registrace** > zařízení –**výchozí** **nastavení stránka registrace registrace**zařízení Windows – stav >  > 
1. Nastavte **Zobrazit průběh instalace profilu aplikace** na **Ano**.
1. Nastavte **blokování použití zařízení, dokud nebudou všechny aplikace a profily nainstalované** na **Ano**.

### <a name="create-an-autopilot-deployment-profile"></a>Vytvoření profilu nasazení Autopilotu

Po vytvoření skupiny zařízení je nutné vytvořit profil nasazení pro konfiguraci zařízení s autopilotem.

V Intune Azure Portal:

1. Vyberte **registrace** >  > zařízení**profily**nasazeníregistraceWindowsvytvořitprofil. > 
1. Napište
   * Název – **profil nasazení zabezpečené pracovní stanice**.
   * Popis – **nasazení zabezpečených pracovních stanic**.
   * Nastavte **převést všechna cílová zařízení na autopilot** na **Ano**. Toto nastavení zajistí, že všechna zařízení v seznamu se budou registrovat ve službě pro nasazení autopilotu. Vyřízení registrace trvá 48 hodin.
1. Vyberte **Další**.
   * V **režimu nasazení**vyberte možnost **samoobslužné nasazení (Preview)** . Zařízení s tímto profilem jsou přidružená k uživateli, který zařízení zaregistruje. Při registraci zařízení se musí zadat přihlašovací údaje uživatele. Je nutné si uvědomit, že nasazení zařízení v režimu **samoobslužného nasazení** vám umožní nasazovat přenosné počítače do sdíleného modelu. Neproběhne žádné přiřazení uživatele, dokud se zařízení poprvé přiřadí uživateli. V důsledku toho nebudou povoleny žádné zásady uživatele, jako je například BitLocker, až do dokončení přiřazení uživatele. Další podrobnosti o tom, jak se přihlásit k zabezpečenému zařízení, najdete v tématu [Vybrané profily](https://docs.microsoft.com/intune/device-profile-assign).
   * **Připojení k Azure AD** by se mělo zobrazit jako **připojené k Azure AD** , které se bude zobrazovat šedě.
   * Vyberte Langugage (oblast), typ uživatelského účtu **Standard**. 
1. Vyberte **Další**.
   * Vyberte značku oboru, pokud jste ji předem nakonfigurovali.
1. Vyberte **Další**.
1. Vyberte **přiřazení** > **přiřadit k** > **vybraným skupinám**. V **možnosti vybrat skupiny, které se mají zahrnout**vyberte **zabezpečený uživatel pracovní stanice**.
1. Vyberte **Další**.
1. Vyberte **Vytvořit** a vytvořte profil. Profil nasazení Autopilotu je teď možné přiřazovat zařízením.

Registrace zařízení v autopilotu poskytuje jiné uživatelské prostředí na základě typu zařízení a role. V našem příkladu nasazení jsme ilustrují model, ve kterém jsou zabezpečená zařízení nasazená a sdílená, ale při prvním použití se zařízení přiřadí uživateli. Další informace najdete v tématu [registrace zařízení v Intune pro Automatický pilot](https://docs.microsoft.com/intune/device-enrollment).

### <a name="configure-windows-update"></a>Konfigurace web Windows Update

Udržování aktuálnosti Windows 10 je jedním z nejdůležitějších věcí, které můžete udělat. Chcete-li zachovat systém Windows v zabezpečeném stavu, nasadíte aktualizační kanál, který bude spravovat tempo použití aktualizací pro pracovní stanice. 

Tento návod doporučuje vytvořit nový aktualizační kanál a změnit následující výchozí nastavení:

Na webu Azure Portal:

1. Přejít na **Microsoft Intune** > **aktualizace** > softwaru**aktualizační kanály Windows 10**.
1. Napište
   * Název – **aktualizace služby Azure Managed Workstation**
   * Kanál pro údržbu – **Windows Insider – Fast**
   * Odložení aktualizace kvality (ve dnech) – **3**
   * Doba odložení aktualizace funkcí (ve dnech) – **3**
   * Chování Automatické aktualizace – **Automatická instalace a restartování bez řízení koncových uživatelů**
   * Blokování uživatele při pozastavení aktualizací Windows – **blokování**
   * Vyžadovat schválení uživatele pro restartování mimo pracovní dobu – **povinné**
   * Povolení automatického restartování uživatelem (v případě restartování) – **povinné**
   * Převod uživatelů na restartování po automatickém restartování (dny) – **3**
   * Odložit připomenutí při restartu (dny) – **3**
   * Nastavit konečný termín pro čekání na restartování (dny) – **3**

1. Vyberte **Vytvořit**.
1. Na kartě **přiřazení** přidejte skupinu zabezpečených **pracovních stanic** .

Další informace o zásadách web Windows Update najdete v tématu [zásady CSP – aktualizace](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Integrace ochrany ATP v programu Windows Defender

Ochrana ATP a Microsoft Intune v programu Windows Defender vzájemně spolupracuje, aby nedocházelo k narušení zabezpečení. Můžou také omezit dopad porušení zabezpečení. Schopnosti ATP poskytují detekci hrozeb v reálném čase a umožňují rozsáhlé auditování a protokolování zařízení koncových bodů.

Pokud chcete nakonfigurovat integraci ochrany ATP v programu Windows Defender a Intune, přečtěte si Azure Portal.

1. Přejděte na **Microsoft Intune** > **dodržování předpisů** > pro zařízení v**programu Windows Defender ATP**.
1. V kroku 1 v části **Konfigurace ochrany ATP v programu Windows Defender**vyberte možnost **připojit ATP v programu windows Defender k Microsoft Intune v Security Center Windows Defenderu**.
1. V Security Center programu Windows Defender:
   1. Vyberte **Nastavení** > **Rozšířené funkce**.
   1. Pro **Microsoft Intune připojení**vyberte **zapnuto**.
   1. Vyberte **uložit předvolby**.
1. Po navázání připojení se vraťte do Intune a v horní části vyberte **aktualizovat** .
1. Nastavte **připojit zařízení s Windows verze 10.0.15063 a vyšší k ochraně ATP v programu Windows Defender** na **zapnuto**.
1. Vyberte **Uložit**.

Další informace najdete v tématu [Rozšířená ochrana před internetovými útoky v programu Windows Defender](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Dokončení posílení profilu pracovní stanice

Aby bylo možné úspěšně dokončit posílení zabezpečení řešení, Stáhněte a spusťte příslušný skript. Najděte odkazy ke stažení pro požadovanou **úroveň profilu**:

| Profil | Umístění pro stahování | Název souboru |
| --- | --- | --- |
| Nízká úroveň zabezpečení | Není k dispozici |  Není k dispozici |
| Rozšířené zabezpečení | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| Vysoké zabezpečení  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Specializovaná | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC-Windows10 (1803) SecurityBaseline. ps1 |
| Specializované dodržování předpisů * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Zabezpečené | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\*Specializované dodržování předpisů je skript, který vynutil specializovanou konfiguraci poskytnutou v NCSC Windows10 SecurityBaseline.

Po úspěšném spuštění skriptu můžete v Intune dělat aktualizace profilů a zásad. Skripty pro rozšířené a zabezpečené profily vytvářejí zásady a profily, ale musíte je přiřadit ke skupině **zabezpečených pracovních stanic** .

* Tady najdete profily konfigurace zařízení Intune vytvořené skripty: **Azure Portal** > **Microsoft Intune**konfiguračníchprofilůzařízení > . > 
* Tady najdete zásady dodržování předpisů zařízením v Intune, které vytvořily tyto skripty: **Azure Portal**MicrosoftIntunezásadydodržování > **předpisů zařízením**. >  > 

Chcete-li zkontrolovat změny provedené skripty, můžete exportovat profily. Tímto způsobem můžete určit další posílení zabezpečení, které může být vyžadováno, jak je uvedeno v [dokumentaci k SECCON](https://docs.microsoft.com/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework).

Spusťte skript `DeviceConfiguration_Export.ps1` pro export dat Intune z [úložiště DeviceConfiguration GiuHub](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) a exportujte všechny aktuální profily Intune.

## <a name="additional-configurations-and-hardening-to-consider"></a>Další konfigurace a posílení zabezpečení pro zvážení

Podle pokynů uvedených tady jste nasadili zabezpečenou pracovní stanici. Měli byste ale také zvážit další ovládací prvky. Příklad:

* omezení přístupu k alternativním prohlížečům
* povolení odchozího HTTP
* blokovat výběr webů
* nastavení oprávnění pro spouštění vlastních skriptů PowerShellu

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Nastavení pravidel ve zprostředkovateli služby brány firewall pro konfiguraci (CSP)

U povolených a blokovaných koncových bodů můžete provádět další změny v rámci správy pravidel příchozího i odchozího připojení. Jak budete nadále posílit zabezpečenou pracovní stanici, můžete snížit omezení, které zakazuje veškerý příchozí a odchozí provoz. Je možné přidat povolené odchozí lokality pro zahrnutí běžných a důvěryhodných webů. Další informace najdete v tématu [Služba konfigurace brány firewall](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Výchozí omezená doporučení jsou:

* Odepřít všechny příchozí
* Odepřít všechny odchozí

Projekt Spamhaus udržuje [seznam blokovaných domén (dvojitý)](https://www.spamhaus.org/dbl/): dobrým prostředkem, který se má použít jako výchozí bod pro blokování lokalit.

### <a name="manage-local-applications"></a>Správa místních aplikací

Zabezpečená pracovní stanice se během odebírání místních aplikací přesune do skutečně zpřísněného stavu, včetně aplikací pro produktivitu. Tady můžete přidat Chrome jako výchozí prohlížeč a pomocí Intune omezit schopnost uživatele měnit prohlížeč a jeho moduly plug-in.

#### <a name="deploy-applications-using-intune"></a>Nasazení aplikací pomocí Intune

V některých situacích jsou v zabezpečené pracovní stanici vyžadovány aplikace, jako je například prohlížeč Google Chrome. Následující příklad uvádí pokyny k instalaci aplikace Chrome do zařízení ve skupině zabezpečení **zabezpečených pracovních stanic**.

1. Stáhněte si balíček Chrome instalačního programu offline [pro Windows 64-bit](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Extrahujte soubory a poznamenejte si umístění `GoogleChromeStandaloneEnterprise64.msi` souboru.
1. V **Azure Portal** přejděte do **Microsoft Intune** > **aplikace** > klienta > aplikace**Přidat**.
1. V části **Typ aplikace**vyberte **obchodní**.
1. V části **soubor balíčku aplikace**vyberte `GoogleChromeStandaloneEnterprise64.msi` soubor z extrahovaného umístění a vyberte **OK**.
1. V části **informace o aplikaci**zadejte popis a vydavatele. Vyberte **OK**.
1. Vyberte **Přidat**.
1. Na kartě **přiřazení** vyberte **dostupné pro zaregistrovaná zařízení** v části **Typ přiřazení**.
1. V části **zahrnuté skupiny**přidejte skupinu **zabezpečených pracovních stanic** .
1. Vyberte **OK**a pak vyberte **Uložit**.

Další pokyny ke konfiguraci nastavení pro Chrome najdete v tématu [Správa prohlížeče Chrome pomocí Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Konfigurace portálu společnosti pro vlastní aplikace

V zabezpečeném režimu je instalace aplikace omezená na portál společnosti Intune. Instalace portálu ale vyžaduje přístup k Microsoft Store. V zabezpečeném řešení můžete portál společnosti zpřístupnit všem zařízením v režimu offline.

Kopie [portál společnosti](https://docs.microsoft.com/Intune/store-apps-company-portal-app) spravovaná v Intune poskytuje přístup na vyžádání k dalším nástrojům, které můžete přemístit do uživatelů zabezpečených pracovních stanic.

Možná budete muset nainstalovat Windows 32 – bitové aplikace nebo jiné aplikace, jejichž nasazení vyžaduje speciální přípravy. V takových případech může [Nástroj pro přípravu obsahu Microsoft Win32](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) poskytnout soubor formátu připravený k použití `.intunewin` pro instalaci.

### <a name="use-powershell-to-create-custom-settings"></a>Použití PowerShellu k vytvoření vlastního nastavení

PowerShell můžete také použít k rozšiřování možností správy hostitele. Tento ukázkový skript nastaví výchozí pozadí na hostiteli. Je to možnost, která je dostupná taky prostřednictvím Azure Portal a profilů. Ukázkový skript slouží pouze k ilustraci funkcí prostředí PowerShell.

Možná budete muset nastavit některé vlastní ovládací prvky a nastavení na zabezpečených pracovních stanicích. Tento příklad změní pozadí pracovní stanice pomocí schopnosti prostředí PowerShell snadno identifikovat zařízení jako připravenou a zabezpečenou pracovní stanici.

Skript [SetDesktopBackground. ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) z skriptovacího centra Microsoftu umožňuje, aby systém Windows při spuštění načetl tento [bezplatný a obecný obrázek na pozadí](https://i.imgur.com/OAJ28zO.png) .

1. Stáhněte si skript do místního zařízení.
1. Aktualizujte customerXXXX a umístění pro stahování obrázku pozadí. V našem příkladu nahrazujeme customerXXXX na pozadí.  
1. Přejděte do části **Azure Portal** > **Microsoft Intune** > **Konfigurace zařízení skripty** **PowerShellu přidat.**  >  > 
1. Zadejte **název** skriptu a zadejte **umístění skriptu**.
1. Vyberte **Konfigurovat**.
   1. Nastavte **Spustit tento skript pomocí přihlašovacích údajů** přihlášeného k **Ano**.
   1. Vyberte **OK**.
1. Vyberte **Vytvořit**.
1. Vyberte **přiřazení** > **Vybrat skupiny**.
   1. Přidejte **zabezpečené pracovní stanice**skupiny zabezpečení.
   1. Vyberte **Uložit**.

## <a name="enroll-and-validate-your-first-device"></a>Registrace a ověření prvního zařízení

1. K registraci zařízení potřebujete následující informace:
   * **Sériové číslo** – našlo se na skříni zařízení.
   * **ID produktu Windows** – v části **systém** > v nabídce nastavení systému Windows se našel.
   * Pomocí rutiny [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) můžete získat soubor hash CSV se všemi požadovanými informacemi pro registraci zařízení.
   
     Spusťte `Get-WindowsAutoPilotInfo – outputfile device1.csv` pro výstup informací jako soubor CSV, který můžete naimportovat do Intune.

     > [!NOTE]
     > Tento skript vyžaduje zvýšená oprávnění. Spouští se jako vzdálený podpis. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` Příkaz umožňuje, aby byl skript správně spuštěn.

   * Tyto informace můžete získat tak, že se přihlásíte k zařízení s Windows 10 verze 1809 nebo vyšší. Tyto informace může poskytnout i prodejce hardwaru.
1. V **Azure Portal**můžete přejít na **Microsoft Intune** > **registrace zařízení registrace** > zařízení s**Windows** >  **– spravovat zařízení s Windows**autopilotem.
1. Vyberte **importovat** a zvolte soubor CSV.
1. Přidejte zařízení do skupiny zabezpečení **zabezpečených pracovních stanic** .
1. V zařízení s Windows 10, které chcete nakonfigurovat, nastavte**obnovení**zabezpečení > na **Windows Settings** > **Update &** .
   1. V **části** resetovat **Tento počítač**vyberte Začínáme.
   1. Podle pokynů obnovte a znovu nakonfigurujte zařízení pomocí nakonfigurovaných zásad profilu a dodržování předpisů.

Po nakonfigurování zařízení dokončete kontrolu a zkontrolujte konfiguraci. Před pokračováním v nasazení potvrďte, že první zařízení je správně nakonfigurované.

## <a name="assign-and-monitor"></a>Přiřadit a monitorovat

Chcete-li přiřadit zařízení a uživatele, je třeba namapovat [Vybrané profily](https://docs.microsoft.com/intune/device-profile-assign) na skupinu zabezpečení. Všichni noví uživatelé, kteří potřebují ke službě oprávnění, musí být také přidáni do skupiny zabezpečení.

Profily můžete monitorovat pomocí [monitorování profilů Intune](https://docs.microsoft.com/intune/device-profile-monitor).

## <a name="next-steps"></a>Další postup

* Přečtěte si další informace o [Microsoft Intune](https://docs.microsoft.com/intune/index).
* Pochopení [služby Azure AD](https://docs.microsoft.com/azure/active-directory/index).
