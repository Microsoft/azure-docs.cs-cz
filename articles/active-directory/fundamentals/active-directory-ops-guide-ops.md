---
title: Odkaz na obecné provozní příručky služby Azure Active Directory
description: Tato referenční příručka k operacím popisuje kontroly a akce, které byste měli provést k zabezpečení obecných operací.
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
ms.openlocfilehash: d039373d3e70076149da2b970a234b59d7aa661a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422943"
---
# <a name="azure-active-directory-general-operations-guide-reference"></a>Odkaz na obecné provozní příručky služby Azure Active Directory

Tato část [referenční příručky pro operace Azure AD](active-directory-ops-guide-intro.md) popisuje kontroly a akce, které byste měli provést k optimalizaci obecných operací služby Azure Active Directory (Azure AD).

> [!NOTE]
> Tato doporučení jsou aktuální k datu publikování, ale mohou se v průběhu času měnit. Organizace by měly průběžně vyhodnocovat své provozní postupy, jak se produkty a služby společnosti Microsoft v průběhu času vyvíjejí.

## <a name="key-operational-processes"></a>Klíčové provozní procesy

### <a name="assign-owners-to-key-tasks"></a>Přiřazení vlastníků ke klíčovým úkolům

Správa služby Azure Active Directory vyžaduje průběžné provádění klíčových provozních úkolů a procesů, které nemusí být součástí projektu zavádění. Je stále důležité nastavit tyto úkoly pro optimalizaci prostředí. Mezi klíčové úkoly a jejich doporučené vlastníky patří:

| Úkol | Vlastník |
| :- | :- |
| Vylepšování skóre zabezpečení identity | Operační tým InfoSec |
| Udržovat servery Azure AD Connect | Operační tým IAM |
| Pravidelné provádění a třídění IdFix Reports | Operační tým IAM |
| Třídění výstrah y stavu Azure AD Connect pro synchronizaci a službu AD FS | Operační tým IAM |
| Pokud nepoužíváte Azure AD Connect Health, pak zákazník má ekvivalentní proces a nástroje pro monitorování vlastní infrastruktury | Operační tým IAM |
| Pokud nepoužíváte službu AD FS, má zákazník ekvivalentní proces a nástroje pro sledování vlastní infrastruktury | Operační tým IAM |
| Monitorování hybridních protokolů: Konektory proxy aplikací Azure AD | Operační tým IAM |
| Monitorování hybridních protokolů: Agenti passthrough authentication | Operační tým IAM |
| Monitorovat hybridní protokoly: Služba zpětného zápisu hesla | Operační tým IAM |
| Monitorování hybridních protokolů: Místní brána ochrany heslem | Operační tým IAM |
| Monitorování hybridních protokolů: Rozšíření serveru NPS Azure MFA (pokud je k dispozici) | Operační tým IAM |

Při kontrole seznamu můžete zjistit, že budete muset buď přiřadit vlastníka pro úkoly, které chybí vlastník, nebo upravit vlastnictví úkolů s vlastníky, kteří nejsou v souladu s výše uvedenými doporučeními.

#### <a name="owners-recommended-reading"></a>Majitelé doporučili čtení

- [Přiřazení rolí správce v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Zásady správného řízení v Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="hybrid-management"></a>Hybridní správa

### <a name="recent-versions-of-on-premises-components"></a>Nejnovější verze místních součástí

Nejaktuálnější verze místních součástí poskytují zákazníkovi všechny nejnovější aktualizace zabezpečení, vylepšení výkonu a funkce, které by mohly pomoci dále zjednodušit prostředí. Většina součástí má automatické nastavení upgradu, které bude automatizovat proces upgradu.

Mezi tyto součásti patří:

- Azure AD Connect
- Konektory proxy aplikací Azure AD
- Předávací agenti Azure AD
- Agenti stavu Azure AD Connect

Pokud nebyla vytvořena, měli byste definovat proces upgradu těchto součástí a spoléhat se na funkci automatického upgradu, kdykoli je to možné. Pokud najdete komponenty, které jsou šest nebo více měsíců pozadu, měli byste upgradovat co nejdříve.

#### <a name="hybrid-management-recommended-reading"></a>Doporučená hybridní správa

- [Azure AD Connect: Automatický upgrade](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-install-automatic-upgrade)
- [Principy konektorů proxy aplikací Azure AD | Automatické aktualizace](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#automatic-updates)

### <a name="azure-ad-connect-health-alert-baseline"></a>Výchozí směrná hodnota výstrahy azure a připojení připojit

Organizace by měly nasazovat [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#what-is-azure-ad-connect-health) pro monitorování a vykazování Azure AD Connect a AD FS. Azure AD Connect a AD FS jsou důležité součásti, které můžou přerušit správu životního cyklu a ověřování a tím vést k výpadkům. Azure AD Connect Health pomáhá monitorovat a získávat přehledo v místní infrastruktuře identit a tím zajistit spolehlivost vašeho prostředí.

![Architektura Azure AD Connect Heath](./media/active-directory-ops-guide/active-directory-ops-img16.png)

Při sledování stavu vašeho prostředí je nutné okamžitě řešit všechny výstrahy vysoké závažnosti, následované výstrahami nižší závažnosti.

#### <a name="azure-ad-connect-health-recommended-reading"></a>Azure AD Connect Health doporučené čtení

- [Instalace agenta stavu Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

### <a name="on-premises-agents-logs"></a>Protokoly místních agentů

Některé služby správy identit a přístupu vyžadují místní agenty k povolení hybridních scénářů. Mezi příklady patří resetování hesla, předávací ověřování (PTA), proxy aplikace Azure AD a rozšíření Azure MFA NPS. Je klíčové, aby provozní tým směrný plán a sledovat stav těchto součástí archivací a analýzou protokolů agenta součásti pomocí řešení, jako je například System Center Operations Manager nebo SIEM. Stejně tak je důležité, aby váš operační tým Infosec nebo oddělení technické podpory pochopili, jak řešit vzorce chyb.

#### <a name="on-premises-agents-logs-recommended-reading"></a>Místní agenti protokoly doporučené čtení

- [Řešení potíží s proxy aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-troubleshoot)
- [Samoobslužné řešení potíží s resetováním hesla – Služba Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#password-writeback-event-log-error-codes)
- [Principy konektorů proxy aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)
- [Azure AD Connect: Poradce při potížích s předávacím ověřováním](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-pass-through-authentication#collecting-pass-through-authentication-agent-logs)
- [Poradce při potížích s kódy chyb pro rozšíření Azure MFA NPS](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-errors)

### <a name="on-premises-agents-management"></a>Správa místních zástupců

Přijetí osvědčených postupů může pomoci optimální mu provoz místních agentů. Zvažte následující doporučené postupy:

- Více konektorů proxy aplikace Azure AD na skupinu konektorů se doporučuje poskytovat bezproblémové vyrovnávání zatížení a vysokou dostupnost tím, že zabrání jediné body selhání při přístupu k proxy aplikací. Pokud máte v současné době pouze jeden konektor ve skupině konektorů, který zpracovává aplikace v produkčním prostředí, měli byste nasadit alespoň dva konektory pro redundanci.
- Vytvoření a použití skupiny konektoru proxy aplikace pro účely ladění může být užitečné pro řešení potíží a při zapisování nových místních aplikací. Doporučujeme také nainstalovat síťové nástroje, jako je analyzátor zpráv a šumař v počítačích konektorů.
- Více předávacích agentů ověřování se doporučuje zajistit bezproblémové vyrovnávání zatížení a vysokou dostupnost tím, že zabrání jeden bod selhání během toku ověřování. Nezapomeňte nasadit alespoň dva předávací agenty ověřování pro redundanci.

#### <a name="on-premises-agents-management-recommended-reading"></a>Místní správa agentů doporučuje čtení

- [Principy konektorů proxy aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors)
- [Předávací ověřování Azure AD – rychlý start](../hybrid/how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)

## <a name="management-at-scale"></a>Řízení ve velkém měřítku

### <a name="identity-secure-score"></a>Identity Secure Score

[Skóre zabezpečení identity](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score) poskytuje kvantifikovatelné měřítko stavu zabezpečení vaší organizace. Je klíčem k neustálému přezkoumávání a řešení hlášených zjištění a usilovat o to, aby bylo možné získat co nejvyšší skóre. Toto skóre vám umožní:

- Objektivně změřit stav zabezpečení vaší identity
- Naplánovat vylepšení zabezpečení identity
- Posoudit úspěšnost těchto vylepšení

![Bezpečnostní skóre](./media/active-directory-ops-guide/active-directory-ops-img17.png)

Pokud vaše organizace aktuálně nemá žádný program pro sledování změn v skóre zabezpečení identity, doporučujeme implementovat plán a přiřadit vlastníkům ke sledování a podpoře akcí zlepšení. Organizace by měly co nejdříve napravit opatření ke zlepšení s dopadem na skóre vyšším než 30.

### <a name="notifications"></a>Oznámení

Společnost Microsoft odesílá správcům e-mailovou komunikaci, aby informovala o různých změnách služby, potřebných aktualizacích konfigurace a chybách, které vyžadují zásah správce. Je důležité, aby zákazníci nastavili e-mailové adresy oznámení tak, aby oznámení byla odeslána správným členům týmu, kteří mohou potvrdit všechna oznámení a jednat podle jich. Doporučujeme přidat více příjemců do [Centra zpráv Office 365](https://docs.microsoft.com/office365/admin/manage/message-center) a požádat o odeslání oznámení (včetně oznámení azure ad připojení stavu) do distribučního seznamu nebo sdílené poštovní schránky. Pokud máte jenom jeden globální účet správce s e-mailovou adresou, nezapomeňte nakonfigurovat alespoň dva účty s podporou e-mailu.

Existuje dvě adresy "Od", které používá <o365mc@email2.microsoft.com>Azure AD: , který odesílá oznámení Centra zpráv Office 365; a <azure-noreply@microsoft.com>, který zasílá oznámení týkající se:

- [Recenze přístupu k Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)
- [Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-operations#enable-email-notifications)
- [Azure AD Identity Protection](/azure/active-directory/identity-protection/howto-identity-protection-configure-notifications)
- [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)
- [Oznámení o certifikátu s platností certifikátu podnikové aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on#add-email-notification-addresses-for-certificate-expiration)
- Oznámení služby zřizování podnikových aplikací

V následující tabulce se dozvíte, jaký typ oznámení jsou odesílána a kde je zkontrolovat:

| Zdroj oznámení | Co je odesláno | Kde zkontrolovat |
|:-|:-|:-|
| Technický kontakt | Chyby synchronizace | Portál Azure – okno vlastností |
| Centrum zpráv Office 365 | Oznámení o incidentech a degradaci služeb identity a back-endových služeb O365 | Portál Office |
| Týdenní digest ochrany identity | Přehled ochrany identity | Okno Azure AD Identity Protection |
| Azure AD Connect Health | Upozornění | Portál Azure – okno Azure AD Connect Health |
| Oznámení podnikových aplikací | Oznámení o vypršení platnosti certifikátů a chyby zřizování | Portál Azure – okno podnikových aplikací (každá aplikace má vlastní nastavení e-mailové adresy) |

#### <a name="notifications-recommended-reading"></a>Oznámení doporučená čtení

- [Změna adresy, technického kontaktu a dalších informací vaší organizace – Office 365](https://docs.microsoft.com/office365/admin/manage/change-address-contact-and-more)

## <a name="operational-surface-area"></a>Provozní plocha

### <a name="ad-fs-lockdown"></a>Uzamčení ad FS

Organizace, které konfigurují aplikace pro ověřování přímo do Azure AD těžit z [Azure AD inteligentní uzamčení](https://docs.microsoft.com/azure/active-directory/active-directory-secure-passwords). Pokud používáte službu AD FS v systému Windows Server 2012 R2, implementujte [ochranu uzamčení extranet](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)služby AD FS . Pokud používáte službu AD FS na Windows Serveru 2016 nebo novějším, implementujte [inteligentní uzamčení extranetu](https://support.microsoft.com/help/4096478/extranet-smart-lockout-feature-in-windows-server-2016). Minimálně doporučujeme povolit uzamčení extranetu tak, aby obsahovalo riziko útoků hrubou silou proti místnímu službě Active Directory. Pokud však máte službu AD FS v systému Windows 2016 nebo vyšší, měli byste také povolit inteligentní uzamčení extranetu, které vám pomohou zmírnit útoky [stříkacím systémem hesel.](https://www.microsoft.com/microsoft-365/blog/2018/03/05/azure-ad-and-adfs-best-practices-defending-against-password-spray-attacks/)

Pokud služba AD FS se používá jenom pro federaci Azure AD, existují některé koncové body, které lze vypnout, aby se minimalizovala oblast povrchu útoku. Například pokud ad FS se používá pouze pro Azure AD, měli byste zakázat WS-Trust koncové body než koncové body povolené pro **usernamemixed** a **windowstransport**.

### <a name="access-to-machines-with-on-premises-identity-components"></a>Přístup k počítačům s místními součástmi identity

Organizace by měly uzamknout přístup k počítačům s místními hybridními součástmi stejným způsobem jako místní doména. Například operátor zálohování nebo správce technologie Hyper-V by se neměli moci přihlásit k serveru Azure AD Connect Server a změnit pravidla.

Model úrovně správy služby Active Directory byl navržen tak, aby chránil systémy identit pomocí sady vyrovnávacích zón mezi úplným řízením prostředí (úroveň 0) a vysoce rizikovými prostředky pracovní stanice, které útočníci často ohrožují. ![Diagram znázorňující tři vrstvy modelu vrstvy](./media/active-directory-ops-guide/active-directory-ops-img18.png)

[Model úrovně](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material) se skládá ze tří úrovní a zahrnuje pouze účty pro správu, nikoli standardní uživatelské účty.

- **Úroveň 0** – přímé řízení podnikových identit v prostředí. Vrstva 0 zahrnuje účty, skupiny a další prostředky, které mají přímou nebo nepřímou správní kontrolu nad doménovou strukturou služby Active Directory, doménami nebo řadiči domén a všemi prostředky v nich. Citlivost všech prostředků ve vrstvě 0 je rovnocenná, protože se všechny účinně navzájem kontrolují.
- **Tier 1** - Řízení podnikových serverů a aplikací. Prostředky vrstvy 1 zahrnují serverové operační systémy, cloudové služby a podnikové aplikace. Účty správců vrstvy 1 mají správní kontrolu nad významnou částí podnikové hodnoty hostované na těchto prostředcích. Jako běžný příklad role je možné uvést správce serveru, kteří udržují tyto operační systémy, které můžou ovlivnit všechny podnikové služby.
- **Úroveň 2** - Řízení uživatelských pracovních stanic a zařízení. Účty správců vrstvy 2 mají správní kontrolu nad významnou částí podnikové hodnoty hostované na těchto prostředcích. Jako příklad můžeme uvést správce technické podpory a podpory pro počítače, protože ty můžou mít vliv na integritu téměř jakýchkoli uživatelských dat.

Uzamkněte přístup k místním součástem identit, jako je Azure AD Connect, AD FS a sql služby stejným způsobem jako u řadičů domény.

## <a name="summary"></a>Souhrn

Existuje sedm aspektů zabezpečené identity infrastruktury. Tento seznam vám pomůže najít akce, které byste měli provést k optimalizaci operací pro Azure Active Directory (Azure AD).

- Přiřaďte vlastníky ke klíčovým úkolům.
- Automatizujte proces upgradu pro místní hybridní součásti.
- Nasazení služby Azure AD Connect Health pro monitorování a vytváření sestav azure a služby Azure AD Connect a služby AD FS.
- Sledujte stav místních hybridních komponent archivací a analýzou protokolů agentů komponent pomocí nástroje System Center Operations Manager nebo řešení SIEM.
- Implementujte vylepšení zabezpečení měřením stavu zabezpečení pomocí skóre identity Secure Score.
- Uzamkněte ad FS.
- Uzamkněte přístup k počítačům s místními součástmi identit.

## <a name="next-steps"></a>Další kroky

Podrobnosti o implementaci všech funkcí, které jste nenasadili, najdete v [plánech nasazení Azure AD.](active-directory-deployment-plans.md)
