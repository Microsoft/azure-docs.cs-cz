---
title: Azure Active Directory Domain Services pro Azure Cloud poskytovatelů řešení | Microsoft Docs
description: Azure Active Directory Domain Services pro poskytovatelů Azure Cloud Solution Providers.
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
ms.topic: article
ms.date: 12/08/2017
ms.author: maheshu
ms.openlocfilehash: 362d7226434733ffeaa5be6f988afa4016a7c827
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213868"
---
# <a name="azure-active-directory-ad-domain-services-for-azure-cloud-solution-providers-csp"></a>Doména služby Azure Active Directory (AD) pro poskytovatelů Azure Cloud Solution Providers (CSP)
Tento článek vysvětluje, jak můžete použít Azure AD Domain Services v předplatné Azure CSP.

## <a name="overview-of-azure-csp"></a>Přehled Azure zprostředkovatele kryptografických služeb
Azure zprostředkovatele kryptografických služeb je program pro Microsoft Partners a poskytuje licenci kanál pro různé cloudové služby Microsoftu. Zprostředkovatel kryptografických služeb Azure umožňuje partnerům spravovat prodej vlastní fakturační vztah, zadejte technické podpory a podpory fakturace a být zákazníka, jeden kontaktní bod. Kromě toho zprostředkovatel kryptografických služeb Azure poskytuje úplnou sadu nástrojů, včetně samoobslužný portál a doplňujícími rozhraní API. Tyto nástroje povolit CSP partnery snadno zřizují a spravují prostředky Azure a poskytnout fakturace pro zákazníky a jejich odběry.

[Partnerské centrum portál](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview) funguje jako vstupní bod pro všechny servery pro zprostředkovatele kryptografických služeb Azure. Poskytuje možnosti správy bohaté zákazníka, automatizované zpracování a další. Azure CSP partnery můžete použít možnosti Partnerské centrum, pomocí uživatelského rozhraní založené na webu nebo pomocí prostředí PowerShell a různých volání rozhraní API.

Následující diagram znázorňuje, jak funguje modelu CSP na vysoké úrovni. Contoso má Azure AD Active Directory. Mají partnerství s CSP, který nasazení a správu prostředků v příslušného předplatného poskytovatele CSP Azure. Contoso mohou mít i regulární (přímo) předplatná Azure, které se účtují přímo na Contoso.

![Přehled modelu zprostředkovatele kryptografických služeb](./media/csp/csp_model_overview.png)

Klient partnerovi CSP má tři speciální agenta skupiny - správce agentů, agenti Helpdesk a prodej agenty. Tato skupina agentů správce je přiřazená k roli správce klienta v adresáři společnosti Contoso Azure AD. V důsledku toho uživatel patřící do skupiny agentů partnerovi CSP správce má oprávnění správce klienta v adresáři společnosti Contoso Azure AD. Když zřizuje partnera zprostředkovatele kryptografických služeb je přiřazení předplatné Azure zprostředkovatele kryptografických služeb pro společnost Contoso, jejich správce agentů skupiny k roli vlastníka pro toto předplatné. V důsledku toho partnerovi CSP správce agentů mít požadovaná oprávnění ke zřízení prostředků Azure, například virtuálních počítačů, virtuálních sítí a Azure AD Domain Services jménem společnosti Contoso.

Další informace najdete v tématu [zprostředkovatele kryptografických služeb Azure – přehled](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview)

## <a name="benefits-of-using-azure-ad-domain-services-in-an-azure-csp-subscription"></a>Výhody použití služby Azure AD Domain Services v předplatné Azure CSP
Služba Azure AD Domain Services poskytuje kompatibilní služby systému Windows Server AD ve službě Azure, jako jsou LDAP, ověřování protokolu Kerberos nebo NTLM, připojení k doméně, zásad skupiny a DNS. Během desetiletí mnoho aplikací mít sestavily pro práci s AD pomocí těchto možností. Mnoho nezávislí dodavatelé softwaru (ISV) máte vytvořené a nasazené aplikace v místním svým zákazníkům. Tyto aplikace jsou obtížné pro podporu vzhledem k tomu, který často vyžaduje přístup do různých prostředí, ve kterých jsou tyto aplikace nasadit. K předplatnému Azure CSP máte jednodušší alternativní s škálování a flexibilitu ve službě Azure.

Azure AD Domain Services nyní podporuje předplatných Azure CSP. Teď můžete nasadit aplikaci v předplatné Azure CSP vázaný na adresář Azure AD vašeho zákazníka. V důsledku toho vaši zaměstnanci (pracovníky technické podpory) můžete spravovat, spravovat a služby virtuálních počítačů, na kterých vaše aplikace je nasazená pomocí podnikové přihlašovací údaje vaší organizace. Navíc můžete zřídit spravované doméně služby Azure AD Domain Services pro adresář Azure AD vašeho zákazníka. Aplikace je připojený k spravované doméně vašeho zákazníka. Proto možnosti v rámci vaší aplikace, které závisí na protokolu Kerberos nebo NTLM, LDAP, nebo [System.DirectoryServices API](https://msdn.microsoft.com/library/system.directoryservices) pracovní bezproblémově proti adresáři vašeho zákazníka. Koncovým zákazníkům využívat výrazně nespotřeboval aplikace jako službu, aniž by museli starosti údržbu infrastruktury, které je aplikace nasazená na.

Všechny fakturace pro prostředky Azure, které spotřebujete v tom, že je pro vás účtován předplatného, včetně Azure AD Domain Services. Udržovat plnou kontrolu nad vztah se zákazníkem, pokud jde o prodeji, fakturace, technické podpory atd. Pružně platformou Azure CSP může obsluhovat malý tým podpory agentů mnoho takové zákazníky, kteří mají instancí aplikace nasazena.


## <a name="csp-deployment-models-for-azure-ad-domain-services"></a>Modely nasazení zprostředkovatele kryptografických služeb pro Azure AD Domain services
Existují dva způsoby, ve kterých můžete použít Azure AD Domain Services s předplatným Azure CSP. Vyberte si ten správný podle aspekty zabezpečení a jednoduchost, kterou mají vaši zákazníci.

### <a name="direct-deployment-model"></a>Přímé nasazení modelu
V tomto modelu nasazení Azure AD Domain Services je povolena v rámci virtuální sítě, které patří do předplatného Azure CSP. CSP partner správce agentů mít následující oprávnění:
* Globální správce oprávnění v adresáři služby Azure AD zákazníka.
* Oprávnění vlastníka předplatného v předplatném Azure CSP.

![Přímé nasazení modelu](./media/csp/csp_direct_deployment_model.png)

V tomto modelu nasazení agentů správce poskytovateli CSP spravovat identity pro zákazníka. Tyto agenty správce mít možnost zřídit nové uživatele, skupiny, přidat aplikace v rámci adresáře služby Azure AD zákazníka atd. Tento model nasazení může být vhodné pro menší organizace, které mají vyhrazené identity správce nebo raději partnera CSP ke správě identit jejich jménem.


### <a name="peered-deployment-model"></a>Model peered nasazení
V tomto modelu nasazení Azure AD Domain Services je povolena v rámci virtuální sítě patřící do zákazník – to znamená, přímé předplatného Azure zaplacení zákazníkem. CSP partner pak můžete nasadit aplikace v rámci virtuální sítě patřící do předplatného poskytovatele CSP zákazníka. Virtuální sítě může být připojena pak pomocí partnerský vztah Azure virtuální sítě. V důsledku toho zatížení nebo aplikace nasazené partner zprostředkovatele kryptografických služeb v rámci předplatného Azure CSP může připojit k spravované doméně zákazníka zřízené v rámci předplatného Azure přímé zákazníka.

![Model peered nasazení](./media/csp/csp_peered_deployment_model.png)

Tento model nasazení umožňuje oddělit oprávnění a umožňuje CSP partnera helpdesk agenti pro správu předplatného Azure a nasadit a spravovat prostředky v něm. Agenti helpdesk partnerovi CSP však není potřeba na adresář Azure AD zákazníka oprávnění globálního správce. Správce identit zákazníka můžete nadále Správa identit pro jejich organizaci.

Tento model nasazení může být vhodnější situací, kdy nezávislý dodavatel softwaru (nezávislý dodavatel softwaru) hostovaný verze místní aplikace, který se taky musí připojit k odběrateli je AD.


## <a name="administering-azure-ad-domain-services-managed-domains-in-csp-subscriptions"></a>Správa Azure AD Domain Services spravované domény v rámci předplatných zprostředkovatele kryptografických služeb
Při správě spravované domény v předplatné Azure CSP, platí následující důležité aspekty:

* **Zprostředkovatel kryptografických služeb správce agentů můžete zřídit spravované doméně pomocí svých přihlašovacích údajů:** Azure AD Domain Services podporuje předplatných Azure CSP. Proto uživatelé, kteří patří do skupiny agentů správce CSP partnera můžete zřídit nové spravované doméně služby Azure AD Domain Services.

* **Zprostředkovatelé kryptografických služeb můžete skript vytvoření nové spravované domény zákazníkům pomocí prostředí PowerShell:** najdete v části [jak povolit Azure AD Domain Services pomocí prostředí PowerShell](active-directory-ds-enable-using-powershell.md) podrobnosti.

* **Zprostředkovatel kryptografických služeb správce agentů nelze provádět úlohy správy probíhající ve spravované doméně pomocí svých přihlašovacích údajů:** CSP správci nelze provádět úlohy běžné správy v rámci spravované doméně pomocí svých přihlašovacích údajů. Tito uživatelé jsou externí vzhledem k adresář Azure AD zákazníka a jejich pověření nejsou k dispozici v rámci adresáře služby Azure AD zákazníka. Azure AD Domain Services proto nemá přístup k protokolu Kerberos a NTLM hodnot hash hesel pro tyto uživatele. V důsledku toho nemůže být ověřen takových uživatelů Azure AD Domain Services spravované domény.

  > [!WARNING]
  > **Musíte vytvořit uživatelský účet v adresáři zákazníka k plnění úkolů průběžnou správu spravované domény.**
  > Nemůžete se přihlásit k spravované doméně pomocí přihlašovacích údajů uživatele CSP správce. Uděláte to tak k použití pověření uživatelského účtu, které patří do adresáře služby Azure AD zákazníka. Je třeba tyto přihlašovací údaje pro úlohy, jako je připojení virtuálních počítačů k spravované doméně, Správa služby DNS, Správa skupiny zásad atd.
  >

* **Uživatelský účet vytvořený pro trvalou správu musí být přidaný do skupiny "Administrators řadič domény AAD.:** 'správci AAD řadič domény, skupina má oprávnění k provedení určité delegované správy úloh ve spravované doméně. Tyto úlohy patří konfigurace DNS, vytvoření organizační jednotky, Správa zásad skupiny atd. Uživatelský účet pro CSP partnera k provádění takových úloh ve spravované doméně, musí být vytvořen v rámci zákazníka adresář Azure AD. Pověření pro tento účet musí být sdílená s agenty správce partnera CSP. Navíc musí tento uživatelský účet přidat do skupiny "Administrators AAD řadiče domény" Povolit úlohy konfigurace na spravované domény, ke které se provádí pomocí tohoto uživatelského účtu.


## <a name="next-steps"></a>Další postup
* [Registrace do programu Azure CSP](https://partnercenter.microsoft.com/partner/programs) a začít vytvářet obchodní prostřednictvím zprostředkovatele kryptografických služeb Azure.
* Projděte si seznam [služby Azure k dispozici v Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).
* [Povolení služby Azure AD Domain Services pomocí PowerShellu](active-directory-ds-enable-using-powershell.md)
* [Začínáme s Azure AD Domain Services](active-directory-ds-getting-started.md)
