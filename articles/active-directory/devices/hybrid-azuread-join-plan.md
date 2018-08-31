---
title: Jak nakonfigurovat hybridní služby Azure Active Directory zařízení připojená k | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat zařízení Azure Active Directory připojená k hybridní.
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
ms.date: 07/31/2018
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: 12d3b358be8bb90b63e5e7310123f8ae7093994c
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190268"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Jak naplánovat vaši implementaci připojení k hybridní službě Azure Active Directory

Podobným způsobem pro uživatele zařízení se mění jiné identity, které chcete chránit a také použít k ochraně vašich prostředků na libovolný čas a umístění. Dosažení tohoto cíle tak, že vaše zařízení identit do služby Azure AD pomocí jedné z následujících metod:

- Připojení k Azure AD
- Připojení k hybridní službě Azure AD
- Registrace Azure AD

Přeneste zařízení do služby Azure AD, maximalizovat produktivitu vašich uživatelů prostřednictvím jednotného přihlašování (SSO) ve vašich cloudových a místních prostředků. Ve stejnou dobu, můžete zabezpečit přístup do cloudu a místních prostředků pomocí [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md).

Pokud máte v místním prostředí služby Active Directory a chcete připojovat zařízení připojených k doméně do Azure AD, můžete to provést pomocí konfigurace hybridních zařízení připojených k Azure AD. Tento článek poskytuje vám související postup implementace hybridní služby Azure AD join ve vašem prostředí. 


## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte zkušenosti s [Úvod ke správě zařízení ve službě Azure Active Directory](../device-management-introduction.md).


## <a name="plan-your-implementation"></a>Plánování implementace

K naplánování vaší implementace hybridní služby Azure AD, které byste se seznámit s:

|   |   |
|---|---|
|![Zaškrtnout][1]|Zkontrolujte podporované zařízení|
|![Zaškrtnout][1]|Kontrola věcí, které byste měli vědět|
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

Registrace zařízení Windows nižší úrovně se nepodporuje na zařízeních nakonfigurovat pro cestovní profil uživatele nebo roamingem. Pokud se spoléháte na cestovní profily nebo nastavení, používají systém Windows 10.

- Registrace zařízení Windows nižší úrovně **je** podporována v jiné než federované prostředí pomocí bezproblémového jednotného přihlašování [Azure Active Directory bezproblémové jednotné přihlašování](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start). 
 
- Registrace zařízení Windows nižší úrovně **není** podporováno při použití předávacího ověřování Azure AD bez bezproblémové jednotné přihlašování.

- Registrace zařízení Windows nižší úrovně **není** podporovaná pro zařízení s použitím profilů roamingu. Pokud se spoléháte na cestovní profily nebo nastavení, používají systém Windows 10.


Registrace Windows Server se spuštěnou rolí řadiče domény (DC) se nepodporuje.

Pokud vaše organizace potřebuje přístup k Internetu prostřednictvím ověřeného odchozího proxy serveru, musí Ujistěte se, že vaše počítače s Windows 10 můžete provádět ověření odchozího proxy serveru. Protože počítače s Windows 10 spusťte registraci zařízení pomocí místní počítač, je nutné nakonfigurovat odchozího proxy serveru ověřování pomocí místní počítač.


Připojení k hybridní službě Azure AD je proces, na zařízení připojeném k doméně místní zařízení automaticky zaregistrovalo s Azure AD. Existují případy, kdy nechcete, aby všechna svá zařízení zaregistrovat automaticky. Pokud je to pro vás platí, přečtěte si téma [řízení připojení k hybridní službě Azure AD. zařízení](hybrid-azuread-join-control.md).



## <a name="select-your-scenario"></a>Vyberte váš scénář

Můžete nakonfigurovat připojení k hybridní službě Azure AD službě v následujících scénářích:

- Spravované domény
- Federované domény  



Pokud vaše prostředí obsahuje spravované domény, podporuje připojení k hybridní službě Azure AD:

- Předat prostřednictvím ověřování (PTA) pomocí bezproblémového jednotného přihlašování (SSO) 

- Synchronizace hodnot Hash hesel (PHS) pomocí bezproblémového jednotného přihlašování (SSO) 

Počínaje verzí 1.1.819.0, Azure AD Connect poskytuje průvodce ke konfiguraci připojení k hybridní službě Azure AD. Průvodce umožňuje výrazně zjednodušuje proces konfigurace. Další informace naleznete v tématu:

- [Konfigurace hybridního připojení Azure Active Directory pro federované domény](hybrid-azuread-join-federated-domains.md)


- [Konfigurace hybridního připojení Azure Active Directory pro spravované domény](hybrid-azuread-join-managed-domains.md)


 Pokud instalaci požadované verze služby Azure AD Connect není pro vás, přečtěte si téma [postup při ruční konfiguraci registrace zařízení](../device-management-hybrid-azuread-joined-devices-setup.md). 






## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Konfigurace připojení k hybridní službě Azure Active Directory službě u federovaných domén](hybrid-azuread-join-federated-domains.md)
> [konfigurovat připojení k hybridní službě Azure Active Directory službě pro spravované domény](hybrid-azuread-join-managed-domains.md)




<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
