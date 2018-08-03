---
title: Delegování nabídek v Azure stacku | Dokumentace Microsoftu
description: Zjistěte, jak vložit jiní lidé starosti vytvoření nabídky a registraci uživatelů.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 157f0207-bddc-42e5-8351-197ec23f9d46
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/11/2018
ms.author: brenduns
ms.reviewer: alfredop
ms.openlocfilehash: 161a17f2360767dacc4f418a078c695d7cb8daa7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449747"
---
# <a name="delegate-offers-in-azure-stack"></a>Delegování nabídek v Azure Stacku

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Jako operátory Azure stacku často chcete umístit jiní lidé starosti vytvoření nabídky a přihlašování uživatelů. Například pokud jste poskytovatel služeb, může být vhodné prodejci v systému k podepisování zákazníci se těmito věcmi a spravovat za vás. Nebo, pokud jste součástí centrální skupina IT v organizaci, může být vhodné delegovat přihlášení uživatele až další IT zaměstnance.

Delegování usnadňuje dosažení a správě více uživatelů, které můžete provést sami. Následující obrázek znázorňuje jeden stupeň delegování, ale podporuje víc než jednu úroveň Azure Stack. Delegovaní poskytovatelé (DPs) můžete delegovat na jiných poskytovatelů, až pět úrovní.

![Úrovně delegování](media/azure-stack-delegated-provider/image1.png)

## <a name="understand-delegation-roles-and-steps"></a>Principy delegování role a kroky

### <a name="delegation-roles"></a>Delegování role

Následující role jsou součástí delegování:

* *Operátory Azure stacku* spravuje infrastruktury Azure stacku a vytvoří šablonu nabídky. Operátor, který deleguje ostatním uživatelům poskytnout nabízí svým uživatelům.

* Delegované operátorům Azure stacku se nazývají *delegované poskytovatele*. Se může patřit do jiné organizace, jako je například jiných uživatelů Azure Active Directory (Azure AD).

* *Uživatelé* zaregistrovat nabídky a jejich použití ke správě své úlohy vytváření virtuálních počítačů, ukládání dat a tak dále.

### <a name="delegation-steps"></a>Postup delegování

Existují dva základní kroky pro nastavení delegování:

1. *Vytvoření delegované poskytovatele* podle vytváří se předplatné uživatele pro nabídky podle plánu, který má jenom předplatná služby. Uživatelé, kteří přihlášení odběru v rámci této nabídky můžete pak rozšířit nabídky registraci a přihlašování uživatelů pro nabídky.

1. *Delegovat nabídku k delegované poskytovatele*. V rámci této nabídky je šablona pro co můžou nabízet delegované poskytovatele. Delegované poskytovatele teď můžete využít nabídky a nabídnout ho jiným uživatelům.

Následující obrázek znázorňuje postup pro nastavení delegování.

![Vytvoření delegované poskytovatele a povolit jejich registrace uživatele](media/azure-stack-delegated-provider/image2.png)

**Požadavky na delegované poskytovatele**

Tak, aby fungoval jako delegované poskytovatele, aby uživatel musí k navázání vztahu s poskytovateli hlavní vytvořením odběru. Toto předplatné identifikuje delegované poskytovatele tak, že má právo k dispozici nabídky jménem hlavní zprostředkovatele.

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
       Tento plán by měl obsahovat pouze službu předplatných. Tento článek používá plán s názvem **PlanForDelegation** jako příklad.

   b.  [Vytvoření nabídky](azure-stack-create-offer.md) na základě tohoto plánu. Tento článek používá nabídky s názvem **OfferToDP** jako příklad.

   c.  Přidat delegované poskytovatele jako předplatitel této nabídky výběrem **předplatná** > **přidat** > **nového předplatného Tenanta**.

   ![Přidání delegovaného poskytovatele jako předplatitel](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > Všechny nabídky Azure Stack, máte možnost vytvoření nabídky veřejné, takže uživatelé zaregistrovat, nebo zachování soukromých, takže operátory Azure stacku správě přihlašování. Delegovaní poskytovatelé jsou obvykle malé skupiny. Chcete určovat, kdo je umožněno, takže udržování soukromý v rámci této nabídky dává smysl ve většině případů.

### <a name="azure-stack-operator-creates-the-delegated-offer"></a>Vytvoří se delegovaná nabídka Azure Stack – operátor

Dalším krokem je vytvoření plánu a nabídky, který se chystáte delegovat, a že budou uživatelé používat. Je vhodné definovat v rámci této nabídky, přesně tak, jak chcete, aby uživatelům zobrazit, protože delegované poskytovatele nejde změnit, plánů a kvót, které obsahuje.

1. Jako operátory Azure stacku [vytvořit plán](azure-stack-create-plan.md) a [nabídky](azure-stack-create-offer.md) na základě plánu. Tento článek používá nabídky s názvem **DelegatedOffer** jako příklad.

   > [!NOTE]
   > V rámci této nabídky nemusí být veřejné, ale můžete si je veřejný Pokud budete chtít. Ale ve většině případů je zapotřebí delegované poskytovatele. Chcete-li mít přístup do nabídky. Po privátní nabídku delegovat, jak je popsáno v následujících krocích, delegované poskytovatele k němu má přístup.

1. Delegate nabídku. Přejděte na **DelegatedOffer**. V části **nastavení**vyberte **delegované poskytovatele** > **přidat**.

1. Z rozevíracího seznamu vyberte předplatné pro delegované poskytovatele a potom vyberte **delegáta**.

   ![Přidání delegovaného zprostředkovatele](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>Přizpůsobí nabídku delegované poskytovatele

Přihlaste se k portálu user portal jako delegované poskytovatele a potom vytvořte novou nabídku s použitím delegovanou nabídku jako šablonu.

1. Vyberte **nové** > **Tenanta nabízí + plány** > **nabízejí**.

    ![Vytvořit novou nabídku](media/azure-stack-delegated-provider/image5.png)

1. Přiřaďte název nabídky. Tento článek používá **ResellerOffer** jako příklad. Vyberte delegovanou nabídku, na kterém chcete založit a pak vyberte **vytvořit**.

   ![Přiřaďte název](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >Je důležité pochopit, že na rozdíl od operátory Azure stacku, nebude delegované poskytovatele vytvořit nabídku základní plány a doplňkové plány. Delegované poskytovatele. můžete vybrat jenom nabídky, které jsou delegovanými jim, že nemůžete provádět změny na tyto nabídky.

1. Zveřejněte tuto nabídku tak, že vyberete **Procházet**a potom **nabízí**. Vyberte nabídky a pak vyberte **změnit stav**.

1. Delegované poskytovatele zpřístupňuje tyto nabídky portálu pro své vlastní adresu URL. Tyto nabídky jsou viditelné pouze prostřednictvím portálu pro delegovaný. Najít a změnit tuto adresu URL:

    a.  Vyberte **Procházet** > **další služby** > **předplatná**. Vyberte předplatné delegované poskytovatele. Například **DPSubscription** > **vlastnosti**.

    b.  Na portálu zkopírujte adresu URL na samostatném umístění, jako je například Poznámkový blok.

    ![Vyberte předplatné delegované poskytovatele.](media/azure-stack-delegated-provider/dpportaluri.png)  

   Dokončení vytváření delegovanou nabídku jako delegované poskytovatele. Odhlaste se jako delegované poskytovatele a zavřete okno prohlížeče, které používáte.

### <a name="sign-up-for-the-offer"></a>Zaregistrujte si tuto nabídku

1. V novém okně prohlížeče, přejděte na portál pro delegované adresu URL, kterou jste uložili v předchozím kroku. Přihlaste se k portálu jako uživatel.

   >[!NOTE]
   >Delegované nabídky se nezobrazí, pokud nechcete použít delegovaný portálu.

1. Na řídicím panelu vyberte **pořiďte si předplatné**. Uvidíte, že uživateli se zobrazí pouze delegované nabídky, které byly vytvořeny delegované poskytovatele.

   ![Zobrazení a vyberte možnost nabídky](media/azure-stack-delegated-provider/image8.png)

Dokončení procesu delegování v rámci nabídky. Nyní uživatele můžou zaregistrovat k této nabídce zaregistrují, získání předplatného.

## <a name="multiple-tier-delegation"></a>Delegování více vrstvami

Více vrstvami delegování umožňuje delegované poskytovatele delegovat nabídku na jiné entity. Například k vytvoření hlubší prodejce kanály, kde:

* Zprostředkovatel, který spravuje služby Azure Stack deleguje nabídku distributora.
* Distributor delegáty pro prodejce.

Pokud chcete vytvořit několik vrstev delegování nabídky, delegované poskytovatele deleguje nabídky další poskytovatele. Proces je stejný pro delegované poskytovatele, jako byl pro operátory Azure stacku. Další informace najdete v tématu [operátory Azure stacku, vytvoří se delegovaná nabídka](#cloud-operator-creates-the-delegated-offer).

## <a name="next-steps"></a>Další postup

[Zřízení virtuálního počítače](azure-stack-provision-vm.md)