---
title: Vymezená synchronizace pro Azure AD Domain Services | Microsoft Docs
description: Naučte se používat Azure Portal ke konfiguraci synchronizace s vymezeným oborem z Azure AD do spravované domény Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 9389cf0f-0036-4b17-95da-80838edd2225
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/24/2020
ms.author: justinha
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4e65b47b2a1fd71c69ecb350f60df1fedff66b74
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2020
ms.locfileid: "96618905"
---
# <a name="configure-scoped-synchronization-from-azure-ad-to-azure-active-directory-domain-services-using-the-azure-portal"></a>Konfigurace vymezené synchronizace z Azure AD pro Azure Active Directory Domain Services pomocí Azure Portal

Pro poskytování ověřovacích služeb Azure Active Directory Domain Services (Azure služba AD DS) synchronizuje uživatele a skupiny z Azure AD. V hybridním prostředí je možné nejdřív synchronizovat uživatele a skupiny z místního prostředí Active Directory Domain Services (služba AD DS) do Azure AD pomocí Azure AD Connect a pak je synchronizovat se spravovanou doménou Azure služba AD DS.

Ve výchozím nastavení se všechny uživatele a skupiny z adresáře Azure AD synchronizují do spravované domény. Pokud máte konkrétní potřeby, můžete místo toho synchronizovat jenom definovanou skupinu uživatelů.

V tomto článku se dozvíte, jak nakonfigurovat vymezenou synchronizaci a pak pomocí Azure Portal změnit nebo zakázat sadu uživatelů s vymezeným oborem. [Tento postup můžete provést také pomocí prostředí PowerShell][scoped-sync-powershell].

## <a name="before-you-begin"></a>Než začnete

K dokončení tohoto článku potřebujete následující prostředky a oprávnění:

* Musíte mít aktivní předplatné Azure.
    * Pokud nemáte předplatné Azure, [vytvořte účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Tenant Azure Active Directory přidružený k vašemu předplatnému, buď synchronizovaný s místním adresářem, nebo jenom s cloudovým adresářem.
    * V případě potřeby [vytvořte tenanta Azure Active Directory][create-azure-ad-tenant] nebo [přidružte předplatné Azure k vašemu účtu][associate-azure-ad-tenant].
* Ve vašem tenantovi Azure AD je povolená a nakonfigurovaná spravovaná doména Azure Active Directory Domain Services.
    * V případě potřeby dokončete kurz a [vytvořte a nakonfigurujte Azure Active Directory Domain Services spravovanou doménu][tutorial-create-instance].
* Ke změně oboru synchronizace služby Azure služba AD DS potřebujete oprávnění *globálního správce* v tenantovi Azure AD.

## <a name="scoped-synchronization-overview"></a>Přehled vymezené synchronizace

Ve výchozím nastavení se všechny uživatele a skupiny z adresáře Azure AD synchronizují do spravované domény. Pokud k spravované doméně potřebuje jenom několik uživatelů, můžete synchronizovat jenom tyto uživatelské účty. Tato synchronizace s vymezeným oborem je založena na skupině. Když nakonfigurujete synchronizaci s rozsahem na základě skupin, budou se do spravované domény synchronizovat jenom uživatelské účty patřící do zadaných skupin. Vnořené skupiny se nesynchronizují, pouze konkrétní skupiny, které vyberete.

Rozsah synchronizace můžete změnit při vytváření spravované domény nebo po jejím nasazení. V existující spravované doméně teď také můžete změnit rozsah synchronizace, aniž byste je museli znovu vytvořit.

Další informace o procesu synchronizace najdete [v tématu pochopení synchronizace v Azure AD Domain Services][concepts-sync].

> [!WARNING]
> Změna rozsahu synchronizace způsobí, že spravovaná doména znovu synchronizuje všechna data. Platí následující důležité informace:
>
>  * Když změníte rozsah synchronizace pro spravovanou doménu, dojde k úplné opakované synchronizaci.
>  * Objekty, které již nejsou ve spravované doméně požadovány, jsou odstraněny. Ve spravované doméně se vytvoří nové objekty.

## <a name="enable-scoped-synchronization"></a>Povolit vymezenou synchronizaci

Pokud chcete povolit vymezenou synchronizaci v Azure Portal, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **Azure AD Domain Services**. Vyberte spravovanou doménu, například *aaddscontoso.com*.
1. V nabídce na levé straně vyberte **synchronizace** .
1. Jako *Typ synchronizace* vyberte **vymezený obor**.
1. Zvolte **Vybrat skupiny**, vyhledejte a vyberte skupiny, které chcete přidat.
1. Po provedení všech změn vyberte **Uložit rozsah synchronizace**.

Změna rozsahu synchronizace způsobí, že spravovaná doména znovu synchronizuje všechna data. Objekty, které již nejsou ve spravované doméně požadovány, jsou odstraněny a dokončení opakované synchronizace může trvat delší dobu.

## <a name="modify-scoped-synchronization"></a>Upravit vymezenou synchronizaci

Chcete-li upravit seznam skupin, jejichž uživatelé mají být synchronizováni do spravované domény, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **Azure AD Domain Services**. Vyberte spravovanou doménu, například *aaddscontoso.com*.
1. V nabídce na levé straně vyberte **synchronizace** .
1. Chcete-li přidat skupinu, zvolte možnost **+ Vybrat skupiny** v horní části a pak zvolte skupiny, které chcete přidat.
1. Chcete-li odebrat skupinu z oboru synchronizace, vyberte ji ze seznamu aktuálně synchronizovaných skupin a zvolte možnost **odebrat skupiny**.
1. Po provedení všech změn vyberte **Uložit rozsah synchronizace**.

Změna rozsahu synchronizace způsobí, že spravovaná doména znovu synchronizuje všechna data. Objekty, které již nejsou ve spravované doméně požadovány, jsou odstraněny a dokončení opakované synchronizace může trvat delší dobu.

## <a name="disable-scoped-synchronization"></a>Zakázat synchronizaci s vymezeným oborem

Pokud chcete zakázat synchronizaci s rozsahem na základě skupin pro spravovanou doménu, proveďte následující kroky:

1. V Azure Portal vyhledejte a vyberte **Azure AD Domain Services**. Vyberte spravovanou doménu, například *aaddscontoso.com*.
1. V nabídce na levé straně vyberte **synchronizace** .
1. Změňte *Typ synchronizace* z **oboru** na **vše** a pak vyberte **Uložit rozsah synchronizace**.

Změna rozsahu synchronizace způsobí, že spravovaná doména znovu synchronizuje všechna data. Objekty, které již nejsou ve spravované doméně požadovány, jsou odstraněny a dokončení opakované synchronizace může trvat delší dobu.

## <a name="next-steps"></a>Další kroky

Další informace o procesu synchronizace najdete [v tématu pochopení synchronizace v Azure AD Domain Services][concepts-sync].

<!-- INTERNAL LINKS -->
[scoped-sync-powershell]: powershell-scoped-synchronization.md
[concepts-sync]: synchronization.md
[tutorial-create-instance]: tutorial-create-instance.md
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
