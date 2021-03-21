---
title: Azure AD Domain Services pro poskytovatele cloudových řešení | Microsoft Docs
description: Naučte se, jak povolit a spravovat Azure Active Directory Domain Services spravované domény pro poskytovatele cloudových řešení Azure.
services: active-directory-ds
author: justinha
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: justinha
ms.openlocfilehash: d8edafff9b6534e5f1ce1c4581595ee187dfd432
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619895"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Azure Active Directory Domain Services nasazení a správy pro poskytovatele cloudových řešení Azure

Poskytovatelé cloudových řešení Azure (CSP) je program pro partnery Microsoftu a poskytuje licenční kanál pro různé cloudové služby Microsoftu. CSP Azure umožňuje partnerům spravovat prodej, vlastnit fakturační vztah, poskytovat technickou a fakturační podporu a být jediným kontaktním bodem zákazníka. Kromě toho poskytuje poskytovatel cloudových služeb Azure kompletní sadu nástrojů, včetně samoobslužného portálu a doprovodného rozhraní API. Tyto nástroje umožňují partnerům CSP snadno zřizovat a spravovat prostředky Azure a poskytovat zákazníkům a jejich předplatným fakturaci.

[Portál partnerského centra](/partner-center/azure-plan-lp) je vstupním bodem pro všechny partnery CSP Azure a poskytuje bohatou možnost správy zákazníků, automatizované zpracování a další funkce. Partneři CSP Azure mohou využívat možnosti partnerského centra pomocí webového uživatelského rozhraní nebo pomocí prostředí PowerShell a různých volání rozhraní API.

Následující diagram znázorňuje, jak model CSP funguje na vysoké úrovni. Společnost Contoso má klienta Azure Active Directory (Azure AD). Mají partnerství se zprostředkovatelem CSP, který nasazuje a spravuje prostředky ve svém předplatném Azure CSP. Contoso může mít také běžná (přímá) předplatná Azure, která se účtují přímo do společnosti Contoso.

![Přehled modelu CSP](./media/csp/csp_model_overview.png)

Tenant partnera CSP má tři speciální skupiny agentů – agenti pro *správu* , agenty *helpdesku* a *prodejní* agenty.

Skupina agentů pro *správu* je přiřazená k roli správce klienta v TENANTOVI Azure AD společnosti Contoso. V důsledku toho uživatel patřící do skupiny agenti správců CSP má oprávnění správce tenanta v tenantovi Azure AD společnosti Contoso.

Když partner CSP zřídí předplatné služby Azure CSP pro společnost Contoso, jejich skupina agentů pro správu je přiřazena k roli vlastníka daného předplatného. V důsledku toho mají agenti správce CSP požadovaná oprávnění ke zřízení prostředků Azure, jako jsou virtuální počítače, virtuální sítě a Azure AD Domain Services jménem společnosti Contoso.

Další informace najdete v tématu [Přehled CSP Azure](/partner-center/azure-plan-lp) .

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Výhody používání Azure služba AD DS v předplatném služby Azure CSP

Azure Active Directory Domain Services (Azure služba AD DS) poskytuje spravované doménové služby, jako je připojení k doméně, zásady skupiny, LDAP, ověřování Kerberos/NTLM, které jsou plně kompatibilní se službou Windows Server Active Directory Domain Services. V průběhu desetiletí bylo mnoho aplikací postavených na spolupráci se službou AD pomocí těchto schopností. Mnoho nezávislých výrobců softwaru (ISV) má v místním prostředí své zákazníky sestavené a nasazené aplikace. Tyto aplikace jsou těžky podporované, protože často potřebujete přístup k různým prostředím, kde jsou aplikace nasazeny. S předplatnými Azure CSP máte jednodušší alternativu s měřítkem a flexibilitou Azure.

Azure služba AD DS podporuje předplatná Azure CSP. Svou aplikaci můžete nasadit v předplatném CSP Azure, které je vázané na tenanta Azure AD vašeho zákazníka. V důsledku toho mohou zaměstnanci (pracovníci podpory) spravovat, spravovat a obsluhovat virtuální počítače, na kterých je vaše aplikace nasazena, pomocí podnikových přihlašovacích údajů vaší organizace.

V tenantovi Azure AD zákazníka můžete taky nasadit spravovanou doménu Azure služba AD DS. Vaše aplikace je pak připojená k spravované doméně zákazníka. Funkce v rámci aplikace, které spoléhají na rozhraní Kerberos/NTLM, LDAP nebo [rozhraní API System. DirectoryServices](/dotnet/api/system.directoryservices) , fungují bez problémů s doménou zákazníka. Koncoví zákazníci využívají výhod vaší aplikace jako služby, aniž byste museli se starat o údržbu infrastruktury, ve které je aplikace nasazená.

Veškerá fakturace za prostředky Azure, které spotřebováváte v tomto předplatném, včetně Azure služba AD DS, se účtují zpátky. V rámci vztahu se zákazníkem můžete udržovat plnou kontrolu nad vztahem k prodeji, fakturaci, technické podpoře atd. Díky flexibilitě platformy Azure CSP může malý tým agentů podpory obsluhovat mnoho takových zákazníků, kteří mají nasazené instance aplikace.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>Modely nasazení CSP pro Azure služba AD DS

Existují dva způsoby, jak můžete Azure služba AD DS použít s předplatným CSP Azure. Vyberte si napravo na základě bezpečnostních a jednoduchosti důležitých informací, které vaši zákazníci mají.

### <a name="direct-deployment-model"></a>Model přímého nasazení

V tomto modelu nasazení je Azure služba AD DS povolený ve virtuální síti, která patří do předplatného Azure CSP. Agenti správce pro poskytovatele CSP mají následující oprávnění:

* Oprávnění *globálního správce* v TENANTOVI Azure AD zákazníka.
* Oprávnění *vlastníka předplatného* v předplatném Azure CSP.

![Model přímého nasazení](./media/csp/csp_direct_deployment_model.png)

V tomto modelu nasazení můžou agenti správce zprostředkovatele CSP spravovat identity pro zákazníka. Tito správci můžou provádět úkoly, jako je například zřízení nových uživatelů nebo skupin, nebo přidávání aplikací v rámci tenanta Azure AD zákazníka.

Tento model nasazení může být vhodný pro menší organizace, které nemají vyhrazeného správce identit nebo dávají přednost za to, aby partner CSP spravoval identity jménem uživatele.

### <a name="peered-deployment-model"></a>Model partnerského nasazení

V tomto modelu nasazení je Azure služba AD DS povolený ve virtuální síti, která patří zákazníkovi – přímé předplatné Azure placené zákazníkem. Partner CSP může nasazovat aplikace v rámci virtuální sítě, které patří k předplatnému poskytovatele CSP zákazníka. Virtuální sítě je pak možné propojit pomocí partnerského vztahu virtuálních sítí Azure.

V rámci tohoto nasazení se můžou úlohy nebo aplikace nasazené partnerem CSP v předplatném Azure CSP připojit ke spravované doméně zákazníka zřízené v přímém předplatném Azure zákazníka.

![Model partnerského nasazení](./media/csp/csp_peered_deployment_model.png)

Tento model nasazení poskytuje oddělení oprávnění a umožňuje agentům helpdesku poskytovatele CSP spravovat předplatné Azure a nasazovat a spravovat prostředky v něm. Agenti helpdesku pro poskytovatele CSP ale nemusí mít oprávnění globálního správce k adresáři Azure AD zákazníka. Správci identity zákazníka můžou dál spravovat identity pro svou organizaci.

Tento model nasazení může být vhodný pro situace, kdy nezávislý výrobce softwaru poskytuje hostovanou verzi své místní aplikace, která se taky musí připojit ke službě Azure AD zákazníka.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>Správa Azure služba AD DS v předplatných CSP

Při správě spravované domény v předplatném Azure CSP platí následující důležité informace:

* **Agenti správce CSP můžou zřídit spravovanou doménu pomocí svých přihlašovacích údajů:** Azure služba AD DS podporuje předplatná Azure CSP. Uživatelé patřící do skupiny agenti správce v partnerovi CSP mohou zřídit novou spravovanou doménu.

* Poskytovatelé **CSP můžou vytvořit skripty pro vytváření nových spravovaných domén pro zákazníky pomocí prostředí PowerShell:** Podrobnosti najdete v tématu [Jak povolit Azure služba AD DS s využitím PowerShellu](powershell-create-instance.md) .

* **Agenti správce CSP nemohou provádět úlohy probíhající správy ve spravované doméně pomocí svých přihlašovacích údajů:** Uživatelé správců CSP nemůžou provádět běžné úlohy správy v rámci spravované domény pomocí svých přihlašovacích údajů. Tito uživatelé jsou externí pro tenanta Azure AD zákazníka a jejich přihlašovací údaje nejsou k dispozici v tenantovi Azure AD zákazníka. Azure služba AD DS nemá přístup k hodnotám hash hesla protokolu Kerberos a NTLM pro tyto uživatele, takže uživatele nejde na spravovaných doménách ověřit.

  > [!WARNING]
  > Aby bylo možné provádět úlohy probíhající správy ve spravované doméně, musíte v adresáři zákazníka vytvořit uživatelský účet.
  >
  > K spravované doméně se nemůžete přihlásit pomocí přihlašovacích údajů uživatele správce CSP. Použijte přihlašovací údaje uživatelského účtu patřícího k tenantovi Azure AD zákazníka. Tyto přihlašovací údaje budete potřebovat pro úlohy, jako je připojení virtuálních počítačů ke spravované doméně, správa DNS nebo Správa Zásady skupiny.

* **Uživatelský účet vytvořený pro probíhající správu musí být přidán do skupiny *správci řadiče domény AAD* :** skupina *AAD Domain Administrators* má oprávnění k provádění určitých delegovaných úloh správy ve spravované doméně. Mezi tyto úlohy patří konfigurace DNS, vytváření organizačních jednotek a Správa zásad skupiny.
    
    Aby mohl partner CSP provádět tyto úlohy ve spravované doméně, musí být vytvořený uživatelský účet v rámci tenanta Azure AD zákazníka. Přihlašovací údaje pro tento účet musí být sdíleny s agenty správce partnera CSP. Tento uživatelský účet musí být také přidán do skupiny *Správci AAD DC* , aby bylo možné provádět úlohy konfigurace ve spravované doméně pomocí tohoto uživatelského účtu.

## <a name="next-steps"></a>Další kroky

Začněte tím, [že se zaregistrujete do programu Azure CSP](/partner-center/enrolling-in-the-csp-program). Pak můžete povolit Azure AD Domain Services pomocí [Azure Portal](tutorial-create-instance.md) nebo [Azure PowerShell](powershell-create-instance.md).