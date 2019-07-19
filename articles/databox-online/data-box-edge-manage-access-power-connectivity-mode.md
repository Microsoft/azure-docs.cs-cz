---
title: Azure Data Box Edge režimu přístupu, napájení a připojení zařízení | Microsoft Docs
description: Popisuje, jak spravovat přístup, režim napájení a režim připojení pro zařízení Azure Data Box Edge, které pomáhá přenést data do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 7ce4b9dda853e63e427757317abc2f7c878ba3a4
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68253163"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge"></a>Správa režimu přístupu, napájení a připojení pro Azure Data Box Edge

Tento článek popisuje, jak spravovat režim přístupu, napájení a připojení k vašemu Azure Data Box Edge. Tyto operace se provádějí prostřednictvím místního webového uživatelského rozhraní nebo Azure Portal.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Správa přístupu k zařízení
> * Správa režimu připojení
> * Správa napájení


## <a name="manage-device-access"></a>Správa přístupu k zařízení

Přístup k vašemu Data Box Edge zařízení je řízen pomocí hesla zařízení. Heslo můžete změnit prostřednictvím místního webového uživatelského rozhraní. Můžete také resetovat heslo zařízení v Azure Portal.

### <a name="change-device-password"></a>Změna hesla zařízení

Pokud chcete změnit heslo zařízení, postupujte podle těchto kroků v místním uživatelském rozhraní.

1. V místním webovém uživatelském rozhraní přejděte do části **údržba > Změna hesla**.
2. Zadejte aktuální heslo a pak nové heslo. Zadané heslo musí mít 8 až 16 znaků. Heslo musí obsahovat 3 z následujících znaků: velká písmena, malá písmena, číslice a speciální znaky. Potvrďte nové heslo.

    ![Změnit heslo](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Vyberte **změnit heslo**.
 
### <a name="reset-device-password"></a>Resetování hesla zařízení

Pracovní postup Resetování nevyžaduje, aby uživatel nahrál staré heslo a byl užitečný při ztrátě hesla. Tento pracovní postup se provádí v Azure Portal.

1. V Azure Portal najdete v části **přehled > resetování hesla správce**.

    ![Resetovat heslo](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Zadejte nové heslo a potvrďte ho. Zadané heslo musí mít 8 až 16 znaků. Heslo musí obsahovat 3 z následujících znaků: velká písmena, malá písmena, číslice a speciální znaky. Vyberte **obnovit**.

    ![Resetovat heslo](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Správa přístupu k prostředkům

Pokud chcete vytvořit Data Box Edge/Data Box Gateway, IoT Hub a Azure Storage prostředků, potřebujete oprávnění jako přispěvatel nebo vyšší na úrovni skupiny prostředků. Budete také potřebovat registrovat odpovídající poskytovatele prostředků. Pro všechny operace, které zahrnují aktivační klíč a přihlašovací údaje, se vyžadují taky oprávnění Azure Active Directory Graph API. Tyto oddíly jsou popsány v následujících částech.

### <a name="manage-microsoft-azure-active-directory-graph-api-permissions"></a>Správa oprávnění rozhraní API Microsoft Azure Active Directory Graph

Při generování aktivačního klíče pro zařízení Data Box Edge nebo při provádění operací, které vyžadují přihlašovací údaje, potřebujete oprávnění Azure Active Directory Graph API. Operace, které vyžadují přihlašovací údaje, můžou být:

-  Vytváří se sdílená složka s přidruženým účtem úložiště.
-  Vytváří se uživatel, který má přístup ke sdíleným složkám na zařízení.

Měli byste mít `User` přístup k tenantovi služby Active Directory, jak potřebujete `Read all directory objects`. Nemůžete být uživatel typu Host, protože k `Read all directory objects`němu nemají oprávnění. Pokud jste Host, pak operace, jako je generace aktivačního klíče, vytvoření sdílené složky na zařízení Data Box Edge, vytvoření uživatele, konfigurace hraniční výpočetní role, resetování hesla zařízení selže.

Další informace o tom, jak poskytnout uživatelům přístup k Azure Active Directory Graph API, najdete v tématu [výchozí přístup pro správce, uživatele a uživatele typu Host](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).

### <a name="register-resource-providers"></a>Zaregistrovat poskytovatele prostředků

Pokud chcete zřídit prostředek v Azure (v Azure Resource Manager modelu), potřebujete poskytovatele prostředků, který podporuje vytvoření tohoto prostředku. Pokud například chcete zřídit virtuální počítač, měli byste mít k dispozici poskytovatele prostředků Microsoft. COMPUTE v předplatném.
 
Poskytovatelé prostředků se registrují na úrovni předplatného. Ve výchozím nastavení je ve všech nových předplatných Azure předem zaregistrovaný seznam běžně využívaných poskytovatelů prostředků. Poskytovatel prostředků pro Microsoft. DataBoxEdge není zahrnutý v tomto seznamu.

Pro uživatele, kteří mají oprávnění k vytváření prostředků, jako je Microsoft. DataBoxEdge, nemusíte v rámci svých skupin prostředků, ke kterým mají oprávnění vlastníka, přidělovat oprávnění k přístupu, pokud už jsou poskytovatelé prostředků pro tyto prostředky v platnosti. registrovat.

Než se pokusíte vytvořit nějaký prostředek, ujistěte se, že je v předplatném zaregistrován poskytovatel prostředků. Pokud poskytovatel prostředků není zaregistrovaný, musíte se ujistit, že uživatel vytvářející nový prostředek má dostatečná práva k registraci požadovaného poskytovatele prostředků na úrovni předplatného. Pokud jste to ještě neudělali, zobrazí se následující chyba:

*\<Název předplatného předplatného > nemá oprávnění registrovat poskytovatele prostředků: Microsoft.DataBoxEdge.*


Pokud chcete získat seznam registrovaných poskytovatelů prostředků v aktuálním předplatném, spusťte následující příkaz:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Pro data box Edge zařízení `Microsoft.DataBoxEdge` by měla být zaregistrovaná. Pokud se `Microsoft.DataBoxEdge`chcete zaregistrovat, Správce předplatného by měl spustit tento příkaz:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Další informace o tom, jak zaregistrovat poskytovatele prostředků, najdete v tématu [řešení chyb pro registraci poskytovatele prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

## <a name="manage-connectivity-mode"></a>Správa režimu připojení

Kromě výchozího režimu s plným připojením může být zařízení také spuštěné v částečně připojeném nebo plně odpojeném režimu. Každý z těchto režimů je popsaný níže:

- **Plně připojeno** – jedná se o normální výchozí režim, ve kterém zařízení funguje. V tomto režimu je povolené nahrávání do cloudu i stahování dat. Ke správě zařízení můžete použít Azure Portal nebo místní webové uživatelské rozhraní.

- **Částečně odpojeno** – v tomto režimu nemůže zařízení nahrávat ani stahovat žádná sdílená data, ale dá se spravovat prostřednictvím Azure Portal.

    Tento režim se obvykle používá, pokud je v měřené satelitní síti a cílem je minimalizovat spotřebu šířky pásma sítě. Minimální spotřeba sítě může stále nastat pro operace monitorování zařízení.

- **Odpojeno** – v tomto režimu je zařízení zcela odpojené od cloudu a jejich cloudové odesílání a stahování je zakázané. Zařízení je možné spravovat jenom prostřednictvím místního webového uživatelského rozhraní.

    Tento režim se obvykle používá, pokud chcete zařízení převést do režimu offline.

Chcete-li změnit režim zařízení, postupujte podle následujících kroků:

1. V místním webovém uživatelském rozhraní vašeho zařízení přejít na **konfigurace > Nastavení cloudu**.
2. V rozevíracím seznamu vyberte režim, ve kterém má být zařízení provozováno. Můžete vybrat z **úplně připojeného**, **částečně připojeného**a **úplného odpojení**. Pokud chcete zařízení spustit v režimu částečně odpojeno, povolte **správu Azure Portal**.

    ![Režim připojení](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Správa napájení

Můžete vypnout nebo restartovat fyzické zařízení pomocí místního webového uživatelského rozhraní. Doporučujeme, abyste před restartováním převedli sdílené složky na datovém serveru a pak na zařízení. Tato akce minimalizuje jakoukoli možnost poškození dat.

1. V místním webovém uživatelském rozhraní přejdete do části **údržba > nastavení napájení**.
2. V závislosti na tom, co máte v úmyslu udělat, vyberte **vypnout** nebo **restartovat** .

    ![Nastavení napájení](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Po zobrazení výzvy k potvrzení vyberte **Ano** a pokračujte.

> [!NOTE]
> Pokud vypnete fyzické zařízení, budete muset zapnout tlačítko napájení v zařízení, abyste ho zapnuli.

## <a name="next-steps"></a>Další postup

- Naučte se [Spravovat sdílené složky](data-box-edge-manage-shares.md).
