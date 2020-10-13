---
title: Nasazení pracovních stanic spravovaných Azure – Azure Active Directory
description: Naučte se, jak nasadit zabezpečené pracovní stanice spravované v Azure, abyste snížili riziko narušení způsobené chybnou konfigurací nebo ohrožením.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 841bc3ae4fbddb376ea4da8141bf4df3f895c4dc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89269552"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Nasazení zabezpečené pracovní stanice spravované v Azure

Teď, když [rozumíte zabezpečeným pracovním stanicím](concept-azure-managed-workstation.md), je čas zahájit proces nasazení. Pomocí těchto pokynů vytvoříte pomocí definovaných profilů pracovní stanici, která je od začátku bezpečnější.

![Nasazení zabezpečené pracovní stanice](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Před nasazením řešení vyberte profil. V nasazení můžete použít více profilů současně a přiřadit je k klíčům nebo skupinám.

> [!NOTE]
> Použijte libovolný profil podle potřeby podle vašich požadavků. Můžete přejít na jiný profil tak, že ho přiřadíte v Microsoft Intune.

| Profil | Nízká | Rozšířené | Vysoké | Specializovaná | Psán | Isolated |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Uživatel v Azure AD | Yes | Yes | Yes | Yes | Yes | Yes |
| Spravovaná v Intune | Yes | Yes | Yes | Yes | Yes | Yes |
| Zařízení – zaregistrované v Azure AD | Yes |  |  |  |  | |   |
| Zařízení – připojeno k Azure AD |   | Yes | Yes | Yes | Yes | Yes |
| Použité standardní hodnoty zabezpečení Intune |   | Yes <br> Rozšíření | Yes <br> (HighSecurity) | Yes <br> (NCSC) | Yes <br> Psán | Není k dispozici |
| Hardware splňuje zabezpečené standardy pro Windows 10. |   | Yes | Yes | Yes | Yes | Yes |
| Povolené ATP programu Microsoft Defender |   | Yes  | Yes | Yes | Yes | Yes |
| Odebrání oprávnění správce |   |   | Yes  | Yes | Yes | Yes |
| Nasazení pomocí programu Microsoft autopilot |   |   | Yes  | Yes | Yes | Yes |
| Aplikace nainstalované jenom pomocí Intune |   |   |   | Yes | Yes |Yes |
| Adresy URL omezené na seznam schválených |   |   |   | Yes | Yes |Yes |
| Blokované na internetu (příchozí/odchozí) |   |   |   |  |  |Yes |

> [!NOTE]
> V doprovodných **zařízeních** zabezpečených pracovních stanic se přiřadí profily a zásady. Uživatelé nebudou mít k těmto zásadám aplikovány přímo, což umožňuje sdílení zařízení (sdílená zařízení) v platnosti. Pokud zabezpečená pracovní stanice není sdílená v nasazení, nebo jsou nutné jednotlivé zásady uživatele, přiřazení profilů uživatelských zásad se dá přiřadit uživateli a zařízení. 

## <a name="license-requirements"></a>Licenční požadavky

V konceptech popsaných v tomto průvodci se předpokládá, že máte Microsoft 365 Enterprise E5 nebo ekvivalentní SKU. Některá doporučení v tomto průvodci můžete implementovat s nižšími SKU. Další informace najdete v tématu [Microsoft 365 Enterprise licencování](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Pro automatizaci zřizování licencí zvažte [licencování na základě skupin](../users-groups-roles/licensing-groups-assign.md) pro vaše uživatele.

## <a name="azure-active-directory-configuration"></a>Konfigurace Azure Active Directory

Azure Active Directory (Azure AD) spravují uživatele, skupiny a zařízení pro pracovní stanice vašich správců. Pomocí [účtu správce](../users-groups-roles/directory-assign-admin-roles.md)povolte služby a funkce identity.

Když vytvoříte zabezpečený účet správce pracovní stanice, vystavte si účet pro aktuální pracovní stanici. Ujistěte se, že k provedení této počáteční konfigurace a všech globálních konfigurací používáte známé bezpečné zařízení. Chcete-li snížit riziko útoku při prvním spuštění, zvažte následující [pokyny, abyste zabránili napadení malwarem](/windows/security/threat-protection/intelligence/prevent-malware-infection).

Vyžadovat službu Multi-Factor Authentication alespoň pro správce. Pokyny k implementaci najdete v tématu [nasazení cloudového vícefaktorového ověřování](../authentication/howto-mfa-getstarted.md) .

### <a name="azure-ad-users-and-groups"></a>Uživatelé a skupiny služby Azure AD

1. V Azure Portal přejděte na **Azure Active Directory**  >  **Uživatelé**  >  **Nový uživatel**.
1. Postupujte podle kroků v [kurzu Vytvoření uživatele](/Intune/quickstart-create-user)a vytvořte Správce zařízení.
1. Zadejte:

   * **Název** – zabezpečený Správce pracovní stanice
   * **Uživatelské jméno** - `secure-ws-admin@identityitpro.com`
   * **Role adresáře**  -  **Správce s omezenými** oprávněními a vyberte roli **správce Intune** .

1. Vyberte **Vytvořit**.

V dalším kroku vytvoříte dvě skupiny: uživatelé pracovní stanice a zařízení pracovní stanice.

Z Azure Portal přejděte na **Azure Active Directory**  >  **skupiny**  >  **Nová skupina**.

1. Pro skupinu uživatelé pracovní stanice možná budete chtít nakonfigurovat [licencování na základě skupin](../users-groups-roles/licensing-groups-assign.md) pro automatizaci zřizování licencí pro uživatele.
1. Pro skupinu uživatelé pracovní stanice zadejte:

   * **Typ skupiny** – zabezpečení
   * **Název skupiny** – uživatelé zabezpečených pracovních stanic
   * Přiřazený **typ členství**

1. Přidejte uživatele správce zabezpečené pracovní stanice: `secure-ws-admin@identityitpro.com`
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

1. Přejděte na **Azure Active Directory** > **Zařízení** > **Nastavení zařízení**.
1. Zvolte možnost **vybrané** v části **Uživatelé můžou připojovat zařízení do Azure AD**a pak vybrat skupinu "zabezpečená pracovní stanice uživatelů".

#### <a name="removal-of-local-admin-rights"></a>Odebrání oprávnění místního správce

Tato metoda vyžaduje, aby uživatelé virtuálních počítačů s VIP, DevOps a zabezpečenými pracovními stanicemi nemají na svých počítačích žádná práva správce. Konfigurace tohoto nastavení z Azure Portal:

1. Přejděte na **Azure Active Directory** > **Zařízení** > **Nastavení zařízení**.
1. **V zařízeních připojených k Azure AD**vyberte **žádné** v rámci dalších místních správců.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Vyžadovat službu Multi-Factor Authentication pro připojení zařízení

Další posílení procesu připojení zařízení do služby Azure AD:

1. Přejděte na **Azure Active Directory** > **Zařízení** > **Nastavení zařízení**.
1. Pokud chcete připojit zařízení **, vyberte v** části **vyžadovat vícefaktorové ověřování službu Multi-Factor auth**.
1. Vyberte **Uložit**.

#### <a name="configure-mobile-device-management"></a>Konfigurace správy mobilních zařízení

Z Azure Portal:

1. Přejděte do **Microsoft Intune Azure Active Directory**  >  **mobility (MDM a mam)**  >  **Microsoft Intune**.
1. Změňte nastavení **oboru uživatele MDM** na **vše**.
1. Vyberte **Uložit**.

Tyto kroky umožňují spravovat jakékoli zařízení v Intune. Další informace najdete v tématu [rychlý Start k Intune: nastavení automatického zápisu pro zařízení s Windows 10](/Intune/quickstart-setup-auto-enrollment). V dalším kroku vytvoříte konfiguraci Intune a zásady dodržování předpisů.

#### <a name="azure-ad-conditional-access"></a>Podmíněný přístup Azure AD

Podmíněný přístup Azure AD může přispět k omezení privilegované úlohy správy na vyhovující zařízení. Aby se při přihlašování ke cloudovým aplikacím provádělo ověřování Multi-Factor Authentication, musí být předdefinovaná členové skupiny **Uživatelé zabezpečených pracovních stanic** . Osvědčeným postupem je vyloučit z těchto zásad účty pro nouzový přístup. Další informace najdete v tématu [Správa účtů pro nouzový přístup ve službě Azure AD](../users-groups-roles/directory-emergency-access.md).

## <a name="intune-configuration"></a>Konfigurace Intune

### <a name="configure-enrollment-status"></a>Konfigurace stavu registrace

Je důležité zajistit, aby zabezpečená pracovní stanice byla důvěryhodného čistého zařízení. Při nákupu nových zařízení můžete nakoupit tovární nastavení na [Windows 10 pro v režimu S](/Windows/deployment/Windows-10-pro-in-s-mode), což omezuje vystavení ohrožení zabezpečení při správě dodavatelských řetězců. Jakmile od dodavatele obdržíte zařízení, můžete ho pomocí nástroje autopilot změnit z režimu S. Následující doprovodné materiály obsahují podrobné informace o použití procesu transformace.

Aby se zajistilo, že zařízení jsou před použitím plně nakonfigurovaná, poskytne Intune způsob, jak **blokovat používání zařízení, dokud se nenainstalují všechny aplikace a profily**.

Z **Azure Portal**:

1. Přejít na **Microsoft Intune**  >  **registrace zařízení**–  >  **Windows enrollment**  >  **Enrollment Status Page**  >  **výchozí**  >  **Nastavení**stránka registrace registrace zařízení Windows – stav
1. Nastavte **Zobrazit průběh instalace profilu aplikace** na **Ano**.
1. Nastavte **blokování použití zařízení, dokud nebudou všechny aplikace a profily nainstalované** na **Ano**.

### <a name="create-an-autopilot-deployment-profile"></a>Vytvoření profilu nasazení Autopilotu

Po vytvoření skupiny zařízení je nutné vytvořit profil nasazení pro konfiguraci zařízení s autopilotem.

V Intune Azure Portal:

1. Vyberte **registrace zařízení**  >  **Windows enrollment**  >  **profily nasazení**registrace Windows  >  **vytvořit profil**.
1. Zadejte:

   * Název – **profil nasazení zabezpečené pracovní stanice**.
   * Popis – **nasazení zabezpečených pracovních stanic**.
   * Nastavte možnost **Převést všechna cílová zařízení na Autopilot** na **Ano**. Toto nastavení zajistí, že všechna zařízení v seznamu se zaregistrují pomocí služby nasazení Autopilot. Vyřízení registrace trvá 48 hodin.

1. Vyberte **Další**.

   * V **režimu nasazení**vyberte možnost **samoobslužné nasazení (Preview)**. Zařízení s tímto profilem jsou přidružená k uživateli, který zařízení zaregistruje. Při registraci zařízení se musí zadat přihlašovací údaje uživatele. Je nutné si uvědomit, že nasazení zařízení v režimu **samoobslužného nasazení** vám umožní nasazovat přenosné počítače do sdíleného modelu. Neproběhne žádné přiřazení uživatele, dokud se zařízení poprvé přiřadí uživateli. V důsledku toho nebudou povoleny žádné zásady uživatele, jako je například BitLocker, až do dokončení přiřazení uživatele. Další informace o tom, jak se přihlásit k zabezpečenému zařízení, najdete v tématu [Vybrané profily](/intune/device-profile-assign).
   * **Připojení k Azure AD** by se mělo zobrazit jako **připojené k Azure AD** , které se bude zobrazovat šedě.
   * Vyberte jazyk (oblast), typ uživatelského účtu **Standard**. 

1. Vyberte **Další**.

   * Vyberte značku oboru, pokud jste ji předem nakonfigurovali.

1. Vyberte **Další**.
1. Vyberte **přiřazení**  >  **přiřadit k**  >  **vybraným skupinám**. V **možnosti vybrat skupiny, které se mají zahrnout**, vyberte **zabezpečené pracovní stanice**.
1. Vyberte **Další**.
1. Vyberte **Vytvořit** a vytvořte profil. Profil nasazení Autopilotu je teď možné přiřazovat zařízením.

Registrace zařízení v autopilotu poskytuje jiné uživatelské prostředí na základě typu zařízení a role. V našem příkladu nasazení jsme ilustrují model, ve kterém jsou zabezpečená zařízení nasazená a sdílená, ale při prvním použití se zařízení přiřadí uživateli. Další informace najdete v tématu [registrace zařízení v Intune pro Automatický pilot](/intune/device-enrollment).

### <a name="configure-windows-update"></a>Konfigurace web Windows Update

Udržování aktuálnosti Windows 10 je jedním z nejdůležitějších věcí, které můžete udělat. Chcete-li zachovat systém Windows v zabezpečeném stavu, nasadíte aktualizační kanál, který bude spravovat tempo použití aktualizací pro pracovní stanice. 

Tento návod doporučuje vytvořit nový aktualizační kanál a změnit následující výchozí nastavení:

Na webu Azure Portal:

1. Přejít na **Microsoft Intune**  >  **aktualizace softwaru**  >  **aktualizační kanály Windows 10**.
1. Zadejte:

   * Název – **aktualizace pro pracovní stanice spravované službou Azure**
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
1. Na kartě **přiřazení** přidejte skupinu **zabezpečených pracovních stanic** .

Další informace o web Windows Updatech zásadách najdete v tématu [zásady CSP – aktualizace](/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Integrace ochrany ATP v programu Windows Defender

Ochrana ATP a Microsoft Intune v programu Windows Defender vzájemně spolupracuje, aby nedocházelo k narušení zabezpečení. Můžou také omezit dopad porušení zabezpečení. Schopnosti ATP poskytují detekci hrozeb v reálném čase a umožňují rozsáhlé auditování a protokolování zařízení koncových bodů.

Pokud chcete nakonfigurovat integraci ochrany ATP v programu Windows Defender a Intune, přečtěte si Azure Portal.

1. Přejděte na **Microsoft Intune**  >  **dodržování předpisů pro zařízení**v  >  **programu Windows Defender ATP**.
1. V kroku 1 v části **Konfigurace ochrany ATP v programu Windows Defender**vyberte možnost **připojit ATP v programu windows Defender k Microsoft Intune v Security Center Windows Defenderu**.
1. V Centru zabezpečení v programu Windows Defender:

   1. Vyberte **Nastavení**  >  **Rozšířené funkce**.
   1. Pro **Microsoft Intune připojení**vyberte **zapnuto**.
   1. Vyberte **Uložit předvolby**.

1. Po navázání připojení se vraťte do Intune a v horní části vyberte **aktualizovat** .
1. Nastavte **Připojit zařízení s Windows verze 10.0.15063 a vyšší ke konektoru Windows Defender ATP** na **Zapnuto**.
1. Vyberte **Uložit**.

Další informace najdete v tématu [Rozšířená ochrana před internetovými útoky v programu Windows Defender](/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Dokončení posílení profilu pracovní stanice

Aby bylo možné úspěšně dokončit posílení zabezpečení řešení, Stáhněte a spusťte příslušný skript. Najděte odkazy ke stažení pro požadovanou **úroveň profilu**:

| Profil | Umístění pro stahování | Bitmap |
| --- | --- | --- |
| Nízká úroveň zabezpečení | N/A | N/A |
| Rozšířené zabezpečení | https://aka.ms/securedworkstationgit | Enhanced-Workstation-Windows10-(1809). ps1 |
| Vysoké zabezpečení | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809). ps1 |
| Specializovaná | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC – Windows10 (1803) SecurityBaseline.ps1 |
| Specializované dodržování předpisů * | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10 (1803). ps1 |
| Psán | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809) -SecurityBaseline.ps1 |

\* Specializované dodržování předpisů je skript, který vynutil specializovanou konfiguraci poskytnutou v NCSC Windows10 SecurityBaseline.

Po úspěšném spuštění skriptu můžete v Intune dělat aktualizace profilů a zásad. Skripty pro rozšířené a zabezpečené profily vytvářejí zásady a profily, ale musíte je přiřadit ke skupině zařízení **zabezpečených pracovních stanic** .

* Tady najdete profily konfigurace zařízení Intune vytvořené skripty: **Azure Portal**  >  **Microsoft Intune**  >  **konfiguračních**  >  **profilů**zařízení.
* Tady najdete zásady dodržování předpisů zařízením Intune, které vytvořily skripty: **Azure Portal**  >  **Microsoft Intune**  >  **zásady dodržování předpisů zařízením**  >  **Policies**.

Chcete-li zkontrolovat změny provedené skripty, můžete exportovat profily. Tímto způsobem můžete určit další posílení zabezpečení, které může být vyžadováno, jak je uvedeno v [dokumentaci k SECCON](/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework).

Spusťte skript pro export dat Intune `DeviceConfiguration_Export.ps1` z [úložiště DeviceConfiguration GiuHub](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) a exportujte všechny aktuální profily Intune.

## <a name="additional-configurations-and-hardening-to-consider"></a>Další konfigurace a posílení zabezpečení pro zvážení

Podle pokynů uvedených tady jste nasadili zabezpečenou pracovní stanici. Měli byste ale také zvážit další ovládací prvky. Například:

* omezení přístupu k alternativním prohlížečům
* povolení odchozího HTTP
* blokovat výběr webů
* nastavení oprávnění pro spouštění vlastních skriptů PowerShellu

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Nastavení pravidel ve zprostředkovateli služby brány firewall pro konfiguraci (CSP)

U povolených a blokovaných koncových bodů můžete provádět další změny v rámci správy pravidel příchozího i odchozího připojení. Jak budete nadále posílit zabezpečenou pracovní stanici, můžete snížit omezení, které zakazuje veškerý příchozí a odchozí provoz. Je možné přidat povolené odchozí lokality pro zahrnutí běžných a důvěryhodných webů. Další informace najdete v tématu [Služba konfigurace brány firewall](/Windows/client-management/mdm/firewall-csp).

Omezující Správa provozu adres URL zahrnuje:

* Zamítnout všechny příchozí přenosy – nastavené v skriptu profilu zabezpečené pracovní stanice.
* Odmítne všechny odchozí s výjimkou vybraných služeb Azure a Microsoft, včetně Azure Cloud Shell a možnosti Povolit resetování hesla Azure.

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

Pokud chcete zajistit přesnější rozlišení pravidel blokování, můžete se podívat na projekt Spamhaus, který udržuje [seznam blokovaných domén (dvojitý)](https://www.spamhaus.org/dbl/): dobrým prostředkem, který se má použít jako Pokročilá sada pravidel, která se má implementovat pro blokování lokalit.

### <a name="manage-local-applications"></a>Správa místních aplikací

Zabezpečená pracovní stanice se během odebírání místních aplikací přesune do skutečně zpřísněného stavu, včetně aplikací pro produktivitu. Tady můžete přidat Chrome jako výchozí prohlížeč a pomocí Intune omezit schopnost uživatele měnit prohlížeč a jeho moduly plug-in.

#### <a name="deploy-applications-using-intune"></a>Nasazení aplikací pomocí Intune

V některých situacích jsou v zabezpečené pracovní stanici vyžadovány aplikace, jako je například prohlížeč Google Chrome. Následující příklad uvádí pokyny k instalaci aplikace Chrome do zařízení ve skupině zabezpečení **zabezpečených pracovních stanic**.

1. Stáhněte si balíček Chrome instalačního programu offline [pro Windows 64-bit](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Extrahujte soubory a poznamenejte si umístění `GoogleChromeStandaloneEnterprise64.msi` souboru.
1. V **Azure Portal** přejděte do **Microsoft Intune**  >  **Client apps**  >  **aplikace**klienta aplikace  >  **Přidat**.
1. V části **Typ aplikace**vyberte **obchodní**.
1. V části **soubor balíčku aplikace**vyberte `GoogleChromeStandaloneEnterprise64.msi` soubor z extrahovaného umístění a vyberte **OK**.
1. V části **informace o aplikaci**zadejte popis a vydavatele. Vyberte **OK**.
1. Vyberte **Přidat**.
1. Na kartě **přiřazení** vyberte **dostupné pro zaregistrovaná zařízení** v části **Typ přiřazení**.
1. V části **zahrnuté skupiny**přidejte skupinu **zabezpečených pracovních stanic** .
1. Vyberte **OK**a pak vyberte **Uložit**.

Další informace o konfiguraci nastavení Chrome najdete v tématu [Správa prohlížeče Chrome pomocí Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Konfigurace portálu společnosti pro vlastní aplikace

V zabezpečeném režimu je instalace aplikace omezená na portál společnosti Intune. Instalace portálu ale vyžaduje přístup k Microsoft Store. V zabezpečeném řešení můžete portál společnosti zpřístupnit všem zařízením v režimu offline.

Kopie [portál společnosti](/Intune/store-apps-company-portal-app) spravovaná v Intune poskytuje přístup na vyžádání k dalším nástrojům, které můžete přemístit do uživatelů zabezpečených pracovních stanic.

Možná budete muset nainstalovat Windows 32 – bitové aplikace nebo jiné aplikace, jejichž nasazení vyžaduje speciální přípravy. V takových případech může [Nástroj pro přípravu obsahu Microsoft Win32](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) poskytnout soubor formátu připravený k použití `.intunewin` pro instalaci.

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>Podmíněný přístup povoluje přístup k Azure Portal jenom zabezpečeným pracovním stanicím.

Azure AD nabízí možnost spravovat a omezovat, kdo a co má přístup k portálu pro správu cloudu Azure. Povolením [podmíněného přístupu](../conditional-access/overview.md) zajistíte, aby mohli spravovat nebo měnit prostředky jenom zabezpečená pracovní stanice. Při nasazení této funkce je nutné vzít v úvahu, že pokud se funkce [nouzového přístupu](../users-groups-roles/directory-emergency-access.md) může nebo měla používat jenom pro extrémní případy a účet spravovaný prostřednictvím zásad.

> [!NOTE]
> Budete muset vytvořit skupinu uživatelů a začlenit svého naléhavého uživatele, který může obejít zásady podmíněného přístupu. V našem příkladu máme skupinu zabezpečení s názvem **Nouzový BreakGlass**

1. Přejděte do **Azure Portal**  >  **Microsoft Intune**  >  **podmíněný přístup – zásady**  >  **nové zásady**.
1. Zadejte **název** zásady.
1. Vybrat **uživatele a skupiny**–  >  **Vybrat uživatele a skupiny** 
1. Vyberte možnost **Zahrnout**  >  **role adresáře** > zvolte role > globální správce, správce privilegovaných rolí, správce privilegovaného ověřování, správce zabezpečení, správce dodržování předpisů, správce podmíněného přístupu, správce aplikace, správce cloudových aplikací, Správce služby Intune.
1. Vyberte možnost **vyloučit** > zvolte **uživatele a skupiny** > vyberte **Vybrat vyloučené uživatele** > vyberte svou **Nouzový BreakGlass** skupinu.
1. Vyberte **cloudové aplikace nebo akce** > vybrat **všechny cloudové aplikace** .
1. Vyberte **podmínky** > vyberte **platformy zařízení** > zvolte konfigurovat **Ano** > vyberte **Vybrat platformy zařízení** zvolte **Windows**
1. Vyberte **řízení přístupu** > vyberte **udělit přístup** **Ano** > zvolte **vyžadovat, aby zařízení bylo označené jako vyhovující**. 
1. Vyberte **Povolit zásadu**  >  **na**
 
Tato sada zásad zajistí, že správci musí používat kompatibilní zařízení s Windows, které je nastavené službou Intune, a rozšířená. 

Organizace by měly také zvážit blokování starších ověřovacích protokolů ve svých prostředích. Existuje několik způsobů, jak tuto úlohu provést. Další informace o blokování starších protokolů ověřování najdete v článku [Postupy: blokování staršího ověřování do služby Azure AD s podmíněným přístupem](../conditional-access/block-legacy-authentication.md).

### <a name="use-powershell-to-create-custom-settings"></a>Použití PowerShellu k vytvoření vlastního nastavení

PowerShell můžete také použít k rozšiřování možností správy hostitele. Tento ukázkový skript nastaví výchozí pozadí na hostiteli. Je to možnost, která je dostupná taky prostřednictvím Azure Portal a profilů. Ukázkový skript slouží pouze k ilustraci funkcí prostředí PowerShell.

Možná budete muset nastavit některé vlastní ovládací prvky a nastavení na zabezpečených pracovních stanicích. Tento příklad změní pozadí pracovní stanice pomocí schopnosti prostředí PowerShell snadno identifikovat zařízení jako připravenou a zabezpečenou pracovní stanici.

Skript [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) z skriptovacího centra Microsoftu umožňuje, aby systém Windows při spuštění načetl tento [bezplatný a obecný obrázek na pozadí](https://i.imgur.com/OAJ28zO.png) .

1. Stáhněte si skript do místního zařízení.
1. Aktualizujte customerXXXX a umístění pro stahování obrázku pozadí. V našem příkladu nahrazujeme customerXXXX na pozadí.
1. Přejděte do části **Azure Portal**  >  **Microsoft Intune**  >  **Konfigurace zařízení**  >  **skripty PowerShellu**  >  **Přidat**.
1. Zadejte **název** skriptu a zadejte **umístění skriptu**.
1. Vyberte **Konfigurovat**.
   1. Nastavte **Spustit tento skript pomocí přihlašovacích údajů přihlášeného** k **Ano**.
   1. Vyberte **OK**.
1. Vyberte **Vytvořit**.
1. Vyberte **přiřazení**  >  **Vybrat skupiny**.
   1. Přidejte **zabezpečené pracovní stanice**skupiny zabezpečení.
   1. Vyberte **Uložit**.

## <a name="enroll-and-validate-your-first-device"></a>Registrace a ověření prvního zařízení

1. K registraci zařízení potřebujete následující informace:
   * **Sériové číslo** – našlo se na skříni zařízení.
   * **ID produktu Windows** – v části **systém**  >  **v** nabídce nastavení systému Windows se našel.
   * Pomocí rutiny [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) můžete získat soubor hash CSV se všemi požadovanými informacemi pro registraci zařízení.
   
     Spusťte `Get-WindowsAutoPilotInfo – outputfile device1.csv` pro výstup informací jako soubor CSV, který můžete naimportovat do Intune.

     > [!NOTE]
     > Tento skript vyžaduje zvýšená oprávnění. Spouští se jako vzdálený podpis. `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned`Příkaz umožňuje, aby byl skript správně spuštěn.

   * Tyto informace můžete získat tak, že se přihlásíte k zařízení s Windows 10 verze 1809 nebo vyšší. Tyto informace může poskytnout i prodejce hardwaru.
1. V **Azure Portal**můžete přejít na **Microsoft Intune**  >  **registrace zařízení registrace**zařízení  >  s**Windows**  >  **– spravovat zařízení s Windows autopilotem**.
1. Vyberte **importovat** a zvolte soubor CSV.
1. Přidejte zařízení do skupiny zabezpečení **zabezpečených pracovních stanic** .
1. V zařízení s Windows 10, které chcete nakonfigurovat, nastavte obnovení zabezpečení na **Windows Settings**  >  **Update &**  >  **Recovery**.
   1. V části **resetovat tento počítač** **vyberte Začínáme.**
   1. Podle pokynů obnovte a znovu nakonfigurujte zařízení pomocí nakonfigurovaných zásad profilu a dodržování předpisů.

Po nakonfigurování zařízení dokončete kontrolu a zkontrolujte konfiguraci. Před pokračováním v nasazení potvrďte, že první zařízení je správně nakonfigurované.

## <a name="assign-devices"></a>Přiřazení zařízení

Chcete-li přiřadit zařízení a uživatele, je třeba namapovat [Vybrané profily](/intune/device-profile-assign) na skupinu zabezpečení. Všichni noví uživatelé, kteří potřebují ke službě oprávnění, musí být také přidáni do skupiny zabezpečení.

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>Monitorování a reakce na incidenty zabezpečení pomocí Sentinel a ochrany ATP v programu Windows Defender

Monitorování nasazení zabezpečených pracovních stanic se dá udělat tak, že povolíte [Sentinel] a využijete [hrozby a správu ohrožení zabezpečení](/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt) . Tyto pokyny neposkytují vyčerpávající povědomí o hrozbách, ale poskytují dobré běžné úsilí, které monitoruje a reaguje na potenciální incidenty zabezpečení.

**Azure Sentinel** použijeme k těmto akcím: 

* Shromažďování dat z Intune, Azure Portal a Azure AD pro monitorování uživatelů a zařízení
* Pomáhat s prověřením podezřelé aktivity konfigurace uživatelů a zařízení
* A umožňují prozkoumat šetření zabezpečení pomocí Rozšířená

Monitorování Sentinel vyžaduje, aby byly nastaveny konektory na vaše zdroje dat, jako je například Azure AD.

1. V **Azure Portal**přejít na **Azure Sentinel (Preview)** > vyberte **Přidat** .
1. V části **zvolte pracovní prostor, který se má přidat do Azure Sentinel** vyberte **vytvořit nový pracovní prostor** .
1. Zadejte:
   * **Pracovní prostor Log Analytics** – "zabezpečené monitorování pracovní stanice"
   * **Předplatné** – vyberte své aktivní předplatné.
   * **Skupina prostředků** – vyberte * * vytvořit novou * * > zabezpečenou pracovní stanici RG > **OK** .
   * **Umístění** – vyberte umístění, které je geograficky nejlépe uzpůsobené pro vaše nasazení.
   * **Cenová úroveň** – vybrat **za GB (2018)**
1. Vyberte **OK**.

Teď budeme k monitorování připojovat dostupné zdroje dat zabezpečené pracovní stanice.

1. V **Azure Portal**klikněte na **pracovní prostor Azure Sentinel** > vyberte pracovní prostor pro **monitorování zabezpečené pracovní stanice** .
1. Výběr **datových konektorů**
1. Po zkontrolování požadované součásti vyberte možnost **Azure Active Directory** > otevřete stránku konektoru >. Přejděte ke konfiguraci a vyberte **připojit** pro protokoly přihlášení k Azure AD i protokoly auditu Azure AD.
1. Po kontrole požadované součásti vyberte **aktivitu Azure** > otevřít stránku konektoru >. Pokračujte v konfiguraci protokolů aktivit Azure > vyberte své předplatné > vyberte **připojit** .

Po shromáždění dat pomocí nástroje Sentinel budete moci sledovat aktivitu výběrem **Azure Portal**, přechodem do **Azure Sentinel – přehled** 

V **programu Windows Defender ATP (rozšířená)** použijeme následující:

* Nepřetržité sledování a monitorování chyb zabezpečení a chybných konfigurací
* Využití rozšířená k určení priorit dynamických hrozeb v přírodě
* Zvýšení korelace chyb zabezpečení s výstrahami detekce koncových bodů a odezva (EDR)
* Pomocí řídicího panelu Identifikujte chybu zabezpečení na úrovni počítače během šetření.
* Nabízení náprav do Intune

Nakonfigurujte [řídicí panel ATP pro Defender](https://securitycenter.windows.com/machines). Pomocí pokynů na [řídicím panelu pro správu ohrožení zabezpečení & hrozeb](/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights).

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>Monitorování aktivity aplikace pomocí Microsoft Monitoring Agent (MMA)
Od specializované pracovní stanice je povolený zámek aplikace pro monitorování aktivity aplikace na pracovní stanici. Aby bylo monitorování integrované v pracovním prostoru Log Analytics, musí být dodržen agent MMA a konfigurace. 

> [!NOTE]
> Profil specializované pracovní stanice obsahuje zásady monitorování AppLockeru. K monitorování aktivity aplikace na klientovi se vyžaduje nasazení zásad.

Nasazení agenta MMA pomocí skriptu PowerShellu pro Intune

1. Stáhněte si instalační [skript do místního zařízení](https://aka.ms/securedworkstationgit).
1. Aktualizace parametrů **$WorkSpaceID** a **$WorkSpaceKey**
1. Přejděte do části **Azure Portal**  >  **Microsoft Intune**  >  **Konfigurace zařízení**  >  **skripty PowerShellu**  >  **Přidat**.
1. Zadejte **název** skriptu a zadejte **umístění skriptu**.
1. Vyberte **Konfigurovat**.
   1. Nastavte **Spustit tento skript pomocí přihlašovacích údajů přihlášeného** k **Ano**.
   1. Vyberte **OK**.
1. Vyberte **Vytvořit**.
1. Vyberte **přiřazení**  >  **Vybrat skupiny**.
   1. Přidejte **zabezpečené pracovní stanice**skupiny zabezpečení.
   1. Vyberte **Uložit**.

Dál musíte nastavit Log Analytics pro příjem nových protokolů.
1. V **Azure Portal**klikněte na **pracovní prostor Log Analytics** > vybrat-' zabezpečené monitorování pracovní stanice '
1. Výběr **rozšířených**  >  **dat**nastavení  >  **protokoly událostí systému Windows**
1. V **části shromažďovat události z následujících protokolů událostí** 
1. Zadejte:
   * ' Microsoft-Windows-AppLocker/EXE a DLL ' > zrušit výběr **informativní**
   * Microsoft-Windows-AppLocker/MSI a skript > nevybranými **informacemi**
   * Microsoft-Windows-AppLocker/zabalená aplikace-nasazení > zrušit výběr **informativní**
   * Microsoft-Windows-AppLocker/zabalená aplikace-spuštění > zrušit výběr **informativní**
1. Vyberte **Uložit**.

Protokolování aplikace bude k dispozici ve vybraném pracovním prostoru Log Analytics.

## <a name="monitoring"></a>Monitorování

* Naučte se [detekovat hrozby pomocí služby Azure Sentinel](../../sentinel/tutorial-detect-threats-built-in.md)
* [Prozkoumat incidenty pomocí služby Azure Sentinel](../../sentinel/tutorial-investigate-cases.md)
* [Nastavení automatických odpovědí na hrozby v Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)
* Informace o tom, jak zkontrolovat [skóre expozice](/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score)
* Zkontrolovat [doporučení zabezpečení](/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation)
* Správa [náprav](/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation) zabezpečení
* Správa [zjišťování a odezvy koncových bodů](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)
* Monitorujte profily pomocí [monitorování profilů Intune](/intune/device-profile-monitor).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [Microsoft Intune](/intune/index).
* Pochopení [služby Azure AD](../index.yml).
* Práce s [rozšířenou ochranou proti hrozbám v programu Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)
* Zjistit [Sentinel Azure](../../sentinel/index.yml)