---
title: Připojte se k nové zařízení s Windows 10 s využitím Azure AD během prvního spuštění | Dokumentace Microsoftu
description: Téma, které vysvětluje, jak uživatelé mohou vytvořit připojení ke službě Azure AD během prvního spuštění prostředí.
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
ms.openlocfilehash: 1376f011d056aac33333f6ac31ee2eaadaf3ef4a
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39414998"
---
# <a name="join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>Připojte se k nové zařízení s Windows 10 s využitím Azure AD během prvního spuštění

Se správou zařízení ve službě Azure Active Directory (Azure AD) můžete zajistit, že vaši uživatelé přistupují ze zařízení, která splňují vaše standardy zabezpečení a dodržování předpisů vašich prostředků. Další informace najdete v tématu [Úvod ke správě zařízení ve službě Azure Active Directory](overview.md).

S Windows 10 musíte připojit nové zařízení do služby Azure AD během prvního spuštění prostředí (FRX).  
To umožňuje distribuovat pečlivě zabaleny zařízení, která zaměstnancům a studentům.

Pokud máte Windows 10 Professional nebo Windows 10 Enterprise nainstalované v zařízení výchozí prostředí procesu instalace pro zařízení vlastněná společností.

V Windows *out-of-box prostředí*, připojení místní doméně Active Directory (AD) se nepodporuje. Pokud máte v plánu připojit počítač k doméně AD během instalace, by měl vybrat odkaz **nastavení Windows místní účet**. Pak můžete připojit k doméně z nastavení v počítači.
 


## <a name="before-you-begin"></a>Než začnete

Připojit zařízení s Windows 10, musíte nakonfigurovat službu device registration service povolit registraci zařízení. Kromě s oprávněními pro připojení k zařízení ve vašem tenantovi Azure AD, musí mít méně zařízení registrovaná než nakonfigurované maximum. Další informace najdete v tématu [nastavení zařízení](device-management-azure-portal.md#configure-device-settings).

Kromě toho pokud je Federovaná vašeho tenanta, zprostředkovatele Identity musí podporovat WS-Fed a WS-Trust koncového bodu uživatelského jména a hesla. To může být verze 1.3 nebo 2005. Tato podpora protokolu je potřeba připojit zařízení k Azure AD i Přihlaste se na zařízení s heslem.

## <a name="joining-a-device"></a>Připojení zařízení

**Připojit zařízení s Windows 10 do služby Azure AD během FRX:**


1. Když zapnete nové zařízení a zahájit proces instalace, měli byste vidět **získávání připravené** zprávy. Postupujte podle pokynů k nastavení vašeho zařízení.

2. Začněte tím, že přizpůsobení oblast a jazyk. Přijměte licenční podmínky pro Software společnosti Microsoft.
 
    ![Přizpůsobit pro vaši oblast](./media/azuread-joined-devices-frx/01.png)

3. Vyberte síť, kterou chcete použít pro připojení k Internetu.

4. Klikněte na tlačítko **toto zařízení patří mojí organizace**. 

    ![Kdo je vlastníkem této obrazovce počítače](./media/azuread-joined-devices-frx/02.png)

5. Zadejte přihlašovací údaje, které vám byly vaše organizace a potom klikněte na tlačítko **přihlášení**.

    ![Přihlašovací obrazovka](./media/azuread-joined-devices-frx/03.png)

6. Zařízení je vyhledá odpovídající tenanta ve službě Azure AD. Pokud jste v federovanou doménu, budete přesměrováni na server v místním zabezpečit službu tokenů (STS), například Active Directory Federation Services (AD FS).

7. Pokud jste uživatelem v jiné než federované domény, zadejte přihlašovací údaje přímo na stránce AD hostované v Azure. 

8. Zobrazí se výzva pro výzvu ověřování službou Multi-Factor Authentication. 
 
9. Azure AD ověří, jestli je potřeba registraci ve službě správy mobilních zařízení.

10. Windows se zařízení registruje do adresáře vaší organizace ve službě Azure AD a zaregistruje ho ve správě mobilních zařízení, pokud je k dispozici.

11. Pokud jste:
    - Spravovaný uživatel, Windows, který vás nasměruje na ploše prostřednictvím procesu automatické přihlášení.

    - Federované uživatele, budete přesměrováni na přihlašovací obrazovce Windows zadejte své přihlašovací údaje.

## <a name="verification"></a>Ověření

Pokud chcete ověřit, jestli se zařízení připojí ke službě Azure AD, přečtěte si **přístup do práce nebo do školy** dialogového okna na zařízení s Windows. Dialogové okno měl označovat, že jste připojeni k adresáři služby Azure AD.

![Přístup do práce nebo do školy](./media/azuread-joined-devices-frx/13.png)


## <a name="next-steps"></a>Další postup

- Další informace najdete v tématu [Úvod ke správě zařízení ve službě Azure Active Directory](overview.md).

- Další informace o správě zařízení na portálu Azure AD najdete v tématu [Správa zařízení pomocí webu Azure portal](device-management-azure-portal.md).
