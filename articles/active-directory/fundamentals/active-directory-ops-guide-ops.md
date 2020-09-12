---
title: Přehled Azure Active Directory obecné provozní příručky
description: Referenční příručka operací popisuje kontroly a akce, které byste měli provést při zabezpečení obecných operací.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: edb35986fcfc0e8855fa712b519ee7a8ca2f0ed8
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89321952"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Přehled Azure Active Directory obecné provozní příručky

Tato část [Referenční příručka operací Azure AD](active-directory-ops-guide-intro.md) popisuje kontroly a akce, které byste měli provést k optimalizaci obecných operací Azure Active Directory (Azure AD).

> [!NOTE]
> Tato doporučení jsou aktuální k datu publikování, ale mohou se v průběhu času měnit. Organizace by měly průběžně vyhodnocovat provozní postupy, protože produkty a služby Microsoftu se v průběhu času vyvíjejí.

## <a name="key-operational-processes"></a>Klíčové provozní procesy

### <a name="assign-owners-to-key-tasks"></a>Přiřazení vlastníků ke klíčovým úlohám

Správa Azure Active Directory vyžaduje průběžné provádění klíčových provozních úloh a procesů, které nemusí být součástí projektu zavedení. Stále je důležité, abyste nastavili tyto úlohy pro optimalizaci prostředí. Mezi klíčové úlohy a jejich Doporučené vlastníky patří:

| Úloha | Vlastník |
| :- | :- |
| Vylepšení hodnocení bezpečného skóre identity | Provozní tým InfoSec |
| Údržba serverů Azure AD Connect | Provozní tým IAM |
| Pravidelné spouštění a třídění sestav IdFix | Provozní tým IAM |
| Třídění Výstrahy služby Azure AD Connect Health pro synchronizaci a AD FS | Provozní tým IAM |
| Pokud nepoužíváte Azure AD Connect Health, má zákazník ekvivalentní proces a nástroje pro monitorování vlastní infrastruktury. | Provozní tým IAM |
| Pokud nepoužíváte AD FS, má zákazník ekvivalentní proces a nástroje pro monitorování vlastní infrastruktury. | Provozní tým IAM |
| Monitorování hybridních protokolů: konektory Aplikace Azure AD proxy serveru | Provozní tým IAM |
| Monitorování hybridních protokolů: předávací agenti ověřování | Provozní tým IAM |
| Monitorování hybridních protokolů: služba zpětného zápisu hesla | Provozní tým IAM |
| Monitorování hybridních protokolů: místní brána ochrany heslem | Provozní tým IAM |
| Monitorování hybridních protokolů: rozšíření Azure MFA NPS (Pokud je k dispozici) | Provozní tým IAM |

Při revizi seznamu se můžete setkat s tím, že budete muset buď přiřadit vlastníka pro úlohy, u kterých chybí vlastník, nebo upravit vlastnictví pro úlohy s vlastníky, které nejsou zarovnané na výše uvedená doporučení.

#### <a name="owners-recommended-reading"></a>Vlastníci doporučené čtení

- [Přiřazení rolí správce v Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md)
- [Zásady správného řízení v Azure](../../governance/index.yml)

## <a name="hybrid-management"></a>Hybridní Správa

### <a name="recent-versions-of-on-premises-components"></a>Poslední verze místních komponent

Nejaktuálnější verze místních komponent nabízí zákazníkům nejnovější aktualizace zabezpečení, vylepšení výkonu a funkce, které by vám mohly přispět k dalšímu zjednodušení prostředí. Většina komponent má nastavení automatického upgradu, které automatizuje proces upgradu.

Mezi tyto komponenty patří:

- Azure AD Connect
- Konektory Azure Proxy aplikací služby AD
- Agenti předávacího ověřování Azure AD
- Agenti Azure AD Connect Health

Pokud není jedna vytvořená, měli byste definovat proces upgradu těchto komponent a spoléhat se na funkci automatického upgradu, kdykoli je to možné. Pokud najdete komponenty, které jsou šest nebo více měsíců, měli byste upgradovat co nejdříve.

#### <a name="hybrid-management-recommended-reading"></a>Doporučené čtení z hybridní správy

- [Azure AD Connect: Automatický upgrade](../hybrid/how-to-connect-install-automatic-upgrade.md)
- [Vysvětlení konektorů služby Azure Proxy aplikací služby AD | Automatické aktualizace](../manage-apps/application-proxy-connectors.md#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Základní Azure AD Connect Health výstrahy

Organizace by měly nasadit [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#what-is-azure-ad-connect-health) pro monitorování a vytváření sestav Azure AD Connect a AD FS. Azure AD Connect a AD FS jsou důležité součásti, které mohou přerušit správu a ověřování životního cyklu, a tedy vést k výpadkům. Azure AD Connect Health pomáhá monitorovat a získávat přehledy o místní infrastruktuře identity, takže zajišťuje spolehlivost vašeho prostředí.

![Architektura Azure AD Connect stavu](./media/active-directory-ops-guide/active-directory-ops-img16.png)

Když monitorete stav svého prostředí, musíte okamžitě vyřešit jakékoli výstrahy s vysokou závažností a potom upozornění s nižší závažností.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD Connect Health doporučené čtení

- [Instalace agenta Azure AD Connect Health](../hybrid/how-to-connect-health-agent-install.md)

### <a name="on-premises-agents-logs"></a>Protokoly místních agentů

Některá služba pro správu identit a přístupu vyžaduje, aby místní agenti povolili hybridní scénáře. Mezi příklady patří resetování hesla, předávací ověřování (PTA), Azure Proxy aplikací služby AD a rozšíření Azure MFA NPS. Jedná se o klíč, který Operations prochází a monitoruje stav těchto komponent tím, že archivuje a analyzuje protokoly agenta komponent pomocí řešení, jako je System Center Operations Manager nebo SIEM. Je stejně důležité, aby provozní tým INFOSEC nebo oddělení technické podpory pochopil, jak řešit problémy se vzory chyb.

#### <a name="on-premises-agents-logs-recommended-reading"></a>Doporučené čtení v protokolech místních agentů

- [Řešení potíží s proxy aplikací](../manage-apps/application-proxy-troubleshoot.md)
- [Řešení potíží s samoobslužným resetem hesla – Azure Active Directory](../authentication/active-directory-passwords-troubleshoot.md#password-writeback-event-log-error-codes)
- [Vysvětlení konektorů Azure Proxy aplikací služby AD](../manage-apps/application-proxy-connectors.md)
- [Azure AD Connect: řešení potíží s předávacím ověřováním](../hybrid/tshoot-connect-pass-through-authentication.md#collecting-pass-through-authentication-agent-logs)
- [Řešení potíží s kódy chyb pro rozšíření Azure MFA NPS](../authentication/howto-mfa-nps-extension-errors.md)

### <a name="on-premises-agents-management"></a>Správa místních agentů

Zavedení osvědčených postupů může přispět k optimálnímu fungování místních agentů. Vezměte v úvahu následující osvědčené postupy:

- Pro zajištění bezproblémového vyrovnávání zatížení a vysoké dostupnosti se doporučuje několik konektorů proxy aplikací Azure AD na skupinu konektorů, a to tak, že se při přístupu k proxy aplikacím vyhnete jediným bodům selhání. Pokud máte ve skupině konektorů, která zpracovává aplikace v produkčním prostředí, jenom jeden konektor, měli byste pro redundanci nasadit aspoň dva konektory.
- Vytvoření a použití skupiny konektorů proxy aplikací pro účely ladění může být užitečné pro scénáře řešení potíží a při připojování nových místních aplikací. Doporučujeme také do počítačů konektorů nainstalovat nástroje sítě, jako je například analyzátor zpráv a Fiddler.
- Pro zajištění bezproblémového vyrovnávání zatížení a vysoké dostupnosti se doporučuje více agentů předávacího ověřování, což zaloučí jediný bod selhání během procesu ověřování. Nezapomeňte nasadit alespoň dva agenty předávacího ověřování pro zajištění redundance.

#### <a name="on-premises-agents-management-recommended-reading"></a>Doporučené čtení pro správu místních agentů

- [Vysvětlení konektorů Azure Proxy aplikací služby AD](../manage-apps/application-proxy-connectors.md)
- [Předávací ověřování Azure AD – rychlý Start](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>Škálovatelná správa

### <a name="identity-secure-score"></a>Identity Secure Score

[Skóre Secure identity](./identity-secure-score.md) poskytuje měřitelnou míru stav zabezpečení vaší organizace. Je klíč k nepřetržité kontrole a zjištění adres hlášených a snaží se dosáhnout nejvyššího možného skóre. Toto skóre vám umožní:

- Objektivně změřit stav zabezpečení vaší identity
- Naplánovat vylepšení zabezpečení identity
- Posoudit úspěšnost těchto vylepšení

![Bezpečnostní skóre](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Pokud vaše organizace aktuálně nemá žádný program k monitorování změn v zabezpečeném skóre identity, doporučujeme implementovat plán a přiřadit vlastníky k monitorování a řízení akcí zlepšování. Organizace by měly opravit akce vylepšení s dopadem skóre vyšším než 30, jakmile to bude možné.

### <a name="notifications"></a>Oznámení

Společnost Microsoft pošle e-mailem komunikaci správcům, aby upozornila na různé změny ve službě, potřebné aktualizace konfigurace a chyby, které vyžadují zásah správce. Je důležité, aby zákazníci nastavili e-mailové adresy pro oznámení, aby se oznámení odesílala řádným členům týmu, kteří můžou potvrdit a reagovat na všechna oznámení. Doporučujeme přidat do [centra zpráv Office 365](/office365/admin/manage/message-center) několik příjemců a požádat o oznámení (včetně oznámení o Azure AD Connect Health) do distribučního seznamu nebo do sdílené poštovní schránky. Pokud máte jenom jeden globální účet správce s e-mailovou adresou, nezapomeňte nakonfigurovat aspoň dva účty s podporou e-mailu.

Existují dva adresy "z", které používá služba Azure AD: <o365mc@email2.microsoft.com> , která odesílá oznámení centra zpráv Office 365 a <azure-noreply@microsoft.com> odesílá oznámení související s:

- [Kontroly přístupu Azure AD](../governance/access-reviews-overview.md)
- [Azure AD Connect Health](../hybrid/how-to-connect-health-operations.md#enable-email-notifications)
- [Azure AD Identity Protection](../identity-protection/howto-identity-protection-configure-notifications.md)
- [Azure AD Privileged Identity Management](../privileged-identity-management/pim-email-notifications.md)
- [Oznámení o certifikátu vypršení platnosti podnikové aplikace](../manage-apps/manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)
- Oznámení služby zřizování podnikových aplikací

Informace o typu odesílaných oznámení a jejich kontrole najdete v následující tabulce:

| Zdroj oznámení | Co se posílá | Kde kontrolovat |
|:-|:-|:-|
| Technický kontakt | Chyby synchronizace | Azure Portal – okno vlastností |
| Centrum zpráv Office 365 | Oznámení o službách identity a back-endové služby pro incidenty a snížení výkonu | Portál Office |
| Týdenní Digest Identity Protection | Digest Identity Protection | Okno Azure AD Identity Protection |
| Azure AD Connect Health | Oznámení výstrah | Okno Azure Portal-Azure AD Connect Health |
| Oznámení podnikových aplikací | Oznámení o chybách a zřizování certifikátů, které brzy vyprší | Okno Azure Portal – podniková aplikace (každá aplikace má vlastní nastavení e-mailové adresy) |

#### <a name="notifications-recommended-reading"></a>Doporučené čtení oznámení

- [Změna adresy vaší organizace, technického kontaktu a dalších kanceláří – Office 365](/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>Oblast provozní plocha

### <a name="ad-fs-lockdown"></a>AD FS uzamčení

Organizace, které konfigurují aplikace pro ověřování přímo ve zvýhodnění Azure AD z [inteligentního uzamčení Azure AD](../authentication/concept-sspr-howitworks.md). Pokud používáte AD FS ve Windows Serveru 2012 R2, implementujte AD FS [extranetovou ochranu](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection). Pokud používáte AD FS na Windows serveru 2016 nebo novějším, implementujte [extranetové inteligentní uzamčení](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). Doporučujeme povolit uzamčení extranetu, aby obsahovalo riziko útoků hrubou silou na místní službu Active Directory. Pokud ale máte AD FS ve Windows 2016 nebo novějším, měli byste taky povolit extranetové inteligentní uzamčení, které vám pomůže zmírnit útoky proti [rozstřiku hesla](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/) .

Pokud se AD FS používá jenom pro federaci Azure AD, existuje několik koncových bodů, které je možné vypnout pro minimalizaci oblasti útoku. Pokud se například AD FS používá jenom pro Azure AD, měli byste zakázat koncové body WS-Trust jiné než koncové body, které jsou povolené pro **usernamemixed** a **windowstransport**.

### <a name="access-to-machines-with-on-premises-identity-components"></a>Přístup k počítačům s místními komponentami identity

Organizace by měly uzamknout přístup k počítačům s místními hybridními komponentami stejným způsobem jako vaše místní doména. Například operátor zálohování nebo Správce technologie Hyper-V by se nemůže přihlásit k serveru Azure AD Connect, aby změnil pravidla.

Model vrstvy správy služby Active Directory byl navržený tak, aby chránil systémy identit pomocí sady zón vyrovnávací paměti mezi úplným řízením prostředí (vrstvy 0) a vysoce rizikovými prostředky pracovní stanice, které útočníci často ohrožují. ![Diagram znázorňující tři vrstvy modelu vrstvy](./media/active-directory-ops-guide/active-directory-ops-img18.png)

[Model vrstev](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) se skládá ze tří úrovní a zahrnuje pouze účty pro správu, nikoli standardní uživatelské účty.

- **Vrstva 0**   – Přímé řízení podnikových identit v prostředí. Vrstva 0 zahrnuje účty, skupiny a další prostředky, které mají přímou nebo nepřímou správní kontrolu nad doménovou strukturou služby Active Directory, doménami nebo řadiči domén a všemi prostředky v nich. Citlivost všech prostředků ve vrstvě 0 je rovnocenná, protože se všechny účinně navzájem kontrolují.
- **Vrstva 1**   Řízení podnikových serverů a aplikací. Prostředky vrstvy 1 zahrnují serverové operační systémy, cloudové služby a podnikové aplikace. Účty správců vrstvy 1 mají správní kontrolu nad významnou částí podnikové hodnoty hostované na těchto prostředcích. Jako běžný příklad role je možné uvést správce serveru, kteří udržují tyto operační systémy, které můžou ovlivnit všechny podnikové služby.
- **Úroveň 2**   – Řízení pracovních stanic a zařízení uživatele. Účty správců vrstvy 2 mají správní kontrolu nad významnou částí podnikové hodnoty hostované na těchto prostředcích. Jako příklad můžeme uvést správce technické podpory a podpory pro počítače, protože ty můžou mít vliv na integritu téměř jakýchkoli uživatelských dat.

Přístup k místním komponentám identity, jako jsou Azure AD Connect, AD FS a SQL Services, můžete uzamknout stejným způsobem jako u řadičů domény.

## <a name="summary"></a>Shrnutí

Zabezpečená infrastruktura identity má sedm aspektů. Tento seznam vám pomůže najít akce, které byste měli provést k optimalizaci operací pro Azure Active Directory (Azure AD).

- Přiřaďte vlastníky ke klíčovým úlohám.
- Automatizujte proces upgradu pro místní hybridní součásti.
- Nasaďte Azure AD Connect Health pro monitorování a vytváření sestav Azure AD Connect a AD FS.
- Pomocí System Center Operations Manager nebo řešení SIEM Monitorujte stav místních hybridních komponent pomocí archivace a analýzy protokolů agenta komponent.
- Naimplementujte vylepšení zabezpečení tím, že vyměříte stav zabezpečení s hodnocením identity Secure.
- Uzamknout AD FS.
- Odblokujte přístup k počítačům s místními komponentami identity.

## <a name="next-steps"></a>Další kroky

Podrobnosti o implementaci všech nenasazených možností najdete v tématu [plány nasazení služby Azure AD](active-directory-deployment-plans.md) .