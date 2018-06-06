---
title: Připojení k nové zařízení Windows 10 s Azure AD při prvním spuštění | Microsoft Docs
description: Téma, které vysvětluje, jak uživatelé mohou vytvořit připojení ke službě Azure AD během prostředí prvního spuštění.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.component: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: 0341c5b65816cf1c54fe3f2f7781dde46bf084ad
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34757388"
---
# <a name="join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Připojení k nové zařízení Windows 10 s Azure AD při prvním spuštění

Se správou zařízení ve službě Azure Active Directory (Azure AD) můžete zajistit, že vaši uživatelé přistupují k prostředkům ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů. Další podrobnosti najdete v tématu [Úvod do správy zařízení v Azure Active Directory](device-management-introduction.md).

S Windows 10 připojíte k nové zařízení do služby Azure AD při prvním spuštění počítače (FRX).  
To umožňuje distribuci pečlivě zabaleny zařízení zaměstnancům nebo studenty.

Pokud máte Windows 10 Professional nebo Windows 10 Enterprise na zařízení nainstalovaná, výchozí možnosti procesu instalace zařízení ve vlastnictví společnosti.

V systému Windows *out-of-box prostředí*, připojení k místní doméně Active Directory (AD) není podporován. Pokud máte v plánu připojit počítač k doméně AD během instalace, měli byste vybrat odkaz **nastavit Windows s místní účet**. Můžete pak připojit k doméně z nastavení v počítači.
 


## <a name="before-you-begin"></a>Než začnete

K připojení k zařízením s Windows 10, je třeba službu device registration service vám umožní zaregistrovat zařízení. Kromě nutnosti oprávnění k připojení zařízení v klientovi služby Azure AD, musí mít méně zařízení registrovaná než nakonfigurované maximum. Další podrobnosti najdete v tématu [nakonfigurovat nastavení zařízení](device-management-azure-portal.md#configure-device-settings).

Kromě toho pokud váš klient je federovaný, zprostředkovatele Identity musí podporovat WS-Fed a WS-Trust koncový bod uživatelského jména a hesla. To může být verze 1.3 nebo 2005. Tato podpora protokolu je potřeba připojit zařízení k Azure AD i Přihlaste se k zařízení s heslem.

## <a name="joining-a-device"></a>Připojení zařízení

**Pro připojení k Azure AD během FRX zařízením s Windows 10:**


1. Když zapnete nové zařízení a zahájení procesu instalace, měli byste vidět **získávání připravené** zprávy. Postupujte podle výzev a nastavit vaše zařízení.

2. Spuštění zadáním oblast a jazyk. Přijměte licenční podmínky pro Software společnosti Microsoft.
 
    ![Přizpůsobit pro vaši oblast](./media/device-management-azuread-joined-devices-frx/01.png)

3. Vyberte síť, kterou chcete použít pro připojení k Internetu.

4. Klikněte na tlačítko **toto zařízení patří mojí organizaci**. 

    ![Kdo je vlastníkem této obrazovce počítače](./media/device-management-azuread-joined-devices-frx/02.png)

5. Zadejte pověření, které byly vám poskytla vaše organizace a pak klikněte na tlačítko **přihlášení**.

    ![Přihlašovací obrazovky](./media/device-management-azuread-joined-devices-frx/03.png)

6. Zařízení můžete vyhledat odpovídající klienta ve službě Azure AD. Pokud jste v federovanou doménu, budete přesměrováni na místním zabezpečení tokenu služby (STS) serveru, například Active Directory Federation Services (AD FS).

7. Pokud jste uživatele v doméně nefederovaných, zadejte přihlašovací údaje přímo na stránce AD hostované v Azure. 

8. Zobrazí se výzva pro výzvu ověřování Multi-Factor authentication. 
 
9. Azure AD zkontroluje, zda je požadovaný zápisu správy mobilních zařízení.

10. Systém Windows se zařízení registruje do adresáře organizace v Azure AD a zaregistruje správy mobilních zařízení, pokud je k dispozici.

11. Pokud jste:
    - Spravované uživatele Windows přejdete na ploše prostřednictvím automatický proces přihlášení.

    - Federované uživatele, budete přesměrováni na přihlašovací obrazovce Windows k zadání pověření.

## <a name="verification"></a>Ověření

Pokud chcete ověřit, jestli se zařízení připojí ke službě Azure AD, přečtěte si **přístup k práci nebo ve škole** dialog v zařízení se systémem Windows. Dialogové okno by měl znamenat, že jste připojeni k adresáři služby Azure AD.

![Přístup k práci nebo ve škole](./media/device-management-azuread-joined-devices-frx/13.png)


## <a name="next-steps"></a>Další postup

- Další podrobnosti najdete v tématu [Úvod do správy zařízení v Azure Active Directory](device-management-introduction.md).

- Další informace o správě zařízení na portálu Azure AD najdete v tématu [Správa zařízení pomocí webu Azure portal](device-management-azure-portal.md).
