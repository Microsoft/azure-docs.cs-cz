---
title: Postup plánování implementace hybridního Azure Active Directory JOIN v Azure Active Directory (Azure AD) | Microsoft Docs
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
ms.openlocfilehash: 53900bcade3f7a283309f5ee1c624c742a62ad3f
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70812452"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Jak: Plánování implementace služby Hybrid Azure Active Directory JOIN

Podobně jako uživatel je zařízení další základní identitou, kterou chcete chránit, a používejte ji k ochraně svých prostředků kdykoli a z libovolného místa. Tento cíl můžete dosáhnout vytvořením a správou identit zařízení ve službě Azure AD pomocí jedné z následujících metod:

- Připojení k Azure AD
- Hybridní připojení k Azure AD
- Registrace v Azure AD

Přenosem zařízení do Azure AD maximalizujete produktivitu uživatelů díky jednotnému přihlašování ke cloudovým i místním prostředkům. V současné době můžete zabezpečený přístup k vašim cloudovým a místním prostředkům zabezpečit pomocí [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md).

Pokud máte místní prostředí Active Directory (AD) a chcete se připojit k počítačům připojeným k doméně AD do služby Azure AD, můžete to provést pomocí hybridního připojení k Azure AD. Tento článek poskytuje související kroky pro implementaci hybridního připojení k Azure AD ve vašem prostředí. 

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že jste obeznámeni se [Seznámkou se správou identit zařízení v Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> Minimální požadovaná verze řadiče domény pro připojení k hybridní službě Azure AD ve Windows 10 je Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Plánování implementace

K naplánování vaší hybridní implementace služby Azure AD byste se měli seznámit s těmito kroky:

|   |   |
| --- | --- |
| ![Kontrola][1] | Zkontrolovat podporovaná zařízení |
| ![Kontrola][1] | Projděte si věci, které byste měli znát. |
| ![Kontrola][1] | Kontrola řízeného ověřování pro připojení k hybridní službě Azure AD |
| ![Kontrola][1] | Vyberte svůj scénář na základě vaší infrastruktury identity |
| ![Kontrola][1] | Kontrola místní podpory služby AD hlavního názvu uživatele (UPN) pro připojení k hybridní službě Azure AD |

## <a name="review-supported-devices"></a>Zkontrolovat podporovaná zařízení

Připojení k hybridní službě Azure AD podporuje širokou škálu zařízení s Windows. Vzhledem k tomu, že konfigurace pro zařízení s staršími verzemi Windows vyžaduje další nebo různé kroky, jsou podporovaná zařízení seskupená do dvou kategorií:

### <a name="windows-current-devices"></a>Aktuální zařízení s Windows

- Windows 10
- Windows Server 2016
- Windows Server. 2019

V případě zařízení s desktopovým operačním systémem Windows je podporovaná verze uvedená v tomto článku [informace o verzi Windows 10](https://docs.microsoft.com/windows/release-information/). Osvědčeným postupem je, že Microsoft doporučuje upgradovat na nejnovější verzi Windows 10.

### <a name="windows-down-level-devices"></a>Zařízení se škálováním na nižší úrovni Windows

- Windows 8.1
- Windows 7. Informace o podpoře ve Windows 7 najdete v článku [Podpora pro Windows 7](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Informace o podpoře na Windows serveru 2008 a 2008 R2 najdete v článku [Příprava na Windows server 2008 na konci podpory](https://www.microsoft.com/cloud-platform/windows-server-2008).

Jako první krok plánování byste měli zkontrolovat prostředí a určit, jestli potřebujete podporovat zařízení se systémem Windows nižší úrovně.

## <a name="review-things-you-should-know"></a>Projděte si věci, které byste měli znát.

Služba připojení k hybridní službě Azure AD se v současné době nepodporuje, pokud vaše prostředí obsahuje jednu doménovou strukturu služby AD synchronizující data identity do více než jednoho tenanta Azure AD.

Připojení k hybridní službě Azure AD se v současné době nepodporuje při použití infrastruktury virtuálních klientských počítačů (VDI).

Připojení k hybridní službě Azure AD se podporuje pro čip TPM kompatibilní se standardem FIPS 2,0 a nepodporuje se pro čip TPM 1,2. Pokud vaše zařízení mají čip TPM kompatibilní se standardem FIPS 1,2, musíte je před tím, než budete pokračovat s hybridním připojením k Azure AD, zakázat. Microsoft neposkytuje žádné nástroje pro zakázání režimu FIPS pro čipy TPM, protože je závislý na výrobci čipu TPM. Požádejte o podporu svého hardwarového výrobce OEM.

Služba připojení k hybridní službě Azure AD není podporovaná pro Windows Server, na kterém běží role řadič domény (DC).

Připojení k hybridní službě Azure AD není při použití roamingu přihlašovacích údajů nebo roamingu profilů uživatelů podporováno v zařízeních Windows nižší úrovně.

Pokud se spoléháte na nástroj pro přípravu systému (Sysprep) a pokud pro instalaci používáte image **starší než Windows 10 1809** , ujistěte se, že image není ze zařízení, které už je zaregistrované ve službě Azure AD, jako připojení k hybridní službě Azure AD.

Pokud při vytváření dalších virtuálních počítačů spoléháte na snímek virtuálního počítače, ujistěte se, že snímek není z virtuálního počítače, který je už zaregistrovaný ve službě Azure AD, jako připojení k hybridní službě Azure AD.

Pokud jsou vaše zařízení připojená k doméně Windows 10 [registrovaná](overview.md#getting-devices-in-azure-ad) ve vašem tenantovi, může to vést k duálnímu stavu připojení k hybridní službě Azure AD a k zaregistrovanému zařízení Azure AD. Pro automatické vyřešení tohoto scénáře doporučujeme upgradovat na Windows 10 1803 (s použitím KB4489894) nebo novějším. Ve verzích starších než 1803 budete muset před povolením hybridního připojení k Azure AD ručně odebrat stav registrovaný pro službu Azure AD. V 1803 a vyšších verzích byly provedeny následující změny, aby nedocházelo k tomuto duálnímu stavu:

- Veškerý stávající stav registrovaný v Azure AD by se <i>po připojení zařízení k hybridní službě Azure AD</i>automaticky odebral.
- Zařízení připojenému k doméně můžete zabránit v registraci Azure AD přidáním tohoto klíče registru – HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin" = DWORD: 00000001.
- Pokud máte v systému Windows 10 1803 nakonfigurovanou možnost Windows Hello pro firmy, uživatel musí po vyčištění duálního stavu znovu nastavit Windows Hello pro firmy. Tento problém se vyřešil pomocí KB4512509.



## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Kontrola řízeného ověřování pro připojení k hybridní službě Azure AD

Když jsou splněné všechny požadavky, zařízení s Windows se v tenantovi Azure AD automaticky registrují jako zařízení. Stav těchto identit zařízení ve službě Azure AD se označuje jako připojení k hybridní službě Azure AD. Další informace o konceptech popsaných v tomto článku najdete v článcích [Seznámení se správou identit zařízení v Azure Active Directory](overview.md) a [Naplánování implementace Hybrid Azure Active Directory JOIN](hybrid-azuread-join-plan.md).

Organizace můžou chtít provést řízené ověřování pro připojení k hybridní službě Azure AD, a to ještě před tím, než je zapnete v celé organizaci najednou. Přečtěte si článek [řízený ověření hybridního připojení ke službě Azure AD](hybrid-azuread-join-control.md) , abyste pochopili, jak to provést.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Vyberte svůj scénář na základě vaší infrastruktury identity

Připojení k hybridní službě Azure AD funguje s oběma spravovanými i federovaným prostředími v závislosti na tom, jestli je hlavní název uživatele směrovatelný nebo není směrovatelný. V podporovaných scénářích se podívejte na dolní část stránky pro tabulku.  

### <a name="managed-environment"></a>Spravované prostředí

Spravované prostředí se dá nasadit buď pomocí [synchronizace hodnot hash hesel (kosmetice)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) , nebo [předávat ověřování (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) pomocí [bezproblémového jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).

Tyto scénáře nevyžadují konfiguraci federačního serveru pro ověřování.

### <a name="federated-environment"></a>Federované prostředí

Federované prostředí by mělo mít poskytovatele identity, který podporuje následující požadavky. Pokud máte federované prostředí pomocí Active Directory Federation Services (AD FS) (AD FS), jsou již podporovány níže uvedené požadavky.

- **WIAORMULTIAUTHN deklarace identity:** Tato deklarace identity se vyžaduje k tomu, aby se pro zařízení se systémem Windows na nižší úrovni mohla připojit hybridní služba Azure AD.
- **Protokol WS-Trust:** Tento protokol je nutný k ověření současných zařízení s Windows připojených k hybridní službě Azure AD pomocí Azure AD. Pokud používáte AD FS, je nutné povolit následující koncové body WS-Trust:`/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **AD FS/Services/Trust/2005/windowstransport** , **AD FS/Services/Trust/13/windowstransport** by měly být povolené jenom jako intranetové koncové body a nesmí být zveřejněné jako extranetové koncové body prostřednictvím proxy webových aplikací. Další informace o tom, jak zakázat koncové body systému Windows WS-Trust, najdete v tématu [zakázání koncových bodů systému Windows WS-Trust na proxy serveru](https://docs.microsoft.com/en-us/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Pomocí konzoly pro správu AD FS v části**koncové body** **služby** > můžete zjistit, jaké koncové body jsou povolené.

> [!NOTE]
> Azure AD nepodporuje čipové karty ani certifikáty ve spravovaných doménách.

Azure AD Connect od verze 1.1.819.0 nabízí průvodce konfigurací hybridního připojení k Azure AD. Tento průvodce vám umožní výrazně zjednodušit proces konfigurace. Pokud se nejedná o možnost instalace požadované verze Azure AD Connect, přečtěte si téma [Postup ruční konfigurace registrace zařízení](hybrid-azuread-join-manual.md). 

V závislosti na scénáři, který odpovídá vaší infrastruktuře identity, se podívejte na:

- [Konfigurace služby Hybrid Azure Active Directory JOIN pro federované prostředí](hybrid-azuread-join-federated-domains.md)
- [Konfigurace hybridního Azure Active Directory JOIN pro spravované prostředí](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Kontrola místní podpory služby AD hlavního názvu uživatele (UPN) pro připojení k hybridní službě Azure AD

Někdy se může stát, že místní hlavní názvy služby AD UPN se liší od vašich místních názvů UPN služby Azure AD. V takových případech služba Windows 10 Hybrid Azure AD JOIN nabízí omezené podpory místních UPN služby AD na základě [metody ověřování](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn), typu domény a verze Windows 10. Existují dva typy místních UPN služby AD, které můžou existovat ve vašem prostředí:

- Směrovatelný hlavní název uživatele: Směrovatelný hlavní název uživatele má platnou ověřenou doménu, která je zaregistrovaná u doménového registrátora. Pokud je například contoso.com primární doménou v Azure AD, contoso.org je primární doména v místní službě AD vlastněná společností Contoso a [ověřená v Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain) .
- Hlavní název uživatele (UPN), který není směrovatelný: Hlavní název uživatele (UPN), který není směrovatelný, nemá ověřenou doménu. Dá se použít jenom v privátní síti vaší organizace. Pokud je například contoso.com primární doménou v Azure AD, contoso. Local je primární doména v místní službě AD, ale nejedná se o ověřitelných doménách v Internetu a používá se jenom v síti Contoso.

V následující tabulce najdete podrobné informace o podpoře místních UPN služby AD ve Windows 10 – připojení k hybridní službě Azure AD.

| Typ místního hlavního názvu uživatele služby AD | Typ domény | Verze Windows 10 | Popis |
| ----- | ----- | ----- | ----- |
| Balíček | Federovaná | Z verze 1703 | Obecně dostupná |
| Bez směrování | Federovaná | Z verze 1803 | Obecně dostupná |
| Balíček | Spravovaní | Z verze 1803 | Všeobecně dostupná služba Azure AD SSPR ve Windows zamykací obrazovky není podporovaná. |
| Bez směrování | Spravovaní | Nepodporuje se | |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Konfigurace hybridního Azure Active Directory JOIN pro federované prostředí](hybrid-azuread-join-federated-domains.md)
> [Konfigurace připojení Hybrid Azure Active Directory JOIN pro spravované prostředí](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
