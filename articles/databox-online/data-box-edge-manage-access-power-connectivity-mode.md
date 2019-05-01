---
title: Přístup k zařízením Azure Data Box Edge, napájení a režimu připojení | Dokumentace Microsoftu
description: Popisuje, jak spravovat přístup, napájení a režimu připojení pro Azure Data Box hraniční zařízení, že pomáhá přenášet data do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/25/2019
ms.author: alkohli
ms.openlocfilehash: 5fbe8f3eb05ac60918e488c68869c3fe44051a3f
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924370"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>Správa přístupu, napájení a režimu připojení pro Azure Data Box hranici

Tento článek popisuje, jak spravovat přístup, power a připojení režimu pro váš Azure Data Box Edge. Tato operace se provádí prostřednictvím místního webového uživatelského rozhraní nebo na webu Azure portal.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Spravovat přístup zařízení
> * Správa režim připojení
> * Správa napájení


## <a name="manage-device-access"></a>Spravovat přístup zařízení

Přístup k zařízení Data Box Edge je řízen pomocí hesla zařízení. Můžete změnit heslo prostřednictvím místního webového uživatelského rozhraní. Můžou také resetovat heslo zařízení na portálu Azure portal.

### <a name="change-device-password"></a>Změna hesla zařízení

Postupujte podle těchto kroků v Uživatelském rozhraní místní, chcete-li změnit heslo zařízení.

1. V místním webovém uživatelském rozhraní, přejděte na **údržby > Změna hesla**.
2. Zadejte aktuální heslo a pak nové heslo. Zadané heslo musí být dlouhé 8 až 16 znaků. Heslo musí obsahovat 3 z následujících znaků: velká písmena, malá písmena, číselné a speciální znaky. Potvrzení nového hesla.

    ![Změnit heslo](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Vyberte **změnit heslo**.
 
### <a name="reset-device-password"></a>Resetovat heslo zařízení

Obnovení pracovního postupu není nutné, aby si možná Vzpomínáte staré heslo a je užitečné v případě ztráty hesla není. Tento pracovní postup se provádí na webu Azure Portal.

1. Na webu Azure Portal, přejděte na **přehled > resetovat heslo správce**.

    ![Resetování hesla](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Zadejte nové heslo a potvrďte jej. Zadané heslo musí být dlouhé 8 až 16 znaků. Heslo musí obsahovat 3 z následujících znaků: velká písmena, malá písmena, číselné a speciální znaky. Vyberte **resetování**.

    ![Resetování hesla](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>Správa režim připojení

Kromě výchozí plně propojené režim můžete zařízení také spustit v režimu částečně připojeno nebo plně odpojeno. Každá z těchto režimech je popsaný níže:

- **Plně připojení** – to je normální výchozí režim, ve kterém zařízení funguje. Jak cloud nahrání a stažení dat je v tomto režimu nepovolily. Na webu Azure portal nebo místního webového uživatelského rozhraní můžete použít ke správě zařízení.

- **Částečně odpojení** – v tomto režimu nelze zařízení odeslání nebo stažení všechny sdílené složky data ale mohou být spravována přes Azure portal.

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