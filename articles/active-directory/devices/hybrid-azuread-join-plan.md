---
title: Jak naplánovat implementaci připojení k hybridní službě Azure Active Directory v Azure Active Directory (Azure AD) | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat hybridní zařízení připojená k Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: c476c2b326045db37c54a358d68f4b5f8bbaed9a
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509603"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Jak: Naplánování vaší implementace připojení k hybridní službě Azure Active Directory

Podobným způsobem pro uživatele zařízení je jiné identity jádro, které chcete chránit a použít ji k ochraně vašich prostředků, kdykoli a odkudkoli. Dosažení tohoto cíle přináší a správou identit zařízení ve službě Azure AD pomocí jedné z následujících metod:

- Připojení k Azure AD
- Hybridní připojení k Azure AD
- Registrace v Azure AD

Přenosem zařízení do Azure AD maximalizujete produktivitu uživatelů díky jednotnému přihlašování ke cloudovým i místním prostředkům. Ve stejnou dobu, můžete zabezpečit přístup do cloudu a místních prostředků pomocí [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md).

Pokud máte v místním prostředí Active Directory (AD) a připojte se k počítače připojené k doméně AD do služby Azure AD, můžete to provést sytém díky připojení k hybridní službě Azure AD. Tento článek poskytuje vám související postup implementace hybridní služby Azure AD join ve vašem prostředí. 

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že máte zkušenosti s [seznámení se správou identit zařízení ve službě Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> Minimální požadovaná funkčnosti domény a funkční úrovně doménové struktury pro připojení k hybridní službě Azure AD Windows 10 je Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Plánování implementace

K naplánování vaší implementace hybridní služby Azure AD, které byste se seznámit s:

|   |   |
| --- | --- |
| ![Kontrola][1] | Zkontrolujte podporované zařízení |
| ![Kontrola][1] | Kontrola věcí, které byste měli vědět |
| ![Kontrola][1] | Zkontrolujte řízené ověřování připojení k hybridní službě Azure AD |
| ![Kontrola][1] | Vyberte váš scénář podle vaší infrastruktury identit |
| ![Kontrola][1] | Zkontrolujte místní hlavní název uživatele AD podporu pro hybridní připojení ke službě Azure AD |

## <a name="review-supported-devices"></a>Zkontrolujte podporované zařízení

Připojení k hybridní službě Azure AD podporuje široký rozsah Windows zařízení. Vzhledem k tomu, že konfigurace pro zařízení se staršími verzemi Windows vyžaduje další nebo odlišné kroky, podporovaných zařízení jsou seskupeny do dvou kategorií:

### <a name="windows-current-devices"></a>Aktuální zařízení Windows

- Windows 10
- Windows Server 2016
- Windows Server 2019

Pro zařízení s operačním systémem klasické pracovní plochy Windows, podporované verze jsou uvedeny v tomto článku [informace o verzi Windows 10](https://docs.microsoft.com/windows/release-information/). Jako osvědčený postup Microsoft doporučuje že upgradovat na nejnovější verzi Windows 10.

### <a name="windows-down-level-devices"></a>Zařízení Windows nižší úrovně

- Windows 8.1
- Windows 7. Informace o podpoře na Windows 7, najdete v tomto článku [ukončení podpory pro Windows 7](https://www.microsoft.com/en-us/windowsforbusiness/end-of-windows-7-support)
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

Jako první krok plánování zkontrolujte vaše prostředí a určit, jestli je potřeba podporovat zařízení Windows nižší úrovně.

## <a name="review-things-you-should-know"></a>Kontrola věcí, které byste měli vědět

Připojení k hybridní službě Azure AD se aktuálně nepodporuje, pokud vaše prostředí se skládá z jedné doménové struktury AD synchronizaci dat identity do více než jednoho tenanta Azure AD.

Připojení k hybridní službě Azure AD není aktuálně podporováno při použití infrastruktury virtuálních klientských (počítačů VDI).

Připojení k hybridní službě Azure AD se nepodporuje pro čipy TPM kompatibilní se standardem FIPS. Pokud vaše zařízení kompatibilní se standardem FIPS čipy TPM, je nutné zakázat před pokračováním v připojení k hybridní službě Azure AD. Společnost Microsoft neposkytuje žádné nástroje pro zakázání režimu FIPS pro čipy TPM, protože je závislá na výrobce čipu TPM. Obraťte se prosím na hardware pro výrobce OEM pro podporu.

Připojení k hybridní službě Azure AD se nepodporuje pro Windows Server se spuštěnou rolí řadiče domény (DC).

Připojení k hybridní službě Azure AD se nepodporuje na zařízeních s Windows nižší úrovně, při použití přihlašovacích údajů roaming nebo cestovní profil uživatele.

Pokud se spoléháte na nástroj System Preparation (Sysprep), a pokud použijete **systémy starší než Windows 10 1809** bitovou kopii pro instalaci, ujistěte se, že bitová kopie není na zařízení, které je už zaregistrovaný službou Azure AD jako připojení k hybridní službě Azure AD.

Pokud se spoléháte na snímek virtuálního počítače (VM) můžete vytvořit další virtuální počítače, ujistěte se, že tomuto snímku není z virtuálního počítače, který je už zaregistrovaný službou Azure AD jako připojení k hybridní službě Azure AD.

Pokud připojená k vaší doméně Windows 10 už zařízení [registrováno v Azure AD](https://docs.microsoft.com/azure/active-directory/devices/overview#azure-ad-registered-devices) do svého tenanta, důrazně doporučujeme odebrat tento stav před povolením připojení k hybridní službě Azure AD. Od verze Windows 10 1809 se provedly následující změny, aby tento duální stav:

- Všechny stávající stav registrováno v Azure AD by být automaticky odstraněna po zařízení je připojená k hybridní Azure AD.
- Vám může zakázat zařízení připojené k vaší doméně Azure AD registrované přidáním tohoto klíče registru - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin" = dword: 00000001.
- Tato změna je teď k dispozici pro verzi Windows 10 1803 s KB4489894 použít. Ale pokud máte Windows Hello pro firmy nakonfigurovaná, uživatel je potřeba re nastavení Windows Hello pro firmy po dvou stavu vyčistit.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Zkontrolujte řízené ověřování připojení k hybridní službě Azure AD

Když jsou všechny požadavky na místě, se automaticky zaregistrují zařízení s Windows jako zařízení ve vašem tenantovi Azure AD. Stav těchto identit zařízení ve službě Azure AD se označuje jako připojení k hybridní službě Azure AD. Další informace o konceptech popsaná v tomto článku najdete v článcích [seznámení se správou identit zařízení ve službě Azure Active Directory](overview.md) a [plánování vašeho připojení k hybridní služby Azure Active Directory implementace](hybrid-azuread-join-plan.md).

Organizace může být vhodné provést řízené ověřování připojení k hybridní službě Azure AD, než povolíte napříč celou organizací všechny najednou. Přečtěte si článek [řídí ověřování připojení k hybridní službě Azure AD](hybrid-azuread-join-control.md) pochopit, jak toho dosáhnout.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Vyberte váš scénář podle vaší infrastruktury identit

Připojení k hybridní službě Azure AD funguje s prostředím, spravované i federované.  

### <a name="managed-environment"></a>Spravované prostředí

Spravované prostředí můžou být nasazené prostřednictvím [synchronizace hodnot Hash hesel (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) nebo [předat prostřednictvím ověřování (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) s [bezproblémového jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).

Tyto scénáře nevyžadují konfigurace federačního serveru pro ověřování.

### <a name="federated-environment"></a>Federovaném prostředí

Federovaném prostředí by měl mít zprostředkovatele identity, která podporuje následující požadavky:

- **Protokol WS-Trust:** Tento protokol je nezbytné k ověření Windows aktuální hybridní službě Azure AD připojené zařízení s Azure AD.
- **WIAORMULTIAUTHN deklarace identity:** Tato deklarace identity je potřeba udělat hybridní připojení ke službě Azure AD pro zařízení s Windows nižší úrovně.

Pokud máte prostředí federované pomocí služby Active Directory Federation Services (AD FS), pak výše uvedené požadavky jsou již podporují.

> [!NOTE]
> Azure AD nepodporuje čipové karty ani certifikáty ve spravovaných doménách.

Azure AD Connect od verze 1.1.819.0 nabízí průvodce konfigurací hybridního připojení k Azure AD. Tento průvodce vám umožní výrazně zjednodušit proces konfigurace. Pokud instalaci požadované verze služby Azure AD Connect není pro vás, přečtěte si téma [postup při ruční konfiguraci registrace zařízení](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual). 

Založený na scénáři, který odpovídá vaší infrastruktury identit, naleznete v tématu:

- [Konfigurace hybridních připojení k Azure Active Directory pro federovaném prostředí](hybrid-azuread-join-federated-domains.md)
- [Konfigurace hybridních připojení k Azure Active Directory pro spravované prostředí](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Zkontrolujte místní hlavní název uživatele AD podporu pro připojení k hybridní službě Azure AD

V některých případech místní AD UPN může být odlišné od UPN vaší Azure AD. V takovém případě připojení k hybridní službě systému Windows 10 Azure AD poskytuje omezenou podporu pro místní AD UPN na základě [metodu ověřování](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), typ domény a verzi Windows 10. Existují dva typy místní AD UPN, které mohou existovat ve vašem prostředí:

- Směrovatelné hlavní název uživatele: Směrovatelné hlavní název uživatele je platný ověřenou doménu, který je zaregistrovaný u registrátora domény. Například, pokud primární doménu contoso.com je ve službě Azure AD, contoso.org je primární doménu v místní AD vlastněných společností Contoso a [ověřit ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)
- Nesměrovatelných hlavní název uživatele: Nesměrovatelných hlavní název uživatele nemá žádné ověřené doméně. To platí pouze v privátní síti vaší organizace. Například, pokud primární doménu contoso.com je ve službě Azure AD, contoso.local je primární doménu v místní služby AD, ale není ověřitelný doménu v Internetu a síť pouze používané v rámci společnosti Contoso.

Následující tabulka obsahuje podrobnosti o podpoře pro tyto místní AD UPN v systému Windows 10 hybridní službě Azure AD join

| Typ on-premises hlavní název uživatele AD | Typ domény | Windows 10 version | Popis |
| ----- | ----- | ----- | ----- |
| Směrovatelné | Federované | Od verze 1703 | Obecně dostupná |
| Non směrovatelné | Federované | Od verze 1803 | Obecně dostupná |
| Směrovatelné | Spravovaní | Nepodporuje se | |
| Non směrovatelné | Spravovaní | Nepodporuje se | |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Konfigurace připojení k hybridní službě Azure Active Directory službě pro federované prostředí](hybrid-azuread-join-federated-domains.md)
> [konfigurovat připojení k hybridní službě Azure Active Directory službě pro spravované prostředí](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
