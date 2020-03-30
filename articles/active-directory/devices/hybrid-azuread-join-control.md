---
title: Řízené ověření hybridního připojení Azure AD – Azure AD
description: Zjistěte, jak provést řízené ověření hybridního připojení Azure AD, než ho povolíte v celé organizaci najednou.
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
ms.openlocfilehash: f43db805ccbb7d4e546c51bbe39350f4bbba2efb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049980"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Řízené ověřování hybridního připojení k Azure AD

Když jsou všechny předpoklady na místě, zařízení s Windows se automaticky zaregistrují jako zařízení ve vašem tenantovi Azure AD. Stav těchto identit zařízení ve službě Azure AD se označuje jako hybridní připojení Azure AD. Další informace o konceptech uvedených v tomto článku najdete v článcích [Úvod do správy zařízení ve službě Azure Active Directory](overview.md) a plánování implementace připojení hybridní [služby Azure Active Directory](hybrid-azuread-join-plan.md).

Organizace mohou chtít provést řízené ověření hybridního připojení Azure AD před povolením v celé organizaci najednou. Tento článek vám vysvětlí, jak provést řízené ověření hybridního připojení Azure AD.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Řízené ověření hybridního připojení Azure AD na aktuálních zařízeních s Windows

U zařízení s desktopovým operačním systémem Windows je podporovanou verzí aktualizace Windows 10 Anniversary Update (verze 1607) nebo novější. Osvědčeným postupem je upgrade na nejnovější verzi Windows 10.

Chcete-li provést řízené ověření hybridního připojení Azure AD na aktuálních zařízeních s Windows, musíte:

1. Vymazání položky spojovacího bodu (SCP) služby ze služby Active Directory (AD), pokud existuje
1. Konfigurace nastavení registru na straně klienta pro protokol SCP v počítačích spojených s doménou pomocí objektu zásad skupiny (GPO)
1. Pokud používáte službu AD FS, musíte také nakonfigurovat nastavení registru na straně klienta pro server SCP na serveru služby AD FS pomocí příkazu gpo  
1. Možná budete muset [také přizpůsobit možnosti synchronizace](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect) v Azure AD Connect, abyste povolili synchronizaci zařízení. 


### <a name="clear-the-scp-from-ad"></a>Vymazání scp z AD

Pomocí Editoru rozhraní služby Active Directory Services (ADSI Edit) můžete upravit objekty SCP ve službě AD.

1. Spusťte pracovní stanici **ADSI Edit desktop** z pracovní stanice pro správu nebo řadič domény jako podnikový správce.
1. Připojte se k **kontextu pojmenování konfigurace** vaší domény.
1. Přejděte na **CN=Configuration,DC=contoso,DC=com** > **CN=Services** > **CN=Konfigurace registrace zařízení**
1. Klikněte pravým tlačítkem myši na listový objekt **CN=62a0ff2e-97b9-4513-943f-0d221bd30080** a vyberte **Vlastnosti**
   1. Vyberte **klíčová slova** z okna **Editor atributů** a klepněte na **Upravit.**
   1. Vyberte hodnoty **azureADId** a **azureADName** (jeden po druhém) a klikněte na **Odebrat**
1. Zavřít **úpravy rozhraní ADSI**


### <a name="configure-client-side-registry-setting-for-scp"></a>Konfigurace nastavení registru na straně klienta pro SCP

Následující příklad slouží k vytvoření objektu zásad skupiny (GPO) k nasazení nastavení registru, které konfiguruje položku SCP v registru vašich zařízení.

1. Otevřete konzolu pro správu zásad skupiny a vytvořte ve své doméně nový objekt zásad skupiny.
   1. Zadejte nově vytvořený seznam skupin název (například ClientSideSCP).
1. Upravte zásady zásad skupiny a vyhledejte následující cestu:**Předvolby** >  **konfigurace** > počítače Registr**nastavení systému** > **Registry** Windows
1. Klikněte pravým tlačítkem myši na registr a vyberte **novou** > **položku registru.**
   1. Na kartě **Obecné** nakonfigurujte následující
      1. Akce: **Aktualizace**
      1. Úl: **HKEY_LOCAL_MACHINE**
      1. Cesta klíče: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Název hodnoty: **TenantId**
      1. Typ hodnoty: **REG_SZ**
      1. Data o hodnotě: Identifikátor GUID nebo **ID adresáře** instance Azure AD (tuto hodnotu najdete na **webu Azure Portal** > **Azure Active Directory** > **Properties** > **Directory Directory ID)**
   1. Klikněte na tlačítko **OK**.
1. Klikněte pravým tlačítkem myši na registr a vyberte **novou** > **položku registru.**
   1. Na kartě **Obecné** nakonfigurujte následující
      1. Akce: **Aktualizace**
      1. Úl: **HKEY_LOCAL_MACHINE**
      1. Cesta klíče: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Název hodnoty: **Název_tenanta**
      1. Typ hodnoty: **REG_SZ**
      1. Data hodnoty: Ověřený **název domény,** pokud používáte federované prostředí, jako je služba AD FS. Ověřený **název domény** nebo onmicrosoft.com název `contoso.onmicrosoft.com` domény, například pokud používáte spravované prostředí
   1. Klikněte na tlačítko **OK**.
1. Zavření editoru pro nově vytvořený gpo
1. Propojte nově vytvořený soubor skupiny s požadovanou oupoložkou obsahující počítače spojené s doménou, které patří do kontrolované hospo- populace zavádění

### <a name="configure-ad-fs-settings"></a>Konfigurace nastavení služby AD FS

Pokud používáte službu AD FS, musíte nejprve nakonfigurovat server SCP na straně klienta pomocí výše uvedených pokynů propojením seznamu skupiny se servery služby AD FS. Objekt SCP definuje zdroj autority pro objekty zařízení. Může být místní nebo Azure AD. Když je scp na straně klienta nakonfigurován pro službu AD FS, zdroj pro objekty zařízení je vytvořen jako Azure AD.

> [!NOTE]
> Pokud se vám nepodařilo nakonfigurovat server SCP na straně klienta na serverech služby AD FS, zdroj identit zařízení by byl považován za místní. Služba ADFS pak začne spouštět objekty zařízení z místního adresáře po uplynutí stanovené doby definované v atributu "MaximumInactiveDays" registrace zařízení Služby ADFS. Objekty Registrace zařízení služby ADFS lze nalézt pomocí [rutiny Get-AdfsDeviceRegistration](/powershell/module/adfs/get-adfsdeviceregistration?view=win10-ps).

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Řízené ověření hybridního připojení Azure AD na zařízeních nižší úrovně Windows

Chcete-li zaregistrovat zařízení systému Windows nižší úrovně, musí organizace nainstalovat [připojení k webu Microsoft Workplace Join pro počítače s jiným systémem Windows 10, které](https://www.microsoft.com/download/details.aspx?id=53554) jsou k dispozici na webu služby Stažení softwaru.

Balíček můžete nasadit pomocí systému distribuce softwaru, jako je [Microsoft Endpoint Configuration Manager](/configmgr/). Balíček podporuje standardní možnosti tiché instalace s tichým parametrem. Aktuální pobočka nástroje Configuration Manager nabízí výhody oproti dřívějším verzím, například možnost sledovat dokončené registrace.

Instalační program vytvoří naplánovanou úlohu v systému, která je spuštěna v uživatelském kontextu. Úloha se spustí, když se uživatel přihlásí k systému Windows. Úloha bezobslužně připojí zařízení s Azure AD s přihlašovacími údaji uživatele po ověření pomocí Azure AD.

Chcete-li řídit registraci zařízení, měli byste nasadit balíček Instalační služby systému Windows do vybrané skupiny zařízení nižší úrovně systému Windows.

> [!NOTE]
> Pokud scp není nakonfigurován ve službě AD, měli byste postupovat stejným způsobem, jak je popsáno v [konfiguraci nastavení registru na straně klienta pro SCP](#configure-client-side-registry-setting-for-scp)) v počítačích spojených s doménou pomocí objektu zásad skupiny (GPO).


Po ověření, že vše funguje podle očekávání, můžete automaticky zaregistrovat zbytek vašeho Windows aktuální a nižší úrovně zařízení s Azure AD [konfigurací SCP pomocí Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Další kroky

[Plánování implementace hybridního připojení k azure active directory](hybrid-azuread-join-plan.md)
