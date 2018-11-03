---
title: Postup konfigurace hybridních zařízení připojených k Azure Active Directory | Microsoft Docs
description: Zjistěte, jak nakonfigurovat hybridní zařízení připojená k Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: ebf5a23743d1fdd9553b391bb0518c2887ddb096
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50959983"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Jak naplánovat vaši implementaci připojení k hybridní službě Azure Active Directory

Zařízení se podobně jako uživatel stávají další identitou, kterou chcete chránit a pomocí které zároveň chcete nepřetržitě chránit své prostředky, ať jsou kdekoli. Můžete toho docílit tím, že pomocí některé z následujících metod přenesete identity vašich zařízení do Azure AD:

- Připojení k Azure AD
- Hybridní připojení k Azure AD
- Registrace v Azure AD

Přenosem zařízení do Azure AD maximalizujete produktivitu uživatelů díky jednotnému přihlašování ke cloudovým i místním prostředkům. Současně můžete zabezpečit přístup k vašim cloudovým i místním prostředkům s využitím [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md).

Pokud máte místní prostředí Active Directory a chcete připojit svá zařízení připojená k doméně k Azure AD, můžete to provést tak, že nakonfigurujete hybridní zařízení připojená k Azure AD. Tento článek poskytuje vám související postup implementace hybridní služby Azure AD join ve vašem prostředí. 


## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte zkušenosti s [Úvod ke správě zařízení ve službě Azure Active Directory](../device-management-introduction.md).


## <a name="plan-your-implementation"></a>Plánování implementace

K naplánování vaší implementace hybridní služby Azure AD, které byste se seznámit s:

|   |   |
|---|---|
|![Zaškrtnout][1]|Zkontrolujte podporované zařízení|
|![Zaškrtnout][1]|Kontrola věcí, které byste měli vědět|
|![Zaškrtnout][1]|Přečtěte si, jak řídit připojení k hybridní službě Azure AD. zařízení|
|![Zaškrtnout][1]|Vyberte váš scénář|


 


## <a name="review-supported-devices"></a>Zkontrolujte podporované zařízení 

Připojení k hybridní službě Azure AD podporuje široký rozsah Windows zařízení. Vzhledem k tomu, že konfigurace pro zařízení se staršími verzemi Windows vyžaduje další nebo odlišné kroky, podporovaných zařízení jsou seskupeny do dvou kategorií:

**Aktuální zařízení Windows**

- Windows 10
    
- Windows Server 2016


Pro zařízení s operačním systémem klasické pracovní plochy Windows, je podporovaná verze Windows 10 Anniversary Update (verze 1607) nebo novější. Jako osvědčený postup upgradujte na nejnovější verzi Windows 10.



 **Zařízení Windows nižší úrovně**

- Windows 8.1
 
- Windows 7

- Windows Server 2012 R2
 
- Windows Server 2012 
 
- Windows Server 2008 R2 


Jako první krok plánování zkontrolujte vaše prostředí a určit, jestli je potřeba podporovat zařízení Windows nižší úrovně.



## <a name="review-things-you-should-know"></a>Kontrola věcí, které byste měli vědět

Hybridní Azure AD join nelze použít, pokud vaše prostředí se skládá z jedné doménové struktury, kterou synchronizovat data identit do více než jednoho tenanta Azure AD.

Pokud se spoléháte na nástroj System Preparation (Sysprep), ujistěte se, že vytvoříte Image z instalace systému Windows, který nebyl nakonfigurován pro připojení k hybridní službě Azure AD.

Pokud se spoléháte na snímek virtuálního počítače (VM) můžete vytvořit další virtuální počítače, ujistěte se, že použití snímku virtuálního počítače, který není nakonfigurovaná pro připojení k hybridní službě Azure AD.

Připojení k hybridní službě Azure AD. zařízení Windows nižší úrovně:

- **Je** podporována v jiné než federované prostředí prostřednictvím [Azure Active Directory bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start). 

- **Není** podporováno při použití předávacího ověřování Azure AD bez bezproblémové jednotné přihlašování.

- **Není** podporována při použití přihlašovacích údajů roaming nebo profil uživatele roamingu, nebo při používání infrastruktury virtuálních klientských (počítačů VDI).


Registrace Windows Server se spuštěnou rolí řadiče domény (DC) se nepodporuje.

Pokud vaše organizace potřebuje přístup k internetu přes ověřený odchozí proxy server, je potřeba zajistit, aby se vaše počítače s Windows 10 mohly úspěšně ověřit u odchozího proxy serveru. Vzhledem k tomu, že se na počítačích s Windows 10 provádí registrace zařízení s využitím kontextu počítače, je potřeba nakonfigurovat ověřování odchozího proxy serveru s použitím kontextu počítače.


Připojení k hybridní službě Azure AD je proces, na zařízení připojeném k doméně místní zařízení automaticky zaregistrovalo s Azure AD. Existují případy, kdy nechcete, aby všechna svá zařízení zaregistrovat automaticky. Pokud je to pro vás platí, přečtěte si téma [řízení připojení k hybridní službě Azure AD. zařízení](hybrid-azuread-join-control.md).


## <a name="review-how-to-control-the-hybrid-azure-ad-join-of-your-devices"></a>Přečtěte si, jak řídit připojení k hybridní službě Azure AD. zařízení

Připojení k hybridní službě Azure AD je proces, na zařízení připojeném k doméně místní zařízení automaticky zaregistrovalo s Azure AD. Existují případy, kdy nechcete, aby všechna svá zařízení zaregistrovat automaticky. Toto je příklad hodnotu true, během počáteční uvedení, chcete-li ověřit, že vše funguje podle očekávání.

Další informace najdete v tématu [řízení připojení k hybridní službě Azure AD. zařízení](hybrid-azuread-join-control.md)

## <a name="select-your-scenario"></a>Vyberte váš scénář

Můžete nakonfigurovat připojení k hybridní službě Azure AD službě v následujících scénářích:

- Spravované domény
- Federované domény  



Pokud vaše prostředí obsahuje spravované domény, podporuje připojení k hybridní službě Azure AD:

- Předat prostřednictvím ověřování (PTA) pomocí bezproblémového jednotného přihlašování (SSO) 

- Synchronizace hodnot Hash hesel (PHS) pomocí bezproblémového jednotného přihlašování (SSO) 

Azure AD Connect od verze 1.1.819.0 nabízí průvodce konfigurací hybridního připojení k Azure AD. Tento průvodce vám umožní výrazně zjednodušit proces konfigurace. Další informace naleznete v tématu:

- [Konfigurace hybridního připojení Azure Active Directory pro federované domény](hybrid-azuread-join-federated-domains.md)


- [Konfigurace hybridního připojení Azure Active Directory pro spravované domény](hybrid-azuread-join-managed-domains.md)


 Pokud instalaci požadované verze služby Azure AD Connect není pro vás, přečtěte si téma [postup při ruční konfiguraci registrace zařízení](../device-management-hybrid-azuread-joined-devices-setup.md). 






## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Konfigurace připojení k hybridní službě Azure Active Directory službě u federovaných domén](hybrid-azuread-join-federated-domains.md)
> [konfigurovat připojení k hybridní službě Azure Active Directory službě pro spravované domény](hybrid-azuread-join-managed-domains.md)




<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
