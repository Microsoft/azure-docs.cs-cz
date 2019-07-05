---
title: Přístup k zařízením Azure Data Box Edge, napájení a režimu připojení | Dokumentace Microsoftu
description: Popisuje, jak spravovat přístup, napájení a režimu připojení pro Azure Data Box hraniční zařízení, že pomáhá přenášet data do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: 2426ecd9c480b0cc236295384ae04c60051f37ce
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442954"
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

    ![Změna hesla](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Vyberte **změnit heslo**.
 
### <a name="reset-device-password"></a>Resetování hesla zařízení

Obnovení pracovního postupu není nutné, aby si možná Vzpomínáte staré heslo a je užitečné v případě ztráty hesla není. Tento pracovní postup se provádí na webu Azure Portal.

1. Na webu Azure Portal, přejděte na **přehled > resetovat heslo správce**.

    ![Resetovat heslo](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Zadejte nové heslo a potvrďte jej. Zadané heslo musí být dlouhé 8 až 16 znaků. Heslo musí obsahovat 3 z následujících znaků: velká písmena, malá písmena, číselné a speciální znaky. Vyberte **resetování**.

    ![Resetovat heslo](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Správa přístupu k prostředkům

Pokud chcete vytvořit Data pole/Data hrany pole brány, IoT Hub a prostředků úložiště Azure, potřebujete oprávnění jako Přispěvatel nebo vyšší na úrovni skupiny prostředků. Musíte také odpovídající poskytovatelů prostředků k registraci. Pro všechny operace, které se týkají aktivační kód a přihlašovací údaje také jsou potřeba oprávnění pro Azure Active Directory Graph API Tyto možnosti jsou popsány v následujících částech.

### <a name="manage-microsoft-azure-active-directory-graph-api-permissions"></a>Spravovat oprávnění k Microsoft Azure Active Directory Graph API

Při generování aktivační klíč pro zařízení Data Box Edge nebo provedením jakékoli operace, které vyžadují přihlašovací údaje, potřebujete oprávnění k Azure Active Directory Graph API. Operace, které je potřeba mít oprávnění může být:

-  Vytvoření sdílené složky pomocí přidružený účet úložiště.
-  Vytváří se uživatel, který můžete přístup ke sdíleným složkám na zařízení.

Měli byste mít `User` přistupovat k na tenanta Active Directory, musíte být schopni `Read all directory objects`. Uživatel typu Host nemůže být, protože nemají oprávnění k `Read all directory objects`. Pokud jste hosta a operace, jako je generování aktivace klíče, vytvoření sdílené složky na vaše zařízení Data Box Edge, vytváření uživatele, provedení konfigurace Edge rolí služby compute, resetovat heslo zařízení se všechny nezdaří.

Další informace o tom, jak poskytnout přístup uživatelům k Azure Active Directory Graph API najdete v tématu [výchozí přístupu pro správce, uživatele a uživatele typu Host](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).

### <a name="register-resource-providers"></a>Zaregistrovat poskytovatele prostředků

Ke zřízení prostředků v Azure (v modelu Azure Resource Manageru), budete potřebovat poskytovatele prostředků, které podporuje vytváření tohoto prostředku. Ke zřízení virtuálního počítače, byste měli mít "Microsoft.Compute" poskytovatele prostředků dostupných v předplatném.
 
Poskytovatelé prostředků jsou registrované na úrovni předplatného. Ve výchozím nastavení je předem registrované pomocí seznamu zprostředkovatelů běžně používaných prostředků žádné nové předplatné Azure. Poskytovatel prostředků pro 'Microsoft.DataBoxEdge' není součástí tohoto seznamu.

Není nutné udělit oprávnění k přístupu na úrovni předplatného uživatelé moct vytvářet prostředkům, jako je "Microsoft.DataBoxEdge" v rámci skupin prostředků, ke kterým mají vlastnických práv, za předpokladu, poskytovatele prostředků pro tyto prostředky se už zaregistrovaný.

Než zkusíte vytvořit prostředek, ujistěte se, že je zaregistrovaný poskytovatel prostředků v předplatném. Pokud není zaregistrovaný poskytovatel prostředků, budete muset Ujistěte se, že uživatel vytváří se nový prostředek má dostatečná oprávnění k registraci poskytovatele požadovaný prostředek na úrovni předplatného. Pokud jste to neudělali stejně, uvidíte následující chybu:

*Předplatné <Subscription name> nemá oprávnění k registraci těchto poskytovatelů prostředků: Microsoft.DataBoxEdge.*


Pokud chcete získat seznam poskytovatelů prostředků registrovaných v aktuálním předplatném, spusťte následující příkaz:

```PowerShell
Get-AzResourceProvider -ListAvailable |where {$_.Registrationstate -eq "Registered"}
```

Pro zařízení Data Box Edge `Microsoft.DataBoxEdge` by měly být zaregistrovány. K registraci `Microsoft.DataBoxEdge`, by měl správce předplatného spuštěním následujícího příkazu:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.DataBoxEdge
```

Další informace o tom, jak zaregistrovat poskytovatele prostředků najdete v tématu [vyřešit chyby registrace poskytovatele prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-register-provider-errors).

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
