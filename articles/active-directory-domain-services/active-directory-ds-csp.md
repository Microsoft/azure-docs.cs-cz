---
title: Azure Active Directory Domain Services pro Azure Cloud Solution Provider | Dokumentace Microsoftu
description: Azure Active Directory Domain Services pro Azure Cloud Solution Providers.
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mahesh-unnikrishnan
editor: curtand
ms.assetid: 56ccb219-11b2-4e43-9f07-5a76e3cd8da8
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/08/2017
ms.author: maheshu
ms.openlocfilehash: 8d87312591f44bac5fd9a4ff63eccc19333a870c
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "42056799"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Domény služby Azure Active Directory (AD) pro Azure Cloud Solution Provider (CSP)
Tento článek vysvětluje, jak můžete pomocí služby Azure AD Domain Services v rámci předplatného Azure CSP.

## <a name="overview-of-azure-csp"></a>Přehled Azure CSP
Azure CSP je program pro Microsoft Partners a poskytuje kanál licence k různým cloudovým službám Microsoftu. Azure CSP umožňuje partnerům, aby řídit prodej, vlastní fakturační vztah, poskytují technickou a fakturační podporu a být zákazníka, jeden kontaktní bod. Azure CSP navíc poskytuje kompletní sadu nástrojů, včetně samoobslužného portálu a doprovodných rozhraní API. Tyto nástroje mohou partneři CSP snadno zřizovat a spravovat prostředky Azure a vyúčtování u zákazníků a jejich předplatného.

[Portál partnerském centru](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) funguje jako vstupní bod pro všechny partnery Azure CSP. Poskytuje možnosti správy zákazníků, automatizované zpracování a další. Partneři CSP Azure můžete použít možnosti partnerského centra s využitím webové uživatelské rozhraní nebo pomocí prostředí PowerShell a volání rozhraní API.

Následující diagram znázorňuje, jak funguje model poskytovatele CSP na vysoké úrovni. Contoso má službu Azure AD Active Directory. Mají partnerství s CSP, který implementuje a spravuje prostředky v rámci svého předplatného Azure CSP. Společnost Contoso může mít také běžná (přímý) předplatná Azure, které se fakturují přímo na Contoso.

![Přehled modelu CSP](./media/csp/csp_model_overview.png)

CSP partner tenant má tři skupiny speciální agent - správce agentů, technické podpory agenty a agenty prodeje. Agenty skupiny správce se přiřadí k roli správce klienta v adresáři společnosti Contoso služby Azure AD. V důsledku toho uživatel patří do skupiny agentů CSP partner správce má oprávnění správce tenanta v adresáři společnosti Contoso služby Azure AD. Když ustanovení partner CSP předplatné Azure CSP pro společnost Contoso, jejich agenty skupiny správců přiřazena role vlastníka pro toto předplatné. V důsledku toho CSP partner správce agentů má požadovaná oprávnění ke zřízení prostředků Azure, jako je například virtuálních počítačů, virtuálních sítí a Azure AD Domain Services jménem společnosti Contoso.

Další informace najdete v tématu [základní informace o Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Výhody používání služby Azure AD Domain Services v rámci předplatného Azure CSP
Azure AD Domain Services poskytuje kompatibilní služby systému Windows Server AD do Azure, jako jsou LDAP, ověřování protokolu Kerberos nebo NTLM, připojení k doméně, zásady skupiny a DNS. Během desetiletí mnohé aplikace sestavené tak, aby odpovídaly AD pomocí těchto možností. Mnoho nezávislí výrobci softwaru (ISV) mají sestavíte a nasadíte aplikace v místě jejich zákazníků. Tyto aplikace jsou obtížné pro podporu od, která často vyžaduje přístup do různých prostředí, ve kterých jsou tyto aplikace nasadit. S předplatnými Azure CSP budete mít jednodušší alternativu se Škálováním a flexibilitě Azure.

Azure AD Domain Services nyní podporuje předplatných Azure CSP. Teď můžete nasadit aplikace v rámci předplatného Azure CSP vázané na adresář Azure AD vašeho zákazníka. V důsledku toho vaši zaměstnanci (pracovníci podpory oddělení) můžete spravovat spravovat a služby virtuálních počítačů, na kterých je vaše aplikace nasazena pomocí firemních přihlašovacích údajů vaší organizace. Kromě toho můžete zřídit spravované domény služby Azure AD Domain Services pro adresář Azure AD vašeho zákazníka. Aplikace je připojená k spravované doméně vašeho zákazníka. Proto funkce v rámci vaší aplikace, které závisí na protokolu Kerberos nebo NTLM, LDAP, nebo [System.DirectoryServices API](https://msdn.microsoft.com/library/system.directoryservices) pracovní bezproblémově proti adresáři vašeho zákazníka. Vaše koncové zákazníky bývá ve využívání vaší aplikace jako služby, aniž byste museli starat o údržbu infrastruktury, které je aplikace nasazená na.

Všechny fakturace pro prostředky Azure, které skutečně využijete v tomto předplatném, včetně služby Azure AD Domain Services, jsou zpoplatněné nad rámec zpět vám. Zachovat plnou kontrolu nad vztah se zákazníkem při rozhodování o prodeje, fakturace, technické podpory atd. Flexibilní platformu Azure CSP, malý tým agenty služby mnoho těmito zákazníky, kteří mají instancí aplikace nasazena.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Modely nasazení zprostředkovatele kryptografických služeb pro Azure AD Domain services
Existují dva způsoby, které můžete použít Azure AD Domain Services s předplatným Azure CSP. Vyberte si ten správný založené na aspekty zabezpečení a jednoduchost, které vaši zákazníci mají.

### <a name="direct-deployment-model"></a>Model nasazení s přímým přístupem
V tomto modelu nasazení Azure AD Domain Services je povolena v rámci virtuální sítě, které patří do předplatného Azure CSP. CSP partner správce agentů mít následující oprávnění:
* Oprávnění globálního správce v adresáři služby Azure AD zákazníka.
* Oprávnění vlastníka předplatného na předplatné Azure CSP.

![Model nasazení s přímým přístupem](./media/csp/csp_direct_deployment_model.png)

V tomto modelu nasazení můžete spravovat poskytovatele CSP správce agentů identit pro zákazníka. Tito agenti pro správu se budou moct zřizovat noví uživatelé, skupiny, přidání aplikace do adresáře služby Azure AD zákazníka atd. Tento model nasazení může být vhodná pro menší organizace, které mají vyhrazené identity správce nebo dáváte přednost pro partner CSP ke správě identit jejich jménem.


### <a name="peered-deployment-model"></a>Partnerské nasazení modelu
V tomto modelu nasazení Azure AD Domain Services je povolena v rámci virtuální sítě patřící do zákazníků – to znamená, přímé předplatné Azure zaplaceno zákazníka. CSP partner pak můžete nasadit aplikace v rámci virtuální sítě, které patří do předplatného zákazníka CSP. Virtuální sítě můžou být připojené pak pomocí partnerského vztahu virtuální sítě Azure. V důsledku toho úloh a aplikací, nasazení partner CSP v předplatném Azure CSP může připojit k spravované doméně zákazníka zřízené v rámci předplatného Azure s přímým přístupem zákazníka.

![Partnerské nasazení modelu](./media/csp/csp_peered_deployment_model.png)

Tento model nasazení umožňuje oddělit oprávnění a umožňuje CSP partner helpdesku agentů pro správu předplatného Azure a nasadit a spravovat prostředky v ní. Agenty helpdesku CSP partner však není potřeba mít oprávnění globálního správce adresáře služby Azure AD zákazníka. Správci zákazníka identit můžete nadále spravovat identity pro svoji organizaci.

Tento model nasazení může být vhodné k scénáře, kdy poskytuje hostovaný ISV (nezávislý výrobce softwaru) verze místních aplikací, takže bude také potřeba připojit k odběrateli uživatele AD.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Správa služby Azure AD Domain Services spravované domény v předplatných CSP
Při správě spravované domény v předplatném Azure CSP, platí následující důležité aspekty:

* **Zprostředkovatel kryptografických služeb správce agentů můžete zřídit spravované domény pomocí svých přihlašovacích údajů:** předplatných Azure CSP podporuje Azure AD Domain Services. Uživatelé, kteří patří do skupiny agentů správce CSP partner, proto můžete zřídit nové spravované doméně Azure AD Domain Services.

* **Zprostředkovatele kryptografických služeb můžete používat skripty pro vytváření nových spravovaných domén pro své zákazníky pomocí prostředí PowerShell:** naleznete v tématu [jak povolit Azure AD Domain Services pomocí prostředí PowerShell](active-directory-ds-enable-using-powershell.md) podrobnosti.

* **CSP správce agentů nelze provádět úlohy průběžné správy ve spravované doméně pomocí svých přihlašovacích údajů:** CSP správcům nelze provádět úlohy běžné správy ve spravované doméně pomocí svých přihlašovacích údajů. Tito uživatelé jsou externí vzhledem k adresáři služby Azure AD zákazníka a jejich pověření nejsou k dispozici v rámci adresáře služby Azure AD zákazníka. Azure AD Domain Services, proto nemá přístup k protokolu Kerberos a NTLM hodnot hash hesel pro tyto uživatele. V důsledku toho tato uživatelé nemohou být ověřeni v Azure AD Domain Services spravované domény.

  > [!WARNING]
  > **Musíte vytvořit uživatelský účet v adresáři zákazníka k provedení úlohy průběžné správy ve spravované doméně.**
  > Nemůžete se přihlásit ke spravované doméně pomocí přihlašovacích údajů uživatele CSP správce. K tomu použijte přihlašovací údaje uživatelského účtu, který patří do adresáře Azure AD zákazníka. Budete potřebovat tyto přihlašovací údaje pro úlohy, jako je připojení virtuálních počítačů do spravované domény, Správa DNS, Správa skupiny zásad atd.
  >

* **Uživatelský účet vytvořený pro průběžné správy musí být přidána do skupiny 'Správci AAD DC':** "Správci AAD DC" skupina má oprávnění k provádění určitých úloh delegovanou správu. ve spravované doméně. Tyto úlohy zahrnují nastavení DNS, vytvoření organizační jednotky, Správa zásad skupiny atd. Pro CSP partnera k provádění takových úloh ve spravované doméně uživatelský účet musí být vytvořeny v rámci adresáře služby Azure AD zákazníka. Přihlašovací údaje pro tento účet musí být sdíleny s agenty správce CSP partner. Tento uživatelský účet musí také, přidají do skupiny "Správci AAD DC" Povolit konfiguračních úloh ve spravované doméně, která se má provést pomocí tohoto uživatelského účtu.


## <a name="next-steps"></a>Další postup
* [Zaregistrujte se do programu Azure CSP](https://docs.microsoft.com/partner-center/enrolling-in-the-csp-program) a začněte vytvářet svého podnikání prostřednictvím Azure CSP.
* Projděte si seznam [služby Azure k dispozici v Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Povolení služby Azure AD Domain Services pomocí PowerShellu](active-directory-ds-enable-using-powershell.md)
* [Začínáme s Azure AD Domain Services](active-directory-ds-getting-started.md)
