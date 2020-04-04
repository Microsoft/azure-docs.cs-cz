---
title: Změna skladové položky pro službu domény Služby Azure AD | Dokumenty společnosti Microsoft
description: Zjistěte, jak se vrstva SKU pro spravovanou doménu služby Azure AD Domain Services, pokud se změní vaše obchodní požadavky
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 01/31/2020
ms.author: iainfou
ms.openlocfilehash: 32f8f157abaf5076911c3908a83be4a644e09656
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80655583"
---
# <a name="change-the-sku-for-an-existing-azure-ad-domain-services-managed-domain"></a>Změna skladové položky pro existující spravovanou doménu služby Azure AD Domain Services

Ve službě Azure Active Directory Domain Services (Azure AD DS) jsou dostupné výkony a funkce založené na typu skladové položky. Tyto rozdíly funkcí zahrnují frekvenci zálohování nebo maximální počet jednosměrných odchozích vztahů důvěryhodnosti doménové struktury (aktuálně ve verzi Preview). Skladovou položku vyberete při vytváření spravované domény a po nasazení spravované domény můžete přepnout skladové položky nahoru nebo dolů, jak se vaše obchodní potřeby změní. Změny v obchodních požadavcích mohou zahrnovat potřebu častějších záloh nebo vytvoření dalších vztahů důvěryhodnosti doménové struktury. Další informace o omezeních a cenách různých skladových míst, najdete v [tématu koncepty skladových míst služby Azure AD DS][concepts-sku] a cenových stránek [Služby Azure AD DS.][pricing]

Tento článek ukazuje, jak změnit skladovou položku pro existující spravovanou doménu Azure AD DS pomocí portálu Azure.

## <a name="before-you-begin"></a>Než začnete

Chcete-li tento článek dokončit, potřebujete následující zdroje a oprávnění:

* Aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte si účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, synchronizovaný s místním adresářem nebo s adresářem pouze pro cloud.
    * V případě potřeby [vytvořte klienta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure ke svému účtu][associate-azure-ad-tenant].
* Spravovaná doména Služby Azure Active Directory Domain Services povolená a nakonfigurovaná ve vašem tenantovi Azure AD.
    * V případě potřeby proveďte kurz [a vytvořte a nakonfigurujte instanci služby Azure Active Directory Domain Services][create-azure-ad-ds-instance].

## <a name="sku-change-limitations"></a>Omezení změn skladové položky

Po nasazení spravované domény Azure AD DS můžete změnit sousady skutek nahoru nebo dolů. Pokud však používáte doménovou strukturu prostředků (aktuálně ve verzi Preview) a vytvořili jednosměrné odchozí vztahy důvěryhodnosti doménové struktury z Azure AD DS do místního prostředí služby AD DS, existují určitá omezení pro operaci změny skladové jednotky. *Hlavní* a *podnikové* sloky definují omezení počtu vztahů důvěryhodnosti, které můžete vytvořit. Nelze změnit na skladovou položku s nižším maximálním limitem, než jste aktuálně nakonfigurovali.

Například:

* Pokud jste vytvořili dva vztahy důvěryhodnosti doménové struktury na *hlavní skladové jednotce,* nelze změnit na *standardní* skladovou položku. *Standardní* skladová položka nepodporuje vztahy důvěryhodnosti doménové struktury.
* Nebo pokud jste vytvořili sedm vztahů důvěryhodnosti na *hlavní skladové* jednotce Premium, nemůžete změnit na *podnikovou* skladovou položku. *Podniková* skladová položka podporuje maximálně pět vztahů důvěryhodnosti.

Další informace o těchto omezeních najdete [v tématu Azure AD DS SKU funkce a omezení][concepts-sku].

## <a name="select-a-new-sku"></a>Výběr nové skladové položky

Pokud chcete změnit skladovou položku pro spravovanou doménu Azure AD DS pomocí webu Azure Portal, proveďte následující kroky:

1. V horní části portálu Azure vyhledejte a vyberte **služby Azure AD Domain Services**. Vseznamu vyberte spravovanou doménu, například *aaddscontoso.com*.
1. V nabídce na levé straně stránky Azure AD DS vyberte **Nastavení > skladové položky**.

    ![Vyberte možnost nabídky Skladová položka pro vaši spravovanou doménu Azure AD DS na webu Azure Portal.](media/change-sku/overview-change-sku.png)

1. V rozevírací nabídce vyberte skladovou položku, kterou chcete pro spravovanou doménu Azure AD DS. Pokud máte doménovou strukturu zdrojů, nemůžete vybrat *standardní* skladovou položku, protože vztahy důvěryhodnosti doménové struktury jsou k dispozici pouze ve skladové jednotce *rozlehlé* sítě nebo vyšší.

    V rozevírací nabídce vyberte požadovanou skladovou položku a pak vyberte **Uložit**.

    ![Výběr požadované skladové položky z rozbalovací nabídky na webu Azure Portal](media/change-sku/change-sku-selection.png)

Změna typu skladové položky může trvat minutu nebo dvě.

## <a name="next-steps"></a>Další kroky

Pokud máte doménovou strukturu prostředků a chcete vytvořit další vztahy důvěryhodnosti po změně skladové položky, přečtěte si informace o [vytvoření odchozí ho vztahu důvěryhodnosti doménové struktury pro místní doménu ve službě Azure AD DS (preview).][create-trust]

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[concepts-sku]: administration-concepts.md#azure-ad-ds-skus
[create-trust]: tutorial-create-forest-trust.md

<!-- EXTERNAL LINKS -->
[pricing]: https://azure.microsoft.com/pricing/details/active-directory-ds/
