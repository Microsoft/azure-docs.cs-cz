---
title: Nasadit spravované Azure stanic – Azure Active Directory
description: Zjistěte, jak nasadit zabezpečené, spravované v Azure pracovní stanice, aby se snížilo riziko porušení dokáže upozornit kvůli chybě konfigurace nebo ohrožení zabezpečení.
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
ms.openlocfilehash: 51d8bbc8b8be9679fbf024d7c51de53c430dc493
ms.sourcegitcommit: 978e1b8cac3da254f9d6309e0195c45b38c24eb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67550489"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Nasazení zabezpečené, spravované v Azure pracovní stanice

Nyní, které jste [porozumět zabezpečení pracovních stanic](concept-azure-managed-workstation.md), je čas zahájení procesu nasazení. Pomocí těchto pokynech použijete k vytvoření pracovní stanice, která je od samého začátku bezpečnější definované profily.

![Nasazení zabezpečené pracovní stanice](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Musíte vybrat profil, před nasazením řešení. Můžete použít více profilů současně v nasazení a přiřadit je značky nebo skupiny.
> [!NOTE]
> Použijte libovolný z profilů, podle potřeby podle vašich požadavků. Po přiřazení v Intune můžete přesunout k jinému profilu.

| Profil | Nízká | Rozšířené | Vysoká | Specializovaná | Zabezpečené | Isolated |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Uživatele ve službě Azure AD | Ano | Ano | Ano | Ano | Ano | Ano |
| Spravovaná pomocí Intune | Ano | Ano | Ano | Ano | Ano | Ano |
| Zařízení – registrováno v Azure AD | Ano |  |  |  |  | |   |
| Zařízení – připojená k Azure AD |   | Ano | Ano | Ano | Ano | Ano |
| Použít směrný plán zabezpečení Intune |   | Ano <br> (Rozšířené) | Ano <br> (HighSecurity) | Ano <br> (NCSC) | Ano <br> (Zabezpečeno) |  Není k dispozici |
| Hardware splňuje standardy 10 zabezpečení systému Windows |   | Ano | Ano | Ano | Ano | Ano |
| Ochrana ATP v programu Defender Microsoft povolen |   | Ano  | Ano | Ano | Ano | Ano |
| Odebrání oprávnění správce |   |   | Ano  | Ano | Ano | Ano |
| Nasazení pomocí Microsoft Autopilot |   |   | Ano  | Ano | Ano | Ano |
| Aplikace nainstalované jenom pomocí Intune |   |   |   | Ano | Ano |Ano |
| Adresy URL omezen na seznamu schválených |   |   |   | Ano | Ano |Ano |
| Internet blokované (příchozí nebo odchozí) |   |   |   |  |  |Ano |

## <a name="license-requirements"></a>Licenční požadavky

Koncepty uvedenými v tomto průvodci předpokládá, že máte Microsoft 365 Enterprise E5 nebo ekvivalentní SKU. Některá doporučení v tomto průvodci je možné implementovat pomocí nižší skladové položky. Další informace najdete v tématu [licencování Microsoft 365 Enterprise](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

K automatizaci zřizování licence, vezměte v úvahu [licencování na základě skupiny](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) pro vaše uživatele.

## <a name="azure-active-directory-configuration"></a>Konfigurace Azure Active Directory

Azure Active Directory (Azure AD) spravuje uživatele, skupiny a zařízení pro pracovní stanice správce. Je nutné povolit identitu služby a funkce s [účet správce](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

Při vytváření účtu zabezpečené pracovní stanice správce zpřístupníte účet aktuální pracovní stanici. Ujistěte se, že používáte známé bezpečné zařízení pro toto počáteční konfigurace a všechny globální konfiguraci. Aby se snížila zranitelnost útoku pro první zkušenosti, vezměte v úvahu následující [pokynů, abyste zabránili napadení malwarem](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection).

Ověřování službou Multi-Factor Authentication, by měl také vyžadují aspoň pro vaše správce. Zobrazit [nasazení cloudového ověřování MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) pro pokyny k implementaci.

### <a name="azure-ad-users-and-groups"></a>Azure AD Uživatelé a skupiny

1. Na webu Azure Portal, přejděte do **Azure Active Directory** > **uživatelé** > **nového uživatele**.
1. Vytvoření správce zařízení podle postupu v [vytvořit uživatele kurzu](https://docs.microsoft.com/Intune/quickstart-create-user).
1. Zadejte:
   * **Název** -pracovní stanice správce zabezpečení
   * **Uživatelské jméno** - `secure-ws-admin@identityitpro.com`
   * **Role adresáře** - **správce s omezením** a vyberte **správce Intune** role.
1. Vyberte **Vytvořit**.

V dalším kroku vytvoříte dvě skupiny: pracovní stanice uživatelů a zařízení pracovní stanice.

Na webu Azure Portal, přejděte do **Azure Active Directory** > **skupiny** > **novou skupinu**.

1. Pro pracovní stanice skupiny uživatelů, můžete chtít konfigurovat [licencování na základě skupiny](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) můžete automatizovat zřizování licence pro uživatele.
1. Pro skupiny uživatelů pracovní stanice zadejte:
   * **Typ skupiny** – zabezpečení
   * **Název skupiny** – zabezpečení pracovních stanic uživatelů
   * **Typ členství** – přiřazené
1. Přidání uživatelů zabezpečené pracovní stanice správce: `secure-ws-admin@identityitpro.com`
1. Můžete přidat další uživatele, kteří budou spravovat zabezpečení pracovních stanic.
1. Vyberte **Vytvořit**.

1. Pro skupiny zařízení pracovní stanice zadejte:
   * **Typ skupiny** – zabezpečení
   * **Název skupiny** – zabezpečení pracovních stanic
   * **Typ členství** – přiřazené
1. Vyberte **Vytvořit**.

### <a name="azure-ad-device-configuration"></a>Konfigurace zařízení k Azure AD

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Zadejte, kdo může připojovat zařízení do služby Azure AD

Konfigurovat zařízení nastavení ve službě Active Directory umožňující vaší skupině pro správu zabezpečení k připojení zařízení k vaší doméně. Ke konfiguraci tohoto nastavení na webu Azure Portal:

1. Přejděte na **Azure Active Directory** > **zařízení** > **nastavení zařízení**.
1. Zvolte **vybrané** pod **uživatelé můžou připojovat zařízení do služby Azure AD**a pak vyberte skupinu "Zabezpečení pracovní stanice uživatele".

#### <a name="removal-of-local-admin-rights"></a>Odebrání oprávnění místního správce

Tato metoda vyžaduje, aby uživatelé virtuálních IP adres, DevOps a úroveň zabezpečení pracovních stanic mít žádná práva správce na svých počítačích. Ke konfiguraci tohoto nastavení na webu Azure Portal:

1. Přejděte na **Azure Active Directory** > **zařízení** > **nastavení zařízení**.
1. Vyberte **žádný** pod **zařízení připojená k další místní správci na Azure AD**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Vyžadovat vícefaktorové ověřování pro připojení zařízení

Dále posílit procesu připojování zařízení ke službě Azure AD:

1. Přejděte na **Azure Active Directory** > **zařízení** > **nastavení zařízení**.
1. Vyberte **Ano** pod **vyžadovat Vícefaktorové ověřování pro připojení zařízení**.
1. Vyberte **Uložit**.

#### <a name="configure-mdm"></a>Konfigurace MDM

Na webu Azure Portal:

1. Přejděte do **Azure Active Directory** > **mobilita (MDM a MAM)**  > **Microsoft Intune**.
1. Změnit **obor uživatele MDM** nastavení **všechny**.
1. Vyberte **Uložit**.

Tyto kroky vám umožňují spravovat jakékoli zařízení s Intune. Další informace najdete v tématu [Intune rychlý start: Nastavení automatické registrace pro zařízení s Windows 10](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment). V dalším kroku vytvoříte zásady Konfigurace a dodržování předpisů Intune.

#### <a name="azure-ad-conditional-access"></a>Podmíněný přístup služby Azure AD

Azure AD podmíněný přístup může pomoct omezit privilegovaný úlohy správy na zařízení dodržující předpisy. Předdefinované členy **zabezpečení pracovních stanic uživatelů** skupiny jsou nutné k provedení ověřování službou Multi-Factor Authentication při přihlašování do cloudových aplikací. Osvědčeným postupem je vyloučit ze zásady účtů pro nouzový přístup. Další informace najdete v tématu [spravovat účty pro nouzový přístup ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access).

Postup konfigurace podmíněného přístupu na webu Azure Portal:

1. Přejděte na **Azure Active Directory** > **podmíněného přístupu** > **nové zásady**.
1. Zadejte:
   * **Název** -potřeba zásad zabezpečení zařízení
   * Přiřazení
     * **Uživatelé a skupiny**
       * Zahrnout - **uživatelů a skupin** – tuto možnost vyberte **zabezpečení pracovních stanic uživatelů** skupinu vytvořili dříve.
       * Vyloučení - **uživatelů a skupin** – vyberte účty pro nouzový přístup vaší organizace.
     * **Cloudové aplikace** – zahrnout **všechny cloudové aplikace**.
    * Řízení přístupu
      * **Udělení** – vyberte **udělit přístup** přepínač.
        * **Vyžadovat vícefaktorové ověřování**.
        * **Vyžadovat, aby zařízení bylo označené jako vyhovující**.
        * Pro více ovládacích prvků – **vyžadovat všechny vybrané ovládací prvky**.
    * Povolit zásady – **na**.

Máte možnost vytvářet zásady, které blokování země, ve kterém by uživatelé přístup k prostředkům společnosti. Další informace o zásady podmíněného přístupu na základě umístění protokolu IP, naleznete v tématu [podmínka umístění v Azure Active Directory podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition).

## <a name="intune-configuration"></a>Konfigurace v Intune

### <a name="configure-enrollment-status"></a>Konfigurace stavu registrace

Je důležité zajistit, že vaše zabezpečené pracovní stanice je důvěryhodné zařízení vyčistit. Při nákupu nová zařízení, může trvat, že jsou k factory nastavenou na [Windows 10 Pro v režimu S](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), což omezuje riziko ohrožení na slabá místa zabezpečení během správy dodavatelského řetězce. Po obdržení zařízení ze svého dodavatele, můžete ho změnit z režimu S Autopilot. Následující pokyny poskytuje podrobné informace o použití procesu transformace.

Aby bylo zajištěno, že se zařízení konfigurují plně před použitím, Intune poskytuje způsob, jak **zablokovat používání zařízení, dokud nebudou nainstalovány všechny aplikace a profily**.

Z **webu Azure portal**:
1. Přejděte na **Microsoft Intune** > **registrace zařízení** > **registrace Windows** > **stav registrace Stránka** > **výchozí** > **nastavení**.
1. Nastavte **zobrazit průběh instalace profilu aplikace** k **Ano**.
1. Nastavte **zablokovat používání zařízení, dokud nebudou nainstalovány všechny aplikace a profily** k **Ano**.

### <a name="create-an-autopilot-deployment-profile"></a>Vytvoření profilu nasazení Autopilotu

Po vytvoření skupiny zařízení, musíte vytvořit profil nasazení můžete nakonfigurovat zařízení Autopilot.

V Intune na portálu Azure portal:

1. Vyberte **registrace zařízení** > **registrace Windows** > **profily nasazení** > **vytvořit profil** .
1. Zadejte:
   * Název – **zabezpečení pracovní stanice nasazení profilu**.
   * Popis – **nasazení zabezpečených pracovních stanic**.
   * Nastavte **převést všechna cílová zařízení do služby Autopilot** k **Ano**. Toto nastavení zajistí, že všechna zařízení v seznamu zaregistrovat ve službě nasazení Autopilotu. Vyřízení registrace trvá 48 hodin.
1. Vyberte **Další**.
   * Pro **režim nasazení**, zvolte **místním nasazení (Preview)** . Zařízení s tímto profilem jsou spojeny s uživatelem, který zaregistruje zařízení. Při registraci zařízení se musí zadat přihlašovací údaje uživatele.
   * **Připojit k Azure AD jako** pole by se měla zobrazit **připojená k Azure AD** a být zobrazena šedě.
   * Vyberte vaše Langugage (oblast), typ uživatelského účtu **standardní**. 
1. Vyberte **Další**.
   * Značka oboru vyberte, pokud máte jeden předkonfigurované.
1. Vyberte **Další**.
1. Zvolte **přiřazení** > **přiřadit** > **vybrané skupiny**. V **vybrat skupiny, které chcete zahrnout**, zvolte **zabezpečení pracovních stanic uživatelů**.
1. Vyberte **Další**.
1. Vyberte **Vytvořit** a vytvořte profil. Profil nasazení Autopilotu je teď možné přiřazovat zařízením.

### <a name="configure-windows-update"></a>Konfigurace Windows Update

Aktuálnost Windows 10 je jedním z nejdůležitějších věcí, které vám pomůžou. Pokud chcete zachovat Windows ve stavu zabezpečení, nasadíte aktualizační kanál pro správu narůstajícím tempu, že se aktualizace nainstalují do pracovní stanice. 

Tyto pokyny se doporučuje vytvořit nový aktualizační kanál a změnit následující výchozí nastavení:

Na webu Azure Portal:

1. Přejděte na **Microsoft Intune** > **aktualizace softwaru** > **aktualizační kanály Windows 10**.
1. Zadejte:
   * Název – **Azure spravovali aktualizace pracovní stanice**
   * Kanál – pro údržbu **Windows insider – rychlý okruh**
   * Odložení aktualizace kvality (ve dnech) - **3**
   * Odložení aktualizace funkcí (ve dnech) - **3**
   * Chování automatické aktualizace – **automaticky nainstalovat a restartovat bez ovládání koncovým uživatelem**
   * Brání uživateli ve pozastavení aktualizací Windows - **bloku**
   * Vyžadovat schválení uživatele k restartování mimo pracovní dobu – **vyžaduje**
   * Povolit uživatele k restartování (znamenají angažovaní restartování) - **vyžaduje**
   * Přechod uživatelům znamenají angažovaní restartování po automatické restartování (dny) - **3**
   * Odložit restartování znamenají angažovaní připomenutí (ve dnech) - **3**
   * Nastavit konečný termín pro čekající restartování (dny) - **3**

1. Vyberte **Vytvořit**.
1. Na **přiřazení** kartu, přidejte **zabezpečení pracovních stanic** skupiny.

Další informace o vytváření zásad Windows Update najdete v tématu [zásady CSP – aktualizace](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Integrace s Intune ochrany ATP v programu Windows Defender

Ochrana ATP v programu Windows Defender a Microsoft Intune společně pomáhají zabránit porušení zabezpečení. Jejich lze také omezit dopad těchto porušení. Možnosti ochrany ATP v programu detekci hrozeb v reálném čase a také povolit podrobné auditování a protokolování zařízení pro koncový bod.

Konfigurace integrace ochrany ATP v programu Windows Defender a Intune, přejděte na webu Azure portal.

1. Přejděte do **Microsoft Intune** > **dodržování předpisů zařízením** > **ochrany ATP v programu Windows Defender**.
1. V kroku 1 v části **konfigurace programu Windows Defender ATP**vyberte **připojení programu Windows Defender ATP s Microsoft Intune v Centru zabezpečení systému Windows Defender**.
1. V Centru zabezpečení systému Windows Defender:
   1. Vyberte **nastavení** > **pokročilé funkce**.
   1. Pro **připojení Microsoft Intune**, zvolte **na**.
   1. Vyberte **uložit předvolby**.
1. Jakmile se vytvoří připojení, vraťte se na Intune a vyberte **aktualizovat** v horní části.
1. Nastavte **připojení Windows verze zařízení 10.0.15063 a vyšší do ochrany ATP v programu Windows Defender** k **na**.
1. Vyberte **Uložit**.

Další informace najdete v tématu [rozšířené ochrany před internetovými útoky programu Windows Defender](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Dokončit profil pracovní stanice, posílení zabezpečení

Pro úspěšné dokončení posílení řešení, stáhněte a spusťte příslušný skript. Odkazy ke stažení pro požadované **profilu úroveň**:

| Profil | Umístění pro stahování | Název souboru |
| --- | --- | --- |
| S nízkým zabezpečením | neuvedeno |  neuvedeno |
| Rozšířené zabezpečení | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| Vysoké zabezpečení  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Specializovaná | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| Specializované dodržování předpisů * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Zabezpečené | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\* Specializované dodržování předpisů je skript, který vynucuje specializované konfigurace součástí SecurityBaseline NCSC Windows 10.

Až se skript úspěšně spustí, můžete provést aktualizace profily a zásady v Intune. Skripty pro rozšířené a zabezpečené profily vytvořit zásady a profily pro vás, ale je nutné přiřadit zásady tak, aby vaše **zabezpečení pracovních stanic** skupiny.

* Tady je místo, kde najdete profilů konfigurace zařízení Intune vytvořené pomocí skriptů: **Azure portal** > **Microsoft Intune** > **konfigurace zařízení** > **profily**.
* Tady je místo, kde najdete Intune zařízení zásady dodržování předpisů vytvořené pomocí skriptů: **Azure portal** > **Microsoft Intune** > **dodržování předpisů zařízením** > **zásady**.

Ke zkontrolování změn provedených skripty, můžete to taky profily. Tímto způsobem můžete určit další posílení zabezpečení, který může být vyžadováno, jak je uvedeno v dokumentaci k SECCON.

Spustit skripty pro export dat Intune `DeviceConfiguration_Export.ps1` z [GiuHub konfigurace zařízení úložiště](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) exportovat všechny aktuální profilů Intune.

## <a name="additional-configurations-and-hardening-to-consider"></a>Další konfigurace a posílení zabezpečení, které je třeba zvážit

Podle pokynů v tomto poli, které jste nasadili zabezpečené pracovní stanice. Nicméně byste také měli zvážit další ovládací prvky. Příklad:

* omezit přístup k alternativní prohlížeče
* Povolit odchozí HTTP
* blokovat vyberte weby
* nastavení oprávnění pro spouštění vlastní skripty prostředí PowerShell

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Nastavení pravidel v poskytovatel konfiguračních služeb (CSP) brány firewall

Můžete provést další změny k řízení příchozího a odchozího pravidla, podle potřeby pro vaše koncové body povolené a blokované. I další posílení zabezpečení pracovních stanic, které povolte omezení, které zakazuje veškerý příchozí a odchozí provoz. Můžete například přidat povolený odchozí lokalitách, aby zahrnovaly běžné a důvěryhodné weby. Další informace najdete v tématu [služba konfigurace brány Firewall](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Výchozí s omezením pomocí specifikátoru doporučení jsou:

* Odmítnout všechny příchozí
* Odepřít veškerý odchozí

Projekt Spamhaus udržuje [domény bloku seznamu (Dvojitá)](https://www.spamhaus.org/dbl/): dobrý prostředek má být použit jako výchozí bod pro blokování.

### <a name="manage-local-applications"></a>Správa místních aplikací

Zabezpečené pracovní stanice přejde do skutečně posílené stavu, když se odeberou místních aplikací, včetně kancelářské aplikace. Tady přidáte jako výchozího prohlížeče Chrome a pomocí Intune můžete omezit uživatele schopnost upravovat v prohlížeči a jeho moduly plug-in.

#### <a name="deploy-applications-using-intune"></a>Nasazení aplikace pomocí Intune

V některých případech na zabezpečené pracovní stanice se vyžadují aplikace, jako je prohlížeč Google Chrome. Následující příklad uvádí pokyny k instalaci Chrome na zařízení ve skupině zabezpečení **zabezpečení pracovních stanic**.

1. Stažení offline instalačního programu [sady Chrome pro Windows 64‑bit](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Extrahujte soubory a poznamenejte si umístění `GoogleChromeStandaloneEnterprise64.msi` souboru.
1. V **webu Azure portal** procházením **Microsoft Intune** > **klientské aplikace** > **aplikace**  >  **Přidat**.
1. V části **typ aplikace**, zvolte **-obchodní**.
1. V části **soubor balíčku aplikace**, vyberte `GoogleChromeStandaloneEnterprise64.msi` z extrahovaných umístění a vyberte možnost **OK**.
1. V části **informace o aplikaci**, zadejte popis a vydavatele. Vyberte **OK**.
1. Vyberte **Přidat**.
1. Na **přiřazení** kartu, vyberte možnost **k dispozici pro zaregistrovaná zařízení** pod **typ přiřazení**.
1. V části **zahrnutých skupin**, přidejte **zabezpečení pracovních stanic** skupiny.
1. Vyberte **OK**a pak vyberte **Uložit**.

Další pokyny ke konfiguraci nastavení Chrome najdete v tématu [spravovat prohlížeč Chrome pomocí Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Konfigurace portálu společnosti pro vlastní aplikace

V zabezpečeném režimu je instalace aplikací s omezeným přístupem k portálu společnosti Intune. Instalace portálu však vyžaduje přístup k Microsoft Store. Ve vašem zabezpečená řešení můžete zpřístupnit aplikaci portál společnosti na všechna zařízení do režimu offline.

Spravované v Intune kopii [portál společnosti](https://docs.microsoft.com/Intune/store-apps-company-portal-app) umožňuje přístup na vyžádání do dalších nástrojů, které můžete odeslat uživatelům zabezpečených pracovních stanic.

Můžete potřebovat k instalaci aplikací Windows 32-bit nebo jiných aplikací, jejichž nasazení vyžadují zvláštní přípravy. V takovém případě [přípravu nástroj Microsoft win32 obsahu](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) může poskytnout připravené k použití `.intunewin` formátový soubor pro instalaci.

### <a name="use-powershell-to-create-custom-settings"></a>Použití Powershellu k vytvoření vlastních nastavení

Prostředí PowerShell můžete použít také k rozšíření možností správy hostitele. Tento ukázkový skript nastaví výchozí pozadí na hostiteli. Jde o funkci, která je dostupná prostřednictvím webu Azure portal a profily. Ukázkový skript slouží pouze pro ilustraci funkce prostředí PowerShell.

Můžete potřebovat nastavit některé vlastní ovládací prvky a nastavení na vaše zabezpečených pracovních stanic. V tomto příkladu se změní na pozadí pracovní stanice s použitím powershellu pro snadnější identifikaci zařízení jako připravené k použití, zabezpečené pracovní stanice.

[SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) skript z webu Microsoft Scripting Center umožňuje Windows načíst to [obrázek pozadí zdarma, obecný](https://i.imgur.com/OAJ28zO.png) na spuštění.

1. Stáhněte si skript do místního zařízení.
1. Aktualizace customerXXXX a umístění stahování obrázku pozadí. V našem příkladu jsme nahradit customerXXXX na pozadí.  
1. Přejděte **webu Azure portal** > **Microsoft Intune** > **konfigurace zařízení** > **prostředí PowerShell skripty** > **přidat**.
1. Zadejte **název** pro skript a zadejte **umístění skriptu**.
1. Vyberte **nakonfigurovat**.
   1. Nastavte **spusťte tento skript pomocí pověření přihlášeného** k **Ano**.
   1. Vyberte **OK**.
1. Vyberte **Vytvořit**.
1. Vyberte **přiřazení** > **vybrat skupiny**.
   1. Přidejte skupinu zabezpečení **zabezpečení pracovních stanic**.
   1. Vyberte **Uložit**.

## <a name="enroll-and-validate-your-first-device"></a>Registrace a ověření prvního zařízení

1. Registrace zařízení, budete potřebovat následující informace:
   * **Sériové číslo** – je uvedený na skříň zařízení.
   * **ID produktu Windows** – nalezené pod **systému** > **o** z nabídky nastavení Windows.
   * Můžete spustit [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) načtení souboru hash sdíleného svazku clusteru se všechny požadované informace pro registraci zařízení.
   
     Spustit `Get-WindowsAutoPilotInfo – outputfile device1.csv` výstupu informací do souboru .csv, který můžete importovat do Intune.

     > [!NOTE]
     > Skript vyžaduje zvýšená oprávnění. Spouští vzdálenou podepsané. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` Příkaz umožňuje skript ke spuštění správně.

   * Tyto informace můžete získat po přihlášení k Windows 10 verze 1809 nebo vyšší zařízení. Prodejce hardwaru můžete také zadat tyto informace.
1. V **webu Azure portal**, přejděte na stránku **Microsoft Intune** > **registrace zařízení** > **registrace Windows**  >  **Zařízení – Správa Windows Autopilot**.
1. Vyberte **Import** a zvolte soubor CSV.
1. Přidat zařízení **zabezpečení pracovních stanic** skupiny zabezpečení.
1. Na zařízení Windows 10, které chcete nakonfigurovat, přejděte na **nastavení Windows** > **aktualizace a zabezpečení** > **obnovení**.
   1. Zvolte **Začínáme** pod **obnovit tento počítač**.
   1. Postupujte podle pokynů k resetování a změna konfigurace zařízení pomocí profilu a dodržování předpisů zásady nakonfigurované.

Po konfiguraci zařízení, proveďte kontrolu a zkontrolujte konfiguraci. Potvrďte, že první zařízení je správně nakonfigurované před pokračováním nasazení.

## <a name="assign-and-monitor"></a>Přiřadit a monitorovat

Přiřadit zařízení a uživatelů, budete muset namapovat [vybrané profily](https://docs.microsoft.com/intune/device-profile-assign) do skupiny zabezpečení. Všechny nové uživatele, kteří požadují oprávnění ke službě musí přidat ke skupině zabezpečení.

Můžete monitorovat profily s [monitorování profilu Intune](https://docs.microsoft.com/intune/device-profile-monitor).

## <a name="next-steps"></a>Další postup

* Další informace o [Microsoft Intune](https://docs.microsoft.com/intune/index).
* Vysvětlení [Azure AD](https://docs.microsoft.com/azure/active-directory/index).
