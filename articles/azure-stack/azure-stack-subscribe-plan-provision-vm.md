---
title: Předplatit nabídky ve službě Azure Stack | Dokumentace Microsoftu
description: Vytvořit odběry pro nabídky ve službě Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/05/2018
ms.author: sethm
ms.openlocfilehash: b6739a194f6374cf90e6508f4a4316892daaf3dd
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079238"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Vytvářet předplatná na nabídky ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Poté co [vytvořte nabídku](azure-stack-create-offer.md), uživatelé potřebují předplatné na tuto nabídku, aby mohli používat. Existují dva způsoby, že uživatelé můžou získat odběru do nabídky:

- Jako operátor cloudu můžete vytvořit odběr pro uživatele v rámci portálu správce. Odběry, které vytvoříte, může být veřejné a soukromé nabídky.
- Jako uživatel tenanta můžete k odběru nabídky na veřejné při použití portálu user portal.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Vytvoření odběru jako operátor cloudu

Operátoři cloudu slouží k vytvoření předplatného na nabídku pro uživatele na portálu pro správu.  Můžete vytvářet předplatných pro členy vašeho vlastního tenanta adresáře.  Když [víceklientská](azure-stack-enable-multitenancy.md) je povolené, můžete také vytvářet odběry pro uživatele v tenantech další adresář.

Pokud nechcete, aby vaši klienti vytvářet své vlastní odběry, soukromá svoje nabídky a pak vytvořte odběry pro vaše klienty. Tento přístup je běžný při integraci s externím fakturace nebo systémů katalogu služby Azure Stack.

Až vytvoříte odběr pro uživatele, se přihlaste k portálu user portal a zkontrolujte, se už zaregistrovali nabídku.  

### <a name="to-create-a-subscription-for-a-user"></a>Jak vytvoříte odběr pro uživatele

1. V portálu pro správu, přejděte na **předplatná uživatelů.**
2. Vyberte **Přidat**. V části **nové předplatné uživatele**, zadejte následující informace:  

   - **Zobrazovaný název** – popisný název pro identifikaci předplatné, které se zobrazí jako *název předplatného uživatele*.
   - **Uživatel** – určení uživatele z tenanta k dispozici adresáře pro toto předplatné. Uživatelské jméno se zobrazí jako *vlastníka*.  Formát uživatelského jména závisí na vašem řešení identity. Příklad:

     - **Azure AD:** `<user1>@<contoso.onmicrosoft.com>`

     - **SLUŽBA AD FS:** `<user1>@<azurestack.local>` 

   - **Adresář tenanta** – vyberte tenantu Active directory, pokud je uživatelský účet členem. Pokud jste ještě nepovolili více tenantů, je k dispozici pouze vašeho místního adresáře tenanta.

3. Vyberte **nabízejí**. V části **nabízí**, zvolte **nabízejí** pro toto předplatné. Vzhledem k tomu, že vytváříte předplatné pro uživatele, vyberte **privátní** jako stav usnadnění.

4. Vyberte **vytvořit** vytvoření odběru. Zobrazí se vám nový odběr v rámci **předplatné uživatele**. Když uživatel přihlásí k portálu user portal mohou zobrazit podrobnosti o předplatném.

### <a name="to-make-an-add-on-plan-available"></a>Chcete-li zpřístupnit doplňkový plán

Operátor cloudu můžete přidat do dříve vytvořené předplatné kdykoli doplňkový plán:

1. V portálu pro správu, vyberte **všechny služby** a potom v části **prostředky pro správu** vyberte **předplatná uživatelů**. Vyberte předplatné, které chcete změnit.

2. Vyberte **doplňky** a pak vyberte **+ přidat**.  

3. V části **přidat plán**, vyberte požadovaného plánu jako doplněk.

## <a name="create-a-subscription-as-a-user"></a>Vytvoření odběru jako uživatel

Jako uživatel můžete se přihlásit k portálu user portal k vyhledání a přihlášení k odběru nabídky veřejné a doplňkové plány pro vašeho tenanta adresáře (organizace).

>[!NOTE]
>Pokud vaše prostředí Azure Stack podporuje [víceklientská](azure-stack-enable-multitenancy.md) také k odběru nabídky z klienta vzdáleného adresáře.

### <a name="to-subscribe-to-an-offer"></a>K odběru nabídky

1. [Přihlaste se](azure-stack-connect-azure-stack.md) k portálu user portal pro Azure Stack (https://portal.local.azurestack.external) a vyberte **pořiďte si předplatné**.

   ![Získat předplatné](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. V části **pořiďte si předplatné**, zadejte popisný název předplatného v **zobrazovaný název**. Vyberte **nabízejí** a v části **zvolit nabídku**, vyberte nabídku. Vyberte **vytvořit** vytvoření odběru.

   ![Vytvoření nabídky](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. Jakmile se přihlásíte k odběru nabídky, aktualizujte stránku portálu, abyste zjistili, které služby jsou součástí nového předplatného.
4. Chcete-li zobrazovat předplatné, které jste vytvořili, vyberte **všechny služby** a potom v části **Obecné** vyberte kategorii **předplatná**. Vyberte předplatné zobrazíte podrobnosti o předplatném.  

### <a name="to-subscribe-to-an-add-on-plan"></a>Přihlaste se k doplňkový plán odběru

Pokud nabídka má doplňkový plán, můžete přidat tento plán k vašemu předplatnému kdykoli.  

1. Na portálu user portal, vyberte **všechny služby**. Části **Obecné** vyberte kategorii **předplatná**a potom vyberte předplatné, který chcete změnit. Pokud jsou k dispozici žádné doplňkové plány **+ přidat plán** je aktivní a dlaždice pro **doplňkové plány**.

   >[!NOTE]
   >Pokud **+ přidat plán** není aktivní, pak nejsou k dispozici žádné doplňkové plány pro nabídku spojený s tímto předplatným.

1. Vyberte **+ přidat plán** nebo **doplňkové plány** dlaždici. V části **doplňkové plány**, vyberte plán, které chcete přidat.

## <a name="next-steps"></a>Další postup

[Zřízení virtuálního počítače](azure-stack-provision-vm.md)
