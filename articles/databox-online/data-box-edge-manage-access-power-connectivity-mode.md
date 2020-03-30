---
title: Režim přístupu k zařízení Azure Data Box Edge, napájení a připojení | Dokumenty společnosti Microsoft
description: Popisuje, jak spravovat režim přístupu, napájení a připojení pro zařízení Azure Data Box Edge, které pomáhá přenášet data do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: be4b780161003470622cb367d78138cfeffe341b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454328"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>Správa režimu přístupu, napájení a připojení pro azure data box edge

Tento článek popisuje, jak spravovat režim přístupu, napájení a připojení pro azure data box edge. Tyto operace se provádějí prostřednictvím místního webového uživatelského prostředí nebo portálu Azure.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Správa přístupu k zařízení
> * Správa režimu připojení
> * Správa napájení


## <a name="manage-device-access"></a>Správa přístupu k zařízení

Přístup k zařízení Data Box Edge je řízen použitím hesla zařízení. Heslo můžete změnit prostřednictvím místního webového uživatelského prostředí. Heslo zařízení můžete také resetovat na webu Azure Portal.

### <a name="change-device-password"></a>Změna hesla zařízení

Chcete-li změnit heslo zařízení, postupujte takto v místním ui.

1. V místním webovém uživatelském okně přejděte na **změnit heslo údržby > .**
2. Zadejte aktuální heslo a potom nové heslo. Zadané heslo musí mít 8 až 16 znaků. Heslo musí mít 3 následující znaky: velká, malá, číselná a speciální znatá. Potvrďte nové heslo.

    ![Změna hesla](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Vyberte **možnost Změnit heslo**.
 
### <a name="reset-device-password"></a>Resetovat heslo zařízení

Pracovní postup obnovení nevyžaduje, aby uživatel odvolal staré heslo, a je užitečný při ztrátě hesla. Tento pracovní postup se provádí na webu Azure Portal.

1. Na webu Azure Portal přejděte na **Přehled > resetování hesla správce**.

    ![Resetování hesla](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Zadejte nové heslo a potvrďte ho. Zadané heslo musí mít 8 až 16 znaků. Heslo musí mít 3 následující znaky: velká, malá, číselná a speciální znatá. Vyberte **Resetovat**.

    ![Resetování hesla](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Správa přístup k prostředků

K vytvoření brány Edge/Data Box Gateway datové schránky, služby IoT Hub a prostředků úložiště Azure potřebujete oprávnění jako přispěvatel nebo vyšší na úrovni skupiny prostředků. Potřebujete také zaregistrovat odpovídající poskytovatele prostředků. Pro všechny operace, které zahrnují aktivační klíč a pověření, jsou také vyžadována oprávnění k rozhraní Microsoft Graph API. Ty jsou popsány v následujících částech.

### <a name="manage-microsoft-graph-api-permissions"></a>Správa oprávnění rozhraní MICROSOFT Graph API

Při generování aktivačního klíče pro zařízení Data Box Edge nebo při provádění operací, které vyžadují pověření, potřebujete oprávnění k rozhraní Microsoft Graph API. Operace, které potřebují pověření může být:

-  Vytvoření sdílené složky s přidruženým účtem úložiště.
-  Vytvoření uživatele, který má přístup ke sdíleným položkym v zařízení.

Měli byste `User` mít přístup k tenantovi služby `Read all directory objects`Active Directory, protože potřebujete mít přístup k aplikaci . Nemůžete být uživatelem typu Host, protože nemá `Read all directory objects`oprávnění k . Pokud jste hostem, pak operace, jako je generování aktivačního klíče, vytvoření sdílené složky na zařízení Data Box Edge, vytvoření uživatele, konfigurace výpočetní role Edge, resetování hesla zařízení, se nezdaří.

Další informace o tom, jak poskytnout uživatelům přístup k rozhraní Microsoft Graph API, naleznete v [tématu Odkaz na oprávnění aplikace Microsoft Graph](https://docs.microsoft.com/graph/permissions-reference).

### <a name="register-resource-providers"></a>Registrace poskytovatelů prostředků

Chcete-li zřídit prostředek v Azure (v modelu Azure Resource Manager), budete potřebovat poskytovatele prostředků, který podporuje vytváření tohoto prostředku. Chcete-li například zřídit virtuální počítač, měli byste mít v předplatném k dispozici zprostředkovatele prostředků Microsoft.Compute.
 
Poskytovatelé prostředků se registrují na úrovni předplatného. Ve výchozím nastavení je ve všech nových předplatných Azure předem zaregistrovaný seznam běžně využívaných poskytovatelů prostředků. Zprostředkovatel prostředků pro 'Microsoft.DataBoxEdge' není zahrnuta v tomto seznamu.

Není nutné udělit přístupová oprávnění k úrovni předplatného pro uživatele, aby mohli vytvářet prostředky, jako je Microsoft.DataBoxEdge v rámci svých skupin prostředků, které mají práva vlastníka, tak dlouho, dokud zprostředkovatelé prostředků pro tyto prostředky je již Registrované.

Než se pokusíte vytvořit libovolný prostředek, ujistěte se, že poskytovatel prostředků je registrován v předplatném. Pokud poskytovatel prostředků není registrován, budete se muset ujistit, že uživatel, který vytváří nový prostředek, má dostatečná práva k registraci požadovaného poskytovatele prostředků na úrovni předplatného. Pokud jste to také neudělali, uvidíte následující chybu:

*Název \<předplatného předplatného> nemá oprávnění k registraci zprostředkovatele prostředků: Microsoft.DataBoxEdge.*


Chcete-li získat seznam registrovaných poskytovatelů prostředků v aktuálním předplatném, spusťte následující příkaz:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Pro zařízení Data `Microsoft.DataBoxEdge` Box Edge by měla být registrována. Chcete-li se zaregistrovat `Microsoft.DataBoxEdge`, správce předplatného by měl spustit následující příkaz:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Další informace o registraci zprostředkovatele prostředků naleznete v tématu [Řešení chyb při registraci poskytovatele prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Správa režimu připojení

Kromě výchozího plně připojeného režimu může zařízení běžet také v částečně připojeném nebo plně odpojeném režimu. Každý z těchto režimů je popsán takto:

- **Plně připojeno** - Jedná se o normální výchozí režim, ve kterém zařízení pracuje. V tomto režimu je povoleno nahrávání a stahování dat v cloudu. Zařízení můžete spravovat pomocí portálu Azure nebo místního webového uživatelského rozhraní.

- **Částečně odpojené** – V tomto režimu zařízení nemůže nahrát nebo stáhnout žádná data sdílení, ale lze spravovat prostřednictvím portálu Azure.

    Tento režim se obvykle používá v satelitní síti účtované podle objemu dat a cílem je minimalizovat spotřebu šířky pásma sítě. Pro operace monitorování zařízení může stále dojít k minimální spotřebě sítě.

- **Odpojeno** – V tomto režimu je zařízení plně odpojeno od cloudu a nahrávání a stahování v cloudu jsou zakázány. Zařízení lze spravovat pouze prostřednictvím místního webového uživatelského rozhraní.

    Tento režim se obvykle používá, když chcete zařízení převést do režimu offline.

Chcete-li změnit režim zařízení, postupujte takto:

1. V místním webovém uživatelském rozhraní vašeho zařízení přejděte na **nastavení konfigurace > cloudu**.
2. V rozevíracím seznamu vyberte režim, ve kterém chcete zařízení ovládat. Můžete si vybrat z **plně připojené**, **částečně připojené**a **plně odpojené**. Chcete-li zařízení spustit v částečně odpojeném režimu, povolte **správu portálu Azure**.

    ![Režim připojení](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Správa napájení

Fyzické zařízení můžete vypnout nebo restartovat pomocí místního webového uživatelského rozhraní. Doporučujeme, abyste před restartováním přepáčte sdílené složky do offline na datovém serveru a potom na zařízení. Tato akce minimalizuje možnost poškození dat.

1. V místním webovém uživatelském prostředí přejděte na **nastavení údržby > napájení**.
2. V yberte **Vypnutí** nebo **Restartování** v závislosti na tom, co chcete udělat.

    ![Nastavení napájení](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Po zobrazení výzvy k potvrzení pokračujte výběrem možnosti **Ano.**

> [!NOTE]
> Pokud vypnete fyzické zařízení, budete muset stisknout tlačítko napájení na zařízení, abyste ho zapnuli.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [spravovat sdílené složky](data-box-edge-manage-shares.md).
