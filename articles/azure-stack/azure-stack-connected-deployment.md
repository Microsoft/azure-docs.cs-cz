---
title: Rozhodnutí o Azure připojené nasazení pro Azure zásobníku integrované systémy | Microsoft Docs
description: Určení plánování rozhodnutí pro nasazení na víc uzlů připojená k Azure zásobník Azure nasazení.
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
ms.date: 02/06/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: def9d5381144026b5ad0e8a076edd3c0692a08f4
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
ms.locfileid: "29120382"
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Plánování rozhodnutí pro Azure zásobník Azure připojené nasazení integrované systémy
Poté, co jste se rozhodli [jak bude zásobník Azure integrovat do cloudového prostředí hybridní](azure-stack-connection-models.md), pak můžete dokončit svoje rozhodnutí o nasazení Azure zásobníku.

Nasazení Azure zásobníku připojení k Azure znamená, že pro vaše úložiště identit může mít Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS). Můžete také z buď fakturační model: platím jako--používání nebo na základě kapacity. Připojené nasazení je výchozí možnost, protože umožňuje zákazníkům získat nejvíce hodnotu mimo Azure zásobníku, zejména pro hybridní cloudové scénáře, které zahrnují Azure a Azure zásobníku. 

## <a name="choose-an-identity-store"></a>Vyberte úložiště identit
Připojené u nasazení můžete zvolit Azure AD ani AD FS pro vaše úložiště identit. Odpojené nasazení, bez připojení k Internetu, lze použít pouze služby AD FS.

Svou volbu úložiště identity nemá žádný vliv na klientské virtuální počítače (VM). Virtuální počítače klienta se rozhodnout, jaké úložiště identit se chcete připojit k v závislosti na tom, jak bude nakonfigurován: Azure AD, pracovní skupiny připojený k doméně, Windows Server Active Directory atd. Toto je vztah k rozhodnutí zprostředkovatele identity Azure zásobníku. 

Například pokud nasazení IaaS klientské virtuální počítače nad zásobník Azure a mají připojení k podnikové doméně Active Directory a používat účty z ní, můžete stále provedete. Není nutné používat úložiště identit Azure AD, které zde vyberete pro tyto účty.

### <a name="azure-ad-identity-store"></a>Úložiště identit Azure AD
Pokud používáte Azure AD pro úložiště vaší identity vyžaduje dva účty Azure AD: účet globálního správce a účet fakturace. Stejné účty nebo účty, které mohou být tyto účty. Pomocí stejného uživatelského účtu může být užitečné, pokud máte omezený počet účtů Azure a jednodušší, by mohla naznačovat obchodních potřeb pomocí dva účty:

1. **Účet globálního správce** (vyžadováno pouze u připojené nasazení). Toto je účet Azure, který slouží k vytváření aplikací a objekty služby pro služby infrastruktury Azure zásobníku v Azure Active Directory. Tento účet musí mít oprávnění správce adresáře do adresáře, který nasadí se váš systém zásobník Azure v části. Operátor"cloud" se stane globálního správce pro Azure AD klienta a bude použit: 
    - Zřídit a delegování aplikací a objekty služby pro všechny služby Azure zásobníku, kteří potřebují pracovat s Azure Active Directory a rozhraní Graph API. 
    - Jako účet správce služby. Toto je vlastník předplatného výchozí poskytovatele (který můžete později změnit). Může přihlásit k portálu pro správu Azure zásobníku s tímto účtem a můžete ji použít k vytvoření nabídky a plány, nastavit kvóty a provádění dalších funkcí pro správu v zásobníku Azure.
2. **Fakturace účtu** (povinné pro obě připojení a odpojení nasazení). Tento účet Azure slouží k vytvoření fakturační vztah mezi systému Azure zásobníku integrované a back-end Azure commerce. Toto je účet, který bude platit pro Azure zásobníku poplatky. Tento účet se použije i pro marketplace syndikace a dalších hybridní scénáře. 

### <a name="ad-fs-identity-store"></a>Úložiště identit služby AD FS
Tuto možnost zvolte, pokud chcete použít vlastní identity úložiště, jako je vaše podnikové služby Active Directory, pro účty správce služby.  

## <a name="choose-a-billing-model"></a>Vyberte fakturační model
Můžete buď **platím jako--používání** nebo **kapacity** model fakturace. Platím jako--používání fakturační model nasazení musí být schopen sestavy využití prostřednictvím připojení k Azure alespoň jednou za 30 dní. Proto fakturační model platím jako--používání je dostupná jenom pro připojené nasazení.  

### <a name="pay-as-you-use"></a>Platím jako jste – použití
Využití je s fakturační model platím jako--používání účtovat k předplatnému Azure. Platíte jenom při použití služby Azure zásobníku. Pokud je model rozhodnete, budete potřebovat předplatné Azure a přidružené k tomuto předplatnému ID účtu (například serviceadmin@contoso.onmicrosoft.com). Jsou podporovány EA, CSP a CSL odběry. Využití sestav se konfiguruje během [registrace Azure zásobníku](azure-stack-registration.md).

> [!NOTE]
> Ve většině případů podnikoví zákazníci použije EA odběry a poskytovatelé služeb bude používat zprostředkovatele kryptografických služeb nebo CSL odběry.

Pokud se chystáte použít předplatného poskytovatele CSP, naleznete v tabulce níže k identifikaci jaké CSP předplatné použít, protože správný přístup závisí na přesný scénář CSP:

|Scénář|Možnosti domény a předplatného|
|-----|-----|
|Jste **přímé partnera CSP** nebo **nepřímých poskytovatele CSP**, a bude fungovat v Azure zásobníku|Použití odběru CSL (běžné vrstvu služby).<br>     nebo<br>Vytvoření klienta Azure AD s popisný název v partnerské centrum. Například &lt;vaší organizace > CSPAdmin s předplatným Azure CSP s ním spojená.|
|Můžete **nepřímých CSP prodejce**, a bude fungovat v Azure zásobníku|Požádejte svého nepřímých poskytovatele CSP vytvoření klienta Azure AD pro vaši organizaci s předplatným Azure CSP přidružené pomocí Partnerské centrum.|

### <a name="capacity-based-billing"></a>Kapacity na základě fakturace
Pokud se rozhodnete použít model fakturace kapacitu, musíte koupit Azure zásobníku kapacity plánování SKU na základě kapacity vašeho systému. Musíte vědět, počet fyzických jader v zásobníku vaší Azure koupit správné množství. 

Enterprise Agreement (EA) vyžaduje kapacity fakturace předplatného Azure pro registraci. Důvodem je, že registrace nastaví syndikace, který vyžaduje předplatné Azure. Předplatné se nepoužije pro použití Azure zásobníku.

## <a name="learn-more"></a>Další informace
- Informace o případy použití, nákup, partneři a OEM výrobci hardwaru najdete v tématu [zásobník Azure](https://azure.microsoft.com/overview/azure-stack/) stránky produktu.
- Informace o plán a geografická dostupnosti pro zásobník Azure integrované systémy, najdete v dokumentu white paper: [Azure zásobník: rozšíření Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Další informace o Microsoft Azure zásobníku balení a ceny [stáhnout .pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Další postup
[Integrace sítě datového centra](azure-stack-network.md)