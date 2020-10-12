---
title: Použití Azure Portal k řešení problémů s pořadím pro Azure Stack Edge pro | Microsoft Docs
description: Popisuje řešení potíží s pořadím Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/11/2020
ms.author: alkohli
ms.openlocfilehash: 3087c209b844d691173edbbd8a993c1d0aae319b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90903640"
---
# <a name="troubleshoot-your-azure-stack-edge-pro-ordering-issues"></a>Řešení potíží s pořadím Azure Stack Edge pro

Tento článek popisuje, jak řešit problémy s pořadím Azure Stack Edge pro.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Řešení potíží s řazením

## <a name="unsupported-subscription-or-region"></a>Nepodporované předplatné nebo oblast

**Popis chyby:** Pokud se zobrazí chyba, v Azure Portal.

*Vybrané předplatné nebo oblast se nepodporují. Vyberte jiné předplatné nebo oblast.*

![Nepodporované předplatné nebo oblast](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-01.png)

**Navrhované řešení:**  Ujistěte se, že jste použili podporované předplatné, jako je například [Microsoft smlouva Enterprise (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)nebo [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Předplatná s průběžnými platbami se nepodporují. Další informace najdete v tématu [Azure Stack požadavky na prostředky Edge](azure-stack-edge-deploy-prep.md#prerequisites).

Je možné, že Microsoft může v jednotlivých případech umožnit upgrade typu předplatného. Obraťte se na [podporu Microsoftu](https://azure.microsoft.com/support/options/) , aby mohli pochopit vaše potřeby a patřičně upravit tato omezení.

## <a name="selected-subscription-type-not-supported"></a>Vybraný typ předplatného není podporovaný.

**Chyba:** Máte k dispozici předplatné EA, CSP nebo sponzorství a zobrazí se následující chyba:

*Vybraný typ předplatného není podporován. Ujistěte se, že používáte podporované předplatné. [Další informace](azure-stack-edge-deploy-prep.md#prerequisites) Pokud používáte podporovaný typ předplatného, ujistěte se, že `Microsoft.DataBoxEdge` je poskytovatel zaregistrovaný. Informace o tom, jak se zaregistrovat, najdete v tématu [registrace poskytovatele prostředků](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers)*.

**Navrhované řešení:** Pomocí těchto kroků zaregistrujete poskytovatele prostředků Azure Stack Edge:

1. V Azure Portal můžete přejít na **domovskou**  >  **předplatná**.

2. Vyberte předplatné, které použijete k objednání zařízení.

3. Vyberte **poskytovatelé prostředků** a potom vyhledejte **Microsoft. DataBoxEdge**.

    ![Registrace poskytovatele prostředků](media/azure-stack-edge-troubleshoot-ordering/azure-stack-edge-troubleshoot-ordering-02.png)

Pokud nemáte oprávnění vlastníka nebo přispěvatele k registraci poskytovatele prostředků, zobrazí se následující chyba: * &lt; název předplatného předplatného nemá &gt; oprávnění registrovat poskytovatele prostředků: Microsoft. DataBoxEdge.*

Další informace najdete v tématu [registrace poskytovatelů prostředků](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).

## <a name="microsoftdataboxedge-not-registered-for-subscription"></a>Microsoft. DataBoxEdge není zaregistrovaný pro předplatné.

**Chyba:** V Azure Portal vyberte předplatné, které se má použít pro Azure Stack Edge pro nebo Data Box Gateway, a zobrazí se následující chyba:

*Poskytovatelé prostředků: Microsoft. DataBoxEdge není registrovaný pro název předplatného předplatného &lt; &gt; a nemáte oprávnění registrovat poskytovatele prostředků pro &lt; název &gt; *předplatného předplatného.

**Navrhované řešení:** Zvyšte přístup k předplatnému nebo vyhledejte někoho s oprávněním vlastníka nebo přispěvatele k registraci poskytovatele prostředků.

## <a name="resource-disallowed-by-policy"></a>Prostředek zakázaný zásadami

**Chyba:** V Azure Portal se pokusíte zaregistrovat poskytovatele prostředků a získat následující chybu:

*&lt; &gt; Zásady nepovolily název prostředku prostředku. (Kód: RequestDisallowedByPolicy). Iniciativa: zamítnout všeobecně nežádoucí typy prostředků. Zásady: nepovolené typy prostředků.*

**Navrhované řešení:** K této chybě dochází z důvodu existující zásady Azure, která blokuje vytváření prostředků. Zásady Azure nastavuje správce systému v organizaci, aby se zajistilo dodržování předpisů při používání nebo vytváření prostředků Azure. Pokud takové zásady blokují Azure Stack vytváření prostředků Edge, požádejte správce systému o úpravu zásad Azure.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [řešení problémů s Azure Stack Edge pro](azure-stack-edge-troubleshoot.md).
