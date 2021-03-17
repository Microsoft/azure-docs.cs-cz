---
title: Připojení nového zařízení s Windows 10 k Azure AD při prvním spuštění | Microsoft Docs
description: Jak můžou uživatelé nastavit službu Azure AD JOIN během nedostatku možnosti.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: da37316724bf6ef166f08faa7208ad196000bb00
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85253099"
---
# <a name="tutorial-join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Kurz: Připojení nového zařízení s Windows 10 k Azure AD při prvním spuštění

Díky správě zařízení v Azure Active Directory (Azure AD) můžete zajistit, aby uživatelé přistupovali k vašim prostředkům ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. Další informace najdete v [úvodu do správy zařízení v Azure Active Directory](overview.md).

Windows 10 umožňuje připojení nového zařízení k Azure AD při prvním spuštění.  
Díky tomu můžete svým zaměstnancům nebo studentům distribuovat zařízení zabalená ve smršťovací fólii.

Pokud máte na zařízení nainstalovaný systém Windows 10 Professional nebo Windows 10 Enterprise, výchozím prostředím pro zařízení vlastněná společností bude proces instalace.

V *prostředí prvního spuštění* Windows se nepodporuje připojení k místní doméně Active Directory (AD). Pokud plánujete připojit počítač k doméně AD, během instalace byste měli vybrat odkaz **Nastavit Windows s místním účtem**. Pak se můžete k doméně připojit přes nastavení na počítači.
 
V tomto kurzu se dozvíte, jak připojit zařízení k Azure AD při prvním spuštění:
 > [!div class="checklist"]
> * Požadavky
> * Připojení zařízení
> * Ověření

## <a name="prerequisites"></a>Požadavky

Abyste mohli připojit zařízení s Windows 10, musíte mít v konfiguraci služby registrace zařízení povoleno registrovat zařízení. Kromě oprávnění k připojování zařízení ve vašem tenantovi Azure AD musíte mít zaregistrovaných méně zařízení, než je nakonfigurované maximum. Další informace najdete v tématu popisujícím [konfiguraci nastavení zařízení](device-management-azure-portal.md#configure-device-settings).

Pokud je váš tenant federovaný, váš zprostředkovatel identity navíc MUSÍ podporovat koncový bod pro zadání uživatelského jména a hesla WS-Fed a WS-Trust. Jeho verze může být 1.3 nebo 2005. Tato podpora protokolu se vyžaduje, aby se zařízení připojilo k Azure AD a přihlásilo se k zařízení pomocí hesla.

## <a name="joining-a-device"></a>Připojení zařízení

**Připojení zařízení s Windows 10 k Azure AD při prvním spuštění:**

1. Po zapnutí nového zařízení a spuštění procesu instalace by se měla zobrazit zpráva **Probíhá příprava**. Postupujte podle zobrazených výzev a nastavte své zařízení.
1. Začněte přizpůsobením oblasti a jazyka. Pak přijměte Licenční podmínky pro software společnosti Microsoft.
 
    ![Přizpůsobení oblasti](./media/azuread-joined-devices-frx/01.png)

1. Vyberte síť, kterou chcete použít k připojování k internetu.
1. Klikněte na **Toto zařízení patří mojí organizaci**. 

    ![Obrazovka Kdo vlastní tento počítač](./media/azuread-joined-devices-frx/02.png)

1. Zadejte přihlašovací údaje, které jste dostali od své organizace, a pak klikněte na **Přihlásit se**.

    ![Přihlašovací obrazovka](./media/azuread-joined-devices-frx/03.png)

1. Vaše zařízení vyhledá odpovídajícího tenanta ve službě Azure AD. Pokud se nacházíte ve federované doméně, budete přesměrování na váš místní server služby tokenů zabezpečení, jako je například služba Active Directory Federation Services (AD FS).
1. Pokud jste uživatelem v jiné než federované doméně, zadejte své přihlašovací údaje přímo na stránce hostované v Azure AD. 
1. Zobrazí se výzva k dokončení vícefaktorového ověřování. 
1. Azure AD zkontroluje, jestli se vyžaduje registrace ve správě mobilních zařízení.
1. Windows zaregistruje zařízení v adresáři organizace v Azure AD a v případě potřeby i ve správě mobilních zařízení.
1. Pokud jste:
   - Spravovaný uživatel: Windows vás automaticky přihlásí a zobrazí plochu.
   - Federovaný uživatel: Budete přesměrování na přihlašovací obrazovku Windows, kde můžete zadat své přihlašovací údaje.

## <a name="verification"></a>Ověření

Pokud chcete ověřit, jestli je zařízení připojené k vaší službě Azure AD, zkontrolujte na zařízení s Windows dialogové okno **Přístup do práce nebo do školy**. V tomto dialogovém okně by se mělo zobrazit, že jste připojeni k vašemu adresáři Azure AD.

![Přístup do práce nebo do školy](./media/azuread-joined-devices-frx/13.png)

## <a name="next-steps"></a>Další kroky

- Další informace najdete v [úvodu do správy zařízení v Azure Active Directory](overview.md).
- Další informace o správě zařízení na portálu Azure AD najdete v tématu věnovaném [správě zařízení pomocí webu Azure Portal](device-management-azure-portal.md).
