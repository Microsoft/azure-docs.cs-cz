---
title: Změna SKU pro Azure AD Domain Services | Microsoft Docs
description: Přečtěte si, jak na úrovni SKU Azure AD Domain Services spravované doméně, pokud se změní vaše obchodní požadavky.
services: active-directory-ds
author: MicrosoftGuyJFlo
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/09/2020
ms.author: joflore
ms.openlocfilehash: c85a3f3262da6002e9604b1506552b9188a64460
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91962747"
---
# <a name="change-the-sku-for-an-existing-azure-active-directory-domain-services-managed-domain"></a>Změna SKU pro existující Azure Active Directory Domain Services spravovanou doménu

V Azure Active Directory Domain Services (Azure služba AD DS) je dostupný výkon a funkce na základě typu SKU. Tyto rozdíly mezi funkcemi zahrnují četnost zálohování nebo maximální počet jednosměrných odchozích vztahů důvěryhodnosti doménové struktury.

SKU vyberete při vytváření spravované domény a po nasazení spravované domény můžete přepínat nebo snížit skladové položky podle toho, jak se vaše firemní potřeby mění. Změny v obchodních požadavcích můžou zahrnovat nutnost častých záloh nebo vytváření dalších vztahů důvěryhodnosti doménové struktury. Další informace o limitech a cenách různých SKU najdete v tématu [Koncepty azure služba AD DS SKU][concepts-sku] a cenové stránky [Azure služba AD DS][pricing] .

V tomto článku se dozvíte, jak změnit SKLADOVOU položku pro existující spravovanou doménu Azure služba AD DS pomocí Azure Portal.

## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby dokončete kurz [Vytvoření a konfigurace spravované domény][create-azure-ad-ds-instance].

## <a name="sku-change-limitations"></a>Omezení změny SKU

Po nasazení spravované domény můžete skladové jednotky změnit nahoru nebo dolů. Pokud ale používáte doménovou strukturu prostředků a vytvořili jste jednosměrnou odchozí důvěryhodnost doménové struktury z Azure služba AD DS do místního prostředí služba AD DS, existují určitá omezení operace změny skladové položky. SKU *Premium* a *Enterprise* definují limit počtu vztahů důvěryhodnosti, které můžete vytvořit. Nemůžete změnit na SKU s nižším maximálním limitem, než je aktuálně nakonfigurované.

Například:

* Pokud jste vytvořili dvě vztahy důvěryhodnosti doménové struktury na SKU *Premium* , nemůžete přejít na standardní SKU *Standard* . *Standardní* SKU nepodporuje vztahy důvěryhodnosti doménové struktury.
* Případně, pokud jste vytvořili sedm vztahů důvěryhodnosti na SKU *Premium* , nemůžete přejít na SKU *Enterprise* . SKU *Enterprise* podporuje maximálně pět vztahů důvěryhodnosti.

Další informace o těchto omezeních najdete v tématu [funkce a omezení služby Azure služba AD DS SKU][concepts-sku].

## <a name="select-a-new-sku"></a>Vybrat novou SKLADOVOU položku

Chcete-li změnit SKLADOVOU položku pro spravovanou doménu pomocí Azure Portal, proveďte následující kroky:

1. V horní části Azure Portal vyhledejte a vyberte **Azure AD Domain Services**. Ze seznamu vyberte spravovanou doménu, například *aaddscontoso.com*.
1. V nabídce na levé straně stránky Azure služba AD DS vyberte **nastavení > SKU**.

    ![Vyberte možnost nabídky SKU pro spravovanou doménu Azure služba AD DS v Azure Portal](media/change-sku/overview-change-sku.png)

1. V rozevírací nabídce vyberte SKU, které chcete pro spravovanou doménu. Pokud máte doménovou strukturu prostředků, nemůžete vybrat *standardní* SKU jako vztahy důvěryhodnosti doménové struktury, které jsou k dispozici jenom pro SKU *organizace* nebo vyšší.

    Z rozevírací nabídky zvolte skladovou jednotku, kterou chcete, a pak vyberte **Uložit**.

    ![Z rozevírací nabídky v Azure Portal vyberte požadovaný SKU.](media/change-sku/change-sku-selection.png)

Změna typu SKU může trvat minutu nebo dvě.

## <a name="next-steps"></a>Další kroky

Pokud máte doménovou strukturu prostředků a chcete po změně SKU vytvořit další vztahy důvěryhodnosti, přečtěte si téma [Vytvoření vztahu důvěryhodnosti odchozí doménové struktury k místní doméně v Azure služba AD DS][create-trust].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
