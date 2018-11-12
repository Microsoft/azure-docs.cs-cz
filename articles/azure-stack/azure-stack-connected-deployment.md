---
title: Integrované systémy pro nasazení v Azure připojené rozhodnutí pro službu Azure Stack | Dokumentace Microsoftu
description: Určení při rozhodování o nasazení na víc uzlů připojeného k Azure stacku Azure pro plánování nasazení.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/05/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: e0f51306ab17e88db9d28da14faab9de0b445f74
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037902"
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Integrované systémy pro nasazení v Azure připojené plánování rozhodnutí pro službu Azure Stack
Poté, co jste se rozhodli [jak budou integrovat Azure Stack v hybridním cloudovém prostředí](azure-stack-connection-models.md), pak můžete dokončit vaše rozhodnutí o nasazení služby Azure Stack.

Nasazení Azure Stack připojení k Azure znamená, že máte Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) pro úložiště identit. Můžete také vybrat z obou model fakturace: platit jako využití nebo na základě kapacity. Připojené nasazení je výchozí možnost, neboť umožňuje zákazníkům, kteří získali co nejvíce mimo Azure Stack, zejména pro hybridní cloudové scénáře, které se týkají Azure a Azure Stack. 

## <a name="choose-an-identity-store"></a>Zvolte úložiště identit
U připojených nasazení můžete mezi Azure AD nebo AD FS pro úložiště identit. Odpojené nasazení bez připojení k Internetu, lze použít pouze služby AD FS.

Zvoleného úložiště identit nemá žádný vliv na klientské virtuální počítače (VM). Virtuální počítače tenanta rozhodnout, které úložiště identit se chcete připojit k, v závislosti na tom, jak se nakonfiguruje: Azure AD, pracovní skupiny připojených k doméně, Windows Server Active Directory atd. To nesouvisí s rozhodnutí zprostředkovatele identity Azure Stack. 

Například pokud nasadíte IaaS klientské virtuální počítače na Azure Stack a nechcete, aby připojení podnikové domény služby Active Directory a pomocí účtů z něj, můžete stále to provedete. Není nutné pro použití úložiště identit Azure AD, kterou tady vyberete pro tyto účty.

### <a name="azure-ad-identity-store"></a>Úložiště identit Azure AD
Při použití služby Azure AD pro úložiště vaší identity vyžaduje dva účty Azure AD: fakturace a účtu globálního správce. Tyto účty můžete být účty stejné nebo různé účty. Pomocí stejného uživatelského účtu může být jednodušší a užitečné, pokud máte omezený počet účtů Azure, by mohla naznačovat vašim obchodním potřebám pomocí dva účty:

1. **Účet globálního správce** (vyžadováno pouze pro připojené nasazení). Toto je účet Azure, který slouží k vytváření aplikací a instanční objekty pro služby infrastruktury Azure Stack v Azure Active Directory. Tento účet musí mít oprávnění správce adresáře na adresář, který se nasadí váš systém Azure Stack v části. To se stane operátor"cloud" oprávnění globálního správce Azure AD tenanta a použije se: 
    - Zřízení a delegování aplikace a instanční objekty pro všechny služby Azure Stack, které potřebují komunikovat s Azure Active Directory a rozhraní Graph API. 
    - Jako účet správce služeb. To je vlastníkem výchozí předplatné poskytovatele (který můžete později změnit). Může přihlásit k portálu pro správu služby Azure Stack s tímto účtem a můžete ji použít k vytvoření nabídky a plány, nastavení kvót a provádění dalších funkcí pro správu ve službě Azure Stack.
2. **Fakturační účet** (požadováno pro obě připojené a odpojené nasazení). Tento účet Azure se používá k navázání fakturační vztah mezi váš systém Azure Stack integrované a back-endu Azure commerce. Toto je účet, který se bude účtovat poplatky za Azure Stack. Tento účet se používá také pro nabídku položky ve na webu marketplace a další hybridní scénáře. 

### <a name="ad-fs-identity-store"></a>Úložiště identit služby AD FS
Tuto možnost zvolte, pokud chcete používat vlastní identity, jako je třeba vaší podnikové služby Active Directory, pro účty správce služby.  

## <a name="choose-a-billing-model"></a>Zvolte model fakturace
Můžete použít buď **platit jako využití** nebo **kapacity** model fakturace. Platba jako využití fakturační model nasazení musí být schopen reportovat využití prostřednictvím připojení k Azure alespoň jednou za 30 dnů. Proto model fakturace platit jako využití je k dispozici pouze pro připojené nasazení.  

### <a name="pay-as-you-use"></a>Platba jako využití
Použití s modelem účtování platit jako využití se účtuje s předplatným Azure. Platíte jenom při použití služby Azure Stack. Pokud je model rozhodování, budete potřebovat předplatné Azure a ID účtu, který je spojený s tímto předplatným (například serviceadmin@contoso.onmicrosoft.com). Předplatných EA, CSP a CSL jsou podporovány. Je nakonfigurováno vytváření sestav využití během [registrace Azure Stack](azure-stack-registration.md).

> [!NOTE]
> Ve většině případů podnikoví zákazníci budou používat předplatných EA a používat předplatná CSP nebo CSL poskytovatelů služeb.

Pokud se chystáte použít jiné předplatné poskytovatele CSP, naleznete v tabulce níže k identifikaci jaké předplatné použít zprostředkovatele kryptografických služeb, jako správný přístup závisí na přesný scénář CSP:

|Scénář|Možnosti domény a předplatné|
|-----|-----|
|Jste **přímé Partner CSP** nebo **nepřímý poskytovatel CSP**, a budou fungovat Azure stacku|Použijte předplatné CSL (běžné vrstvu služby).<br>     nebo<br>Vytvoření tenanta Azure AD s popisný název, v partnerském centru. Například &lt;vaší organizace > CSPAdmin s předplatným Azure CSP, který je s ním spojená.|
|Můžete **nepřímé prodejce CSP**, a budou fungovat Azure stacku|Požádejte vašeho nepřímý poskytovatel CSP k vytvoření tenanta Azure AD vaší organizace s předplatným Azure CSP spojené s jeho pomocí partnerského centra.|

### <a name="capacity-based-billing"></a>Kapacity na základě fakturace
Pokud se rozhodnete použít kapacitního modelu fakturace, je nutné zakoupit Azure Stack kapacity plánu SKU na základě kapacity vašeho systému. Je potřeba vědět, počet fyzických jader ve vaší službě Azure Stack si správné množství. 

Smlouvy Enterprise (EA) vyžaduje kapacity fakturace předplatného Azure k registraci. Důvodem je, že registrace nastaví dostupnost položky na webu Marketplace, které musíte mít předplatné Azure. Předplatné se nepoužívá pro používání služby Azure Stack.

## <a name="learn-more"></a>Další informace
- Informace o případy použití, nákup, partnery a dodavateli hardwaru OEM, najdete v článku [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) stránce produktu.
- Informace o plánu a geografické dostupnosti pro službu Azure Stack integrované systémy, najdete v dokumentu white paper: [Azure Stack: rozšíření Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Další informace o Microsoft Azure Stack balení a ceny [stáhnout PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Další postup
[Integrace sítě datového centra](azure-stack-network.md)