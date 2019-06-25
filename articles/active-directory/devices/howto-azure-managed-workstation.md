---
title: Nasazení Azure managed pracovní stanice – Azure Active Directory
description: Zjistěte, jak nasadit zabezpečené spravovanými Azure pracovních stanic pro snížení rizika porušení dokáže upozornit kvůli chybě konfigurace nebo ohrožení zabezpečení
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
ms.openlocfilehash: ae7c823b9aea262556081354a108ac9509a284ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110691"
---
# <a name="deploy-a-secure-workstation"></a>Nasazení zabezpečené pracovní stanice

Teď, když rozumíte [důvod, proč zabezpečení přístupu k pracovní stanici je důležité?](concept-azure-managed-workstation.md) je čas zahájení procesu nasazení pomocí nástroje k dispozici. Tyto doprovodné materiály použije k vytvoření pracovní stanice, která je od samého začátku bezpečnější definované profily.

![Nasazení zabezpečené pracovní stanice](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Před nasazením řešení, musíte vybrat profil, který budete používat. Je důležité si uvědomit, že můžete použít některý z vybrané profily a přesunout do jiného po přiřazení profilu v Intune na základě vašich požadavků. Více profilů můžete využívat současně v nasazení a přiřazují pomocí přiřazení na značku nebo skupiny.

| Profil | Nízká | Rozšířené | Vysoká | Specializovaná | Zabezpečené | Isolated |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Uživatele ve službě Azure AD | Ano | Ano | Ano | Ano | Ano | Ano |
| Spravovaná pomocí Intune | Ano | Ano | Ano | Ano | Ano | Ano |
| Zaregistrované zařízení v Azure AD | Ano |  |  |  |  | |   |
| Zařízení připojená k Azure AD |   | Ano | Ano | Ano | Ano | Ano |
| Použít směrný plán zabezpečení Intune |   | Ano <br> (Rozšířené) | Ano <br> (HighSecurity) | Ano <br> (NCSC) | Ano <br> (Zabezpečeno) |  Není k dispozici |
| Hardware splňuje standardy 10 zabezpečení systému Windows |   | Ano | Ano | Ano | Ano | Ano |
| Ochrana ATP v programu Defender Microsoft povolen |   | Ano  | Ano | Ano | Ano | Ano |
| Odebrání oprávnění správce |   |   | Ano  | Ano | Ano | Ano |
| Nasazení pomocí Microsoft Autopilot |   |   | Ano  | Ano | Ano | Ano |
| Aplikace nainstalované jenom pomocí Intune |   |   |   | Ano | Ano |Ano |
| Omezeno na seznamu schválených pouze adresy URL |   |   |   | Ano | Ano |Ano |
| Internet (příchozí/odchozí blokované) |   |   |   |  |  |Ano |

## <a name="license-requirements"></a>Licenční požadavky

Koncepty uvedenými v tomto průvodci se předpokládá Microsoft 365 Enterprise E5 nebo ekvivalentní SKU. Některá doporučení v tomto průvodci je možné implementovat pomocí nižší skladové položky. Další informace najdete na [licencování Microsoft 365 Enterprise](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Můžete chtít konfigurovat [licencování na základě skupiny](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) pro vaše uživatele můžete automatizovat zřizování licence.

## <a name="azure-active-directory-configuration"></a>Konfigurace Azure Active Directory

Konfigurace adresáře Azure Active Directory (Azure AD), které bude spravovat vaše uživatele, skupiny a zařízení pro pracovní stanice správce vyžaduje, abyste povolili funkce s a služby identit [účet správce](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

Při vytváření účtu správce zabezpečené pracovní stanice jsou vystaveny účet pro aktuální pracovní stanici. Doporučuje se, že provedete tento počáteční konfiguraci a všechny globální konfiguraci ze známé zařízení bezpečné. Můžete zvážit doprovodné materiály k [zabránit napadení malwarem](https://docs.microsoft.com/windows/security/threat-protection/intelligence/prevent-malware-infection) snížit riziko odhalení příslušných nejprve první prostředí před útoky.

Organizace by měl aspoň vyžadovat vícefaktorové ověřování pro jejich správce. Zobrazit [nasazení cloudového ověřování MFA](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted) pro pokyny k implementaci.

### <a name="azure-ad-users-and-groups"></a>Azure AD Uživatelé a skupiny

Na webu Azure Portal, přejděte do **Azure Active Directory** > **uživatelé** > **nového uživatele**. [Vytvoření zabezpečené pracovní stanice uživatelů](https://docs.microsoft.com/Intune/quickstart-create-user), kdo bude Správce zařízení.

* **Název** -pracovní stanice správce zabezpečení
* **Uživatelské jméno** - secure-ws-admin@identityitpro.com
* **Role adresáře** - **správce s omezením** a vyberte následující roli pro správu
   * **Správce služby Intune**
* **Vytvoření**

Vytvoříme dvě skupiny jeden pro uživatele pracovních stanic a jeden pro pracovní stanice, sami. Na webu Azure Portal, přejděte do **Azure Active Directory** > **skupiny** > **nové skupiny**

První skupinu pro uživatele pracovních stanic. Můžete chtít konfigurovat [licencování na základě skupiny](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-assign) pro uživatele v této skupině můžete automatizovat zřizování licence pro uživatele.

* **Typ skupiny** – zabezpečení
* **Název skupiny** – zabezpečení pracovních stanic uživatelů
* **Typ členství** – přiřazené
* Přidejte do skupiny uživatelů zabezpečené pracovní stanice správce
   * secure-ws-admin@identityitpro.com
* Můžete přidat další uživatele, které budou spravovat zabezpečení pracovních stanic do skupiny
* **Vytvoření**

Druhé skupiny pro pracovní stanice zařízení.

* **Typ skupiny** – zabezpečení
* **Název skupiny** – zabezpečení pracovních stanic
* **Typ členství** – přiřazené
* **Vytvoření**

### <a name="azure-ad-device-configuration"></a>Konfigurace zařízení k Azure AD

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Zadejte, kdo může připojovat zařízení do služby Azure AD

Zařízení nakonfigurovat nastavení ve službě Active Directory umožňující vaší skupině pro správu zabezpečení k připojení zařízení k vaší doméně. Ke konfiguraci tohoto nastavení na webu Azure Portal, přejděte do **Azure Active Directory** > **zařízení** > **nastavení zařízení**. Zvolte **vybrané** pod **uživatelé můžou připojovat zařízení do služby Azure AD** a vyberte skupinu "Zabezpečení pracovní stanice uživatele".

#### <a name="removal-of-local-admin-rights"></a>Odebrání oprávnění místního správce

Jako součást zavádění budou mít uživatelé pracovní stanice virtuálních IP adres, DevOps a zabezpečené úroveň pracovních stanic žádná práva správce na svých počítačích. Ke konfiguraci tohoto nastavení na webu Azure Portal, přejděte do **Azure Active Directory** > **zařízení** > **nastavení zařízení**. Vyberte **žádný** pod **zařízení připojená k další místní správci na Azure AD**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Vyžadovat vícefaktorové ověřování pro připojení zařízení

Chcete-li dále posílit procesu připojování zařízení ke službě Azure AD, přejděte na **Azure Active Directory** > **zařízení** > **nastavení zařízení** Zvolte **Ano** pod **vyžadovat Vícefaktorové ověřování pro připojení zařízení** klikněte na tlačítko **Uložit**.

#### <a name="configure-mdm"></a>Konfigurace MDM

Na webu Azure Portal, přejděte do **Azure Active Directory** > **mobilita (MDM a MAM)**  > **Microsoft Intune**. Změnit nastavení **obor uživatele MDM** k **všechny** a zvolte **Uložit** jako povolíme libovolné zařízení spravovat přes Intune. v tomto scénáři. Další informace najdete v článku [Intune rychlý start: Nastavení automatické registrace pro zařízení s Windows 10](https://docs.microsoft.com/Intune/quickstart-setup-auto-enrollment). V dalším kroku vytvoříme zásad konfigurace a dodržování předpisů Intune.

#### <a name="azure-ad-conditional-access"></a>Podmíněný přístup služby Azure AD

Azure AD podmíněný přístup vám může pomoci udržovat úkoly správy privilegovaných na kompatibilních zařízeních. Uživatelů jsme definovali jako členy **zabezpečení pracovních stanic uživatelů** skupiny, bude nutné provést ověřování službou Multi-Factor Authentication při přihlašování do cloudových aplikací. Budeme postupovat podle nejlepších praktik a účtů pro nouzový přístup vyloučit ze zásad. Další informace najdete v článku [spravovat účty pro nouzový přístup ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)

Jak nakonfigurovat podmíněný přístup na webu Azure Portal, přejděte do **Azure Active Directory** > **podmíněného přístupu** > **nové zásady**.

* **Název** -potřeba zásad zabezpečení zařízení
* Přiřazení
   * **Uživatelé a skupiny**
      * Zahrnout - **uživatelů a skupin** – tuto možnost vyberte **zabezpečení pracovních stanic uživatelů** dříve vytvořenou skupinu
      * Vyloučení - **uživatelů a skupin** – vyberte účty pro nouzový přístup vaší organizace
   * **Cloudové aplikace**
      * Zahrnout - **všechny cloudové aplikace**
* Řízení přístupu
   * **Udělení** – vyberte **udělit přístup** přepínací tlačítko
      * **Vyžadovat vícefaktorové ověřování**
      * **Vyžadovat, aby zařízení bylo označené jako vyhovující**
      * Pro více ovládacích prvků – **vyžadovat všechny vybrané ovládací prvky**
* Povolit zásady – **na**

Organizace mohou volitelně vytvořit zásady pro blokování zemí, kde by uživatelé přístup k prostředkům společnosti. Další informace o IP zásady podmíněného přístupu na základě umístění najdete v článku [co je podmínka umístění v Azure Active Directory podmíněného přístupu?](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)

## <a name="intune-configuration"></a>Konfigurace v Intune

### <a name="configure-enrollment-status"></a>Konfigurace stavu registrace

Jak je uvedeno v Správa dodavatelského řetězce, zajištění zabezpečené pracovní stanice je důvěryhodné zařízení vyčistit doporučujeme při nákupu nová zařízení, které zařízení být nastavena na objekt pro vytváření [Windows 10 Pro v režimu S](https://docs.microsoft.com/Windows/deployment/Windows-10-pro-in-s-mode), což omezuje riziko ohrožení a ohrožení zabezpečení během správy dodavatelského řetězce. Jakmile se zařízení při obdržení ze svého dodavatele, zařízení se odebere z režimu S použití Autopilotu. Následující pokyny poskytuje podrobné informace o použití procesu transformace.

Chceme se ujistit, že se zařízení konfigurují plně před použitím. Intune poskytuje způsob, jak **zablokovat používání zařízení, dokud nebudou nainstalovány všechny aplikace a profily**. 

1. Z **webu Azure portal** přejděte do:
1. **Microsoft Intune** > **registrace zařízení** > **registrace Windows** > **stránka stavu registrace(veverziPreview)**  >  **Výchozí** > **nastavení**.
1. Nastavte **zobrazit průběh instalace profilu aplikace** k **Ano**.
1. Nastavte **zablokovat používání zařízení, dokud nebudou nainstalovány všechny aplikace a profily** k **Ano**.

### <a name="create-an-autopilot-deployment-profile"></a>Vytvoření profilu nasazení Autopilotu

Po vytvoření skupiny zařízení, musíte vytvořit profil nasazení tak, abyste mohli nakonfigurovat zařízení Autopilot.

1. V Intune na portálu Azure portal zvolte **registrace zařízení** > **registrace Windows** > **profily nasazení**  >   **Vytvoření profilu**.
1. Název zadejte **zabezpečení pracovní stanice nasazení profilu**. Zadejte popis, **nasazení zabezpečených pracovních stanic**.
1. Sada převést všechna cílová zařízení Autopilot na Ano. Toto nastavení zajistí, že všechna zařízení v seznamu zaregistrovat ve službě nasazení Autopilotu.  Vyřízení registrace trvá 48 hodin.
1. Nasazení režimu, zvolte **místním nasazení (Preview)** . Zařízení s tímto profilem jsou spojeny s uživatelem, registrace zařízení. Při registraci zařízení se musí zadat přihlašovací údaje uživatele.
1. V připojení k Azure AD jako pole **připojená k Azure AD** by měla být vybrána a šedě.
1. Vyberte prostředí Out-of-box (OOBE), nakonfigurujte následující možnosti a nechte ostatní nastavenou na výchozí hodnotu a pak vyberte **Ok**:
   1. Typ uživatelského účtu: **Standard**
1. Vyberte **Vytvořit** a vytvořte profil. Profil nasazení Autopilotu je teď možné přiřazovat zařízením.
1. Zvolte **přiřazení** > **přiřadit** > **vybrané skupiny**
   1. **Vyberte skupiny, které chcete zahrnout** – zabezpečení pracovních stanic uživatelů

### <a name="configure-windows-update"></a>Konfigurace Windows Update

Jeden z nejdůležitějších věcí, které organizace dokáže je aktualizovat Windows 10. Aby byla zachována ve stavu zabezpečení Windows 10, nasadíme aktualizační kanál pro správu rychlost, jakou mají aktualizace použít pracovní stanice. Tuto konfiguraci najdete v **webu Azure portal** > **Microsoft Intune** > **aktualizace softwaru**  >  **Aktualizační kanály Windows 10**.

Pracujeme na tom **vytvořit** nový aktualizační kanál s následujícím nastavením změnit z výchozí hodnoty.

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

Klikněte na tlačítko **vytvořit** pak na **přiřazení** přidejte kartu **zabezpečení pracovních stanic** jako zahrnutou skupinu.

Další informace o vytváření zásad Windows Update najdete v [zásady CSP – aktualizace](https://docs.microsoft.com/windows/client-management/mdm/policy-csp-update)

### <a name="windows-defender-atp-intune-integration"></a>Integrace s Intune ochrany ATP v programu Windows Defender

Ochrana ATP v programu Windows Defender a Microsoft Intune společně pomáhají zabránit porušení zabezpečení a pomáhají omezit dopad těchto porušení. Možnosti poskytne detekce v reálném čase. Ochrana ATP v programu také poskytne naše nasazení rozsáhlé audit a protokolování zařízení pro koncový bod.

Pokud chcete nakonfigurovat integrace s Intune ochrany ATP v programu Windows Defender na webu Azure Portal, přejděte na **Microsoft Intune** > **dodržování předpisů zařízením** > **ochrany ATP v programu Windows Defender** .

1. V kroku 1 v části **konfigurace programu Windows Defender ATP**, klikněte na tlačítko **připojení programu Windows Defender ATP s Microsoft Intune v Centru zabezpečení systému Windows Defender**.
1. V Centru zabezpečení systému Windows Defender:
   1. Vyberte **nastavení** > **pokročilé funkce**
   1. Pro **připojení Microsoft Intune**, zvolte **na**
   1. Vyberte **uložit předvolby**
1. Po navázání připojení vraťte do Intune a klikněte na tlačítko **aktualizovat** v horní části.
1. Nastavte **připojení Windows verze zařízení 10.0.15063 a vyšší do ochrany ATP v programu Windows Defender** k **na**.
1. **Uložit**

Další informace najdete v článku [rozšířené ochrany před internetovými útoky programu Windows Defender](https://docs.microsoft.com/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="completing-hardening-of-the-workstation-profile"></a>Dokončení posílení zabezpečení pracovních stanic profilu

Pro úspěšné dokončení posílení řešení, stažení a spuštění skriptu založeného na požadovaný **profilu úroveň** z následujícího grafu.

| Profil | Umístění pro stahování | Název souboru |
| --- | --- | --- |
| S nízkým zabezpečením | neuvedeno |  neuvedeno |
| Rozšířené zabezpečení | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809).ps1 |
| Vysoké zabezpečení  | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Specializovaná | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| Specializované dodržování předpisů * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Zabezpečené | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

Specializované dodržování předpisů * je skript, který vynucuje specializované konfigurace součástí SecurityBaseline NCSC Windows 10.

Jakmile vybraný skript úspěšně spustí, provádět aktualizace zásady a profily v Microsoft Intune. Skripty pro rozšířené a zabezpečené profily vytvořit zásady a profily pro vás, ale je nutné přiřadit zásady tak, aby vaše **zabezpečení pracovních stanic** skupiny.

* Vytvořit skripty konfigurace profilů zařízení v Intune najdete v **webu Azure portal** > **Microsoft Intune** > **konfigurace zařízení**  >  **Profily**.
* Zásady dodržování předpisů Intune vytvořené pomocí skriptů najdete v **webu Azure portal** > **Microsoft Intune** > **dodržování předpisů zařízením**  >  **Zásady**.

Zkontrolovat změny můžete také exportovat profily, a aplikovat změny do souboru exportu, jak je uvedeno v SECCON dokumentaci založenou na a další posílení zabezpečení, který je povinný.

Běží Intune data exportovat skript `DeviceConfiguration_Export.ps1` z [GiuHub konfigurace zařízení úložiště](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) poskytne aktuální export všech existujících profilů Intune.

## <a name="additional-configurations-and-hardening-to-consider"></a>Další konfigurace a posílení zabezpečení, které je třeba zvážit

Doprovodné materiály nemá povolené zabezpečené pracovní stanice, další ovládací prvky byste také zvážit, jako je přístup k alternativní prohlížeče, odchozí HTTP povolených a blokovaných webů a možnost spustit vlastní skript prostředí PowerShell.

### <a name="restrictive-inbound-and-outbound-rules-in-firewall-configuration-service-provider-csp"></a>Omezující příchozí a odchozí pravidla v firewall poskytovatel konfiguračních služeb (CSP)

Další Správa příchozích i odchozích pravidel je možné aktualizovat tak, aby odrážely povolených a blokovaných koncové body. Jak budeme dál posiluje zabezpečení pracovní stanice, můžeme přesunout omezení na odepřít, všechny příchozí a odchozí jako výchozí a přidejte povolené lokality pro odchozí tak, aby odrážely běžné a důvěryhodné weby. Další konfigurační informace pro poskytovatele služeb konfigurace brány Firewall najdete v článku [CSP brány Firewall](https://docs.microsoft.com/Windows/client-management/mdm/firewall-csp).

Výchozí s omezením pomocí specifikátoru doporučení jsou:

* Odmítnout všechny příchozí
* Odepřít veškerý odchozí

Projekt Spamhaus udržuje seznam dobré, který organizacím můžete použít jako výchozí bod pro blokování lokalit označované jako [seznamu bloku domény (Dvojitá)](https://www.spamhaus.org/dbl/).

### <a name="managing-local-applications"></a>Správa místních aplikací

Odebrání místních aplikací, včetně odebrání kancelářské aplikace se přesune zabezpečené pracovní stanice do skutečně posílené stavu. V našem příkladu budeme přidávat jako výchozího prohlížeče Chrome a omezit schopnost upravovat prohlížeče, včetně modulů plug-in pomocí Intune.

#### <a name="deploy-applications-using-intune"></a>Nasazení aplikace pomocí Intune

V některých případech na zabezpečené pracovní stanice se vyžadují aplikace, jako je prohlížeč Google Chrome. Následující příklad uvádí pokyny k instalaci Chrome na zařízení ve skupině zabezpečení **zabezpečení pracovních stanic** vytvořili dříve.

1. Stažení offline instalačního programu [sady Chrome pro Windows 64‑bit](https://cloud.google.com/chrome-enterprise/browser/download/)
1. Extrahujte soubory a poznamenejte si umístění `GoogleChromeStandaloneEnterprise64.msi` nainstalovat pomocí Intune
1. V **webu Azure portal** procházením **Microsoft Intune** > **klientské aplikace** > **aplikace**  >  **Přidat**
1. V části **typ aplikace**, zvolte **-obchodní**
1. V části **soubor balíčku aplikace**, vyberte `GoogleChromeStandaloneEnterprise64.msi` extrahované umístění a klikněte na **OK**
1. V části **informace o aplikaci**, zadejte popis a vydavatele a tlačítko **OK**
1. Klikněte na tlačítko **přidat**
1. Na **přiřazení** vyberte **k dispozici pro zaregistrovaná zařízení** pod **typ přiřazení**
1. V části **zahrnutých skupin**, přidejte **zabezpečení pracovních stanic** dříve vytvořenou skupinu
1. Klikněte na tlačítko **OK** pak **uložit**

Další informace o konfiguraci nastavení Chrome najdete v článku jejich podporu [spravovat prohlížeč Chrome pomocí Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Konfigurace portálu společnosti pro vlastní aplikace

V zabezpečeném režimu instalaci aplikací bude omezená na portál společnosti Intune. Instalace portálu však vyžaduje přístup k Microsoft Store. V našem zabezpečené řešení učiníme portálu k dispozici pro všechna zařízení pomocí režimu offline aplikace portál společnosti.

Instalaci Intune spravovat kopii [portál společnosti](https://docs.microsoft.com/Intune/store-apps-company-portal-app) povolí možnost nabízet dolů další nástroje na vyžádání uživatelům zabezpečených pracovních stanic.

V některých organizacích může být nutné nainstalovat Windows 32-bit aplikace nebo aplikace, které vyžadují další přípravy pro nasazení. V případě těchto aplikací [přípravu nástroj Microsoft win32 obsahu](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) poskytne je připraven k použití `.intunewin` formátový soubor pro instalaci.

### <a name="setting-up-custom-settings-using-powershell"></a>Nastavení vlastních nastavení pomocí Powershellu

Pokud použijeme příklad použití Powershellu k zajistí možnosti rozšíření při správě hostitele. Skript se nastavit výchozí pozadí na hostiteli. Tato funkce je také k dispozici v profilech a slouží pouze pro ilustraci funkci.

V řešení může být potřeba nastavit některé vlastní ovládací prvky a nastavení na zabezpečených pracovních stanic. V našem příkladu Změníme na pozadí pracovní stanice, abyste mohli snadno identifikovat zařízení jako zabezpečené pracovní stanice připravené k použití pomocí Powershellu. Zatímco náš příklad pomocí prostředí PowerShell a dokončete tuto úlohu, můžete dokončit také na webu Azure Portal.

Náš příklad použije následující [obrázek pozadí bezplatné obecný](https://i.imgur.com/OAJ28zO.png) a [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) z webu Microsoft Scripting Center umožňuje Windows pro načtení na pozadí v nabídce start.

1. Stáhněte si skript do místního zařízení
1. Aktualizace customerXXXX a umístění stahování na pozadí, kterou Pokud chcete použít ve skriptu, tak, aby odrážely pozadí souboru a složky, kterou chcete nasazení použít. V našem příkladu jsme nahradit customerXXXX na pozadí.  
1. Přejděte **webu Azure portal** > **Microsoft Intune** > **konfigurace zařízení** > **prostředí PowerShell skripty** > **přidat**
1. Zadejte **název** pro skript a zadejte **umístění skriptu** kterého jste stáhli na
1. Vyberte **konfigurace**
   1. Nastavte **spusťte tento skript pomocí pověření přihlášeného**do **Ano**
   1. Klikněte na tlačítko **OK**.
1. Klikněte na **Vytvořit**.
1. Vyberte **přiřazení** > **vybrat skupiny**
   1. Přidejte skupinu zabezpečení **zabezpečení pracovních stanic** vytvořili a klikněte na tlačítko **uložit**

### <a name="using-the-preview-mdm-security-baseline-for-october-2018"></a>Používání verze preview: Směrný plán zabezpečení MDM pro října 2018

Microsoft Intune přináší funkce Správa standardních hodnot zabezpečení poskytuje správcům jednoduchý způsob, jak vynutit běžné stav standardních hodnot zabezpečení. Směrný plán poskytuje podobné prostředky k dosažení uzamčeném dolů rozšířená profilu pracovní stanice.

Pro zabezpečené pracovní stanice implementace těchto standardních hodnot se nepoužívá, protože koliduje s zabezpečenou konfiguraci nasazení.

|   |   |   |
| :---: | :---: | :---: |
| Nad zámkem | Instalace zařízení | Vzdálená plocha |
| Modul Runtime aplikace | Uzamčení zařízení | Vzdálená správa |
| Správa aplikací | Služba Protokol událostí | Vzdálené volání procedur |
| Auto Play | Prostředí | Search |
| BitLocker | Ochrana Exploit Guard | SmartScreen |
| Prohlížeč | Průzkumník souborů | Požadavky na systém|
| Připojení | Internet Explorer | Wi-Fi |
| Delegování přihlašovacích údajů | Možnosti místní zásady zabezpečení | Správce připojení k Windows |
| Přihlašovací údaje uživatelského rozhraní | Příručka zabezpečení MS | Windows Defender|
| Ochrana dat | Starší verze MSS | Pracovní prostor Windows Ink |
| Device Guard | Napájení | Windows PowerShell |

Další informace o této funkci ve verzi preview najdete v článku [nastavení standardních hodnot zabezpečení Windows Intune](https://docs.microsoft.com/Intune/security-baseline-settings-windows).

## <a name="enroll-and-validate-your-first-device"></a>Registrace a ověření prvního zařízení

1. Registrace zařízení, budete potřebovat následující informace:
   * **Sériové číslo** – je uvedený na skříň zařízení
   * **ID produktu Windows** – nalezené pod **systému** > **o** z nabídky nastavení Windows.
   * Spuštění [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) bude poskytovat hodnotu hash souboru CSV pro registraci zařízení se všemi požadované informace. 
      * Spustit `Get-WindowsAutoPilotInfo – outputfile device1.csv` výstupu informací do souboru .csv, který mohl být importován do Intune.

   > [!NOTE]
   > Skript vyžadují zvýšenými oprávněními a spustit vzdálenou podepsané. Následující příkaz můžete povolit skript spuštěn správně. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`

   *  Tyto informace můžete získat po přihlášení k Windows 10 verze 1809 nebo vyšší zařízení, jak získat informace nebo případně váš prodejce hardwaru může poskytnout tyto informace při řazení nová zařízení.
1. Získání požadovaných informací a vrátit se **webu Azure portal**. Přejděte do **Microsoft Intune** > **registrace zařízení** > **registrace Windows** > **zařízení – Spravovat zařízení Windows Autopilot**vyberte **Import**a zvolte soubor CSV, které jste vytvořili nebo bylo zadáno.
1. Přidejte nyní zaregistrovaná zařízení do skupiny zabezpečení **zabezpečení pracovních stanic** vytvořili dříve.
1. Ze zařízení Windows 10, které chcete nakonfigurovat, přejděte do **nastavení Windows** > **aktualizace a zabezpečení** > **obnovení**. Zvolte **Začínáme** pod **obnovit tento počítač** a postupujte podle pokynů k resetování a změna konfigurace zařízení pomocí profilu a dodržování předpisů zásady nakonfigurované.

Po dokončení konfigurace zařízení, proveďte kontrolu a zkontrolujte konfiguraci. Potvrďte, že první zařízení je správně nakonfigurované před pokračováním nasazení.

## <a name="assignment-and-monitoring"></a>Přiřazení a sledování

Přiřazení zařízení a uživatelů bude vyžadovat mapování [vybrané profily](https://docs.microsoft.com/intune/device-profile-assign) zabezpečení skupiny a všechny nové uživatele, kteří budou mít oprávnění ke službě bude nutné přidat ke skupině zabezpečení.

Monitorování profilů pro lze provést pomocí sledování [profilů v Microsoft Intune](https://docs.microsoft.com/intune/device-profile-monitor).

## <a name="next-steps"></a>Další postup

[Microsoft Intune](https://docs.microsoft.com/intune/index) dokumentace

[Azure AD](https://docs.microsoft.com/azure/active-directory/index) dokumentace