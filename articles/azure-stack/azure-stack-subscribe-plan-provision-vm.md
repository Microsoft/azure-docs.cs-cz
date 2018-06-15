---
title: Přihlášení k odběru na nabídku v zásobníku Azure | Microsoft Docs
description: Vytvořte odběr pro nabídky v Azure zásobníku
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/11/2018
ms.author: brenduns
ms.openlocfilehash: 9153649774a67533649fb62da83a3f50abd592da
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35295206"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Vytvoření odběrů nabídky v Azure zásobníku

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Po jste [vytvořit nabídku](azure-stack-create-offer.md), musí uživatelé před použitím ji předplatné v rámci této nabídky. Existují dva způsoby, uživatelé mohou získat předplatné na nabídku:

- Jako operátor cloudu můžete vytvořit odběr pro uživatele z portálu pro správce. Odběry, které vytvoříte, může být pro veřejné a privátní nabídky.
- Jako uživatel klienta můžete přihlásit na nabídku veřejného při použití portálu user portal.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Vytvoření odběru jako operátor cloudu

Operátoři cloudu můžete použít k vytvoření odběru na nabídku pro uživatele portálu pro správu.  Můžete vytvářet odběry pro členy klientovi služby directory.  Když [víceklientský](azure-stack-enable-multitenancy.md) je povoleno, můžete také vytvářet odběry pro uživatele v klientech další adresář.

Pokud nechcete, aby klienti vytvářet své vlastní odběry, soukromá vaší nabídky a pak vytvořte odběry pro vaše klienty. Tento přístup je běžné při integraci s externí fakturace nebo systémů katalogu služby Azure zásobníku.

Po vytvoření odběru pro uživatele, se můžete přihlásit k portálu user portal a najdete v článku jste předplatné nabídku.  

### <a name="to-create-a-subscription-for-a-user"></a>Vytvořit odběr pro uživatele

1. V portálu pro správu, přejděte do **odběry uživatele.**
2. Vyberte **Přidat**. V části **nové předplatné uživatele**, zadejte následující informace:  

   - **Zobrazovaný název** – popisný název pro identifikaci předplatné, které se zobrazí jako *název odběru uživatele*.
   - **Uživatel** – zadejte uživatel z tenanta služby k dispozici adresáře pro toto předplatné. Uživatelské jméno se zobrazí jako *vlastníka*.  Formát uživatelského jména, závisí na řešení identity. Příklad:

     - **Azure AD:***&lt;uživatel1 > @&lt;contoso.onmicrosoft.com >* 

     - **Službu AD FS:***&lt;uživatel1 > @&lt;azurestack.local >* 

   - **Klienta Directory** – vyberte adresář klienta, pokud je uživatelský účet členem. Pokud jste nepovolili víceklientský, je k dispozici pouze vašeho místního adresáře klienta.

3. Vyberte **nabízejí**. V části **nabízí**, zvolte **nabízejí** pro toto předplatné. Vzhledem k tomu, že vytváříte odběr pro uživatele, vyberte **privátní** usnadnění stavu.

4. Vyberte **vytvořit** vytvoření odběru. Zobrazí se nové předplatné v části **uživatele předplatné**. Uživatel se přihlásí k portálu user portal mohou zjistit podrobnosti o předplatném.

### <a name="to-make-an-add-on-plan-available"></a>Chcete-li zpřístupnit plán rozšíření

Operátor cloudu můžete přidat plán rozšíření na dříve vytvořenou předplatné kdykoli:

1. V portálu pro správu, vyberte **více služeb** > **uživatele odběry**. Vyberte odběr, který chcete změnit.

2. Vyberte **doplňky** a pak vyberte **+ přidat**.  

3. V části **přidat plán**, vyberte plán, který chcete jako doplněk.

## <a name="create-a-subscription-as-a-user"></a>Vytvoření odběru jako uživatel

Jako uživatel můžete se přihlásit k portálu user portal k vyhledání a přihlásit se k veřejné nabídky a rozšíření plány pro váš adresář klienta (organizace).

>[!NOTE]
>Pokud vaše prostředí zásobníku Azure podporuje [víceklientský](azure-stack-enable-multitenancy.md) můžete také odebírat nabídky z klienta vzdáleného adresáře.

### <a name="to-subscribe-to-an-offer"></a>K odběru na nabídku

1. [Přihlaste se](azure-stack-connect-azure-stack.md) k portálu user portal Azure zásobníku (https://portal.local.azurestack.external) a vyberte **získat předplatné**.

   ![Získat předplatné](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. V části **získat předplatné**, zadejte popisný název odběru v **zobrazovaný název**. Vyberte **nabízejí** a v části **zvolte nabídku**, vyberte nabídku. Vyberte **vytvořit** vytvoření odběru.

   ![Vytvoření nabídky](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. Jakmile se přihlásíte k nabídku, aktualizujte portálu, abyste zjistili, služby, které jsou součástí nové předplatné.
4. Chcete-li zobrazit předplatné, které jste vytvořili, vyberte **další služby** a pak vyberte **odběry**. Vyberte předplatné, které chcete zobrazit podrobnosti o předplatném.  

### <a name="to-subscribe-to-an-add-on-plan"></a>K odběru plánu rozšíření

Pokud nabídku plán rozšíření, můžete přidat plán k vašemu předplatnému kdykoli.  

1. V portálu pro uživatele, vyberte **další služby** > **odběry**a potom vyberte předplatné, že chcete změnit. Pokud nejsou k dispozici, všechny plány rozšíření **+ přidat plán** je aktivní a není dlaždici **rozšíření plány**.

   >[!NOTE]
   >Pokud **+ přidat plán** není aktivní, pak nejsou k dispozici žádné plány rozšíření nabídky přidružené k tomuto předplatnému.

1. Vyberte **+ přidat plán** nebo **rozšíření plány** dlaždici. V části **rozšíření plány**, vyberte plán, který chcete přidat.

## <a name="next-steps"></a>Další postup

[Zřízení virtuálního počítače](azure-stack-provision-vm.md)
