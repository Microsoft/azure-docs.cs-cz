---
title: Nasazení pracovních stanic spravovaných azure - Azure Active Directory
description: Zjistěte, jak nasadit zabezpečené pracovní stanice spravované Azure, abyste snížili riziko porušení kvůli chybné konfiguraci nebo ohrožení zabezpečení.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d02b0299b6267fdd9d880d5bc0fe8c93d0edadc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672608"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Nasazení zabezpečené pracovní stanice spravované Azure

Nyní, když [rozumíte zabezpečeným pracovním stanicím](concept-azure-managed-workstation.md), je čas zahájit proces nasazení. Pomocí těchto pokynů použijete definované profily k vytvoření pracovní stanice, která je od začátku bezpečnější.

![Nasazení zabezpečené pracovní stanice](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Před nasazením řešení vyberte profil. V nasazení můžete použít více profilů současně a přiřadit je pomocí značek nebo skupin.

> [!NOTE]
> Použijte některý z profilů podle potřeby podle vašich požadavků. K jinému profilu se můžete přesunout tak, že ho přiřadíte v Microsoft Intune.

| Profil | Nízká | Rozšířené | Vysoká | Specializovaná | Zabezpečené | Izolovaný |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Uživatel ve službě Azure AD | Ano | Ano | Ano | Ano | Ano | Ano |
| Spravované Intune | Ano | Ano | Ano | Ano | Ano | Ano |
| Zařízení – registrace Azure AD | Ano |  |  |  |  | |   |
| Zařízení – azure ad připojen |   | Ano | Ano | Ano | Ano | Ano |
| Použitý směrný plán zabezpečení Intune |   | Ano <br> (Rozšířené) | Ano <br> (Vysokábezpečnost) | Ano <br> (NCSC) | Ano <br> (Zabezpečeno) | Není k dispozici |
| Hardware splňuje zabezpečené standardy Windows 10 |   | Ano | Ano | Ano | Ano | Ano |
| Program ATP programu Microsoft Defender povolen |   | Ano  | Ano | Ano | Ano | Ano |
| Odstranění administrátorských práv |   |   | Ano  | Ano | Ano | Ano |
| Nasazení pomocí aplikace Microsoft Autopilot |   |   | Ano  | Ano | Ano | Ano |
| Aplikace nainstalované jenom intune |   |   |   | Ano | Ano |Ano |
| Adresy URL omezené na schválený seznam |   |   |   | Ano | Ano |Ano |
| Internet blokován (příchozí/odchozí) |   |   |   |  |  |Ano |

> [!NOTE]
> V zabezpečené pracovní stanice naváděcí **zařízení** budou přiřazeny s profily a zásady. Uživatelé nebudou mít zásady použít přímo, což umožňuje sdílení zařízení (sdílená zařízení) být v platnosti. Pokud ve vašem nasazení není sdílena zabezpečená pracovní stanice nebo jsou potřeba jednotlivé uživatelské zásady, lze uživateli a zařízení přiřadit přiřazení profilů zásad uživatele. 

## <a name="license-requirements"></a>Licenční požadavky

Koncepty uvedené v této příručce předpokládají, že máte Microsoft 365 Enterprise E5 nebo ekvivalentní skladovou položku. Některá doporučení v této příručce lze implementovat s nižšími skum. Další informace naleznete v [tématu Microsoft 365 Enterprise Licensing](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise).

Chcete-li automatizovat zřizování licencí, zvažte [skupinové licencování](../users-groups-roles/licensing-groups-assign.md) pro uživatele.

## <a name="azure-active-directory-configuration"></a>Konfigurace služby Azure Active Directory

Azure Active Directory (Azure AD) spravuje uživatele, skupiny a zařízení pro vaše pracovní stanice správce. Povolte služby identit a funkce pomocí [účtu správce](../users-groups-roles/directory-assign-admin-roles.md).

Při vytváření účtu správce zabezpečené pracovní stanice vystavit účet aktuální pracovní stanice. Ujistěte se, že k této počáteční konfiguraci a celé globální konfiguraci používáte známé bezpečné zařízení. Chcete-li snížit expozici útoku při prvním spuštění, zvažte následující [pokyny k prevenci malwarových infekcí](/windows/security/threat-protection/intelligence/prevent-malware-infection).

Vyžadovat vícefaktorové ověřování, alespoň pro správce. Pokyny k implementaci najdete v [tématu Nasazení cloudového mfa.](../authentication/howto-mfa-getstarted.md)

### <a name="azure-ad-users-and-groups"></a>Uživatelé a skupiny Azure AD

1. Na webu Azure Najdete v tom, že se najdete na**Novém uživateli****služby** >  **Azure Active Directory** > Users .
1. Vytvořte správce zařízení podle kroků v [kurzu pro vytváření uživatelů](/Intune/quickstart-create-user).
1. Zadejte:

   * **Název** – správce zabezpečené pracovní stanice
   * **Uživatelské jméno** - `secure-ws-admin@identityitpro.com`
   * **Správce role** - adresáře**a** vyberte roli **Správce Intune.**

1. Vyberte **Vytvořit**.

Dále vytvoříte dvě skupiny: uživatele pracovní stanice a zařízení pracovní stanice.

Na webu Azure najdete skupinu**Skupiny Služby** >  **Azure active directory** > **Nové**.

1. Pro skupinu uživatelů pracovních stanic můžete nakonfigurovat [skupinové licencování](../users-groups-roles/licensing-groups-assign.md) pro automatizaci zřizování licencí uživatelům.
1. Pro skupinu uživatelů pracovní stanice zadejte:

   * **Typ skupiny** - zabezpečení
   * **Název skupiny** - Uživatelé zabezpečené pracovní stanice
   * **Typ členství** - Přiřazeno

1. Přidejte zabezpečeného správce pracovní stanice:`secure-ws-admin@identityitpro.com`
1. Můžete přidat další uživatele, kteří budou spravovat zabezpečené pracovní stanice.
1. Vyberte **Vytvořit**.
1. Pro skupinu pracovních stanic zadejte:

   * **Typ skupiny** - zabezpečení
   * **Název skupiny** - Zabezpečené pracovní stanice
   * **Typ členství** - Přiřazeno

1. Vyberte **Vytvořit**.

### <a name="azure-ad-device-configuration"></a>Konfigurace zařízení Azure AD

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Určení, kdo může připojit zařízení k Azure AD

Nakonfigurujte nastavení zařízení ve službě Active Directory tak, aby vaše skupina zabezpečení pro správu mohla připojit zařízení k vaší doméně. Postup konfigurace tohoto nastavení z webu Azure Portal:

1. Přejděte na**nastavení zařízení****Služby** >  **Azure active directory** > .
1. V **Selected** části **Uživatelé můžete připojit zařízení k Azure AD**a pak vyberte skupinu "Uživatelé zabezpečené pracovní stanice".

#### <a name="removal-of-local-admin-rights"></a>Odstranění práv místních správců

Tato metoda vyžaduje, aby uživatelé pracovních stanic VIP, DevOps a zabezpečené úrovně nemají na svých počítačích žádná práva správce. Postup konfigurace tohoto nastavení z webu Azure Portal:

1. Přejděte na**nastavení zařízení****Služby** >  **Azure active directory** > .
1. V části **Další místní správci na zařízeních spojených s Azure AD vyberte** **možnost Žádný** .

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Vyžadovat vícefaktorové ověřování pro připojení zařízení

Další posílení procesu připojování zařízení k Azure AD:

1. Přejděte na**nastavení zařízení****Služby** >  **Azure active directory** > .
1. V části **Vyžadovat vícefaktorové ušití vyberte Ano, chcete-li připojit zařízení**. **Yes**
1. Vyberte **Uložit**.

#### <a name="configure-mobile-device-management"></a>Konfigurace správy mobilních zařízení

Z portálu Azure:

1. Přejděte na **Azure Active Directory** > **Mobility (MDM a MAM)** > **Microsoft Intune**.
1. Změňte nastavení **uživatelského oboru MDM** na **Vše**.
1. Vyberte **Uložit**.

Tyto kroky umožňují spravovat libovolné zařízení pomocí Intune. Další informace najdete [v tématu Intune Quickstart: Nastavení automatické registrace pro zařízení s Windows 10](/Intune/quickstart-setup-auto-enrollment). Zásady konfigurace intune a dodržování předpisů vytvoříte v budoucím kroku.

#### <a name="azure-ad-conditional-access"></a>Podmíněný přístup Azure AD

Podmíněný přístup Azure AD může pomoci omezit privilegované úlohy správy na kompatibilní zařízení. Předdefinovaní členové skupiny **Secure Workstation Users** musí při přihlašování ke cloudovým aplikacím provádět vícefaktorové ověřování. Osvědčeným postupem je vyloučit účty nouzového přístupu ze zásad. Další informace najdete [v tématu Správa účtů pro nouzový přístup ve službě Azure AD](../users-groups-roles/directory-emergency-access.md).

## <a name="intune-configuration"></a>Konfigurace Intune

### <a name="configure-enrollment-status"></a>Konfigurace stavu registrace

Je důležité zajistit, aby vaše zabezpečená pracovní stanice byla důvěryhodným čistým zařízením. Při nákupu nových zařízení můžete trvat na tom, že jsou z výroby nastavená na [Systém Windows 10 Pro v režimu S](/Windows/deployment/Windows-10-pro-in-s-mode), což omezuje vystavení zranitelným místům během správy dodavatelského řetězce. Po obdržení zařízení od dodavatele můžete použít autopilot a změnit ho z režimu S. Následující pokyny obsahují podrobnosti o použití transformačního procesu.

Aby bylo zajištěno, že zařízení jsou plně nakonfigurovánpřed použitím, Intune poskytuje prostředky k **blokování používání zařízení, dokud nejsou nainstalovány všechny aplikace a profily**.

Z **portálu Azure**:

1. Přejděte na**výchozí** >  > **nastavení**stránky stavu registrace registrace registrace**zařízení** >  **Microsoft Intune** > Pro**registraci systému** > **Windows**.
1. Nastavte **možnost Zobrazit průběh instalace profilu aplikace** na **ano**.
1. Nastavte **blokování používání zařízení, dokud nebudou všechny aplikace a profily nainstalovány** na **Ano**.

### <a name="create-an-autopilot-deployment-profile"></a>Vytvoření profilu nasazení Autopilotu

Po vytvoření skupiny zařízení je nutné vytvořit profil nasazení pro konfiguraci zařízení Autopilot.

V Intune na webu Azure Portal:

1. Vyberte **možnost Profil** > **nasazení** > **registrace systému** > Windows**vytvořit profil**.
1. Zadejte:

   * Název – **profil nasazení zabezpečené pracovní stanice**.
   * Popis - **Nasazení zabezpečených pracovních stanic**.
   * Nastavte možnost **Převést všechna cílová zařízení na Autopilot** na **Ano**. Toto nastavení zajistí, že všechna zařízení v seznamu se zaregistrují pomocí služby nasazení Autopilot. Vyřízení registrace trvá 48 hodin.

1. Vyberte **další**.

   * V **režimu nasazení**zvolte **Vlastní nasazení (náhled).** Zařízení s tímto profilem jsou přidružena k uživateli, který zařízení zaregistruje. Při registraci zařízení se musí zadat přihlašovací údaje uživatele. Je důležité si uvědomit, že nasazení zařízení v režimu **vlastního nasazení** vám umožní nasadit přenosné počítače ve sdíleném modelu. K žádnému přiřazení uživatele nedojde, dokud nebude zařízení poprvé přiřazeno uživateli. V důsledku toho nebudou povoleny žádné uživatelské zásady, například nástroj BitLocker, dokud nebude přiřazení uživatele dokončeno. Další informace o přihlášení k zabezpečenému zařízení naleznete v [tématu vybrané profily](/intune/device-profile-assign).
   * **Připojit se k Azure AD jako** pole by měl zobrazit Azure **AD připojen** a být šedě.
   * Vyberte jazyk (oblast), **standard**typu uživatelského účtu . 

1. Vyberte **další**.

   * Pokud jste ji předkonfigurovali, vyberte značku oboru.

1. Vyberte **další**.
1. Zvolte **Přiřazení přiřazení** > **k vybraným skupinám** > **Selected Groups**. V **možnosti Vybrat skupiny, které chcete zahrnout**, zvolte Zabezpečené pracovní **stanice**.
1. Vyberte **další**.
1. Vyberte **Vytvořit** a vytvořte profil. Profil nasazení Autopilotu je teď možné přiřazovat zařízením.

Registrace zařízení v autopilotu poskytuje jiné uživatelské prostředí na základě typu zařízení a role. V našem příkladu nasazení ilustrujeme model, kde jsou zabezpečená zařízení hromadně nasazena a mohou být sdílena, ale při prvním použití je zařízení přiřazeno uživateli. Další informace najdete [v tématu Intune Autopilot device enrollment .](/intune/device-enrollment)

### <a name="configure-windows-update"></a>Konfigurace služby Windows Update

Udržování systému Windows 10 v aktuálním stavu je jednou z nejdůležitějších věcí, které můžete udělat. Chcete-li zachovat systém Windows v zabezpečeném stavu, nasaďte vyzváněcí okruh aktualizace ke správě tempa, které jsou aktualizace použity na pracovní stanice. 

Tento návod doporučuje vytvořit nový aktualizační okruh a změnit následující výchozí nastavení:

Na webu Azure Portal:

1. Přejděte na **Microsoft Intune** > **Software updates** > **Windows 10 Update Rings**.
1. Zadejte:

   * Název – **aktualizace pracovní stanice spravované Azure**
   * Servisní kanál - **Windows Insider - Rychlý**
   * Odklad aktualizace kvality (dny) - **3**
   * Doba odkladu aktualizace funkce (dny) - **3**
   * Automatické chování aktualizací - **Automatická instalace a restartování bez ovládacího prvku koncového uživatele**
   * Blokovat uživatele v pozastavení aktualizací systému Windows – **blokovat**
   * Vyžadovat souhlas uživatele s restartováním mimo pracovní dobu – **povinné**
   * Povolit uživateli restartovat (zapojené restartování) - **Povinné**
   * Přechod uživatelů na zapojený restart po automatickém restartu (dny) - **3**
   * Odložení zapojené připomenutí restartu (dny) - **3**
   * Nastavit konečný termín pro nevyřízené restartování (dny) - **3**

1. Vyberte **Vytvořit**.
1. Na kartě **Úlohy** přidejte skupinu **Zabezpečené pracovní stanice.**

Další informace o zásadách služby Windows Update naleznete [v tématu Policy CSP - Update](/windows/client-management/mdm/policy-csp-update).

### <a name="windows-defender-atp-intune-integration"></a>Integrace ochrany ATP v programu Windows Defender

Ochrana ATP v programu Windows Defender a Microsoft Intune spolupracují, aby zabránily narušení zabezpečení. Mohou také omezit dopad porušení. Funkce ATP poskytují detekci hrozeb v reálném čase a umožňují rozsáhlé auditování a protokolování koncových zařízení.

Pokud chcete nakonfigurovat integraci ochrany ATP v programu Windows Defender a Intune, přejděte na portál Azure.

1. Přejděte na **microsoft intune** > **zařízení dodržování** > **Windows Defender ATP**.
1. V kroku 1 v části **Konfigurace ochrany ATP programu Windows Defender**vyberte v centru zabezpečení programu Windows Defender **položku Connect Windows Defender ATP k aplikaci Microsoft Intune**.
1. V Centru zabezpečení v programu Windows Defender:

   1. Vyberte **Nastavení** > **Upřesňující funkce**.
   1. Pro **připojení Microsoft Intune**zvolte **Zapnuto**.
   1. Vyberte **Uložit předvolby**.

1. Po navázání připojení se vraťte do Intune a nahoře vyberte **Aktualizovat.**
1. Nastavte **Připojit zařízení s Windows verze 10.0.15063 a vyšší ke konektoru Windows Defender ATP** na **Zapnuto**.
1. Vyberte **Uložit**.

Další informace naleznete v [tématu Windows Defender Advanced Threat Protection](/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection).

### <a name="finish-workstation-profile-hardening"></a>Dokončovací kalení profilu pracovní stanice

Chcete-li úspěšně dokončit posílení zabezpečení řešení, stáhněte a spusťte příslušný skript. Najděte odkazy ke stažení pro požadovanou **úroveň profilu**:

| Profil | Umístění pro stažení | Název_souboru |
| --- | --- | --- |
| Nízké zabezpečení | Není dostupné. | Není dostupné. |
| Rozšířené zabezpečení | https://aka.ms/securedworkstationgit | Vylepšená pracovní stanice-Windows10-(1809).ps1 |
| Vysoká bezpečnost | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Specializovaná | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC – Windows10 (1803) SecurityBaseline.ps1 |
| Specializovaná shoda* | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Zabezpečené | https://aka.ms/securedworkstationgit | Zabezpečená pracovní stanice-Windows10-(1809)-ZabezpečeníSměr.ps1 |

\*Specialized Compliance je skript, který vynucuje konfiguraci Specialized poskytovanou v ncsc windows10 securitybaseline.

Po úspěšném spuštění skriptu můžete v Intune aktualizovat profily a zásady. Skripty pro rozšířené a zabezpečené profily vytvářejí zásady a profily pro vás, ale musíte je přiřadit skupině **zařízení Zabezpečené pracovní stanice.**

* Tady najdete profily konfigurace zařízení Intune vytvořené skripty: **Azure portal** > **Microsoft Intune** > **Device configuration** > **Profiles**.
* Tady najdete zásady dodržování předpisů pro zařízení Intune vytvořené skripty: **Azure portal** > **Microsoft Intune** > **Device Compliance** > **Policies**.

Chcete-li zkontrolovat změny provedené skripty, můžete profily exportovat. Tímto způsobem můžete určit další posílení, které může být požadováno, jak je uvedeno v [dokumentaci SECCON](/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework).

Spusťte skript `DeviceConfiguration_Export.ps1` pro export dat Intune z [úložiště DeviceConfiguration GiuHub](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) a exportujte všechny aktuální profily Intune.

## <a name="additional-configurations-and-hardening-to-consider"></a>Další konfigurace a kalení, které je třeba zvážit

Podle pokynů zde jste nasadili zabezpečenou pracovní stanici. Měli byste však také zvážit další ovládací prvky. Například:

* omezení přístupu k alternativním prohlížečům
* povolit odchozí protokol HTTP
* blokovat vybrané weby
* nastavení oprávnění pro spouštění vlastních skriptů prostředí PowerShell

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Nastavení pravidel u poskytovatele konfiguračních služeb brány firewall (CSP)

Můžete provést další změny ve správě příchozích i odchozích pravidel podle potřeby pro povolené a blokované koncové body. Jak budete pokračovat v posílení zabezpečení pracovní stanice, můžete uvolnit omezení, které odepře všechny příchozí a odchozí provoz. Můžete přidat povolené odchozí weby, které budou obsahovat běžné a důvěryhodné weby. Další informace naleznete v tématu [Konfigurační služba brány firewall](/Windows/client-management/mdm/firewall-csp).

Restriktivní správa provozu adresy URL zahrnuje:

* Odepřít všechny příchozí přenosy – nastavit ve skriptu profilu zabezpečené pracovní stanice.
* Odepřít všechny odchozí kromě vybraných služeb Azure a Microsoft, včetně Azure Cloud Shell a možnost povolit Azure Password Reset.

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

Pokud chcete poskytnout podrobnější informace o pravidlech blokování, můžete se obrátit na projekt Spamhaus, který spravuje [seznam blokování domény (DBL):](https://www.spamhaus.org/dbl/)dobrý zdroj, který lze použít jako pokročilou sadu pravidel pro implementaci pro blokování webů.

### <a name="manage-local-applications"></a>Správa místních aplikací

Zabezpečená pracovní stanice se po odebrání místních aplikací přesune do skutečně tvrzeného stavu, včetně aplikací pro zvýšení produktivity. Zde přidáte Chrome jako výchozí prohlížeč a pomocí Intune omezíte možnost uživatele upravit prohlížeč a jeho moduly plug-in.

#### <a name="deploy-applications-using-intune"></a>Nasazení aplikací pomocí Intune

V některých situacích jsou na zabezpečené pracovní stanici vyžadovány aplikace, jako je prohlížeč Google Chrome. Následující příklad obsahuje pokyny k instalaci chromu do zařízení ve skupině **zabezpečení Zabezpečené pracovní stanice**.

1. Stáhněte si [sadu Offline instalačního zařízení chrome pro 64bitový balíček windows](https://cloud.google.com/chrome-enterprise/browser/download/).
1. Extrahujte soubory a poznamenejte si umístění souboru. `GoogleChromeStandaloneEnterprise64.msi`
1. Na **portálu Azure** přejděte na **Microsoft Intune** > **Klientské aplikace** > **Aplikace Přidat** > **Add**.
1. V části **Typ aplikace**zvolte **Obchodní činnost**.
1. V části Soubor balíčku `GoogleChromeStandaloneEnterprise64.msi` **aplikace**vyberte soubor z extrahovaného umístění a vyberte **OK**.
1. V **části Informace o aplikaci**zadejte popis a vydavatele. Vyberte **OK**.
1. Vyberte **Přidat**.
1. Na kartě **Přiřazení** vyberte **možnost K dispozici pro zaregistrovaná zařízení** v části Typ **přiřazení**.
1. V části **Zahrnuté skupiny**přidejte skupinu **Zabezpečené pracovní stanice.**
1. Vyberte **OK**a pak vyberte **Uložit**.

Další informace o konfiguraci nastavení Chromu najdete v [tématu Správa prohlížeče Chrome pomocí aplikace Microsoft Intune](https://support.google.com/chrome/a/answer/9102677).

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Konfigurace portálu společnosti pro vlastní aplikace

V zabezpečeném režimu je instalace aplikace omezena na portál společnosti Intune. Instalace portálu však vyžaduje přístup k webu Microsoft Store. Ve zabezpečeném řešení můžete portál společnosti zpřístupnit všem zařízením v režimu offline.

Intune spravovaná kopie [portálu společnosti](/Intune/store-apps-company-portal-app) umožňuje přístup na vyžádání k dalším nástrojům, které můžete stlačit uživatelům zabezpečených pracovních stanic.

Možná budete muset nainstalovat 32bitové aplikace pro Windows nebo jiné aplikace, jejichž nasazení vyžaduje speciální přípravy. V takových případech může [nástroj pro přípravu obsahu win32 společnosti Microsoft](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) poskytnout soubor ve formátu připravenék použití `.intunewin` pro instalaci.

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>Podmíněný přístup umožňuje přístup k portálu Azure pouze zabezpečené pracovní stanici

Azure AD nabízí možnost spravovat a omezovat, kdo a co má přístup k portálu pro správu cloudu Azure. Povolení [podmíněného přístupu](../conditional-access/overview.md) zajistí, že prostředky může spravovat nebo měnit pouze zabezpečená pracovní stanice. Je nezbytné, aby při nasazování této funkce, které považujete, pokud funkce [nouzového přístupu](../users-groups-roles/directory-emergency-access.md) může nebo by měla být použita pouze pro extrémní případy a účet spravovaný prostřednictvím zásad.

> [!NOTE]
> Budete muset vytvořit skupinu uživatelů a zahrnout uživatele tísňového volání, který může obejít zásady podmíněného přístupu. Pro náš příklad máme bezpečnostní skupinu s názvem **Emergency BreakGlass**

1. Přejděte na **portál** > Azure**Microsoft Intune** > **podmíněný přístup – zásady** > **nové zásady**.
1. Zadejte **název** zásady.
1. Vybrat **možnost Uživatel a skupiny** > Vybrat uživatele a**skupiny** 
1. Vyberte **Zahrnout** > **role adresáře** > Zvolte role > globálního správce, správce privilegovaných rolí, správce privilegovaného ověřování, správce zabezpečení, správce dodržování předpisů, správce podmíněného přístupu, správce aplikací, správce cloudových aplikací, správce služby Intune
1. Vyberte **Vyloučit** > Vybrat **uživatele a skupiny** > Vybrat **vybrané vyloučené uživatele** > Vyberte **skupinu Emergency BreakGlass.**
1. Vyberte **Cloudové aplikace nebo akce** > Vybrat **všechny cloudové aplikace**
1. Vybrat **podmínky** > Vybrat **platformy zařízení** > zvolit konfiguraci **Ano** > Vybrat **platformy pro výběr zařízení** Zvolte **Windows**
1. Vyberte **ovládací prvky aplikace Access** > Vybrat možnost **Udělit přístup** **Ano** > **Zvolit, aby zařízení vyžadovalo označení jako vyhovující**. 
1. Vybrat **možnost Povolit zásady** > **zapnuto**
 
Tato sada zásad zajistí, že správci musí používat kompatibilní zařízení s Windows, které je nastavené intune a WDATP. 

Organizace by také měly zvážit blokování starších ověřovacích protokolů ve svých prostředích. Existuje několik způsobů, jak provést tento úkol, další informace o blokování starších ověřovacích protokolů naleznete v [článku: Blokovat starší verze ověřování azure ad s podmíněným přístupem](../conditional-access/block-legacy-authentication.md).

### <a name="use-powershell-to-create-custom-settings"></a>Vytvoření vlastního nastavení pomocí PowerShellu

PowerShell můžete také použít k rozšíření možností správy hostitelů. Tento ukázkový skript nastaví výchozí pozadí na hostiteli. Je to funkce, která je dostupná taky na webu Azure Portal a profily. Ukázkový skript slouží pouze pro ilustraci funkce prostředí PowerShell.

Na zabezpečených pracovních stanicích může být nutné nastavit některé vlastní ovládací prvky a nastavení. Tento příklad změní pozadí pracovní stanice pomocí schopnosti aplikace Powershell snadno identifikovat zařízení jako zabezpečenou pracovní stanici připravenou k použití.

Skript [SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) z Centra skriptů společnosti Microsoft umožňuje systému Windows načíst tento [bezplatný obecný obrázek na pozadí](https://i.imgur.com/OAJ28zO.png) při spuštění.

1. Stáhněte skript do místního zařízení.
1. Aktualizujte customerXXXX a umístění stahování obrázku na pozadí. V našem příkladu nahradíme zákazníkaXXXX na pozadí.
1. Přejděte na **portál Azure Portal** > **Microsoft Intune** > **Device konfigurace** > **PowerShell skripty** > **Přidat**.
1. Zadejte **název** skriptu a zadejte **umístění Skript**.
1. Vyberte **Konfigurovat**.
   1. Nastavit **Spustit tento skript pomocí přihlášených přihlašovacích údajů** na **Ano**.
   1. Vyberte **OK**.
1. Vyberte **Vytvořit**.
1. Vyberte **možnost Úlohy** > **Vybrat skupiny**.
   1. Přidejte skupinu zabezpečení **Zabezpečené pracovní stanice**.
   1. Vyberte **Uložit**.

## <a name="enroll-and-validate-your-first-device"></a>Registrace a ověření prvního zařízení

1. K registraci zařízení potřebujete následující informace:
   * **Sériové číslo** - nalezeno na podvozku zařízení.
   * **Windows Product ID** - najdete v části **System** > **About** z nabídky Nastavení systému Windows.
   * Můžete spustit [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) získat soubor hash CSV se všemi požadovanými informacemi pro registraci zařízení.
   
     Spusťte `Get-WindowsAutoPilotInfo – outputfile device1.csv` výstup informací jako soubor CSV, který můžete importovat do Intune.

     > [!NOTE]
     > Skript vyžaduje zvýšená práva. Běží jako vzdálené podepsané. Příkaz `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` umožňuje správné spuštění skriptu.

   * Tyto informace můžete získat po přihlášení k zařízení s Windows 10 verze 1809 nebo vyšší. Tyto informace může poskytnout také prodejce hardwaru.
1. Na **webu Azure Portal**přejděte na informace o**registraci** > zařízení **Microsoft Intune** > Device pro**registraci zařízení pro registraci** > windows **– správa zařízení Windows Autopilot**.
1. Vyberte **Importovat** a zvolte soubor CSV.
1. Přidejte zařízení do skupiny zabezpečení **Zabezpečené pracovní stanice.**
1. Na zařízení s Windows 10, které chcete konfigurovat, přejděte na**aktualizaci** >  **nastavení** > systému Windows &**obnovení zabezpečení**.
   1. V části **Resetování tohoto počítače**zvolte **Začínáme** .
   1. Podle pokynů resetujte a překonfigurujte zařízení pomocí nakonfigurovaných zásad profilu a dodržování předpisů.

Po konfiguraci zařízení proveďte kontrolu a zkontrolujte konfiguraci. Před pokračováním v nasazení zkontrolujte, zda je první zařízení správně nakonfigurováno.

## <a name="assign-devices"></a>Přiřazení zařízení

Chcete-li přiřadit zařízení a uživatele, je třeba namapovat [vybrané profily](/intune/device-profile-assign) na skupinu zabezpečení. Všichni noví uživatelé, kteří vyžadují oprávnění ke službě, musí být také přidáni do skupiny zabezpečení.

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>Použití protokolu ATP sentinelu a programu Windows Defender ke sledování a reakci na bezpečnostní incidenty

Monitorování nasazení zabezpečené pracovní stanice lze provést povolením [Sentinel] a využitím [správy hrozeb a zranitelnosti](/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt) Pokyny neposkytují vyčerpávající lov hrozeb, ale poskytují zdravý rozum úsilí ke sledování a reaguje na potenciální bezpečnostní incidenty.

**Azure Sentinel** použijeme k: 

* Shromažďování dat z Intune, portálu Azure a Azure AD pro monitorování uživatelů a zařízení
* Pomozte prozkoumat podezřelou aktivitu konfigurace uživatelů a zařízení
* A řídit schopnost prozkoumat bezpečnostní vyšetřování pomocí WDATP

Monitorování sentinelu vyžaduje, aby byly nastaveny konektory ke zdrojům dat, jako je Azure AD.

1. Na **webu Azure Portal**přejděte na **Azure Sentinel (Preview)** > vybrat **přidat**
1. V části **Volba pracovního prostoru, který chcete přidat do Azure Sentinelu,** vyberte **vytvořit nový pracovní prostor.**
1. Zadejte:
   * **Pracovní prostor log Analytics** – "Monitorování zabezpečené pracovní stanice"
   * **Předplatné** – vyberte aktivní předplatné.
   * **Skupina prostředků** - vyberte ** Vytvořit nový** > Secure Workstation RG > **ok**
   * **Poloha** – vyberte umístění, které je geograficky nejvhodnější pro vaše nasazení.
   * **Cenová úroveň** – výběr **za GB (2018)**
1. Vyberte **OK**.

Dále připojíme dostupné zdroje dat zabezpečené pracovní stanice k monitorování.

1. Na **webu Azure Portal**přejděte do **pracovního prostoru Azure Sentinel** > vybrat pracovní prostor monitorování zabezpečené pracovní **stanice**
1. Vybrat **datové konektory**
1. Po kontrole předpokladu zvolte stránka > otevřený konektor **služby Azure Active Directory** > otevřít. Přejděte na konfiguraci a vyberte **připojit** pro oba protokoly přihlášení Azure AD, stejně jako protokoly auditování Azure AD.
1. Po kontrole předpokladu zvolte **stránka aktivity** > otevření > azure. Přejděte ke konfiguraci protokolů aktivit Azure > vyberte předplatné > vybrat **připojit**

Jak jsou data shromažďována sentinelem, budete moct sledovat aktivitu výběrem **portálu Azure**, přejděte na **Přehled Azure Sentinelu** 

Atp **(WDATP) programu Windows Defender (Windows Defender)** použijeme k:

* Neustále sledujte a sledujte chybné chyby a chybné konfigurace
* Využijte WDATP k upřednostnění dynamických hrozeb ve volné přírodě
* Podpořte korelaci zranitelných míst pomocí výstrah detekce koncových bodů a odezvy (EDR)
* Použití řídicího panelu k identifikaci chyby zabezpečení na úrovni počítače během šetření
* Vytlačit nápravu do Intune

Nakonfigurujte [řídicí panel ochrany ATP programu Defender](https://securitycenter.windows.com/machines). Pomocí pokynů na [panelu řízení správy ohrožení & zranitelnosti](/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights).

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>Sledování aktivity aplikace pomocí microsoft monitoring agenta (MMA)
Počínaje pracovní stanicí Specialized je uzamykatelná skříňka aplikací povolena pro sledování aktivity aplikací na pracovní stanici. Aby bylo monitorování integrováno do pracovního prostoru Analýzy protokolů, musí být sledován agent MMA a konfigurace. 

> [!NOTE]
> Profil pracovní stanice Specialized obsahuje zásady monitorování nástroje AppLocker. Nasazení zásad je vyžadováno pro monitorování aktivity aplikace na klientovi

Nasazení agenta MMA pomocí skriptu Intune PowerShell

1. Stáhněte [instalační skript do místního zařízení](https://aka.ms/securedworkstationgit).
1. Aktualizace parametrů, **$WorkSpaceID** a **$WorkSpaceKey**
1. Přejděte na **portál Azure Portal** > **Microsoft Intune** > **Device konfigurace** > **PowerShell skripty** > **Přidat**.
1. Zadejte **název** skriptu a zadejte **umístění Skript**.
1. Vyberte **Konfigurovat**.
   1. Nastavit **Spustit tento skript pomocí přihlášených přihlašovacích údajů** na **Ano**.
   1. Vyberte **OK**.
1. Vyberte **Vytvořit**.
1. Vyberte **možnost Úlohy** > **Vybrat skupiny**.
   1. Přidejte skupinu zabezpečení **Zabezpečené pracovní stanice**.
   1. Vyberte **Uložit**.

Dále je nutné nastavit Log Analytics pro příjem nových protokolů
1. Na **webu Azure Portal**přejděte na web **Log Analytics Workspace** > Select – "Monitorování zabezpečené pracovní stanice"
1. Vyberte **upřesnit nastavení** > **Data** > **Windows Protokoly událostí**
1. V **shromažďovat události z následujících protokolů událostí** 
1. Zadejte:
   * 'Microsoft-Windows-AppLocker/EXE a DLL' > Unselect **Informational**
   * 'Microsoft-Windows-AppLocker/MSI a Script' > Unselect **Informational**
   * 'Microsoft-Windows-AppLocker/Packaged app-Deployment' > Unselect **Informational**
   * 'Microsoft-Windows-AppLocker/Packaged app-Execution' > Unselect **Informational**
1. Vyberte **Uložit**.

Protokolování aplikací bude k dispozici ve vybraném pracovním prostoru Log Analytics.

## <a name="monitoring"></a>Monitorování

* Přečtěte si, jak [rozpoznat hrozby pomocí Azure Sentinelu](/azure/sentinel/tutorial-detect-threats)
* [Vyšetřování incidentů s Azure Sentinelem](/azure/sentinel/tutorial-investigate-cases)
* [Nastavení automatických odpovědí na hrozby v Azure Sentinelu](/azure/sentinel/tutorial-respond-threats-playbook)
* Zjistěte, jak zkontrolovat [skóre expozice](/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score)
* Zkontrolovat [doporučení zabezpečení](/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation)
* Správa [nápravy](/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation) zabezpečení
* Správa [zjišťování a odezvy koncového bodu](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)
* Sledování profilů pomocí [sledování profilu Intune](/intune/device-profile-monitor).

## <a name="next-steps"></a>Další kroky

* Další informace o [Microsoft Intune](/intune/index).
* Pochopit [Azure AD](../index.yml).
* Práce s [pokročilým programem ochrany před hrozbami v programu Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)
* Objevte [Azure Sentinel](/azure/sentinel/)
