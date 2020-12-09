---
title: Řízené ověřování pro připojení k hybridní službě Azure AD – Azure AD
description: Přečtěte si, jak provést řízené ověřování hybridního připojení ke službě Azure AD a teprve potom ho v celé organizaci povolit najednou.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38754b9e349e27afcff58dac27a616e3e4fb5319
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860928"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Řízené ověřování hybridního připojení k Azure AD

Když jsou splněné všechny požadavky, zařízení s Windows se v tenantovi Azure AD automaticky registrují jako zařízení. Stav těchto identit zařízení ve službě Azure AD se označuje jako připojení k hybridní službě Azure AD. Další informace o konceptech popsaných v tomto článku najdete v článcích [Úvod do správy zařízení v Azure Active Directory](overview.md) a [Naplánování implementace Hybrid Azure Active Directory JOIN](hybrid-azuread-join-plan.md).

Organizace můžou chtít provést řízené ověřování pro připojení k hybridní službě Azure AD, a to ještě před tím, než je zapnete v celé organizaci najednou. V tomto článku se dozvíte, jak provést řízené ověření služby připojení k hybridní službě Azure AD.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Řízené ověřování služby Azure AD JOIN v současných zařízeních s Windows

U zařízení s desktopovým operačním systémem Windows je podporovanou verzí aktualizace pro Windows 10 pro výročí (verze 1607) nebo novější. Osvědčeným postupem je upgradovat na nejnovější verzi Windows 10.

Aby bylo možné provádět řízené ověřování služby Azure AD JOIN v současných zařízeních s Windows, musíte provést tyto kroky:

1. Vymažte položku bodu připojení služby (SCP) ze služby Active Directory (AD), pokud existuje.
1. Konfigurace nastavení registru na straně klienta pro spojovací bod služby na počítačích připojených k doméně pomocí objektu Zásady skupiny (GPO)
1. Pokud používáte AD FS, musíte taky nakonfigurovat nastavení registru na straně klienta pro spojovací bod služby na serveru AD FS pomocí objektu zásad skupiny.  
1. Je také možné, že budete muset [přizpůsobit možnosti synchronizace](../hybrid/how-to-connect-post-installation.md#additional-tasks-available-in-azure-ad-connect) v Azure AD Connect, aby bylo možné povolit synchronizaci zařízení. 


### <a name="clear-the-scp-from-ad"></a>Vymazání spojovacího bodu služby ve službě AD

Chcete-li upravit objekty spojovacího bodu služby ve službě AD, použijte Editor rozhraní ADSI (Active Directory Services).

1. Spusťte aplikaci **ADSI Edit** Desktop z nástroje a pracovní stanice pro správu nebo řadič domény jako správce podnikové sítě.
1. Připojte se k **názvovému kontextu konfigurace** vaší domény.
1. Přejděte na **CN = Configuration, DC = contoso, DC = com**  >  **CN = Services**  >  **CN = Device Registration Configuration** .
1. Klikněte pravým tlačítkem na listový objekt **CN = 62a0ff2e-97B9-4513-943F-0d221bd30080** a vyberte **vlastnosti** .
   1. V okně **Editor atributů** vyberte **klíčová slova** a klikněte na **Upravit** .
   1. Vyberte hodnoty **azureADId** a **azureADName** (jeden po druhém) a klikněte na **Odebrat** .
1. Zavřít **Editor ADSI**


### <a name="configure-client-side-registry-setting-for-scp"></a>Konfigurace nastavení registru na straně klienta pro spojovací bod služby

Pomocí následujícího příkladu vytvořte objekt Zásady skupiny (GPO), který nasadí nastavení registru konfigurace položky SCP v registru vašich zařízení.

1. Otevřete konzolu pro správu Zásady skupiny a vytvořte nový objekt Zásady skupiny ve vaší doméně.
   1. Zadejte název nově vytvořeného objektu zásad skupiny (například ClientSideSCP).
1. Upravte objekt zásad skupiny a vyhledejte následující cestu: **Předvolby konfigurace počítače**  >  **Preferences**  >  **Windows registr nastavení systému Windows**  >  **Registry**
1. Klikněte pravým tlačítkem na registr a vyberte možnost **Nová**  >  **položka registru** .
   1. Na kartě **Obecné** nakonfigurujte následující
      1. Akce: **aktualizace**
      1. Podregistr: **HKEY_LOCAL_MACHINE**
      1. Cesta ke klíči: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Název hodnoty: **TenantId**
      1. Typ hodnoty: **REG_SZ**
      1. Value data: identifikátor GUID nebo **ID adresáře** vaší instance služby Azure AD (Tato hodnota se nachází v **Azure Portal**  >  **Azure Active Directory**  >  **vlastností**  >  **ID adresáře**)
   1. Klikněte na tlačítko **OK**.
1. Klikněte pravým tlačítkem na registr a vyberte možnost **Nová**  >  **položka registru** .
   1. Na kartě **Obecné** nakonfigurujte následující
      1. Akce: **aktualizace**
      1. Podregistr: **HKEY_LOCAL_MACHINE**
      1. Cesta ke klíči: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Název hodnoty: **tenant**
      1. Typ hodnoty: **REG_SZ**
      1. Údaj hodnoty: ověřený **název domény** , pokud používáte federované prostředí, například AD FS. Pokud například používáte spravované prostředí, váš ověřený **název domény** nebo název domény onmicrosoft.com. `contoso.onmicrosoft.com`
   1. Klikněte na tlačítko **OK**.
1. Zavřít editor nově vytvořeného objektu zásad skupiny
1. Propojení nově vytvořeného objektu zásad skupiny s požadovanými organizačními jednotkami, které patří počítačům připojeným k doméně, které patří k vašemu řízenému naplnění

### <a name="configure-ad-fs-settings"></a>Konfigurace nastavení AD FS

Pokud používáte AD FS, musíte nejprve nakonfigurovat spojovací bod služby na straně klienta pomocí výše uvedených pokynů, a to tak, že objekt zásad skupiny propojíte se servery AD FS. Objekt spojovacího bodu služby definuje zdroj autority pro objekty zařízení. Může to být místní nebo Azure AD. Když je spojovací bod služby na straně klienta nakonfigurovaný pro AD FS, je zdroj pro objekty zařízení vytvořený jako Azure AD.

> [!NOTE]
> Pokud se nezdařila konfigurace spojovacího bodu služby klienta na serverech AD FS, bude zdroj pro identity zařízení považován za místní. Služba AD FS pak začne odstraňovat objekty zařízení z místního adresáře po stanoveném období definovaném v atributu registrace zařízení ADFS "MaximumInactiveDays". Objekty registrace zařízení ADFS najdete pomocí [rutiny Get-AdfsDeviceRegistration](/powershell/module/adfs/get-adfsdeviceregistration).

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Řízené ověřování připojení k hybridní službě Azure AD na zařízeních nižší úrovně Windows

Aby bylo možné zaregistrovat zařízení Windows nižší úrovně, musí organizace nainstalovat [microsoft Workplace JOIN pro počítače s jiným systémem než Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) , které jsou k dispozici na webu Microsoft Download Center.

Balíček můžete nasadit pomocí systému distribuce softwaru, jako je [Microsoft Endpoint Configuration Manager](/configmgr/). Balíček podporuje standardní možnosti bezobslužné instalace s parametrem quiet. Aktuální větev Configuration Manager nabízí výhody oproti starším verzím, jako je schopnost sledovat dokončené registrace.

Instalační program vytvoří v systému naplánovanou úlohu, která běží v uživatelském kontextu. Úkol se aktivuje, když se uživatel přihlásí k Windows. Úloha se tiše připojí k zařízení pomocí Azure AD s přihlašovacími údaji uživatele po ověření ve službě Azure AD.

K řízení registrace zařízení byste měli nasadit balíček Instalační služba systému Windows do vaší vybrané skupiny zařízení Windows nižší úrovně.

> [!NOTE]
> Pokud není bod připojení služby ve službě AD nakonfigurovaný, měli byste dodržovat stejný přístup, jak je popsáno v tématu [Konfigurace nastavení registru služby SCP na straně klienta](#configure-client-side-registry-setting-for-scp), na počítačích připojených k doméně pomocí objektu Zásady skupiny (objekt zásad skupiny).


Až ověříte, že vše funguje podle očekávání, můžete do služby Azure AD automaticky zaregistrovat zbývající zařízení s Windows a nižší úrovní pomocí [Konfigurace SCP pomocí Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Další kroky

[Plánování implementace služby Hybrid Azure Active Directory JOIN](hybrid-azuread-join-plan.md)
