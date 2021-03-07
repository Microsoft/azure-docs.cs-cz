---
title: Azure Stack pro přístup k zařízení GPU pro grafický procesor, napájení a režim připojení | Microsoft Docs
description: Popisuje, jak spravovat přístup, režim napájení a režim připojení pro zařízení s GRAFICKÝm rozhraním Azure Stack Edge pro, které pomáhá přenést data do Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 39990a557315c3fcc79f2b9dab59f25f758ab2bd
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443110"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-stack-edge-pro-gpu"></a>Správa režimu přístupu, napájení a připojení pro grafický procesor Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Tento článek popisuje, jak spravovat režim přístupu, napájení a připojení pro Azure Stack Edge pro pomocí zařízení GPU. Tyto operace se provádějí prostřednictvím místního webového uživatelského rozhraní nebo Azure Portal.

Tento článek se týká Azure Stackch PROCESORů Edge pro, Azure Stack Edge pro R a Azure Stack hraničních zařízení R.


V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Správa přístupu k zařízení
> * Správa přístup k prostředků
> * Správa režimu připojení
> * Správa napájení


## <a name="manage-device-access"></a>Správa přístupu k zařízení

Přístup k vašemu zařízení Azure Stack Edge pro je ovládán použitím hesla zařízení. Heslo můžete změnit prostřednictvím místního webového uživatelského rozhraní. Můžete také resetovat heslo zařízení v Azure Portal.

Přístup k datům na discích zařízení se taky řídí pomocí klíčů pro zašifrování.

### <a name="change-device-password"></a>Změna hesla zařízení

Pokud chcete změnit heslo zařízení, postupujte podle těchto kroků v místním uživatelském rozhraní.

1. V místním webovém uživatelském rozhraní přejdete do části **údržba > heslo**.
2. Zadejte aktuální heslo a pak nové heslo. Zadané heslo musí mít 8 až 16 znaků. Heslo musí obsahovat 3 z následujících znaků: velká písmena, malá písmena, číslice a speciální znaky. Potvrďte nové heslo.

    ![Změna hesla](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/change-password-1.png)

3. Vyberte **změnit heslo**.
 
### <a name="reset-device-password"></a>Resetování hesla zařízení

Pracovní postup Resetování nevyžaduje, aby uživatel nahrál staré heslo a byl užitečný při ztrátě hesla. Tento pracovní postup se provádí v Azure Portal.

1. V Azure Portal najdete v části **přehled > resetování hesla správce**.

    ![Snímek obrazovky se zvoleným zařízením s resetováním hesla zařízení.](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/reset-password-1.png)


2. Zadejte nové heslo a potvrďte ho. Zadané heslo musí mít 8 až 16 znaků. Heslo musí obsahovat 3 z následujících znaků: velká písmena, malá písmena, číslice a speciální znaky. Vyberte **Resetovat**.

    ![Resetování hesla 2](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

### <a name="manage-access-to-device-data"></a>Správa přístupu k datům zařízení

V případě zařízení Azure Stack Edge pro R a Azure Stack hraničních zařízení R se přístup k datům zařízení ovládá pomocí klíčů zašifrovaného šifrování pro jednotky zařízení. Po úspěšném nakonfigurování zařízení pro šifrování v klidovém prostředí bude možnost Otočit klíče šifrování s příponou REST k dispozici v místním uživatelském rozhraní zařízení. 

Tato operace vám umožní změnit klíče pro svazky BitLockeru `HcsData` a `HcsInternal` a všechny vlastní šifrovací jednotky na vašem zařízení.

Pomocí těchto kroků otočíte klíče šifrování s příponou.

1. V místním uživatelském rozhraní zařízení, **navštivte stránku Začínáme** . Na dlaždici **zabezpečení** vyberte možnost **šifrování-zapnuto: otočit klíče** . Tato možnost je k dispozici až po úspěšné konfiguraci klíčů pro šifrování s příponou REST.

    ![Vyberte možnost Otočit klíče pro šifrování – na stránce Začínáme.](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-1.png)

1. Můžete použít vlastní klíče BitLockeru nebo použít systémem generované klíče.  

    Pokud chcete zadat svůj vlastní klíč, zadejte řetězec s délkou "32" dlouhého řetězce v kódování Base-64. Vstup se podobá tomu, co byste zadali při prvním nastavování šifrování v klidovém čase.

    ![Přineste si vlastní klíč k šifrování.](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-2.png)

    Můžete se také rozhodnout použít systémový klíč vygenerovaný systémem.

    ![Použít systémově generované šifrování – klávesa on-REST](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-3.png)

1. Vyberte **Použít**. Ochrany pomocí klíče se otočí.

    ![Použít nové šifrování a klíč na REST](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-4.png)

1. Po zobrazení výzvy ke stažení a uložení souboru klíče vyberte **Stáhnout a pokračovat**. 

    ![Stažení a pokračování souboru klíče](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/rotate-encryption-keys-5.png)

    Uložte `.json` soubor klíče na bezpečné místo. Tento soubor slouží k usnadnění případného budoucího obnovení zařízení.

    ![Snímek obrazovky se zobrazí v dialogovém okně Resetovat heslo zařízení.](media/azure-stack-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-resource-access"></a>Správa přístup k prostředků

Pokud chcete vytvořit Azure Stack hraničních, Data Box Gateway, IoT Hub a Azure Storage prostředků, potřebujete oprávnění jako přispěvatel nebo vyšší na úrovni skupiny prostředků. Budete také potřebovat registrovat odpovídající poskytovatele prostředků. Pro všechny operace, které zahrnují aktivační klíč a přihlašovací údaje, se taky vyžadují oprávnění k rozhraní Microsoft Graph API. Tyto oddíly jsou popsány v následujících částech. 

### <a name="manage-microsoft-graph-api-permissions"></a>Správa oprávnění rozhraní API Microsoft Graph

Při generování aktivačního klíče pro zařízení Azure Stack Edge pro nebo provádění operací, které vyžadují přihlašovací údaje, potřebujete oprávnění Azure Active Directory Graph API. Operace, které vyžadují přihlašovací údaje, můžou být:

-  Vytváří se sdílená složka s přidruženým účtem úložiště.
-  Vytváří se uživatel, který má přístup ke sdíleným složkám na zařízení.

Měli byste mít `User` přístup k Tenantovi služby Active Directory, jak potřebujete `Read all directory objects` . Nemůžete být uživatel typu Host, protože k němu nemají oprávnění `Read all directory objects` . Pokud jste Host, pak operace, jako je generace aktivačního klíče, vytvoření sdílené složky na zařízení Azure Stack Edge pro, vytvoření uživatele, konfigurace hraniční výpočetní role, resetování hesla zařízení selže.

Další informace o tom, jak poskytnout uživatelům přístup k Microsoft Graph rozhraní API, najdete v tématu [Microsoft Graph oprávnění](/graph/permissions-reference).

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

Další informace o tom, jak zaregistrovat poskytovatele prostředků, najdete v tématu [řešení chyb pro registraci poskytovatele prostředků](../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="manage-connectivity-mode"></a>Správa režimu připojení

Kromě výchozího režimu s plným připojením může být zařízení také spuštěné v částečně připojeném nebo plně odpojeném režimu. Každý z těchto režimů je popsaný níže:

- **Plně připojeno** – jedná se o normální výchozí režim, ve kterém zařízení funguje. V tomto režimu je povolené nahrávání do cloudu i stahování dat. Ke správě zařízení můžete použít Azure Portal nebo místní webové uživatelské rozhraní.

- **Částečně odpojeno** – v tomto režimu nemůže zařízení nahrávat ani stahovat žádná sdílená data, ale dá se spravovat prostřednictvím Azure Portal.

    Tento režim se obvykle používá, pokud je v měřené satelitní síti a cílem je minimalizovat spotřebu šířky pásma sítě. Minimální spotřeba sítě může stále nastat pro operace monitorování zařízení.

- **Odpojeno** – v tomto režimu je zařízení zcela odpojené od cloudu a jejich cloudové odesílání a stahování je zakázané. Zařízení je možné spravovat jenom prostřednictvím místního webového uživatelského rozhraní.

    Tento režim se obvykle používá, pokud chcete zařízení převést do režimu offline.

Chcete-li změnit režim zařízení, postupujte podle následujících kroků:

1. V místním webovém uživatelském rozhraní zařízení, navštivte **konfigurační > Cloud**.
2. V rozevíracím seznamu vyberte režim, ve kterém má být zařízení provozováno. Můžete vybrat z **úplně připojeného**, **částečně připojeného** a **úplného odpojení**. Pokud chcete zařízení spustit v režimu částečně odpojeno, povolte **správu Azure Portal**.

 
## <a name="manage-power"></a>Správa napájení

Fyzické zařízení můžete vypnout nebo restartovat pomocí místního webového uživatelského rozhraní. Před restartováním doporučujeme přepnout sdílené složky na datovém serveru a potom na zařízení do offline režimu. Tato akce minimalizuje možnost poškození dat.

1. V místním webovém uživatelském rozhraní přejdete do části **údržba > napájení**.
2. V závislosti na tom, co máte v úmyslu udělat, vyberte **vypnout** nebo **restartovat** .

    ![Nastavení napájení](media/azure-stack-edge-gpu-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. Po zobrazení výzvy k potvrzení vyberte **Ano** a pokračujte.

> [!NOTE]
> Pokud vypnete fyzické zařízení, budete muset zapnout tlačítko napájení v zařízení, abyste ho zapnuli.

## <a name="next-steps"></a>Další kroky

- Naučte se [Spravovat sdílené složky](azure-stack-edge-manage-shares.md).