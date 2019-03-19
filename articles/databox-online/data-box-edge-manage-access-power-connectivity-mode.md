---
title: Přístup k Microsoft Azure Data Box hraniční zařízení, napájení a režimu připojení | Dokumentace Microsoftu
description: Popisuje, jak spravovat přístup, napájení a režimu připojení pro Azure Data Box hraniční zařízení, že pomáhá přenášet data do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/11/2019
ms.author: alkohli
ms.openlocfilehash: e0aa3468bda9f904d62e9e20545ac5f990cef521
ms.sourcegitcommit: b8f9200112cae265155b8877f7e1621c4bcc53fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2019
ms.locfileid: "57902057"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge-preview"></a>Správa přístupu, napájení a režimu připojení pro Azure Data Box hranici (preview)

Tento článek popisuje, jak spravovat přístup, power a připojení režimu pro váš Azure Data Box Edge. Tato operace se provádí prostřednictvím místního webového uživatelského rozhraní nebo na webu Azure portal.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Spravovat přístup zařízení
> * Správa režim připojení
> * Správa napájení

> [!IMPORTANT]
> Data Box Edge je ve verzi Preview. Před objednáním a nasazením tohoto řešení si přečtěte [podmínky užívání pro předběžné verze systému Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="manage-device-access"></a>Spravovat přístup zařízení

Přístup k zařízení Data Box Edge se řídí použití hesla správce zařízení. Můžete změnit heslo správce prostřednictvím místního webového uživatelského rozhraní. Můžou také resetovat hesla správce zařízení na portálu Azure portal.

### <a name="change-device-administrator-password"></a>Změna hesla správce zařízení

Následující postup v místním uživatelském rozhraní ke změně hesla správce zařízení.

1. V místním webovém uživatelském rozhraní, přejděte na **údržby > Změna hesla**.
2. Zadejte aktuální heslo a pak nové heslo. Zadané heslo musí být dlouhé 8 až 16 znaků. Heslo musí obsahovat 3 z následujících znaků: velká písmena, malá písmena, číselné a speciální znaky. Potvrzení nového hesla.

    ![Změnit heslo](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Vyberte **změnit heslo**.
 
### <a name="reset-device-administrator-password"></a>Resetování hesla správce zařízení

Obnovení pracovního postupu není nutné, aby si možná Vzpomínáte staré heslo a je užitečné v případě ztráty hesla není. Tento pracovní postup se provádí na webu Azure Portal.

1. Na webu Azure Portal, přejděte na **přehled > resetovat heslo správce**.

    ![Resetování hesla](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Zadejte nové heslo a potvrďte jej. Zadané heslo musí být dlouhé 8 až 16 znaků. Heslo musí obsahovat 3 z následujících znaků: velká písmena, malá písmena, číselné a speciální znaky. Vyberte **resetování**.

    ![Resetování hesla](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>Správa režim připojení

Kromě výchozí plně propojené režim můžete zařízení také spustit v režimu částečně připojeno nebo plně odpojeno. Každá z těchto režimech je popsaný níže:

- **Plně připojení** – to je normální výchozí režim, ve kterém zařízení funguje. Jak cloud nahrání a stažení dat je v tomto režimu nepovolily. Na webu Azure portal nebo místního webového uživatelského rozhraní můžete použít ke správě zařízení.

- **Částečně odpojení** – v tomto režimu zařízení nemůžete nahrát žádné data do sdílených složek ale je možné spravovat prostřednictvím portálu Azure portal.

    Tento režim se obvykle používá při práci v síti satelitních Účtovaná podle objemu dat a cílem je minimalizovat využití šířky pásma sítě. Využití sítě minimální může stále dojít k pro monitorování provozu zařízení.

- **Odpojení** – v tomto režimu zařízení je plně odpojen od cloudu a oba cloudu nahrávání a stahování jsou zakázané. Zařízení je možné spravovat pouze prostřednictvím místního webového uživatelského rozhraní.

    Tento režim se obvykle používá, když chcete převést zařízení do režimu offline.

Chcete-li změnit režim zařízení, postupujte podle těchto kroků:

1. V místním webovém uživatelském rozhraní vašeho zařízení, přejděte na **konfigurace > Nastavení Cloud**.
2. Z rozevíracího seznamu vyberte režim, ve kterém chcete pracovat v zařízení. Můžete vybrat z **plně připojení**, **částečně připojeno**, a **plně odpojení**. Spustit v částečně odpojeném režimu zařízení, povolit **Azure portal management**.

    ![Režim připojení](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Správa napájení

Můžete vypnout nebo restartovat fyzických zařízení pomocí místního webového uživatelského rozhraní. Můžeme doporučit, před restartováním, využít sdílené složky offline na serveru pro data a poté zařízení. Tato akce minimalizuje možnost poškození dat.

1. V místním webovém uživatelském rozhraní, přejděte na **údržby > nastavení napájení**.
2. Vyberte **vypnutí** nebo **restartovat** v závislosti na tom, co chcete udělat.

    ![Nastavení napájení](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Po zobrazení výzvy k potvrzení, vyberte **Ano** pokračovat.

> [!NOTE]
> Pokud vypnete fyzické zařízení, musíte vložit na tlačítku napájení v zařízení ho chcete zapnout.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [spravovat sdílené složky](data-box-edge-manage-shares.md).