---
title: Služby domény Azure AD pro poskytovatele cloudových řešení | Dokumenty společnosti Microsoft
description: Azure Active Directory Domain Services pro poskytovatele cloudových řešení Azure.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: iainfou
ms.openlocfilehash: 1134c078ee36a146cb1e1cbf8ca46f6cd9f8d775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72754445"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Doménové služby Azure Active Directory (AD) pro poskytovatele cloudových řešení Azure (CSP)
Tento článek vysvětluje, jak můžete používat služby Azure AD Domain Services v předplatném Azure CSP.

## <a name="overview-of-azure-csp"></a>Přehled o azure csp
Poskytovatel csp Azure je program pro partnery Microsoftu a poskytuje licenční kanál pro různé cloudové služby Microsoftu. Předpokladu CSP Azure umožňuje partnerům spravovat prodej, vlastnit fakturační vztah, poskytovat technickou a fakturační podporu a být jediným kontaktním místem zákazníka. Kromě toho azure csp poskytuje úplnou sadu nástrojů, včetně samoobslužného portálu a doprovodných api. Tyto nástroje umožňují partnerům CSP snadno zřídit a spravovat prostředky Azure a poskytovat fakturaci zákazníkům a jejich předplatným.

[Portál Partnerského centra](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) funguje jako vstupní bod pro všechny partnery Azure CSP. Poskytuje bohaté možnosti správy zákazníků, automatizované zpracování a další. Partneři Azure CSP můžou používat možnosti Centra partnerů pomocí webového uživatelského rozhraní nebo pomocí Prostředí PowerShell a různých volání rozhraní API.

Následující diagram znázorňuje, jak model CSP funguje na vysoké úrovni. Contoso má službu Azure AD Active Directory. Mají partnerství s csp, který nasazuje a spravuje prostředky v jejich předplatném Azure CSP. Contoso může mít také pravidelná (přímá) předplatná Azure, která se účtují přímo společnosti Contoso.

![Přehled modelu CSP](./media/csp/csp_model_overview.png)

Tenant partnera CSP má tři skupiny zvláštních agentů – agenty správce, agenty helpdesku a obchodní zástupce. Skupina agentů správce je přiřazena k roli správce klienta v adresáři Azure AD společnosti Contoso. V důsledku toho má uživatel patřící do skupiny agentů správce partnera CSP oprávnění správce v adresáři Azure AD společnosti Contoso. Když partner CSP zřídí předplatné Azure CSP pro Contoso, jejich skupina agentů správce je přiřazena k roli vlastníka pro toto předplatné. V důsledku toho mají agenti správce partnera CSP požadovaná oprávnění k poskytování prostředků Azure, jako jsou virtuální počítače, virtuální sítě a služby Domény Azure AD jménem společnosti Contoso.

Další informace najdete v přehledu [o azure csp](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Výhody používání služeb Azure AD Domain Services v předplatném Azure CSP
Služba Azure AD Domain Services poskytuje služby kompatibilní se službou Windows Server AD v Azure, jako je LDAP, ověřování protokolu Kerberos/NTLM, připojení k doméně, zásady skupiny a SLUŽBY DNS. V průběhu desetiletí bylo vytvořeno mnoho aplikací, které fungují proti službu AD pomocí těchto funkcí. Mnoho nezávislých dodavatelů softwaru (ISV) vytvořilo a nasadilo aplikace v prostorách svých zákazníků. Tyto aplikace jsou obtížné podporovat, protože to často vyžaduje přístup k různým prostředím, ve kterých jsou tyto aplikace nasazeny. S předplatnými Azure CSP máte jednodušší alternativu s škálovatrozsahem a flexibilitou Azure.

Služby Azure AD Domain Services teď podporují předplatná Azure CSP. Teď můžete nasadit svou aplikaci v předplatném Azure CSP vázaném na adresář Azure AD vašeho zákazníka. V důsledku toho mohou vaši zaměstnanci (pracovníci podpory) spravovat, spravovat a obsluhovat virtuální počítače, na kterých je vaše aplikace nasazena, pomocí podnikových přihlašovacích údajů vaší organizace. Dále můžete zřídit spravovanou doménu Služby Azure AD pro adresář Azure AD vašeho zákazníka. Aplikace je připojena ke spravované doméně zákazníka. Proto možnosti v rámci aplikace, které spoléhají na Kerberos/NTLM, LDAP nebo [System.DirectoryServices API](/dotnet/api/system.directoryservices) pracovat bez problémů proti adresáři zákazníka. Vaši koncoví zákazníci mají velký prospěch z využívání vaší aplikace jako služby, aniž byste se museli starat o údržbu infrastruktury, na které je aplikace nasazena.

Všechny fakturace pro prostředky Azure, které spotřebováváte v tomto předplatném, včetně služby Azure AD Domain Services, se vám účtují zpět. Máte plnou kontrolu nad vztahem se zákazníkem, pokud jde o prodej, fakturaci, technickou podporu atd. Díky flexibilitě platformy Zprostředkovatele copiínů Azure může malý tým agentů podpory obsluhovat mnoho takových zákazníků, kteří mají nasazené instance vaší aplikace.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Modely nasazení CSP pro služby domény Azure AD
Existují dva způsoby, jak můžete používat služby Azure AD Domain Services s předplatným Azure CSP. Vyberte si ten správný na základě aspekty zabezpečení a jednoduchosti vaši zákazníci mají.

### <a name="direct-deployment-model"></a>Model přímého nasazení
V tomto modelu nasazení je služba Azure AD Domain Services povolená v rámci virtuální sítě, která patří k předplatnému Azure CSP. Agenti správce partnera CSP mají následující oprávnění:
* Oprávnění globálního správce v adresáři Azure AD zákazníka.
* Oprávnění vlastníka předplatného v předplatném Azure CSP.

![Model přímého nasazení](./media/csp/csp_direct_deployment_model.png)

V tomto modelu nasazení mohou agenti správce zprostředkovatele CSP spravovat identity pro zákazníka. Tito agenti správy mají možnost zřazovat nové uživatele, skupiny, přidávat aplikace v adresáři Azure AD zákazníka atd. Tento model nasazení může být vhodný pro menší organizace, které nemají vyhrazeného správce identity nebo dávají přednost partnerovi CSP ke správě identit jejich jménem.


### <a name="peered-deployment-model"></a>Partnerský model nasazení
V tomto modelu nasazení azure ad doménové služby je povolena v rámci virtuální sítě patřící zákazníkovi – to znamená, že přímé předplatné Azure placené zákazníkem. Partner CSP pak může nasadit aplikace v rámci virtuální sítě, která patří k předplatnému CSP zákazníka. Virtuální sítě pak můžete připojit pomocí partnerského vztahu virtuální sítě Azure. V důsledku toho se úlohy nebo aplikace nasazené partnerem CSP v předplatném Azure CSP mohou připojit ke spravované doméně zákazníka zřízené v přímém předplatném Azure zákazníka.

![Partnerský model nasazení](./media/csp/csp_peered_deployment_model.png)

Tento model nasazení poskytuje oddělení oprávnění a umožňuje agentům technické podpory partnera CSP spravovat předplatné Azure a nasazovat a spravovat prostředky v něm. Agenti technické podpory partnera CSP však nemusí mít oprávnění globálního správce v adresáři Azure AD zákazníka. Správci identit zákazníka mohou nadále spravovat identity pro svou organizaci.

Tento model nasazení může být vhodný pro scénáře, kde nezávislý dodavatel softwaru (nezávislý dodavatel softwaru) poskytuje hostovizovanou verzi své místní aplikace, která se také potřebuje připojit ke službě AD zákazníka.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Správa spravovaných domén služby Azure AD Domain Services v předplatných CSP
Následující důležité důležité důležité informace platí při správě spravované domény v předplatném Azure CSP:

* **Agenti správce zprostředkovatele csp mohou zřídit spravovanou doménu pomocí svých přihlašovacích údajů:** Služby Azure AD Domain Services podporují předplatná Azure CSP. Uživatelé, kteří patří do skupiny agentů správce partnera CSP, proto mohou zřídit novou spravovanou doménu služby Azure AD Domain Services.

* **Kresličtí kreslicí lékaři mohou vytvářet nové spravované domény pro své zákazníky pomocí prostředí PowerShell:** Podrobnosti [najdete v tématu, jak povolit služby Azure AD Domain Services pomocí PowerShellu.](powershell-create-instance.md)

* **Agenti správce zprostředkovatele zprostředkovatele služeb a služeb pro správu nemohou provádět průběžné úlohy správy ve spravované doméně pomocí svých pověření:** Uživatelé správce csp nemohou provádět rutinní úlohy správy v rámci spravované domény pomocí svých pověření. Tito uživatelé jsou externí než adresář Azure AD zákazníka a jejich přihlašovací údaje nejsou k dispozici v adresáři Azure AD zákazníka. Proto Azure AD Domain Services nemá přístup k hodnotě hash hesla Kerberos a NTLM pro tyto uživatele. V důsledku toho tito uživatelé nelze ověřit na spravovaných doménách služby Azure AD Domain Services.

  > [!WARNING]
  > **Chcete-li provádět probíhající úlohy správy ve spravované doméně, je nutné vytvořit uživatelský účet v adresáři zákazníka.**
  > Ke spravované doméně se nelze přihlásit pomocí přihlašovacích údajů správce CSP. Použijte přihlašovací údaje uživatelského účtu, který patří do adresáře Azure AD zákazníka k tomu. Tato pověření potřebujete pro úlohy, jako je připojení virtuálních počítačů ke spravované doméně, správa SLUŽBY DNS, správa zásad skupiny atd.
  >

* **Uživatelský účet vytvořený pro průběžnou správu musí být přidán do skupiny Správci řadiče domény AAD:** Skupina Správci řadiče domény AAD má oprávnění k provádění určitých delegovaných úloh správy ve spravované doméně. Mezi tyto úkoly patří konfigurace DNS, vytváření organizačních jednotek, správa zásad skupiny atd. Pro partnera CSP provádět takové úkoly ve spravované doméně, uživatelský účet je třeba vytvořit v adresáři Azure AD zákazníka. Pověření pro tento účet musí být sdílena s agenty správce partnera CSP. Tento uživatelský účet musí být také přidán do skupiny Správci řadiče domény Řadičdomény aadua, aby bylo možné provádět úlohy konfigurace spravované domény pomocí tohoto uživatelského účtu.


## <a name="next-steps"></a>Další kroky
* [Zaregistrujte se do programu Azure CSP](https://docs.microsoft.com/partner-center/enrolling-in-the-csp-program) a začněte vytvářet podnikání prostřednictvím Azure CSP.
* Projděte si seznam [služeb Azure dostupných v Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Povolení služby Azure AD Domain Services pomocí PowerShellu](powershell-create-instance.md)
* [Začínáme s Azure AD Domain Services](tutorial-create-instance.md)
