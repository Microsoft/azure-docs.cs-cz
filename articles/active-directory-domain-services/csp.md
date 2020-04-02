---
title: Služby domény Azure AD pro poskytovatele cloudových řešení | Dokumenty společnosti Microsoft
description: Zjistěte, jak povolit a spravovat spravované domény služby Azure Active Directory Domain Services pro poskytovatele cloudových řešení Azure
services: active-directory-ds
author: iainfoulds
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: iainfou
ms.openlocfilehash: e7276dcfca6ba033942d62f347ac3a799524cac4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519091"
---
# <a name="azure-active-directory-domain-services-deployment-and-management-for-azure-cloud-solution-providers"></a>Nasazení a správa služby Azure Active Directory Domain Services pro poskytovatele cloudových řešení Azure

Azure Cloud Solution Providers (CSP) je program pro partnery Microsoftu a poskytuje licenční kanál pro různé cloudové služby Microsoftu. Předpokladu CSP Azure umožňuje partnerům spravovat prodej, vlastnit fakturační vztah, poskytovat technickou a fakturační podporu a být jediným kontaktním místem zákazníka. Kromě toho azure csp poskytuje úplnou sadu nástrojů, včetně samoobslužného portálu a doprovodných api. Tyto nástroje umožňují partnerům CSP snadno zřídit a spravovat prostředky Azure a poskytovat fakturaci zákazníkům a jejich předplatným.

[Portál Partnerského centra](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) je vstupním bodem pro všechny partnery Azure CSP a poskytuje bohaté možnosti správy zákazníků, automatizované zpracování a další. Partneři Azure CSP můžou používat možnosti Centra partnerů pomocí webového uživatelského rozhraní nebo pomocí Prostředí PowerShell a různých volání rozhraní API.

Následující diagram znázorňuje, jak model CSP funguje na vysoké úrovni. Tady má Contoso klienta Azure Active Directory (Azure AD). Mají partnerství s csp, který nasazuje a spravuje prostředky v jejich předplatném Azure CSP. Contoso může mít také pravidelná (přímá) předplatná Azure, která se účtují přímo společnosti Contoso.

![Přehled modelu CSP](./media/csp/csp_model_overview.png)

Tenant partnera CSP má tři skupiny zvláštních agentů – *agenty správce,* agenty *helpdesku* a *obchodní* zástupce.

Skupina agentů *správce* je přiřazena k roli správce klienta v tenantu Azure AD společnosti Contoso. V důsledku toho má uživatel patřící do skupiny agentů správce partnera CSP oprávnění správce v tenantovi Azure AD společnosti Contoso.

Když partner CSP zřídí předplatné Azure CSP pro Contoso, jejich skupina agentů správce je přiřazena k roli vlastníka pro toto předplatné. V důsledku toho mají agenti správce partnera CSP požadovaná oprávnění k poskytování prostředků Azure, jako jsou virtuální počítače, virtuální sítě a služby Domény Azure AD jménem společnosti Contoso.

Další informace najdete v přehledu [o azure csp](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-ds-in-an-azure-csp-subscription"></a>Výhody používání Azure AD DS v předplatném Azure CSP

Služba Azure Active Directory Domain Services (Azure AD DS) poskytuje služby spravované domény, jako je připojení k doméně, zásady skupiny, ověřování LDAP, Kerberos/NTLM, které je plně kompatibilní se službou Windows Server Active Directory Domain Services. V průběhu desetiletí bylo vytvořeno mnoho aplikací, které fungují proti službu AD pomocí těchto funkcí. Mnoho nezávislých dodavatelů softwaru (ISV) vytvořilo a nasadilo aplikace v prostorách svých zákazníků. Tyto aplikace jsou těžko podporovat, protože často vyžadují přístup k různým prostředím, kde jsou aplikace nasazeny. S předplatnými Azure CSP máte jednodušší alternativu s škálovatrozsahem a flexibilitou Azure.

Azure AD DS podporuje předplatná Azure CSP. Aplikaci můžete nasadit v předplaceně Azure CSP vázaném na klienta Azure AD vašeho zákazníka. V důsledku toho mohou vaši zaměstnanci (pracovníci podpory) spravovat, spravovat a obsluhovat virtuální počítače, na kterých je vaše aplikace nasazená, pomocí podnikových přihlašovacích údajů vaší organizace.

Můžete také nasadit spravovanou doménu Azure AD DS v tenantovi Azure AD vašeho zákazníka. Aplikace je pak připojena ke spravované doméně zákazníka. Funkce v rámci aplikace, které spoléhají na kerberos / NTLM, LDAP nebo [System.DirectoryServices API](/dotnet/api/system.directoryservices) pracovat bez problémů s doménou zákazníka. Koncoví zákazníci těží z využívání vaší aplikace jako služby, aniž by se museli starat o údržbu infrastruktury, na které je aplikace nasazená.

Všechny fakturace pro prostředky Azure, které spotřebováváte v tomto předplatném, včetně Azure AD DS, se vám účtují zpět. Máte plnou kontrolu nad vztahem se zákazníkem, pokud jde o prodej, fakturaci, technickou podporu atd. Díky flexibilitě platformy Zprostředkovatele copiínů Azure může malý tým agentů podpory obsluhovat mnoho takových zákazníků, kteří mají nasazené instance vaší aplikace.

## <a name="csp-deployment-models-for-azure-ad-ds"></a>Modely nasazení CSP pro Azure AD DS

Existují dva způsoby, jak můžete použít Azure AD DS s předplatným Azure CSP. Vyberte si ten správný na základě aspekty zabezpečení a jednoduchosti vaši zákazníci mají.

### <a name="direct-deployment-model"></a>Model přímého nasazení

V tomto modelu nasazení Azure AD DS je povolená v rámci virtuální sítě, která patří k předplatnému Azure CSP. Agenti správce partnera CSP mají následující oprávnění:

* *Oprávnění globálního správce* v tenantovi Azure AD zákazníka.
* Oprávnění *vlastníka předplatného* v předplatném Azure CSP.

![Model přímého nasazení](./media/csp/csp_direct_deployment_model.png)

V tomto modelu nasazení mohou agenti správce zprostředkovatele CSP spravovat identity pro zákazníka. Tito agenti správy můžete provádět úkoly, jako je zřízení nových uživatelů nebo skupin, nebo přidat aplikace v rámci klienta Azure AD zákazníka.

Tento model nasazení může být vhodný pro menší organizace, které nemají vyhrazeného správce identity nebo dávají přednost partnerovi CSP pro správu identit jejich jménem.

### <a name="peered-deployment-model"></a>Partnerský model nasazení

V tomto modelu nasazení Azure AD DS je povolena v rámci virtuální sítě patřící zákazníkovi – přímé předplatné Azure placené zákazníkem. Partner CSP může nasadit aplikace v rámci virtuální sítě, která patří k předplatnému CSP zákazníka. Virtuální sítě pak můžete připojit pomocí partnerského vztahu virtuální sítě Azure.

Díky tomuto nasazení se úlohy nebo aplikace nasazené partnerem CSP v předplatném Azure CSP můžou připojit ke spravované doméně zákazníka zřízené v přímém předplatném Azure zákazníka.

![Partnerský model nasazení](./media/csp/csp_peered_deployment_model.png)

Tento model nasazení poskytuje oddělení oprávnění a umožňuje agentům technické podpory partnera CSP spravovat předplatné Azure a nasazovat a spravovat prostředky v něm. Agenti technické podpory partnera CSP však nemusí mít oprávnění globálního správce v adresáři Azure AD zákazníka. Správci identit zákazníka mohou nadále spravovat identity pro svou organizaci.

Tento model nasazení může být vhodný pro scénáře, kde nevlastní zákazník poskytuje hostovkou verzi své místní aplikace, která se také potřebuje připojit k Azure AD zákazníka.

## <a name="administer-azure-ad-ds-in-csp-subscriptions"></a>Správa služby Azure AD DS v předplatných CSP

Následující důležité důležité důležité informace platí při správě spravované domény v předplatném Azure CSP:

* **Agenti správce zprostředkovatele csp mohou zřídit spravovanou doménu pomocí svých přihlašovacích údajů:** Azure AD DS podporuje předplatná Azure CSP. Uživatelé, kteří patří do skupiny agentů správce partnera CSP, můžou zřídit novou spravovanou doménu Azure AD DS.

* **Kresličtí kreslicí lékaři mohou vytvářet nové spravované domény pro své zákazníky pomocí prostředí PowerShell:** Podrobnosti [najdete v tématu, jak povolit Azure AD DS pomocí PowerShellu.](powershell-create-instance.md)

* **Agenti správce zprostředkovatele csp nemohou provádět průběžné úlohy správy ve spravované doméně pomocí svých přihlašovacích údajů:** Uživatelé správce csp nemohou provádět rutinní úlohy správy v rámci spravované domény pomocí svých přihlašovacích údajů. Tito uživatelé jsou externí klienta Azure AD zákazníka a jejich přihlašovací údaje nejsou k dispozici v rámci klienta Azure AD zákazníka. Azure AD DS nemá přístup k hodnotám hash hesel protokolu Kerberos a NTLM pro tyto uživatele, takže uživatelé nelze ověřit na spravovaných doménách Azure AD DS.

  > [!WARNING]
  > Chcete-li provádět probíhající úlohy správy ve spravované doméně, je nutné vytvořit uživatelský účet v adresáři zákazníka.
  >
  > Ke spravované doméně se nemůžete přihlásit pomocí přihlašovacích údajů správce CSP. Použijte přihlašovací údaje uživatelského účtu, který patří klientovi Azure AD zákazníka k tomu. Tato pověření potřebujete pro úlohy, jako je připojení virtuálních připojení ke spravované doméně, správa SLUŽBY DNS nebo správa zásad skupiny.

* **Uživatelský účet vytvořený pro průběžnou správu musí být přidán do *skupiny Správci řadiče domény řadiče domény AAD:* ** Skupina *Správci řadiče domény Řadiče domény AAD* má oprávnění k provádění určitých úkolů delegované správy ve spravované doméně. Mezi tyto úkoly patří konfigurace služby DNS, vytváření organizačních jednotek a správa zásad skupiny.
    
    Pro partnera CSP provádět tyto úkoly na spravované doméně, uživatelský účet musí být vytvořen v rámci klienta Azure AD zákazníka. Pověření pro tento účet musí být sdílena s agenty správce partnera CSP. Tento uživatelský účet musí být také přidán do *skupiny Správci řadiče domény Řadič domény Řadič domény AAD,* aby bylo možné provádět úlohy konfigurace spravované domény pomocí tohoto uživatelského účtu.

## <a name="next-steps"></a>Další kroky

Chcete-li začít, [zaregistrujte se v programu Azure CSP](/partner-center/enrolling-in-the-csp-program). Potom můžete povolit služby Azure AD Domain Services pomocí [portálu Azure nebo](tutorial-create-instance.md) [Azure PowerShell](powershell-create-instance.md).
