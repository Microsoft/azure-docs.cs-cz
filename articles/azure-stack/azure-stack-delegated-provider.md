---
title: Delegování nabídek v Azure stacku | Dokumentace Microsoftu
description: Zjistěte, jak vložit jiní lidé starosti vytvoření nabídky a registraci uživatelů.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 1b22d465df12d313e05d3fd93437b461ef939ee7
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242480"
---
# <a name="delegate-offers-in-azure-stack"></a>Delegování nabídek v Azure Stacku

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Jako operátory Azure stacku často chcete umístit jiní lidé starosti přihlašování uživatelů a vytvoření předplatného. Například pokud jste poskytovatel služeb, může být vhodné prodejci v systému k podepisování zákazníci se těmito věcmi a spravovat za vás. Nebo, pokud jste součástí centrální skupina IT v organizaci, může být vhodné delegovat registraci uživatele na další IT zaměstnance.

Delegování usnadňuje dosažení a správě více uživatelů, než můžete provést sami, jak je znázorněno na následujícím obrázku:

![Úrovně delegování](media/azure-stack-delegated-provider/image1.png)

Delegování delegované poskytovatele spravuje nabídky (delegovanou nabídku) a koncovým zákazníkům získat předplatná v rámci této nabídky bez zásahu od správce systému.

## <a name="understand-delegation-roles-and-steps"></a>Principy delegování role a kroky

### <a name="delegation-roles"></a>Delegování role

Následující role jsou součástí delegování:

* *Operátory Azure stacku* spravuje infrastruktury Azure stacku a vytvoří šablonu nabídky. Operátor, který deleguje ostatním uživatelům poskytovat nabídky do svého tenanta.

* Delegované operátorům Azure stacku jsou uživatelé s *vlastníka* nebo *Přispěvatel* volá práva v předplatných *delegované poskytovatele*. Se může patřit do jiné organizace, jako je například jiných tenantů Azure Active Directory (Azure AD).

* *Uživatelé* zaregistrovat nabídky a jejich použití ke správě své úlohy vytváření virtuálních počítačů, ukládání dat a tak dále.

### <a name="delegation-steps"></a>Postup delegování

Existují dva základní kroky pro nastavení delegování:

1. **Vytvoření odběru delegované poskytovatele**: Předplatné pro uživatele na nabídku obsahující pouze službu předplatných. Uživatelé, kteří přihlášení odběru v rámci této nabídky můžete pak rozšířit delegované nabídky ostatním uživatelům po přihlášení do těchto nabídek.

2. **Delegovat nabídku k delegované poskytovatele**: Tato nabídka umožňuje delegované poskytovatele vytvářet předplatná nebo rozšířit nabídky svým uživatelům. Delegované poskytovatele teď můžete využít nabídky a nabídnout ho jiným uživatelům.

Následující obrázek znázorňuje postup pro nastavení delegování:

![Vytvoření delegované poskytovatele a povolit jejich registrace uživatele](media/azure-stack-delegated-provider/image2.png)

#### <a name="delegated-provider-requirements"></a>Požadavky na delegované poskytovatele

Uživatel tak, aby fungoval jako delegované poskytovatele, vytvoří relaci se hlavní poskytovatele tak, že vytvoříte odběr. Toto předplatné identifikuje delegované poskytovatele tak, že má právo k dispozici delegované nabídky jménem hlavní zprostředkovatele.

Po navázání tohoto vztahu operátory Azure stacku můžete delegovat nabídku k delegované poskytovatele. Delegované poskytovatele může trvat nabídky, přejmenujte ho (ale nezmění její obsah) a nabízí svým zákazníkům.

## <a name="delegation-walkthrough"></a>Názorný postup delegování

Následující části obsahují praktické návod pro nastavení delegované poskytovatele, delegování v rámci nabídky a ověřuje, že uživatelé mohou zaregistrovat pro delegovanou nabídku.

### <a name="set-up-roles"></a>Nastavení rolí

Pokud chcete použít tento návod, potřebujete kromě svého účtu Azure Stack – operátor dva účty Azure AD. Pokud nemáte k dispozici tyto dva účty, musíte je vytvořit. Účty může patřit do jakéhokoli uživatele Azure AD a jsou označovány jako delegované poskytovatele a uživatele.

| **Role** | **Organizační práva** |
| --- | --- |
| Delegované poskytovatele |Uživatel |
| Uživatel |Uživatel |

### <a name="identify-the-delegated-provider"></a>Identifikujte delegované poskytovatele

1. Přihlaste se k portálu správce jako operátory Azure stacku.

1. Vytvoření nabídky, která umožňuje uživateli k delegované poskytovatele:

   a.  [Vytvoření plánu](azure-stack-create-plan.md).
       Tento plán by měl obsahovat pouze předplatné služby. Tento článek používá plán s názvem **PlanForDelegation** jako příklad.

   b.  [Vytvoření nabídky](azure-stack-create-offer.md) na základě tohoto plánu. Tento článek používá nabídky s názvem **OfferToDP** jako příklad.

   c.  Přidat delegované poskytovatele jako předplatitel této nabídky výběrem **předplatná**, pak **přidat**, pak **nového předplatného Tenanta**.

   ![Přidání delegovaného poskytovatele jako předplatitel](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > Všechny nabídky Azure Stack, máte možnost vytvoření nabídky veřejné, takže uživatelé zaregistrovat, nebo zachování soukromých, takže operátory Azure stacku správě přihlašování. Delegovaní poskytovatelé jsou obvykle malé skupiny. Chcete určovat, kdo je umožněno, takže udržování soukromý v rámci této nabídky dává smysl ve většině případů.

### <a name="azure-stack-operator-creates-the-delegated-offer"></a>Vytvoří se delegovaná nabídka Azure Stack – operátor

Dalším krokem je vytvoření plánu a nabídky, který se chystáte delegovat, a že budou uživatelé používat. Je vhodné definovat v rámci této nabídky, přesně tak, jak se má uživatelům zobrazit, protože delegované poskytovatele nejde změnit, plánů a kvót, které obsahuje.

1. Jako operátory Azure stacku [vytvořit plán](azure-stack-create-plan.md) a [nabídky](azure-stack-create-offer.md) na základě plánu. Tento článek používá nabídky s názvem **DelegatedOffer** jako příklad.

   > [!NOTE]
   > V rámci této nabídky nemusí být veřejné, ale může být veřejné. Ale ve většině případů je zapotřebí delegované poskytovatele. Chcete-li mít přístup do nabídky. Po privátní nabídku delegovat, jak je popsáno v následujících krocích, delegované poskytovatele k němu má přístup.

2. Delegate nabídku. Přejděte na **DelegatedOffer**. V části **nastavení**vyberte **delegované poskytovatele**a pak vyberte **přidat**.

3. Z rozevíracího seznamu vyberte předplatné, pro delegované poskytovatele a potom vyberte **delegáta**.

   ![Přidání delegovaného zprostředkovatele](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>Přizpůsobí nabídku delegované poskytovatele

Přihlaste se k portálu user portal jako delegované poskytovatele a potom vytvořte novou nabídku s použitím delegovanou nabídku jako šablonu.

1. Vyberte **+ vytvořit prostředek**, pak **nabízí Tenanta + plány**a pak vyberte **nabízejí**.

    ![Vytvořit novou nabídku](media/azure-stack-delegated-provider/image5.png)

2. Přiřaďte název nabídky. Tento příklad používá **ResellerOffer**. Vyberte delegovanou nabídku, na kterém chcete založit a pak vyberte **vytvořit**.

   ![Přiřaďte název](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >Je důležité pochopit, že delegované poskytovatele. můžete vybrat jenom nabídky, které jsou delegovanými k nim. Na tyto nabídky jsou nemůže provádět změny. Pouze operátory Azure stacku můžete změnit tyto nabídky, například změnit jejich plánů a kvót. Delegované poskytovatele konstruovat nabídku základní plány a doplňkové plány.

3. Delegované poskytovatele můžete zveřejnit tyto nabídky portálu pro své vlastní adresu URL. Zveřejněte tuto nabídku, vyberte **Procházet**a potom **nabízí**. Vyberte nabídky a pak vyberte **změnit stav**.

4. Veřejné delegované nabídky jsou nyní viditelné jenom prostřednictvím portálu pro delegovaný. Najít a změnit tuto adresu URL:

    a.  Vyberte **Procházet**, pak **všechny služby**a potom v části **Obecné** vyberte **předplatná**. Vyberte předplatné delegované poskytovatele; například **DPSubscription**, pak **vlastnosti**.

    b.  Na portálu zkopírujte adresu URL na samostatném umístění, jako je například Poznámkový blok.

    ![Vyberte předplatné delegované poskytovatele.](media/azure-stack-delegated-provider/dpportaluri.png)  

   Dokončení vytváření delegovanou nabídku jako delegované poskytovatele. Odhlaste se jako delegované poskytovatele a zavřete okno prohlížeče.

### <a name="sign-up-for-the-offer"></a>Zaregistrujte si tuto nabídku

1. V novém okně prohlížeče, přejděte na portál pro delegované adresu URL, kterou jste uložili v předchozím kroku. Přihlaste se k portálu jako uživatel.

   >[!NOTE]
   >Delegované nabídky se nezobrazí, pokud nechcete použít delegovaný portálu.

1. Na řídicím panelu vyberte **pořiďte si předplatné**. Uvidíte, že uživateli se zobrazí pouze delegované nabídky, které byly vytvořeny delegované poskytovatele.

   ![Zobrazení a vyberte možnost nabídky](media/azure-stack-delegated-provider/image8.png)

Dokončení procesu delegování v rámci nabídky. Nyní uživatele můžete zaregistrovat k této nabídce zaregistrují získání předplatného k němu.

## <a name="move-subscriptions-between-delegated-providers"></a>Přesun předplatných mezi delegované poskytovatele.

V případě potřeby předplatné lze přesunout mezi předplatnými nové nebo existující delegované poskytovatele, které patří do stejného tenanta adresáře. To se provádí pomocí rutiny prostředí PowerShell [přesunout AzsSubscription](/powershell/module/azs.subscriptions.admin).

To je užitečné, když:

* Připojení nového člena týmu, který bude trvat delegované poskytovatele role a chcete přiřadit tato předplatná uživatelů člena týmu, které byly dříve vytvořeny v výchozí předplatné poskytovatele.
* Máte více předplatných delegovaní poskytovatelé ve stejném tenantu Active directory (Azure Active Directory) a potřebujeme předplatná uživatelů mezi nimi. Tento scénář může být případ, kdy člen týmu přesune mezi týmy a svoje předplatné je potřeba přidělit pro nový tým.

## <a name="next-steps"></a>Další postup

* [Zřízení virtuálního počítače](azure-stack-provision-vm.md)