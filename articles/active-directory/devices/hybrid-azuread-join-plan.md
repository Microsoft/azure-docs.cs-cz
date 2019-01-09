---
title: Jak naplánovat implementaci připojení k hybridní službě Azure Active Directory v Azure Active Directory (Azure AD) | Dokumentace Microsoftu
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
ms.date: 01/08/2019
ms.author: markvi
ms.reviewer: sandeo
ms.openlocfilehash: bddd183c517c611373afd1df64f22bfcd6a0cea8
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54102274"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Jak: Naplánování vaší implementace připojení k hybridní službě Azure Active Directory

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

Pokud připojená k vaší doméně Windows 10 už zařízení [registrováno v Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/devices/overview#azure-ad-registered-devices) do svého tenanta, měli byste zvážit odebrání tohoto stavu před povolením připojení k hybridní službě Azure AD. Duální stav zařízení na obě, připojení k hybridní službě Azure AD a Azure AD registrované se nepodporuje. Od verze Windows 10 1809 se provedly následující změny, aby tento duální stav: 
 - Všechny stávající stav registrováno v Azure AD by být automaticky odstraněna po zařízení je připojená k hybridní Azure AD. 
 - Vám může zakázat zařízení připojené k vaší doméně Azure AD registrované přidáním tohoto klíče registru - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin" = dword: 00000001


## <a name="review-how-to-control-the-hybrid-azure-ad-join-of-your-devices"></a>Přečtěte si, jak řídit připojení k hybridní službě Azure AD. zařízení

Připojení k hybridní službě Azure AD je proces, na zařízení připojeném k doméně místní zařízení automaticky zaregistrovalo s Azure AD. Existují případy, kdy nechcete, aby všechna svá zařízení zaregistrovat automaticky. Toto je příklad hodnotu true, během počáteční uvedení, chcete-li ověřit, že vše funguje podle očekávání.

Další informace najdete v tématu [řízení připojení k hybridní službě Azure AD. zařízení](hybrid-azuread-join-control.md)

## <a name="select-your-scenario"></a>Vyberte váš scénář

Můžete nakonfigurovat připojení k hybridní službě Azure AD službě v následujících scénářích:

- Spravované domény
- Federované domény  



Pokud vaše prostředí obsahuje spravované domény, podporuje připojení k hybridní službě Azure AD:

- Ověření (PTA) průchodu

- Synchronizace hodnot Hash hesel (PHS)

Azure AD Connect od verze 1.1.819.0 nabízí průvodce konfigurací hybridního připojení k Azure AD. Tento průvodce vám umožní výrazně zjednodušit proces konfigurace. Další informace naleznete v tématu:

- [Konfigurace hybridního připojení Azure Active Directory pro federované domény](hybrid-azuread-join-federated-domains.md)


- [Konfigurace hybridního připojení Azure Active Directory pro spravované domény](hybrid-azuread-join-managed-domains.md)


 Pokud instalaci požadované verze služby Azure AD Connect není pro vás, přečtěte si téma [postup při ruční konfiguraci registrace zařízení](../device-management-hybrid-azuread-joined-devices-setup.md). 


## <a name="alternate-login-id-support-in-hybrid-azure-ad-join"></a>Podpora alternativní přihlašovací ID v připojení k hybridní službě Azure AD

Připojení k Windows 10 hybridní Azure AD poskytuje omezenou podporu pro [alternativní ID přihlášení](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) na základě typu alternativního přihlašovacího ID [metodu ověřování](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), typ domény a verzi Windows 10. Existují dva druhy alternativního přihlašovacího ID, která může existovat ve vašem prostředí:

 - Směrovatelné alternativního přihlašovacího ID: Směrovatelné alternativního přihlašovacího ID má platný ověřenou doménu, který je zaregistrovaný u registrátora domény. Například pokud primární doména je contoso.com contoso.org a contoso.co.uk jsou platné domény, které jsou majetkem společnosti Contoso a [ověřit ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)
 
 - Non směrovatelné alternativního přihlašovacího ID: Nesměrovatelných alternativního přihlašovacího ID nemá žádné ověřené doméně. To platí pouze v privátní síti vaší organizace. Například pokud primární doména je contoso.com contoso.local není ověřitelný doménu v Internetu ale se používá v rámci sítě společnosti Contoso.
 
Následující tabulka obsahuje podrobnosti o podpoře pro některý z těchto alternativních přihlašovacích ID v připojení k hybridní službě Azure AD Windows 10

|Typ alternativního přihlašovacího ID|Typ domény|Verze Windows 10|Popis|
|-----|-----|-----|-----|
|Směrovatelné|Federovaná |Od verze 1703|Obecně dostupná|
|Směrovatelné|Spravované|Od verze 1709|Aktuálně ve verzi private preview. Samoobslužné resetování HESLA Azure AD se nepodporuje. |
|Non směrovatelné|Federovaná|Od verze 1803|Obecně dostupná|
|Non směrovatelné|Spravované|Nepodporuje se||



## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Konfigurace připojení k hybridní službě Azure Active Directory službě u federovaných domén](hybrid-azuread-join-federated-domains.md)
> [konfigurovat připojení k hybridní službě Azure Active Directory službě pro spravované domény](hybrid-azuread-join-managed-domains.md)




<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
