---
title: Změna SKU pro Azure AD Domain Services | Microsoft Docs
description: Přečtěte si, jak na úrovni SKU Azure AD Domain Services spravované doméně, pokud se změní vaše obchodní požadavky.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 46557d802222190c0ed82f6243dd2a9b997ecaa5
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960565"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>Změna SKU pro existující Azure AD Domain Services spravovanou doménu

V Azure Active Directory Domain Services (Azure služba AD DS) je dostupný výkon a funkce na základě typu SKU. Tyto rozdíly mezi funkcemi zahrnují četnost zálohování nebo maximální počet jednosměrných odchozích vztahů důvěryhodnosti doménové struktury (aktuálně ve verzi Preview). SKU vybíráte při vytváření spravované domény a po nasazení spravované domény můžete přepnout SKU podle toho, jak se vaše firemní potřeby změnily. Změny v obchodních požadavcích můžou zahrnovat nutnost častých záloh nebo vytváření dalších vztahů důvěryhodnosti doménové struktury. Další informace o limitech a cenách různých SKU najdete v tématu [Koncepty azure služba AD DS SKU][concepts-sku] a cenové stránky [Azure služba AD DS][pricing] .

V tomto článku se dozvíte, jak změnit SKLADOVOU položku pro existující spravovanou doménu Azure služba AD DS pomocí Azure Portal.

## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku potřebujete následující prostředky a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby dokončete kurz a [vytvořte a nakonfigurujte instanci Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="sku-change-limitations"></a>Omezení změny SKU

Existují určitá omezení operace změny skladové položky, pokud používáte doménovou strukturu prostředků (aktuálně ve verzi Preview) a vytvořili jste jednosměrné vztahy důvěryhodnosti mezi Azure služba AD DS do místního prostředí služba AD DS. SKU *Premium* a *Enterprise* definují limit počtu vztahů důvěryhodnosti, které můžete vytvořit. Nemůžete změnit na SKU s nižším maximálním limitem, než je aktuálně nakonfigurované.

Pokud jste například vytvořili dvě vztahy důvěryhodnosti doménové struktury na SKU *Premium* , nemůžete přejít na *standardní* SKU. *Standardní* SKU nepodporuje vztahy důvěryhodnosti doménové struktury. Případně, pokud jste vytvořili sedm vztahů důvěryhodnosti na SKU *Premium* , nemůžete přejít na SKU *Enterprise* . SKU *Enterprise* podporuje maximálně pět vztahů důvěryhodnosti.

Další informace o těchto omezeních najdete v tématu [funkce a omezení služby Azure služba AD DS SKU][concepts-sku].

## <a name="select-a-new-sku"></a>Vybrat novou SKLADOVOU položku

Pokud chcete změnit SKLADOVOU položku pro spravovanou doménu Azure služba AD DS pomocí Azure Portal, proveďte následující kroky:

1. V horní části Azure Portal vyhledejte a vyberte **Azure AD Domain Services**. Ze seznamu vyberte spravovanou doménu, například *aadds.contoso.com*.
1. V nabídce na levé straně stránky Azure služba AD DS vyberte **nastavení > SKU**.

    ![Vyberte možnost nabídky SKU pro spravovanou doménu Azure služba AD DS v Azure Portal](media/change-sku/overview-change-sku.png)

1. V rozevírací nabídce vyberte SKU, které chcete pro spravovanou doménu Azure služba AD DS. Pokud máte doménovou strukturu prostředků, nemůžete vybrat *standardní* SKU jako vztahy důvěryhodnosti doménové struktury, které jsou k dispozici jenom pro SKU *organizace* nebo vyšší.

    Z rozevírací nabídky zvolte skladovou jednotku, kterou chcete, a pak vyberte **Uložit**.

    ![Z rozevírací nabídky v Azure Portal vyberte požadovaný SKU.](media/change-sku/change-sku-selection.png)

Změna typu SKU může trvat minutu nebo dvě.

## <a name="next-steps"></a>Další kroky

Pokud máte doménovou strukturu prostředků a chcete po změně SKU vytvořit další vztahy důvěryhodnosti, přečtěte si téma [Vytvoření vztahu důvěryhodnosti odchozí doménové struktury k místní doméně v Azure služba AD DS (Preview)][create-trust].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
