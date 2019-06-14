---
title: Řízené ověřování připojení k hybridní službě Azure AD službě – Azure AD
description: Zjistěte, jak provést řízené ověřování připojení k hybridní službě Azure AD, než povolíte napříč najednou celou organizaci
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: article
ms.date: 05/30/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd5b388f92a875fb2635037a6eae3ff3b6a95793
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66513293"
---
# <a name="controlled-validation-of-hybrid-azure-ad-join"></a>Řízené ověřování hybridního připojení k Azure AD

Když jsou všechny požadavky na místě, se automaticky zaregistrují zařízení s Windows jako zařízení ve vašem tenantovi Azure AD. Stav těchto identit zařízení ve službě Azure AD se označuje jako připojení k hybridní službě Azure AD. Další informace o konceptech popsaná v tomto článku najdete v článcích [Úvod ke správě zařízení ve službě Azure Active Directory](overview.md) a [naplánování vaší implementace připojení k hybridní službě Azure Active Directory ](hybrid-azuread-join-plan.md).

Organizace může být vhodné provést řízené ověřování připojení k hybridní službě Azure AD, než povolíte napříč celou organizací všechny najednou. Tento článek vysvětluje, jak provést řízené ověřování připojení k hybridní službě Azure AD.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-current-devices"></a>Řízené ověřování připojení k hybridní službě Azure AD službě na aktuální zařízení Windows

Pro zařízení s operačním systémem klasické pracovní plochy Windows, je podporovaná verze Windows 10 Anniversary Update (verze 1607) nebo novější. Jako osvědčený postup upgradujte na nejnovější verzi Windows 10.

Řízené ověřování připojení k hybridní službě Azure AD službě na aktuální zařízení Windows, budete muset:

1. Vymazat vstupní bod připojení služby (SCP) ze služby Active Directory (AD), pokud existuje
1. Konfigurace nastavení registru na straně klienta pro spojovací bod služby v počítači připojeném k doméně pomocí objektů zásad skupiny (GPO)
1. Pokud používáte službu AD FS, musíte také nakonfigurovat nastavení registru na straně klienta pro spojovací bod služby na serveru služby AD FS pomocí objektu zásad skupiny  



### <a name="clear-the-scp-from-ad"></a>Vymazat spojovací bod služby ze služby AD

K úpravě spojovacího bodu služby objektů ve službě AD, použijte Active Directory Services rozhraní Editoru (ADSI).

1. Spusťte **ADSI Edit** aplikace klasické pracovní plochy z a pracovní stanice pro správu nebo řadič domény jako správce podnikové sítě.
1. Připojte se k **konfigurace názvový kontext** vaší domény.
1. Přejděte do **CN = Configuration, DC = contoso, DC = com** > **CN = Services** > **CN = Device Registration Configuration**
1. Klikněte pravým tlačítkem na objekt typu list v rámci **CN = Device Registration Configuration** a vyberte **vlastnosti**
   1. Vyberte **klíčová slova** z **Editor atributů** okna a klikněte na **upravit**
   1. Vyberte hodnoty **azureADId** a **azureADName** (postupně po jednom) a klikněte na tlačítko **odebrat**
1. Zavřít **Editor rozhraní ADSI**


### <a name="configure-client-side-registry-setting-for-scp"></a>Konfigurace nastavení registru na straně klienta pro spojovací bod služby

Použijte následující příklad k vytvoření objektu zásad skupiny (GPO) k nasazení nastavení registru konfiguraci spojovacího bodu služby záznam v registru zařízení.

1. Otevřete konzolu pro správu zásad skupiny a vytvořte nový objekt zásad skupiny ve vaší doméně.
   1. Zadejte název (například ClientSideSCP) nově vytvořeného objektu zásad skupiny.
1. Upravte objekt zásad skupiny a vyhledejte následující cestu: **Konfigurace počítače** > **Předvolby** > **nastavení Windows** > **registru**
1. Klikněte pravým tlačítkem na registr a vyberte **nový** > **položky registru**
   1. Na **Obecné** kartu, proveďte následující konfiguraci
      1. Akce: **Aktualizace**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Cesta ke klíči: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Název hodnoty: **ID Tenanta**
      1. Typ hodnoty: **REG_SZ**
      1. Údaj hodnoty: Identifikátor GUID nebo **ID adresáře** vaší instance služby Azure AD (tuto hodnotu najdete v **webu Azure portal** > **Azure Active Directory**  >   **Vlastnosti** > **ID adresáře**)
   1. Klikněte na tlačítko **OK**.
1. Klikněte pravým tlačítkem na registr a vyberte **nový** > **položky registru**
   1. Na **Obecné** kartu, proveďte následující konfiguraci
      1. Akce: **Aktualizace**
      1. Hive: **HKEY_LOCAL_MACHINE**
      1. Cesta ke klíči: **SOFTWARE\Microsoft\Windows\CurrentVersion\CDJ\AAD**
      1. Název hodnoty: **TenantName**
      1. Typ hodnoty: **REG_SZ**
      1. Údaj hodnoty: Vaše ověřené **název domény** ve službě Azure AD (například `contoso.onmicrosoft.com` nebo jiný název ověřené domény ve vašem adresáři)
   1. Klikněte na tlačítko **OK**.
1. Zavřete editor pro nově vytvořený objekt zásad skupiny
1. Propojit požadované organizační jednotku obsahující připojené k doméně počítače, které patří do vašeho základního souboru řízeně uvádět nově vytvořený objekt zásad skupiny

### <a name="configure-ad-fs-settings"></a>Konfigurace nastavení služby AD FS

Pokud používáte službu AD FS, musíte nejprve nakonfigurovat spojovací bod služby na straně klienta pomocí pokynů uvedených výše, ale propojení objektu zásad skupiny na servery služby AD FS. Tato konfigurace je potřeba pro službu AD FS pro stanovení zdroj identity zařízení služby Azure AD.

## <a name="controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices"></a>Řízené ověřování připojení k hybridní službě Azure AD na zařízeních s Windows nižší úrovně

K registraci zařízení s Windows nižší úrovně, musíte nainstalovat organizace [Microsoft Workplace Join pro počítače s Windows 10](https://www.microsoft.com/download/details.aspx?id=53554) k dispozici na webu Microsoft Download Center.

Balíček můžete nasadit pomocí systém distribuce softwaru, jako je [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Balíček podporuje možnosti standardní tichou instalaci s parametrem tichý. Aktuální větev nástroje Configuration Manager nabízí výhody starší verze, jako je schopnost sledování dokončení registrace.

Instalační program vytvoří naplánovanou úlohu v systému, na kterém běží v kontextu uživatele. Úloha se aktivuje, když uživatel přihlásí do Windows. Úloha tiše připojí zařízení s Azure AD s přihlašovacími údaji uživatele po ověření pomocí Azure AD.

Pokud chcete nastavit registraci zařízení, měli byste nasadit balíček Instalační služby systému Windows do vybrané skupiny zařízení Windows nižší úrovně.

> [!NOTE]
> Pokud spojovací bod služby SCP není nakonfigurovaná ve službě AD a jak je popsáno na postupujte stejným způsobem [nakonfigurovat nastavení registru na straně klienta pro spojovací bod služby](#configure-client-side-registry-setting-for-scp)) v počítači připojeném k doméně pomocí objektů zásad skupiny (GPO).


Po ověření, že vše funguje podle očekávání, můžete zaregistrovat automaticky rest zařízení s Windows aktuální a nižší úrovně s Azure AD pomocí [konfiguraci spojovacího bodu služby pomocí služby Azure AD Connect](hybrid-azuread-join-managed-domains.md#configure-hybrid-azure-ad-join).

## <a name="next-steps"></a>Další postup

[Naplánování vaší implementace připojení k hybridní službě Azure Active Directory](hybrid-azuread-join-plan.md)
