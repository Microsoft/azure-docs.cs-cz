---
title: Azure Data Box Gateway přístup k zařízení, napájení a režim připojení
description: Popisuje, jak spravovat přístup, režim napájení a režim připojení pro zařízení Azure Data Box Gateway, které pomáhá přenést data do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 06/03/2019
ms.author: alkohli
ms.openlocfilehash: 27b6d8ca61ed10b5c7362e089fe94d8d64164878
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743858"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway"></a>Správa režimu přístupu, napájení a připojení pro Azure Data Box Gateway

Tento článek popisuje, jak spravovat režim přístupu, napájení a připojení k vašemu Azure Data Box Gateway. Tyto operace se provádějí prostřednictvím místního webového uživatelského rozhraní nebo Azure Portal. 

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> * Správa přístupu k zařízení
> * Správa režimu připojení
> * Správa napájení

## <a name="manage-device-access"></a>Správa přístupu k zařízení

Přístup k vašemu Data Box Gateway zařízení je řízen pomocí hesla zařízení. Heslo můžete změnit prostřednictvím místního webového uživatelského rozhraní. Můžete také resetovat heslo zařízení v Azure Portal.

### <a name="change-device-password"></a>Změna hesla zařízení

Pokud chcete změnit heslo zařízení, postupujte podle těchto kroků v místním uživatelském rozhraní.

1. V místním webovém uživatelském rozhraní přejděte do části **údržba > Změna hesla**.
2. Zadejte aktuální heslo a pak nové heslo. Zadané heslo musí mít 8 až 16 znaků. Heslo musí obsahovat 3 z následujících znaků: velká písmena, malá písmena, číslice a speciální znaky. Potvrďte nové heslo.

    ![Změna hesla](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Klikněte na **změnit heslo**.
 
### <a name="reset-device-password"></a>Resetování hesla zařízení

Pracovní postup Resetování nevyžaduje, aby uživatel nahrál staré heslo a byl užitečný při ztrátě hesla. Tento pracovní postup se provádí v Azure Portal.

1. V Azure Portal najdete v části **přehled > resetování hesla správce**.

    ![Resetování hesla](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Zadejte nové heslo a potvrďte ho. Zadané heslo musí mít 8 až 16 znaků. Heslo musí obsahovat 3 z následujících znaků: velká písmena, malá písmena, číslice a speciální znaky. Klikněte na **resetovat**.

    ![Resetování hesla 2](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Správa přístup k prostředků

Pokud chcete vytvořit Azure Stack Edge pro/Data Box Gateway, IoT Hub a Azure Storage prostředků, potřebujete oprávnění jako přispěvatel nebo vyšší na úrovni skupiny prostředků. Budete také potřebovat registrovat odpovídající poskytovatele prostředků. Pro všechny operace, které zahrnují aktivační klíč a přihlašovací údaje, se vyžadují taky oprávnění Azure Active Directory Graph API. Tyto oddíly jsou popsány v následujících částech.

### <a name="manage-microsoft-graph-api-permissions"></a>Správa oprávnění rozhraní API Microsoft Graph

Při generování aktivačního klíče pro zařízení Azure Stack Edge pro nebo provádění operací, které vyžadují přihlašovací údaje, potřebujete oprávnění Microsoft Graph API. Operace, které vyžadují přihlašovací údaje, můžou být:

-  Vytváří se sdílená složka s přidruženým účtem úložiště.
-  Vytváří se uživatel, který má přístup ke sdíleným složkám na zařízení.

Měli byste mít `User` přístup k Tenantovi služby Active Directory, jak potřebujete `Read all directory objects` . Nemůžete být uživatel typu Host, protože k němu nemají oprávnění `Read all directory objects` . Pokud jste Host, pak operace, jako je například generace aktivačního klíče, vytvoření sdílené složky na zařízení Azure Stack Edge pro, vytvoření uživatele se nezdaří.

Další informace o tom, jak poskytnout uživatelům přístup k Microsoft Graph rozhraní API, najdete v tématu [Microsoft Graph oprávnění](https://docs.microsoft.com/graph/permissions-reference).

### <a name="register-resource-providers"></a>Registrovat poskytovatele prostředků

Pokud chcete zřídit prostředek v Azure (v Azure Resource Manager modelu), potřebujete poskytovatele prostředků, který podporuje vytvoření tohoto prostředku. Pokud například chcete zřídit virtuální počítač, měli byste mít k dispozici poskytovatele prostředků Microsoft. COMPUTE v předplatném.
 
Poskytovatelé prostředků se registrují na úrovni předplatného. Ve výchozím nastavení je ve všech nových předplatných Azure předem zaregistrovaný seznam běžně využívaných poskytovatelů prostředků. Poskytovatel prostředků pro Microsoft. DataBoxEdge není zahrnutý v tomto seznamu.

Pro uživatele, kteří mají oprávnění k vytváření prostředků, jako je Microsoft. DataBoxEdge, nemusíte v rámci skupin prostředků, ke kterým mají oprávnění vlastníka, přidělovat oprávnění k přístupu, pokud už jsou poskytovatelé prostředků pro tyto prostředky zaregistrovaní.

Než se pokusíte vytvořit nějaký prostředek, ujistěte se, že je v předplatném zaregistrován poskytovatel prostředků. Pokud poskytovatel prostředků není zaregistrovaný, musíte se ujistit, že uživatel vytvářející nový prostředek má dostatečná práva k registraci požadovaného poskytovatele prostředků na úrovni předplatného. Pokud jste to ještě neudělali, zobrazí se následující chyba:

*Předplatné nemá \<Subscription name> oprávnění registrovat poskytovatele prostředků: Microsoft. DataBoxEdge.*


Pokud chcete získat seznam registrovaných poskytovatelů prostředků v aktuálním předplatném, spusťte následující příkaz:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Pro zařízení Azure Stack Edge pro `Microsoft.DataBoxEdge` by se měla zaregistrovat. Pokud se chcete zaregistrovat `Microsoft.DataBoxEdge` , Správce předplatného by měl spustit tento příkaz:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Další informace o tom, jak zaregistrovat poskytovatele prostředků, najdete v tématu [řešení chyb pro registraci poskytovatele prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Správa režimu připojení

Kromě výchozího normálního režimu může být zařízení také spuštěné v částečně odpojeném nebo odpojeném režimu. Každý z těchto režimů je popsaný níže:

- **Částečně odpojeno** – v tomto režimu nemůže zařízení nahrávat žádná data do sdílených složek, ale dá se spravovat prostřednictvím Azure Portal.

    Tento režim se obvykle používá, pokud je v měřené satelitní síti a cílem je minimalizovat spotřebu šířky pásma sítě. Minimální spotřeba sítě může stále nastat pro operace monitorování zařízení.

- **Odpojeno** – v tomto režimu je zařízení zcela odpojené od cloudu a jejich cloudové odesílání a stahování je zakázané. Zařízení je možné spravovat jenom prostřednictvím místního webového uživatelského rozhraní.

    Tento režim se obvykle používá, pokud chcete zařízení převést do režimu offline.

Chcete-li změnit režim zařízení, postupujte podle následujících kroků:

1. V místním webovém uživatelském rozhraní vašeho zařízení přejít na **konfigurace > nastavení cloudu**.
2. Zakažte **nahrávání a stahování cloudu**.
3. Pokud chcete zařízení spustit v režimu částečně odpojeno, povolte **správu Azure Portal**.

    ![Režim připojení](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Pokud chcete zařízení spustit v odpojeném režimu, zakažte **správu Azure Portal**. Zařízení se teď dá spravovat jenom přes místní webové uživatelské rozhraní.

    ![Režim připojení 2](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Správa napájení

Virtuální zařízení můžete vypnout nebo restartovat pomocí místního webového uživatelského rozhraní. Doporučujeme před restartováním přepnout sdílené složky na hostiteli a potom na zařízení do offline režimu. Tato akce minimalizuje možnost poškození dat.

1. V místním webovém uživatelském rozhraní přejdete do části **údržba > nastavení napájení**.
2. V závislosti na tom, co máte v úmyslu udělat, klikněte na **vypnout** nebo **restartovat** .

    ![Nastavení napájení](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Po zobrazení výzvy k potvrzení klikněte na **Ano** a pokračujte.

> [!NOTE]
> Pokud vypnete virtuální zařízení, budete muset spustit zařízení prostřednictvím správy hypervisoru.
