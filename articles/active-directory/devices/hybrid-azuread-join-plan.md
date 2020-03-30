---
title: Plánování hybridního připojení k službě Azure Active Directory – Služba Azure Active Directory
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
ms.openlocfilehash: 152ff52ce52b573d7f24cbb2fafc944b1794f6d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129261"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Postup: Plánování hybridní implementace připojení k hybridníslužbě Azure Active Directory

Podobně jako u uživatele je zařízení další základní identitou, kterou chcete chránit, a používat ji k ochraně vašich prostředků kdykoli a z libovolného místa. Tohoto cíle můžete dosáhnout tím, že ve službě Azure AD přinesete a spravujete identity zařízení pomocí jedné z následujících metod:

- Připojení k Azure AD
- Hybridní připojení k Azure AD
- Registrace v Azure AD

Přenosem zařízení do Azure AD maximalizujete produktivitu uživatelů díky jednotnému přihlašování ke cloudovým i místním prostředkům. Současně můžete zabezpečit přístup ke svým cloudovým a místním prostředkům pomocí [podmíněného přístupu](../active-directory-conditional-access-azure-portal.md).

Pokud máte místní prostředí služby Active Directory (AD) a chcete se připojit k počítačům doprostředkované službou AD k Azure AD, můžete toho dosáhnout hybridním připojením k Azure AD. Tento článek obsahuje související kroky k implementaci hybridní připojení Azure AD ve vašem prostředí. 

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá, že jste obeznámeni se [správou identit zařízení úvod do správy identit zařízení ve službě Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> Minimální požadovaná verze řadiče domény pro hybridní připojení Azure AD pro Windows 10 je Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Naplánujte si implementaci

Chcete-li naplánovat hybridní implementaci Azure AD, měli byste se seznámit s:

|   |   |
| --- | --- |
| ![Zaškrtnout][1] | Kontrola podporovaných zařízení |
| ![Zaškrtnout][1] | Zkontrolujte, co byste měli vědět |
| ![Zaškrtnout][1] | Kontrola řízeného ověření hybridního připojení Azure AD |
| ![Zaškrtnout][1] | Vyberte svůj scénář na základě infrastruktury identit |
| ![Zaškrtnout][1] | Kontrola místní podpory služby AD UPN pro hybridní připojení Azure AD |

## <a name="review-supported-devices"></a>Kontrola podporovaných zařízení

Hybridní připojení Azure AD podporuje širokou škálu zařízení s Windows. Vzhledem k tomu, že konfigurace pro zařízení se staršími verzemi systému Windows vyžaduje další nebo různé kroky, jsou podporovaná zařízení seskupena do dvou kategorií:

### <a name="windows-current-devices"></a>Aktuální zařízení systému Windows

- Windows 10
- Windows Server 2016
  - **Poznámka:** Zákazníci azure národního cloudu vyžadují verzi 1809
- Windows Server 2019

U zařízení s desktopovým operačním systémem Windows jsou v tomto článku [informace o verzi Windows 10](/windows/release-information/)uvedeny podporované verze. Jako osvědčený postup společnost Microsoft doporučuje upgradovat na nejnovější verzi systému Windows 10.

### <a name="windows-down-level-devices"></a>Zařízení windows nižší úrovně

- Windows 8.1
- Podpora windows 7 skončila 14. ledna 2020. Další informace naleznete v [tématu Podpora systému Windows 7 byla ukončena](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Informace o podpoře v systémech Windows Server 2008 a 2008 R2 naleznete v [tématu Příprava na ukončení podpory systému Windows Server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008).

Jako první krok plánování byste měli zkontrolovat své prostředí a zjistit, zda je třeba podporovat zařízení windows nižší úrovně.

## <a name="review-things-you-should-know"></a>Zkontrolujte, co byste měli vědět

### <a name="unsupported-scenarios"></a>Nepodporované scénáře
- Hybridní připojení Azure AD není aktuálně podporováno, pokud vaše prostředí se skládá z jedné doménové struktury služby AD, která synchronizuje data identity s více než jedním klientem Azure AD.

- Hybridní připojení azure ad není podporováno pro Windows Server s rolí řadiče domény (DC).

- Hybridní připojení Azure AD není podporováno na zařízeních nižší úrovně Windows při použití roamingu pověření nebo roamingu profilu uživatele nebo povinného profilu.

- Server Core OS nepodporuje žádný typ registrace zařízení.

### <a name="os-imaging-considerations"></a>Důležité informace o zobrazování operačního systému
- Pokud se spoléháte na nástroj pro přípravu systému (Sysprep) a pokud používáte **image před Windows 10 1809** pro instalaci, ujistěte se, že image není ze zařízení, které je již registrována s Azure AD jako hybridní připojení Azure AD.

- Pokud se spoléháte na snímek virtuálního počítače (VM) k vytvoření dalších virtuálních počítačů, ujistěte se, že snímek není z virtuálního počítače, který je již registrována s Azure AD jako hybridní připojení Azure AD.

- Pokud používáte [jednotný filtr zápisu](/windows-hardware/customize/enterprise/unified-write-filter) a podobné technologie, které vymažou změny na disku při restartování, musí být použity po připojení zařízení Hybridní Azure AD. Povolení těchto technologií před dokončením připojení hybridní azure ad bude mít za následek zařízení získání nepřipojeno při každém restartování

### <a name="handling-devices-with-azure-ad-registered-state"></a>Zpracování zařízení se stavem registrovaného pro Azure AD
Pokud jsou vaše zařízení připojená k doméně Windows 10 [registrovaná](overview.md#getting-devices-in-azure-ad) pro vašeho tenanta, může to vést ke dvojímu stavu hybridního Azure AD a registrovaného zařízení Azure AD. Doporučujeme upgradovat na Windows 10 1803 (s KB4489894 použít) nebo vyšší, aby se tento scénář automaticky řešit. V předběžných verzích 1803 budete muset odebrat stav registrovaného azure ad ručně před povolením hybridní ho připojení Azure AD. V roce 1803 a výše byly provedeny následující změny, aby se zabránilo tomuto dvojímu stavu:

- Všechny existující Azure AD registrovaný stav pro uživatele by se automaticky odebrat <i>poté, co je připojen hybridní Azure AD a stejný uživatel přihlásí</i>. Například pokud uživatel A měl azure ad registrovaný stav na zařízení, duální stav pro uživatele A je vyčištěna pouze v případě, že uživatel A přihlásí k zařízení. Pokud je na stejném zařízení více uživatelů, je při přihlášení těchto uživatelů dvoustavný dvoustavový stav jednotlivě vyčištěn.
- Přidáním tohoto klíče registru – HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, BlockAADWorkplaceJoin"=dword:000000001 můžete zabránit tomu, aby vaše zařízení připojeno k doméně bylo registrováno v Azure AD.
- Pokud máte ve Windows 10 1803 nakonfigurovaný Windows Hello pro firmy, musí uživatel po vyčištění duálního stavu windows Hello for Business přenastavit Windows Hello pro firmy. Tento problém byl vyřešen pomocí KB4512509

> [!NOTE]
> Registrované zařízení Azure AD se automaticky neodebere, pokud ho spravuje Intune.

### <a name="additional-considerations"></a>Další aspekty
- Pokud vaše prostředí používá infrastrukturu virtuálních ploch (VDI), přečtěte si informace [o virtualizaci identity zařízení a plochy](/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure).

- Hybridní připojení Azure AD je podporované pro čip TPM 2.0 kompatibilní s FIPS a není podporováno pro čip TPM 1.2. Pokud vaše zařízení mají Čip TPM 1.2 kompatibilní s FIPS, musíte je před pokračováním v hybridním připojení Azure AD zakázat. Společnost Microsoft neposkytuje žádné nástroje pro zakázání režimu FIPS pro tpms, protože je závislá na výrobci čipu TPM. Obraťte se na svého hardwaru OEM pro podporu. 

- Počínaje verzí Windows 10 1903 se tpms 1.2 nepoužívají s hybridním připojením Azure AD a zařízení s těmito tpms budou považovány, jako by neměly čip TPM.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Kontrola řízeného ověření hybridního připojení Azure AD

Když jsou všechny předpoklady na místě, zařízení s Windows se automaticky zaregistrují jako zařízení ve vašem tenantovi Azure AD. Stav těchto identit zařízení ve službě Azure AD se označuje jako hybridní připojení Azure AD. Další informace o konceptech uvedených v tomto článku najdete v článku [Úvod ke správě identit zařízení ve službě Azure Active Directory](overview.md).

Organizace mohou chtít provést řízené ověření hybridního připojení Azure AD před povolením v celé organizaci najednou. Projděte si článek [řízené ověření hybridní připojení Azure AD pochopit,](hybrid-azuread-join-control.md) jak to provést.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Vyberte svůj scénář na základě infrastruktury identit

Hybridní připojení Azure AD funguje s oběma, spravovaných a federovaných prostředí v závislosti na tom, jestli je hlavní upn směrovatelný nebo nesměrovatelný. Tabulka v podporovaných scénářích najdete v dolní části stránky.  

### <a name="managed-environment"></a>Spravované prostředí

Spravované prostředí lze nasadit buď prostřednictvím [synchronizace hash hes (PHS)](/azure/active-directory/hybrid/whatis-phs) nebo [předat ověřování (PTA)](/azure/active-directory/hybrid/how-to-connect-pta) s [bezproblémovým jedním přihlášením](/azure/active-directory/hybrid/how-to-connect-sso).

Tyto scénáře nevyžadují konfiguraci federačního serveru pro ověřování.

### <a name="federated-environment"></a>Federované prostředí

Federované prostředí by mělo mít zprostředkovatele identity, který podporuje následující požadavky. Pokud máte federované prostředí používající službu AD FS (Active Directory Federation Services), jsou již následující požadavky podporovány.

- **Tvrzení WIAORMULTIAUTHN:** Tato deklarace je vyžadována k provedení hybridního připojení Azure AD pro zařízení nižší úrovně windows.
- **WS-Trust protokol:** Tento protokol je nutný k ověření aktuálního hybridního zařízení Azure AD pro Windows pomocí Azure AD. Pokud používáte službu AD FS, musíte povolit následující koncové body WS-Trust:`/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Oba **adfs/services/trust/2005/windowstransport** nebo **adfs/services/trust/13/windowstransport** by měly být povoleny jako intranet směřující pouze koncové body a nesmí být vystaveny jako koncové body směřující k extranetu prostřednictvím proxy webové aplikace. Další informace o zakázání koncových bodů systému WS-Trust systému Windows naleznete [v tématu Zakázání koncových bodů systému WS-Trust systému Windows na serveru proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Můžete vidět, jaké koncové body jsou povoleny prostřednictvím konzoly pro správu služby AD FS v části Koncové body **služby** > **Endpoints**.

> [!NOTE]
> Azure AD nepodporuje čipové karty nebo certifikáty ve spravovaných doménách.

Azure AD Connect od verze 1.1.819.0 nabízí průvodce konfigurací hybridního připojení k Azure AD. Tento průvodce vám umožní výrazně zjednodušit proces konfigurace. Pokud instalace požadované verze Služby Azure AD Connect není pro vás možnost, přečtěte si, přečtěte si, [jak ručně nakonfigurovat registraci zařízení](hybrid-azuread-join-manual.md). 

Na základě scénáře, který odpovídá infrastruktuře identit, najdete v tématu:

- [Konfigurace hybridního připojení služby Azure Active Directory pro federované prostředí](hybrid-azuread-join-federated-domains.md)
- [Konfigurace hybridního připojení služby Azure Active Directory pro spravované prostředí](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>Kontrola místní podpory UPN uživatelů služby AD pro připojení hybridního Azure AD

V některých případě se vaše místní upny uživatelů služby AD mohou lišit od vašich upnů Služby Azure AD. V takových případech poskytuje připojení k hybridnímu azure ad systému Windows 10 omezenou podporu pro místní hlavní názvy upn služby AD na základě [metody ověřování](/azure/security/fundamentals/choose-ad-authn), typu domény a verze Windows 10. Existují dva typy místních upen ů ad, které mohou existovat ve vašem prostředí:

- Směrovatelných uživatelů UPN: Směrovatelný upn má platnou ověřenou doménu, která je registrována u doménového registrátora. Pokud je například contoso.com primární doménou ve službě Azure AD, je contoso.org primární doménou v místním ad vlastněném společností Contoso a [ověřenou ve službě Azure AD.](/azure/active-directory/fundamentals/add-custom-domain)
- Nesměrovatelných uživatelů UPN: Nesměrovatelný název UŽIVATELE nemá ověřenou doménu. Platí pouze v privátní síti vaší organizace. Pokud je například contoso.com primární doménou ve službě Azure AD, contoso.local je primární doménou v místní službě AD, ale není ověřitelnou doménou v internetu a používá se pouze v síti contoso.

> [!NOTE]
> Informace v této části platí pouze pro místní uživatele HLAVNÍ HO DISPON. Nevztahuje se na místní příponu domény počítače (například computer1.contoso.local).

Níže uvedená tabulka obsahuje podrobnosti o podpoře těchto místních upn ů služby AD v připojení hybridního azure ad ve Windows 10.

| Typ místního upn ad | Typ domény | Verze Windows 10 | Popis |
| ----- | ----- | ----- | ----- |
| Směrovatelný | Federovaní | Od vydání 1703 | Obecná dostupnost |
| Nesměrovatelné | Federovaní | Od vydání 1803 | Obecná dostupnost |
| Směrovatelný | Spravovaní | Od vydání 1803 | Obecně dostupné, azure ad sspr na zamykací obrazovce Windows není podporována |
| Nesměrovatelné | Spravovaní | Nepodporuje se | |

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace hybridního připojení služby Azure Active Directory pro federované prostředí](hybrid-azuread-join-federated-domains.md)
> [Konfigurace hybridního připojení služby Azure Active Directory pro spravované prostředí](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
