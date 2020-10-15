---
title: Plánování nasazení zařízení Azure Active Directory
description: Vyberte strategie integrace zařízení Azure AD, které vyhovují potřebám vaší organizace.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/15/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf30570f10f176c47aed0f99127e03a027eff775
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92093085"
---
# <a name="plan-your-azure-active-directory-device-deployment"></a>Plánování nasazení zařízení Azure Active Directory

Tento článek vám pomůže vyhodnotit metody pro integraci zařízení s Azure AD, zvolit plán implementace a poskytuje klíčové odkazy na podporované nástroje pro správu zařízení.

Na šířku zařízení, ze kterých se uživatelé přihlásí, se rozbalí. Organizace můžou poskytovat stolní počítače, přenosné počítače, telefony, tablety a další zařízení. Vaši uživatelé můžou přinášet vlastní pole zařízení a přistupovat k informacím z různých míst. V tomto prostředí je vaše úloha jako správce, aby byly prostředky vaší organizace zabezpečené napříč všemi zařízeními.

Azure Active Directory (Azure AD) umožňuje vaší organizaci plnit tyto cíle pomocí správy identit zařízení. Teď můžete svoje zařízení ve službě Azure AD získat a řídit z centrálního umístění v [Azure Portal](https://portal.azure.com/). Získáte tak jednotné prostředí, zvýšené zabezpečení a zkrátíte čas potřebný ke konfiguraci nového zařízení.

Vaše zařízení je možné integrovat do služby Azure AD několika způsoby:

* Zařízení můžete [zaregistrovat](concept-azure-ad-register.md) ve službě Azure AD.

* [Připojení zařízení](concept-azure-ad-join.md) ke službě Azure AD (jenom pro Cloud) nebo

* [Vytvořte si hybridní připojení ke službě Azure AD](concept-azure-ad-join-hybrid.md) mezi zařízeními v místní službě Active Directory a Azure AD. 

## <a name="learn"></a>Learn

Než začnete, ujistěte se, že jste obeznámeni s [přehledem správy identit zařízení](overview.md).

### <a name="benefits"></a>Výhody

Klíčové výhody poskytování identity Azure AD pro vaše zařízení:

* Zvyšte produktivitu – pomocí Azure AD můžou vaši uživatelé dělat [bezproblémové přihlašování (SSO)](./azuread-join-sso.md) k místním i cloudovým prostředkům, což jim umožní zajistit jejich produktivitu bez ohledu na to, kde jsou.

* Zvýšení zabezpečení – zařízení Azure AD umožňují použít [zásady podmíněného přístupu (CA)](../conditional-access/require-managed-devices.md) na prostředky na základě identity zařízení nebo uživatele. Zásady certifikační autority mohou nabídnout dodatečnou ochranu pomocí [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md). Předpokladem pro zvýšení zabezpečení pomocí strategie [ověřování bez hesla](../authentication/concept-authentication-passwordless.md) je připojení zařízení k Azure AD.

* Zlepšení uživatelského prostředí – pomocí identit zařízení ve službě Azure AD můžete svým uživatelům poskytnout snadný přístup k cloudovým prostředkům vaší organizace z osobních i firemních zařízení. Správci můžou povolit [Enterprise State Roaming](enterprise-state-roaming-overview.md) pro jednotné prostředí napříč všemi zařízeními s Windows.

* Zjednodušení nasazení a správy – Správa identit zařízení zjednodušuje proces zavedení zařízení do Azure AD s využitím automatického [pilotního projektu Windows](/windows/deployment/windows-autopilot/windows-10-autopilot), [hromadného zřizování](/mem/intune/enrollment/windows-bulk-enroll)a [samoobslužné služby: funkce OOBE](../user-help/user-help-join-device-on-network.md). Tato zařízení můžete spravovat pomocí nástrojů pro správu mobilních zařízení (MDM), jako je [Microsoft Intune](/mem/intune/fundamentals/what-is-intune), a jejich identit v [Azure Portal](https://portal.azure.com/).

### <a name="training-resources"></a>Školicí materiály

Video:  [podmíněný přístup s ovládacími prvky zařízení](https://youtu.be/NcONUf-jeS4)

Nejčastější dotazy: [Nejčastější dotazy k správě zařízení Azure AD](faq.md)  a [nastavení a roaming dat](enterprise-state-roaming-faqs.md) 

## <a name="plan-the-deployment-project"></a>Plánování projektu nasazení

Při určování strategie pro toto nasazení v prostředí zvažte potřeby vaší organizace.

### <a name="engage-the-right-stakeholders"></a>Zapojení správných zúčastněných stran

Když projekty technologie selžou, obvykle to vznikne z důvodu neshodných očekávání na dopad, výsledky a zodpovědnosti. Pokud se chcete těmto nástrah vyhnout, ujistěte se, že jste si [jisti, že jste připravují správné zúčastněné strany](../fundamentals/active-directory-deployment-plans.md) a že role účastníků v projektu jsou dobře pochopitelné. 

Pro tento plán přidejte do svého seznamu následující účastníky:

| Role| Popis |
| - | - |
| Správce zařízení| Zástupce týmu zařízení, který dokáže ověřit, jestli plán splňuje požadavky na zařízení vaší organizace. |
| Správce sítě| Zástupce ze síťového týmu, který může zajistit, aby splňoval požadavky na síť. |
| Tým správy zařízení| Tým, který spravuje inventář zařízení. |
| Týmy pro správu specifické pro operační systém| Týmy, které podporují a spravují konkrétní verze operačního systému Může se například jednat o tým zaměřený na Mac nebo iOS. |

### <a name="plan-communications"></a>Plán komunikace

Komunikace je zásadní pro úspěch jakékoli nové služby. Proaktivně komunikujte s vašimi uživateli, jak se změní, když se změní, a jak získat podporu, pokud se jim setkávají problémy.

### <a name="plan-a-pilot"></a>Plánování pilotního projektu

Doporučujeme, aby počáteční konfigurace vaší integrační metody byla v testovacím prostředí, nebo s malou skupinou testovacích zařízení. Podívejte [se na osvědčené postupy pro pilotní nasazení](../fundamentals/active-directory-deployment-plans.md).

Nasazení připojení k hybridní službě Azure AD je jednoduché a je 100% úloha správce bez nutnosti zásahu koncového uživatele. Je možné, že budete chtít provést [řízené ověřování pro připojení k hybridní službě Azure AD](hybrid-azuread-join-control.md) a teprve potom ho v celé organizaci najednou povolit.

## <a name="choose-your-integration-methods"></a>Výběr metod integrace

Vaše organizace může v jednom tenantovi Azure AD používat víc metod integrace zařízení. Cílem je zvolit metody, které jsou vhodné pro vaše zařízení zabezpečeně spravovat ve službě Azure AD. Existuje mnoho parametrů, které řídí toto rozhodnutí včetně vlastnictví, typů zařízení, primární cílové skupiny a infrastruktury vaší organizace.

Následující informace vám pomohou rozhodnout, jaké metody integrace použít.

### <a name="decision-tree-for-devices-integration"></a>Rozhodovací strom pro integraci zařízení

Pomocí tohoto stromu můžete určit možnosti pro zařízení vlastněná organizací. 

> [!NOTE]
> V tomto diagramu nejsou k disBYOD žádné scénáře osobní nebo vlastní zařízení (). Vždycky mají za následek registraci v Azure AD.

 ![Rozhodovací strom](./media/plan-device-deployment/flowchart.png)

### <a name="comparison-matrix"></a>Srovnávací matice

zařízení s iOS a Androidem můžou být jenom registrovaná v Azure AD. Následující tabulka uvádí nejdůležitější požadavky na klientská zařízení Windows. Použijte ho jako přehled a podrobněji si prozkoumejte různé metody integrace.

| Aspekty | Registrováno v Azure AD| Připojení k Azure AD| Hybridní připojení k Azure AD |
| - | - | - | - |
| **Klientské operační systémy**| | |  |
| Zařízení s Windows 10| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png) |
| Zařízení se škálováním na nižší úrovni Windows (Windows 8.1 nebo Windows 7)| | | ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png) |
|**Možnosti přihlášení**| | |  |
| Místní přihlašovací údaje pro koncové uživatele| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| |  |
| Heslo| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png) |
| PIN kód zařízení| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| |  |
| Windows Hello| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| |  |
| Windows Hello pro firmy| | ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png) |
| Bezpečnostní klíče FIDO 2,0| | ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png) |
| Aplikace Microsoft Authenticator (s heslem)| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png) |
|**Klíčové funkce**| | |  |
| Jednotné přihlašování ke cloudovým prostředkům| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png) |
| Jednotné přihlašování k místním prostředkům| | ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png) |
| Podmíněný přístup <br> (Vyžadovat, aby zařízení byla označená jako vyhovující) <br> (Musí se spravovat přes MDM)| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png) | ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)|![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png) |
Podmíněný přístup <br>(Vyžadovat zařízení připojená k hybridní službě Azure AD)| | | ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)
| Samoobslužné resetování hesla z přihlašovací obrazovky Windows| | ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png) |
| Resetování PIN kódu Windows Hello| | ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png) |
| Roaming podnikového stavu napříč zařízeními| | ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png) |


## <a name="azure-ad-registration"></a>Registrace Azure AD 

Registrovaná zařízení se často spravují pomocí [Microsoft Intune](/mem/intune/enrollment/device-enrollment). Zařízení se registrují v Intune mnoha různými způsoby v závislosti na operačním systému. 

Zařízení registrovaná v Azure AD poskytují podporu pro vlastní zařízení (BYOD) a zařízení vlastněná podnikem pro jednotné přihlašování ke cloudovým prostředkům. Přístup k prostředkům je založen na [zásadách certifikační autority](../conditional-access/require-managed-devices.md) Azure AD, které se vztahují na zařízení a uživatele.

### <a name="registering-devices"></a>Registrace zařízení

Registrovaná zařízení se často spravují pomocí [Microsoft Intune](/mem/intune/enrollment/device-enrollment). Zařízení se registrují v Intune mnoha různými způsoby v závislosti na operačním systému. 

Mobilní zařízení BYOD a vlastněná podnikem jsou registrována uživateli, kteří si instalují aplikaci Portál společnosti.

* [iOS](/mem/intune/user-help/install-and-sign-in-to-the-intune-company-portal-app-ios)

* [Android](/mem/intune/user-help/enroll-device-android-company-portal)

* [Windows 10](/mem/intune/user-help/enroll-windows-10-device)

Pokud je registrace zařízení nejlepší volbou pro vaši organizaci, podívejte se na následující zdroje informací:

* Tento přehled [zařízení registrovaných pro Azure AD](concept-azure-ad-register.md).

* Tato dokumentace pro koncové uživatele k [registraci vašeho osobního zařízení v síti vaší organizace](../user-help/user-help-register-device-on-network.md).

## <a name="azure-ad-join"></a>Připojení k Azure AD

Služba Azure AD JOIN umožňuje přechod na model cloudového modelu v systému Windows. Nabízí skvělou základnu, pokud plánujete modernizovat správu zařízení a omezit náklady na IT související s tímto zařízením. Připojení k Azure AD funguje jenom se zařízeními s Windows 10. Zvažte jako první volbu pro nová zařízení.

[Zařízení připojená k Azure AD](azuread-join-sso.md) se ale můžou přihlašuje k místním prostředkům v případě, že jsou v síti organizace, můžou se ověřit na místních serverech, jako jsou soubory, tisk a další aplikace.

Pokud je to nejlepší možnost pro vaši organizaci, podívejte se na následující zdroje informací:

* Tento přehled [zařízení připojených k Azure AD](concept-azure-ad-join.md)

* Seznamte se s [plánem implementace služby Azure AD JOIN](azureadjoin-plan.md).

### <a name="provisioning-azure-ad-join-to-your-devices"></a>Zřizování služby Azure AD JOIN k vašim zařízením

Při zřizování služby Azure AD JOIN máte k dispozici následující přístupy:

* Samoobslužná služba: [první spuštění Windows 10](azuread-joined-devices-frx.md)

Pokud máte na zařízení nainstalovaný systém Windows 10 Professional nebo Windows 10 Enterprise, výchozím prostředím pro zařízení vlastněná společností bude proces instalace.

* [Rozhraní Windows mimo box (OOBE) nebo nastavení systému Windows](../user-help/user-help-join-device-on-network.md)

* [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)

* [Hromadná registrace](/mem/intune/enrollment/windows-bulk-enroll)

Po pečlivém [porovnání těchto přístupů](azureadjoin-plan.md)vyberte svůj postup nasazení.

Můžete určit, že je připojení k Azure AD nejlepším řešením pro zařízení. Toto zařízení se už může nacházet v jiném stavu. Tady jsou pokyny k upgradu.

| Aktuální stav zařízení| Požadovaný stav zařízení| Postupy |
| - | - | - |
| Připojené k místní doméně| Připojení k Azure AD| Před připojením k Azure AD odpojte zařízení od místní domény. |
| Připojení k hybridní službě Azure AD| Připojení k Azure AD| Před připojením k Azure AD odpojte zařízení od místní domény a ze služby Azure AD. |
| Registrováno v Azure AD| Připojení k Azure AD| Zrušení registrace zařízení před připojením k Azure AD |


## <a name="hybrid-azure-ad-join"></a>Hybridní připojení k Azure AD

Pokud máte místní prostředí Active Directory a chcete se připojit k počítačům připojeným k doméně služby Active Directory do služby Azure AD, můžete k tomu využít hybridní službu Azure AD JOIN. Podporuje [širokou škálu zařízení s Windows](hybrid-azuread-join-plan.md), včetně zařízení s Windows na nižší úrovni.

Většina organizací už má zařízení připojená k doméně a spravuje je přes Zásady skupiny nebo System Center Configuration Manager (SCCM). V takovém případě doporučujeme nakonfigurovat službu hybridního připojení k Azure AD, abyste začali získávat výhody a využili stávající investice.

Pokud je pro vaši organizaci nejlepší možností připojení k hybridní službě Azure AD, přečtěte si následující zdroje informací:

* Tento přehled [zařízení připojených k hybridní službě Azure AD](concept-azure-ad-join-hybrid.md).

* Seznamte se s plánem [implementace připojení k hybridní službě Azure AD](hybrid-azuread-join-plan.md) .

### <a name="provisioning-hybrid-azure-ad-join-to-your-devices"></a>Zřizování hybridního připojení služby Azure AD k vašim zařízením

[Projděte si infrastrukturu identity](hybrid-azuread-join-plan.md). Azure AD Connect poskytuje průvodce pro konfiguraci hybridního připojení ke službě Azure AD pro:

* [Federované domény](hybrid-azuread-join-federated-domains.md)

* [Spravované domény](hybrid-azuread-join-managed-domains.md)

Pokud se instalace požadované verze Azure AD Connect pro vás nepoužívá, přečtěte si téma [Postup ruční konfigurace hybridního připojení k Azure AD](hybrid-azuread-join-manual.md). 

> [!NOTE] 
> Místní zařízení s Windows 10 připojené k doméně se pokusí automaticky připojit ke službě Azure AD, aby se ve výchozím nastavení připojila k hybridní službě Azure AD. To bude úspěšné jenom v případě, že jste nastavili správné prostředí. 

Můžete určit, že je pro zařízení nejlepší řešení Azure AD JOIN a že toto zařízení už může být v jiném stavu. Tady jsou pokyny k upgradu.

| Aktuální stav zařízení| Požadovaný stav zařízení| Postupy |
| - | - | - |
| Připojení k místní doméně| Připojení k hybridní službě Azure AD| Použití Azure AD Connect nebo AD FS pro připojení k Azure |
| Místní pracovní skupina je připojená nebo nová.| Připojení k hybridní službě Azure AD| Podporováno při [autopilotu Windows](/windows/deployment/windows-autopilot/windows-autopilot). Jinak musí být zařízení připojená k místní doméně před připojením k hybridní službě Azure AD. |
| Připojené k Azure AD| Připojení k hybridní službě Azure AD| Odpojte se od Azure AD, který ho vloží do místní pracovní skupiny nebo nového stavu. |
| Registrované v Azure AD| Připojení k hybridní službě Azure AD| Závisí na verzi Windows. [Podívejte se na tyto informace](hybrid-azuread-join-plan.md). |

## <a name="manage-your-devices"></a>správu zařízení

Jakmile vaše zařízení zaregistrujete nebo připojíte k Azure AD, použijte [Azure Portal](https://portal.azure.com/) jako centrální místo ke správě identit zařízení. Stránka Azure Active Directory zařízení vám umožní:

* [Konfigurace nastavení zařízení](device-management-azure-portal.md#configure-device-settings)
* Abyste mohli spravovat zařízení s Windows, musíte být místním správcem. [Azure AD aktualizuje toto členství pro zařízení připojená k Azure AD](assign-local-admin.md)a automaticky je přidá do role Správce zařízení jako správci do všech připojených zařízení.

Ujistěte se, že prostředí vyčistíte tím, že budete [Spravovat zastaralá zařízení](manage-stale-devices.md)a Zaměřte se na prostředky na správu aktuálních zařízení.

* [Kontrola protokolů auditu souvisejících se zařízením](device-management-azure-portal.md#audit-logs)

### <a name="supported-device-management-tools"></a>Podporované nástroje pro správu zařízení

Správci můžou tato registrovaná a připojená zařízení zabezpečit a dále řídit pomocí dalších nástrojů pro správu zařízení. Tyto nástroje poskytují způsob, jak vyhovět konfiguracím vyžadovaným v organizaci, jako je třeba šifrování úložiště, složitost hesla, instalace softwaru a aktualizace softwaru. 

Zkontrolujte podporované a nepodporované platformy pro integrovaná zařízení:

| Nástroje pro správu zařízení| Registrováno v Azure AD| Připojení k Azure AD| Hybridní připojení k Azure AD|
| - | - | - | - |
| [Správa mobilních zařízení (MDM) ](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) <br>Příklad: Microsoft Intune| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)|  |
| [Spoluspráva pomocí Microsoft Intune a koncového bodu Microsoft Configuration Manager](/mem/configmgr/comanage/overview) <br>(Windows 10 a novější)| | ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)| ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)|  |
| [Zásady skupiny](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11))<br>(Jenom Windows)| | | ![Zaškrtnutí těchto hodnot.](./media/plan-device-deployment/check.png)|  |



 Doporučujeme, abyste při správě zařízení se systémem iOS nebo Android zvážili [Microsoft Intune správu mobilních aplikací (MAM)](/mem/intune/apps/app-management) se systémem nebo bez ní.

 Správci mohou také [nasadit platformy infrastruktury virtuálních klientských počítačů (VDI)](howto-device-identity-virtual-desktop-infrastructure.md) hostujících operační systémy Windows ve svých organizacích a zjednodušit tak správu a snížit náklady prostřednictvím konsolidace a centralizovaného využívání prostředků. 

### <a name="troubleshoot-device-identities"></a>Řešení problémů s identitami zařízení

* [Řešení potíží se zařízeními pomocí příkazu dsregcmd](troubleshoot-device-dsregcmd.md)

* [Řešení potíží s nastavením Enterprise State Roaming v Azure Active Directory](enterprise-state-roaming-troubleshooting.md)

Pokud dochází k problémům s dokončením hybridního připojení služby Azure AD pro zařízení s Windows připojená k doméně, přečtěte si téma:

* [Řešení potíží s hybridním připojením ke službě Azure AD pro aktuální zařízení s Windows](troubleshoot-hybrid-join-windows-current.md)

* [Řešení potíží s hybridním připojením k Azure AD pro zařízení se systémem Windows na nižší úrovni](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Další kroky

* [Plánování implementace služby Azure AD JOIN](azureadjoin-plan.md)
* [Plánování vaší hybridní implementace služby Azure AD JOIN](hybrid-azuread-join-plan.md)
* [Správa identit zařízení](device-management-azure-portal.md)