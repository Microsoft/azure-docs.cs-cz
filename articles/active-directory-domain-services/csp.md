---
title: Azure Active Directory Domain Services pro poskytovatele cloudových řešení Azure | Microsoft Docs
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
ms.openlocfilehash: dc4ad7d8cf9f3267713fd066fa79a4d9d8ab733f
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612962"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Služba AD DS (Active Directory Domain Services) pro Azure Cloud Solution Provider (CSP) Azure Active Directory
Tento článek vysvětluje, jak můžete používat Azure AD Domain Services v předplatném CSP Azure.

## <a name="overview-of-azure-csp"></a>Přehled CSP Azure
CSP Azure je program pro partnery Microsoftu a poskytuje licenční kanál pro různé cloudové služby Microsoftu. CSP Azure umožňuje partnerům spravovat prodej, vlastnit fakturační vztah, poskytovat technickou a fakturační podporu a být jediným kontaktním bodem zákazníka. Kromě toho poskytuje poskytovatel cloudových služeb Azure kompletní sadu nástrojů, včetně samoobslužného portálu a doprovodného rozhraní API. Tyto nástroje umožňují partnerům CSP snadno zřizovat a spravovat prostředky Azure a poskytovat zákazníkům a jejich předplatným fakturaci.

[Portál partnerského centra](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) funguje jako vstupní bod pro všechny partnery CSP Azure. Nabízí bohatou možnost správy zákazníků, automatizované zpracování a další funkce. Partneři CSP Azure mohou využívat možnosti partnerského centra pomocí webového uživatelského rozhraní nebo pomocí prostředí PowerShell a různých volání rozhraní API.

Následující diagram znázorňuje, jak model CSP funguje na vysoké úrovni. Contoso má Active Directory služby Azure AD. Mají partnerství se zprostředkovatelem CSP, který nasazuje a spravuje prostředky ve svém předplatném Azure CSP. Contoso může mít také běžná (přímá) předplatná Azure, která se účtují přímo do společnosti Contoso.

![Přehled modelu CSP](./media/csp/csp_model_overview.png)

Tenant partnera CSP má tři speciální skupiny agentů – agenti pro správu, agenty helpdesku a prodejní agenty. Skupina agentů pro správu je přiřazená k roli správce klienta v adresáři Azure AD společnosti Contoso. V důsledku toho uživatel patřící do skupiny agenti správců CSP má oprávnění správce tenanta v adresáři Azure AD společnosti Contoso. Když partner CSP zřídí předplatné služby Azure CSP pro společnost Contoso, jejich skupina agentů pro správu je přiřazena k roli vlastníka daného předplatného. V důsledku toho mají agenti správce CSP požadovaná oprávnění ke zřízení prostředků Azure, jako jsou virtuální počítače, virtuální sítě a Azure AD Domain Services jménem společnosti Contoso.

Další informace najdete v tématu [Přehled CSP Azure](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview) .

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Výhody používání Azure AD Domain Services v předplatném služby Azure CSP
Azure AD Domain Services poskytuje služby kompatibilní se službou Windows Server AD v Azure, jako je LDAP, ověřování pomocí protokolu Kerberos nebo NTLM, připojení k doméně, zásady skupiny a DNS. V průběhu desetiletí bylo mnoho aplikací postavených na spolupráci se službou AD pomocí těchto schopností. Mnoho nezávislých výrobců softwaru (ISV) má v místním prostředí své zákazníky sestavené a nasazené aplikace. Tyto aplikace jsou náročné na podporu, protože to často vyžaduje přístup k různým prostředím, ve kterých jsou tyto aplikace nasazené. S předplatnými Azure CSP máte jednodušší alternativu s měřítkem a flexibilitou Azure.

Azure AD Domain Services teď podporuje předplatná Azure CSP. Svou aplikaci teď můžete nasadit v předplatném CSP Azure, které je vázané na adresář služby Azure AD vašeho zákazníka. V důsledku toho mohou zaměstnanci (pracovníci podpory) spravovat, spravovat a obsluhovat virtuální počítače, na kterých je vaše aplikace nasazena, pomocí podnikových přihlašovacích údajů vaší organizace. Dále můžete zřídit Azure AD Domain Services spravovanou doménu pro adresář Azure AD vašeho zákazníka. Vaše aplikace je připojená k spravované doméně zákazníka. Proto funkce v rámci aplikace, které závisí na protokolu Kerberos/NTLM, LDAP nebo [rozhraní API System. DirectoryServices](/dotnet/api/system.directoryservices) , bez problémů fungují s adresářem zákazníka. Vaši koncoví zákazníci významně využívají používání vaší aplikace jako služby, aniž by museli se starat o údržbu infrastruktury, ve které je aplikace nasazená.

Veškerá fakturace za prostředky Azure, které spotřebováváte v tomto předplatném, včetně Azure AD Domain Services, se účtují zpátky. V rámci vztahu se zákazníkem můžete udržovat plnou kontrolu nad vztahem k prodeji, fakturaci, technické podpoře atd. Díky flexibilitě platformy Azure CSP může malý tým agentů podpory obsluhovat mnoho takových zákazníků, kteří mají nasazené instance aplikace.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Modely nasazení CSP pro službu Azure AD Domain Services
Existují dva způsoby, jak můžete Azure AD Domain Services použít s předplatným CSP Azure. Vyberte si napravo na základě bezpečnostních a jednoduchosti důležitých informací, které vaši zákazníci mají.

### <a name="direct-deployment-model"></a>Model přímého nasazení
V tomto modelu nasazení je Azure AD Domain Services povolený ve virtuální síti patřící do předplatného služby Azure CSP. Agenti správce pro poskytovatele CSP mají následující oprávnění:
* Oprávnění globálního správce v adresáři Azure AD zákazníka.
* Oprávnění vlastníka předplatného v předplatném Azure CSP.

![Model přímého nasazení](./media/csp/csp_direct_deployment_model.png)

V tomto modelu nasazení můžou agenti správce zprostředkovatele CSP spravovat identity pro zákazníka. Tito agenti správců mají možnost zřídit nové uživatele, skupiny a přidat aplikace v adresáři Azure AD zákazníka atd. Tento model nasazení může být vhodný pro menší organizace, které nemají vyhrazeného správce identit nebo dávají přednost za to, aby partner CSP spravoval identity jménem.


### <a name="peered-deployment-model"></a>Model partnerského nasazení
V tomto modelu nasazení je Azure AD Domain Services povolený ve virtuální síti, která je součástí zákazníka – to znamená přímé předplatné Azure placené zákazníkem. Partner CSP pak může nasadit aplikace v rámci virtuální sítě, které patří k předplatnému poskytovatele CSP zákazníka. Virtuální sítě je pak možné propojit pomocí partnerského vztahu virtuálních sítí Azure. V důsledku toho se můžou zatížení a aplikace nasazené partnerem CSP v předplatném Azure CSP připojit k spravované doméně zákazníka zřízené v přímém předplatném Azure zákazníka.

![Model partnerského nasazení](./media/csp/csp_peered_deployment_model.png)

Tento model nasazení poskytuje oddělení oprávnění a umožňuje agentům helpdesku poskytovatele CSP spravovat předplatné Azure a nasazovat a spravovat prostředky v něm. Agenti helpdesku pro poskytovatele CSP ale nemusí mít oprávnění globálního správce k adresáři Azure AD zákazníka. Správci identity zákazníka můžou dál spravovat identity pro svou organizaci.

Tento model nasazení může být vhodný pro situace, kdy nezávislý výrobce softwaru (nezávislý výrobce softwaru) poskytuje hostovanou verzi své místní aplikace, která se také musí připojit ke službě AD zákazníka.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Správa Azure AD Domain Services spravovaných domén v předplatných CSP
Při správě spravované domény v předplatném Azure CSP platí následující důležité informace:

* **Agenti správce CSP můžou zřídit spravovanou doménu pomocí svých přihlašovacích údajů:** Azure AD Domain Services podporuje předplatná Azure CSP. Proto uživatelé patřící do skupiny agenti správce v partnerovi CSP mohou zřídit novou Azure AD Domain Services spravovanou doménu.

* **Poskytovatelé CSP můžou vytvořit skripty pro vytváření nových spravovaných domén pro zákazníky pomocí prostředí PowerShell:** Podrobnosti najdete v tématu [povolení Azure AD Domain Services pomocí prostředí PowerShell](powershell-create-instance.md) .

* **Agenti správce CSP nemohou provádět probíhající úlohy správy ve spravované doméně pomocí svých přihlašovacích údajů:** Uživatelé správců CSP nemohou provádět rutinní úlohy správy v rámci spravované domény pomocí svých přihlašovacích údajů. Tito uživatelé jsou externí v adresáři Azure AD zákazníka a jejich přihlašovací údaje nejsou k dispozici v adresáři Azure AD zákazníka. Proto Azure AD Domain Services pro tyto uživatele nemá přístup k hodnotám hash hesla protokolu Kerberos a NTLM. V důsledku toho se tyto uživatele nedají ověřit u Azure AD Domain Services spravovaných domén.

  > [!WARNING]
  > **Aby bylo možné provádět úlohy probíhající správy ve spravované doméně, musíte v adresáři zákazníka vytvořit uživatelský účet.**
  > K spravované doméně se nemůžete přihlásit pomocí přihlašovacích údajů uživatele správce CSP. K tomu slouží přihlašovací údaje uživatelského účtu patřícího k adresáři Azure AD zákazníka. Tyto přihlašovací údaje budete potřebovat pro úlohy, jako je například připojení virtuálních počítačů ke spravované doméně, Správa služby DNS, Správa Zásady skupiny atd.
  >

* **Uživatelský účet vytvořený pro probíhající správu musí být přidán do skupiny Správci AAD DC:** Skupina Správci AAD DC má oprávnění provádět určité delegované úlohy správy ve spravované doméně. Mezi tyto úlohy patří konfigurace DNS, vytváření organizačních jednotek, Správa zásad skupiny atd. Aby mohl partner CSP provádět takové úkoly ve spravované doméně, je potřeba vytvořit uživatelský účet v adresáři Azure AD zákazníka. Přihlašovací údaje pro tento účet musí být sdíleny s agenty správce partnera CSP. Tento uživatelský účet musí být také přidán do skupiny Správci AAD DC, aby bylo možné provádět úlohy konfigurace ve spravované doméně pomocí tohoto uživatelského účtu.


## <a name="next-steps"></a>Další postup
* [Zaregistrujte se do programu Azure CSP](https://docs.microsoft.com/partner-center/enrolling-in-the-csp-program) a začněte vytvářet firmy prostřednictvím CSP Azure.
* Projděte si seznam [služeb Azure dostupných ve zprostředkovateli CSP Azure](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Povolení služby Azure AD Domain Services pomocí PowerShellu](powershell-create-instance.md)
* [Začínáme s Azure AD Domain Services](tutorial-create-instance.md)
