---
title: Plánování připojení Hybrid Azure Active Directory – Azure Active Directory
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
ms.openlocfilehash: 419823086fd7ba05ba5023216be302576350e30a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687277"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Postupy: plánování implementace služby Hybrid Azure Active Directory JOIN

Podobně jako uživatel je zařízení další základní identitou, kterou chcete chránit, a používejte ji k ochraně svých prostředků kdykoli a z libovolného místa. Tento cíl můžete dosáhnout vytvořením a správou identit zařízení ve službě Azure AD pomocí jedné z následujících metod:

- Připojení k Azure AD
- Hybridní připojení k Azure AD
- Registrace v Azure AD

Přenosem zařízení do Azure AD maximalizujete produktivitu uživatelů díky jednotnému přihlašování ke cloudovým i místním prostředkům. V současné době můžete zabezpečený přístup k vašim cloudovým a místním prostředkům zabezpečit pomocí [podmíněného přístupu](../conditional-access/overview.md).

Pokud máte místní prostředí Active Directory (AD) a chcete se připojit k počítačům připojeným k doméně AD do služby Azure AD, můžete to provést pomocí hybridního připojení k Azure AD. Tento článek poskytuje související kroky pro implementaci hybridního připojení k Azure AD ve vašem prostředí. 

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že jste obeznámeni se [Seznámkou se správou identit zařízení v Azure Active Directory](./overview.md).

> [!NOTE]
> Minimální požadovaná verze řadiče domény pro připojení k hybridní službě Azure AD ve Windows 10 je Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Plánování implementace

K naplánování vaší hybridní implementace služby Azure AD byste se měli seznámit s těmito kroky:

> [!div class="checklist"]
> - Zkontrolovat podporovaná zařízení
> - Projděte si věci, které byste měli znát.
> - Kontrola řízeného ověřování pro připojení k hybridní službě Azure AD
> - Vyberte svůj scénář na základě vaší infrastruktury identity
> - Kontrola místní podpory služby AD hlavního názvu uživatele (UPN) pro připojení k hybridní službě Azure AD

## <a name="review-supported-devices"></a>Zkontrolovat podporovaná zařízení

Připojení k hybridní službě Azure AD podporuje širokou škálu zařízení s Windows. Vzhledem k tomu, že konfigurace pro zařízení s staršími verzemi Windows vyžaduje další nebo různé kroky, jsou podporovaná zařízení seskupená do dvou kategorií:

### <a name="windows-current-devices"></a>Aktuální zařízení s Windows

- Windows 10
- Windows Server 2016
  - **Poznámka**: zákazníci s vnitrostátními cloudy Azure vyžadují verzi 1803.
- Windows Server 2019

V případě zařízení s desktopovým operačním systémem Windows je podporovaná verze uvedená v tomto článku [informace o verzi Windows 10](/windows/release-information/). Osvědčeným postupem je, že Microsoft doporučuje upgradovat na nejnovější verzi Windows 10.

### <a name="windows-down-level-devices"></a>Zařízení se škálováním na nižší úrovni Windows

- Windows 8.1
- Podpora Windows 7 skončila 14. ledna 2020. Další informace najdete v tématu [Podpora pro Windows 7 skončila](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Informace o podpoře na Windows serveru 2008 a 2008 R2 najdete v článku [Příprava na Windows server 2008 na konci podpory](https://www.microsoft.com/cloud-platform/windows-server-2008).

Jako první krok plánování byste měli zkontrolovat prostředí a určit, jestli potřebujete podporovat zařízení se systémem Windows nižší úrovně.

## <a name="review-things-you-should-know"></a>Projděte si věci, které byste měli znát.

### <a name="unsupported-scenarios"></a>Nepodporované scénáře
- Služba připojení k hybridní službě Azure AD se v současné době nepodporuje, pokud vaše prostředí obsahuje jednu doménovou strukturu služby AD synchronizující data identity do více než jednoho tenanta Azure AD.

- Služba připojení k hybridní službě Azure AD není podporovaná pro Windows Server, na kterém běží role řadič domény (DC).

- Připojení k hybridní službě Azure AD není podporované v zařízeních Windows nižší úrovně při použití roamingu přihlašovacích údajů nebo cestovního profilu uživatele nebo povinného profilu.

- Operační systém jádra serveru nepodporuje žádný typ registrace zařízení.

- Nástroj pro migraci uživatelských souborů a nastavení (USMT) nefunguje s registrací zařízení.  

### <a name="os-imaging-considerations"></a>Požadavky na vytváření bitových kopií operačního systému
- Pokud se spoléháte na nástroj pro přípravu systému (Sysprep) a pokud pro instalaci používáte image **starší než Windows 10 1809** , ujistěte se, že image není ze zařízení, které už je zaregistrované ve službě Azure AD, jako připojení k hybridní službě Azure AD.

- Pokud při vytváření dalších virtuálních počítačů spoléháte na snímek virtuálního počítače, ujistěte se, že snímek není z virtuálního počítače, který je už zaregistrovaný ve službě Azure AD, jako připojení k hybridní službě Azure AD.

- Pokud používáte [Sjednocený filtr zápisu](/windows-hardware/customize/enterprise/unified-write-filter) a podobné technologie, které při restartování vymažou změny disku, musí se použít po připojení zařízení k hybridní službě Azure AD. Pokud tyto technologie povolíte před dokončením programu připojení k hybridní službě Azure AD, dojde při každém restartování k tomu, že se zařízení nepřipojí.

### <a name="handling-devices-with-azure-ad-registered-state"></a>Zpracování zařízení s registrovaným stavem Azure AD
Pokud jsou vaše zařízení připojená k doméně Windows 10 [registrovaná](overview.md#getting-devices-in-azure-ad) ve vašem tenantovi, může to vést k duálnímu stavu připojení k hybridní službě Azure AD a k zaregistrovanému zařízení Azure AD. Pro automatické vyřešení tohoto scénáře doporučujeme upgradovat na Windows 10 1803 (s použitím KB4489894) nebo novějším. Ve verzích starších než 1803 budete muset před povolením hybridního připojení k Azure AD ručně odebrat stav registrovaný pro službu Azure AD. V 1803 a vyšších verzích byly provedeny následující změny, aby nedocházelo k tomuto duálnímu stavu:

- Veškerý stávající stav registrovaný pro uživatele Azure AD by se automaticky odebral <i>poté, co je zařízení připojené k hybridní službě Azure AD a přihlásí se stejný uživatel</i>. Pokud třeba uživatel A měl na zařízení zaregistrován stav služby Azure AD, bude se duální stav pro uživatele A vyčistit jenom v případě, že se uživatel k zařízení přihlásí. Pokud se na jednom zařízení nachází více uživatelů, dvojí stav se vyčistí jednotlivě při přihlášení uživatelů. Kromě odebrání stavu registrovaného ve službě Azure AD bude Windows 10 také rušit registraci zařízení v Intune nebo jiné MDM, pokud k registraci došlo jako součást registrace Azure AD prostřednictvím automatického zápisu.
- Tato změna neovlivní zaregistrovaný stav služby Azure AD na jakýchkoli místních účtech v zařízení. Vztahuje se pouze na doménové účty. Takže stav registrovaných účtů Azure AD na místních účtech se automaticky neodebere ani po přihlášení uživatele, protože uživatel není uživatelem domény. 
- Registraci zařízení připojeného k doméně můžete zabránit tím, že se zaregistrujete do služby Azure AD tak, že do HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin přidáte následující hodnotu registru: "BlockAADWorkplaceJoin" = DWORD: 00000001.
- Pokud máte v systému Windows 10 1803 nakonfigurovanou možnost Windows Hello pro firmy, uživatel musí po vyčištění duálního stavu znovu nastavit Windows Hello pro firmy. Tento problém se vyřešil pomocí KB4512509.

> [!NOTE]
> I když Windows 10 automaticky odstraní místně registrovaný stav služby Azure AD, objekt zařízení ve službě Azure AD se okamžitě neodstraní, pokud ho spravuje Intune. Odebrání stavu registrovaného pro Azure AD můžete ověřit spuštěním dsregcmd/status a zvažte, jestli zařízení není zaregistrované v Azure AD na základě toho.

### <a name="additional-considerations"></a>Další aspekty
- Pokud vaše prostředí používá infrastrukturu virtuálních klientských počítačů (VDI), přečtěte si téma [Identita zařízení a virtualizace plochy](./howto-device-identity-virtual-desktop-infrastructure.md).

- Připojení k hybridní službě Azure AD se podporuje pro čip TPM kompatibilní se standardem FIPS 2,0 a nepodporuje se pro čip TPM 1,2. Pokud vaše zařízení mají čip TPM kompatibilní se standardem FIPS 1,2, musíte je před tím, než budete pokračovat s hybridním připojením k Azure AD, zakázat. Microsoft neposkytuje žádné nástroje pro zakázání režimu FIPS pro čipy TPM, protože je závislý na výrobci čipu TPM. Požádejte o podporu svého hardwarového výrobce OEM. 

- Od verze Windows 10 1903 se čipy TPM 1,2 nepoužívá s hybridním připojením k Azure AD a zařízením s těmito čipy TPM se bude považovat za to, že nemají čip TPM.

- Změny hlavního názvu uživatele (UPN) jsou podporované jenom při spuštění aktualizace Windows 10 2004. Pro zařízení starší než Windows 10 2004 Update by uživatelé měli na svých zařízeních problémy jednotného přihlašování a podmíněného přístupu. Pokud chcete tento problém vyřešit, musíte zařízení odpojte z Azure AD (spusťte dsregcmd/Leave s vyššími oprávněními) a znovu se připojte (automaticky se stane). Uživatelé, kteří se přihlásí pomocí Windows Hello pro firmy, ale tento problém nečelí.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Kontrola řízeného ověřování pro připojení k hybridní službě Azure AD

Když jsou splněné všechny požadavky, zařízení s Windows se v tenantovi Azure AD automaticky registrují jako zařízení. Stav těchto identit zařízení ve službě Azure AD se označuje jako připojení k hybridní službě Azure AD. Další informace o konceptech popsaných v tomto článku najdete v článku [Úvod do správy identit zařízení v Azure Active Directory](overview.md).

Organizace můžou chtít provést řízené ověřování pro připojení k hybridní službě Azure AD, a to ještě před tím, než je zapnete v celé organizaci najednou. Přečtěte si článek [řízený ověření hybridního připojení ke službě Azure AD](hybrid-azuread-join-control.md) , abyste pochopili, jak to provést.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Vyberte svůj scénář na základě vaší infrastruktury identity

Připojení k hybridní službě Azure AD funguje s oběma spravovanými i federovaným prostředími v závislosti na tom, jestli je hlavní název uživatele směrovatelný nebo není směrovatelný. V podporovaných scénářích se podívejte na dolní část stránky pro tabulku.  

### <a name="managed-environment"></a>Spravované prostředí

Spravované prostředí se dá nasadit buď pomocí [synchronizace hodnot hash hesel (kosmetice)](../hybrid/whatis-phs.md) , nebo [předávat ověřování (PTA)](../hybrid/how-to-connect-pta.md) pomocí [bezproblémového jednotného přihlašování](../hybrid/how-to-connect-sso.md).

Tyto scénáře nevyžadují konfiguraci federačního serveru pro ověřování.

> [!NOTE]
> [Cloudové ověřování pomocí připraveného zavedení](../hybrid/how-to-connect-staged-rollout.md) je podporované jenom při spuštění aktualizace Windows 10 1903.

### <a name="federated-environment"></a>Federované prostředí

Federované prostředí by mělo mít poskytovatele identity, který podporuje následující požadavky. Pokud máte federované prostředí pomocí Active Directory Federation Services (AD FS) (AD FS), jsou již podporovány níže uvedené požadavky.

- **WIAORMULTIAUTHN deklarace identity:** Tato deklarace identity se vyžaduje k tomu, aby se pro zařízení se systémem Windows na nižší úrovni mohla připojit hybridní služba Azure AD.
- **Protokol WS-Trust:** Tento protokol je nutný k ověření současných zařízení s Windows připojených k hybridní službě Azure AD pomocí Azure AD. Pokud používáte AD FS, je nutné povolit následující WS-Trust koncové body: `/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **AD FS/Services/Trust/2005/windowstransport** , **AD FS/Services/Trust/13/windowstransport** by měly být povolené jenom jako intranetové koncové body a nesmí být zveřejněné jako extranetové koncové body prostřednictvím proxy webových aplikací. Další informace o tom, jak zakázat WS-Trust koncové body Windows, najdete v tématu [zakázání WS-Trust koncových bodů Windows na proxy serveru](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Pomocí konzoly pro správu AD FS v části   >  **koncové body** služby můžete zjistit, jaké koncové body jsou povolené.

> [!NOTE]
> Azure AD nepodporuje čipové karty ani certifikáty ve spravovaných doménách.

Azure AD Connect od verze 1.1.819.0 nabízí průvodce konfigurací hybridního připojení k Azure AD. Tento průvodce vám umožní výrazně zjednodušit proces konfigurace. Pokud se nejedná o možnost instalace požadované verze Azure AD Connect, přečtěte si téma [Postup ruční konfigurace registrace zařízení](hybrid-azuread-join-manual.md). 

V závislosti na scénáři, který odpovídá vaší infrastruktuře identity, se podívejte na:

- [Konfigurace služby Hybrid Azure Active Directory JOIN pro federované prostředí](hybrid-azuread-join-federated-domains.md)
- [Konfigurace hybridního Azure Active Directory JOIN pro spravované prostředí](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>Kontrola podpory místních uživatelů služby AD v hlavním názvu uživatele (UPN) pro připojení k hybridní službě Azure AD

V některých případech se vaše místní uživatelské názvy UPN můžou lišit od uživatelských UPN Azure AD. V takových případech služba Windows 10 Hybrid Azure AD JOIN nabízí omezené podpory místních UPN služby AD na základě [metody ověřování](../hybrid/choose-ad-authn.md), typu domény a verze Windows 10. Existují dva typy místních UPN služby AD, které můžou existovat ve vašem prostředí:

- Název UPN pro směrování uživatelů: směrovatelný hlavní název uživatele má platnou ověřenou doménu, která je zaregistrovaná u doménového registrátora. Pokud je například contoso.com primární doménou v Azure AD, contoso.org je primární doména v místní službě AD vlastněná společností Contoso a [ověřená v Azure AD](../fundamentals/add-custom-domain.md) .
- Hlavní název uživatele bez směrování uživatelů: nesměrovatelný hlavní název uživatele (UPN) nemá ověřenou doménu. Dá se použít jenom v privátní síti vaší organizace. Pokud je například contoso.com primární doménou v Azure AD, contoso. Local je primární doména v místní službě AD, ale nejedná se o ověřitelných doménách v Internetu a používá se jenom v síti Contoso.

> [!NOTE]
> Informace v této části se vztahují jenom na hlavní název uživatele (UPN) pro místní uživatele. Neplatí pro příponu místní domény počítače (příklad: Počítač1. contoso. Local).

V následující tabulce najdete podrobné informace o podpoře místních UPN služby AD ve Windows 10 – připojení k hybridní službě Azure AD.

| Typ místního hlavního názvu uživatele služby AD | Typ domény | Verze Windows 10 | Popis |
| ----- | ----- | ----- | ----- |
| Balíček | Federovaní | Z verze 1703 | Obecná dostupnost |
| Bez směrování | Federovaní | Z verze 1803 | Obecná dostupnost |
| Balíček | Spravované | Z verze 1803 | Všeobecně dostupná služba Azure AD SSPR ve Windows zamykací obrazovky není podporovaná. |
| Bez směrování | Spravované | Nepodporováno | |

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace služby hybrid Azure Active Directory JOIN pro federované prostředí](hybrid-azuread-join-federated-domains.md) 
>  [Konfigurace hybridního Azure Active Directory JOIN pro spravované prostředí](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
